
Orb, as a control plane, facilitates edge network observability when utilized in conjunction with a side-car style agent. For a seamless pairing with Orb, check out [pktvisor](https://pktvisor.dev/)—a production-ready [open source](https://github.com/ns1labs/pktvisor/) observability agent.

## Orb + pktvisor
 Via Orb's user interface, you decide what data to extract from which agents. The resource-efficient, side-car style [pktvisor](https://github.com/ns1/pktvisor) observability agent performs edge analysis on network data streams. 
 
 This combination allows you to:

* Adjust analysis and collection parameters dynamically across the entire fleet via a powerful control plane
* Perform centralized fleet management, allowing you to configure heartbeats, tagging, and grouping for each of the pktvisor agents
* Orchestrate data-set policies that specify the type of data to extract from each agent

In terms of metrics, pktvisor can capture DNS, DHCP, and L2/L3 network data via packet capture, `dnstap`, `sflow`, among other input methods. 

For a complete list of metrics currently collected by pktvisor, look [here](https://github.com/ns1labs/pktvisor/wiki/Current-Metrics).

To view a Grafana dashboard for visualizing pktvisor Prometheus metrics, look [here](https://grafana.com/grafana/dashboards/14221).

## Core Concepts
### Agent
This is a sensor installed next to a data source (at the edge) so it can summarize, analyze, and collect information.

### Agent group
This is a list of simple key/value pairs that match against agent tags to dynamically define a group of agents. For example, “region: US” will group all agents in the fleet that have this key/value set in their tags.

### Fleet
This is a collection of agents which may be widely distributed and number in the tens, hundreds, or thousands and are all able to connect to and contribute to the same observability system.

### Policies
These are the instructions sent to the agents to define how to collect metrics. It is backend-specific information that is needed at the edge.

### Data Set
These are instructions for how specific agents in the fleet (matched according to a given agent group), should apply collection policies and where they should sink their data. Orb will manage many data sets concurrently.

### Sinks
This is where you send the data. This is the system that collects the data and allows you to sync that data to different locations. Currently, Orb supports Prometheus but will support more backends in the future.