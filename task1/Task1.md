
# Task 1: Verify Network with LDP Configuration
Step 1:
The Lab is pre-configured with MPLS (LDP) in the core (xrvr-1 to xrvr-6) and L3-VPN “RED” xrvr-1 (PE) interface facing xrvr-7 (CE), as well as xrvr-4 (PE) interface facing xrvr-8 (CE).

Step2:
Press Enter to access the connection and log into the console with the credentials: 

username: cisco 
password: C1sco12345

Step 3:
Issue the command 'show route ipv4' command to view the route table and verify the presence of the loopback0 addresses in the table.


```
RP/0/0/CPU0:xrvr-1#show route ipv4
Fri Nov 27 13:38:55.323 UTC

Codes: C - connected, S - static, R - RIP, B - BGP, (>) - Diversion path
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - ISIS, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, su - IS-IS summary null, * - candidate default
       U - per-user static route, o - ODR, L - local, G  - DAGR, l - LISP
       A - access/subscriber, a - Application route
       M - mobile route, r - RPL, (!) - FRR Backup path

Gateway of last resort is not set

C    99.1.2.0/24 is directly connected, 01:37:09, GigabitEthernet0/0/0/0
L    99.1.2.1/32 is directly connected, 01:37:09, GigabitEthernet0/0/0/0
C    99.1.6.0/24 is directly connected, 01:37:09, GigabitEthernet0/0/0/1
L    99.1.6.1/32 is directly connected, 01:37:09, GigabitEthernet0/0/0/1
i L2 99.2.3.0/24 [115/20] via 99.1.2.2, 01:37:09, GigabitEthernet0/0/0/0
i L2 99.2.6.0/24 [115/20] via 99.1.2.2, 01:36:58, GigabitEthernet0/0/0/0
                 [115/20] via 99.1.6.6, 01:36:58, GigabitEthernet0/0/0/1
i L2 99.3.4.0/24 [115/30] via 99.1.2.2, 01:36:29, GigabitEthernet0/0/0/0
i L2 99.3.5.0/24 [115/30] via 99.1.2.2, 01:36:29, GigabitEthernet0/0/0/0
                 [115/30] via 99.1.6.6, 01:36:29, GigabitEthernet0/0/0/1
i L2 99.4.5.0/24 [115/30] via 99.1.6.6, 01:36:58, GigabitEthernet0/0/0/1
i L2 99.5.6.0/24 [115/20] via 99.1.6.6, 01:36:58, GigabitEthernet0/0/0/1
S    100.0.0.0/24 is directly connected, 01:37:10, Null0
L    100.0.0.1/32 is directly connected, 01:37:09, Loopback0
i L2 100.0.0.2/32 [115/10] via 99.1.2.2, 01:37:09, GigabitEthernet0/0/0/0
i L2 100.0.0.3/32 [115/20] via 99.1.2.2, 01:36:29, GigabitEthernet0/0/0/0
i L2 100.0.0.4/32 [115/30] via 99.1.2.2, 01:36:20, GigabitEthernet0/0/0/0
                  [115/30] via 99.1.6.6, 01:36:20, GigabitEthernet0/0/0/1
i L2 100.0.0.5/32 [115/20] via 99.1.6.6, 01:36:58, GigabitEthernet0/0/0/1
i L2 100.0.0.6/32 [115/10] via 99.1.6.6, 01:36:58, GigabitEthernet0/0/0/1
```
> [OPTIONAL]
> Repeat this command on all nodes to view the routing tables.
>

Step 4:
On xrvr-1 and xrvr-4, issue "show running-config router bgp" command to verify the BGP configuration as below.

```
RP/0/0/CPU0:xrvr-1#show running-config router bgp
Fri Nov 27 13:42:41.327 UTC
router bgp 64001
 bgp router-id 100.0.0.1
 address-family ipv4 unicast
 !
 address-family vpnv4 unicast
 !
 neighbor 100.0.0.4
  remote-as 64001
  description to xrvr-4
  update-source Loopback0
  address-family vpnv4 unicast
  !
 !
 neighbor 100.0.0.9
  remote-as 64001
  description to xrvr-9
  update-source Loopback0
  address-family ipv4 unicast
  !
  address-family vpnv4 unicast
  !
 !
 vrf RED
  rd 1:1
  address-family ipv4 unicast
  !
  neighbor 99.1.7.7
   remote-as 3
   address-family ipv4 unicast
    route-policy ALL in
    route-policy ALL out
   !
  !
 !
!
```
```
RP/0/0/CPU0:xrvr-4#show running-config router bgp
Fri Nov 27 13:43:30.505 UTC
router bgp 64001
 bgp router-id 100.0.0.4
 address-family ipv4 unicast
 !
 address-family vpnv4 unicast
 !
 neighbor 100.0.0.1
  remote-as 64001
  description to xrvr-1
  update-source Loopback0
  address-family vpnv4 unicast
  !
 !
 neighbor 100.0.0.10
  remote-as 64002
  description to xrvr-10
  update-source Loopback0
  address-family ipv4 unicast
  !
  address-family vpnv4 unicast
  !
 !
 vrf RED
  rd 1:1
  address-family ipv4 unicast
  !
  neighbor 99.4.8.8
   remote-as 2
   address-family ipv4 unicast
    route-policy ALL in
    route-policy ALL out
   !
  !
 !
!
```
> [INFORMATION]
> An iBGP session is configured between xrvr-1 and xrvr-4
>

 Step 5:
