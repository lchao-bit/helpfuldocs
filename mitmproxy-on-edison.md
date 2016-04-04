###在Edison平台上安装运行mitmproxy

http://iotdk.intel.com/repos/1.1/iotdk/i586/libxslt-dev_1.1.28-r0_i586.ipk
http://iotdk.intel.com/repos/1.1/iotdk/i586/libxslt_1.1.28-r0_i586.ipk

1. 修改开发板系统中的/etc/opkg/base-feeds.conf文件

加入


opkg update  

opkg install python-dev
opkg install libxml2-dev

2. opkg install python-pip 

如果出现  
    ...  
    But that file is already provided by package  * python-misc  
    opkg_install_cmd: Cannot install package python-pip.  
的错误

尝试运行opkg install --force-overwrite python-pip  

3. pip install --upgrade pip  

4. pip install lxml

5. pip install mitmproxy