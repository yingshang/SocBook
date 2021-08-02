# 规则集

在该ruleset/文件夹中，您会找到所有常见的规则和解码器。Wazuh更新过程中将覆盖或修改此文件夹中的所有文件，因此请不要在该文件夹中编辑文件或添加自定义文件。如果我们需要执行一些[自定义](https://documentation.wazuh.com/3.11/user-manual/ruleset/custom.html#ruleset-custom)更改，则将使用该etc/文件夹。您可以在此处添加自己的解码器/规则文件或使用默认的local\_decoder.xml和local\_rules.xml文件。

```text
/var/ossec/
        ├─ etc/
        │   ├─ decoders/
        |   |        └─ local_decoder.xml
        │   └─ rules/
        |         └─ local_rules.xml
        └─ ruleset/
                ├─ decoders/
                └─ rules/
```

ossec-logtest,日志调试工具

![](../.gitbook/assets/image%20%28189%29.png)

![](../.gitbook/assets/image%20%28190%29.png)



