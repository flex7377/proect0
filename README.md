# 📡 Проект: proect0

## 🖧 Устройство: (R40)

```bash
en
conf t
hostname ISP
interface range e0/0 - 3
no shutdown
interface e0/0
 ip address 100.100.111.1 255.255.255.0
 ip nat outside
interface e0/1
 ip address 172.16.4.1 255.255.255.240
 ip nat inside
interface e0/2
 ip address 172.16.5.1 255.255.255.240
 ip nat inside
interface e0/3
 ip address 172.16.6.1 255.255.255.240
 ip nat inside
ip route 0.0.0.0 0.0.0.0 100.100.111.254
access-list 1 permit any
ip nat inside source list 1 interface e0/0 overload
clock timezone +05 5 0
do wr

```
##🖧 Устройство: (R39)

```bash

en
conf t
hostname HQ-RTR
interface range e0/0, e0/1
no shutdown
interface e0/1
 ip address 172.16.4.2 255.255.255.240
 ip nat outside
interface e0/0
 no ip address
interface e0/0.100
 encapsulation dot1Q 100
 ip address 192.168.11.1 255.255.255.192
 ip nat inside
interface e0/0.200
 encapsulation dot1Q 200
 ip address 192.168.12.1 255.255.255.240
 ip nat inside
interface e0/0.999
 encapsulation dot1Q 999
 ip address 192.168.1.1 255.255.255.248
 ip nat inside
access-list 1 permit any
ip nat inside source list 1 interface e0/1 overload
ip route 0.0.0.0 0.0.0.0 172.16.4.1
username admin privilege 15 secret P@$$word
interface Tunnel0
 ip address 192.168.254.1 255.255.255.252
 tunnel source e0/1
 tunnel destination 172.16.5.2
 ip ospf authentication
 ip ospf authentication-key cisco123
crypto isakmp policy 10
 encryption aes 256
 hash sha512
 authentication pre-share
 group 24
exit
crypto isakmp key cisco123 address 172.16.5.2
crypto isakmp keepalive 10 5
crypto ipsec transform-set IPSEC esp-aes esp-sha-hmac
 mode tunnel
exit
ip access-list extended GRE
 permit gre host 172.16.4.2 host 172.16.5.2
exit
crypto map VPN 10 ipsec-isakmp
 set peer 172.16.5.2
 set transform-set IPSEC
 match address GRE
interface e0/1
 crypto map VPN
router ospf 1
 network 192.168.1.0 0.0.0.7 area 0
 network 192.168.11.0 0.0.0.63 area 0
 network 192.168.12.0 0.0.0.15 area 0
 network 192.168.254.0 0.0.0.3 area 0
 passive-interface default
 no passive-interface Tunnel0
ip dhcp excluded-address 192.168.12.14
ip dhcp pool HQ-CLI-POOL
 network 192.168.12.0 255.255.255.240
 default-router 192.168.12.14
 dns-server 192.168.11.1
 domain-name au-team.irpo
exit
clock timezone +05 5 0

ntp server 192.168.12.2
ip domain name au-team.irpo

```

##🖧 Устройство: (R41)

```bash

en
conf t
hostname BR-RTR
interface range e0/0, e0/2
no shutdown
interface e0/2
 ip address 172.16.5.2 255.255.255.240
 ip nat outside
interface e0/0
 ip address 192.168.21.1 255.255.255.224
 ip nat inside
access-list 1 permit any
ip nat inside source list 1 interface e0/2 overload
ip route 0.0.0.0 0.0.0.0 172.16.5.1
username admin privilege 15 secret P@$$word
interface Tunnel0
 ip address 192.168.254.2 255.255.255.252
 tunnel source e0/2
 tunnel destination 172.16.4.2
 ip ospf authentication
 ip ospf authentication-key cisco123
crypto isakmp policy 10
 encryption aes 256
 hash sha512
 authentication pre-share
 group 24
exit
crypto isakmp key cisco123 address 172.16.4.2
crypto isakmp keepalive 10 5
crypto ipsec transform-set IPSEC esp-aes esp-sha-hmac
 mode tunnel
exit
ip access-list extended GRE
 permit gre host 172.16.5.2 host 172.16.4.2
exit
crypto map VPN 10 ipsec-isakmp
 set peer 172.16.4.2
 set transform-set IPSEC
 match address GRE
interface e0/2
 crypto map VPN
router ospf 1
 network 192.168.21.0 0.0.0.31 area 0
 network 192.168.254.0 0.0.0.3 area 0
 passive-interface default
 no passive-interface Tunnel0
clock timezone +05 5 0

ntp server 192.168.12.2
ip domain name au-team.irpo

```

##🖧 Устройство: (R42)

```bash

en
conf t
hostname SW
interface range e0/0 - 2
no shutdown
interface e0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
interface e0/1
 switchport mode access
 switchport access vlan 100
interface e0/2
 switchport mode access
 switchport access vlan 200
vlan 999
interface vlan 999
 ip address 192.168.1.2 255.255.255.248
 no shutdown
no cdp run
clock timezone +05 5 0

```
