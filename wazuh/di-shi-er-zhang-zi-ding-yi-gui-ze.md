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

不过使用update\_ruleset命令进行规则更新会失败，因为需要科学上网，而且这个功能在4.2版本之后将会废弃。

```text
[root@wazuh-manager ~]# /var/ossec/bin/update_ruleset
### Wazuh ruleset ###
ERROR: 	Download Error:HTTPSConnectionPool(host='github.com', port=443): Max retries exceeded with url: /wazuh/wazuh-ruleset/archive/4.1.zip (Caused by NewConnectionError('<urllib3.connection.HTTPSConnection object at 0x7fa48fc43520>: Failed to establish a new connection: [Errno 111] Connection refused')).
Exit.
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

### 解码语法





<table>
  <thead>
    <tr>
      <th style="text-align:center">&#x6807;&#x7B7E;</th>
      <th style="text-align:center">&#x53C2;&#x6570;&#x540D;</th>
      <th style="text-align:center">&#x53C2;&#x6570;&#x503C;</th>
      <th style="text-align:center">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">decoder</td>
      <td style="text-align:center">name</td>
      <td style="text-align:center">&#x968F;&#x4FBF;&#x586B;</td>
      <td style="text-align:center">&#x5B9A;&#x4E49;&#x89E3;&#x7801;&#x5668;&#x7684;&#x540D;&#x5B57;</td>
    </tr>
    <tr>
      <td style="text-align:center">parent</td>
      <td style="text-align:center">&#x65E0;</td>
      <td style="text-align:center">&#x7236;&#x7EA7;&#x89E3;&#x7801;&#x5668;&#x540D;&#x5B57;</td>
      <td style="text-align:center">&#x5B9A;&#x4E49;&#x89E3;&#x7801;&#x5668;&#x8C03;&#x7528;&#x7236;&#x7EA7;&#x5185;&#x5BB9;&#xFF0C;&#x975E;&#x5FC5;&#x9009;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">accumulate</td>
      <td style="text-align:center">&#x65E0;</td>
      <td style="text-align:center">&lt;accumulate /&gt;</td>
      <td style="text-align:center">&#x5141;&#x8BB8;&#x89E3;&#x6790;&#x76F8;&#x540C;&#x6807;&#x7B7E;&#x7684;&#x591A;&#x884C;&#x65E5;&#x5FD7;&#xFF0C;&#x975E;&#x5FC5;&#x9009;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">program_name</td>
      <td style="text-align:center">type</td>
      <td style="text-align:center">
        <p>osmatch</p>
        <p>osregex</p>
        <p>pcre2</p>
      </td>
      <td style="text-align:center">&#x5B9A;&#x4E49;&#x89E3;&#x7801;&#x5668;&#x4F7F;&#x7528;&#x7684;&#x6B63;&#x5219;&#x5339;&#x914D;&#x6A21;&#x5F0F;&#xFF0C;&#x6709;&#x4E09;&#x79CD;&#x6B63;&#x5219;&#x5339;&#x914D;&#x6A21;&#x5F0F;&#xFF0C;&#x5982;&#x679C;type&#x4E3A;&#x7A7A;&#xFF0C;&#x9ED8;&#x8BA4;&#x9009;&#x62E9;osmatch&#x6B63;&#x5219;&#x5339;&#x914D;&#x6A21;&#x5F0F;&#xFF0C;&#x975E;&#x5FC5;&#x9009;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">prematch</td>
      <td style="text-align:center">offset</td>
      <td style="text-align:center">
        <p>after_regex</p>
        <p>after_parent</p>
      </td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center"></td>
      <td style="text-align:center">type</td>
      <td style="text-align:center">
        <p>osregex</p>
        <p>pcre2</p>
      </td>
      <td style="text-align:center">&#x9ED8;&#x8BA4;osregex</td>
    </tr>
    <tr>
      <td style="text-align:center">regex</td>
      <td style="text-align:center">offset</td>
      <td style="text-align:center">
        <p>after_regex</p>
        <p>after_parent</p>
        <p>after_prematch</p>
      </td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center"></td>
      <td style="text-align:center">type</td>
      <td style="text-align:center">
        <p>osregex</p>
        <p>pcre2</p>
      </td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">order</td>
      <td style="text-align:center">&#x65E0;</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">fts</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">ftscomment</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">plugin_decoder</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">use_own_name</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">json_null_field</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">json_array_structure</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">var</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">type</td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
  </tbody>
</table>

#### decoder

定义解码器的名字，这个名字根据自身需求进行编写。例子如下：

```text
<decoder name="demo">
  ...
</decoder>
```

#### parent

定义父级解码器，这个父级解码器下接多个子级解码器。例子如下：

```text
<decoder name="demo">
  <program_name>^python</program_name>
</decoder>

<decoder name="demo1">
  <parent>demo</parent>
</decoder>
```

#### prematch

```text
Mon  6 10:26:52 localhost python[1234]: predata username 'admin' end
```









### 规则语法





### 覆盖规则



### 静默规则





