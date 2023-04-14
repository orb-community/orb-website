This solution guide will walk you through setting up ==Observability for Network Traffic (Flow)== using Orb agents. This is a monitoring configuration that can be used to collect and process network flow datagrams (sFlow, Netflow, IPFIX) at the edge to generate summarized metric streams.

## Configure Observation

The first step is to configure the agents to accept inbound network flow datagrams. This is done by configuring the agents to enable the ==Flow== input tap.

### Flow tap configuration

The ==Flow== tap supports sFlow, Netflow and IPFIX datagram formats. The `flow_type` attribute should be set to match your environment:

- `flow_type: sflow` to receive sFlow datagrams
- `flow_type: netflow` to receive either Netflow or IPFIX datagrams

 Your agent tap configuration should look something like this:

=== "sFlow"
    ```yaml
    visor:
        taps:
            my_flow_tap:
                input_type: flow
                config:
                    port: 6343
                    bind: 192.168.1.1
                    flow_type: sflow
                tags:
                    flow: true
    ```
=== "Netflow/IPFIX"
    ```yaml
    visor:
        taps:
            my_flow_tap:
                input_type: flow
                config:
                    port: 9996
                    bind: 192.168.1.1
                    flow_type: netflow
                tags:
                    flow: true
    ```

## Configure Analysis

The second step is to configure the agents to analyze the received flow datagrams. This is done by defining and applying a tailored ==Policy== to the agents, which specificies what filters to apply on the data (if any) and what metrics to generate.

### Create a basic Policy

The following is a simple policy that allows you to observe from what sources you are receiving flow datagrams:

=== "YAML"
    ```yaml
    handlers:
        modules:
            flow_simple:
                type: flow
                metric_groups:
                    enable:
                      - by_bytes
                      - top_interfaces
                    disable:
                      - all
    input:
        input_type: flow
        tap: my_flow_tap
    kind: collection
    ```

### Create a more advanced Policy

The following is a more advanced policy that allows you to observe aggregated data based on ASNs, filtering on datagrams coming from specific devices and interfaces (expressed as SNMP index numbers):

=== "YAML"
    ```yaml
    handlers:
        modules:
            flow_advanced:
                config:
                    summarize_ips_by_asn: true
                    exclude_unknown_asns_from_summarization: true
                    exclude_asns_from_summarization:
                      - 16509
                    subnets_for_summarization:
                      - 0.0.0.0/24
                      - ::/64
                filter:
                    only_device_interfaces:
                        10.10.10.1: [1,2,3]
                        10.10.10.2: [1,2,3]
                        10.10.10.3: [1,2,3]
                type: flow
                metric_groups:
                    enable:
                      - counters
                      - by_bytes
                      - top_ports
                      - top_ips
                      - top_tos
                    disable:
                      - all
    input:
        input_type: flow
        tap: my_flow_tap
    kind: collection
    ```

For more tailored Flow policies to filter on specific traffic or to add (or exclude) specific metrics, please refer to the [Orb Policy Reference](/documentation/advanced_policies).

### Applying your policy

The final step is to apply your policy by creating a [Dataset](/getting_started/#create-a-dataset), which links your agents (through an [Agent Group](/getting_started/#create-an-agent-group)) with your policy and the [Sink](/getting_started/#create-a-sink) where you want to send your metrics.