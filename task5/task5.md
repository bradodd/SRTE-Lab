# Task 5: Implement SR-TE using Tunnels (Optional Task - Skip to next task)

This scenario demonstrates how to configure SR Traffic Engineering Tunnels in both explicit and dynamic manner.

Explicit path to xrvr-4 is:

The path is xrvr-1 ⮕ xrvr-2  ⮕ xrvr-3 ⮕ xrvr-5 ⮕ xrvr-4.

Step 1:

Enable mpls traffic-eng on all the links of the router xrvr-1,xrvr-2,xrvr-3,xrvr-4,xrvr-5,xrvr-6 to support Traffic Engineering in the network as shown below.

> WARNING
> Please enable Traffic Engineering process under ISIS on: xrvr-1, xrvr-2, xrvr-3, xrvr-4, xrvr-5 and xrvr-6.×Dismiss this alert.
```
configure
  router isis 1
	address-family ipv4 unicast
	mpls traffic-eng level-2-only
	mpls traffic-eng router-id loopback0
	root
  mpls traffic-eng
commit
end
```
Step 2:
Tunnels in SR can be created in both ways, explicit, and dynamic.

Let’s check first the Prefix-SIDs (SR Labels) for our respective Core Routers (P and PE).

Keep in mind, for SR-TE we will mostly use the Prefix-SIDs to create the path, via Dynamic or Static (Explicit) tunnels.
```
RP/0/0/CPU0:xrvr-1#show isis segment-routing label table
Sun Apr 11 12:47:26.031 UTC

IS-IS 1 IS Label Table
Label         Prefix/Interface
----------    ----------------
19001         Loopback0
19002         100.0.0.2/32
19003         100.0.0.3/32
19004         100.0.0.4/32
19005         100.0.0.5/32
19006         100.0.0.6/32
```
Step 3:
Enter the following commands to create a dynamic SR-TE “tunnel-te20” on xrvr-1 (this creates a SR-TE Tunnel towards the other PE, xrvr-4).
```
configure
  interface tunnel-te 20
	ipv4 unnumbered loopback0
	destination 100.0.0.4
	autoroute destination 100.0.0.4
	path-option 1 dynamic segment-routing
commit
end
```
Step 4:
Check the tunnel with alias “d3″ (show mpls traffic-eng tunnels 20) on xrvr-1. The Tunnel 20 is created dynamically using SR and the path that it has taken is shown below.

The path shows that it takes the route xrvr-1  ⮕  xrvr-2  ⮕  xrvr-3  ⮕  xrvr-4.
In your lab it can take the path xrvr-1  ⮕  xrvr-6  ⮕  xrvr-5  ⮕  xrvr-4.

Step 5:
Enter the following command to find the Binding SID (24018) for the SR Policy instantiated from “tunnel-te 20” on xrvr-1. By default, the binding SID of an SR policy is dynamically allocated.
```
RP/0/0/CPU0:xrvr-1#show mpls traffic-eng tunnels 20 detail | i Bind
Fri Nov 27 15:38:09.703 UTC
  Binding SID: 24018
```
  Step 6:
Check the route towards 100.0.0.4/32 using "show route 100.0.0.4/32" from xrvr-1, it shows its going via tunnel-te20.
```
RP/0/0/CPU0:xrvr-1#show route 100.0.0.4/32
Fri Nov 27 15:38:40.810 UTC

Routing entry for 100.0.0.4/32
  Known via "te-client", distance 2, metric 30 (connected)
  Installed Nov 27 15:32:37.275 for 00:06:03
  Routing Descriptor Blocks
    directly connected, via tunnel-te20
      Route metric is 30
  No advertising protos.
  ```
Step 7:
Enter the following command to verify the forwarding entry of the Tunnel instantiated from “tunnel-te 20” on xrvr-1. A forwarding entry is installed, which steers the packets into the SR based Tunnel.

```
RP/0/0/CPU0:xrvr-1#show mpls forwarding tunnels
Fri Nov 27 15:39:13.218 UTC
Tunnel        Outgoing    Outgoing     Next Hop        Bytes
Name          Label       Interface                    Switched
------------- ----------- ------------ --------------- ------------
tt20         (SR) 24015       Gi0/0/0/0    99.1.2.2        705
Step 8:
Enter the following command to review the imposed Label Stack:
```
```
RP/0/0/CPU0:xrvr-1#show mpls forwarding tunnels detail
Fri Nov 27 15:40:10.764 UTC
Tunnel        Outgoing    Outgoing     Next Hop        Bytes
Name          Label       Interface                    Switched
------------- ----------- ------------ --------------- ------------
tt20         (SR) 24015       Gi0/0/0/0    99.1.2.2        812
     Updated: Nov 27 15:32:36.805
     Version: 64, Priority: 2
     Label Stack (Top -> Bottom): { 24015 24013 }
     NHID: 0x0, Encap-ID: N/A, Path idx: 0, Backup path idx: 0, Weight: 0
     MAC/Encaps: 14/22, MTU: 1500
     Outgoing Interface: GigabitEthernet0/0/0/0 (ifhandle 0x00000020)
     Packets Switched: 15

  Interface:
    Name: tunnel-te20 (ifhandle 0x00000070)
    Local Label: 24019, Forwarding Class: 0, Weight: 0
    Packets/Bytes Switched: 0/0
```

