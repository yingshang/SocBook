# 其他问题

## 日志存储路径迁移

```text
mv /var/ossec/logs/* /data/wazuh/
mount --bind /data/wazuh/ /var/ossec/logs
echo "/data/wazuh /var/ossec/logs/ none defaults,bind 0 0" >> /etc/fstab
```

