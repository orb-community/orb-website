## Configuring Observability for DHCP Services

### Overview

This solution guide will walk you through setting up monitoring for the DHCP services.

### Agent Configuration

The default **PCAP** tap configuration is all that is necessary for DHCP monitoring. Your agent tap configurations should look something like this:

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
    {
        "taps": {
            "default_pcap": {
                "config": {
                    "iface": "eth0",
                },
                "input_type": "pcap",
                "interface": "visor.module.input/1.0"
            }
        }
    }
    ```

Please be sure that the `iface` and `pcap_source` are configured with the appropriate interface name on the node where the agent is running. If the interface defined is not correct, you will need to re-run the agent provisioning command specifying the correct interface using the [`PKTVISOR_PCAP_IFACE_DEFAULT`](documentation/orb/running_orb_agent.md#sample-provisioning-commands) environment variable.

### Policy

The default policy created using the [policy wizard](/getting_started/#create-a-policy) should be sufficient for DHCP monitoring. Please be sure to add the **NET** and **DHCP** handlers to the policy. Your policy should look something like this:

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
    "handlers": {
        "modules": {
            "handler_dhcp_1": {
                "type": "dhcp"
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
    ```

For a more tailored DHCP policy to filter on specific traffic or to add (or exclude) specific metrics, please refer to the [Orb Policy Reference](/documentation/orb/advanced_policies/).

### Applying your policy

The final step is to apply your policy by creating a [dataset](/getting_started/#create-a-dataset), which links your agents (through an [agent group](/getting_started/#create-an-agent-group)) with your policy and the [sink](/getting_started/#create-a-sink) where you want to send your metrics.