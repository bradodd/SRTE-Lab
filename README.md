# SRTE-Lab

Segment Routing provides complete control over the forwarding paths by combining simple network instructions. It does not require any additional protocol. Indeed in some cases it removes unnecessary protocols simplifying your network. SR is a new mechanism that can be applied in conjunction with software-defined networking (SDN) applications to solve the conundrum of achieving granular control with good scalability. Segment Routing (SR) technology has been proposed to enforce effective routing strategies without relying on signaling protocols.

Segment Routing (SR), applied to the MPLS data plane, offers the ability to tunnel services (VPN, VPLS, VPWS) from an ingress PE to an egress PE, without any other protocol than ISIS or OSPF. LDP and RSVP-TE signaling protocols are not required.

The operator only needs to allocate one prefix segment per PE and the SR IGP control-plane automatically builds the required MPLS forwarding constructs from any PE to any PE.

In this lab, you will learn how to configure SR in the network and understand the preference of LDP and SR. This lab will also help specify explicit paths using both old configuration commands and new SRTE-specific configuration commands, as well as dynamic paths using a Network Controller (SR-PCE) as a Path Computation element.

The nine tasks (Task 1 – Task 9) are mandatory to follow in order to finish the LAB successfully.


Upon completion of this lab, you will be able to:

* Implement SR in the provider network.<br>
* Prefer SR in the provider network.<br>
* Implement Topology-Independent Loop-Free Alternate (TI-LFA)<br>
* Implement Segment Routing Traffic Engineering in various ways<br>
* Implement SR PCE<br>
* Implement SR Dynamic path<br>

Lab Tasks
This page gives you an overview about all tasks included in this lab. While you can easily navigate between the tasks on any task-specific page, use this page as overview.

* Task 1: Verify LDP
* Task 2: Implement Segment Routing
* Task 3: Migrate LDP to Segment Routing
* Task 4: Implement TI-LFA
* Task 5: Implement SR-TE with old CLI (using Tunnels)
* Task 6: Implement SR-TE with new CLI (using Policy)
* Task 7: Enable Network Controller (SR-PCE)
* Task 8: Configure SR Dynamic Path
* Task 9: Advanced SR-TE (Flex-Also, AS, ODN)

## Physical 
<img src="images/SRTE Lab.png" width="1200">
