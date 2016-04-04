###在Edison平台上安装运行mitmproxy

1. 修改开发板系统中的/etc/opkg/base-feeds.conf文件  
加入  
src/gz all http://repo.opkg.net/edison/repo/all  
src/gz edison http://repo.opkg.net/edison/repo/edison  
src/gz core2-32 http://repo.opkg.net/edison/repo/core2-32  
执行  
opkg update  

1. 安装pip  
opkg install python-pip  
如果出现  
   <code>But that file is already provided by package  * python-misc  
    \* opkg\_install\_cmd: Cannot install package python-pip.</code>  
的错误  
执行  
opkg install --force-overwrite python-pip

1. 更新pip  
pip install --upgrade pip

1. 安装libxslt和libxslt-dev  
wget http://iotdk.intel.com/repos/1.1/iotdk/i586/libxslt-dev\_1.1.28-r0\_i586.ipk  
wget http://iotdk.intel.com/repos/1.1/iotdk/i586/libxslt\_1.1.28-r0\_i586.ipk  
opkg install libxslt_1.1.28-r0_i586.ipk
opkg install libxslt-dev_1.1.28-r0_i586.ipk




1. 安装libtiff-dev和libjpeg-dev  
opkg install libtiff-dev  
opkg install libjpeg-dev  


1. 修正系统当前时间

1. 安装mitmproxy  
pip install -v mitmproxy   
