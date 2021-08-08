# 资产信息监控

wazuh提供收集代理端各种信息（软件安装信息、操作系统版本信息、端口开放信息）能力，通过`Syscollector`模块收集代理端信息整合起来存储到管理端的`sqlite`数据库中。

管理端配置文件默认已经设置。

```text
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

<table>
  <thead>
    <tr>
      <th style="text-align:center">&#x53C2;&#x6570;</th>
      <th style="text-align:center">&#x9ED8;&#x8BA4;&#x503C;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">
        <p>disabled
          <br />
        </p>
        <p>
          <br />
        </p>
      </td>
      <td style="text-align:center">no</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x5173;&#x95ED;syscollector&#x529F;&#x80FD;</td>
    </tr>
    <tr>
      <td style="text-align:center">interval</td>
      <td style="text-align:center">1h</td>
      <td style="text-align:left">&#x626B;&#x63CF;&#x68C0;&#x6D4B;&#x65F6;&#x95F4;&#x5468;&#x671F;</td>
    </tr>
    <tr>
      <td style="text-align:center">scan_on_start</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x91CD;&#x542F;&#x670D;&#x52A1;&#x5C31;&#x5F00;&#x59CB;&#x626B;&#x63CF;&#x68C0;&#x6D4B;</td>
    </tr>
    <tr>
      <td style="text-align:center">hardware</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;&#x786C;&#x4EF6;&#x4FE1;&#x606F;</td>
    </tr>
    <tr>
      <td style="text-align:center">os</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;&#x64CD;&#x4F5C;&#x7CFB;&#x7EDF;&#x4FE1;&#x606F;</td>
    </tr>
    <tr>
      <td style="text-align:center">network</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;&#x7F51;&#x7EDC;&#x4FE1;&#x606F;</td>
    </tr>
    <tr>
      <td style="text-align:center">packages</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;&#x8F6F;&#x4EF6;&#x5305;&#x4FE1;&#x606F;</td>
    </tr>
    <tr>
      <td style="text-align:center">ports</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;&#x7AEF;&#x53E3;&#x5F00;&#x653E;&#x4FE1;&#x606F;&#xFF0C; <b>all=&#x2019;no&#x2019;&#x8BBE;&#x7F6E;&#x53EA;&#x6536;&#x96C6;&#x76D1;&#x542C;&#x7AEF;&#x53E3;&#x3002;</b>
      </td>
    </tr>
    <tr>
      <td style="text-align:center">processes</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;&#x8FDB;&#x7A0B;&#x8FD0;&#x884C;&#x4FE1;&#x606F;</td>
    </tr>
    <tr>
      <td style="text-align:center">hotfixes</td>
      <td style="text-align:center">yes</td>
      <td style="text-align:left">&#x662F;&#x5426;&#x6536;&#x96C6;Windows&#x8865;&#x4E01;&#x5B89;&#x88C5;&#x4FE1;&#x606F;</td>
    </tr>
  </tbody>
</table>



```text
[root@wazuh-manager ~]# ls /var/ossec/queue/db/
000.db  003.db  006.db  006.db-journal  007.db  008.db  009.db  global.db  global.db-journal  wdb
```

将006.db复制到本地，使用navicat进行查看

![](../.gitbook/assets/image%20%28210%29.png)



sys\_processes表

![](../.gitbook/assets/image%20%28209%29.png)







