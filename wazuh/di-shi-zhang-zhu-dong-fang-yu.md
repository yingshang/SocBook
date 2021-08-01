# 主动防御

主动防御是基于威胁对象触发的规则告警而进行主动拦截的行为，防御威胁对象继续对代理端进行攻击。

要触发主动防御，主要有两个流程：

1. 修改管理端配置文件，开启主动防御模式。
2. 使用拦截脚本，定义拦截方式和拦截频率次数。

说明一下主动防御这些标签作用。

<table>
  <thead>
    <tr>
      <th style="text-align:center">&#x6807;&#x7B7E;</th>
      <th style="text-align:left">&#x4F5C;&#x7528;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">command</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x8981;&#x4F7F;&#x7528;&#x7684;&#x62E6;&#x622A;&#x811A;&#x672C;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">location</td>
      <td style="text-align:left">
        <p>&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x89C4;&#x5219;&#x7684;&#x4F4D;&#x7F6E;&#xFF0C;&#x5176;&#x4E2D;&#x6709;&#x56DB;&#x79CD;&#x9009;&#x578B;&#xFF1A;local&#x3001;server&#x3001;&#x67D0;&#x4EE3;&#x7406;&#x7AEF;ID&#x53F7;&#xFF0C;all&#x3002;</p>
        <p><b>local</b>&#xFF1A;&#x89E6;&#x53D1;&#x89C4;&#x5219;&#x53EA;&#x5F71;&#x54CD;&#x5F53;&#x524D;&#x4EE3;&#x7406;&#x7AEF;&#x3002;</p>
        <p><b>server</b>: &#x89E6;&#x53D1;&#x89C4;&#x5219;&#x5F71;&#x54CD;&#x7BA1;&#x7406;&#x7AEF;&#x3002;</p>
        <p>&#x67D0;&#x4EE3;&#x7406;&#x7AEF;ID&#x53F7;: &#x89E6;&#x53D1;&#x89C4;&#x5219;&#x5F71;&#x54CD;&#x67D0;&#x4EE3;&#x7406;&#x7AEF;&#x3002;</p>
        <p><b>all</b>: &#x89E6;&#x53D1;&#x89C4;&#x5219;&#x5F71;&#x54CD;&#x6240;&#x6709;&#x4EE3;&#x7406;&#x7AEF;&#xFF0C;<b>&#x7B49;&#x4E8E;&#x5168;&#x5C40;&#x5C01;&#x7981;</b>&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:center">rules_id</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x7684;&#x89C4;&#x5219;ID&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:center">timeout</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x5468;&#x671F;&#x65F6;&#x95F4;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">level</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x7684;&#x544A;&#x8B66;&#x7B49;&#x7EA7;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">rules_group</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x7684;&#x89C4;&#x5219;&#x7EC4;&#xFF0C;&#x591A;&#x4E2A;&#x89C4;&#x5219;&#x7EC4;&#x4F7F;&#x7528; <b><code>|</code></b> &#x5206;&#x5272;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">repeated_offenders</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x5A01;&#x80C1;&#x5BF9;&#x8C61;&#x591A;&#x6B21;&#x653B;&#x51FB;&#x7684;&#x65F6;&#x95F4;&#x9012;&#x589E;&#x5217;&#x8868;&#xFF0C;&#x6700;&#x591A;&#x53EF;&#x4EE5;&#x8BBE;&#x7F6E;5&#x4E2A;&#x9012;&#x589E;&#x60C5;&#x51B5;&#x3002;</td>
    </tr>
  </tbody>
</table>

主动防御默认有以下的拦截脚本。

