# CCNA 1

- Crossover cables
	- ay hagten zi b3d
	- router to PC
	- wireless router w switch

- Straight through

	- ay hagten msh zi b3d 
	- switch to router
	- switch to pc
	- switch to server
---
### Speed and duplex
S1(config-if)# duplex full
S1(config-if)# speed 100

### verify the default switch configuration

-Switch> enable
 Switch#

### Examine the current switch configuration.

- Switch# show running-config

### Assign a name to a switch.

-Switch# configure terminal
 Switch(config)# hostname S1

### Secure access to the console line.

-S1# configure terminal
-S1(config)# line console 0
 S1(config-line)# password letmein
 S1(config-line)# login

### Secure privileged mode access.

-S1> enable
S1# configure terminal
S1(config)# enable password c1$c0

### Verify your configurations by examining the contents of the running-configuration file

-S1# show running-configuration

### Configure an encrypted password to secure access to privileged mode.

-S1# config t
S1(config)# enable secret itsasecret

### Verify that the enable secret password is added to the configuration file.

-S1# show run

### Encrypt the enable and console passwords.

-S1# config t
S1(config)# service password-encryption

### Configure a message of the day (MOTD) banner.

-S1# config t
S1(config)# banner motd "This is a secure system. Authorized Access Only!"

### Save the configuration file.

-S1# copy running-config startup-config

### Configure S1 with an IP address.

-S1# configure terminal
Enter configuration commands, one per line. End with CNTL/Z.
S1(config)# interface vlan 1
S1(config-if)# ip address 192.168.1.253 255.255.255.0
S1(config-if)# no shutdown
S1(config-if)# ip default-gateway 192.168.1.252

### Assign cisco as the vty password and enable login.

-R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit


### Configure and activate interfaces on the router.

-R1(config)# interface g0/0/0
R1(config-if)# ip address 192.168.0.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad::1/64
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# description Connected to F0/5 on S1
R1(config-if)# no shutdown
R1(config-if)# exit


### To enable IPv6 routing, enter the ipv6 unicast-routing command.

-R1(config)# ipv6 unicast-routing


### Set the clock on the router.

-R1# clock set 15:30:00 25 Dec 2020


### Set the minimum password length to 10.

-RTA(config)# security password min-length 10

### Disable DNS lookup.
-Prevent the router from attempting to resolve incorrectly entered commands as domain names.

-RTA(config)# no ip domain-lookup


## FOR SSH:

### More secure version of SSH
S1(config)# ip ssh version 2

### Set the domain name 

-RTA(config)# ip domain-name CCNA.com

### crypto key generate rsa command

-RTA(config)# crypto key generate rsa

### Create a user of your choosing with a strong encrypted password.

-RTA(config)# username any_user secret any_password

### Configure all VTY lines for SSH access and use the local user profiles for authentication.

-RTA(config)# line vty 0 4
-RTA(config-line)# transport input ssh
-RTA(config-line)# login local


### Block anyone for three minutes who fails to log in after four attempts within a two-minute period.

-RTA(config)# login block-for 180 attempts 4 within 120

### Set the EXEC mode timeout to 6 minutes on the VTY lines.

-RTA(config-line)# exec-timeout 6

### Disable all unused switch ports.

-SW1(config)# interface range F0/2-24, G0/2
-SW1(config-if-range)# shutdown


### for all ip address details 

-SW1(config)# ip config / all




# CCNA 2
## Module 3 VLANs

### Show Commands
- S1# show vlan brief -> show vlans with ports
- S1# show show vlan {brief | id (vlan-id )| name (vlan-name) | summary}
- S1# show interfaces fa0/18 switchport -> data and voice vlans
- S1# show dtp interface fa 0/1 
- Show interface trunk
- show interfaces switchport

### Configure Vlan
- Switch(config)# vlan (vlan-id)
- Switch(config-vlan)# name (vlan-name)
- Switch(config-vlan)# end

### Vlan port assignment
- Switch(config)# interface interface-id
- Switch(config-if)# switchport mode access
- Switch(config-if)# switchport access vlan (vlan-id)
- Switch(config-if)# mls qos trust cos
- Switch(config-if)# switchport voice vlan (vlan-id)