eBGP sessions are configured between xrvr-7 (CE) and xrvr-1 (PE), as well as between xrvr-4 (PE) and xrvr-8 (CE). To verify these connections, issue the following commands on xrvr-7 and xrvr-8:
```
show running-config | section router bgp
show running-config | section ip route 0.0.0.0
```

# Verify LDP Configuration
Step 1:
Verify the LDP is configured in the core using the “show running-config mpls ldp" and "show mpls ldp neighbor brief"
```
RP/0/0/CPU0:xrvr-1#show running-config mpls ldp
Fri Nov 27 13:51:00.503 UTC
mpls ldp
 interface GigabitEthernet0/0/0/0
 !
 interface GigabitEthernet0/0/0/1
 !
!
```
```
RP/0/0/CPU0:xrvr-1#a7
RP/0/0/CPU0:xrvr-1#show mpls ldp neighbor brief
Fri Nov 27 13:51:39.510 UTC

Peer               GR  NSR  Up Time     Discovery   Addresses     Labels
                                        ipv4  ipv6  ipv4  ipv6  ipv4   ipv6
-----------------  --  ---  ----------  ----------  ----------  ------------
100.0.0.2:0        N   N    00:00:41    1     0     4     0     15     0
100.0.0.6:0        N   N    00:00:41    1     0     4     0     15     0
```
Repeat this step on the core routers xrvr-2 through xrvr-6 to verify the ldp configuration.

Step 2:
Check the end-to-end reachability between the 2 CE routers xrvr-7 and xrvr-8.
```
RP/0/0/CPU0:xrxr-7#ping 100.0.0.8
Fri Nov 27 14:46:43.321 UTC
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 100.0.0.8, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 9/21/39 ms
```
```
RP/0/0/CPU0:xrxr-7#traceroute 100.0.0.8
Fri Nov 27 14:47:19.548 UTC

Type escape sequence to abort.
Tracing the route to 100.0.0.8

 1  99.1.7.1 9 msec  9 msec  0 msec
 2  99.1.6.6 [MPLS: Labels 24007/24014 Exp 0] 49 msec  19 msec  19 msec
 3  99.5.6.5 [MPLS: Labels 24004/24014 Exp 0] 29 msec  9 msec  19 msec
 4  99.4.5.4 [MPLS: Label 24014 Exp 0] 29 msec  9 msec  9 msec
 5  99.4.8.8 29 msec  *  9 msec
```
* Verify the reachability to xrvr-7 from xrvr-8.
* From the traceroute command check the hops 2,3 and 4. The trace has mpls labels. Keep note of the labels numbers.
* 24014 in the picture above is the vpn label (in your lab it might be different label value) and the 24007 and 24004 are mpls ldp labels.
* In your setup the path in the trace may be xrvr-7 –- xrvr-1 –- xrvr-2 — xrvr-3 –- xrvr-4 –- xrvr-8

Step 3:
Check the cef entry of route 100.0.0.8/32 on xrvr-1 with "show cef vrf RED 100.0.0.8/32"
```
RP/0/0/CPU0:xrvr-1#a10
RP/0/0/CPU0:xrvr-1#show cef vrf RED 100.0.0.8/32
Fri Nov 27 14:53:03.928 UTC
100.0.0.8/32, version 6, internal 0x5000001 0x0 (ptr 0xa13fafcc) [1], 0x0 (0x0), 0x208 (0xa1527190)
 Updated Nov 27 14:31:14.647
 Prefix Len 32, traffic index 0, precedence n/a, priority 3
   via 100.0.0.4/32, 3 dependencies, recursive [flags 0x6000]
    path-idx 0 NHID 0x0 [0xa158ffdc 0x0]
    recursion-via-/32
    next hop VRF - 'default', table - 0xe0000000
    next hop 100.0.0.4/32 via 24010/0/21
     next hop 99.1.2.2/32 Gi0/0/0/0    labels imposed {24007 24014}
     next hop 99.1.6.6/32 Gi0/0/0/1    labels imposed {24007 24014}
```
Step 4:
Verify Segment Routing is not enabled/configured in the Core using “show running-config segment-routing"
```
RP/0/0/CPU0:xrvr-1#a11
RP/0/0/CPU0:xrvr-1#show running-config segment-routing
Fri Nov 27 14:54:04.624 UTC
% No such configuration item(s)
```
(Optional) Repeat the above step on the core routers xrvr-2 to xrvr-6 to verify Segment Routing is not enabled/configured.
