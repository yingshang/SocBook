# 2.1 系统架构

安装wazuh的服务端的操作系统是Linux系统，其中包括Centos、Ubuntu、红帽等Linux操作系统。本书选择是Centos Linux 7和Ubuntu 18.04系统安装wazuh服务端，一是这两个系统比较多人使用，二是其他系统跟这两个系统的安装没多大的区别。

**wazuh之间是基于客户端/服务端\(C/S\)或者管理端和代理端的形式进行通信**。wazuh管理端是代理端注册控制的中心，用于收集代理端产生的日志、软件版本情况、文件系统变化情况以及执行管理端下发的命令，并且wazuh代理端是一个轻量级代理软件，它不关心是否存在攻击行为，只管收集、监控、执行操作，wazuh管理端则是根据收集上来的信息，去匹配规则文件，找到对应的规则告警和告警等级风险，所以wazuh管理端的工作是分析日志、触发规则、告警发送、命令执行等操作。

因为最终结果是要做成一个安全监控系统，所以需要多台服务器集成不同的软件，共同构造成安全监控系统。为了让读者更加了解部署架构，本文的安装部署和配置管理都按照以下拓扑图的IP地址和系统名称去进行说明。

![&#x7CFB;&#x7EDF;&#x67B6;&#x6784;&#x56FE;](../../.gitbook/assets/fu-wu-qi-jia-gou-tu-.png)

本书撰写的时候wazuh官方陆陆续续更新软件版本，读者也不必纠结版本新旧，其更新版本大多是修复软件问题，核心功能的配置和呈现效果不会出现太大的偏差。以下表格是每台服务器安装的软件信息。



<table>
  <thead>
    <tr>
      <th style="text-align:center">&#x670D;&#x52A1;&#x5668;</th>
      <th style="text-align:center">&#x8F6F;&#x4EF6;&#x7248;&#x672C;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.200</p>
        <p>&#xFF08;wazuh&#x7BA1;&#x7406;&#x7AEF;&#xFF09;</p>
      </td>
      <td style="text-align:center">wazuh-manager-4.1.5-1.x86_64</td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.210</p>
        <p>(wazuh-worker-1)</p>
      </td>
      <td style="text-align:center">wazuh-manager-4.1.5-1.x86_64</td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.220</p>
        <p>(wazuh-worker-2)</p>
      </td>
      <td style="text-align:center">wazuh-manager-4.1.5-1.x86_64</td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.201</p>
        <p>&#xFF08;EK&#xFF09;</p>
      </td>
      <td style="text-align:center">
        <p>elasticsearch-7.11.2</p>
        <p>kibana-7.11.2</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:center">192.168.1.120</td>
      <td style="text-align:center">nginx</td>
    </tr>
    <tr>
      <td style="text-align:center"></td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.103</p>
        <p>(splunk)</p>
      </td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.104</p>
        <p>&#xFF08;splunk-cluster&#xFF09;</p>
      </td>
      <td style="text-align:center"></td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.101</p>
        <p>(wazuh centos agent)</p>
      </td>
      <td style="text-align:center">wazuh-agent-4.1.5-1.x86_64</td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.100</p>
        <p>&#xFF08;wazuh ubuntu agent&#xFF09;</p>
      </td>
      <td style="text-align:center">wazuh-agent_4.1.5-1_amd64</td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.102</p>
        <p>&#xFF08;wazuh windows agent&#xFF09;</p>
      </td>
      <td style="text-align:center">wazuh-agent-4.1.5-1</td>
    </tr>
    <tr>
      <td style="text-align:center">
        <p>192.168.1.110</p>
        <p>&#xFF08;puppet+ansible&#xFF09;</p>
      </td>
      <td style="text-align:center"></td>
    </tr>
  </tbody>
</table>






