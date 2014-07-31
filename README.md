Junos Operational Script ospf-tool.slax
=================================

### DESCRIPTION  
This is a Juniper SLAX op script to provide a quick breakdown of OSPF and OSPFv3 neighbors. Three CLI arguments can be passed for additional functionality. The first two include being able to specify a neighbor ID or interface to retrieve detailed troubleshooting information about that interface (or interface where the neighbor ID is found). The third CLI argument is to pass a router ID, and retrieve all the OSPF routes received from that router ID, and indicate which ones are active in the routing table.  

These functions are accomplished by combining and shortening the output of the `show ospf neighbor`, `show ospf interface extensive`, `show ospf route extensive`, `show ospf overview`, and `show route` commands (along with the appropriate OSPFv3 commands as well) into one easy to read format. 

For detailed information for storing and enabling an op script on your Junos device, please visit [this page](http://www.juniper.net/techpubs/en_US/junos13.2/topics/task/configuration/junos-script-automation-script-storing-enabling.html).

[General SLAX Overview](http://www.juniper.net/techpubs/en_US/junos14.1/topics/concept/junos-script-automation-slax-overview.html)  
[Junos Automation Documentation](http://www.juniper.net/techpubs/en_US/junos13.2/information-products/pathway-pages/config-guide-automation/configuration-and-operations-automation.html#overview)  

### Requirements
Junos Version >= 12.2
This script does use `mvar`s and other SLAX features first introduced in 12.2.

### VERSION HISTORY:  
* v1.0  
	- Initial Release  

### AUTHOR:  
Nathan Printz  

## EXAMPLES:  

Explanation of CLI arguments available through question mark:  
	
	> op ospf-tool ?
	Possible completions:
	  <[Enter]>            Execute this command
	  <name>               Argument name
	  detail               Display detailed output
	  id                   Neighbor ID to retrieve detailed troubleshooting info.
	  int                  Interface on which to retrieve detailed troubleshooting info. Include the Unit number. (ex. ge-0/0/12.0)
	  route                Specify a Router ID to be shown all routes advertised by that router.
	  |                    Pipe through a command

No Passed argument gives a summary of the OSPF topologies:  

	> op ospf-tool                  

	===================
	|       OSPF      |
	===================
	Local Router ID: 192.168.246.178
	 
	--------   Area 0.0.0.0 - Not Stub  --------
	ABRs: 0   ASBRs: 0   Neighbors Up: 2
	 
	 Interface      State        ID               rDNS hostname         Neighbor Address   
	ge-0/0/13.0    Full         10.42.0.107                            10.100.3.1          
	ge-0/0/9.0     Full         192.168.246.146                        10.100.2.1          
	lo0.0          Passive      N/A                                    N/A                 
	 
	--------   Area 0.0.0.1 - Not Stub  --------
	ABRs: 0   ASBRs: 0   Neighbors Up: 1
	 
	 Interface      State        ID               rDNS hostname         Neighbor Address   
	ge-0/0/12.0    Full         10.0.0.246       asdf.qwer.com 		   10.100.4.2          


	===================
	|      OSPFv3     |
	===================
	Local Router ID: 192.168.246.178
	 
	--------   Area 0.0.0.0 - Not Stub  --------
	ABRs: 1   ASBRs: 1   Neighbors Up: 2
	 
	 Interface      State        ID               rDNS hostname         Neighbor Address   
	ge-0/0/13.0    Full         10.42.0.107                            fe80::226:88ff:fe63:3910
	ge-0/0/15.0    No Neighbor  N/A              N/A                   
	ge-0/0/9.0     Full         192.168.246.146                        fe80::219:e2ff:fe51:83cc
	lo0.0          Passive      N/A                                    N/A                 
	 
	--------   Area 0.0.0.1 - Stub  --------
	ABRs: 0   ASBRs: 0   Neighbors Up: 1
	 
	 Interface      State        ID               rDNS hostname         Neighbor Address   
	ge-0/0/12.0    Full         10.0.0.246       asdf.qwer.com 		   fe80::3e8a:b0ff:fe9b:e60f

Retrieving routes from a single neighbor ID:  

	> op ospf-tool route 10.0.0.246              

	===================
	|       OSPF      |
	===================
	Prefix              Route      Path  NH    Metric Area      Next Hop      Next Hop      
	                    Type       Type  Type                   Interface     Address/LSP   
	10.0.0.246          Router     Intra IP    50     0.0.0.1   ge-0/0/12.0   10.100.4.2    
	* 10.100.5.0/24     Network    Intra IP    100    0.0.0.1   ge-0/0/12.0   10.100.4.2    

	 * denotes an active route in the routing table.


	===================
	|      OSPFv3     |
	===================
	Prefix                                  Route      Path  NH    Metric Area      Next Hop      Next Hop      
	                                        Type       Type  Type                   Interface     Address/LSP   
	10.0.0.246                              Router     Intra IP    50     0.0.0.1   ge-0/0/12.0   fe80::3e8a:b0ff:fe9b:e60f
	10.0.0.246;0.0.0.1                      Transit    Intra IP    50     0.0.0.1   ge-0/0/12.0                 
	10.0.0.246;0.0.0.2                      Transit    Intra IP    51     0.0.0.1   ge-0/0/12.0   fe80::3e8a:b0ff:fe9b:e60f
	2000::4000:0/127                        Network    Intra IP    50     0.0.0.1   ge-0/0/12.0                 
	* 2000::5000:0/127                      Network    Intra IP    51     0.0.0.1   ge-0/0/12.0   fe80::3e8a:b0ff:fe9b:e60f

	 * denotes an active route in the routing table.

Retrieving troubleshooting information for a neighbor ID:  

	> op ospf-tool id 10.0.0.246       

	===================
	|       OSPF      |
	===================
	The neighbor ID you specified was found in OSPFv3 on interface: ge-0/0/12.0
	 
	These items must match the other side for any neighborship:
	-----------------------------------------------------------
	Area ID: 0.0.0.1    Area Type: Not Stub
	Interface Type: LAN    Hello Timer: 10    Dead Timer: 40
	MTU: 1500    Subnet/mask: 10.100.4.1 / 255.255.255.0
	Authentication: Password
	 
	Your local router ID is 192.168.246.178, which must be different than the other side!


	===================
	|      OSPFv3     |
	===================
	The neighbor ID you specified was found in OSPFv3 on interface: ge-0/0/12.0
	 
	These items must match the other side for any neighborship:
	-----------------------------------------------------------
	Area ID: 0.0.0.1    Area Type: Stub
	Interface Type: LAN    Hello Timer: 10    Dead Timer: 40
	MTU: 1500    Subnet/mask: fe80::219:e2ff:fe51:85cf / 64
	 
	Your local router ID is 192.168.246.178, which must be different than the other side!

Retrieving troubleshooting information for an interface:  

	> op ospf-tool int ge-0/0/12    

	===================
	|       OSPF      |
	===================
	There is 1 OSPFv2 neighbor(s) found on interface ge-0/0/12.0
	 
	These items must match the other side for any neighborship:
	-----------------------------------------------------------
	Area ID: 0.0.0.1    Area Type: Not Stub
	Interface Type: LAN    Hello Timer: 10    Dead Timer: 40
	MTU: 1500    Subnet/mask: 10.100.4.1 / 255.255.255.0
	Authentication: Password
	 
	Your local router ID is 192.168.246.178, which must be different than the other side!


	===================
	|      OSPFv3     |
	===================
	There is 1 OSPFv3 neighbor(s) found on interface ge-0/0/12.0
	 
	These items must match the other side for any neighborship:
	-----------------------------------------------------------
	Area ID: 0.0.0.1    Area Type: Stub
	Interface Type: LAN    Hello Timer: 10    Dead Timer: 40
	MTU: 1500    Subnet/mask: fe80::219:e2ff:fe51:85cf / 64
	 
	Your local router ID is 192.168.246.178, which must be different than the other side!