# elasticsearch

## 单节点

elasticsearch（ES）是一款搜索和数据分布式实时分析引擎，可以对文档进行实时存储和查询。可以将它理解成一个数据库，存放内容到字段形成索引，查询就能够直接去查询索引内容，可以减少查询时间。

导入GPG密钥和添加yum仓库

```text
[root@EK ~]# rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
[root@EK ~]# cat > /etc/yum.repos.d/elastic.repo << EOF
[elasticsearch-7.x]
name=Elasticsearch repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
EOF
```

使用yum命令安装elasticsearch

```text
yum install -y elasticsearch-7.11.2
```

不过使用yum安装elasticsearch的速度非常慢，通过迅雷直接下载elasticsearch直链就非常快。下载地址：

```text
#7.11.2版本类型选择
https://www.elastic.co/cn/downloads/past-releases/elasticsearch-7-11-2
#选择rpm包
https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.11.2-x86_64.rpm
```

rpm命令本地安装elasticsearch

![](../../.gitbook/assets/image%20%2860%29.png)

按照提示，设置elasticsearch开机启动和服务启动

```text
[root@EK ~]# systemctl daemon-reload
[root@EK ~]# systemctl enable elasticsearch.service
Created symlink from /etc/systemd/system/multi-user.target.wants/elasticsearch.service to /usr/lib/systemd/system/elasticsearch.service.
[root@EK ~]# systemctl start elasticsearch
```

查看elasticsearch服务启动状态

![](../../.gitbook/assets/image%20%2857%29.png)

由于Elasticsearch监听端口默认是本地9200端口，这样会导致在其他服务器的日志无法转发日志过来，所以需要设置对外的网卡IP。

通过编辑**`/etc/elasticsearch/elasticsearch.yml`**文件，找到**`network.host`**这个参数，将`127.0.0.1`修改成`0.0.0.0`或者这台服务器的IP地址`192.168.1.201`。

接着还需要找到`node.name`这个参数位置，将前面的注释符号删掉。这边使用默认的节点名字，这个节点名字可以随便写。

找到`cluster.initial_master_nodes`这个参数，修改成如下

```text
cluster.initial_master_nodes: ["node-1"]
```

具体文件配置就如下所示

```text
[root@EK ~]# cat /etc/elasticsearch/elasticsearch.yml 
node.name: node-1
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
cluster.initial_master_nodes: ["node-1"]
```

把服务重启一下，`systemctl restart elasticsearch`。

查看端口启动情况，可以看到9200端口开始监听，代表elasticsearch启动成功。使用curl命令请求elasticsearch地址。

![](../../.gitbook/assets/image%20%2855%29.png)

## ES集群

根据实际生产环境测试，一旦代理端数目多起来，使用一台ES查询的速度是非常慢的，所以需要ES集群加快查询的速度。 按照网络架构图，es的集群是ES-2\(192.168.1.202\)，ES-3（192.168.1.203），其中这两台的安装过程就不演示，按照es的安装教程进行安装。

ES节点1（192.168.1.201）配置文件

```text
[root@EK ~]# cat /etc/elasticsearch/elasticsearch.yml 
node.name: node-1
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["192.168.1.201","192.168.1.202","192.168.1.203"]
cluster.initial_master_nodes: ["node-1","node-2","node-3"]
```

ES节点2（192.168.1.202）配置文件

```text
[root@EK ~]# cat /etc/elasticsearch/elasticsearch.yml 
node.name: node-2
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["192.168.1.201","192.168.1.202","192.168.1.203"]
cluster.initial_master_nodes: ["node-1","node-2","node-3"]
```

ES节点3（192.168.1.203）配置文件

```text
[root@EK ~]# cat /etc/elasticsearch/elasticsearch.yml 
node.name: node-3
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["192.168.1.201","192.168.1.202","192.168.1.203"]
cluster.initial_master_nodes: ["node-1","node-2","node-3"]
```

配置完成之后，重启三台es服务。查看ES集群健康情况，可以发现节点数已经有三个了，说明彼此之间已经成功通信。

```text
[root@EK ~]# curl http://192.168.1.201:9200/_cat/nodes
192.168.1.201 32 29 1 0.05 0.09 0.17 cdhilmrstw * node-1
192.168.1.202 9 68 0 0.20 0.38 0.27 cdhilmrstw * node-2
192.168.1.203 10 68 60 1.27 0.71 0.40 cdhilmrstw * node-3
```

