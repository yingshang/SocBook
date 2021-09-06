# kibana

## 代理端共享操作

因为有wazuh-api这个服务存在，所以kibana的wazuh插件就会调用其API进行增删查改。在浏览器打开kibana界面（`http://192.168.1.201:5601/app/wazuh`）。

点击management选项栏，里面就可以看到groups（管理你的代理群组）。

![](../.gitbook/assets/image%20%28109%29.png)

点击groups进行之后，找到add new group进行添加新的群组。

![](../.gitbook/assets/image%20%28100%29.png)

刷新页面查看，可以发现Centos、Ubuntu、Windows群组已经添加完毕。

![&#x6DFB;&#x52A0;&#x7FA4;&#x7EC4;&#x5B8C;&#x6210;](../.gitbook/assets/image%20%28105%29.png)

点击centos群组的名称，就能够进入到详细的页面对功能进行操作。Manage agents是添加或者删除代理端到该群组里面。Files是查看该群组的共享配置文件。

![](../.gitbook/assets/image%20%28108%29.png)

添加centos代理端到centos群组。

![](../.gitbook/assets/image%20%28107%29.png)

![&#x6DFB;&#x52A0;&#x4EE3;&#x7406;&#x7AEF;&#x5B8C;&#x6210;](../.gitbook/assets/image%20%28103%29%20%281%29.png)

查看Files选项卡，可以看到有三个文件，这三个文件待会详细说明。

![](../.gitbook/assets/image%20%2897%29.png)

## 

