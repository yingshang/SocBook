# 规则集

## 描述

在`ruleset`目录可以看到官方定义的日志编码规则文件（decoders）和日志告警文件（rules），这两个目录最好不要做任何修改，当规则升级的时候会把原来修改记录给覆盖掉。

```text
[root@wazuh-manager ~]# tree /var/ossec/ruleset/
/var/ossec/ruleset/
├── decoders
├── rules
```

所以需要修改规则的时候，可以在`local_decoder.xml`和`local_rules.xml`文件进行修改，这两个文件执行优先级比默认的规则要高。

```text
[root@wazuh-manager ~]# tree /var/ossec/etc/
/var/ossec/etc/
├── decoders
│   └── local_decoder.xml
├── rules
    └── local_rules.xml
```



## 日志调试

从代理端`secure`日志文件提取SSH登录日志做日志编码和规则告警测试。

```text
Aug  5 10:11:55 wazuh-centos-agent sshd[7650]: Accepted password for root from 192.168.1.1 port 14933 ssh2
```

使用`ossec-logtest`日志测试功能，发现在wazuh 4.1版本之后就会被丢弃，官方推荐使用`wazuh-logtest`日志调试工具。

![](../.gitbook/assets/image%20%28206%29.png)

通过`ossec-logtest`和`wazuh-logtest`对比，`wazuh-logtest`多了MITRE ATT&CK框架描述日志行为。

![](../.gitbook/assets/image%20%28208%29.png)

通过上述图片可以发现，wazuh处理日志有三个阶段

* **预解码阶段（pre-decode）**，在日志中提取常用的字段。
* **解码阶段（decode）**，分析日志类型，提取该日志的字段信息。
* **规则匹配阶段（Rule match）**，根据解码阶段找到对应的规则文件，接着匹配关键字找到对应的告警规则。

接下来说说具体处理流程，根据最终生成告警ID号5715，去寻找具体的规则匹配文件的位置。

```text
[root@wazuh-manager ruleset]# grep "5715" -r *
rules/0095-sshd_rules.xml:  <rule id="5715" level="3">
```

查看`0095-sshd_rules.xml`文件，找到5715的内容，规则正则匹配`^Accepted|authenticated.$`日志内容，往上溯源规则发现5715的父ID是5700。

```text
  <rule id="5715" level="3">
    <if_sid>5700</if_sid>
    <match>^Accepted|authenticated.$</match>
    <description>sshd: authentication success.</description>
    <mitre>
      <id>T1078</id>
      <id>T1021</id>
    </mitre>
    <group>authentication_success,pci_dss_10.2.5,gpg13_7.1,gpg13_7.2,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>
```

查看5700规则内容，找到`decoded_as`参数以及`sshd`参数内容，确定解码规则是关于sshd。

```text
  <rule id="5700" level="0" noalert="1">
    <decoded_as>sshd</decoded_as>
    <description>SSHD messages grouped.</description>
  </rule>
```

根据关键字找到解码文件（/var/ossec/ruleset/decoders/0310-ssh\_decoders.xml），以下内容是预解码阶段，确定该日志是使用`0310-ssh_decoders.xml`解码文件。

```text
<decoder name="sshd">
  <program_name>^sshd</program_name>
</decoder>
```

使用正则表达式获取匹配的字段。

```text
<decoder name="sshd-success">
  <parent>sshd</parent>
  <prematch>^Accepted</prematch>
  <regex offset="after_prematch">^ \S+ for (\S+) from (\S+) port </regex>
  <order>user, srcip</order>
  <fts>name, user, location</fts>
</decoder>
```

## 自定义规则

### 参数配置



### 覆盖规则



### 静默规则





