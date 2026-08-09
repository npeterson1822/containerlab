August 8, 2026

This BGP lab is the first real lab I've build with Containerlab, which I enjoy using a lot. The goal is to have two available transit providers available within AS65000, both advertising a 100.0.0.1/32 prefix. 

As the lab stands now, BGP is configured so that prefix is advertised via both providers and propagated into the core network. eBGP is configured between the edge routers and transit PE routers, and iBGP is configured between the routers within AS65000. 

For this particular setup, I am not worried about a full iBGP mesh. Both edge routers connect to both core routers, so the core routers and edge routers don't need to be meshed to one another. In a future lab, I'll include more core routers and use a reflector.

For the IGP for basic L3 reachability (which in this case was technically superfluous, but I wanted to use edge loopback IPs as the iBGP next-hops), I used OSPF. Routers know how to reach each other's loopback addresses. This means that iBGP can just set the loopback of the advertising router as the next hop (via next-hop-self) and it's reachable by all other routers in the AS. 

The only route map I'm currently using is one that permits any inbound and outbound advertisements - FRR requires this for any advertisements to occur. I have also not touched or adjusted OSPF costs. Therefore, no BGP attributes are manipulated. IGP metrics for both core routers to both providers are identical. FRR prefers the lower router ID, so the route installed into the RIB is the one through 1.1.1.1 (edge1) out to transit1.

To build off this, I want to experiment with various methods of influencing outbound path selection/RIB installation using route-maps.