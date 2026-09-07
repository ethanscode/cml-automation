# Core Router Setup config (MALL-CORE)

# Identity & Credentials
enable
configure terminal
hostname MALL-CORE
ip domain name mall.lab
no ip domain lookup
username netops privilege 15 secret MallLab123
enable secret MallLab123
crypto key generate rsa modulus 2048
ip ssh version 2
line vty 0 4
 login local
 transport input ssh

 # Make MALL-CORE the mall's interior router & keep the VLAN database strictly local
ip routing
vtp mode transparent

# VLANs
vlan 10
 name PIZZA-PLANET
vlan 20
 name FYE
vlan 30
 name AMERICAN-EAGLE
vlan 40
 name ARCADE-NIGHTS
vlan 50
 name MALL-SECURITY
vlan 99
 name MGMT

 # Gateways
 interface Vlan10
 description Pizza Planet gateway
 ip address 10.10.10.1 255.255.255.0
interface Vlan20
 description FYE gateway
 ip address 10.10.20.1 255.255.255.0
interface Vlan30
 description American Eagle gateway
 ip address 10.10.30.1 255.255.255.0
interface Vlan40
 description Arcade Nights gateway
 ip address 10.10.40.1 255.255.255.0
interface Vlan50
 description Mall Security gateway
 ip address 10.10.50.1 255.255.255.0
interface Vlan99
 description Management
 ip address 10.10.99.1 255.255.255.0

 # Physical Ports
 interface GigabitEthernet0/0
 description Link to MALL-EDGE
 no switchport
 ip address 10.255.0.2 255.255.255.252
interface GigabitEthernet0/1
 description NETOPS automation host
 switchport mode access
 switchport access vlan 99
 spanning-tree portfast
interface GigabitEthernet0/2
 description Trunk to SW-PIZZA
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 10,99
interface GigabitEthernet0/3
 description Trunk to SW-FYE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 20,99
interface GigabitEthernet1/0
 description Trunk to SW-AE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 30,99
interface GigabitEthernet1/1
 description Trunk to SW-ARCADE
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 40,99
interface GigabitEthernet1/2
 description Trunk to SW-SEC
 switchport trunk encapsulation dot1q
 switchport mode trunk
 switchport trunk allowed vlan 50,99

 # DHCP for tenants
 ip dhcp excluded-address 10.10.10.1 10.10.10.10
ip dhcp excluded-address 10.10.20.1 10.10.20.10
ip dhcp excluded-address 10.10.30.1 10.10.30.10
ip dhcp excluded-address 10.10.40.1 10.10.40.10
ip dhcp excluded-address 10.10.50.1 10.10.50.10
ip dhcp pool PIZZA
 network 10.10.10.0 255.255.255.0
 default-router 10.10.10.1
 dns-server 8.8.8.8
ip dhcp pool FYE
 network 10.10.20.0 255.255.255.0
 default-router 10.10.20.1
 dns-server 8.8.8.8
ip dhcp pool AE
 network 10.10.30.0 255.255.255.0
 default-router 10.10.30.1
 dns-server 8.8.8.8
ip dhcp pool ARCADE
 network 10.10.40.0 255.255.255.0
 default-router 10.10.40.1
 dns-server 8.8.8.8
ip dhcp pool SECURITY
 network 10.10.50.0 255.255.255.0
 default-router 10.10.50.1
 dns-server 8.8.8.8

 # OSPF
 router ospf 1
 router-id 10.10.99.1
 passive-interface default
 no passive-interface GigabitEthernet0/0
 network 10.255.0.0 0.0.0.3 area 0
 network 10.10.0.0 0.0.255.255 area 0
end
copy running-config startup-config