```text
[root@wazuh-manager opt]# ls -l /var/ossec/active-response/bin/
total 88
-rwxr-x---. 1 root ossec  6746 Apr 22 09:39 default-firewall-drop.sh
-rwxr-x---. 1 root ossec  1750 Apr 22 09:39 disable-account.sh
-rwxr-x---. 1 root ossec  3990 Apr 22 09:39 firewalld-drop.sh
-rwxr-x---. 1 root ossec  6746 Apr 22 09:39 firewall-drop.sh
-rwxr-x---. 1 root ossec  3577 Apr 22 09:39 host-deny.sh
-rwxr-x---. 1 root ossec   838 Apr 22 09:39 ip-customblock.sh
-rwxr-x---. 1 root ossec  1622 Apr 22 09:39 ipfw_mac.sh
-rwxr-x---. 1 root ossec  1423 Apr 22 09:39 ipfw.sh
-rwxr-x---. 1 root ossec 14435 Apr 22 09:39 kaspersky.py
-rwxr-x---. 1 root ossec   714 Apr 22 09:39 kaspersky.sh
-rwxr-x---. 1 root ossec  1344 Apr 22 09:39 npf.sh
-rwxr-x---. 1 root ossec  1674 Apr 22 09:39 ossec-slack.sh
-rwxr-x---. 1 root ossec  1674 Apr 22 09:39 ossec-tweeter.sh
-rwxr-x---. 1 root ossec  1987 Apr 22 09:39 pf.sh
-rwxr-x---. 1 root ossec   580 Apr 22 09:39 restart-ossec.sh
-rwxr-x---. 1 root ossec  1047 Apr 22 09:39 restart.sh
-rwxr-x---. 1 root ossec  1220 Apr 22 09:39 route-null.sh
```

## linux host封禁

`host-deny.sh`脚本主要将来源IP地址加入到`/etc/hosts.deny`文件里面，Linux系统就会禁止来源IP地址访问。

