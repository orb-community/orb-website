
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
