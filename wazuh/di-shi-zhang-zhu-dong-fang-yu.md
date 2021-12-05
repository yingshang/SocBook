# 主动防御

主动防御是基于威胁对象触发的规则告警而进行主动拦截的行为，防御威胁对象继续对代理端进行攻击。

要触发主动防御，主要有两个流程：

1. 修改管理端配置文件，开启主动防御模式。
2. 使用拦截脚本，定义拦截方式和拦截频率次数。

说明一下主动防御这些标签作用。

|          标签         | 作用                                                                                                                                                                                                                                  |
| :-----------------: | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|       command       | 定义要使用的拦截脚本。                                                                                                                                                                                                                         |
|       location      | <p>定义触发规则的位置，其中有四种选型：local、server、某代理端ID号，all。</p><p><strong>local</strong>：触发规则只影响当前代理端。</p><p><strong>server</strong>: 触发规则影响管理端。</p><p>某代理端ID号: 触发规则影响某代理端。</p><p><strong>all</strong>: 触发规则影响所有代理端，<strong>等于全局封禁</strong>。</p> |
|      rules\_id      | 定义触发主动防御的规则ID号                                                                                                                                                                                                                      |
|       timeout       | 定义主动防御周期时间。                                                                                                                                                                                                                         |
|        level        | 定义触发主动防御的告警等级。                                                                                                                                                                                                                      |
|     rules\_group    | 定义触发主动防御的规则组，多个规则组使用  **`\|`**  分割。                                                                                                                                                                                                 |
| repeated\_offenders | 定义威胁对象多次攻击的时间递增列表，最多可以设置5个递增情况。                                                                                                                                                                                                     |

主动防御默认有以下的拦截脚本。

```
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

```
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

```
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

![](<../.gitbook/assets/image (154).png>)

查看`/etc/hosts.deny`文件，就会发现攻击IP被添加上去。

```
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

```
[root@wazuh-centos-agent ~]# cat /var/ossec/logs/active-responses.log 
Mon Jul 19 10:34:25 EDT 2021 /var/ossec/active-response/bin/host-deny.sh add - 192.168.1.130 1626705265.17483312 5716
```

等待600秒之后，再次查看主动防御日志，就会看到来源IP地址会自动解禁。

```
[root@wazuh-centos-agent ~]# cat /var/ossec/logs/active-responses.log 
Mon Jul 19 10:34:25 EDT 2021 /var/ossec/active-response/bin/host-deny.sh add - 192.168.1.130 1626705265.17483312 5716
Mon Jul 19 10:44:32 EDT 2021 /var/ossec/active-response/bin/host-deny.sh delete - 192.168.1.130 1626705265.17483312 5716
```

查看管理端代理日志，找到关于5716的告警，显示的是ssh验证失败。

![](<../.gitbook/assets/image (156).png>)

细心的朋友可能发现了，上面这么严格规则（输入错误一次就封禁），在生产环境的时候，访问SSH输入错误的密码是常见的行为，要是应用了这种规则，怕是要直接炸了，所以需要**引入允许错误次数机制**。但是查看`active-response`标签是没有错误次数限制的，这时候，我们需要查看5716告警规则写了什么。

查看 `/var/ossec/ruleset/rules/0095-sshd_rules.xml`规则文件。

```
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

```
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

```
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

![](<../.gitbook/assets/image (158).png>)

在告警日志可以看到相关信息，其中特别注意就是`firedtimes`参数，这个参数就是触发的次数，也就是超过5716规则定义8次才触发这条规则。

![](<../.gitbook/assets/image (157).png>)

但是这里面就有一个非常奇怪的问题，第一次SSH攻击如果没有中止，就会一直发送，看到下面5716的`firedtimes`参数的值已经到达80次，只有等我停止SSH攻击的时候，就会生成5720规则，这样就会导致攻击识别判断逻辑有问题。

![](<../.gitbook/assets/image (159).png>)

解决的方式也很简单，重写5716规则给它加上`frequency`参数或者新建一条新规则使用这条规则做触发封禁。以下是重写5716规则，设置频率`frequency`为30次，告警等级`level`设置为10级，设置重写`overwrite`规则。修改完成，重启管理端服务。

```
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

![](<../.gitbook/assets/image (161).png>)

我们设置频率是20次，为什么在日志限制到达了84次，主要是管理端没有反应过来，hydra我设置了密码字典是1000条，它一秒发送好几十个攻击，管理端需要收集和处理日志，最后超过阈值就启动封禁。

![](<../.gitbook/assets/image (160).png>)

## Linux 防火墙封禁

