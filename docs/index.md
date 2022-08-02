---
hide:
- navigation
- toc
- tags
---
<h1>Orb Dynamic<br>Edge Observability</h1>


[Get Started with Orb](install/){ .md-button .md-button--primary }

***

# Why Orb?

## Distributed deep network observability
Orb manages a [fleet](about/#fleet) of **[agents](about/#agent)** deployed across distributed, hybrid infrastructure: *containers*, *VMs*, *servers*, *routers*, and *switches*. 
Agents tap into traffic streams and extract real-time insights, resulting in lightweight, actionable metrics. The result: faster time-to-action at a lower cost.


## Streaming analysis at the edge
Powered by the [pktvisor observability agent](https://pktvisor.dev), **Orb's goal is to push analysis to the edge**, where high-resolution data can be analysed in real time without the need to send raw data to a central location for batch processing. [Current analysis](https://github.com/ns1labs/pktvisor/wiki/Current-Metrics) focuses on L2-L3 Network, DNS, Flow, and DHCP with more analyzers in the works.

## Real-time agent orchestration
Orb uses Internet of Things (IoT) principles to allow the **[observability agents](https://orb.community/about/#the-agents)** to connect out from edge infrastructure to the Orb central **[control plane](https://orb.community/about/#the-control-plane)**, avoiding firewall problems. Once connected, agents are controlled in real time from the Orb Portal or REST API, orchestrating observability [policies](about/#policies) designed to precisely extract the desired insights. Agents are grouped and addressed based on [tags](about/#agent-group).

## Flexible integration with modern observability stacks
Orb was built to integrate with modern observability stacks, supporting [Prometheus](https://prometheus.io/) natively and
designed to support a host of [sinks](about/#sinks) in the future, powered by [OpenTelemetry](https://opentelemetry.io/). Collection and sinking of the metrics from the agents
is included—there is no need to run additional data collection pipelines for Orb metrics.

## Portal and REST API out of the box
Orb includes a modern, responsive user interface for managing *agents*, *agent groups*, *policies*, *datasets*, and *sinks*. Orb is API-first, and all platform functionality is available for automation via the [well-documented REST API](docs/#working-with-api-docs).

## Open source, vendor neutral, cloud native
Orb is free, open source software (FOSS) released under Mozilla Public License (MPL). It's a modern microservices application that can be deployed to any Kubernetes service in a private or public cloud. It does not depend on any single vendor to function, thus avoiding vendor lock-in. If you prefer not to run your own control plane, you can use the free [https://orb.live](https://orb.live/) community site instead, and get started immediately.

***

# Backed by NS1
**Orb** was born at [NS1 Labs](https://ns1.com/labs), where we're committed to making [open source, dynamic edge observability a reality](https://ns1.com/blog/orb-a-new-paradigm-for-dynamic-edge-observability).

