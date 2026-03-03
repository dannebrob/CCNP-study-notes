# Summarization

OSPF is a link-state protocol, which means that all routers in an area must learn all the routes in that area and store them in the LSDB. The LSDB must be the same on all routers in the same area. 

The problem summarization solves: When a network grows, the number of LSAs increases and this creates several scaling challenges. 

Larger LSBDs consume more memory on every router in the network

More LSAs means more flooding, especially during topology changes. 

SPF calculations become more expensive, because the calculation is done on the entire LSDB.

The way summarization solves this is by reducing the information that must be shared between areas. 

- OSPF cannot summarize intra‑area routes inside an area. Summarization is only performed at a ABR (Type 3 LSA) or an ASBR (Type 5 LSA), where routes from an area (intra‑area routes) can be summarized as they are advertised to other areas.
  - Inter-area routes are routes coming from an ABR.
  - External routes are routes coming from an ASBR; redistributing routes from other routing protocols or static routes. 

To summarize IPs under one IP range with a smaller subnet mask its required that the LSAs that are sent are of the type 3 or 5. 

More information about LSA types see: [Network-lessons](https://networklessons.com/ospf/ospf-lsa-types-explained)

For an ASBR to be able to redistribute external routes in OSPF, the ASBR can inject the external routes into OSPF and advertise them to other routers you must configure it like this:

```
R1(config)# router ospf 1
R1(config-router)# redistribute connected subnets
```

This will make R1 to take all its connected interfaces , that are not in OSPF and make them external routes, this can be a bit to uncontrolled since it includes all the routes, like loopback addresses for example.  

Its very important to include the subnets keyword in the command, without it only classful networks are redistributed and not subnets. 

A more narrow way to configure redistribution is to use:

```
R1(config-router)# redistribute static subnets
```

This will only include the static routes and inject them in the OSPF advertisement.

The routes that are injected will still be stored as Type 5 in the LSDB. Its important to remember that area routers keep identical LSDBs. 

### Summarize external routes

- To summarize a range of external IPs, in this example 10.10.4.0/24 and 10.10.5.0/24 into a larger range, use these commands:
```
R1(config)# router ospf 1
R1(config-router)# summary-address 10.10.4.0 255.255.252.0
```
  - This will make the ASBR advertise only one route, the 10.10.4.0/22, since the /22 subnet contains all the IPs in that range.

### Summarize inter-area routes

To summarize a range of inter-area routes, routes from other areas, on an ABR:

```
R2(config)# router ospf 1
R2(config-router)# area 1 range 10.10.8.0 255.255.252.0
```

This will do the same as when we summarized an external range. In this example above a plausible IP would be 10.10.9.1/24. 

When using **area 1 range **specifies where the summarization will take place, resulting in R2 only advertise one type 3 LSA. 

Three of the big pros of summarization is to keep the size of the LSDBs small,  limiting the amount of LSAs being sent (LSA flooding) and making sure the routing table is as small as possible. 

A fourth pro is if one of the hosts or routes in the summarized route would go down, the summary hides the more specific failure, so other areas don't receive new LSAs, ie no LSAs are sent due to the failure and no SPF recalculations are needed an reduces the CPU load.This might be most visible in bigger enterprise networks with thousands of routes. 

Ending words

- A subnet that is summarized will only be advertised if there is at least one active route in that summary. If all hosts/routes go down, the route will be removed. 

- The cost of the summarized route will be the subnet with the lowest cost, even if there are routes that has an individually higher cost in that summary.  

- The ABR installs a Null0 route for the summary prefix to prevent loops at Layer 3. This is done to ensure that traffic matching the summary, but not any specific subnet, is discarded. Looping would otherwise occur since the summary can include more addresses that actually exists. Without this protection traffic destined for a host that do not exist, would still be routed back and forth between routes, and thus creating a loop.



LAB

E1 (ABR + ASBR)

redistribute DMZ hosts 10.99.0.0

summarize area 10,20,30

R1 

```
R1# show ip interface brief 
Interface     IP-Address       OK?
Eth0/0        10.20.0.2        YES
Eth0/1        10.20.1.1        YES
Eth0/2        10.20.2.1        YES
Eth0/3        10.20.3.1        YES
Loopback0     10.10.10.10      YES
```