按照上面的步骤，在管理端配置主动防御，这个时候我们调用`firewall-drop.sh`脚本进行封禁，与hosts.deny不同，这个脚本是使用iptables添加拦截规则。设置完成之后，重启管理端服务。

```
  <active-response>
    <command>firewall-drop</command>
    <location>local</location>
    <rules_id>5716</rules_id>
    <timeout>60</timeout>
  </active-response>
```

使用hydra对SSH服务进行暴力破解攻击。

![](<../.gitbook/assets/image (163).png>)

这时候查看主动防御日志，就可以看到已经调用`firewall-drop.sh`脚本封禁kali（192.168.1.130）的暴力破解攻击。

![](<../.gitbook/assets/image (164).png>)

使用命令`iptables -L INPUT`查看防火墙规则，可以看到有条对192.168.1.130这个IP丢弃（DROP）数据包规则。

![](<../.gitbook/assets/image (162).png>)

上面我们设置都是local，按照官方文档说明`local`只应用于当前代理端，`all`是应用所有代理端，接下来测试一下`all`的是否可以应用所有代理端。修改配置文件，设置`all`。

```
  <active-response>
    <command>firewall-drop</command>
    <location>all</location>
    <rules_id>5716</rules_id>
    <timeout>60</timeout>
  </active-response>
```

使用hydra进行暴力破解攻击，一段时间后，查看centos代理端和Ubuntu代理端，会看到这两条机器的防火墙规则上面已经添加对IP（192.168.1.130）丢弃数据包规则。

![](<../.gitbook/assets/image (165).png>)

## windows封禁

打开系统设置，配置远程连接，允许远程计算机访问Windows服务器。

![](<../.gitbook/assets/image (169).png>)

打开本地安全策略，将审核登录事件策略设置为`成功，失败`，wazuh代理端会收集Windows登录日志。

![](<../.gitbook/assets/image (176).png>)

