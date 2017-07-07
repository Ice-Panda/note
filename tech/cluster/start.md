# 集群配置

## ubuntu设置
### 固定 IP

`sudo vim /etc/network/interfaces`

```shell 
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto enp0s3
#iface enp0s3 inet dhcp
iface enp0s3 inet static
address 192.168.1.161 
netmask 255.255.255.0
gateway 192.168.1.1
dns-nameservers 114.114.114.114 
```

