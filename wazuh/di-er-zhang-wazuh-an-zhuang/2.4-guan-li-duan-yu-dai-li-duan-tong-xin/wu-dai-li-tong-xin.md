# 无代理通信

wazuh提供一种无代理通信模式，应用于具有基本命令的阉割简单版Linux系统，它通过SSH连接进去，执行文件完整性检测，常用于交换机、路由器等设备的配置文件监控。

使用`register_host.sh`脚本添加SSH连接192.168.1.130记录，密码为123456。为了方便，130这台服务器是采用centos7系统。

```text
[root@wazuh-manager agentless]# /var/ossec/agentless/register_host.sh add root@192.168.1.130 123456
*Host root@192.168.1.130 added.
```

查看无代理通信主机记录。

```text
[root@wazuh-manager agentless]# /var/ossec/agentless/register_host.sh list
*Available hosts: 
root@192.168.1.130
```

## ssh\_integrity\_check\_linux

管理端设置配置文件，`ssh_integrity_check_linux`类型是监控文件的hash值来判断文件增删查改；`frequency`是设置多少秒再次SSH服务器进行检测文件完整性；`host`是设置SSH连接服务器；`state`是设置分析日志模式，其中`periodic`作用取代理端日志匹配规则产生告警日志；`arguments`是设置监控文件夹内的文件内容。

```text
<agentless>
  <type>ssh_integrity_check_linux</type>
  <frequency>60</frequency>
  <host>root@192.168.1.130</host>
  <state>periodic</state>
  <arguments>/bin /etc /sbin</arguments>
</agentless>
```

配置完成并且重启管理端后，会在osses.log发现测试错误。

```text
2021/08/03 09:42:09 ossec-agentlessd: ERROR: Test failed for 'ssh_integrity_check_linux' (126). Ignoring.
```

运行无代理服务需要`expect`这个软件包，管理端使用`yum`命令进行安装即可。

```text
[root@wazuh-manager agentless]# yum install expect -y
```

安装完成之后，再次重启管理端，查看日志可以看到已经运行起来并且开始检测和检测完成。

![](../../../.gitbook/assets/image%20%28196%29.png)

在130这台服务器查看secure日志，可以看到管理端都是SSH连接过来，检测完成之后就会断开。

![](../../../.gitbook/assets/image%20%28195%29.png)





### ssh\_generic\_diff

```text
<agentless>
  <type>ssh_generic_diff</type>
  <frequency>60</frequency>
  <host>root@192.168.1.130</host>
  <state>periodic_diff</state>
  <arguments>ls -la /etc; cat /etc/passwd</arguments>
</agentless>
```



![](../../../.gitbook/assets/image%20%28191%29.png)

新增和修改文件不行，需要删除文件

![](../../../.gitbook/assets/image%20%28200%29.png)