windows日志有两种`evelogchanel`和`evenlog`默认情况下，wazuh收集windows日志是使用`evelogchanel`类型。测试结果表示`evelogchanel`类型是有些问题的，建议使用`evenlog`类型，参考链接：[https://github.com/wazuh/wazuh/issues/4888](https://github.com/wazuh/wazuh/issues/4888)。

```
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

![](<../.gitbook/assets/image (170).png>)

在管理端会生成一条60122告警日志。

![](<../.gitbook/assets/image (171).png>)

使用json格式化这段日志，看到描述：**显示登录失败-未知用户或者错误密码**。

![](<../.gitbook/assets/image (173).png>)

![](<../.gitbook/assets/image (172).png>)

根据上面远程计算机登录失败的告警ID是60122，设置主动防御的规则ID号为60122，拦截脚本选择`netsh-win-2016.cmd`。

```
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

```
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

![](<../.gitbook/assets/image (175).png>)

在防火墙入站规则添加了一条wazuh针对于114.114.114.2这个IP的封禁规则，

![](<../.gitbook/assets/image (174).png>)

现在看起来脚本是没有问题的，查看[官方文档](https://documentation.wazuh.com/current/user-manual/reference/ossec-conf/commands.html#expect)发现`expect`参数如果日志没有srcip这个字段就会跳过主动防御拦截，而采用`evelogchanel`日志模式的IP字段就会变成`data.win.eventdata.ipAddress`字段，解决这个问题，有两种办法：

1. 在对日志编码处理的时候，修改字段名字为srcip。（自定义规则展示）
2. 采用`evenlog`日志模式。



```
  <localfile>
    <location>Security</location>
    <log_format>eventlog</log_format>
  </localfile>
```



![](<../.gitbook/assets/image (177).png>)



```
  <active-response>
    <command>netsh-win-2016</command>
    <location>local</location>
    <rules_id>18130</rules_id>
    <timeout>30</timeout>
  </active-response>

```



![](<../.gitbook/assets/image (179).png>)



![](<../.gitbook/assets/image (178).png>)

## 自定义防御脚本

在管理端配置文件新增执行命令内容，`sshdeny.sh`脚本基于原始`firewall-drop.sh`进行改造的，这个需求是因为原始脚本封禁会对整个IP进行封禁，也就是说如果误封的话，把业务端口干掉怎么办，所以需要改造爆破ssh服务就直接封禁攻击者不能访问22端口。

```
  <command>
    <name>sshdeny</name>
    <executable>/var/ossec/etc/shared/sshdeny.sh</executable>
    <expect>srcip</expect>
    <timeout_allowed>yes</timeout_allowed>
  </command>
```

在centos代理端查看主动防御日志，可以发现它调用的路径是基于`/var/ossec/active-response/bin/`目录往后添加，那就说只能把脚本放到bin目录下面才可以执行。

![](<../.gitbook/assets/image (180).png>)

于是管理端配置文件的`executable`只能写脚本的名字。因为执行封禁动作是在代理端执行，也就是说生产的时候需要用ansible将封禁脚本放到每个代理端的`/var/ossec/active-response/bin/`目录下面才可以执行封禁动作。

```
  <command>
    <name>sshdeny</name>
    <executable>sshdeny.sh</executable>
    <expect>srcip</expect>
    <timeout_allowed>yes</timeout_allowed>
  </command>
```

复制`firewall-drop.sh`脚本。

```
[root@wazuh-centos-agent ~]# cd /var/ossec/active-response/bin/
[root@wazuh-centos-agent bin]# cp firewall-drop.sh sshdeny.sh
```

改造只封端口的脚本，具体内容看脚本注释。

```
#!/bin/sh
# Adds an IP to the iptables drop list (if linux)
# Adds an IP to the ipfilter drop list (if solaris, freebsd or netbsd)
# Adds an IP to the ipsec drop list (if aix)
# Requirements: Linux with iptables, Solaris/FreeBSD/NetBSD with ipfilter or AIX with IPSec
# Expect: srcip
# Copyright (C) 2015-2019, Wazuh Inc.
# Author: Ahmet Ozturk (ipfilter and IPSec)
# Author: Daniel B. Cid (iptables)
# Author: cgzones

UNAME=`uname`
ECHO="/bin/echo"
GREP="/bin/grep"
IPTABLES=""
IP4TABLES="/sbin/iptables"
IP6TABLES="/sbin/ip6tables"
IPFILTER="/sbin/ipf"
if [ "X$UNAME" = "XSunOS" ]; then
    IPFILTER="/usr/sbin/ipf"
fi
GENFILT="/usr/sbin/genfilt"
LSFILT="/usr/sbin/lsfilt"
MKFILT="/usr/sbin/mkfilt"
RMFILT="/usr/sbin/rmfilt"
ARG1=""
ARG2=""
RULEID=""
ACTION=$1
USER=$2
IP=$3
PWD=`pwd`
LOCK="${PWD}/fw-drop"
LOCK_PID="${PWD}/fw-drop/pid"

#取当前服务器的SSH端口
port=`netstat -antlp | grep LISTEN | grep ssh | grep "0.0.0.0" | awk '{print $4}' | awk -F ":" '{print$2}'`


LOCAL=`dirname $0`;
cd $LOCAL
cd ../
filename=$(basename "$0")

LOG_FILE="${PWD}/../logs/active-responses.log"

#加上端口信息放到主动防御日志里面。
echo "`date` $0 $1 $2 $3 $4 $5 $port" >> ${LOG_FILE}

# Checking for an IP
if [ "x${IP}" = "x" ]; then
   echo "$0: <action> <username> <ip>"
   exit 1;
fi

case "${IP}" in
    *:* ) IPTABLES=$IP6TABLES;;
    *.* ) IPTABLES=$IP4TABLES;;
    * ) echo "`date` Unable to run active response (invalid IP: '${IP}')." >> ${LOG_FILE} && exit 1;;
esac

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
            kill="false"
            for pid in `pgrep -f "${filename}"`; do
                if [ "x${pid}" = "x${C_PID}" ]; then
                    # Unlocking and exiting
                    kill -9 ${C_PID}
                    echo "`date` Killed process ${C_PID} holding lock." >> ${LOG_FILE}
                    kill="true"
                    unlock;
                    i=0;
                    S_PID="";
                    break;
                fi
            done

            if [ "x${kill}" = "xfalse" ]; then
                echo "`date` Unable kill process ${C_PID} holding lock." >> ${LOG_FILE}
                # Unlocking and exiting
                unlock;
                exit 1;
            fi
        fi
    done
}

# Unlock function
unlock()
{
   rm -rf ${LOCK}
}



# Blocking IP
if [ "x${ACTION}" != "xadd" -a "x${ACTION}" != "xdelete" ]; then
   echo "$0: invalid action: ${ACTION}"
   exit 1;
fi



