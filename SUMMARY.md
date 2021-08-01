# Table of contents

* [序言](README.md)
* [初识SIEM](di-yi-zhang-chu-shi-siem.md)

## wazuh

* [wazuh安装配置](wazuh/di-er-zhang-wazuh-an-zhuang/README.md)
  * [系统架构](wazuh/di-er-zhang-wazuh-an-zhuang/2.1-xi-tong-jia-gou.md)
  * [wazuh管理端安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.2-wazuh-guan-li-duan/README.md)
    * [Centos 7安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.2-wazuh-guan-li-duan/2.2.1-centos7-an-zhuang.md)
    * [Ubuntu 18.04安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.2-wazuh-guan-li-duan/2.2.2-ubuntu-18.04-an-zhuang.md)
  * [wazuh代理端安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/README.md)
    * [Centos 7安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/2.3.1-centos-7-an-zhuang.md)
    * [Ubuntu 18.04安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/2.3.2-ubuntu-18.04-an-zhuang.md)
    * [Windows安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/2.3.3-windows-an-zhuang.md)
  * [管理端与代理端通信](wazuh/di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/README.md)
    * [LINUX系统通信](wazuh/di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/2.4.1-linux-xi-tong-tong-xin.md)
    * [Windows系统通信](wazuh/di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/2.4.2-windows-xi-tong-tong-xin.md)
    * [无代理通信](wazuh/di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/wu-dai-li-tong-xin.md)
  * [puppet批量部署](wazuh/di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/README.md)
    * [puppet server安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.1-puppet-server-an-zhuang.md)
    * [wazuh管理端安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.2-wazuh-guan-li-duan-an-zhuang.md)
    * [Ubuntu代理端安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.3-ubuntu-dai-li-duan-an-zhuang.md)
    * [Windows代理端安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.4-windows-dai-li-duan-an-zhuang.md)
    * [wazuh参数](wazuh/di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.5-wazuh-can-shu.md)
  * [ansible批量部署](wazuh/di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/README.md)
    * [ansible Linux 安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/2.6.1-ansible-linux-an-zhuang.md)
    * [ansible windows安装](wazuh/di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/2.6.2-ansible-windows-an-zhuang.md)
    * [wazuh参数](wazuh/di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/2.6.3-wazuh-can-shu.md)
* [日志收集安装配置](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/README.md)
  * [代理端收集日志](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/README.md)
    * [Linux日志收集](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/3.1.1-linux-ri-zhi-shou-ji.md)
    * [windows日志收集](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/3.1.2-windows-ri-zhi-shou-ji.md)
    * [远程日志收集](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/3.1.3-yuan-cheng-ri-zhi-shou-ji.md)
  * [EL\(F\)K安装配置](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/README.md)
    * [elasticsearch](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.1-elasticsearch.md)
    * [filebeat](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.2-filebeat.md)
    * [kibana](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.3-kibana.md)
    * [Logstash](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.4-logstash.md)
  * [splunk安装配置](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/README.md)
    * [splunk server](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/3.3.1-splunk-server.md)
    * [splunk wazuh app](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/3.3.3-splunk-wazuh-app.md)
    * [splunk forward](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/3.3.2-splunk-forward.md)
  * [syslog导出](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-syslog-dao-chu.md)
  * [ELK鉴权机制](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.5-elk-jian-quan-ji-zhi/README.md)
    * [HTTP认证](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.5-elk-jian-quan-ji-zhi/3.5.1-http-ren-zheng.md)
    * [HTTPS认证](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.5-elk-jian-quan-ji-zhi/3.5.2-https-ren-zheng.md)
  * [wazuh鉴权机制](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.6-wazuh-jian-quan-ji-zhi/README.md)
    * [管理端与代理端通信](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.6-wazuh-jian-quan-ji-zhi/3.6.1-guan-li-duan-yu-dai-li-duan-tong-xin.md)
    * [wazuhAPI认证](wazuh/di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.6-wazuh-jian-quan-ji-zhi/3.6.2-wazuhapi-ren-zheng.md)
* [wazuh共享和集群](wazuh/di-si-zhang-wazuh-tong-bu-he-ji-qun/README.md)
  * [wazuh共享](wazuh/di-si-zhang-wazuh-tong-bu-he-ji-qun/4.1-wazuh-tong-bu.md)
  * [wazuh集群](wazuh/di-si-zhang-wazuh-tong-bu-he-ji-qun/4.2-wazuh-ji-qun.md)
* [文件完整性监控](wazuh/di-wu-zhang-wen-jian-wan-zheng-xing-jian-kong/README.md)
  * [功能描述](wazuh/di-wu-zhang-wen-jian-wan-zheng-xing-jian-kong/5.1-gong-neng-miao-shu.md)
  * [实战操作](wazuh/di-wu-zhang-wen-jian-wan-zheng-xing-jian-kong/5.2-shi-zhan-yan-lian.md)
* [异常和恶意软件检测](wazuh/di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/README.md)
  * [本地特征检测](wazuh/di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/6.1-ben-di-gui-ze-jian-ce.md)
  * [VirusTotal检测](wazuh/di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/6.2-virustotal-jian-ce.md)
  * [clamav检测](wazuh/di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/6.3-clamav-jian-ce.md)
* [安全基线检测](wazuh/di-qi-zhang-an-quan-ji-xian-jian-ce/README.md)
  * [SCA](wazuh/di-qi-zhang-an-quan-ji-xian-jian-ce/7.1-sca.md)
  * [openscap](wazuh/di-qi-zhang-an-quan-ji-xian-jian-ce/untitled.md)
* [命令内容监控](wazuh/di-ba-zhang-ming-ling-nei-rong-jian-kong.md)
* [安全漏洞检测](wazuh/di-jiu-zhang-an-quan-lou-dong-jian-kong-he-jian-ce.md)
* [主动防御](wazuh/di-shi-zhang-zhu-dong-fang-yu.md)
* [自定义规则](wazuh/di-shi-er-zhang-zi-ding-yi-gui-ze.md)
* [第三方服务集成](wazuh/di-san-fang-fu-wu-ji-cheng.md)

## NIDS

* [suricata](nids/untitled.md)

## 安全审计

* [audit](an-quan-shen-ji/audit.md)
* [sysmon](an-quan-shen-ji/sysmon.md)
* [osquery](an-quan-shen-ji/osquery.md)

## 指标可视化 <a id="tu-xing-ke-shi-hua"></a>

* [Untitled](tu-xing-ke-shi-hua/untitled.md)

## 告警手段

* [wazuh邮件告警](gao-jing-shou-duan/wazuh-you-jian-gao-jing.md)
* [ESalert告警](gao-jing-shou-duan/esalert-gao-jing.md)
* [自定义告警](gao-jing-shou-duan/zi-ding-yi-gao-jing.md)

## 虚拟机

* [wazuh](xu-ni-ji/wazuh.md)
* [misp](xu-ni-ji/misp.md)
* [selks](xu-ni-ji/selks.md)
* [siemonster](xu-ni-ji/siemonster.md)

