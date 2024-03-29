# 7.2 SCA

## SCA运行过程

SCA按照预先定义好的安全基线标准文件（CIS）对文件进行扫描，每个代理本地都会存在一个数据库用于保存扫描文件的结果，并且在管理端也存在一个数据库，用于对比代理端和同步代理端的扫描结果数据。其中有三种结果：`pass`（通过），`failed`（失败）以及`not-applicable`（规则不适用）。

一旦SCA启动扫描操作，发现某个文件不符合安全基线标准，就会在数据库更新文件状态，并且发送更改信息到管理端，管理端就会生成日志告警。如果没有发生文件更改，代理端只会发送扫描日志结果到管理端做记录，管理端并不会生成告警日志。

## 配置说明

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

## 规则运行

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

![](../../.gitbook/assets/image%20%28150%29.png)

在代理端执行以下命令，给`local_internal_options.conf`配置文件添加参数，查了相关资料，这个文件涉及到wazuh默认配置的更改，不常用，你可以理解为这个文件就是开发者模式，可以调整相应的参数值进行调试排查错误。

```text
[root@wazuh-centos-agent ~]# echo "sca.remote_commands=1" >> /var/ossec/etc/local_internal_options.conf
```

执行完命令之后，重启代理端，再查看日志可以看到已经开始评估apache安全基线文件。

![](../../.gitbook/assets/image%20%28148%29.png)

管理端收集到基于apache的安全基线的日志，总共有30条安全基线配置策略，5条配置通过，18条没有配置，7条不适合，分数只有21分。

![](../../.gitbook/assets/image%20%28149%29.png)

## 自定义安全基线规则

我们先看一下官方的`cis_rhel7_linux.yml`文件是怎么编写的？文件是采用`yaml`格式进行编写，其中安全基线文件检测由四个部分组成，分别是`policy`（安全基线文件信息描述），`requirements`（检测前提条件），`variables`（定义变量）以及`checks`（检测清单）。

![](../../.gitbook/assets/image%20%28147%29.png)

下面表格是展示安全基线规则检测组成四部分的详细参数名和参数内容，**核心参数主要是rules和condition，这两块组成了规则检测的重要条件**。

| 参数 | 字段 | 必填项 | 字段类型 | 允许值 | 描述 |
| :---: | :---: | :---: | :---: | :---: | :--- |
| policy（必填） | id | 是 | 字符串 | 任意字符串 | 定义安全基线扫描规则的标识 |
|  | file | 是 | 字符串 | 任意字符串 | 定义该安全基线扫描规则的文件名 |
|  | name | 是 | 字符串 | 任意字符串 | 定义安全基线扫描规则的名字 |
|  | description | 是 | 字符串 | 任意字符串 | 定义安全基线扫描规则的描述信息 |
|  | references | 否 | 字符串数组 | 任意字符串 | 定义安全基线扫描规则的来源信息 |
| Requirements（选填） | title | 是 | 字符串 | 任意字符串 | 定义判别规则是否适用的标题 |
|  | description | 是 | 字符串 | 任意字符串 | 定义判别规则是否适用的描述信息 |
|  | condition | 是 | 字符串 | all,any,none | 定义判别规则的条件判断 |
|  | rules | 是 | 字符串数组 | 任意字符串 | 定义判别规则的规则数组 |
| variables（选填） | variable\_name | 是 | 字符串数组 | 任意字符串 | 定义变量信息，用于规则引用 |
| checks（必填） | id | 是 | 整型 | 任意数字 | 定义检测规则的ID号 |
|  | title | 是 | 字符串 | 任意字符串 | 定义检测规则的标题 |
|  | description | 否 | 字符串 | 任意字符串 | 定义检测规则的描述信息 |
|  | rationale | 否 | 字符串 | 任意字符串 | 定义检测规则的原因描述 |
|  | remediation | 否 | 字符串 | 任意字符串 | 定义检测规则的修复建议 |
|  | compliance | 否 | 字符串数组 | 任意字符串 | 定义检测规则的符合什么标准 |
|  | references | 否 | 字符串数组 | 任意字符串 | 定义检测规则的来源信息 |
|  | condition | 是 | 字符串 | all,any,none | 定义检测规则的判别条件 |
|  | rules | 是 | 字符串数组 | 任意字符串 | 定义检测规则的判别规则 |