# 在原来的防火墙规则加上禁止目的端口号
if [ "X${UNAME}" = "XLinux" ]; then
 if [ "x${ACTION}" = "xadd" ]; then
      ARG1="-I INPUT -p tcp --dport ${port} -s ${IP} -j DROP"
      ARG2="-I FORWARD -p tcp --dport ${port} -s ${IP} -j DROP"
   else
      ARG1="-D INPUT -p tcp --dport ${port}  -s ${IP} -j DROP"
      ARG2="-D FORWARD -p tcp --dport ${port}   -s ${IP} -j DROP"
   fi


   # Checking if iptables is present
   if [ ! -x ${IPTABLES} ]; then
      IPTABLES="/usr"${IPTABLES}
      if [ ! -x ${IPTABLES} ]; then
        echo "$0: can not find iptables"
        exit 0;
      fi
   fi

   # Executing and exiting
   COUNT=0;
   lock;
   while [ 1 ]; do
        ${IPTABLES} ${ARG1}
        RES=$?
        if [ $RES = 0 ]; then
            break;
        else
            COUNT=`expr $COUNT + 1`;
            echo "`date` Unable to run (iptables returning != $RES): $COUNT - $0 $1 $2 $3 $4 $5" >> ${LOG_FILE}
            sleep $COUNT;

            if [ $COUNT -gt 4 ]; then
                break;
            fi
        fi
   done

   COUNT=0;
   while [ 1 ]; do
        ${IPTABLES} ${ARG2}
        RES=$?
        if [ $RES = 0 ]; then
            break;
        else
            COUNT=`expr $COUNT + 1`;
            echo "`date` Unable to run (iptables returning != $RES): $COUNT - $0 $1 $2 $3 $4 $5" >> ${LOG_FILE}
            sleep $COUNT;

            if [ $COUNT -gt 4 ]; then
                break;
            fi
        fi
   done
   unlock;

   exit 0;

# FreeBSD, SunOS or NetBSD with ipfilter
elif [ "X${UNAME}" = "XFreeBSD" -o "X${UNAME}" = "XSunOS" -o "X${UNAME}" = "XNetBSD" ]; then

   # Checking if ipfilter is present
   ls ${IPFILTER} >> /dev/null 2>&1
   if [ $? != 0 ]; then
      exit 0;
   fi

   # Checking if echo is present
   ls ${ECHO} >> /dev/null 2>&1
   if [ $? != 0 ]; then
       exit 0;
   fi

   if [ "x${ACTION}" = "xadd" ]; then
      ARG1="\"@1 block out quick from any to ${IP}\""
      ARG2="\"@1 block in quick from ${IP} to any\""
      IPFARG="${IPFILTER} -f -"
   else
      ARG1="\"@1 block out quick from any to ${IP}\""
      ARG2="\"@1 block in quick from ${IP} to any\""
      IPFARG="${IPFILTER} -rf -"
   fi

   # Executing it
   eval ${ECHO} ${ARG1}| ${IPFARG}
   eval ${ECHO} ${ARG2}| ${IPFARG}

   exit 0;

# AIX with ipsec
elif [ "X${UNAME}" = "XAIX" ]; then

  # Checking if genfilt is present
  ls ${GENFILT} >> /dev/null 2>&1
  if [ $? != 0 ]; then
     exit 0;
  fi

  # Checking if lsfilt is present
  ls ${LSFILT} >> /dev/null 2>&1
  if [ $? != 0 ]; then
     exit 0;
  fi
  # Checking if mkfilt is present
  ls ${MKFILT} >> /dev/null 2>&1
  if [ $? != 0 ]; then
     exit 0;
  fi

  # Checking if rmfilt is present
  ls ${RMFILT} >> /dev/null 2>&1
  if [ $? != 0 ]; then
     exit 0;
  fi

  if [ "x${ACTION}" = "xadd" ]; then
    ARG1=" -v 4 -a D -s ${IP} -m 255.255.255.255 -d 0.0.0.0 -M 0.0.0.0 -w B -D \"Access Denied by OSSEC-HIDS\""
    #Add filter to rule table
    eval ${GENFILT} ${ARG1}

    #Deactivate  and activate the filter rules.
    eval ${MKFILT} -v 4 -d
    eval ${MKFILT} -v 4 -u
  else
    # removing a specific rule is not so easy :(
     eval ${LSFILT} -v 4 -O  | ${GREP} ${IP} |
     while read -r LINE
     do
         RULEID=`${ECHO} ${LINE} | cut -f 1 -d "|"`
         let RULEID=${RULEID}+1
         ARG1=" -v 4 -n ${RULEID}"
         eval ${RMFILT} ${ARG1}
     done
    #Deactivate  and activate the filter rules.
    eval ${MKFILT} -v 4 -d
    eval ${MKFILT} -v 4 -u
  fi

else
    exit 0;
fi

```

配置好脚本之后，使用hydra进行暴力破解攻击，在centos代理端的防火墙规则上面已经添加封禁22端口规则。

![](<../.gitbook/assets/image (181).png>)



