# filebeat

Filebeat是本地日志文件的收集转发器，可以根据自身需要，监控日志目录或者特定日志文件，并将日志转发到logstash处理或者elasticsearch存储。 

**wazuh管理端**添加仓库GPG密钥和yum仓库信息。

```text
[root@wazuh-manager ~]# rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
[root@wazuh-manager ~]# cat > /etc/yum.repos.d/elastic.repo << EOF
> [elasticsearch-7.x]
> name=Elasticsearch repository for 7.x packages
> baseurl=https://artifacts.elastic.co/packages/7.x/yum
> gpgcheck=1
> gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
> enabled=1
> autorefresh=1
> type=rpm-md
> EOF

```

使用yum命令进行安装filebeat

![](../../.gitbook/assets/image%20%2861%29.png)

不过使用yum安装filebeat的速度非常慢，通过迅雷直接下载filebeat直链就非常快。下载地址：

```text
#7.11.2版本类型选择
https://www.elastic.co/cn/downloads/past-releases/filebeat-7-11-2
#选择rpm包
https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.11.2-x86_64.rpm
```

安装完成之后，filebeat配置文件位置：`/etc/filebeat/filebeat.yml` 。清空filebeat配置文件默认内容，修改filebeat配置文件如下

```text
[root@wazuh-manager ~]# cat /etc/filebeat/filebeat.yml 
output.elasticsearch.hosts: ["192.168.1.201:9200"]  #设置ES的地址

filebeat.modules:
  - module: wazuh
    alerts:
      enabled: true
    archives:
      enabled: false

setup.template.json.enabled: true
setup.template.json.path: /etc/filebeat/wazuh-template.json  #wazuh字段模板
setup.template.json.name: wazuh
setup.template.overwrite: true
setup.ilm.enabled: false
```

下载filebeat的wazuh字段模板

```text
# curl -so /etc/filebeat/wazuh-template.json https://raw.githubusercontent.com/wazuh/wazuh/4.1/extensions/elasticsearch/7.x/wazuh-template.json
# chmod go+r /etc/filebeat/wazuh-template.json
```

filebeat安装索引模板，以此es收到filebeat的日志之后，可以自动对字段进行索引查询。

```text
[root@wazuh-manager ~]# filebeat setup --index-management -E setup.template.json.enabled=false
ILM policy and write alias loading not enabled.

Index setup finished.
```

下载filebeat的wazuh模块

```text
[root@wazuh-manager ~]# curl -s https://packages.wazuh.com/4.x/filebeat/wazuh-filebeat-0.1.tar.gz | tar -xvz -C /usr/share/filebeat/module
wazuh/
wazuh/module.yml
wazuh/archives/
wazuh/archives/config/
wazuh/archives/config/archives.yml
wazuh/archives/ingest/
wazuh/archives/ingest/pipeline.json
wazuh/archives/manifest.yml
wazuh/alerts/
wazuh/alerts/config/
wazuh/alerts/config/alerts.yml
wazuh/alerts/ingest/
wazuh/alerts/ingest/pipeline.json
wazuh/alerts/manifest.yml
wazuh/_meta/
wazuh/_meta/config.yml
wazuh/_meta/fields.yml
wazuh/_meta/docs.asciidoc
```

设置filebeat开机启动和服务启动

```text
[root@wazuh-manager ~]# systemctl daemon-reload
[root@wazuh-manager ~]# systemctl enable filebeat
Created symlink from /etc/systemd/system/multi-user.target.wants/filebeat.service to /usr/lib/systemd/system/filebeat.service.
[root@wazuh-manager ~]# systemctl start filebeat
```

测试filebeat是否可以连接ES

```text
[root@wazuh-manager ~]# filebeat test output
elasticsearch: http://192.168.1.201:9200...
  parse url... OK
  connection...
    parse host... OK
    dns lookup... OK
    addresses: 192.168.1.201
    dial up... OK
  TLS... WARN secure connection disabled
  talk to server... OK
  version: 7.11.2

```