### Change Vlan on port
- Switch(config-if)# no switchport access vlan -> vlan 1
- Switch(config-if)# switchport access vlan (vlan-id) -> different vlan

### * Caution: Before deleting a VLAN, reassign all member ports to a different VLAN

### Delete vlans
- S1(config)# no vlan (vlan-id)
- S1(config)# delete flash:vlan.dat OR delete vlan.dat -> all vlans

### Trunk configuration
- Switch(config-if)# switchport mode trunk
- Switch(config-if)# switchport trunk native vlan (vlan-id)
- Switch(config-if)# switchport trunk allowed vlan (vlan-list)
- switchport nonegotiate -> turn off DTP

## Module 4 InterVLAN routing
### Show Commands Router on a Stick

- R1# show ip route | begin Gateway
- R1# show ip interface brief <------------------------------------
- R1# show interfaces g0/0/1.10
- S1# show interfaces trunk

### General show commands


### Router on a Stick configuration
- R1 Subinterface Configuration
	- R1(config)# interface G0/0/1.10
	- R1(config-subif)# description Default Gateway for VLAN 10
	- R1(config-subif)# encapsulation dot1Q 10
	- R1(config-subif)# ip add 192.168.10.1 255.255.255.0
	- R1(config-subif)# exit
	- 
	- R1(config)# interface G0/0/1.20
	- R1(config-subif)# description Default Gateway for VLAN 20
	- R1(config-subif)# encapsulation dot1Q 20
	- R1(config-subif)# ip add 192.168.20.1 255.255.255.0
	- R1(config-subif)# exit

### Layer 3 Switches configuration
- S1 layer 3 Configuration
	- D1(config)# ip routing

	- D1(config)# vlan 10
	- D1(config-vlan)# name LAN10
	- D1(config-vlan)# vlan 20
	- D1(config-vlan)# name LAN20
	- D1(config-vlan)# exit
	- D1(config)#
	- 
	- D1(config)# interface vlan 10
	- D1(config-if)# description Default Gateway SVI for 192.168.10.0/24
	- D1(config-if)# ip add 192.168.10.1 255.255.255.0
	- D1(config-if)# no shut
	- D1(config-if)# exit
	- 
	- D1(config)# interface GigabitEthernet1/0/6
	- D1(config-if)# description Access port to PC1
	- D1(config-if)# switchport mode access
	- D1(config-if)# switchport access vlan 10
	- D1(config-if)# exit

### Routing configuration on layer 3 Switch

- D1(config)# interface GigabitEthernet0/0/1
- D1(config-if)# description routed Port Link to R1
- D1(config-if)# no switchport
- D1(config-if)# ip address 10.10.10.2 255.255.255.0
- D1(config-if)# no shut
- D1(config-if)# exit
- D1(config)#
- D1(config)# ip routing
- D1(config)# router ospf 10
- D1(config-router)# network 192.168.10.0 0.0.0.255 area 0
- D1(config-router)# network 192.168.20.0 0.0.0.255 area 0 
- D1(config-router)# network 10.10.10.0 0.0.0.3 area 0


## Module 6 EtherChannel

### Show commands
- show interfaces port-channel 1
- show etherchannel summary
- show etherchannel port-channel
- show interfaces f0/1 etherchannel -> physical member interface of the EtherChannel
- show run | begin interface port-channel

### Modes
- On -> force channel, doesn't exchange packets
- PAgP desirable / LACP active -> active negotiate
- PAgP auto / LACP passive -> passive negotiate

### Configuration guidelines
- Speed and duplex match
- VLAN match or trunk
- Range of allowed VLANs 

### LACP configuration 

- S1(config)# interface range FastEthernet 0/1 - 2
- shutdown
- S1(config-if-range)# channel-group 1 mode <ins>active</ins>
   Creating a port-channel interface Port-channel 1
- S1(config-if-range)# exit
- S1(config)# interface port-channel 1
- S1(config-if)# switchport mode trunk
- S1(config-if)# switchport trunk allowed vlan 1,2,20

### Etherchannel trunk
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk



