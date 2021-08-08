# 命令内容监控

wazuh命令监控功能是wazuh执行系统命令或者脚本，对执行之后输出的结果发送到wazuh管理端进行规则识别，获取到监控信息进而告警这么一个功能。

查看代理端的配置文件，可以发现官方预先定义的三个命令监控指令，**其中包括磁盘大小监控、端口开放情况监控以及用户最近登录信息监控**。

其中日志收集格式`log_format`对于命令内容收集有两种：`command`和`full_command`，他们区别是前者收集命令执行后单行内容是，后者是收集命令执行后多行命令内容。

```text
  <!-- Log analysis -->
  <localfile>
    <log_format>command</log_format>
    <command>df -P</command>
    <frequency>360</frequency>
  </localfile>

  <localfile>
    <log_format>full_command</log_format>
    <command>netstat -tulpn | sed 's/\([[:alnum:]]\+\)\ \+[[:digit:]]\+\ \+[[:digit:]]\+\ \+\(.*\):\([[:digit:]]*\)\ \+\([0-9\.\:\*]\+\).\+\ \([[:digit:]]*\/[[:alnum:]\-]*\).*/\1 \2 == \3 == \4 \5/' | sort -k 4 -g | sed 's/ == \(.*\) ==/:\1/' | sed 1,2d</command>
    <alias>netstat listening ports</alias>
    <frequency>360</frequency>
  </localfile>

  <localfile>
    <log_format>full_command</log_format>
    <command>last -n 20</command>
    <frequency>360</frequency>
  </localfile>
```

`command`标签内的内容就是shell命令，把端口开放情况监控命令在shell界面执行一下，看到输出内容有通信协议、监听端口和端口服务，wazuh就会把这些内容丢到管理端进行规则处理，这个规则需要自定义编写识别内容。

```text
[root@wazuh-centos-agent ~]# netstat -tulpn | sed 's/\([[:alnum:]]\+\)\ \+[[:digit:]]\+\ \+[[:digit:]]\+\ \+\(.*\):\([[:digit:]]*\)\ \+\([0-9\.\:\*]\+\).\+\ \([[:digit:]]*\/[[:alnum:]\-]*\).*/\1 \2 == \3 == \4 \5/' | sort -k 4 -g | sed 's/ == \(.*\) ==/:\1/' | sed 1,2d
tcp 0.0.0.0:22 0.0.0.0:* 1126/sshd
tcp6 :::22 :::* 1126/sshd
tcp 127.0.0.1:25 0.0.0.0:* 1271/master
tcp6 ::1:25 :::* 1271/master
udp 127.0.0.1:323 0.0.0.0:* 726/chronyd
udp6 ::1:323 :::* 726/chronyd
```

wazuh管理端收到该日志并进入处理之后，变成了一条ID号533告警日志

![](../.gitbook/assets/image%20%28156%29.png)

通过告警日志有规则ID号，去找到对应的识别规则。关于规则内容在规则那一章节说明，先不细说。

```text
[root@wazuh-manager rules]# cat /var/ossec/ruleset/rules/0015-ossec_rules.xml | grep -A 7 533 
  <rule id="533" level="7">
    <if_sid>530</if_sid>
    <match>ossec: output: 'netstat listening ports</match>
    <check_diff />
    <description>Listened ports status (netstat) changed (new port opened or closed).</description>
    <group>pci_dss_10.2.7,pci_dss_10.6.1,gpg13_10.1,gdpr_IV_35.7.d,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AU.6,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>
```

使用这个功能的话，有两种使用方式，一种是直接修改代理端配置文件，新增命令监控内容，不过这种我是不推荐这种，改起来很复杂；另一种就是通过管理端下发配置文件，这种需要**代理端开启远程命令内容收集权限**。

```text
[root@wazuh-centos-agent ~]# echo "logcollector.remote_commands=1" >> /var/ossec/etc/local_internal_options.conf 
```

设置共享配置文件

```text
[root@wazuh-manager opt]# cat /var/ossec/etc/shared/default/agent.conf 
<agent_config>
  <localfile>
    <log_format>full_command</log_format>
    <command>ps aux</command>
    <frequency>60</frequency>
  </localfile>
</agent_config>
```

我们先不对`ps aux`内容做规则和告警处理，单纯查看代理端是否执行这个命令，并且是否将日志发送到管理端。

开启记录全部日志，并且重启管理端服务。

```text
<logall>yes</logall>
<logall_json>yes</logall_json>
```

等一分钟时间，就可以看到`/var/ossec/logs/archives/archives.log`文件已经收到日志。

![](../.gitbook/assets/image%20%28158%29.png)

现在看到是原始日志，后面在规则自定义章节进行对日志的处理。

