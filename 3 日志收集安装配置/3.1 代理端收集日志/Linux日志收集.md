# Linux日志收集

Linux系统wazuh日志收集默认配置如下：

```text
<localfile>
  <location>/var/log/*.log</location>
  <log_format>syslog</log_format>    
  <ignore_binaries>yes</ignore_binaries>
  <label key="agent.type">apache</label>
  <exclude>/var/logs/e*.log</exclude>
</localfile>

<localfile>
    <log_format>full_command</log_format>
    <command>last -n 20</command>
    <frequency>360</frequency>
</localfile>

<localfile>
  <log_format>command</log_format>
  <command>df -P</command>
  <frequency>360</frequency>
</localfile>
```

**localfile标签：**用来设置收集什么样的日志，收集什么地方的日志，localfile的子标签（属性）有很多，这边将对常用的子标签进行说明其效果作用。localfile大部分的子标签在Linux系统和Windows系统都是一样的，只有小部分适合自身的操作系统。

**location标签：**是用于表示你要收集那个地方的日志，Linux系统和windows需要在标签内输入绝对路径，可以使用\*号匹配符合的日志文件，例子中，就是收集/var/log/目录下面后缀为.log的日志文件。

**ignore\_binaries标签：**表示是否忽略二进制文件，默认是yes，这个标签可以不添加。

**label标签：**作用是在解析内容之后加标签，比如收集到有一段json日志为{"a":"b"}，最后输出的日志会变成{"a":"b","agent":{"type":"apache"}}，也就是给这段日志添加一个标识记号。

**exclude标签：**排除某个日志文件，在例子中排除/var/log中的首字母为e，后缀为.log的日志文件。

**frequency标签：**是表示收集日志的周期时间是多少，单位是秒数计算。这个标签与command和full\_command一起使用。

**log\_format标签：**表示收集的日志格式，wazuh官方支持很多种类型日志格式，具体如下面表格所示：

| 日志格式类型 | 描述 |
| :---: | :--- |
| syslog | 收集syslog类型的日志，比如Linux操作系统里面secure、message日志等。 |
| json | 收集单行的json类型的日志，注意的是，官方不支持解析json内容含有json array（json数组），比如{"a":\[{"b":"1","b":"2"}\]} |
| snort-full | 收集snort入侵检测生成的日志。目前NIDS（网络入侵检测系统）主要用suricata，所以这个类型不深入。 |
| squid | 收集squid反向代理的日志。 |
| eventlog | 用于收集windows操作系统的日志，支持每个Windows版本。 |
| eventchannel | 用于收集windows操作系统的日志，支持适用适用于Windows Vista和更高版本 |
| audit | 用于收集Linux系统的audit工具的日志 |
| mysql\_log | 用于收集mysql运行的日志。 |
| postgresql\_log | 用于收集postgresql\_log运行的日志。 |
| nmapg | 用于收集nmap扫描产生的日志。 |
| iis | 用于收集iis服务器运行的日志。 |
| command | 用于收集root用户执行某个命令输出单行的日志内容。 |
| full\_command | 用于收集root用户执行某个命令输出完整（多行）的日志内容。 |
| multi-line | 用于收集某个应用的多行日志，并且还需要加上要收集多少行的内容才可以。&lt;log\_format&gt;multi-line: 数字行&lt;/log\_format&gt; |

接下来，**用三个配置文件的例子说明**。

①syslog

```text
<localfile>
  <location>/var/log/*.log</location>
  <log_format>syslog</log_format>    
  <ignore_binaries>yes</ignore_binaries>
  <label key="agent.type">apache</label>
  <exclude>/var/logs/e*.log</exclude>
</localfile>
```

提取secure日志里面的一行SSH断开连接日志，secure日志格式是syslog类型，用wazuh日志测试工具**ossec-logtest**进行测试验证是否解析成功。

```text
Jun 26 06:11:57 wazuh-centos-agent sshd[15929]: Received disconnect from 192.168.1.110 port 59346:11: disconnected by user
```

![](../../.gitbook/assets/image%20%2854%29.png)

②多行日志

```text
<localfile>
  <log_format>command</log_format>
  <command>df -P</command>
  <frequency>360</frequency>
</localfile>
```

command的类型里面是收集执行df -P命令的日志，但是ossec-logtest在测试的时候不支持多行输入，多行输入的话，它就会对每一行进行解码处理，最后无法解码正确的日志内容。

![](../../.gitbook/assets/image%20%2846%29.png)

只不过当把多行内容的换行转义为"\n"的话，还是解码不正常，这种结果是属于正常范围之内的，**因为所输入的日志内容不符合规则解码规则，这一块会在后面章节说明**。

![](../../.gitbook/assets/image%20%2849%29.png)

③json日志

第一行可以看到json的数组是不能解析的，最后没有输出内容；第二行使用普通的key value形式，就可以解析为json内容。

![](../../.gitbook/assets/clipboard%20%2810%29.png)



