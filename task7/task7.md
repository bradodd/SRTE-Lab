# Task 7: Enable Network Controller (SR-PCE)

The Transport Network Controller also known as SR-PCE functionality should be enabled on xrvr-9. SR-PCE functionality is fundamentally distributed, and you can install multiple SR-PCE nodes in a network. In this example, we have one SR-PCE node. Path Computation Client (PCC) which are xrvr-1 and xrvr-4 PE routers use the SR-PCE node xrvr-9 for Path Computation purposes. Enabling the SR-PCE server functionality on a node is simple. The only required configuration is the local address used for the PCEP connections.

Step 1: On xrvr-9 enter the following command to configure PCE functionality.
```
pce
 address ipv4 100.0.0.9
commit
```

SR-PCE gets its topology information from the BGP-LS (link-state) address family. Typically, BGP Route Reflectors distribute BGP-LS information throughout the network. In this example, the xrvr-2 distributes BGP-LS information to SR-PCE (xrvr-9). Any node can provide the BGP-LS topology feed to the SR-PCE.

Step 2:  Enable the link state address family on the router xrvr-2 and xrvr-9 in AS 64001 in order to share the BGP-LS with the SR-PCE.

> !! On xrvr-2 and xrvr-9 !!

On xrvr-9:
Enter the following 
```
configure
router bgp 64001
address-family link-state link-state
neighbor 100.0.0.2
address-family link-state link-state
commit
end
```
On xrvr-2:
```
router bgp 64001
address-family link-state link-state
neighbor 100.0.0.9
address-family link-state link-state
commit
end
```

Step 3:  Enable the Link Gig0/0/0/4 on xrvr-2 as following:
```
conf t
 interface GigabitEthernet0/0/0/4
  no shutdown
  commit
 ```

Step 4:  Enter the following commands to see the BGP-LS information on xrvr-9.

```
RP/0/0/CPUO:xrvr-9#show bgp link-state link-state
BGP router identifier 100.0.0.9, local es number 64001
BGP generic scan interval 60 secs
Non-stop routing is enabled
BGP table state: Active
Table ID: OxO RD version: 53
BGP main routing table version 53
BGP NSR Initial initsync version 1 (Reached)
BGP NSR/ISSU Sync-Group versions 0/0
BGP scan interval 60 secs

Status codes:   s suppressed, d damped, h history, * valid, > best
                i - internal, r RIB-failure, s stale, N Next hop-discard
Origin codes:   i - IGP, e - EGP, ? - incomplete
Prefix codes:   E link, V node, TIP reacheable route, u/u unknown
                I Identifier, N local node, Rremote node, L link, P prefix
                L1/L2 ISIS level-1/level-2, O OSPF, D direct, s static/peer-node
                a area-ID, 1 link-ID, t topology-ID, 3 ISO-ID,
                c confed-ID/ ASN, bbgp-identifier, r router-ID,
                i if-address, n nbr-address, O OSPF Route-type, P IP-prefix
                d designated router address

Network     Next Hop    Metric LocPrf Weight Path
*>i[V] [12][100] [N[C64001] [b0.0.0.0] [31000.0000.0001.00]]/328 100.0.0.2 100 0 i
*>i[V] [12] [100] [N[C64001] [b0.0.0.0] [31000.0000.0002.00]]/328 100.0.0.2 100 0 i
*>i(V) (L2] [100] [N[C64001] [b0.0.0.0] [31000.0000.0006.00]]/328 100.0.0.2 100 0 i
*>i[V] [12] [10xO] [N[C64001] [b0..0.0] [31000.0000.0009.00]]/328 100.0.0.2 100 0 i
*> [V] [12] [100] [N[C64002] [b0.0.0.0] [31000.0000.0003.00]]/328 100.0.0.3 0 64002 i
*> [V] [12] [100] [N[C64002] [b0.0.0.0] [31000.0000.0004.00]]/328 100.0.0.3 0 64002 i
*> [V] [12] [100] [N[C64002] [b0.0.0.0] [31000.0000.0005.00]]/328 100.0.0.3 0 64002 i
*> [V] [12] [100] [N[C64002] [b0.0.0.0] [31000.0000.0010.00]]/328 100.0.0.3 0 64002 i
```

