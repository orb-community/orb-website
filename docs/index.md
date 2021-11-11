---
hide:
- navigation

---
<script async defer src="https://buttons.github.io/buttons.js"></script>
<img src="img/ORB-logo-black@3x.png" alt="Orb" width="500"/>

*An open-source orchestration platform for dynamic edge observability*

<a class="github-button" href="https://github.com/ns1labs/orb" data-size="large" aria-label="Star ns1labs/orb on GitHub">Star on GitHub</a>

[Get started with Orb](https://getorb.io/install/)

## The story
Born at [NS1 Labs](https://ns1.com/labs), **Orb** is a new kind of observability platform that makes it easier for operators and developers to gain a deeper understanding of their networks, distributed applications, and traffic flows in real time. Orb supplements your observability stack by providing dynamic orchestration of observability agents that can extract business intelligence at the edge. The platform is completely [open source](https://github.com/ns1labs/orb), extensible, vendor-neutral, and cloud-native.

## The components
### The agents
Orb manages **observability agents** that collect network data from applications, systems, and edge locations (VMs, containers, servers) in real time. An agent acts as a sensor installed next to a data source so it can summarize, analyze, and collect information. You run agents on your edge locations and orchestrate them via the control plane.

While ingesting a high volume of information-dense data streams, the agents translate this information to deliver consumable, actionable data sets. Developers and network operators can view this data locally at the edge via the agent's command-line interface and globally in a central database via a standard dashboard tool (such as Grafana).

### The control plane
Orb combines concepts from edge computing, the Internet of Things (IoT), and high-throughput stream processing. 

As an IoT-inspired cloud **control plane**, Orb connects a fleet of distributed observability agents (such as the [open source pktvisor](https://github.com/ns1labs/pktvisor/)) deployed at the edge and gives you command over that fleet. In operating the control plane, you issue instructions to the agents, dynamically programming and re-programming them with data-collection policies to build different data sets in real time. 
  
## The features
Orb orchestrates network observability policies across a fleet of agents on the edge—providing you with lightweight, immediately actionable results.

* Plugs into popular observability stacks, like Prometheus and Elasticsearch, as well as cloud storage and data pipelines
* Built using a cloud-native, microservices-based architecture
* Offers a self-hosted (via Docker Compose or Kubernetes) or a SaaS option

Orb focuses on edge analysis, preferring “small data”-style, actionable metrics over the collection and storage of terabytes of raw, inscrutable data.

* Allows you to visualize and automate on data at the edge for a hyper-real-time local view or centrally in the cloud for global a view
* Streamlines data collection and exporting back to the control plane where it is available for analytics, security, automation, etc.
* Provides a single pane of glass across all sensors


<img src="../img/ORB-diagram2.png" alt="Orb" width="600"/>