### Condition

有三种条件判断在规则集进行识别检测的时候进行条件判断，这三个条件判断是：

* all，满足所识别规则才能pass，否则显示failed，相当于and判断条件。
* any，只要满足规则集内的一条就显示pass，否则显示failed，相当于or判断条件。
* none，不满足规则集所有规则就显示pass，否则显示failed，相当于not判断条件。

官方除了定义pass（通过）和failed（失败）状态，还定义non-applicable（不适用），一般来说不适用的原因是找不到需要检测的文件或者文件夹。

### Rules

基线规则可以识别文件（`f`）、文件夹（`d`）、进程信息（`p`）、命令信息（`c`）以及注册表信息（`r`），其中注册表信息限windows系统使用。

当进行内容检测的时候，会检测文件内容，对目录的文件进行递归检测，检测进程信息数据内容，对命令输出内容进行检测以及windows注册表信息的检测。

用以下的例子来说明一下规则的匹配：

#### （1）文件内容匹配

①匹配文件内容（某一行）有test内容字符串，这里内容匹配是全量匹配，不是内容包含匹配。

```text
'f:$test_file -> test'
```

②匹配文件内容（某一行）包含test内容字符串，这里使用到正则匹配，`r`表示正则匹配操作符，`\s*`就是字符串贪婪匹配到XXX内容。

```text
'f:$test_file -> r:\s*test'
```



③匹配文件内容（某一行）既包含test内容字符串，也包含abc内容字符串，其中`&&`操作符就是同时满足匹配条件。

```text
'f:$test_file -> r:test && r:abc'
```


④匹配文件内容（全部）不包含test内容字符串，其中`!`作用就是取反，变成不匹配XXX内容。

```text
'f:$test_file -> !r:test'
```


⑤匹配文件内容（某一行）既开头包含`#`字符，也包含test内容字符串，其中`^`符合匹配开头XXX内容

```text
'f:$test_file -> r:^# && r:test'
```


⑥匹配文件内容（某一行）包含test=数字，其中`n`代表匹配整型类型，后面compare是关键字取`\d`内容与`1`进行比较，除了`==`（等于）还有`>`（大于）、`<`（小于）、`！=`（不等于）、`>=`（大于或等于）、`<=`（小于或不等于）。


```text
'f:$test_file -> n:test=(\d) compare == 1'
```

⑦匹配文件内容（某一行）不包含test内容，与`!`不同的是，`!`只是局部取反，`not`只全部取反。

```text
'not f:$test_file -> r:test'
```

#### （2）目录匹配

①检测是否存在/etc/目录

```text
'd:/etc'
```


②检测/etc/目录是否存在passwd文件

```text
'd:/etc -> passwd'
```


③检测/etc/passwd这个文件内容是否有root字符串

```text
'd:/etc -> passwd -> r:root'
```

#### 
（3）进程匹配

①匹配进程为sshd，经实际测试可以匹配sshd进程，但是其他进程都无法匹配，我取ps aux的命令和systemctl服务名字放进去匹配，提示都无法匹配到，如果需要匹配进程，可以使用命令进行匹配相关字符串。


```text
'p:sshd'
```

#### （4）命令匹配

①调用ps aux命令查看httpd进程是否存在

```text
'c:ps aux -> r:http'
```

#### 
（5）注册表匹配

①检测注册表是否存在wazuh服务


```text
'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\OssecSvc'=
```

②检测注册表里面wazuh服务是否存在DisplayName这个键值

```text
'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\OssecSvc ->DisplayName'
```


