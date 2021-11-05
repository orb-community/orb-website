
Orb, as a control plane, facilitates edge network observability when utilized in conjunction with a side-car style agent. For a logical pairing with Orb, check out [pktvisor](https://pktvisor.dev/)—a production-ready [open source](https://github.com/ns1labs/pktvisor/) observability agent.

## Orb + pktvisor
 Via the Orb control plane and user interface, you decide what data to extract from which agents. The resource-efficient, side-car style [pktvisor](https://github.com/ns1/pktvisor) observability agent performs edge analysis on network data streams. This combination:

* Adjusts analysis and collection parameters dynamically across the entire fleet via a powerful control plane
* Provides centralized fleet management, allowing you to configure heartbeats, tagging, and grouping for each of the pktvisor agents
* Orchestrates data set policies that specify the type of data to extract from each agent