Step 9: Create a dynamic SR-TE “tunnel-te20” on xrvr-4. This SR-TE tunnel will have as destination the xrvr-1.

Apply the configuration on xrvr-4 manually as shown below:
```
configure
  interface tunnel-te 20
	ipv4 unnumbered loopback0
	destination 100.0.0.1
	autoroute destination 100.0.0.1
	path-option 1 dynamic segment-routing
commit
end
```
Step 10: Check the tunnel as shown below (show mpls traffic-eng tunnels 20) on xrvr-4. The Tunnel 20 is created dynamically using SR and the path that it has taken is shown below at the bottom of the picture.

The path shows that it takes the route:  xrvr-4  ⮕  xrvr-3  ⮕  xrvr-2  ⮕  xrvr-1.
In your lab it can take the path xrvr-4  ⮕  xrvr-5  ⮕  xrvr-6  ⮕  xrvr-1.

Step 11:
Enter the following command to find the Binding SID (24015) for the Tunnel instantiated from tunnel-te 20 on xrvr-4. By default, the binding SID of a Tunnel is dynamically allocated.
```
RP/0/0/CPU0:xrvr-4#show mpls traffic-eng tunnels 20 detail | i Bind
Sun Apr 11 12:35:09.703 UTC
  Binding SID: 24015
```
Step 12: Check the route towards 100.0.0.1/32 on xrvr-4 as shown below. It must be going now via “tunnel-te20”.
```
RP/0/0/CPU0:xrvr-4#show route 100.0.0.1/32
Sun Apr 11 12:39:42.330 UTC

Routing entry for 100.0.0.1/32
  Known via "te-client", distance 2, metric 30 (connected)
  Installed Nov 27 12:38:37.275 for 00:03:02
  Routing Descriptor Blocks
    directly connected, via tunnel-te20
      Route metric is 30
  No advertising protos.
```
Step 13: Enter the following command to verify the forwarding entry of the Tunnel instantiated from “tunnel-te20” on xrvr-4. A forwarding entry is installed for the Binding SID, which steers the packets with the Binding SID label into the Tunnel.
```
RP/0/0/CPU0:xrvr-4#show mpls forwarding tunnels
Sun Apr 11 12:41:13.148 UTC
Tunnel        Outgoing    Outgoing     Next Hop        Bytes
Name          Label       Interface                    Switched
------------- ----------- ------------ --------------- ------------
tt20         (SR) 24011       Gi0/0/0/0    99.3.4.2        705
```

Step 14: Enter the following command (show mpls forwarding tunnels detail) on xrvr-4 to review the imposed Label Stack.
```
RP/0/0/CPU0:xrvr-4#show mpls forwarding tunnels detail
Sun Apr 1 12:51:13.764 UTC
Tunnel        Outgoing    Outgoing     Next Hop        Bytes
Name          Label       Interface                    Switched
------------- ----------- ------------ --------------- ------------
tt20         (SR) 24015       Gi0/0/0/0    99.1.2.2        812
     Updated: Apr 11 12:50:36.845
     Version: 64, Priority: 2
     Label Stack (Top -> Bottom): { 24011 24013 }
     NHID: 0x0, Encap-ID: N/A, Path idx: 0, Backup path idx: 0, Weight: 0
     MAC/Encaps: 14/22, MTU: 1500
     Outgoing Interface: GigabitEthernet0/0/0/0 (ifhandle 0x00000020)
     Packets Switched: 33

  Interface:
    Name: tunnel-te20 (ifhandle 0x00000070)
    Local Label: 24016, Forwarding Class: 0, Weight: 0
    Packets/Bytes Switched: 0/0
```

In previous tasks we created bi-directional dynamic paths/tunnels between xrvr-1 and xrvr-4. Now we are about to create Explicit paths, meaning we are going to tell xrvr-1 and xrvr-4 which path to take to reach each other.

Step 15: Create an explicit path on xrvr-1.

```
configure
	explicit-path name SIDLIST1
	index 10 next-label 19002
	index 20 next-label 19003
	index 30 next-label 19005
	index 40 next-label 19004
	commit
end
```
Step 16:
Create an explicit tunnel, “tunnel-te1” on xrvr-1.
```
configure
	interface tunnel-te1
	ipv4 unnumbered loopback0
	destination 100.0.0.4
	autoroute destination 100.0.0.4
	path-option 1 explicit name SIDLIST1 segment-routing
	commit
end
```
Step 17:
Check the “tunnel-te1” path by using alias “d10”. The path is as per the explicit path SIDLIST1 configured previously.