## Module 7 DHCPv4
### Show commands
- show running-config | section dhcp
- show ip dhcp binding
- show ip dhcp server statistics

### DHCP Server
- R1(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.9
- R1(config)# ip dhcp excluded-address 192.168.10.254
- R1(config)# ip dhcp pool LAN-POOL-1
- R1(dhcp-config)# network 192.168.10.0 255.255.255.0
- R1(dhcp-config)# default-router 192.168.10.1 -> default gateway for switch

- R1(dhcp-config)# dns-server 192.168.11.5
- R1(dhcp-config)# domain-name example.com
- R1(dhcp-config)# end
- R1#

### Disable the Cisco IOS DHCPv4 Server
```
R1(config)# no service dhcp
R1(config)# service dhcp
R1(config)# 
```

### DHCP relay
```
R1(config)# interface g0/0/0
R1(config-if)# ip helper-address 192.168.11.6
R1(config-if)# end
R1#
```

### DHCP Client
```
SOHO(config)# interface G0/0/1
SOHO(config-if)# ip address dhcp
SOHO(config-if)# no shutdown
```

## Module 9 FHRP - HSRP
### Configure HSRP
- R1(config)# interface g0/1
- R1(config-if)# standby version 2
- R1(config-if)# standby 1 ip 192.168.1.254
- R1(config-if)# standby 1 priority 150 -> to make active (def: 100)
- R1(config-if)# standby 1 preempt
## Module 11 Port Security
## <ins>Port Security: </ins>

### Show commands
- S1# show port-security interface (interface) -> Display the current port security settings
- S1# show port-security -> display port security settings for the switch
- S1# show port-security address -> display addresses on all ports
- S1# show run | begin interface (interface) -> check for sticky address

### Enable Port Security (Access ports only)

- S1(config)# interface f0/1
- S1(config-if)# switchport port-security
	- can be used with: aging, mac-address, maximum, violation

### Limit and Learn MAC Addresses

- For limiting: 
	- Switch(config-if)# switchport port-security maximum (value).
	
- For learning:
	- Manual: 
		- Switch(config-if)# switchport port-security mac-address (mac-address)
	- Dynamically Learned – Sticky:
		- Switch(config-if)# switchport port-security mac-address sticky
		- dynamically learn the MAC address and “stick” them to the running configuration

### Port Security Aging

- Absolute - The secure addresses on the port are deleted after the specified aging time.
- Inactivity - The secure addresses on the port are deleted if they are inactive for a specified time.
- Switch(config-if)# switchport port-security aging {static | time (time) | type {absolute | inactivity}}

### Port Security Violation Modes

- Switch(config-if)# switchport port-security violation {shutdown | restrict | protect}
- Do not drop packets
	- S1(config-if-range)# switchport port-security violation restrict
- shutdown is default -> goes to error-disabled state
- in show interface:
	- err-disabled = secure-shutdown in show port-security


### <ins>Mitigate STP Attacks: </ins>

### Show commands
- S1# show running-config | begin span -> global
- S1# show spanning-tree summary -> global
- S1# spanning-tree interface type/number detail -> interface


### Configure portfast

- Only enable PortFast on access ports.
- PortFast on inter switch links can create a spanning-tree loop.
- On an interface: 
	- S1(config-if)# switchport mode access
	- S1(config-if)# spanning-tree portfast

- Global (all access ports):
	- S1(config)# spanning-tree portfast default


### Configure BPDU Guard
- Re-enable ports -> S1# errdisable recovery cause psecure_violation
- S1(config-if)# spanning-tree bpduguard enable
- OR
- S1(config)# spanning-tree portfast bpduguard default -> global (access ports)

## UNUSED in labs
### <ins>Mitigate VLAN Attacks: </ins>

### Mitigate VLAN hopping attacks

- S1(config-if): switchport mode access -> disable DTP (non-trunk)
- Disable unused ports and put them in an unused VLAN
- S1(config-if-range): switchport mode trunk
- S1(config-if-range): switchport nonegotiate -> disable DTP on trunks
- S1(config-if-range): switchport trunk native vlan (vlan_number) -> set another native vlan

### <ins>Mitigate DHCP Attacks: </ins>


### Show Commands
- S1# show ip dhcp snooping ->verify DHCP snooping settings
- S1# show ip dhcp snooping binding -> view clients that received DHCP information

### Implement DHCP Snooping

- S1(config): ip dhcp snooping -> Enable DHCP snooping
- S1(config-if): ip dhcp snooping trust -> On trusted ports
- S1(config-if): ip dhcp snooping limit rate (packets-per-second) -> On untrusted interfaces, limit the number of DHCP discovery messages

- S1(config): ip dhcp snooping (vlan) -> Enable DHCP snooping by VLAN, or by a range of VLANs

### <ins>Mitigate ARP Attacks: </ins>

### Implement ARP inspection

- Dynamic ARP inspection (DAI) requires DHCP snooping
- To mitigate the chances of ARP spoofing andARP poisoning, follow these DAI implementation guidelines:
	-  Enable DHCP snooping globally.
	-  Enable DHCP snooping on selected VLANs.
	- Enable DAI on selected VLANs.
		- ip arp inspection vlan (vlan)
	- Configure trusted interfaces for DHCP snooping and ARP inspection.
		- ip arp inspection trust

### Can also validate ARP messages
- S1(config): ip arp inspection validate {(src-mac) (dst-mac) (ip)} -> drop wrong packets

## Module 12

- renew dhcp lease from host -> ipconfig /renew


## Module 14 Router

### Show Commands <-----------
- Can replace ip with ipv6
R1# show ip interface brief
R1# show running-config interface interface-type number
R1# show interfaces
R1# show ip interface
R1# show ip route


### <ins>Router Configs: </ins>

Router> enable
Router# configure terminal
Router(config)# hostname R1
R1(config)# enable secret class
R1(config)# line console 0
R1(config-line)# logging synchronous -> delay to prevent interruption
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# transport input ssh telnet
R1(config-line)# exit
R1(config)# service password-encryption

R1(config)# ipv6 unicast-routing
R1(config)# interface gigabitethernet 0/0/0
R1(config-if)# description Link to LAN 1
R1(config-if)# ip address 10.0.1.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
R1(config-if)# ipv6 address fe80::1:a link-local
R1(config-if)# no shutdown
R1(config-if)# exit
R1# copy running-config startup-config


## Module 15 IP Routing

### <ins>Static Routes in the IP Routing Table: </ins>

- Router(config)# ip route (network-address) (subnet-mask) 
   { ip-address | exit-intf [ip-address]} [distance]
	- R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2 -> def (quad zero)
	- R1(config)# ip route 172.16.1.0 255.255.255.0 172.16.2.2 -> static
	- R1(config)# ip route 172.16.1.0 255.255.255.0 s0/1/0->direct connect
	- R1(config)# ip route 172.16.1.0 255.255.255.0 s0/1/0 172.16.2.2 ->full

- Router(config)# ipv6 route (ipv6-prefix/prefix-length) 
   {ipv6-address | exit-intf [ipv6-address]} [distance]
	- R1(config)# ipv6 route ::/0 2001:db8:acad:2::2 -> def
	- R1(config)# ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:2::2
	- R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0 -> direct
	- R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0 fe80::2 -> fully
- Default route is 0.0.0.0/0 , ::/0 -> zero bits need to match

### <ins>Static Floating Routes in the IP Routing Table: </ins>
- R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2 -> static
- R1(config)# ip route 0.0.0.0 0.0.0.0 10.10.10.2 5 -> floating

### <ins>Static Host Routes in the IP Routing Table: </ins>
- Branch(config)# ip route 209.165.200.238 255.255.255.255 198.51.100.2

- Branch(config)# ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2
- OR
- Branch(config)# ipv6 route 2001:db8:acad:2::238/128 s0/1/0 fe80::2



--------
# CCNA 3

## Module 1 & 2 (OSPF):
- Show Commands:
	- Adjacency database & Dead Time: `show ip ospf neighbor `
	- Link-state Database: `show ip ospf database`
	- Forwarding Database & costs: `show ip route`
		- `show ip route ospf` -> filter for ospf routes only
	- OSPF info for an interface: `show ip ospf interface <interface>`
	- OSPF configuration information: `show ip protocols`, `show ip ospf` <- area & algorithm info 
	- For OSPF enabled interfaces: `show ip ospf interface brief`

- OSPF Configuration (Router ID):
	- Enable OSPF:  `R1(config)# router ospf 10`
	- Configure Router ID:
		```
		R1(config)# router ospf 10
		R1(config-router)# router-id 1.1.1.1
		R1(config-router)# end
		*May 23 19:33:42.689: %SYS-5-CONFIG_I: Configured from console by console
		R1# show ip protocols | include Router ID
		Router ID 1.1.1.1
		R1#
		```

	- Modify router ID after ==clearing OSPF process==:
	```
	R1# show ip protocols | include Router ID
	Router ID 10.10.1.1
	R1# conf t
	Enter configuration commands, one per line.  End with CNTL/Z.
	R1(config)# router ospf 10 
	R1(config-router)# router-id 1.1.1.1
	% OSPF: Reload or use "clear ip ospf process" command, for this to take effect
	R1(config-router)# end
	R1# clear ip ospf process
	Reset ALL OSPF processes? [no]: y
	*Jun  6 01:09:46.975: %OSPF-5-ADJCHG: Process 10, Nbr 3.3.3.3 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Interface down or detached
	*Jun  6 01:09:46.975: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
	*Jun  6 01:09:46.981: %OSPF-5-ADJCHG: Process 10, Nbr 3.3.3.3 on GigabitEthernet0/0/1 from LOADING to FULL, Loading Done
	*Jun  6 01:09:46.981: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from LOADING to FULL, Loading Done
	R1# show ip protocols | include Router ID
	Router ID 1.1.1.1
	R1#
	```


- OSPF Configuration (Point to point):
	- specify interfaces that belong to P-O-P network: `Router(config-router)# network _network-address_ _wildcard-mask_ **area** _area-id_`
	- Configure OSPF using network command:
		```
		R1(config)# router ospf 10
		R1(config-router)# network 10.10.1.0 0.0.0.255 area 0
		R1(config-router)# network 10.1.1.4 0.0.0.3 area 0
		R1(config-router)# network 10.1.1.12 0.0.0.3 area 0
		R1(config-router)#
		```
	- Configure OSPF using network command(specify interface):
		```
		R1(config)# router ospf 10
		R1(config-router)# network 10.10.1.1 0.0.0.0 area 0
		R1(config-router)# network 10.1.1.5 0.0.0.0 area 0
		R1(config-router)# network 10.1.1.14 0.0.0.0 area 0
		R1(config-router)#
		```
	- Configure OSPF using the ip ospf command: `Router(config-if)# **ip ospf** _process-id_ **area** _area-id_`
		```
		R1(config)# router ospf 10
		R1(config-router)# no network 10.10.1.1 0.0.0.0 area 0
		R1(config-router)# no network 10.1.1.5 0.0.0.0 area 0
		R1(config-router)# no network 10.1.1.14 0.0.0.0 area 0
		R1(config-router)# interface GigabitEthernet 0/0/0
		R1(config-if)# ip ospf 10 area 0
		R1(config-if)# interface GigabitEthernet 0/0/1 
		R1(config-if)# ip ospf 10 area 0
		R1(config-if)# interface Loopback 0
		R1(config-if)# ip ospf 10 area 0
		R1(config-if)#
		```
	- Configure passive interfaces:
		```
		R1(config)# router ospf 10
		R1(config-router)# passive-interface loopback 0
		R1(config-router)# end
		R1#
		*May 23 20:24:39.309: %SYS-5-CONFIG_I: Configured from console by console
		R1# show ip protocols
		*** IP Routing is NSF aware ***
		(output omitted)
		Routing Protocol is "ospf 10"
		  Outgoing update filter list for all interfaces is not set
		  Incoming update filter list for all interfaces is not set
		  Router ID 1.1.1.1
		  Number of areas in this router is 1. 1 normal 0 stub 0 nssa
		  Maximum path: 4
		  Routing for Networks:
		  Routing on Interfaces Configured Explicitly (Area 0):
		    Loopback0
		    GigabitEthernet0/0/1
		    GigabitEthernet0/0/0
		  Passive Interface(s):
		    Loopback0
		  Routing Information Sources:
		    Gateway         Distance      Last Update
		    3.3.3.3              110      01:01:48
		    2.2.2.2              110      01:01:38
		  Distance: (default is 110)
		R1#
		```
	- Interfaces that should not be passive can be re-enabled using the `no passive-interface` command
		```
		R3(config-router)#no passive-interface g0/0/0
		
		*Jun  5 23:07:07.746: %OSPF-5-ADJCHG: Process 10, Nbr 1.1.1.1 on     GigabitEthernet0/0/0 from LOADING to FULL, Loading Done
		```
	- all interfaces can be made passive using the `passive-interface default`:
	```
	R3(config-router)#passive-interface default
	
	*Jun  5 23:06:46.668: %OSPF-5-ADJCHG: Process 10, Nbr 1.1.1.1 on GigabitEthernet0/0/0 from FULL to DOWN, Neighbor Down: Interface down or detached
	*Jun  5 23:06:46.669: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/1 from FULL to DOWN, Neighbor Down: Interface down or detached
	```
	- Change from broadcast (multiaccess) to point-to-point:
		```
		R1(config)# interface GigabitEthernet 0/0/0
		R1(config-if)# ip ospf network point-to-point
		*Jun 6 00:44:05.208: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from
		FULL to DOWN, Neighbor Down: Interface down or detached
		*Jun 6 00:44:05.211: %OSPF-5-ADJCHG: Process 10, Nbr 2.2.2.2 on GigabitEthernet0/0/0 from
		LOADING to FULL, Loading Done
		R1(config-if)# end
		```


- OSPF configuration (multiaccess) :
	- To change interface priority
		```
		R1(config-if)# ip ospf priority 255 
		```

- Modify Single-Area OSPF:
	- Adjust the reference bandwidth: `Router(config-router)# auto-cost reference-bandwidth <Mbps>` (Default is 100Mbps)
	- Can change cost on interface only:
		```
		R1(config)# interface g0/0/1
		R1(config-if)# ip ospf cost 30
		R1(config-if)# interface lo0
		R1(config-if)# ip ospf cost 10
		R1(config-if)# end
		R1#
		```
	- Modify OSPF timers:
		```
		Router(config-if)# ip ospf hello-interval seconds
		Router(config-if)# ip ospf dead-interval seconds
		```

- Default route propagation:
	- Propagate the default route in ospf updates
		```
		R2(config)# router ospf 10
		R2(config-router)# default-information originate
		```


## Module 3&4 ACL:

- Extended close to source, standard close to destination


- Show commands:
	- View ACL with stats but without remarks`show access-lists`
	- Clear ACL stats: `clear access-list counters`
	- View ACL with remarks: `R1# show run | section access-list` OR `R1# show running-config | begin ip access-list`
	- Verify applied ACL on interface:`show ip interface <interface>`
 

### - Standard Access List
- Permit a host: `access-list 10 permit 192.168.1.1 0.0.0.0`
- Permit /24 subnet: `access-list 10 permit 192.168.1.1 0.0.0.255`
- Create a numbered ACL: 
```
Router(config)# access-list <access-list-number> {deny | permit | remark text} source [source-wildcard] [log]
```
- Delete numbered ACL: `no access-list <access-list-number>`
- Create named ACL: 
```
Router(config)# ip access-list standard <access-list-name>
```
- Bind a numbered or named standard IPv4 ACL to an interface:
```
Router(config-if) # ip access-group {access-list-number | access-list-name} {in | out}
```
- Remove an ACL from an interface: `no ip access-group`

- To edit ACE in-place:
```
R1# show access-lists 
Standard IP access list 1 
    10 deny 19.168.10.10 
    20 permit 192.168.10.0, wildcard bits 0.0.0.255
R1# conf t
R1(config)# ip access-list standard 1
R1(config-std-nacl)# no 10
R1(config-std-nacl)# 10 deny host 192.168.10.10
R1(config-std-nacl)# end
R1# show access-lists
Standard IP access list 1
    10 deny   192.168.10.10
    20 permit 192.168.10.0, wildcard bits 0.0.0.255
R1#
```

- Apply an ACL to the vty lines:
```
R1(config-line)# access-class {access-list-number | access-list-name} { in | out } 
```
- Example: 
```
R1(config)# username ADMIN secret class
R1(config)# ip access-list standard ADMIN-HOST
R1(config-std-nacl)# remark This ACL secures incoming vty lines
R1(config-std-nacl)# permit 192.168.10.10
R1(config-std-nacl)# deny any
R1(config-std-nacl)# exit
R1(config)# line vty 0 4
R1(config-line)# login local
R1(config-line)# transport input telnet
R1(config-line)# access-class ADMIN-HOST in
R1(config-line)# end
R1#
```

---
### - Extended Access List
- Bind a numbered or named standard IPv4 ACL to an interface:
```
Router(config-if) # ip access-group {access-list-number | access-list-name} {in | out}
```

- Numbered ACL:
	- Create Numbered ACL
	```
	Router(config)# access-list <access-list-number> {deny | permit | remark text} <protocol> <source> <source-wildcard> [operator {port}] <destination> <destination-wildcard> [operator {port}] [established] [log]
	```
	- To apply Extended ACL, Command is the same as in the standard
	- TCP established Extended ACL
	```
	R1(config)# access-list 120 permit tcp any 192.168.10.0 0.0.0.255 established
	R1(config)# interface g0/0/0 
	R1(config-if)# ip access-group 120 out 
	R1(config-if)# end
	R1# show access-lists 
	Extended IP access list 110
	     10 permit tcp 192.168.10.0 0.0.0.255 any eq www
	     20 permit tcp 192.168.10.0 0.0.0.255 any eq 443 (657 matches)
	Extended IP access list 120
	     10 permit tcp any 192.168.10.0 0.0.0.255 established (1166 matches)
	R1#
	```
	- Remove Extended numbered ACL: `no access-list <access-list-number>` 

- Named ACL:
	- Create Named E-ACL: (this command enters the named extended configuration mode)
	```
	Router(config)# ip access-list extended access-list-name 
	```
	- Example:
	```
	R1(config)# ip access-list extended SURFING
	R1(config-ext-nacl)# Remark Permits inside HTTP and HTTPS traffic 
	R1(config-ext-nacl)# permit tcp 19.168.10.0 0.0.0.255 any eq 80
	R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 443
	R1(config-ext-nacl)# exit
	R1(config)# 
	R1(config)# ip access-list extended BROWSING
	R1(config-ext-nacl)# Remark Only permit returning HTTP and HTTPS traffic 
	R1(config-ext-nacl)# permit tcp any 192.168.10.0 0.0.0.255 established
	R1(config-ext-nacl)# exit
	R1(config)# interface g0/0/0
	R1(config-if)# ip access-group SURFING in
	R1(config-if)# ip access-group BROWSING out
	R1(config-if)# end
	R1# show access-lists
	Extended IP access list SURFING
	    10 permit tcp 192.168.10.0 0.0.0.255 any eq www
	    20 permit tcp 192.168.10.0 0.0.0.255 any eq 443 (124 matches) 
	Extended IP access list BROWSING
	    10 permit tcp any 192.168.10.0 0.0.0.255 established (369 matches) 
	R1#
	```

	- Edit ACL: (use `show access-lists`)
	```
	R1# configure terminal
	R1(config)# ip access-list extended SURFING 
	R1(config-ext-nacl)# no 10
	R1(config-ext-nacl)# 10 permit tcp 192.168.10.0 0.0.0.255 any eq www
	R1(config-ext-nacl)# end
	```
	 - Permit all access:
	 `RT1(config-ext-nacl)# permit ip any any`


## Module 6 NAT:

- Show commands: 
	- `R2# show ip nat translations` -> shows active NAT translations.
	- `R2# show ip nat statistics` -> displays information about the total number of active translations
	- `R2# clear ip nat statistics` 


- Configure Static NAT:
```
R2(config)# ip nat inside source static 192.168.10.254 209.165.201.5

R2(config)# interface serial 0/1/0
R2(config-if)# ip address 192.168.1.2 255.255.255.252
R2(config-if)# ip nat inside
R2(config-if)# exit
R2(config)# interface serial 0/1/1
R2(config-if)# ip address 209.165.200.1 255.255.255.252
R2(config-if)# ip nat outside
```

- Configure Dynamic NAT:

```
R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255

R2(config)# ip nat pool NAT-POOL1 209.165.200.226 209.165.200.240 netmask 255.255.255.224

R2(config)# ip nat inside source list 1 pool NAT-POOL1

R2(config)# interface serial 0/1/0
R2(config-if)# ip nat inside

R2(config-if)# interface serial 0/1/1
R2(config-if)# ip nat outside
```

- `clear ip nat translation` -> Clears all dynamic address translation entries from the NAT translation table.
- Configure PAT:
	- For single address: (add overload) ==else like static==  (PAT WITH INTERFACE)
		```
		R2(config)# ip nat inside source list 1 interface serial 0/1/1 overload
		R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
		R2(config)# interface serial0/1/0
		R2(config-if)# ip nat inside
		R2(config-if)# exit
		R2(config)# interface Serial0/1/1
		R2(config-if)# ip nat outside
		```
	- For address pool: (dynamic NAT with PAT)
		```
		R2(config)# ip nat pool NAT-POOL2 209.165.200.226 209.165.200.240 netmask 255.255.255.224
		R2(config)# access-list 1 permit 192.168.0.0 0.0.255.255
		R2(config)# ip nat inside source list 1 pool NAT-POOL2 overload
		R2(config)# 
		R2(config)# interface serial0/1/0
		R2(config-if)# ip nat inside
		R2(config-if)# exit
		R2(config)# interface serial0/1/1
		R2(config-if)# ip nat outside
		R2(config-if)# end
		R2#
		```

## Module 10 Network Management:

### CDP
- Show commands:
	- `Router# show cdp` -> verify the status of CDP and display information
	- `Router# show cdp neighbors [detail]` -> displays important information about the CDP neighbors
	- `Router# show cdp interface` -> display the interfaces that are CDP-enabled


- Enable CDP: `Router(config)# cdp run`
- Enable CDP on specific interface: 
	```
	Switch(config)# interface gigabitethernet 0/0/1
	Switch(config-if)# cdp enable
	```

### LLDP

- Show commands:
	- `S1# show lldp neighbors [detail]`


- Enable LLDP: `Switch(config)# lldp run`
- Enable LLDP on specific interfaces (must enable transmit and receive): 
```
Switch(config)# interface gigabitethernet 0/1
Switch(config-if)# lldp transmit
Switch(config-if)# lldp receive
```

### NTP:
- Show commands:
	- `R1# show clock detail `
	- `R1# show ntp associations`
	- `R1# show ntp status`


- Set time Manually: `R1# clock set 16:01:00 sept 25 2020 `
- configure NTP: 
	```
	R1(config)# ntp server 209.165.200.225 
	R1(config)# end 
	R1# show clock detail 
	21:01:34.563 UTC Fri Nov 15 2019
	Time source is NTP
	```




- Backup to tftp:
```
R1>en
R1#**copy running-config tftp**
Address or name of remote host []? 192.168.1.5
Destination filename [R1-confg]? R1-Run-Config
```

- Use TFTP server to update/upgrade IOS software
```
S3#copy tftp flash:
Address or name of remote host []? 209.165.202.131
Source filename []? c2960-lanbasek9-mz.150-2.SE4.bin
Destination filename [c2960-lanbasek9-mz.150-2.SE4.bin]? 

Accessing tftp://209.165.202.131/c2960-lanbasek9-mz.150-2.SE4.bin....
Loading c2960-lanbasek9-mz.150-2.SE4.bin from 209.165.202.131: !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
[OK - 4670455 bytes]

S3#configure terminal*
S3(config)#boot system flash:c2960-lanbasek9-mz.150-2.SE4.bin
S3(config)#exit
S3#
%SYS-5-CONFIG_I: Configured from console by console

S3#**copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
S3#**reload**
Proceed with reload? [confirm]
```
