# 容器安全

目前业内HIDS针对于容器安全可以做的有这么几块：

* 文件完整性监控（木马病毒分析）
* 容器异常行为审计
* 容器安全基线
* 镜像安全

wazuh官方文档并没有体现到上面这4个功能，只是很单纯记录容器创建、销毁、运行的日志。对安全来说，没有价值体现。

wazuh其实可以做容器的文件完整性监控、异常行为审计以及安全基线，因为容器本质是依赖于宿主机的磁盘和进程，所以直接监控宿主机就可以间接监控到容器里面情况。

首先，centos代理端安装docker服务。

```
[root@wazuh-centos-agent ~]# yum install -y docker
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.cn99.com
 * epel: hk.mirrors.thegigabit.com
 * extras: mirrors.ustc.edu.cn
 * updates: mirrors.ustc.edu.cn
Package 2:docker-1.13.1-208.git7d71120.el7_9.x86_64 already installed and latest version
```

启动docker服务。

```
[root@wazuh-centos-agent ~]# service docker start
Redirecting to /bin/systemctl start docker.service
```

拖取centos镜像到本地用来做测试。

```
[root@wazuh-centos-agent ~]# docker pull centos
Using default tag: latest
Trying to pull repository docker.io/library/centos ... 
latest: Pulling from docker.io/library/centos
7a0437f04f83: Pull complete 
Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
Status: Downloaded newer image for docker.io/centos:latest
```

启动centos镜像，并且让他长驻后台运行。

```
[root@wazuh-centos-agent ~]# docker run  -itd centos /bin/sh -c "while true; do echo 'hello world'; sleep 1; done"
3f5af405725cca57012dcf0139901d41247646c342f59b384a74fcfe8985f532
```

创建测试文件，使用docker的`cp`参数复制测试文件到容器里面。

```
[root@wazuh-centos-agent opt]# touch test.txt
[root@wazuh-centos-agent opt]# docker cp test.txt 3f5a:/opt/test.txt
```

寻找测试文件，可以发现它在`/var/lib/docker/overlay2/`目录下面，19399这个是centos容器的一个临时存储卷。

```
[root@wazuh-centos-agent opt]# find / -name "test.txt"
/var/lib/docker/overlay2/19399f667a533ab59589aad43cbae2e3a5548185478c42a8ee1abbce7e47cd0f/diff/opt/test.txt
/var/lib/docker/overlay2/19399f667a533ab59589aad43cbae2e3a5548185478c42a8ee1abbce7e47cd0f/merged/opt/test.txt
/opt/test.txt
```

既然在服务器内找到容器的存储位置，那么我们就可以监控`/var/lib/docker/overlay2/`目录的文件变化。

```
<syscheck>
  <directories check_all="yes" realtime="yes">/var/lib/docker/overlay2</directories>
</syscheck>
```

复制木马病毒样本到centos容器里面。

```
[root@wazuh-centos-agent Legacy-of-intrusion-master]# docker cp files/ 3f5a:/hacker
```

查看告警日志，可以看到`/var/lib/docker/overlay2/`目录下面文件新增情况。

![](<../.gitbook/assets/image (210).png>)

virustotal也可以发现容器卷里面的木马病毒。

![](<../.gitbook/assets/image (211).png>)

