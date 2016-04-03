###Windows XP DHCPv6 客户端使用说明  
####一、安装IPv6协议  
1. 在命令行中输入ipv6 install如下图：  
![](http://nic.seu.edu.cn/_upload/article/7d/3f/8e114aea43e3b9df29a046ea9fdb/2d67e40a-2574-456e-a55e-30199f890e69.png)   
####二、运行dibbler-0.8.2-win32.exe DHCPv6 客户端  
安装组件列表如下：  
![](http://nic.seu.edu.cn/_upload/article/7d/3f/8e114aea43e3b9df29a046ea9fdb/4c5b08db-95f3-4926-96f9-7cc67fe38f1e.png)   
####三、修改Clients配置文件  
1. 单击 开始 -> 所有程序 -> Dibbler -> Client Edit config file    
2. 将以下几行用#符号注释并保存  
             iface "Local Area Connection" {  
                  ia  
                  option dns-server  
             }  
     如图：  
![](http://nic.seu.edu.cn/_upload/article/7d/3f/8e114aea43e3b9df29a046ea9fdb/5c1c57ab-2f75-40d6-bf2f-5ab26cd8d5d8.png)   
###四、运行 Client：
点击 开始 -> 所有程序 -> Dibbler -> Client Run in the console  
当出现获得2001:da80:1002前缀的IPv6地址信息时表示获取成功。如下图：  
![](http://nic.seu.edu.cn/_upload/article/7d/3f/8e114aea43e3b9df29a046ea9fdb/67604bc6-4cf0-4dde-941e-292590f07afa.png)  
验证获得的IP地址  
在命令行中输入ipconfig /all ，检查自己是否取得以2001:da8:1002前缀的IP地址、网关和DNS服务器  
###五、设置Dibbler为系统服务
单击 开始 -> 所有程序 -> Dibbler -> Client Install as service 设置Dibbler为系统服务自动运行