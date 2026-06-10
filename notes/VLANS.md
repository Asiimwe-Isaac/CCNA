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
