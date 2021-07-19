# 第十章  主动防御

主动防御是基于威胁对象触发的规则告警而进行主动拦截的行为，防御威胁对象继续对代理端进行攻击。

要触发主动防御，主要有两个流程：

1. 修改管理端配置文件，开启主动防御模式。
2. 使用拦截脚本，定义拦截方式和拦截频率次数。

## 管理端配置文件

```text
[root@wazuh-manager opt]# cat  /var/ossec/etc/ossec.conf | grep -A 6 "<active"
  <active-response>
    <command>host-deny</command>
    <location>local</location>
    <rules_id>5716</rules_id>
    <timeout>600</timeout>
  </active-response>
```

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

这里配置文件例子就是使用了`host-deny.sh`拦截脚本，查看`host-deny.sh`拦截脚本。这个脚本主要将来源IP地址加入到`/etc/hosts.deny`文件里面，Linux系统就会禁止来源IP地址访问。

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











