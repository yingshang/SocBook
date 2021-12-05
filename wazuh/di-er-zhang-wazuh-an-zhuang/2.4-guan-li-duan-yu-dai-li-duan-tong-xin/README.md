# 管理端与代理端通信

centos7系统防火墙默认是启动状态，系统与系统之间是无法通信，所以需要关掉防火墙，在实际生产环境中可以设置允许某些地址访问即可。第一条命令是把firewalld（防火墙服务）给停止掉，第二条命令则是关闭防火墙开机启动的规则，这样两条命令就可以实现主机与主机之间的通信。

```
[root@wazuh-manager ~]# systemctl stop firewalld
[root@wazuh-manager ~]# systemctl disable firewalld
Removed symlink /etc/systemd/system/multi-user.target.wants/firewalld.service.
Removed symlink /etc/systemd/system/dbus-org.fedoraproject.FirewallD1.service.
```

wazuh管理端可以统筹全局功能的变化情况，无论是管理端还是代理端，他们配置文件的位置都是在同一个位置上面，位于**/var/ossec/etc/ossec.conf**。

通过使用vim编辑配置文件找到下面这段配置auth标签。

![](<../../../.gitbook/assets/image (10).png>)

wazuh的配置文件是以xml文件格式进行解析的。通过查看这段xml的配置文件，可以发现服务器与客户端远程的通信是默认开启的，其中**监听端口默认是1515**，可以将监听端口修改1-65535可用的端口；使用来源IP（use\_source\_ip）这个功能的作用就是当代理端与管理端通信之后，管理端不显示代理端的IP，默认情况下设置为no，也就是管理端这里只显示any，这样做有什么好处呢？假如使用来源地址的话，当在外网同一个IP地址里面内网几台代理端服务器对管理端通信的时候，后一台会把前一台的记录进行覆盖，因为对外通信都是同一个IP，这样的设置会导致管理端以为你是同一台代理端。

接下来就是代理端如何跟管理端通信，主要将两个方面的代理端，一种是Linux系统，其中centos、ubuntu等系统，通信的配置都是一样的，这里就只拿centos7系统做例子；另外一种，就是windows系统，其通信配置跟Linux系统差不多，只不过在windows系统下有图形界面可用操作。

总结起来，管理端与客户端通信有三步动作：

**①修改配置文件，输入管理端的IP地址。**

**②使用agent\_auth命令，进行客户端与管理端的通信。**

**③重启客户端的服务。**
