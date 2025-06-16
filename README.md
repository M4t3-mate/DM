# DM
ECO
en
conf te
port ge0
service-instance ge0
encapsulation untagged
int ge0
ip add 172.16.4.1/28
connect port ge0 service-instance ge0
exit
ip route 0.0.0.0/0 172.16.4.14

port ge1 
service-instance vl100
encapsulation dot1q 100
rewrite pop 1
exit
service-instance vl200
encapsulation dot1q 200
rewrite pop 1
exit
service-instance vl999
encapsulation dot1q 999
rewrite pop 1
exit

int vl100
ip add 192.168.100.62/26
connect port ge1 service-instance vl100
int vl200
ip add 192.168.200.14/28
connect port ge1 service-instance vl200
int vl999
ip add 192.168.9.6/29
connect port ge1 service-instance vl999
do wr

int tunnel.0
ip add 10.255.255.1/30
ip mtu 1400
ip tunnel 172.16.4.1 172.16.5.1 mode gre

router ospf 1
network 10.255.255.0/30 area 0
network 192.168.100.0/26 area 0
network 192.168.200.0/28 area 0
network 192.168.9.0/29 area 0
int tunnel.0
ip ospf network point-to-point
ip ospf mtu-ignore

int vl100
ip nat inside 
int vl200
ip nat inside 
int vl999
ip nat inside 
int ge0
ip nat outside
exit
ip nat pool VL100 192.168.100.0-192.168.100.62
ip nat pool VL200 192.168.200.0-192.168.200.14
ip nat pool VL999 192.168.9.0-192.168.9.6
ip nat source dynamic inside pool VL100 overload interface ge0
ip nat source dynamic inside pool VL200 overload interface ge0
ip nat source dynamic inside pool VL999 overload interface ge0

ip pool V200 192.168.200.1-192.168.200.13
dhcp-server 200
pool V200 1
mask 28
gateway 192.168.200.14
dns 192.168.100.1
domain-name au-team.irpo
domain-search au-team.irpo
exit
exit
int vl200
dhcp-server 200

username net_admin
password P@ssw0rd
role admin
do wr

security none