Step 18:
Check the Binding-sid of the tunnel by using alias “d11”.
```
RP/0/0/CPU0:xrvr-1#d11
RP/0/0/CPU0:xrvr-1#show mpls traffic-eng tunnels 1 detail | i Bind
Fri Nov 27 15:54:21.106 UTC
  Binding SID: 24020
```
Step 19:
Check the route to the xrvr-4 loopback interface from xrvr-1 using alias “d5″.
```
RP/0/0/CPU0:xrvr-1#d5
RP/0/0/CPU0:xrvr-1#show route 100.0.0.4/32
Fri Nov 27 15:54:55.074 UTC

Routing entry for 100.0.0.4/32
  Known via "te-client", distance 2, metric 30 (connected)
  Installed Nov 27 15:42:47.694 for 00:12:07
  Routing Descriptor Blocks
    directly connected, via tunnel-te20
      Route metric is 30
    directly connected, via tunnel-te1
      Route metric is 30
  No advertising protos.
```
Step 20:
Check the labels used by xrvr-1 to reach xrvr-4 by using the alias “d12”.

```
RP/0/0/CPU0:xrvr-1#traceroute 100.0.0.4 source loopback0
Fri Nov 27 15:55:28.811 UTC

Type escape sequence to abort.
Tracing the route to 100.0.0.4

 1  99.1.2.2 [MPLS: Labels 24015/24013 Exp 0] 29 msec  9 msec  19 msec
 2  99.2.3.3 [MPLS: Label 24013 Exp 0] 39 msec  9 msec  9 msec
 3  99.3.4.4 19 msec  *  9 msec
```
Step 21:
Check the trace from xrvr-7 to xrvr-8 as below.
```
RP/0/0/CPU0:xrxr-7#traceroute 100.0.0.8
Fri Nov 27 15:56:25.534 UTC

Type escape sequence to abort.
Tracing the route to 100.0.0.8

 1  99.1.7.1 9 msec  0 msec  0 msec
 2  99.1.2.2 [MPLS: Labels 19003/19005/19004/24014 Exp 0] 29 msec  9 msec  9 msec
 3  99.2.3.3 [MPLS: Labels 19005/19004/24014 Exp 0] 9 msec  9 msec  19 msec
 4  99.3.5.5 [MPLS: Labels 19004/24014 Exp 0] 9 msec  9 msec  9 msec
 5  99.4.5.4 [MPLS: Label 24014 Exp 0] 9 msec  9 msec  19 msec
 6  99.4.8.8 9 msec  *  29 msec
```

> ATTENTION
> Please shutdown tunnel 'te-20' on xrvr-1 before moving to Step-22.×Dismiss this alert.

Step 22:
Create an explicit path on xrvr-4.
```
configure
	explicit-path name SIDLIST1
	index 10 next-label 19005
	index 20 next-label 19003
	index 30 next-label 19002
	index 40 next-label 19001
	commit
end
```
Step 23:
Create an explicit tunnel, “tunnel-te1” on xrvr-4 as shown below.

```
configure
	interface tunnel-te1
	ipv4 unnumbered loopback0
	destination 100.0.0.1
	autoroute destination 100.0.0.1
	path-option 1 explicit name SIDLIST1 segment-routing
	commit
end
```
Step 24:
Check the tunnel-te1 path (show mpls traffic-eng tunnels 1) on xrvr-4. The path is as per the explicit path SIDLIST1 configured previously. See the bottom of the picture below for the Path.


Step 25:
Check the Binding-SID of the tunnel as shown below:
```
RP/0/0/CPU0:xrvr-4#show mpls traffic-eng tunnels 1 detail | i Bind
Sun Apr 11 13:37:14.154 UTC
  Binding SID: 24000
```

Step 26:
Check the route to the xrvr-1 loopback interface from xrvr-4 as shown below:
```
RP/0/0/CPU0:xrvr-4#show route 100.0.0.1/32
Sun Apr 11 13:46:55.244 UTC

Routing entry for 100.0.0.1/32
  Known via "te-client", distance 2, metric 30 (connected)
  Installed Apr 11 13:41:33.694 for 00:23:07
  Routing Descriptor Blocks
    directly connected, via tunnel-te20
      Route metric is 30
    directly connected, via tunnel-te1
      Route metric is 30
  No advertising protos.
```
Step 27:
Check the labels used by xrvr-4 to reach xrvr-1 as shown below:


Step 28:
Check the traceroute from xrvr-8 towards xrvr-7 as shown below:


> INFORMATION
> The trace in your lab may be different. Check the traces after shutting down either of the tunnels. This section helps to understand the old method of creating the tunnels, and is still valid with Segment Routing.
