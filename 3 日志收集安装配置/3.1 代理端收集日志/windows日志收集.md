# windows日志收集

windows的wazuh代理端的日志收集配置部分中，有三个子标签内容是Windows特有的，其他的子标签都跟Linux系统一样。

在Windows收集日志有两种渠道，**eventlog和eventchannel**，它们的区别就是windows版本支持，Eventlog支持每个windows操作系统，而Eventchannel只支持Windows Vista以上的版本，总的来说，一个旧的事件接口和新的事件接口，**对此我推荐使用Eventlog，因为在后面主动防御的章节中，Eventchannel不能满足我们的需求**。

```text
<localfile>
    <location>Security</location>
    <log_format>eventchannel</log_format>
    <query>Event/System[EventID != 5145 and EventID != 5156]</query>
</localfile>

<localfile>
    <location>System</location>
    <log_format>eventlog</log_format>
</localfile>
```

在location子标签内容中，除了可以使用文件路径，还提供了收集windows事件查看器里面的日志内容的接口类型，具体如表格所示。

