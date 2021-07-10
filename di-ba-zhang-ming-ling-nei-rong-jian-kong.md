# 第八章  命令内容监控



wazuh命令监控功能是wazuh执行系统命令或者脚本，对执行之后输出的结果发送到wazuh管理端进行规则识别，获取到监控信息进而告警这么一个功能。

查看代理端的配置文件，可以发现官方预先定义的三个命令监控指令，**其中包括磁盘大小监控、端口开放情况监控以及用户最近登录信息监控**。

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



