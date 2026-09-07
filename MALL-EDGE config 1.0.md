# Edge Router Setup config (MALL-EDGE)

enable
configure terminal
hostname MALL-EDGE
ip domain name mall.lab
no ip domain lookup
username netops privilege 15 secret MallLab123
enable secret MallLab123