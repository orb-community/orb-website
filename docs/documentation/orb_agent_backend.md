An orb agent can have its backend customized with advanced configuration using [configuration files](/documentation/running_orb_agent/#configuration-files).<br>
It is worth emphasizing that no extra installation is necessary, since the `orb-agent` docker container already includes the available backends.
See below for configuration options for each backend supported by Orb agents.


## Pktvisor Configuration

For configure pktvisor you could specify `taps`. It's defined under `visor` top level key ([check an example](https://raw.githubusercontent.com/ns1labs/orb/develop/cmd/agent/agent.example.yaml)).<br>
The tap section specifies what data the agent should be listening in on and the goal of Taps is to abstract away host level details such as ethernet interface or dnstap socket location so that collection policies can apply to a broad set of pktvisor agents without worrying about these details. See [here](https://github.com/ns1labs/pktvisor/blob/develop/RFCs/2021-04-16-75-taps.md) for more information. <br>
Single or multiple taps can be configured in the same agent.

=== "Pktvisor Configuration Structure"
```yaml
visor:
  taps:
    first_tap_name:
      input_type: type
      config: ...
      filter: ...
      tags:
        key1: value1
        key2: value2
    second_tap_name:
      input_type: type
      config: ...
      filter: ...
      tags:
        key1: value1
        key3: value3
```

The following inputs are supported: `pcap`, `flow` and `dnstap`. For each input type, specific configuration, filters and tags can be defined.<br><br>

### Packet Capture (pcap)

#### Configurations

There are 5 configurations for pcap input: `pcap_file`, `pcap_source`, `iface`, `host_spec` and `debug`.

|   Config    | Type |
|:-----------:|:-----|
|  pcap_file  | str  |
| pcap_source | str  |
|    iface    | str  |
|  host_spec  | str  |
|    debug    | bool |

`pcap_file`: *str* <br>
One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>

=== "YAML"
```yaml
pcap_file: "path/to/file"
```

<br>
`debug`: *bool* <br>

When `true` activate debug logs

=== "YAML"
```yaml
debug: true
```

<br>
`pcap_source`: *str* <br>

`pcap_source` specifies the type of library to use. Default: libpcap. Options: libpcap or af_packet (linux).

=== "YAML"
```yaml
pcap_source: "af_packet"
```

<br>
`iface`: *str* <br>

Name of the interface to bind.

=== "YAML"
```yaml
iface: str
```    
Example:    
```yaml
iface: eth0
```

<br>
`host_spec`: *str* <br>
The `host_spec` setting is useful to determine the direction of observed packets, once knowing the host ip, it is possible to determine the data flow direction, ie if they are being sent by the observed host (from host) or received (to host). <br>

=== "YAML"
```yaml
host_spec: str
```
Example:
```yaml
host_spec: "192.168.0.1/24"
```

#### Filters

There is only one filter referring to the input PCAP: `bpf`.

<br>
`bpf`: *str* <br>

filter data based on Berkeley Packet Filters (BPF).

=== "YAML"
```yaml
bpf: str
```
Example:
```yaml
bpf: "port 53"
```
<br>

PCAP tap example:

!!! Example "Example: Pktvisor PCAP Tap Configuration"
    ```yaml
    visor:
      taps:
        my_pcap_tap:
          input_type: pcap
          config:
            pcap_source: "libpcap"
            debug: true
            iface: auto
            host_spec: "192.168.0.1/24"
          filter:
            bpf: "port 53"
          tags:
            pcap: true
    ```

### Sflow/Netflow (flow)

#### Configurations

There are 4 configs for flow inputs: `port`, `bind` and `flow_type`. `pcap_file` and `port+bind` are mutually exclusive and one of them must exist.

|  Config   | Type |
|:---------:|:-----|
|   port    | int  |
|   bind    | str  |
| flow_type | str  |



`port`: *int* and `bind`: *str* <br>

The other option for using flow is specifying a port AND an ip to bind (only udp bind is supported). Note that, in this case, both variables must be set.

=== "YAML"
```yaml
port: int
bind: str
```    
Example:    
```yaml
port: 6343
bind: 192.168.1.1
```

<br>
`flow_type`: *str* <br>

Default: sflow. options: sflow or netflow (ipfix is supported on netflow). <br><br>

=== "YAML"
```yaml
flow_type: str
```
Example:
```yaml
flow_type: netflow
```

#### Filters

There are no specific filters for the FLOW input.<br><br>

FLOW tap example:

!!! Example "Example: Pktvisor FLOW Tap Configuration"
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

###  Dnstap

#### Configurations

The 3 existing DNSTAP configurations (`dnstap_file`, `socket` and `tcp`) are mutually exclusive, that is, only one can be used in each input and one of them must exist. They are arranged in order of priority. <br>

|   Config    | Type |
|:-----------:|:-----|
| dnstap_file | str  |
|   socket    | int  |
|     tcp     | str  |

`dnstap_file`: *str* <br>

One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>

=== "YAML"
```yaml
dnstap_file: path/to/file
```

<br>
`socket`: *str* <br>

Path to socket file containing port and ip to bind

=== "YAML"
```yaml
socket: path/to/file.sock
```

<br>
`tcp`: *str* <br>

The other way to inform the ip and port to be monitored is through the 'tcp' configuration. Usage syntax is a string with port:ip (only ipv4 is supported for now). <br>

=== "YAML"
```yaml
tcp: ip:port
```
Example:
```yaml
tcp: 192.168.8.2:235
```

<br>
#### Filters

`only_hosts`: *str* <br>

`only_hosts` filters data from a specific host.

=== "YAML"
```yaml    
only_hosts: str
```
Example:
```yaml
only_hosts: 192.168.1.4/32
```
<br><br>

DNSTAP tap example:

!!! Example "Example: Pktvisor DNSTAP Tap Configuration"
    ```yaml
    visor:
      taps:
        my_dnstap_tap:
          input_type: dnstap
          config:
            socket: path/to/file.sock
            tcp: 192.168.8.2:235
          filter:
            only_hosts: 192.168.1.4/32
          tags:
            dnstap: true
    ```
