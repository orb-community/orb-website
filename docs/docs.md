# Documentation


## Orb Concepts
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

## Running Orb Agent
### Sample provisioning command
Replace `mock` interface with host network interface (e.g. `eth0`).

        docker run -d --net=host
        -e ORB_CLOUD_ADDRESS=<HOST>
        -e ORB_CLOUD_MQTT_ID=<AGENTID>
        -e ORB_CLOUD_MQTT_CHANNEL_ID=<CHANNELID>
        -e ORB_CLOUD_MQTT_KEY=<AGENTKEY>
        -e PKTVISOR_PCAP_IFACE_DEFAULT=mock
        ns1labs/orb-agent
