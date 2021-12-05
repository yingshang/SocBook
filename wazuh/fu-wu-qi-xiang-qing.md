# 资产信息监控

wazuh提供收集代理端各种信息（软件安装信息、操作系统版本信息、端口开放信息）能力，通过`Syscollector`模块收集代理端信息整合起来存储到管理端的`sqlite`数据库中。

管理端配置文件默认已经设置。

```
  <wodle name="syscollector">
    <disabled>no</disabled>
    <interval>1h</interval>
    <scan_on_start>yes</scan_on_start>
    <hardware>yes</hardware>
    <os>yes</os>
    <network>yes</network>
    <packages>yes</packages>
    <ports all="no">yes</ports>
    <processes>yes</processes>
  </wodle>
```

1

|            参数            | 默认值 | 描述                                   |
| :----------------------: | :-: | ------------------------------------ |
| <p>disabled</p><p></p> |  no | 是否关闭syscollector功能                   |
|         interval         |  1h | 扫描检测时间周期                             |
|      scan\_on\_start     | yes | 是否重启服务就开始扫描检测                        |
|         hardware         | yes | 是否收集硬件信息                             |
|            os            | yes | 是否收集操作系统信息                           |
|          network         | yes | 是否收集网络信息                             |
|         packages         | yes | 是否收集软件包信息                            |
|           ports          | yes | 是否收集端口开放信息， **all=’no’设置只收集监听端口信息。** |
|         processes        | yes | 是否收集进程运行信息                           |
|         hotfixes         | yes | 是否收集Windows补丁安装信息                    |



```
[root@wazuh-manager ~]# ls /var/ossec/queue/db/
000.db  003.db  006.db  006.db-journal  007.db  008.db  009.db  global.db  global.db-journal  wdb
```

将006.db复制到本地，使用navicat进行查看

![](<../.gitbook/assets/image (205).png>)



sys\_processes表

![](<../.gitbook/assets/image (206).png>)

API

```
[root@wazuh-manager db]# curl -u 'wazuh:FXKNECzaW4qOL$QO' -k -X GET "https://localhost:55000/security/user/authenticate?raw=true"
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJ3YXp1aCIsImF1ZCI6IldhenVoIEFQSSBSRVNUIiwibmJmIjoxNjI4MzkyMTM4LCJleHAiOjE2MjgzOTMwMzgsInN1YiI6IndhenVoIiwicnVuX2FzIjpmYWxzZSwicmJhY19yb2xlcyI6WzFdLCJyYmFjX21vZGUiOiJ3aGl0ZSJ9.dN-4sQjSYHDaA_4OwosryYDQx0RbiPwp4rjgb5BL8pA
[root@wazuh-manager db]# 
[root@wazuh-manager db]# curl -k -X GET "https://localhost:55000/syscollector/006/netaddr?pretty=true" -H  "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJ3YXp1aCIsImF1ZCI6IldhenVoIEFQSSBSRVNUIiwibmJmIjoxNjI4MzkyMTM4LCJleHAiOjE2MjgzOTMwMzgsInN1YiI6IndhenVoIiwicnVuX2FzIjpmYWxzZSwicmJhY19yb2xlcyI6WzFdLCJyYmFjX21vZGUiOiJ3aGl0ZSJ9.dN-4sQjSYHDaA_4OwosryYDQx0RbiPwp4rjgb5BL8pA"
{
   "data": {
      "affected_items": [
         {
            "scan": {
               "id": 1177088535
            },
            "netmask": "255.255.255.0",
            "address": "192.168.1.101",
            "iface": "ens33",
            "proto": "ipv4",
            "broadcast": "192.168.1.255",
            "agent_id": "006"
         },
         {
            "scan": {
               "id": 1177088535
            },
            "netmask": "ffff:ffff:ffff:ffff::",
            "address": "fe80::bef0:841f:6e49:6e07",
            "iface": "ens33",
            "proto": "ipv6",
            "agent_id": "006"
         },
         {
            "scan": {
               "id": 1177088535
            },
            "netmask": "ffff:ffff:ffff:ffff::",
            "address": "fe80::303f:b012:123f:6647",
            "iface": "ens33",
            "proto": "ipv6",
            "agent_id": "006"
         },
         {
            "scan": {
               "id": 1177088535
            },
            "netmask": "ffff:ffff:ffff:ffff::",
            "address": "fe80::9dd4:5cb4:cd63:d841",
            "iface": "ens33",
            "proto": "ipv6",
            "agent_id": "006"
         }
      ],
      "total_affected_items": 4,
      "total_failed_items": 0,
      "failed_items": []
   },
   "message": "All specified syscollector information was returned",
   "error": 0
}
```



