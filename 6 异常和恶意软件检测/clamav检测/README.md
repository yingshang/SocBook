# 6.3 clamav检测

wazuh集成了virustotal进行异常检测，但是我想直接检测文件特征状态，又不想将文件泄露出去，所以有了一个新的解决方案。

clamav是一个开源病毒扫描软件，针对于Linux木马病毒进行本地化扫描，发现木马，并生成告警日志，那么可以将clamav日志发送到wazuh管理端进行日志处理，生成告警信息。

在centos代理端使用yum命令安装`clamav`。

```text
 #安装epel扩展仓库
 yum install epel-release -y
 #安装clamav
 yum -y install clamav-server clamav-data clamav-update  \
 clamav-filesystem clamav clamav-scanner-systemd clamav-devel \ 
 clamav-lib clamav-server-systemd
```

安装完成之后，需要更新clamav的病毒特征库，使用`freshclam`命令进行更新。

![](../../.gitbook/assets/image%20%28144%29.png)

我这边提供两种思路：一种是直接扫描，另外一种就是配置好文件再扫描，前者简单直接，后者日志数据会丰富一点，配置麻烦。

## 直接扫描

clamscan命令是扫描命令，`-r`参数是递归扫描目录参数后面接扫描目录，`-l`参数则是生成扫描日志的位置。为了可以周期性扫描，可以使用crontab命令制定计划任务。

![](../../.gitbook/assets/image%20%28146%29.png)

## 配置文件扫描

新建clamav日志文件，因为clamav是以clamscan启动的，没有在/var/log目录有创建权限，所以需要先创建授权。

```text
[root@wazuh-centos-agent opt]# touch /var/log/clamd.log
[root@wazuh-centos-agent opt]# chmod 666 /var/log/clamd.log
```

修改配置文件`/etc/clamd.d/scan.conf`。

```text
[root@wazuh-centos-agent opt]# cat /etc/clamd.d/scan.conf 
LogFile /var/log/clamd.log #clamav文件扫描日志
LogTime yes  #记录时间
LogSyslog yes  #记录到syslog
LogVerbose yes  #记录详细信息
ExtendedDetectionInfo yes  #记录扩展检测信息
LocalSocket /run/clamd.scan/clamd.sock   #本地sock监听
LogRotate yes   #轮转日志记录
LogFileMaxSize 20M  #最大轮转容量记录为20M
```

启动clamav服务。

```text
[root@wazuh-centos-agent opt]# /usr/sbin/clamd
```

虽然wazuh有对clamav的日志做了规则和告警处理，但是实际使用发现无法触发规则和告警，需要自定义规则。自定义规则具体内容在后面章节说明。

日志规则解码，根据自己需要提取日志字段。

```text
[root@wazuh-manager ~]# cat /var/ossec/etc/decoders/local_decoder.xml 
<decoder name="virusFound">
   <prematch>FOUND</prematch>
   <regex>-> (\S+): (\S+)\((\S+)\)</regex>
   <order>url, extra_data, id</order>
</decoder>
```

编写告警规则，设置告警信息和告警等级

```text
[root@wazuh-manager ~]# cat /var/ossec/etc/rules/local_rules.xml
<group name="clamd">

  <rule id="111001" level="0" noalert="1">
    <decoded_as>virusFound</decoded_as>
    <description>Clamd messages grouped.</description>
  </rule>
  <rule id="111002" level="8">
    <if_sid>111001</if_sid>
    <match>FOUND</match>
    <description>ClamAV: Virus detected</description>
    <group>virus</group>
  </rule>

</group>
```

收集代理端clamav的日志。

```text
[root@wazuh-manager ~]# cat /var/ossec/etc/shared/default/agent.conf 
<agent_config>
 <localfile>
    <log_format>syslog</log_format>
    <location>/var/log/clamd.log</location>
  </localfile>
</agent_config>
```

配置完成之后，扫描木马病毒就可以生成告警日志。

![](../../.gitbook/assets/image%20%28142%29.png)

