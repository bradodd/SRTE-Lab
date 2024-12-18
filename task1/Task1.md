
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
On xrvr-1 and xrvr-4, issue alias “a2” (show running-config router bgp) command to verify the BGP configuration as below.

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