You can see detailed information of each NLRI by copy and pasting the NLRI in the show bgp link-state link-state command and add detail at the end.

> NOTE
> The legend shows that [E] indicates a link info NLRI, [V] indicates node info, and [T] indicates IP info.×Dismiss this alert.

```
RP/0/0/CPUO:xrvr-9#show bgp link-state link-state | i [V] | i 1000.0000.0002
Sun Apr 22 08:56:26.005 UTC
*>i(V) (L2] [100] [N[C64001] [b0.0.0.0] [31000.0000.0002.00]]/328
```
Step 5:  Execute the the follown in order to have detailed information about the specific prefix received by BGP-LS at the SR-PCE controller.
```
RP/0/0/CPUO:xrvr-9#show bgp link-state link-state [V] [12] [IOXO] [N[C64001] [b0.0.0.0] [31000.0000.0002.00]]/328 detail
Sun Apr 11 08:56:57.792 UTC
BGP routing table entry for [V] [12] [IOXO] [N[C64001] [b0.0.0.0] [31000.0000.0002.00]]/328
NLRI Type: Node
Protocol: ISIS L2
Identifier: Ox0
Local Node Descriptor:
    AS Number: 64001
    BGP Identifier: 0.0.0.0
    ISO Node ID: 1000.0000.0002.00

Versions:
 Process     bRIB/RIB   SendTb lVer
 Speaker        5           5
   Flags: Ox00000001+Ox00000000;
Last Modified: Apr 22 08:53:27.052 for 00:03:30
Paths: (1 available, best #1)
  Advertised to peers (in unique update groups):
 100.0.0.10
 Path #1: Received by speaker 0
 Flags: Ox4000000001060005, import: Ox20
 Advertised to peers (in unique update groups) :
  100.0.0.10
 Local
   100.0.0.2 (metric 10) from 100.0.0.2 (100.0.0.2)
     Origin IGP, localpret 100, valid, internal, best, group-best
     Received Path ID 0, Local Path ID 0, version 5
     Link-state: Node-name: xrve-2, ISIS area: 49.00.01, Local TE Router-ID:
                 100.0.0.2 SRGB: 19000:2000 SR-ALG: 0 SR-ALG: 1
```
Step 6:  Links are advertised as unidirectional inter-connections. For example, the link from xrvr-1 to xrvr-2 (99.1.2.1 is the interface address on xrvr-1).

```
RP/0/0/CPUO:xrvr-9#show bgp link-state link-state | incl [E] | incl 99.1.2.1
Sun Apr 11 08:57:20.221 UTC
*>i[E] [12] [IOXO] [N[C64001] [b0.0.0.0) ($1000.0000.0001.00]] [R[C64001] [b0.0.0.0] [31000.0000.0002.00]][L[199.1.2.1) [n99.1.2.2])/696
*>i[E] [12] [IOXO] [N[C64001] [b0.0.0.0] [31000.0000.0002.00]] [R[C64001] [b0.0.0.0] [31000.0000.0001.00]][L[199.1.2.2] [n99.1.2.1]]/696
```
```
RP/0/0/CPUO:xrvr-9#show bgp link-state link-state [E] [L2] [100] [N[C64001] [b0.0.0.0) (s1000.0000.0001.00]][R[C64001] (bo .0.0.0] [s1000.0000.0002.00]] [L[i99.1.2.1] [n99.1.2.2]]/696 detail
Sun Apr 11 08:57:58.758 UTC
BGP routing table entry for [E] [L2] [10XO] [N[C64001] [b0.0.0.0] [31000.0000.0001.00]][R[C64001] [b0.0.0.0] [31000.0000.000 2.00]] [L[i99.1.2.1] [n99.1.2.2]]/696
NLRI Type: Link
Protocol: ISIS L2
Identifier: 0x0
Local Node Descriptor:
        AS Number: 64001
        BGP Identifier: 0.0.0.0
        ISO Node ID: 1000.0000.0001.00
Remote Node Descriptor:
        AS Number: 64001
        BGP Identifier: 0.0.0.0
        ISO Node ID: 1000.0000.0002.00
Link Descriptor:
        Local Interface Address IPv4: 99.1.2.1
        Neighbor Interface Address IPv4: 99.1.2.2

Versions:
 Process         BRIB/RIB    SendTb lVer
 Speaker            19          19
  Flags: Ox00000001+0x00000000;
Last Modified: Apr 22 08:53:27.052 for 00:04:31

Paths: (1 available, best #1)
 Advertised to peers (in unique update groups):
    100.0.0.10
 Path #1: Received by speaker 0
 Flags: Ox4000000001060005, import: 0x20
 Advertised to peers (in unique update groups)
    100.0.0.10
 Local
    100.0.0.2 (metric 10) from 100.0.0.2 (100.0.0.2)
     Origin IGP, localpref 100, valid, internal, best, group-best
     Received Path ID 0, Local Path ID 0, version 19
     Link-state:    Local TE Router-ID: 100.0.0.1, Remote TE Router-ID:
                    100.0.0.2 admin-group: 0x00000000, max-link-bu (kbits/sec): 1000000
                    max-reserv-link-bw (kbits/sec): 0, max-unreserv-link-bu (kbits/sec):
                    0 0 0 0 0 00 0 TE-default-metric: 10, metric: 10,
                    ADJ-SID: 24015 (30)
```
Step 7:  SR-PCE uses the BGP-LS information to feed its SR-TE Database (SRTE-DB). Enter the following command (alias “h10”) to see a summary of the SRTE-DB on xrvr-9:

