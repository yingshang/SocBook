# 第三方服务集成

## 基础说明

第三方服务集成是由wazuh管理端的配置文件进行配置相关的第三方调用脚本或者接口。其中第三方服务集成使用`integration`标签进行标识，以下内容是可用参数：

* **name：**调用第三方服务的脚本名字，脚本路径位于`/var/ossec/integrations/`。
* **hook\_url：**第三方服务提供的API接口，这个是可选，也可以在脚本里面。
* **api\_key：**第三方服务提供的API接口的API密钥，这个是可选，也可以在脚本里面。
* **level：**设置大于多少风险等级的日志才可以调用脚本。
* **rule\_id：**设置什么规则ID的日志才可以调用脚本。
* **group：**设置什么告警组的日志才可以调用脚本。
* **event\_location：**设置什么日志来源才可以调用脚本。
* **alert\_format：**设置接收日志的规则格式。

```text
<integration>
  <name>custom-integration</name>
  <hook_url>WEBHOOK</hook_url>
  <level>10</level>
  <group>multiple_drops|authentication_failures</group>
  <alert_format>json</alert_format>
</integration>
```

首先，在python脚本首部设置python执行环境变量。

```text
#!/usr/bin/env python
```

接着，根据wazuh定义的格式，第一个参数是接收告警日志的文件路径（可以写死路径），第二个参数接收第三方服务接口的API密钥，第三个参数接收第三方服务的接口地址。当然这三个参数可以不写，在脚本写死内容就可以了。

```text
alert_file = sys.argv[1]
api_key = sys.argv[2]
hook_url = sys.argv[3]
```

最后根据自己的需要提取JSON日志的字段，作为判断依据。

```text
alert_level = alert_json['rule']['level']
description = alert_json['rule']['description']
```

## 微步威胁情报

访问微步威胁情报的API地址（[https://x.threatbook.cn/v5/myApi](https://x.threatbook.cn/v5/myApi)），获取API key

![](../.gitbook/assets/image%20%28216%29.png)

这边提供一个挖矿病毒样本的哈希值做脚本测试。

```text
eb808932714c9533962e129e61d84c29536497e62b2a7d89dce3376d882c6965
```

参考微步API文档（[https://x.threatbook.cn/v5/apiDocs](https://x.threatbook.cn/v5/apiDocs)），写一个demo脚本测试一下效果。

```text
import requests

url = 'https://api.threatbook.cn/v3/file/report/multiengines'
params = {
    'apikey': '',
    'sandbox_type': 'centos_7_x64',
    'sha256': 'eb808932714c9533962e129e61d84c29536497e62b2a7d89dce3376d882c6965'
}
response = requests.get(url, params=params)
print(response.json())
```

![](../.gitbook/assets/image%20%28220%29.png)

![](../.gitbook/assets/image%20%28218%29.png)

接下来把wazuh文件完整性监控到哈希值传到微步在线进行检测。









## jira

![](../.gitbook/assets/image%20%28211%29.png)





