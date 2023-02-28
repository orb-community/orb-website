# Configure an Orb Agent Using Configuration File

## Orb Agent Configuration

As you can see [here](/documentation/running_orb_agent/#configuration-files) configuration files are needed if you want to customize your orb agent. Follow the lines below to understand how to use them and make Orb more versatile and powerful to your needs.
The configuration file is written in YAML and can be used to make Orb agent to auto-provision or to configure an already connected Orb agent. 


=== "Configuration File to Orb agent to auto-provision"
    ```yaml
    orb:
      cloud:
        api:
          address: https://orb.live
          token: TOKEN
        config:
          agent_name: agent_name
          auto_provision: true
        mqtt:
          address: tls://agents.orb.live:8883
      backends:
        pktvisor:
          api_port: 10853
          binary: /usr/local/sbin/pktvisord
          config_file: "/path/pktvisor.yaml"
      tags:
        key_1: value_1
        key_2: value_2
      tls:
        verify: true
      db:
        file: "/orb-agent.db"
      debug:
        enable: true
    visor:
      # this section is used by pktvisor. Check Pktvisor configuration to more information.
    ```
=== "Configuration File to Orb agents already connected"
    ```yaml
    orb:
      cloud:
        api:
          address: https://orb.live
        config:
          auto_provision: false
        mqtt:
          address: tls://agents.orb.live:8883
          channel_id: "AGENT_KEY_UUID"
          id: "AGENT_UUID"
          key: "AGENT_KEY_UUID"
      backends:
        pktvisor:
          api_port: 10853
          binary: /usr/local/sbin/pktvisord
          config_file: "/path/pktvisor.yaml"
      tags:
        key_1: value_1
        key_2: value_2
      tls:
        verify: true
      db:
        file: "/orb-agent.db"
      debug:
        enable: true
    visor:
     # this section is used by pktvisor. Check Pktvisor configuration to more information.
    ```

### Cloud

Cloud section has three subsections: `api`, `config` and `mqtt` and it establishes all the necessary configurations for the agent connection.


| Subsection |          Configurations           |  Type  |                           Required                            |          Default           |                                                   Extra                                                   |
|:----------:|:---------------------------------:|:------:|:-------------------------------------------------------------:|:--------------------------:|:---------------------------------------------------------------------------------------------------------:|
|    api     |             `address`             | *str*  |                               ❌                               |      https://orb.live      |                                                     -                                                     |
|    api     |              `token`              | *str*  |     It's required if you want the agent to auto provision     |            None            | Check [here](/documentation/running_orb_agent/#advanced-auto-provisioning-setup) to how to create a token |
|   config   |           `agent_name`            | *str*  |                               ❌                               |          hostname          |                               It will only be used if auto_provision: True                                |
|   config   | <nobr>  `auto_provision`  </nobr> | *Bool* |                               ❌                               |            True            |                                                     -                                                     |
|    mqtt    |             `address`             | *str*  |                               ❌                               | tls://agents.orb.live:8883 |                                                     -                                                     |
|    mqtt    |               `id`                | *UUID* | It's required if you *DON'T* want the agent to auto provision |            None            |       Check [here](/getting_started/#create-agent-credentials) to understand how to get an agent id       |
|    mqtt    |           `channel_id`            | *UUID* | It's required if you *DON'T* want the agent to auto provision |            None            |       Check [here](/getting_started/#create-agent-credentials) to understand how to get an agent id       |
|    mqtt    |               `key`               | *UUID* | It's required if you *DON'T* want the agent to auto provision |            None            |       Check [here](/getting_started/#create-agent-credentials) to understand how to get an agent id       |


### Backends

It is worth emphasizing that no extra installation is necessary, since the `orb-agent` docker container already includes the available backends.
The subsections here are related to each backend supported by Orb agents. See below for configuration options:

***Pktvisor:***<br>

Check [pktvisor configurations](/documentation/orb_agent_configs/#pktvisor-configuration) for more information on available options to this backend.

| Subsection | Configurations | Type  | Required |          Default          |                                                                                                                                       <div style="width:500px">Meaning</div>                                                                                                                                       |
|:----------:|:--------------:|:-----:|:--------:|:-------------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|  pktvisor  |    `binary`    | *str* |    ❌     | /usr/local/sbin/pktvisord |                                                                                                                    It specifies the path on orb container where the pktvisor binary is located                                                                                                                     |
|  pktvisor  | `config_file`  | *str* |    ❌     |    /opt/orb/agent.yaml    | It specifies the path in orb container where the pktvisor configuration file is located and assumes that you are using the same agent file, named agent.yaml, to configure both the agent itself and pktvisor. If your pktvisor configuration file has another name/path, you must replace it with the proper one. |
|  pktvisor  |   `api_host`   | *str* |    ❌     |         localhost         |                                                                                                                                      Host where pktvisor web server will run.                                                                                                                                      |
|  pktvisor  |   `api_port`   | *int* |    ❌     |           10853           |                                                                                                                                    Port in which pktvisor web server will run.                                                                                                                                     |


### Tags
- Tags are intended to dynamically define a group of agents by matching against agent group tags and are a dict type.
  - !!! warning
    Agent tags defined on the edge (configuration files) *CAN NOT* be removed/edited through API or orb-website and they are not required.

### TLS verification

It verifies the identity of the server and must be set to False if you want to use self-hosted versions.

- *Not Required* <br>

- *Default:* True <br>
- *Type:* Bool

### Debug

If `True`, this will cause more agent logs (debug type) to be provided.

- *Not Required* <br>

- *Default:* False <br>
- *Type:* Bool


## Pktvisor Configuration

For configure pktvisor you could specify `taps`. It's defined under `visor` top level key ([check an example](https://raw.githubusercontent.com/ns1labs/orb/develop/cmd/agent/agent.example.yaml)).<br>
The tap section specifies what data the agent should be listening in on and the goal of Taps is to abstract away host level details such as ethernet interface or dnstap socket location so that collection policies can apply to a broad set of pktvisor agents without worrying about these details. See [here](https://github.com/orb-community/pktvisor/blob/develop/RFCs/2021-04-16-75-taps.md) for more information. <br>
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

The following inputs are supported: `pcap`, `flow`, `dnstap` and `netprobe`. For each input type, specific configuration, filters and tags can be defined.<br><br>

### Packet Capture (pcap)

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
<br>

#### Configurations <a name="input_pcap_configurations"></a><br>

There are 5 configurations for pcap input: `pcap_file`, `pcap_source`, `iface`, `host_spec` and `debug`.

|                 Config                 | Type |
|:--------------------------------------:|:-----|
|   [pcap_file](#pcap_file_input_pcap)   | str  |
| [pcap_source](#pcap_source_input_pcap) | str  |
|    [iface](#pcap_source_input_pcap)    | str  |
|   [host_spec](#host_spec_input_pcap)   | str  |
|       [debug](#debug_input_pcap)       | bool |

**pcap_file**: *str* <a name="pcap_file_input_pcap"></a><br>
<font size="1">[Back to pcap configurations list](#input_pcap_configurations)</font>

One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>

=== "YAML"
```yaml
pcap_file: "path/to/file"
```

<br>

**pcap_source**: *str* <a name="pcap_source_input_pcap"></a><br>
<font size="1">[Back to pcap configurations list](#input_pcap_configurations)</font>

`pcap_source` specifies the type of library to use. Default: libpcap. Options: libpcap or af_packet (linux).

=== "YAML"
```yaml
pcap_source: "af_packet"
```

<br>

**iface**: *str* <a name="iface_input_pcap"></a><br>
<font size="1">[Back to pcap configurations list](#input_pcap_configurations)</font>

Name of the interface to bind.

=== "YAML"
```yaml
iface: str
```    
Example:    
```yaml
iface: eth0
```


!!! tip

    You can use `auto` as iface. In this way, the network with the highest data flow will be analyzed.
    === "YAML"
    ```yaml
    iface: auto
    ```



**host_spec**: *str* <a name="host_spec_input_pcap"></a><br>
<font size="1">[Back to pcap configurations list](#input_pcap_configurations)</font>
The `host_spec` setting is useful to determine the direction of observed packets, once knowing the host ip, it is possible to determine the data flow direction, ie if they are being sent by the observed host (from host) or received (to host). <br>

=== "YAML"
```yaml
host_spec: str
```
Example:
```yaml
host_spec: "192.168.0.1/24"
```

<br>

**debug**: *bool* <a name="debug_input_pcap"></a><br>
<font size="1">[Back to pcap configurations list](#input_pcap_configurations)</font>

When `true` activate debug logs

=== "YAML"
```yaml
debug: true
```


#### Filters <a name="input_pcap_filters"></a><br>


|          Filter          | Type |
|:------------------------:|:-----|
| [`bpf`](#bpf_input_pcap) | str  |

**bpf:** *str* <a name="bpf_input_pcap"></a><br>
<font size="1">[Back to pcap filters list](#input_pcap_filters)</font>

`bpf` filter data based on Berkeley Packet Filters (BPF).

=== "YAML"
```yaml
bpf: str
```
Example:
```yaml
bpf: "port 53"
```

### sFlow/Netflow (flow)

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
<br>

#### Configurations <a name="input_flow_configurations"></a><br>

There are 3 configs for flow inputs: `port`, `bind` and `flow_type`.

|               Config               | Type |
|:----------------------------------:|:-----|
|   [port](#port_bind_input_flow)    | int  |
|   [bind](#port_bind_input_flow)    | str  |
| [flow_type](#flow_type_input_flow) | str  |



**port**: *int* and **bind**: *str* <a name="port_bind_input_flow"></a><br>
<font size="1">[Back to sFlow/Netflow filters list](#input_flow_configurations)</font>

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

**flow_type**: *str* <a name="flow_type_input_flow"></a><br>
<font size="1">[Back to sFlow/Netflow filters list](#input_flow_configurations)</font>

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

###  dnstap

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
<br>

#### Configurations <a name="input_dnstap_configurations"></a><br>

The 3 existing DNSTAP configurations (`dnstap_file`, `socket` and `tcp`) are mutually exclusive, that is, only one can be used in each input and one of them must exist. They are arranged in order of priority. <br>

|                  Config                  | Type |
|:----------------------------------------:|:-----|
| [dnstap_file](#dnstap_file_input_dnstap) | str  |
|      [socket](#socket_input_dnstap)      | int  |
|         [tcp](#tcp_input_dnstap)         | str  |

**dnstap_file**: *str* <a name="dnstap_file_input_dnstap"></a><br>
<font size="1">[Back to dnstap configurations list](#input_dnstap_configurations)</font>

One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>

=== "YAML"
```yaml
dnstap_file: path/to/file
```

<br>

**socket**: *str* <a name="socket_input_dnstap"></a><br>
<font size="1">[Back to dnstap configurations list](#input_dnstap_configurations)</font>

Path to socket file containing port and ip to bind

=== "YAML"
```yaml
socket: path/to/file.sock
```

<br>

**tcp**: *str* <a name="tcp_input_dnstap"></a><br>
<font size="1">[Back to dnstap configurations list](#input_dnstap_configurations)</font>

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

#### Filters <a name="input_dnstap_filters"></a><br>


|                  Filter                  | Type |
|:----------------------------------------:|:-----|
| [`only_hosts`](#only_hosts_input_dnstap) | str  |

**only_hosts**: *str* <a name="only_hosts_input_dnstap"></a><br>
<font size="1">[Back to dnstap filters list](#input_dnstap_filters)</font>

`only_hosts` filters data from a specific host.

=== "YAML"
```yaml    
only_hosts: str
```
Example:
```yaml
only_hosts: 192.168.1.4/32
```


### Netprobe

!!! Example "Example: Pktvisor Netprobe Tap Configuration"
    ```yaml
    visor:
      taps:
        default_netprobe:
          input_type: netprobe
          config:
            test_type: ping
            interval_msec: 2000
            timeout_msec: 1000
            packets_per_test: 10
            packets_interval_msec: 25
            packet_payload_size: 56
            targets:
              www.google.com:
                target: www.google.com
              orb_community:
                target: orb.community
          tags:
            netprobe: true
    ```
<br>

#### Configurations <a name="input_netprobe_configurations"></a><br>

The following configs are available for netprobe inputs: `test_type`, `interval_msec`, `timeout_msec`, `packets_per_test`, `packets_interval_msec`, `packet_payload_size`. 

|                             Config                             | Type | Required | Default |
|:--------------------------------------------------------------:|:----:|:--------:|:-------:|
|             [test_type](#test_type_input_netprobe)             | str  |    ✅     |    -    |
|         [interval_msec](#interval_msec_input_netprobe)         | int  |    ❌     |  5000   |
|         [timeout_msec](#interval_msec_input_netprobe)          | int  |    ❌     |  2000   |
|      [packets_per_test](#packets_per_test_input_netprobe)      | int  |    ❌     |    1    |
| [packets_interval_msec](#packets_interval_msec_input_netprobe) | int  |    ❌     |   25    |
|   [packet_payload_size](#packet_payload_size_input_netprobe)   | int  |    ❌     |   48    |
|               [targets](#targets_input_netprobe)               | map  |    ✅     |    -    |



**test_type**: *str* <a name="test_type_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

Defines the type of the test to be performed. Type options are listed below:

- ping: implements a ping prober that can probe multiple targets. The test will run against the targets to verify if the systems are working fine.

=== "YAML"
```yaml
test_type: str
```    
Example:
```yaml
test_type: ping
```

<br>

**interval_msec**: *int* <a name="interval_msec_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

How often to run the probe (in milliseconds). <br><br>

=== "YAML"
```yaml
interval_msec: int
```
Example:
```yaml
interval_msec: 5000
```

<br>

**timeout_msec**: *int* <a name="timeout_msec_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

Probe timeout (in milliseconds). <br><br>

=== "YAML"
```yaml
timeout_msec: int
```
Example:
```yaml
timeout_msec: 2000
```

<br>

**packets_per_test**: *int* <a name="packets_per_test_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

Number of packets to be sent in each test.

=== "YAML"
```yaml
packets_per_test: int
```
Example:
```yaml
packets_per_test: 1
```

<br>

**packets_interval_msec**: *int* <a name="packets_interval_msec_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

Time interval between packets per test (in milliseconds).


=== "YAML"
```yaml
packets_interval_msec: int
```
Example:
```yaml
packets_interval_msec: 25
```

<br>

**packet_payload_size**: *int* <a name="packet_payload_size_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

Defines the payload of the packets sent in the tests.


=== "YAML"
```yaml
packet_payload_size: int
```
Example:
```yaml
packet_payload_size: 48
```

<br>

**targets**: *map* <a name="targets_input_netprobe"></a><br>
<font size="1">[Back to netprobe configurations list](#input_netprobe_configurations)</font>

Here, the targets against which the probe will run are defined.
For each target is required to specify the target name and the address to be tested.

=== "YAML"
```yaml
targets: map
```
Example:
```yaml
targets:
  target_name:
    target: ipv4 address to test
```
Generic Example:
```yaml
targets:
  google:
    target: www.google.com
```

#### Filters

There are no specific filters for Netprobe input.<br><br>
