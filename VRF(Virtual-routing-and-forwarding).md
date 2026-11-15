# VRF #
### What is it? ###
Cisco Virtual Routing and Forwarding, known as Cisco VRF, gives a virtualization functionality that creates multiple routing tables using a single layer 3 device. 
One can say that you can do more with less since with VRF engineers dont need seprate routers to have multiple routing tables.

This also can improve security and optimize performance, while keeping it simple to configure and scale well.
Its a L3 function that can be compared to VLANs in L2. VRFs segment the layer 3 logical network. 

The seperation of both vlans and vrfs can be a protection aganins malicius trafic on the network.
In a small bussnies enviroment with 2 or more locations VRF can be used to restrict each location to comunicate with etchother,
but still allow comunctation to a centralized server at HQ or in the cloud. 
The VRF acts like a kind of VPN betwwen the locations and the HQ/cloud. 

### Benefits ###
Enhanced security, segmentation and preformance. 
The network that is using VRF is completly anaware of routing tables and subnets outside its own routing table.
This segmentation is also key for ISPs that dont want diffrent customers to access resources thay should not access.

VRF can also be configurd to allow different subnets to have more optimal paths, witch could be used by ISPs to seprate susers based on pricing or subscrition tier.
This abillity to manipulate network speeds and reuse IP addresses greatly increese the number of devices QoS a network can support. 


### Configuration ###
The exact method and commands to config differ between versions of operation system. 

To create a new VRF with the CLI:
```
vrf definition [VRF_name]
address-family ipv4 | ipv6
exit
exit
interface etho0/1
vrf forwarding [VRF_name]
ip address 123.123.123.123 255.255.255.0
```


#### Sources ####
[CBT nuggets](https://www.cbtnuggets.com/blog/technology/networking/what-is-cisco-vrf-virtual-routing-forwarding)