③检测注册表里面wazuh服务DisplayName这个键值是否等于wazuh字符串

```text
'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\OssecSvc ->DisplayName ->wazuh'
```

### 实战

对于一些字段信息根据个人需要进行填写，我这边都是随便写的。我定义了两个变量名字：`test_file`和`none_file2`，其中`none_file2`里面的文件是不存在的，我设置这个条件取测试前提条件`requirements`里面`condition`的`any`条件匹配。

接着我设置了两条检测规则，分为是ID`5100`通过调用命令去查找httpd进程是否存在，ID`5101`号去匹配`testfile`文件是否存在`test`字符串。如果你有其他的需求，可以参考上面规则的匹配进行相关的替换即可。

```text
[root@wazuh-centos-agent ~]# cat /var/ossec/ruleset/sca/test.yml 
policy:
  id: "test"
  file: "test.yml"
  name: "规则名字，随便填"
  description: "规则描述，随便填"
  references:
    - https://www.test.com

requirements:
  title: "前提条件标题，随便填"
  description: "前提条件描述，随便填"
  condition: any
  rules:
    - 'f:$test_file'
    - 'f:$none_file'

variables:
  $test_file: /etc/testfile
  $none_file: /etc/nonefile,/etc/nonefile1

checks:
  - id: 5100 
    title: "找httpd进程"
    description: "规则描述"
    rationale: "原因描述"
    remediation: "修复建议"
    compliance:
      - cis: ["1.1.1.1"]
      - cis_csc: ["5.1"]
      - pci_dss: ["2.2.5"]
      - tsc: ["CC6.3"]
    references:
      - AJ Lewis, "LVM HOWTO", https://tldp.org/HOWTO/LVM-HOWTO/
    condition: all
    rules:
      - 'c:ps aux -> r:http'

  - id: 5101 
    title: "匹配testfile文件"
    description: "规则描述"
    rationale: "原因描述"
    remediation: "修复建议"
    compliance:
      - cis: ["1.1.1.2"]
      - cis_csc: ["5.1"]
      - pci_dss: ["2.2.5"]
      - tsc: ["CC6.3"]
    references:
      - AJ Lewis, "LVM HOWTO", https://tldp.org/HOWTO/LVM-HOWTO/
    condition: all
    rules:
      - 'f:$test_file -> r:test'
```

代理端配置文件新增基线规则路径。更新完成之后，重启代理端服务。

```text
  <sca>
    <enabled>yes</enabled>
    <scan_on_start>yes</scan_on_start>
    <interval>30s</interval>
    <skip_nfs>yes</skip_nfs>
    <policies>
      <policy>/var/ossec/etc/shared/cis_apache_24.yml</policy>
      <policy>/var/ossec/ruleset/sca/test.yml</policy>
    </policies>
  </sca>
```

需新建testfile，因为我们设置前提条件，两个变量文件必须有个条件是存在的。

```text
[root@wazuh-centos-agent ~]# touch /etc/testfile
```

接着在管理端接收到代理端基线扫描的发送过来的告警信息。

![](../../.gitbook/assets/image%20%28152%29.png)

左边告警信息是查看`testfile`是否存在`test`内容，因为只创建文件，没有往里面填充内容，所以显示失败；右边告警信息是执行`ps aux`命令查看是否存在http进程。

![](../../.gitbook/assets/image%20%28153%29.png)

接着我们根据相应规则整改基线。

```text
#填充test内容
[root@wazuh-centos-agent ~]# echo "test" > /etc/testfile 

#安装apache，并且启动它
[root@wazuh-centos-agent ~]# yum install httpd -y
[root@wazuh-centos-agent ~]# service httpd start
Redirecting to /bin/systemctl start httpd.service
```

一段时间后，就收到了满分基线！！！两条检测规则都通过了。

![](../../.gitbook/assets/image%20%28154%29.png)

对比之前失败结果，现在就显示已经通过安全基线检测。

![](../../.gitbook/assets/image%20%28155%29.png)

