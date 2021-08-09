# 规则集

## 规则描述

在`ruleset`目录可以看到官方定义的日志编码规则文件（decoders）和日志告警文件（rules），这两个目录最好不要做任何修改，当规则升级的时候会把原来修改记录给覆盖掉。

```text
[root@wazuh-manager ~]# tree /var/ossec/ruleset/
/var/ossec/ruleset/
├── decoders
│   ├── 0005-wazuh_decoders.xml
│   ├── 0006-json_decoders.xml
│   ├── 0010-active-response_decoders.xml
│   ├── 0015-aix-ipsec_decoders.xml
│   ├── 0025-apache_decoders.xml
│   ├── 0030-arpwatch_decoders.xml
│   ├── 0035-asterisk_decoders.xml
│   ├── 0040-auditd_decoders.xml
│   ├── 0045-barracuda_decoders.xml
│   ├── 0050-checkpoint_decoders.xml
│   ├── 0051-checkpoint-smart1_decoders.xml
│   ├── 0055-cimserver_decoders.xml
│   ├── 0060-cisco-estreamer_decoders.xml
│   ├── 0064-cisco-asa_decoders.xml
│   ├── 0065-cisco-ios_decoders.xml
│   ├── 0070-cisco-vpn_decoders.xml
│   ├── 0075-clamav_decoders.xml
│   ├── 0080-courier_decoders.xml
│   ├── 0085-dovecot_decoders.xml
│   ├── 0090-dragon-nids_decoders.xml
│   ├── 0095-dropbear_decoders.xml
│   ├── 0100-fortigate_decoders.xml
│   ├── 0105-freeipa_decoders.xml
│   ├── 0110-ftpd_decoders.xml
│   ├── 0115-grandstream_decoders.xml
│   ├── 0120-horde_decoders.xml
│   ├── 0125-hp_decoders.xml
│   ├── 0130-imapd_decoders.xml
│   ├── 0135-imperva_decoders.xml
│   ├── 0140-kernel_decoders.xml
│   ├── 0145-mailscanner_decoders.xml
│   ├── 0150-mysql_decoders.xml
│   ├── 0155-named_decoders.xml
│   ├── 0160-netscaler_decoders.xml
│   ├── 0165-netscreen_decoders.xml
│   ├── 0170-nginx_decoders.xml
│   ├── 0175-ntpd_decoders.xml
│   ├── 0180-openbsd_decoders.xml
│   ├── 0185-openldap_decoders.xml
│   ├── 0190-openvpn_decoders.xml
│   ├── 0195-oscap_decoders.xml
│   ├── 0200-ossec_decoders.xml
│   ├── 0205-pam_decoders.xml
│   ├── 0210-pix_decoders.xml
│   ├── 0215-portsentry_decoders.xml
│   ├── 0220-postfix_decoders.xml
│   ├── 0225-postgresql_decoders.xml
│   ├── 0230-proftpd_decoders.xml
│   ├── 0235-puppet_decoders.xml
│   ├── 0240-pure-ftpd_decoders.xml
│   ├── 0245-racoon_decoders.xml
│   ├── 0250-redis_decoders.xml
│   ├── 0255-roundcube_decoders.xml
│   ├── 0260-rsa-auth-manager_decoders.xml
│   ├── 0265-rshd_decoders.xml
│   ├── 0270-samba_decoders.xml
│   ├── 0275-sendmail_decoders.xml
│   ├── 0280-serv-u_decoders.xml
│   ├── 0285-snort_decoders.xml
│   ├── 0290-solaris_decoders.xml
│   ├── 0295-sonicwall_decoders.xml
│   ├── 0300-sophos_decoders.xml
│   ├── 0305-squid_decoders.xml
│   ├── 0310-ssh_decoders.xml
│   ├── 0315-su_decoders.xml
│   ├── 0320-sudo_decoders.xml
│   ├── 0325-suhosin_decoders.xml
│   ├── 0330-symantec_decoders.xml
│   ├── 0335-telnet_decoders.xml
│   ├── 0340-trend-osce_decoders.xml
│   ├── 0345-unbound_decoders.xml
│   ├── 0350-unix_decoders.xml
│   ├── 0355-vm-pop3_decoders.xml
│   ├── 0360-vmware_decoders.xml
│   ├── 0365-vpopmail_decoders.xml
│   ├── 0370-vsftpd_decoders.xml
│   ├── 0375-web-accesslog_decoders.xml
│   ├── 0378-mariadb_decoders.xml
│   ├── 0379-dpkg_decoders.xml
│   ├── 0380-windows_decoders.xml
│   ├── 0385-wordpress_decoders.xml
│   ├── 0390-zeus_decoders.xml
│   ├── 0395-sqlserver_decoders.xml
│   ├── 0400-identity_guard_decoders.xml
│   ├── 0405-mongodb_decoders.xml
│   ├── 0410-docker_decoders.xml
│   ├── 0415-jenkins_decoders.xml
│   ├── 0420-vshell_decoders.xml
│   ├── 0425-qualysguard_decoders.xml
│   ├── 0430-cylance_decoders.xml
│   ├── 0435-owncloud_decoders.xml
│   ├── 0440-proxmox-ve_decoders.xml
│   ├── 0445-exim_decoders.xml
│   ├── 0450-openvas_decoders.xml
│   ├── 0455-pfsense_decoders.xml
│   ├── 0460-kaspersky_decoders.xml
│   ├── 0465-azure_decoders.xml
│   ├── 0470-panda-paps_decoders.xml
│   ├── 0475-mcafee_decoders.xml
│   ├── 0480-perdition_decoders.xml
│   ├── 0485-nextcloud_decoders.xml
│   ├── 0490-junos_decoders.xml
│   ├── 0500-macos-sshd_decoders.xml
│   └── 0505-paloalto_decoders.xml
├── rules
│   ├── 0010-rules_config.xml
│   ├── 0015-ossec_rules.xml
│   ├── 0016-wazuh_rules.xml
│   ├── 0020-syslog_rules.xml
│   ├── 0025-sendmail_rules.xml
│   ├── 0030-postfix_rules.xml
│   ├── 0035-spamd_rules.xml
│   ├── 0040-imapd_rules.xml
│   ├── 0045-mailscanner_rules.xml
│   ├── 0050-ms-exchange_rules.xml
│   ├── 0055-courier_rules.xml
│   ├── 0060-firewall_rules.xml
│   ├── 0065-pix_rules.xml
│   ├── 0070-netscreenfw_rules.xml
│   ├── 0075-cisco-ios_rules.xml
│   ├── 0080-sonicwall_rules.xml
│   ├── 0085-pam_rules.xml
│   ├── 0090-telnetd_rules.xml
│   ├── 0095-sshd_rules.xml
│   ├── 0100-solaris_bsm_rules.xml
│   ├── 0105-asterisk_rules.xml
│   ├── 0110-ms_dhcp_rules.xml
│   ├── 0115-arpwatch_rules.xml
│   ├── 0120-symantec-av_rules.xml
│   ├── 0125-symantec-ws_rules.xml
│   ├── 0130-trend-osce_rules.xml
│   ├── 0135-hordeimp_rules.xml
│   ├── 0140-roundcube_rules.xml
│   ├── 0145-wordpress_rules.xml
│   ├── 0150-cimserver_rules.xml
│   ├── 0155-dovecot_rules.xml
│   ├── 0160-vmpop3d_rules.xml
│   ├── 0165-vpopmail_rules.xml
│   ├── 0170-ftpd_rules.xml
│   ├── 0175-proftpd_rules.xml
│   ├── 0180-pure-ftpd_rules.xml
│   ├── 0185-vsftpd_rules.xml
│   ├── 0190-ms_ftpd_rules.xml
│   ├── 0195-named_rules.xml
│   ├── 0200-smbd_rules.xml
│   ├── 0205-racoon_rules.xml
│   ├── 0210-vpn_concentrator_rules.xml
│   ├── 0215-policy_rules.xml
│   ├── 0220-msauth_rules.xml
│   ├── 0225-mcafee_av_rules.xml
│   ├── 0230-ms-se_rules.xml
│   ├── 0235-vmware_rules.xml
│   ├── 0240-ids_rules.xml
│   ├── 0245-web_rules.xml
│   ├── 0250-apache_rules.xml
│   ├── 0255-zeus_rules.xml
│   ├── 0260-nginx_rules.xml
│   ├── 0265-php_rules.xml
│   ├── 0270-web_appsec_rules.xml
│   ├── 0275-squid_rules.xml
│   ├── 0280-attack_rules.xml
│   ├── 0285-systemd_rules.xml
│   ├── 0290-firewalld_rules.xml
│   ├── 0295-mysql_rules.xml
│   ├── 0300-postgresql_rules.xml
│   ├── 0305-dropbear_rules.xml
│   ├── 0310-openbsd_rules.xml
│   ├── 0315-apparmor_rules.xml
│   ├── 0320-clam_av_rules.xml
│   ├── 0325-opensmtpd_rules.xml
│   ├── 0330-sysmon_rules.xml
│   ├── 0335-unbound_rules.xml
│   ├── 0340-puppet_rules.xml
│   ├── 0345-netscaler_rules.xml
│   ├── 0350-amazon_rules.xml
│   ├── 0360-serv-u_rules.xml
│   ├── 0365-auditd_rules.xml
│   ├── 0375-usb_rules.xml
│   ├── 0380-redis_rules.xml
│   ├── 0385-oscap_rules.xml
│   ├── 0390-fortigate_rules.xml
│   ├── 0395-hp_rules.xml
│   ├── 0400-openvpn_rules.xml
│   ├── 0405-rsa-auth-manager_rules.xml
│   ├── 0410-imperva_rules.xml
│   ├── 0415-sophos_rules.xml
│   ├── 0420-freeipa_rules.xml
│   ├── 0425-cisco-estreamer_rules.xml
│   ├── 0430-ms_wdefender_rules.xml
│   ├── 0435-ms_logs_rules.xml
│   ├── 0440-ms_sqlserver_rules.xml
│   ├── 0445-identity_guard_rules.xml
│   ├── 0450-mongodb_rules.xml
│   ├── 0455-docker_rules.xml
│   ├── 0460-jenkins_rules.xml
│   ├── 0470-vshell_rules.xml
│   ├── 0475-suricata_rules.xml
│   ├── 0480-qualysguard_rules.xml
│   ├── 0485-cylance_rules.xml
│   ├── 0490-virustotal_rules.xml
│   ├── 0495-proxmox-ve_rules.xml
│   ├── 0500-owncloud_rules.xml
│   ├── 0505-vuls_rules.xml
│   ├── 0510-ciscat_rules.xml
│   ├── 0515-exim_rules.xml
│   ├── 0520-vulnerability-detector_rules.xml
│   ├── 0525-openvas_rules.xml
│   ├── 0530-mysql_audit_rules.xml
│   ├── 0535-mariadb_rules.xml
│   ├── 0540-pfsense_rules.xml
│   ├── 0545-osquery_rules.xml
│   ├── 0550-kaspersky_rules.xml
│   ├── 0555-azure_rules.xml
│   ├── 0560-docker_integration_rules.xml
│   ├── 0565-ms_ipsec_rules.xml
│   ├── 0570-sca_rules.xml
│   ├── 0575-win-base_rules.xml
│   ├── 0580-win-security_rules.xml
│   ├── 0585-win-application_rules.xml
│   ├── 0590-win-system_rules.xml
│   ├── 0595-win-sysmon_rules.xml
│   ├── 0600-win-wdefender_rules.xml
│   ├── 0601-win-vipre_rules.xml
│   ├── 0602-win-wfirewall_rules.xml
│   ├── 0605-win-mcafee_rules.xml
│   ├── 0610-win-ms_logs_rules.xml
│   ├── 0615-win-ms-se_rules.xml
│   ├── 0620-win-generic_rules.xml
│   ├── 0625-cisco-asa_rules.xml
│   ├── 0625-mcafee_epo_rules.xml
│   ├── 0630-nextcloud_rules.xml
│   ├── 0635-owlh-zeek_rules.xml
│   ├── 0640-junos_rules.xml
│   ├── 0675-panda-paps_rules.xml
│   ├── 0680-checkpoint-smart1_rules.xml
│   ├── 0685-macos-sshd_rules.xml
│   ├── 0690-gcp_rules.xml
│   └── 0700-paloalto_rules.xml
├── sca
│   ├── cis_apache_24.yml.disabled
│   ├── cis_apple_macOS_10.11.yml.disabled
│   ├── cis_apple_macOS_10.12.yml.disabled
│   ├── cis_apple_macOS_10.13.yml.disabled
│   ├── cis_centos6_linux.yml.disabled
│   ├── cis_centos7_linux.yml
│   ├── cis_centos8_linux.yml.disabled
│   ├── cis_debian10.yml.disabled
│   ├── cis_debian7.yml.disabled
│   ├── cis_debian8.yml.disabled
│   ├── cis_debian9.yml.disabled
│   ├── cis_mysql5-6_community.yml.disabled
│   ├── cis_mysql5-6_enterprise.yml.disabled
│   ├── cis_rhel5_linux.yml.disabled
│   ├── cis_rhel6_linux.yml.disabled
│   ├── cis_rhel7_linux.yml.disabled
│   ├── cis_rhel8_linux.yml.disabled
│   ├── cis_sles11_linux.yml.disabled
│   ├── cis_sles12_linux.yml.disabled
│   ├── cis_solaris11.yml.disabled
│   ├── cis_sqlserver_2012.yml.disabled
│   ├── cis_sqlserver_2014.yml.disabled
│   ├── cis_sqlserver_2017.yml.disabled
│   ├── cis_sqlserver_2019.yml.disabled
│   ├── cis_win10_enterprise.yml.disabled
│   ├── cis_win2012r2.yml.disabled
│   ├── cis_win2016.yml.disabled
│   ├── cis_win2019.yml.disabled
│   ├── sca_unix_audit.yml.disabled
│   ├── sca_win_audit.yml.disabled
│   └── web_vulnerabilities.yml.disabled
└── VERSION

3 directories, 269 files

```