```text
[root@wazuh-manager opt]# cat /var/ossec/active-response/bin/host-deny.sh 
#!/bin/sh
# Adds an IP to the /etc/hosts.deny file
# Requirements: sshd and other binaries with tcp wrappers support
# Expect: srcip
# Copyright (C) 2015-2020, Wazuh Inc.
# Author: Daniel B. Cid
# Last modified: Nov 09, 2005

ACTION=$1
USER=$2
IP=$3

LOCAL=`dirname $0`;
cd $LOCAL
cd ../
PWD=`pwd`
LOCK="${PWD}/host-deny-lock"
LOCK_PID="${PWD}/host-deny-lock/pid"
UNAME=`uname`


# This number should be more than enough (even if a hundred
# instances of this script is ran together). If you have
# a really loaded env, you can increase it to 75 or 100.
MAX_ITERATION="50"


# Lock function
lock()
{
    i=0;
    # Providing a lock.
    while [ 1 ]; do
        mkdir ${LOCK} > /dev/null 2>&1
        MSL=$?
        if [ "${MSL}" = "0" ]; then
            # Lock acquired (setting the pid)
            echo "$$" > ${LOCK_PID}
            return;
        fi

        # Getting currently/saved PID locking the file
        C_PID=`cat ${LOCK_PID} 2>/dev/null`
        if [ "x" = "x${S_PID}" ]; then
            S_PID=${C_PID}
        fi

        # Breaking out of the loop after X attempts
        if [ "x${C_PID}" = "x${S_PID}" ]; then
            i=`expr $i + 1`;
        fi

        sleep $i;

        i=`expr $i + 1`;

        # So i increments 2 by 2 if the pid does not change.
        # If the pid keeps changing, we will increments one
        # by one and fail after MAX_ITERACTION
        if [ "$i" = "${MAX_ITERATION}" ]; then
            echo "`date` Unable to execute. Locked: $0" \
                        >> ${PWD}/ossec-hids-responses.log

            # Unlocking and exiting
            unlock;
            exit 1;
        fi
    done
}

# Unlock function
unlock()
{
    rm -rf ${LOCK}
}


# Logging the call
echo "`date` $0 $1 $2 $3 $4 $5" >> ${PWD}/../logs/active-responses.log


# IP Address must be provided
if [ "x${IP}" = "x" ]; then
    echo "$0: Missing argument <action> <user> (ip)"
    exit 1;
fi


# Checking for invalid entries (lacking "." or ":", etc)
echo "${IP}" | egrep "\.|\:" > /dev/null 2>&1
if [ ! $? = 0 ]; then
    echo "`date` Invalid ip/hostname entry: ${IP}" >> ${PWD}/../logs/active-responses.log
    exit 1;
fi


# Adding the ip to hosts.deny
if [ "x${ACTION}" = "xadd" ]; then
    # Looking for duplication
    IPKEY=$(grep -w "${IP}" /etc/hosts.deny)
    if [ ! -z "$IPKEY" ]; then
        echo "IP ${IP} already exists on host.deny..." >> ${PWD}/../logs/active-responses.log
        exit 1
    fi
    lock;
    echo "${IP}" | grep "\:" > /dev/null 2>&1
    if [ $? = 0 ]; then
        IP="[${IP}]"
    fi
    if [ "X$UNAME" = "XFreeBSD" ]; then
        echo "ALL : ${IP} : deny" >> /etc/hosts.allow
    else
        echo "ALL:${IP}" >> /etc/hosts.deny
    fi
    unlock;
    exit 0;


# Deleting from hosts.deny
elif [ "x${ACTION}" = "xdelete" ]; then
    lock;
    TMP_FILE=`mktemp ${PWD}/ossec-hosts.XXXXXXXXXX`
    if [ "X${TMP_FILE}" = "X" ]; then
        # Cheap fake tmpfile, but should be harder then no random data
        TMP_FILE="${PWD}/ossec-hosts.`cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 32 | head -1 `"
    fi
    echo "${IP}" | grep "\:" > /dev/null 2>&1
    if [ $? = 0 ]; then
        IP="\[${IP}\]"
    fi
    if [ "X$UNAME" = "XFreeBSD" ]; then
        cat /etc/hosts.allow | grep -v "ALL : ${IP} : deny$"> ${TMP_FILE}
        mv ${TMP_FILE} /etc/hosts.allow
    else
        cat /etc/hosts.deny | grep -v "ALL:${IP}$"> ${TMP_FILE}
        cat ${TMP_FILE} > /etc/hosts.deny
        rm ${TMP_FILE}
    fi
    unlock;
    exit 0;


# Invalid action
else
    echo "$0: invalid action: ${ACTION}"
fi

exit 1;

```

这里配置文件例子就是使用了`host-deny.sh`拦截脚本，在`command`标签定义`host-deny`，提取来源IP地址，并且设置可以自动解锁封禁（`timeout_allowed`）。当触发规则为`5716`的时候，就会使用`host-deny`，并且封禁范围是当前代理端，封禁时间为600秒。

```text
[root@wazuh-manager opt]# cat  /var/ossec/etc/ossec.conf

  <command>
    <name>host-deny</name>
    <executable>host-deny.sh</executable>
    <expect>srcip</expect>
    <timeout_allowed>yes</timeout_allowed>
  </command>

  <active-response>
    <command>host-deny</command>
    <location>local</location>
    <rules_id>5716</rules_id>
    <timeout>600</timeout>
  </active-response>
```

使用kali ssh到centos代理端，第一次连接输入错误的密码，第二次重连就会发现被代理端重置连接。

![](../.gitbook/assets/image%20%28159%29.png)

查看`/etc/hosts.deny`文件，就会发现攻击IP被添加上去。

```text
[root@wazuh-centos-agent ~]# cat /etc/hosts.deny 
#
# hosts.deny	This file contains access rules which are used to
#		deny connections to network services that either use
#		the tcp_wrappers library or that have been
#		started through a tcp_wrappers-enabled xinetd.
#
#		The rules in this file can also be set up in
#		/etc/hosts.allow with a 'deny' option instead.
#
#		See 'man 5 hosts_options' and 'man 5 hosts_access'
#		for information on rule syntax.
#		See 'man tcpd' for information on tcp_wrappers
#
ALL:192.168.1.130
```

