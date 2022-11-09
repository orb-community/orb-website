This solution guide will walk you through setting up ==Observability for DHCP Services== using Orb agents. This is a monitoring configuration that can be used when monitoring of DHCP services is required.

## Configure Observation

The first step is to configure what data the agents should be observing. This is done by defining and configuring what ==Tap== the agents should be listenining on.

The default ==PCAP== (Packet Capture) tap configuration is all that is necessary for DHCP monitoring. Your agent tap configurations should look something like this:

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
    {
        "taps": {
            "default_pcap": {
                "config": {
                    "iface": "auto",
                },
                "input_type": "pcap",
                "interface": "visor.module.input/1.0"
            }
        }
    }
    ```

If you want to explicitely specify the interface the PCAP tap should be listening on (instead of using the `auto` selection), please ensure that the `iface` is configured with the appropriate interface name. If the interface defined is not correct, you will need to re-run the agent provisioning command specifying the correct interface using the [`PKTVISOR_PCAP_IFACE_DEFAULT`](/documentation/running_orb_agent#sample-provisioning-commands) environment variable.

## Configure Analysis

The second step is to configure the agents to analyze for DHCP traffic. This is done by defining and applying a tailored ==Policy== to the agents.

### Create a Policy

The following is a sample policy that includes the ==NET== and ==DHCP== handlers, as well as a BPF filter to restrict analyzed data to DHCP traffic. Your policy should look something like this:

=== "YAML"
    ```yaml
    handlers:
        modules:
            handler_dns_1:
                type: dhcp
            handler_net_1:
                type: net
    input:
        input_type: pcap
        tap: default_pcap
        filter:
            bpf: 'port 67 or port 68'
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
        "tap": "default_pcap",
        "filter": {
            "bpf": "port 67 or port 68"
        }
    },
    "kind": "collection"
    ```

For a more tailored DHCP policy to filter on specific traffic or to add (or exclude) specific metrics, please refer to the [Orb Policy Reference](/documentation/advanced_policies).

### Applying your policy

The final step is to apply your policy by creating a [Dataset](/getting_started/#create-a-dataset), which links your agents (through an [Agent Group](/getting_started/#create-an-agent-group)) with your policy and the [Sink](/getting_started/#create-a-sink) where you want to send your metrics.