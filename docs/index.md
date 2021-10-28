---
hide:
- navigation

---
<script async defer src="https://buttons.github.io/buttons.js"></script>
<img src="img/ORB-logo-black@3x.png" alt="Orb" width="500"/>

*A business intelligence platform for orchestrating dynamic edge observability*

<a class="github-button" href="https://github.com/ns1labs/orb" data-size="large" aria-label="Star ns1labs/orb on GitHub">Star on GitHub</a>

## Introduction
Born at [NS1 Labs](https://ns1.com/labs), **Orb** is a new kind of observability platform that makes it easier for operators and developers to gain a deeper understanding of their networks, distributed applications, and traffic flows in real time. Orb allows you to collect and filter critical "small data" from your global network and translates that data into lightweight, actionable insights. The platform is completely [open-source](https://github.com/ns1labs/orb), extensible, vendor-neutral, and cloud-native.

Orb combines concepts from edge computing, the Internet of Things (IoT), and high-throughput stream processing—giving you command over a fleet of distributed observability agents deployed at the edge.

## Features
### Form
 **Orb** orchestrates network observability policies across a fleet of agents on the edge—providing you with lightweight, immediately actionable results in real time.

* Plugs into popular observability stacks, like Prometheus and Elasticsearch, as well as cloud storage and data pipelines
* Powered by a fast, reliable, secure, modular, extensible, open-source platform
* Enables dynamic administration and configuration
* Deployed as a modular system of inputs and analyzers
* Built using a cloud-native, microservices-based architecture
* Offers a self-hosted (via docker-compose or k8s) or SaaS option

### Function
 The resource-efficient, side-car style [pktvisor](https://github.com/ns1/pktvisor) observability agent performs edge analysis on network data streams, while an IoT-inspired central control plane and UI provides fleet and configuration management tools.

* Adjusts analysis and collection parameters dynamically across the entire fleet via a powerful control plane
* Provides centralized fleet management, allowing you to configure heartbeats, tagging, and grouping for each of the pktvisor agents
* Orchestrates data set policies that specify the type of data to extract from each agent

### Outcome
**Orb** focuses on edge analysis, preferring “small data”-style, actionable metrics over the collection and storage of terabytes of raw, inscrutable data.

* Allows you to visualize and automate on data at the edge for a hyper-real-time *local view* or centrally in the cloud for a *global view*
* Streamlines data collection and exporting back to the control data where it is available for analytics, security, automation, etc.
* Provides a single pane of glass across all sensors
* Delivers the necessary tools to help you make more effective network decisions 

<img src="../img/ORB-diagram1.png" alt="Orb" width="400"/>