所以需要修改规则的时候，可以在`local_decoder.xml`和`local_rules.xml`文件进行修改，这两个文件执行优先级比默认的规则要高。

```text
[root@wazuh-manager ~]# tree /var/ossec/etc/
/var/ossec/etc/
├── decoders
│   └── local_decoder.xml
├── rules
    └── local_rules.xml
```



## 日志调试

从代理端`secure`日志文件提取SSH登录日志做日志编码和规则告警测试。

```text
Aug  5 10:11:55 wazuh-centos-agent sshd[7650]: Accepted password for root from 192.168.1.1 port 14933 ssh2
```

使用`ossec-logtest`日志测试功能，发现在wazuh 4.1版本之后就会被丢弃，官方推荐使用`wazuh-logtest`日志调试工具。

![](../.gitbook/assets/image%20%28206%29.png)

通过`ossec-logtest`和`wazuh-logtest`对比，`wazuh-logtest`多了MITRE ATT&CK框架描述日志行为。

![](../.gitbook/assets/image%20%28208%29.png)

通过上述图片可以发现，wazuh处理日志有三个阶段

* **预解码阶段（pre-decode）**，在日志中提取常用的字段。
* **解码阶段（decode）**，分析日志类型，提取该日志的字段信息。
* **规则匹配阶段（Rule match）**，根据解码阶段找到对应的规则文件，接着匹配关键字找到对应的告警规则。





## 自定义规则



