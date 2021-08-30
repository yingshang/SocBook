# 第三方服务集成

## 基础说明

第三方服务集成是由wazuh管理端的配置文件进行配置相关的第三方调用脚本或者接口。其中第三方服务集成使用`integration`标签进行标识，以下内容是可用参数：

* name：调用第三方服务的脚本名字
* hook\_url：
* api\_key：
* level：
* rule\_id：
* group：
* event\_location：
* alert\_format：





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





