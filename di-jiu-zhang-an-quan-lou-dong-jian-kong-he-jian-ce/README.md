# 第九章 主机安全漏洞检测

为了能够进行主机安全漏洞检测，代理端需收集本地所有的软件版本信息，将这些信息发送到管理端后对安全漏洞数据库进行匹配关联，一旦匹配成功就会生成关于该软件的安全漏洞信息告警。其中这个安全漏洞数据库会自动创建，从以下漏洞信息网站来源获取：

* [https://canonical.com](https://canonical.com/)：Ubuntu系统CVE漏洞信息。
* [https://www.redhat.com](https://www.redhat.com/)：红帽和centos的CVE漏洞信息。
* [https://www.debian.org](https://www.debian.org/)：debian系统CVE漏洞信息。
* [https://nvd.nist.gov/](https://nvd.nist.gov/)：美国国家收集CVE漏洞信息。
* [https://feed.wazuh.com/](https://feed.wazuh.com/)：wazuh官方收集CVE和Windows的漏洞信息。

管理端会自动更新安全漏洞数据库，以此来响应最新的安全漏洞告警和补丁更新。