查看主动防御的日志，就会发现攻击IP（192.168.1.130），攻击时间（1626705265.17483312），触发拦截规则（5716）。

```text
[root@wazuh-centos-agent ~]# cat /var/ossec/logs/active-responses.log 
Mon Jul 19 10:34:25 EDT 2021 /var/ossec/active-response/bin/host-deny.sh add - 192.168.1.130 1626705265.17483312 5716
```

等待600秒之后，再次查看主动防御日志，就会看到来源IP地址会自动解禁。

```text
[root@wazuh-centos-agent ~]# cat /var/ossec/logs/active-responses.log 
Mon Jul 19 10:34:25 EDT 2021 /var/ossec/active-response/bin/host-deny.sh add - 192.168.1.130 1626705265.17483312 5716
Mon Jul 19 10:44:32 EDT 2021 /var/ossec/active-response/bin/host-deny.sh delete - 192.168.1.130 1626705265.17483312 5716
```

查看管理端代理日志，找到关于5716的告警，显示的是ssh验证失败。

![](../.gitbook/assets/image%20%28160%29.png)

细心的朋友可能发现了，上面这么严格规则（输入错误一次就封禁），在生产环境的时候，访问SSH输入错误的密码是常见的行为，要是应用了这种规则，怕是要直接炸了，所以需要**引入允许错误次数机制**。但是查看`active-response`标签是没有错误次数限制的，这时候，我们需要查看5716告警规则写了什么。

查看 `/var/ossec/ruleset/rules/0095-sshd_rules.xml`规则文件。

```text
  <rule id="5716" level="5">
    <if_sid>5700</if_sid>
    <match>^Failed|^error: PAM: Authentication</match>
    <description>sshd: authentication failed.</description>
    <mitre>
      <id>T1110</id>
    </mitre>
    <group>authentication_failed,pci_dss_10.2.4,pci_dss_10.2.5,gpg13_7.1,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>
```

当我们继续往下寻找的时候，会找到5720规则，这其中`frequency`参数就是触发规则频率次数。

```text
  <rule id="5720" level="10" frequency="8">
    <if_matched_sid>5716</if_matched_sid>
    <same_source_ip />
    <description>sshd: Multiple authentication failures.</description>
    <mitre>
      <id>T1110</id>
    </mitre>
    <group>authentication_failures,pci_dss_10.2.4,pci_dss_10.2.5,pci_dss_11.4,gpg13_7.1,gdpr_IV_35.7.d,gdpr_IV_32.2,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AC.7,nist_800_53_SI.4,tsc_CC6.1,tsc_CC6.8,tsc_CC7.2,tsc_CC7.3,</group>
  </rule>

```

所以我们只需将`active-response`触发的规则从5716改到5720。修改完成之后，需要重启管理端服务。

```text
[root@wazuh-manager opt]# cat  /var/ossec/etc/ossec.conf

  <command>
    <name>host-deny</name>
    <executable>host-deny.sh</executable>
    <expect>srcip</expect>
    <timeout_allowed>yes</timeout_allowed>
  </command>

  <active-response>
    <command>host-deny</command>
    <location>local</location>
    <rules_id>5720</rules_id>
    <timeout>600</timeout>
  </active-response>
```

使用hydra进行SSH暴力破解攻击

![](../.gitbook/assets/image%20%28162%29.png)

在告警日志可以看到相关信息，其中特别注意就是`firedtimes`参数，这个参数就是触发的次数，也就是超过5716规则定义8次才触发这条规则。

![](../.gitbook/assets/image%20%28165%29.png)

但是这里面就有一个非常奇怪的问题，第一次SSH攻击如果没有中止，就会一直发送，看到下面5716的`firedtimes`参数的值已经到达80次，只有等我停止SSH攻击的时候，就会生成5720规则，这样就会导致攻击识别判断逻辑有问题。

