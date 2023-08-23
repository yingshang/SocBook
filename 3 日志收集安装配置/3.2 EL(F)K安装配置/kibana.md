# kibana

由于ES与kibana同在一台服务器上面，无需再导入Elasticsearch的仓库密钥和yum仓库，直接就可以使用yum安装。

![](../../.gitbook/assets/image%20%2862%29.png)

不过使用yum安装kibana的速度非常慢，通过迅雷直接下载kibana直链就非常快。下载地址：

```text
#7.11.2版本类型选择
https://www.elastic.co/cn/downloads/past-releases/kibana-7-11-2
#选择rpm包
https://artifacts.elastic.co/downloads/kibana/kibana-7.11.2-x86_64.rpm
```

rpm命令安装

```text
[root@EK ~]# rpm -ivh kibana-7.11.2-x86_64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:kibana-7.11.2-1                  ################################# [100%]
Creating kibana group... OK
Creating kibana user... OK
```

下载和安装kibana上的wazuh插件，用户展示wazuh安全监控数据和图表，进入到kibana的目录`/usr/share/kibana/`，运行`kibana-plugin`命令进行安装wazuh的图表展示。

```text
[root@EK kibana]# /usr/share/kibana/bin/kibana-plugin install https://packages.wazuh.com/4.x/ui/kibana/wazuh_kibana-4.1.5_7.11.2-1.zip
Attempting to transfer from https://packages.wazuh.com/4.x/ui/kibana/wazuh_kibana-4.1.5_7.11.2-1.zip
Transferring 34222306 bytes.
....................
```

或者将插件下载到本地进行安装

```text
[root@EK kibana]# /usr/share/kibana/bin/kibana-plugin install file:///opt/wazuh_kibana-4.1.5_7.11.2-1.zip 
Found previous install attempt. Deleting...
Attempting to transfer from file:///opt/wazuh_kibana-4.1.5_7.11.2-1.zip
Transferring 34222306 bytes....................
Transfer complete
Retrieving metadata from plugin archive
Extracting plugin archive
Extraction complete
Plugin installation complete
```

默认情况下，kibana只在本地监听，需要设置对外的监听，才可以在其他计算机访问。通过编辑`/etc/kibana/kibana.yml`找到`server.host`参数，修改如下

```text
server.host: "0.0.0.0"
```

kibana要与Elasticsearch通信，找到`elasticsearch.hosts`参数，因为Elasticsearch跟kibana在同一服务器，只需连接到localhost就可以通信。

```text
elasticsearch.hosts: ["http://localhost:9200"]
```

创建数据目录

```text
[root@EK kibana]# mkdir /usr/share/kibana/data
[root@EK kibana]# chown -R kibana:kibana /usr/share/kibana
```

设置kibana服务开机启动和启动服务

```text
[root@EK kibana]# systemctl daemon-reload
[root@EK kibana]# systemctl enable kibana
Created symlink from /etc/systemd/system/multi-user.target.wants/kibana.service to /etc/systemd/system/kibana.service.
[root@EK kibana]# systemctl start kibana
```

修改kibana的wazuh插件连接wazuhAPI的连接地址，配置文件：`/usr/share/kibana/data/wazuh/config/wazuh.yml` 

```text
hosts:
  - default:
     url: https://192.168.1.200   #修改IP地址为wazuh管理端IP地址
     port: 55000
     username: wazuh-wui
     password: wazuh-wui
     run_as: false
```

在浏览器访问kibana地址：[**http://192.168.1.201:5601/app/wazuh\#**](http://192.168.1.201:5601/app/wazuh#/settings)。下图就是可以在kibana的wauzh模块里面看到与管理端中wazuhAPI服务配置细节。

![](../../.gitbook/assets/image%20%2863%29.png)

配置好与wazuh-api通信之后，回到首页，可以看到很多功能。这些功能后续章节将会展开说明

![](../../.gitbook/assets/image%20%2831%29.png)



