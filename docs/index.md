---
hide:
- navigation

---
<script async defer src="https://buttons.github.io/buttons.js"></script>
<img src="img/ORB-logo-black@3x.png" alt="Orb" width="500"/>

*A cloud-native orchestration platform for dynamic edge observability*

<a class="github-button" href="https://github.com/ns1labs/orb" data-size="large" aria-label="Star ns1labs/orb on GitHub">Star on GitHub</a>

## The story
Born at [NS1 Labs](https://ns1.com/labs), **Orb** is a new kind of observability platform that makes it easier for operators and developers to gain a deeper understanding of their networks, distributed applications, and traffic flows in real time. Orb allows you to collect and filter critical "small data" from your global network and translates that data into lightweight, actionable insights. The platform is completely [open-source](https://github.com/ns1labs/orb), extensible, vendor-neutral, and cloud-native.

## The pieces
### The agents
Orb manages **observability agents** that collect network data from applications, systems, and edge locations (VMs, containers, servers) in real time. You run agents on your edge locations and orchestrate them via the control plane.

While ingesting a high volume of information-dense data streams, the agents translate this information to deliver consumable, actionable data sets. Developers and network operators can view this data locally at the edge via the agent's command-line interface and globally in a central database via a standard dashboard tool (such as Grafana).

### The control plane
Orb combines concepts from edge computing, the Internet of Things (IoT), and high-throughput stream processing. 

As an IoT-inspired cloud **control plane**, Orb connects a fleet of distributed observability agents deployed at the edge and gives you command over that fleet. In operating the control plane, you issue instructions to the agents, dynamically programming and re-programming them with data-collection policies to build different data sets in real time. 
  

<img src="../img/ORB-diagram2.png" alt="Orb" width="600"/>

