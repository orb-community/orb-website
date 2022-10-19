## Configuring Observability for Edge Networks

!!! info
    This is a general purpose monitoring configuration for Orb agents that can be used wherever observability of the network is required.

### Overview

This solution guide will walk you through setting up general purpose monitoring for the Edge network using Orb agents.

### Agent Configuration

The default **PCAP** tap configuration is all that is necessary for broad monitoring. Your agent tap configurations should look something like this:

=== "YAML"
    ```yaml
    taps:
        default_pcap:
            interface: "visor.module.input/1.0"
            input_type: pcap
            config:
                iface: "eth0"
    ```
=== "JSON"
    ```json
    "taps": {
        "default_pcap": {
            "config": {
                "iface": "eth0",
            },
            "input_type": "pcap",
            "interface": "visor.module.input/1.0"
        }
    }
    ```

Please be sure that the `iface` and `pcap_source` are configured with the appropriate interface name on the node where the agent is running. If the interface defined is not correct, you will need to re-run the agent provisioning command specifying the correct interface using the [`PKTVISOR_PCAP_IFACE_DEFAULT`](documentation/orb/running_orb_agent.md#sample-provisioning-commands) environment variable.

!!! tip
    This monitoring configuration presumes that the Orb agents have full visibility on the network traffic of interest. This can be accomplished by mirroring the traffic to the configured interface or by configuring the interface to be in promiscuous mode.

### Policy

The default policy created using the [policy wizard](/getting_started/#create-a-policy) is all that is necessary for broad monitoring. Please be sure to add the desired handlers to the policy, such as the **NET** and **DNS** handlers. Your policy should look something like this:

=== "YAML"
    ```yaml
    handlers:
        modules:
            handler_dns_1:
                type: dns
            handler_net_1:
                type: net
    input:
        input_type: pcap
        tap: default_pcap
    kind: collection
    ```
=== "JSON"
    ```json
    {
        "handlers": {
            "modules": {
                "handler_dns_1": {
                    "type": "dns"
                },
                "handler_net_1": {
                    "type": "net"
                }
            }
        },
        "input": {
            "input_type": "pcap",
            "tap": "default_pcap"
        },
        "kind": "collection"
    }
    ```

For a more tailored observability policy to filter on specific traffic or to add (or exclude) specific metrics, please refer to the [Orb Policy Reference](/documentation/orb/advanced_policies/).

### Applying your policy

The final step is to apply your policy by creating a [dataset](/getting_started/#create-a-dataset), which links your agents (through an [agent group](/getting_started/#create-an-agent-group)) with your policy and the [sink](/getting_started/#create-a-sink) where you want to send your metrics.