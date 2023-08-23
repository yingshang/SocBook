# kibana

## 代理端共享操作

因为有wazuh-api这个服务存在，所以kibana的wazuh插件就会调用其API进行增删查改。在浏览器打开kibana界面（`http://192.168.1.201:5601/app/wazuh`）。

点击management选项栏，里面就可以看到groups（管理你的代理群组）。

![](<../.gitbook/assets/image (92).png>)

点击groups进行之后，找到add new group进行添加新的群组。

![](<../.gitbook/assets/image (94).png>)

刷新页面查看，可以发现Centos、Ubuntu、Windows群组已经添加完毕。

![添加群组完成](<../.gitbook/assets/image (95).png>)

点击centos群组的名称，就能够进入到详细的页面对功能进行操作。Manage agents是添加或者删除代理端到该群组里面。Files是查看该群组的共享配置文件。

![](<../.gitbook/assets/image (96).png>)

添加centos代理端到centos群组。

![](<../.gitbook/assets/image (107).png>)

![添加代理端完成](<../.gitbook/assets/image (98).png>)

查看Files选项卡，可以看到有三个文件，这三个文件待会详细说明。

![](<../.gitbook/assets/image (99).png>)

##
