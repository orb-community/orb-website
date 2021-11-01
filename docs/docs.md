# Resources

## Documentation
Official documentation will be released with the first public version of Orb.

## Orb Architecture
### Agent
This is a sensor that is installed next to a data source (at the edge) so that it can summarize, analyze, and collect information.

### Agent group
This is a list of simple key/value pairs that match against agent tags to dynamically define a group of agents. For example, “region: US” will group all agents in the fleet that have this key/value set in their tags.

### Fleet
This is a collection of agents which may be widely distributed and number in the tens, hundreds, or thousands and are all able to connect to and contribute to the same observability system.

### Policies
These are the instructions sent to the agents to define how to collect metrics. It is backend-specific information that is needed at the edge.

### Data Set
These are instructions for how specific agents in the fleet (matched according to a given agent group), should apply collection policies and where they should sink their data. Orb will manage many datasets concurrently.

### Sinks
This is where you send the data. This is the system that collects the data and allows you to sync that data to different locations. Currently supports Prometheus but will support more backends in the future.

## Articles
* [Extracting the Signal: Rethinking Network Observability](https://ns1.com/blog/extracting-the-signal-rethinking-network-observability)
* [Orb - A New Paradigm for Dynamic Edge Observability](https://ns1.com/blog/orb-a-new-paradigm-for-dynamic-edge-observability)
* [A Wave of Open Source Innovation at NS1 Labs with Orb and NetBox](https://ns1.com/blog/a-wave-of-open-source-innovation-at-ns1-labs-with-orb-and-netbox)
* [NS1 Launches Innovation Lab to Solve Challenges in Modern Application Delivery and Edge Networking](https://ns1.com/press/ns1-launches-innovation-lab-to-solve-challenges-in-modern-application-delivery-and-edge-networking)

## Other Resources

* [NS1 Labs](https://ns1.com/labs)
* [NS1 INSIGHTS 2021: Build the Better Future](https://resources.ns1.com/ins1ghts-2021-replay) 2021 Talk [Recording](https://www.youtube.com/watch?v=cyKaGIFhiGk) 
* [ICANN DNS Symposium](https://www.icann.org/ids) 2021 Talk [Recording](https://drive.google.com/file/d/14sDxmwvMV1wF_1cBUBv3qj29Hen_thea/view?usp=sharing) and [Slides](https://drive.google.com/file/d/1fEydsa-suUbE9fNHo87zY88mOmbeZviU/view)
* [DNS-OARC](https://www.dns-oarc.net/) 2020 Talk [Recording](https://www.youtube.com/watch?v=PwEOePOvkug&list=PLCAxS3rufJ1cv93o5VK226HEHo25fv06M&index=3) (first talk) with [Slides](https://indico.dns-oarc.net/event/34/contributions/783/attachments/774/1328/pktvisor3-OARC-sweyrick.pdf)
* O'reilly Velocity San Jose 2019 Talk [Recording](https://drive.google.com/file/d/1AjhbUjkXT5saBP6iYIZjoinYsiZ5LDaV/view?usp=sharing) with [Slides](https://docs.google.com/presentation/d/e/2PACX-1vR984fhii0Pso97RRjSFgZupknwQqf-XMhGuriT8HPHHiLlB1c4SnDtRnJtX66nxYv2GETk4ex81QiU/pub?start=false&loop=false&delayms=3000)
