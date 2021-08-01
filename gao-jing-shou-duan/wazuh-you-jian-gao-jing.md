# wazuh邮件告警

[https://documentation.wazuh.com/current/user-manual/manager/automatic-reports.html](https://documentation.wazuh.com/current/user-manual/manager/automatic-reports.html)





```text
[root@wazuh-manager ~]# yum install -y postfix mailx cyrus-sasl cyrus-sasl-plain
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.163.com
 * epel: mirrors.ustc.edu.cn
 * extras: mirrors.163.com
 * updates: mirrors.163.com
Package 2:postfix-2.10.1-9.el7.x86_64 already installed and latest version
Package mailx-12.5-19.el7.x86_64 already installed and latest version
Package cyrus-sasl-2.1.26-23.el7.x86_64 already installed and latest version
Package cyrus-sasl-plain-2.1.26-23.el7.x86_64 already installed and latest version
Nothing to do
```

/etc/postfix/main.cf，

```text
#修改
inet_interfaces = all
#最下面添加
relayhost = [smtp.163.com]:25
smtp_sasl_auth_enable = yes
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_sasl_security_options = noanonymous
smtp_use_tls = yes
smtp_sasl_type = cyrus
```

/etc/postfix/sasl\_passwd

```text
[smtp.163.com]:25  test@163.com:123456
```

使用postmap产生数据库文件

```text
[root@wazuh-manager ~]# postmap /etc/postfix/sasl_passwd
```



```text
[root@wazuh-manager ~]# service postfix restart
Redirecting to /bin/systemctl restart postfix.service
```



```text
echo "Test mail from postfix" | mail -s "Test Postfix" -r "test@163.com" test@qq.com
```



![](../.gitbook/assets/image%20%28188%29.png)



```text
<global>
    <jsonout_output>yes</jsonout_output>
    <alerts_log>yes</alerts_log>
    <logall>no</logall>
    <logall_json>no</logall_json>
    <email_notification>yes</email_notification>
    <smtp_server>localhost</smtp_server>
    <email_from>test@163.com</email_from>
    <email_to>test@qq.com</email_to>
    <email_maxperhour>12</email_maxperhour>
    <email_log_source>alerts.log</email_log_source>
  </global>

  <alerts>
    <log_alert_level>3</log_alert_level>
    <email_alert_level>3</email_alert_level>
  </alerts>
```



![](../.gitbook/assets/image%20%28187%29.png)





