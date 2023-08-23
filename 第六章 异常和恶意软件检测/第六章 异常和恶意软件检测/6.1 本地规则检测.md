# 6.1 本地规则检测

自从wazuh的3.9.0版本开始，rootcheck集成到SCA（安全基线配置），原则上说，现在rootcheck从木马检测这一块变成安全基线扫描，倾向于木马病毒检测的方向直接偏移到安全基线扫描。为什么会这么说呢？在下面rootcheck配置文件，可以看到它的检测规则文件是基于`rootkit_files.txt`和`rootkit_trojans.txt`进行匹配告警，但是可以发现这两个文件的内容是已经过时的，作为攻防对抗，就是需要与时俱进，总不能拿着过时技术去检测新的技术。

作为科普，我还是会说一下它的运行机制，以及实际效果，不建议生产使用，检测到一个文件异常就会持续生成日志，误报生成的日志非常多。

在centos代理端配置文件，会有rootcheck默认的配置，一般来说，不用修改这部分配置文件，因为已经配置完成的，不需要做额外修改。

```text
  <rootcheck>
    <disabled>no</disabled>
    <check_files>yes</check_files>
    <check_trojans>yes</check_trojans>
    <check_dev>yes</check_dev>
    <check_sys>yes</check_sys>
    <check_pids>yes</check_pids>
    <check_ports>yes</check_ports>
    <check_if>yes</check_if>

    <!-- Frequency that rootcheck is executed - every 12 hours -->
    <frequency>43200</frequency>

    <rootkit_files>/var/ossec/etc/shared/rootkit_files.txt</rootkit_files>
    <rootkit_trojans>/var/ossec/etc/shared/rootkit_trojans.txt</rootkit_trojans>

    <skip_nfs>yes</skip_nfs>
  </rootcheck>
```

查看这两个文件的特征库，可以看到是特征库内容不多并且很多都是已经过时的。

![](../.gitbook/assets/image%20%28133%29.png)

为了测试效果，将检测频率修改为30秒，以及快速检测到出现问题。

![](../.gitbook/assets/image%20%28135%29.png)

使用echo命令在/dev目录生成一个隐藏文件，在Linux系统\(.\)代表隐藏文件，所以rootcheck会觉得有问题，就会进行告警。

```text
[root@wazuh-centos-agent ~]# echo "test" > /dev/.test
```

在管理端收集告警日志，在full\_log字符看到描述说是可能是隐藏文件。

![](../.gitbook/assets/image%20%28131%29.png)

下图可以看到，这是我之前使用蜜罐系统捕捉到互联网的恶意脚本，执行这些恶意脚本测试一下rootcheck检测效果是怎样的。

![](../.gitbook/assets/image%20%28136%29.png)

批量运行脚本：

```text
import os
for root, dirs, files in os.walk("/opt/Legacy-of-intrusion-master/files", topdown=False):
    for name in files:
        print("run {}".format(name))
        os.system('cd '+root+' && ./'+name)
```

恶意脚本运行了一段时间之后，**rootcheck并没有检测到恶意脚本的行为**，相反audit还检测到一些执行操作，这一块检测到后面章节说到。

![](../.gitbook/assets/image%20%28137%29.png)

