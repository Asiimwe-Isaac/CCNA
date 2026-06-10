A  VLAN is a virtually local area network that logically segments a sing;e network into multiple separate broadcast domans. Devices cannot communicate directly without a layer 3 or layer two device ie switch and router.

WHY DO WE USE VLANS 
To reduce broadcast traffic- each VLAN has its own broadcast domain.
To improve security, let me separate sensitive departments (eg Finance from HR department, okay substations)
Reduce costs , there is no need for multiple switches 

ACCESS PORTS VS TRUNK PORTS
Access ports belong to single VLANS but trunk ports are used to carry traffic from multiple VLANS between switches, here the frame is tagged inorder to to know which VLAN it is coming from

BASIC ACCESS PORT CONFIGURATION
SW1(config)# interface f0/1
SW1(config)#switchport mode access
SW1(config)#switch access vlan 10

THE TWO RUNKING PROTOCOLS
ISL- this is rarely used today.
802.1Q- this is mostly used today where a 4 byte between the source and type. here is where the tag that helps us determine where the VLAN is from int the ethernet frame

SRUCTURE OF THE 802.1 ENCAPSULATION

TPID(16 bits)- Tag protocol identifier is used to show that the frame is dot 1 tagged
PCD (3 bits)- Priority Code Point is used prioritisation 
DEI  (1 bit) - Drop Eligible Indicator is used to mark which frames may be droppped during congestion
VID (12 bits)- VLAN identifier used to identify which VLAN the frame belongs to like from the range (0 to 4095)

VID Details
There are 4095 possible values (0 to 4095)
VLANS 0 and 4095 are reserved - cannot be used

NATIVE VLAN
 the native VLAN is not tagged and when a vlan receives a frame from a vlan that is not tagged they ass ume that it is the native vlan. The native vlan is default vlan 1

 The the native VLAN must match on both ends on the trunk_ mismatch causes traffic issues

 CONFIGURING TRUNK ENCAPSULATION
 On switches supporting both ISL and dot1q. you must set encapsulation first, otherwise switchport mode trunk will be rejected

 SW1(config)#interface g0/1
SW1(config)#switchport trunk encapsulation dot1q
SW1(config)#switch mode trunk

On newer switches that only support dot1q, skip the encapsulation line:
SW1(config)#interface g0/01
SW1(config)#switch mode trunk

TRUNK CONFIGURATIONS 
SETTING THE NATIVE VLAN
SW1(config-if)# switchport trunk native vlan 1001

MODIFYING ALLOWED VLANs ON A TRUNK
BY default all VLANS ARE ALLOWED (1-4094) on a trunk. so you can adjust them by restricting, add, or remove VLANS with the following commands

switchport trunk allowed vlan 10,20,30  :This allows vlans 10,20,30 to be put on the trunk

switchport trunk allowed vlan add v40 : This is used to add a vlan on the allowed vlans 

switchport trunk allowed vlan remove 20 : This is used to remove vlan 20 from the vlan 20 from the vlans 

switchport trunk allowed vlan all : This allows all vlan on the  tuunk port

switchport trunk allowed vlan except 30 : This allows all VLANS except the specified ones

VERIFICATION COMMANDS 

show vlan brief : List all VLANS and their ACCESS port assignments(does not show trunk ports)

show interfaces trunk : List trunk interfaces , encapsulation, native VLAN and VLANS allowed/active

show interfaces g0/1 switchport : detailed switchport into a specific interface

Full Example: SWITCH TRUNK CONFIGURATION
SW1(config)#vlan 10
SW1(config-vlan)#name SALES
SW1(config-vlan)#vlan 20
SW1(config-vlan)#name IT
SW1(config-vlan)#vlan 30
SW1(config-vlan)#name HR
SW1(config-vlan)#exit

SW1(config)#interface g0/1
SW1(config-if)#switchport trunk encapsulation dot1q
SW1(config-if)#switport mode trunk
SW1(config-if)#switchport trunk native vlan 1001
SW1(config-if)#switchport trunk allowed vlan 10,20,30


ROUTER ON A STICK (ROAS)
This is a method of performing inter-VLAN routing using a single physical interface on a router. The router's interface is divided into multiple logical sub interfaces, each associatd with a different VLAN. The physical link to the switch must configured as a trunk

WHY USE ROAS
Allows inter-VLAN routing with a single router interface(cost effective)
The single physical link carries taggged traffic for all VLANS (trunk)
Each VLAN gets its own sb-interface with its own IP address(default gateway for that VLAN)

ROAS Topology
Let me give an example of a setup
PC in VLAN10 pings PC in VLAN20. Traffic flows : PC TO SWITCH(ACCESS PORT) TO TRUNK THEN TO THE ROUTER SUB-INTERFACE Then the router routes it backdown trunkdown to swtich then to the destination PC

NOTE : ONLY ONE PHYSICAL INTERFACE IS USED FOR ALL INTER VLAN TRAFFIC.

ROAS Configuration - Step by Step
Step1: Configure The Switch Side(Trunk)
SW1(config)#interface g0/0
SW1(config-if)# switchport trunk encapsulation dot1q
SW1(config-if)#switchport mode trunk

STEP2: ENABLE THE PHYSICAL ROUTER
R1(config)#interface g0/0
R1(config-if)#no shutdown

STEP 3:CREATE SUB-INTERFACES(ONE PER VLAN)
Sub-interface numbering convention:use the VLAN ID as the sub-interface number(e.g. g0/0.10 for VLAN 10). This is not required but is standard practice
R1(config)#interface g0/0.10
R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip address 192.168.10.1 255.255.255.0

R1(config)#interface g0/0.20
R1(config-subif)#encapsulation dot1q 20
R1(config-subif)#ip address 192.168.20.1 255.255.255.0

R1(config)#interface g0/0.30
R1(config-subif)#encapsulation dot1q 30
R1(config-subif)ip address 192.168.30.1 255.255.255.0
