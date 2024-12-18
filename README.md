# SRTE-Lab

Segment Routing provides complete control over the forwarding paths by combining simple network instructions. It does not require any additional protocol. Indeed in some cases it removes unnecessary protocols simplifying your network. SR is a new mechanism that can be applied in conjunction with software-defined networking (SDN) applications to solve the conundrum of achieving granular control with good scalability. Segment Routing (SR) technology has been proposed to enforce effective routing strategies without relying on signaling protocols.

Segment Routing (SR), applied to the MPLS data plane, offers the ability to tunnel services (VPN, VPLS, VPWS) from an ingress PE to an egress PE, without any other protocol than ISIS or OSPF. LDP and RSVP-TE signaling protocols are not required.

The operator only needs to allocate one prefix segment per PE and the SR IGP control-plane automatically builds the required MPLS forwarding constructs from any PE to any PE.

In this lab, you will learn how to configure SR in the network and understand the preference of LDP and SR. This lab will also help specify explicit paths using both old configuration commands and new SRTE-specific configuration commands, as well as dynamic paths using a Network Controller (SR-PCE) as a Path Computation element.

The nine tasks (Task 1 – Task 9) are mandatory to follow in order to finish the LAB successfully.


Upon completion of this lab, you will be able to:

Implement SR in the provider network.
Prefer SR in the provider network.
Implement Topology-Independent Loop-Free Alternate (TI-LFA)
Implement Segment Routing Traffic Engineering in various ways
Implement SR PCE
Implement SR Dynamic path

