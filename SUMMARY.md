# Table of contents

* [序言](README.md)
* [第一章 初识SIEM](di-yi-zhang-chu-shi-siem.md)
* [第二章 wazuh安装](di-er-zhang-wazuh-an-zhuang/README.md)
  * [2.1 系统架构](di-er-zhang-wazuh-an-zhuang/2.1-xi-tong-jia-gou.md)
  * [2.2 wazuh管理端安装](di-er-zhang-wazuh-an-zhuang/2.2-wazuh-guan-li-duan/README.md)
    * [2.2.1 Centos 7安装](di-er-zhang-wazuh-an-zhuang/2.2-wazuh-guan-li-duan/2.2.1-centos7-an-zhuang.md)
    * [2.2.2 Ubuntu 18.04安装](di-er-zhang-wazuh-an-zhuang/2.2-wazuh-guan-li-duan/2.2.2-ubuntu-18.04-an-zhuang.md)
  * [2.3 wazuh代理端安装](di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/README.md)
    * [2.3.1 Centos 7安装](di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/2.3.1-centos-7-an-zhuang.md)
    * [2.3.2 Ubuntu 18.04安装](di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/2.3.2-ubuntu-18.04-an-zhuang.md)
    * [2.3.3 Windows安装](di-er-zhang-wazuh-an-zhuang/2.3-wazuh-dai-li-duan-an-zhuang/2.3.3-windows-an-zhuang.md)
  * [2.4 管理端与代理端通信](di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/README.md)
    * [2.4.1 LINUX系统通信](di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/2.4.1-linux-xi-tong-tong-xin.md)
    * [2.4.2 Windows系统通信](di-er-zhang-wazuh-an-zhuang/2.4-guan-li-duan-yu-dai-li-duan-tong-xin/2.4.2-windows-xi-tong-tong-xin.md)
  * [2.5 puppet批量部署](di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/README.md)
    * [2.5.1 puppet server安装](di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.1-puppet-server-an-zhuang.md)
    * [2.5.2 wazuh管理端安装](di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.2-wazuh-guan-li-duan-an-zhuang.md)
    * [2.5.3 Ubuntu代理端安装](di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.3-ubuntu-dai-li-duan-an-zhuang.md)
    * [2.5.4 Windows代理端安装](di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.4-windows-dai-li-duan-an-zhuang.md)
    * [2.5.5 wazuh参数](di-er-zhang-wazuh-an-zhuang/2.5-puppet-pi-liang-bu-shu/2.5.5-wazuh-can-shu.md)
  * [2.6 ansible批量部署](di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/README.md)
    * [2.6.1  ansible Linux 安装](di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/2.6.1-ansible-linux-an-zhuang.md)
    * [2.6.2 ansible windows安装](di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/2.6.2-ansible-windows-an-zhuang.md)
    * [2.6.3 wazuh参数](di-er-zhang-wazuh-an-zhuang/2.6-ansible-pi-liang-bu-shu/2.6.3-wazuh-can-shu.md)
  * [2.7 无状态代理](di-er-zhang-wazuh-an-zhuang/2.7-wu-zhuang-tai-dai-li.md)
* [第三章 日志收集安装配置](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/README.md)
  * [Untitled](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/untitled.md)
  * [3.1 代理端收集日志](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/README.md)
    * [3.1.1 Linux日志收集](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/3.1.1-linux-ri-zhi-shou-ji.md)
    * [3.1.2 windows日志收集](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/3.1.2-windows-ri-zhi-shou-ji.md)
    * [3.1.3 远程日志收集](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.1-dai-li-duan-shou-ji-ri-zhi/3.1.3-yuan-cheng-ri-zhi-shou-ji.md)
  * [3.2 EL\(F\)K安装配置](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/README.md)
    * [3.2.1 elasticsearch](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.1-elasticsearch.md)
    * [3.2.2 filebeat](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.2-filebeat.md)
    * [3.2.3 kibana](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.3-kibana.md)
    * [3.2.4 Logstash](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.2-elfk-an-zhuang-pei-zhi/3.2.4-logstash.md)
  * [3.3 splunk安装配置](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/README.md)
    * [3.3.1 splunk server](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/3.3.1-splunk-server.md)
    * [3.3.2 splunk wazuh app](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/3.3.3-splunk-wazuh-app.md)
    * [3.3.3 splunk forward](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-splunk-an-zhuang-pei-zhi/3.3.2-splunk-forward.md)
  * [3.4 syslog导出](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.4-syslog-dao-chu.md)
  * [3.5 ELK鉴权机制](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.5-elk-jian-quan-ji-zhi/README.md)
    * [3.5.1 HTTP认证](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.5-elk-jian-quan-ji-zhi/3.5.1-http-ren-zheng.md)
    * [3.5.2 HTTPS认证](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.5-elk-jian-quan-ji-zhi/3.5.2-https-ren-zheng.md)
  * [3.6 wazuh鉴权机制](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.6-wazuh-jian-quan-ji-zhi/README.md)
    * [3.6.1 管理端与代理端通信](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.6-wazuh-jian-quan-ji-zhi/3.6.1-guan-li-duan-yu-dai-li-duan-tong-xin.md)
    * [3.6.2 wazuhAPI认证](di-san-zhang-ri-zhi-shou-ji-an-zhuang-pei-zhi/3.6-wazuh-jian-quan-ji-zhi/3.6.2-wazuhapi-ren-zheng.md)
* [第四章 wazuh共享和集群](di-si-zhang-wazuh-tong-bu-he-ji-qun/README.md)
  * [4.1 wazuh共享](di-si-zhang-wazuh-tong-bu-he-ji-qun/4.1-wazuh-tong-bu.md)
  * [4.2 wazuh集群](di-si-zhang-wazuh-tong-bu-he-ji-qun/4.2-wazuh-ji-qun.md)
* [第五章 文件完整性监控](di-wu-zhang-wen-jian-wan-zheng-xing-jian-kong/README.md)
  * [5.1 功能描述](di-wu-zhang-wen-jian-wan-zheng-xing-jian-kong/5.1-gong-neng-miao-shu.md)
  * [5.2 实战操作](di-wu-zhang-wen-jian-wan-zheng-xing-jian-kong/5.2-shi-zhan-yan-lian.md)
* [第六章 异常和恶意软件检测](di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/README.md)
  * [6.1 本地规则检测](di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/6.1-ben-di-gui-ze-jian-ce.md)
  * [6.2 VirusTotal检测](di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/6.2-virustotal-jian-ce.md)
  * [6.3 clamav检测](di-liu-zhang-yi-chang-heeyi-ruan-jian-jian-ce/6.3-clamav-jian-ce.md)
* [第N章 SIEM虚拟机](dinzhang-siem-xu-ni-ji/README.md)
  * [siemonster](dinzhang-siem-xu-ni-ji/siemonster.md)
  * [wazuh](dinzhang-siem-xu-ni-ji/wazuh.md)
  * [selks](dinzhang-siem-xu-ni-ji/selks.md)
