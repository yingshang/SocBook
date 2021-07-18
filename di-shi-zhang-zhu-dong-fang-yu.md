# 第十章  主动防御

主动防御是基于威胁对象触发的规则告警而进行主动拦截的行为，防御威胁对象继续对代理端进行攻击。

## 主动防御配置

要触发主动防御，主要有两个流程：

1. 修改管理端配置文件，开启主动防御模式。
2. 使用拦截脚本，定义拦截方式和拦截频率次数。

### 修改管理端配置文件

```text
[root@wazuh-manager opt]# cat  /var/ossec/etc/ossec.conf | grep -A 6 "<active"
  <active-response>
    <command>host-deny</command>
    <location>local</location>
    <rules_id>5716</rules_id>
    <timeout>600</timeout>
  </active-response>
```

说明一下主动防御这些标签作用。

<table>
  <thead>
    <tr>
      <th style="text-align:center">&#x6807;&#x7B7E;</th>
      <th style="text-align:left">&#x4F5C;&#x7528;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">command</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x8981;&#x4F7F;&#x7528;&#x7684;&#x62E6;&#x622A;&#x811A;&#x672C;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">location</td>
      <td style="text-align:left">
        <p>&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x89C4;&#x5219;&#x7684;&#x4F4D;&#x7F6E;&#xFF0C;&#x5176;&#x4E2D;&#x6709;&#x56DB;&#x79CD;&#x9009;&#x578B;&#xFF1A;local&#x3001;server&#x3001;&#x67D0;&#x4EE3;&#x7406;&#x7AEF;ID&#x53F7;&#xFF0C;all&#x3002;</p>
        <p>local&#xFF1A;&#x89E6;&#x53D1;&#x89C4;&#x5219;&#x53EA;&#x5F71;&#x54CD;&#x5F53;&#x524D;&#x4EE3;&#x7406;&#x7AEF;&#x3002;</p>
        <p>server: &#x89E6;&#x53D1;&#x89C4;&#x5219;&#x5F71;&#x54CD;&#x7BA1;&#x7406;&#x7AEF;&#x3002;</p>
        <p>&#x67D0;&#x4EE3;&#x7406;&#x7AEF;ID&#x53F7;: &#x89E6;&#x53D1;&#x89C4;&#x5219;&#x5F71;&#x54CD;&#x67D0;&#x4EE3;&#x7406;&#x7AEF;&#x3002;</p>
        <p>all: &#x89E6;&#x53D1;&#x89C4;&#x5219;&#x5F71;&#x54CD;&#x6240;&#x6709;&#x4EE3;&#x7406;&#x7AEF;&#xFF0C;<b>&#x7B49;&#x4E8E;&#x5168;&#x5C40;&#x5C01;&#x7981;</b>&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:center">rules_id</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x7684;&#x89C4;&#x5219;ID&#x53F7;</td>
    </tr>
    <tr>
      <td style="text-align:center">timeout</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x5468;&#x671F;&#x65F6;&#x95F4;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">level</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x7684;&#x544A;&#x8B66;&#x7B49;&#x7EA7;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">rules_group</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x89E6;&#x53D1;&#x4E3B;&#x52A8;&#x9632;&#x5FA1;&#x7684;&#x89C4;&#x5219;&#x7EC4;&#xFF0C;&#x591A;&#x4E2A;&#x89C4;&#x5219;&#x7EC4;&#x4F7F;&#x7528; <b><code>|</code></b> &#x5206;&#x5272;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:center">repeated_offenders</td>
      <td style="text-align:left">&#x5B9A;&#x4E49;&#x5A01;&#x80C1;&#x5BF9;&#x8C61;&#x591A;&#x6B21;&#x653B;&#x51FB;&#x7684;&#x65F6;&#x95F4;&#x9012;&#x589E;&#x5217;&#x8868;&#xFF0C;&#x6700;&#x591A;&#x53EF;&#x4EE5;&#x8BBE;&#x7F6E;5&#x4E2A;&#x9012;&#x589E;&#x60C5;&#x51B5;&#x3002;</td>
    </tr>
  </tbody>
</table>



