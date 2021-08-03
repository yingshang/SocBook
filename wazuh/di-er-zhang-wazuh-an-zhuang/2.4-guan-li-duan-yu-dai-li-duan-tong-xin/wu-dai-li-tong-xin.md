# 无代理通信



```text
[root@wazuh-manager ~]# /var/ossec/agentless/register_host.sh add root@192.168.1.101 123456
*Host root@192.168.1.101 added.
```



```text
[root@wazuh-manager ~]# /var/ossec/agentless/register_host.sh list
*Available hosts: 
root@192.168.1.101
```



```text
<agentless>
  <type>ssh_integrity_check_linux</type>
  <frequency>60</frequency>
  <host>root@192.168.1.101</host>
  <state>periodic</state>
  <arguments>/bin /etc /sbin</arguments>
</agentless>
```



```text
2021/08/03 09:42:09 ossec-agentlessd: ERROR: Test failed for 'ssh_integrity_check_linux' (126). Ignoring.
```



```text
[root@wazuh-manager agentless]# yum install expect -y
```



![](../../../.gitbook/assets/image%20%28193%29.png)

ssh连接过来

![](../../../.gitbook/assets/image%20%28192%29.png)



![](../../../.gitbook/assets/image%20%28194%29.png)





```text
<agentless>
  <type>ssh_generic_diff</type>
  <frequency>60</frequency>
  <host>root@192.168.1.101</host>
  <state>periodic_diff</state>
  <arguments>ls -la /etc; cat /etc/passwd</arguments>
</agentless>
```



![](../../../.gitbook/assets/image%20%28191%29.png)