![](../.gitbook/assets/image%20%28161%29.png)

解决的方式也很简单，重写5716规则给它加上`frequency`参数或者新建一条新规则使用这条规则做触发封禁。以下是重写5716规则，设置频率`frequency`为30次，告警等级`level`设置为10级，设置重写`overwrite`规则。修改完成，重启管理端服务。

```text
[root@wazuh-manager ~]# cat /var/ossec/etc/rules/local_rules.xml 
<group name="ssh_deny">
  <rule id="5716" level="10" frequency="20"  overwrite="yes">
    <if_matched_sid>5700</if_matched_sid>
    <match>^Failed|^error: PAM: Authentication</match>
    <description>sshd: authentication failed.</description>
  </rule>
</group>

```

接着使用hydra进行暴力破解攻击，几秒之后就显示连接重置。

![](../.gitbook/assets/image%20%28164%29.png)

我们设置频率是20次，为什么在日志限制到达了84次，主要是管理端没有反应过来，hydra我设置了密码字典是1000条，它一秒发送好几十个攻击，管理端需要收集和处理日志，最后超过阈值就启动封禁。

![](../.gitbook/assets/image%20%28163%29.png)

## Linux 防火墙封禁

按照上面的步骤，在管理端配置主动防御，这个时候我们调用`firewall-drop.sh`脚本进行封禁，与hosts.deny不同，这个脚本是使用iptables添加拦截规则。设置完成之后，重启管理端服务。

```text
  <active-response>
    <command>firewall-drop</command>
    <location>local</location>
    <rules_id>5716</rules_id>
    <timeout>60</timeout>
  </active-response>
```

使用hydra对SSH服务进行暴力破解攻击。

![](../.gitbook/assets/image%20%28173%29.png)

这时候查看主动防御日志，就可以看到已经调用`firewall-drop.sh`脚本封禁kali（192.168.1.130）的暴力破解攻击。

![](../.gitbook/assets/image%20%28176%29.png)

使用命令`iptables -L INPUT`查看防火墙规则，可以看到有条对192.168.1.130这个IP丢弃（DROP）数据包规则。

![](../.gitbook/assets/image%20%28169%29.png)

上面我们设置都是local，按照官方文档说明`local`只应用于当前代理端，`all`是应用所有代理端，接下来测试一下`all`的是否可以应用所有代理端。修改配置文件，设置`all`。

```text
  <active-response>
    <command>firewall-drop</command>
    <location>all</location>
    <rules_id>5716</rules_id>
    <timeout>60</timeout>
  </active-response>
```

使用hydra进行暴力破解攻击，一段时间后，查看centos代理端和Ubuntu代理端，会看到这两条机器的防火墙规则上面已经添加对IP（192.168.1.130）丢弃数据包规则。

![](../.gitbook/assets/image%20%28166%29.png)

## windows封禁

打开系统设置，配置远程连接，允许远程计算机访问Windows服务器。

![](../.gitbook/assets/image%20%28175%29.png)

打开本地安全策略，将审核登录事件策略设置为`成功，失败`，wazuh代理端会收集Windows登录日志。

![](../.gitbook/assets/image%20%28180%29.png)