```
RP/0/0/CPUO:xrvr-9#show pce ipv4 topology summary
Sun Apr 11 09:00:20.129 UTC

PCE's topology database surmary:
--------------------------------

Topology nodes:             8
Prefixes:                   6
Prefix SIDS:
 Total:                     6
 Regular:                   6
 Strict:                    0
Links:
 Total:                     18
 EPE:                       4
Adjacency SIDS:
 Total:                     18
 Unprotected:               14
 Protected:                 0
 EPE:                       4

Private Information:
Lookup Nodes                12
Consistent                  yes

Update Stats (from IGP and/or BGP) :
 Noded added:               8
 Noded deleted:             0
 Links added:               20
 Links deleted:             0
 Prefix added:              24
 Prefix deleted:            0
```
Step 8:  The PCCs (xrvr-1 and xrvr-4) are configured to set up PCEP sessions with the SR-PCE (xrvr-9) node.

On xrvr-1, configure the following:
```
conf t
segment-routing traffic-eng
 pcc
  source-address ipv4 100.0.0.1
  pce address ipv4 100.0.0.9
  !
 commit
end
!
```
On xrvr-4, configure the following:
```
conf t
segment-routing traffic-eng
 pcc
  source-address ipv4 100.0.0.4
  pce address ipv4 100.0.0.9
  !
 commit
end
!
```

Step 9: Enter the following commands (show segment-routing traffic-eng pcc ipv4 peer) on both xrvr-1 and xrvr-4 to check the PCEP session between PCC and SR-PCE:

```
RP/0/0/CPU0:xrvr-1#show segment-routing traffic-eng pcc ipv4 peer
Mon Apr 12 22:06:04.372 UTC

PCC's peer database:
--------------------

Peer address: 100.0.0.9, Precedence: 255, (best PCE)
  State up
  Capabilities: Stateful, Update, Segment-Routing, Instantiation
```
Step 10:  Check on SR-PCE node (xrvr-9) the PCEP sessions with the PE nodes (xrvr-1 and xrvr-4) as following:
```
RP/0/0/CPU0:xrvr-9#show pce ipv4 peer
Mon Apr 12 21:37:09.111 UTC

PCE's peer database:
--------------------
Peer address: 100.0.0.1
  State: Up
  Capabilities: Stateful, Segment-Routing, Update, Instantiation

Peer address: 100.0.0.4
  State: Up
  Capabilities: Stateful, Segment-Routing, Update, Instantiation


```
Up to this point, you have successfully enabled the Network Controller functionality on xrvr-9.
```
