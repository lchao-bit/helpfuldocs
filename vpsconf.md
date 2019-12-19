 ./server_linux_amd64 -t "127.0.0.1:1560" -l "[2607:fcd0:100:8b00::6:2e95]:10089" --nocomp -mode fast2 -mtu 1300 &  
 ./udp2raw_amd64 -s -l [2607:fcd0:100:8b00::6:2e95]:10079 -r [2607:fcd0:100:8b00::6:2e95]:10089 -k socks11520 --raw-mode faketcp -a &  
 
 
 
 #!/bin/bash  
REMOTE_IP6="2402:f000:1:1501:200:5efe"  
REMOTE_IP4="166.111.21.1"  

IFACE4=`ip route show|grep default|sed -e 's/^default.*dev \([^ ]\+\).*$/\1/'`  
IP4=`ip addr show dev $IFACE4 | grep -m 1 'inet\ ' | sed -e 's/^.*inet \([^ \\]\+\)\/.*$/\1/'`  

sudo ip tunnel del sit1  # 删除已经创建的设备，若没有则忽略  
sudo ip tunnel add sit1 mode sit remote $REMOTE_IP4 local $IP4  
sudo ip link set dev sit1 up  
sudo ip -6 addr add $REMOTE_IP6:$IP4/64 dev sit1  
sudo ip -6 route add default via $REMOTE_IP6:$REMOTE_IP4 dev sit1  



sslocal -s "127.0.0.1" -p 8388 -b "0.0.0.0" -l 2166 -m aes-6-cfb -k socks11520  
sudo ./udp2raw_amd64 -c -r [2001:470:18:a9c::2]:10079 -l 127.0.0.1:10079 --raw-mode faketcp -a -k socks11520  
./client_linux_amd64 -r "127.0.0.1:10079" -l "127.0.0.1:8388"  --nocomp -mode fast2 -mtu 1300  


./udp2raw_amd64 -s -l [2001:470:18:a9c::2]:10079 -r [2001:470:18:a9c::2]:10089 -k socks11520 --raw-mode faketcp -a &
./server_linux_amd64 -t "127.0.0.1:1560" -l "[2001:470:18:a9c::2]:10089" --nocomp -mode fast2 -mtu 1300 &




 