windows日志有两种`evelogchanel`和`evenlog`默认情况下，wazuh收集windows日志是使用`evelogchanel`类型。测试结果表示`evelogchanel`类型是有些问题的，建议使用`evenlog`类型，参考链接：[https://github.com/wazuh/wazuh/issues/4888](https://github.com/wazuh/wazuh/issues/4888)。

```text
  <localfile>
    <location>Application</location>
    <log_format>eventchannel</log_format>
  </localfile>

  <localfile>
    <location>Security</location>
    <log_format>eventchannel</log_format>
    <query>Event/System[EventID != 5145 and EventID != 5156 and EventID != 5447 and
      EventID != 4656 and EventID != 4658 and EventID != 4663 and EventID != 4660 and
      EventID != 4670 and EventID != 4690 and EventID != 4703 and EventID != 4907 and
      EventID != 5152 and EventID != 5157]</query>
  </localfile>

  <localfile>
    <location>System</location>
    <log_format>eventchannel</log_format>
  </localfile>
```

在windows的事件管理器中，对于远程登录失败的审计事件的ID是4625。

![](../.gitbook/assets/image%20%28167%29.png)

在管理端会生成一条60122告警日志。

![](../.gitbook/assets/image%20%28168%29.png)

使用json格式化这段日志，看到描述：**显示登录失败-未知用户或者错误密码**。

![](../.gitbook/assets/image%20%28172%29.png)

![](../.gitbook/assets/image%20%28179%29.png)

根据上面远程计算机登录失败的告警ID是60122，设置主动防御的规则ID号为60122，拦截脚本选择`netsh-win-2016.cmd`。

```text
  <command>
    <name>netsh-win-2016</name>
    <executable>netsh-win-2016.cmd</executable>
    <expect>srcip</expect>
    <timeout_allowed>yes</timeout_allowed>
  </command>
  
  <active-response>
    <command>netsh-win-2016</command>
    <location>local</location>
    <rules_id>60122</rules_id>
    <timeout>100</timeout>
  </active-response>
```

但是配置完成之后进行攻击，**就会发现防御并没有触发**。使用管理端进行手动封禁，测试脚本是否有效。

```text
#查看当前主动防御有什么防御脚本
[root@wazuh-manager ~]# /var/ossec/bin/agent_control -L

Wazuh agent_control. Available active responses:

   Response name: netsh-win-2016100, command: netsh-win-2016.cmd

#查看代理端信息，找到win2008的ID为009
[root@wazuh-manager ~]# /var/ossec/bin/agent_control -l

Wazuh agent_control. List of available agents:
   ID: 000, Name: wazuh-manager (server), IP: 127.0.0.1, Active/Local
   ID: 006, Name: wazuh-centos-agent, IP: any, Active
   ID: 003, Name: wazuh-ubuntu-agent, IP: any, Disconnected
   ID: 007, Name: win2008-agent, IP: any, Disconnected
   ID: 008, Name: WIN-EMSHFJ78JTT, IP: any, Disconnected
   ID: 009, Name: win2008, IP: any, Active

List of agentless devices:

#使用-b参数封禁IP，-f参数使用防御脚本，-u参数在那台代理端
[root@wazuh-manager ~]# /var/ossec/bin/agent_control -b 114.114.114.2 -f  netsh-win-2016100 -u 009

Wazuh agent_control: Running active response 'netsh-win-2016100' on: 009

```

在Windows代理端查看主动防御日志，其中一开始我测试使用`netsh.cmd`脚本，发现无法应用于Windows2008，后使用`netsh-win-2016.cmd`脚本可以生效。

![](../.gitbook/assets/image%20%28170%29.png)

在防火墙入站规则添加了一条wazuh针对于114.114.114.2这个IP的封禁规则，

![](../.gitbook/assets/image%20%28171%29.png)

现在看起来脚本是没有问题的，查看[官方文档](https://documentation.wazuh.com/current/user-manual/reference/ossec-conf/commands.html#expect)发现`expect`参数如果日志没有srcip这个字段就会跳过主动防御拦截，而采用`evelogchanel`日志模式的IP字段就会变成`data.win.eventdata.ipAddress`字段，解决这个问题，有两种办法：

1. 在对日志编码处理的时候，修改字段名字为srcip。（自定义规则展示）
2. 采用`evenlog`日志模式。
3. 1



```text
  <localfile>
    <location>Security</location>
    <log_format>eventlog</log_format>
  </localfile>
```



![](../.gitbook/assets/image%20%28181%29.png)



```text
  <active-response>
    <command>netsh-win-2016</command>
    <location>local</location>
    <rules_id>18130</rules_id>
    <timeout>30</timeout>
  </active-response>

```













