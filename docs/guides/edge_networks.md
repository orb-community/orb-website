This solution guide will walk you through setting up ==Observability for Edge Networks== using Orb agents. This is a general purpose monitoring configuration that can be used wherever general observability of the network is required.

## Configure Observation

The first step is to configure what data the agents should be observing. This is done by defining and configuring what ==Tap== the agents should be listenining on.

For this use case, the default ==PCAP== (Packet Capture) tap configuration is all that is necessary for broad monitoring. Your agent tap configurations should look something like this:

=== "YAML"
    ```yaml
    taps:
        default_pcap:
            interface: "visor.module.input/1.0"
            input_type: pcap
            config:
                iface: "auto"
    ```
=== "JSON"
    ```json
    "taps": {
        "default_pcap": {
            "config": {
                "iface": "auto",
            },
            "input_type": "pcap",
            "interface": "visor.module.input/1.0"
        }
    }
    ```

If you want to explicitely specify the interface the PCAP tap should be listening on (instead of using the `auto` selection), please ensure that the `iface` is configured with the appropriate interface name. If the interface defined is not correct, you will need to re-run the agent provisioning command specifying the correct interface using the [`PKTVISOR_PCAP_IFACE_DEFAULT`](/documentation/running_orb_agent#sample-provisioning-commands) environment variable.

!!! tip
    This monitoring configuration presumes that the Orb agents have full visibility on the network traffic of interest. This can be accomplished by mirroring the traffic to the configured interface or by configuring the interface to be in promiscuous mode.

## Configure Analysis

The second step is to configure what analysis the agents should be doing on the data they are observing. This is done by defining and applying a ==Policy== to the agents.

### Create a Policy

The default policy created using the [policy wizard](/getting_started/#create-a-policy) is all that is necessary for broad monitoring. Please be sure to add the desired handlers to the policy, such as the ==NET==, ==DNS== and ==DHCP== handlers. Your policy should look something like this:

=== "YAML"
    ```yaml
    handlers:
        modules:
            handler_dns:
                type: dns
            handler_dhcp:
                type: dhcp
            handler_net:
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
                "handler_dns": {
                    "type": "dns"
                },
                "handler_dhcp": {
                    "type": "dhcp"
                },
                "handler_net": {
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

For a more tailored observability policy to filter on specific traffic or to add (or exclude) specific metrics, please refer to the [Orb Policy Reference](/documentation/advanced_policies).

### Apply your policy

The final step is to apply your policy by creating a [Dataset](/getting_started/#create-a-dataset), which links your agents (through an [Agent Group](/getting_started/#create-an-agent-group)) with your policy and the [Sink](/getting_started/#create-a-sink) where you want to send your metrics.