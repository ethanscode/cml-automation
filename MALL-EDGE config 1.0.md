# Edge Router Setup config (MALL-EDGE)

# Identity & Credentials
enable
configure terminal
hostname MALL-EDGE
ip domain name mall.lab
no ip domain lookup
username netops privilege 15 secret MallLab123
enable secret MallLab123

# SSH
crypto key generate rsa modulus 2048
ip ssh version 2
line vty 0 4
 login local
 transport input ssh

 # Interface descriptions
 interface GigabitEthernet1
 description ISP uplink
 ip address dhcp
 ip nat outside
 no shutdown
interface GigabitEthernet2
 description Link to MALL-CORE
 ip address 10.255.0.1 255.255.255.252
 ip nat inside
 no shutdown
interface Loopback0
 description Management
 ip address 10.10.0.1 255.255.255.255

 # NAT 
 ip access-list standard NAT-INSIDE
 permit 10.10.0.0 0.0.255.255
ip nat inside source list NAT-INSIDE interface GigabitEthernet1 overload

# OSPF
router ospf 1
 router-id 10.10.0.1
 passive-interface default
 no passive-interface GigabitEthernet2
 network 10.10.0.1 0.0.0.0 area 0
 network 10.255.0.0 0.0.0.3 area 0
 default-information originate

 # Turn on programmable interfaces & save
netconf-yang
restconf
ip http secure-server
ip http authentication local
end
copy running-config startup-config

# Verify setup
show ip interface brief
ping 8.8.8.8
show ip route
show platform software yang-management process

