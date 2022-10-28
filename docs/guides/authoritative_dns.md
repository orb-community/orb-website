This solution guide will walk you through setting up ==Observability for Authoritative DNS Services== using Orb agents. This is a monitoring configuration that can be used when you want to monitor only inbound DNS queries and their responses.

## Configure Observation

The first step is to configure the agents to observe only inbound DNS traffic. This can be done using either the ==PCAP== (Packet Capture) or the ==DNSTAP== taps.

### PCAP tap configuration

Using ==PCAP== (Packet Capture), your agent tap configurations should look something like this:

=== "YAML"
    ```yaml
    taps:
        dns_pcap:
            interface: "visor.module.input/1.0"
            input_type: pcap
            config:
                iface: "auto"
                bpf: '(dst port 53 and dst net 172.16.2.0/24) or (src port 53 and src net 172.16.2.0/24)'
                host_spec: '172.16.2.0/24'
    ```
=== "JSON"
    ```json
    {
        "taps": {
            "dns_pcap": {
                "config": {
                    "iface": "auto",
                    "bpf": "(dst port 53 and dst net 172.16.2.0/24) or (src port 53 and src net 172.16.2.0/24)",
                    "host_spec": "172.16.2.0/24"
                },
                "input_type": "pcap",
                "interface": "visor.module.input/1.0"
            }
        }
    }
    ```

If you want to explicitly specify the interface the PCAP tap should be listening on (instead of using the `auto` selection), please ensure that the `iface` is configured with the appropriate interface name. If the interface defined is not correct, you will need to re-run the agent provisioning command specifying the correct interface using the [`PKTVISOR_PCAP_IFACE_DEFAULT`](/documentation/running_orb_agent#sample-provisioning-commands) environment variable.

## Configure Analysis

The second step is to configure the agents to analyze for DNS traffic. This is done by defining and applying a tailored ==Policy== to the agents.

### Create a Policy

The following is a sample policy that includes the ==NET== and ==DNS== handlers. Your policy should look something like this:

=== "YAML"
    ```yaml
    handlers:
        modules:
            - handler_dns_1:
                config:
                    public_suffix_list: true
                metric_groups:
                    enable:
                        - top_ecs
                type: dns
            - handler_net_1:
                type: net
    input:
        input_type: pcap
        tap: dns_pcap
    kind: collection
    ```
=== "JSON"
    ```json
    "handlers": {
        "modules": [
            {
                "handler_dns_1": {
                    "config": {
                        "public_suffix_list": true
                    },
                    "metric_groups": {
                        "enable": [
                            "top_ecs"
                        ]
                    },
                    "type": "dns"
                }
            },
            {
                "handler_net_1": {
                    "type": "net"
                }
            }
        ]
    },
    "input": {
        "input_type": "pcap",
        "tap": "dns_pcap"
    },
    "kind": "collection"
    ```

For a more tailored DNS policy to filter on specific traffic or to add (or exclude) specific metrics, please refer to the [Orb Policy Reference](/documentation/advanced_policies).

### Applying your policy

The final step is to apply your policy by creating a [Dataset](/getting_started/#create-a-dataset), which links your agents (through an [Agent Group](/getting_started/#create-an-agent-group)) with your policy and the [Sink](/getting_started/#create-a-sink) where you want to send your metrics.
