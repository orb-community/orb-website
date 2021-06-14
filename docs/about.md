# About

## Intro
Born at [NS1 Labs](https://ns1.com/labs), **Orb** is a new kind of observability platform, fully [open source](http://orb.community) and backed by [NS1](https://ns1.com). Orb orchestrates network observability policies across a fleet of agents on the edge in real time, allowing you to dynamically filter and collect precise “small data” from your global network, providing you with lightweight, immediately actionable results you can make sense of.

## How is Orb Different?

**Orb** focuses on edge analysis, preferring “small data” style actionable metrics over collection and storage of terabytes of raw, inscrutable data: think of it as a scalpel rather than a machete

* Powerful control plane to adjust analysis and collection parameters in real time across the entire fleet
* Designed to plug in to popular observability stacks like Prometheus and Elasticsearch, plus cloud storage and data pipelines
* Visualize and automate on results both at the edge (for hyper-real time local view) or centrally in the cloud (for global view)
* Fast, reliable, secure, modular, extensible, open source

## How Orb Works
**Orb** is composed of a side-car style agent (the open source [pktvisor](https://github.com/ns1/pktvisor)) that performs edge analysis on network data streams, combined with an IoT-inspired central control plane and UI that provide fleet and configuration management functionality.

<img src="../img/ORB-diagram1.png" alt="Orb" width="400"/>

* Resource efficient [agent](https://github.com/ns1/pktvisor) that analyses data streams in real-time
* Dynamic administration and configuration
* Modular system of inputs and analysers
* Cloud native, microservices-based architecture
* Self-host (docker-compose or k8s) or SaaS option
* Manages fleet of pktvisor agents, including heartbeats, tagging and grouping
* Orchestrates data set policies to decide what data to extract from which agents
* Central collection and exporting of summarized results that are then available for analytics, security, automation, etc
* Provides a single pane of glass across all sensors
* Provides a deep dive into visualizations and analytics that can help you make more effective network decisions in the future 

