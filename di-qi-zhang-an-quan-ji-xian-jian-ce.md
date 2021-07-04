# 第七章 安全基线检测

安全基线合规是主机安全重要的一环，它代表着主机安全防御缺陷的范围程度，将木桶短板中的短板变长，减少可攻击的范围面。

说起来很抽象，说个实际的例子：有个业务使用struts2，按照安全基线标准只能使用tomcat用户启动，但是为了图方便，直接使用root用户启动，于是struts2漏洞爆发之后，因为是root用户，黑客可以攻击的范围就变得非常大，拖库删数据都可以搞，但是一旦使用tomcat用户启动，等于把攻击范围缩小到tomcat权限目录下，损失就可以减少很多。

wazuh自带了一个叫SCA（Security Configuration Assessment）功能，直接翻译叫安全配置评估，咱们国家有等保合规标准，我喜欢叫安全基线合规，要向本地化标准看齐。

除了wazuh自身的SCA，还集成了openscap安全基线扫描评估功能。接下来将对这两种方式的配置和使用进行说明。

## SCA

### SCA运行过程

SCA按照预先定义好的安全基线标准文件（CIS）对文件进行扫描，每个代理本地都会存在一个数据库用于保存扫描文件的结果，并且在管理端也存在一个数据库，用于对比代理端和同步代理端的扫描结果数据。其中有三种结果：`pass`（通过），`failed`（失败）以及`not-applicable`（规则不适用）。

一旦SCA启动扫描操作，发现某个文件不符合安全基线标准，就会在数据库更新文件状态，并且发送更改信息到管理端，管理端就会生成日志告警。如果没有发生文件更改，代理端只会发送扫描日志结果到管理端做记录，管理端并不会生成告警日志。

### 配置说明

通过前几章学习，以下的SCA配置文件都是相似的，默认进行扫描的时间是12个小时。

```text
  <sca>
    <enabled>yes</enabled>
    <scan_on_start>yes</scan_on_start>
    <interval>12h</interval>
    <skip_nfs>yes</skip_nfs>
  </sca>
```

既然SCA的扫描是基于CIS文件去扫描的，那么在默认的配置里面并没有看到CIS文件的存在，这是因为默认会指定一个规则集目录（`/var/ossec/ruleset/sca`）放置安全基线文件，官方指出放在这个目录下面会被更新和安装覆盖，但是咱们可以使用share功能进行同步或者使用ansible进行同步安全基线文件到代理端某个文件夹内，修改配置文件调用该文件夹路径。

SCA会读取规则集目录里面的yaml文件或者yml文件，比如我现在进入到centos代理端，进入到规则集目录内，会发现只有一个安全基线文件（`cis_rhel7_linux.yml`），这是wazuh根据你当前服务器是什么操作系统就会放置不同的安全基线文件，也就是说当需要检测apache、nginx等组件的安全基线的时候，需要自己加载文件进去才可以执行检测。

### 规则运行

在wazuh管理端的SCA目录下有着一堆安全基线文件，只不过后缀为disabled，这是wazuh禁用该文件的机制识别，如果需要启动某个安全基线文件，只需将disabled后缀去掉就可以使用。

```text
[root@wazuh-manager ~]# ls /var/ossec/ruleset/sca/
cis_apache_24.yml.disabled          cis_debian10.yml.disabled             cis_rhel6_linux.yml.disabled     cis_sqlserver_2014.yml.disabled    sca_unix_audit.yml.disabled
cis_apple_macOS_10.11.yml.disabled  cis_debian7.yml.disabled              cis_rhel7_linux.yml.disabled     cis_sqlserver_2017.yml.disabled    sca_win_audit.yml.disabled
cis_apple_macOS_10.12.yml.disabled  cis_debian8.yml.disabled              cis_rhel8_linux.yml.disabled     cis_sqlserver_2019.yml.disabled    web_vulnerabilities.yml.disabled
cis_apple_macOS_10.13.yml.disabled  cis_debian9.yml.disabled              cis_sles11_linux.yml.disabled    cis_win10_enterprise.yml.disabled
cis_centos6_linux.yml.disabled      cis_mysql5-6_community.yml.disabled   cis_sles12_linux.yml.disabled    cis_win2012r2.yml.disabled
cis_centos7_linux.yml               cis_mysql5-6_enterprise.yml.disabled  cis_solaris11.yml.disabled       cis_win2016.yml.disabled
cis_centos8_linux.yml.disabled      cis_rhel5_linux.yml.disabled          cis_sqlserver_2012.yml.disabled  cis_win2019.yml.disabled
```

复制一个apache的安全基线文件到共享目录那里

```text
[root@wazuh-manager ~]# cp /var/ossec/ruleset/sca/cis_apache_24.yml.disabled /var/ossec/etc/shared/default/cis_apache_24.yml
```

直接复制过来是无法进行同步的，需要对文件进行赋权，该文件夹是ossec用户，并且里面的文件是660`rw-rw----`权限。

```text
[root@wazuh-manager ~]# chown ossec:ossec /var/ossec/etc/shared/default/cis_apache_24.yml
[root@wazuh-manager ~]# chmod 660  /var/ossec/etc/shared/default/cis_apache_24.yml
```

并且在centos7代理端配置SCA的安全基线策略。

```text
 <sca>
    <policies>
      <policy>/var/ossec/etc/shared/cis_apache_24.yml</policy>
    </policies>
  </sca>
```

由于centos7没有apache，需要使用`yum install httpd -y`命令进行安装。

安装完成之后，查看日志提示`sca.remote_commands`没有启动，这是因为管理端需要执行命令检测代理端安全基线。

![](.gitbook/assets/image%20%28149%29.png)

在代理端执行以下命令，给`local_internal_options.conf`配置文件添加参数，查了相关资料，这个文件涉及到wazuh默认配置的更改，不常用，你可以理解为这个文件就是开发者模式，可以调整相应的参数值进行调试排查错误。

```text
[root@wazuh-centos-agent ~]# echo "sca.remote_commands=1" >> /var/ossec/etc/local_internal_options.conf
```

执行完命令之后，重启代理端，再查看日志可以看到已经开始评估apache安全基线文件。

![](.gitbook/assets/image%20%28147%29.png)

管理端收集到基于apache的安全基线的日志，总共有30条安全基线配置策略，5条配置通过，18条没有配置，7条不适合，分数只有21分。

![](.gitbook/assets/image%20%28148%29.png)

### 自定义安全基线规则

我们先看一下官方的`cis_rhel7_linux.yml`文件是怎么编写的？文件是采用`yaml`格式进行编写，其中安全基线文件检测由四个部分组成，分别是`policy`（安全基线文件信息描述），`requirements`（检测前提条件），`variables`（定义变量）以及`checks`（检测清单）。















## openscap







