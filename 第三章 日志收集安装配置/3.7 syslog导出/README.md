# 3.4 syslog导出

wazuh管理端自带syslog导出功能，为了测试这个功能，使用logstash设置一个日志输入口，将wazuh管理端的日志发送到514这个端口，并且logstash输出到/opt/alert.json这个地方。

```text
[root@wazuh-manager ~]# cat /etc/logstash/conf.d/sysout.conf
input {
  udp {
     host => "0.0.0.0"
     port => "514"
   }  

}

output {
  file {
     path => "/opt/alert.json"
   }
}
```

配置好logstash文件，使用以下命令前台显示logstash运行状态。

```text
[root@wazuh-manager ~]# /usr/share/logstash/bin/logstash -f /etc/logstash/conf.d/sysout.conf 
Using bundled JDK: /usr/share/logstash/jdk
OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
WARNING: Could not find logstash.yml which is typically located in $LS_HOME/config or /etc/logstash. You can specify the path using --path.settings. Continuing using the defaults
Could not find log4j2 configuration at path /usr/share/logstash/config/log4j2.properties. Using default config which logs errors to the console
[INFO ] 2021-06-29 10:29:28.259 [main] runner - Starting Logstash {"logstash.version"=>"7.11.2", "jruby.version"=>"jruby 9.2.13.0 (2.5.7) 2020-08-03 9a89c94bcc OpenJDK 64-Bit Server VM 11.0.8+10 on 11.0.8+10 +indy +jit [linux-x86_64]"}
[INFO ] 2021-06-29 10:29:28.347 [main] writabledirectory - Creating directory {:setting=>"path.queue", :path=>"/usr/share/logstash/data/queue"}
[INFO ] 2021-06-29 10:29:28.370 [main] writabledirectory - Creating directory {:setting=>"path.dead_letter_queue", :path=>"/usr/share/logstash/data/dead_letter_queue"}
[WARN ] 2021-06-29 10:29:28.946 [LogStash::Runner] multilocal - Ignoring the 'pipelines.yml' file because modules or command line options are specified
[INFO ] 2021-06-29 10:29:28.989 [LogStash::Runner] agent - No persistent UUID file found. Generating new UUID {:uuid=>"436314af-ab02-44b9-9a78-31188c439fd7", :path=>"/usr/share/logstash/data/uuid"}
[INFO ] 2021-06-29 10:29:31.354 [Converge PipelineAction::Create<main>] Reflections - Reflections took 228 ms to scan 1 urls, producing 23 keys and 47 values 
[INFO ] 2021-06-29 10:29:32.209 [[main]-pipeline-manager] javapipeline - Starting pipeline {:pipeline_id=>"main", "pipeline.workers"=>1, "pipeline.batch.size"=>125, "pipeline.batch.delay"=>50, "pipeline.max_inflight"=>125, "pipeline.sources"=>["/etc/logstash/conf.d/sysout.conf"], :thread=>"#<Thread:0x1f5813a4 run>"}
[INFO ] 2021-06-29 10:29:33.576 [[main]-pipeline-manager] javapipeline - Pipeline Java execution initialization time {"seconds"=>1.36}
[INFO ] 2021-06-29 10:29:33.640 [[main]-pipeline-manager] javapipeline - Pipeline started {"pipeline.id"=>"main"}
[INFO ] 2021-06-29 10:29:33.790 [Agent thread] agent - Pipelines running {:count=>1, :running_pipelines=>[:main], :non_running_pipelines=>[]}
[INFO ] 2021-06-29 10:29:33.987 [[main]<udp] udp - Starting UDP listener {:address=>"0.0.0.0:514"}
[INFO ] 2021-06-29 10:29:34.266 [Api Webserver] agent - Successfully started Logstash API endpoint {:port=>9600}
[INFO ] 2021-06-29 10:29:34.325 [[main]<udp] udp - UDP listener started {:address=>"0.0.0.0:514", :receive_buffer_bytes=>"106496", :queue_size=>"2000"}
```

wazuh管理端配置syslog导出功能，地址填wazuh管理端自身地址，端口就是logstash输入的端口，等级就是发送最低告警的等级，发送的格式选择是json格式。

```text
<ossec_config>
  <syslog_output>
    <server>192.168.1.200</server>
    <port>514</port>
    <level>3</level>
    <format>json</format>
  </syslog_output>
</ossec_config>
```

重启wazuh管理端服务。

![](../.gitbook/assets/image%20%2870%29.png)

过一段时间，查看`/opt/alert.json`文件，可以发现wazuh管理端的日志已经转发过来。

![](../.gitbook/assets/image%20%2875%29.png)

