
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
