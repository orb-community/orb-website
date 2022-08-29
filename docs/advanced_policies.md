# Advanced Policies

An Orb policy can be written in either JSON or YAML, and has three top level sections: “input”, “handlers” and “kind”.

```json
{
  "input": {
   
  },
  "handlers": {
    
  },
  "kind": "collection"
}

```
## Input section

The input section specifies what data streams the policy will be using for analysis, in other words, this specifies what data the agent should be listening in on, and is defined at the agent level. <br>
3 types of input are supported: `pcap`, `flow` and `dnstap`. For each input type, specific configuration, filters and tags can be defined.<br><br>
**Required fields:** <br>
`input_type` - the type of input.  This field will be validated with the type of tap indicated by the `tap` parameter or by the `tap selector` . If the types are incompatible, the policy will fail.<br>

`tap` - the name given to this input in the tap/agent configuration  or `tap_selector` -  tags to match existing taps.
If `tap_selector` is used, it can be chosen whether taps with any of the tags or with all tags will be attached.


**Optional fields**: <br>
`filter` - to specify what data to include from the input <br>
`config` -  how the input will be used <br><br>

Every configuration set at the input can be reset at the tap level, with the one set on the tap dominant over the one set on the input.<br>

Default input structure:

```yaml
input:
  tap: tap_name
  input_type: type_of_input
  filter:
    bpf: ...
  config:
    ...
```

or

```yaml
input:
  tap_selector:
    any:
      key1: value1
      key2: value2
  input_type: type_of_input
  filter:
    bpf: ...
  config:
    ...
```

or

```yaml
input:
  tap_selector:
    all:
      key1: value1
      key2: value2
  input_type: type_of_input
  filter:
    bpf: ...
  config:
    ...
```

#### "PCAP"
`pcap_file`: *str* <br>
One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>
When this variable exists, the bpf filter must also be informed. Otherwise, the execution will fail.

```yaml
 pcap_file: "path/to/file"
 ```

`debug`: *bool* <br>

When `true` activate debug logs

```yaml
debug: true
```

`pcap_source`: *str* <br>

`pcap_source` specifies the type of library to use. Default: libpcap. Options: libpcap or af_packet (linux).

```yaml
pcap_source: "af_packet"
```

`iface`: *str* <br>

Name of the interface to bind. If the interface name does not exist, the policy will fail.

```yaml
iface: str
```

Example:

```yaml
iface: "eth0"
```


`host_spec`: *str* <br>
The `host_spec` setting is useful to determine the direction of observed packets, once knowing the host ip, it is possible to determine the data flow direction, ie if they are being sent by the observed host (from host) or received (to host). <br>

```yaml
host_spec: str
```

Example:

```yaml
host_spec: "192.168.0.1/24"
```



**filter**:

`bpf`: *str* <br>

filter data based on Berkeley Packet Filters (BPF).

```yaml
bpf: str
```

Example:

```yaml
bpf: "port 53"
```


#### "FLOW"

`pcap_file` and `port+bind` are mutually exclusive and one of them must exist.


`pcap_file`: *str* <br>

One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>
 
```yaml
 pcap_file: "path/to/file"
 ```

`port`: *int* and `bind`: *str* <br>

The other option for using flow is specifying a port AND an ip to bind (only udp bind is supported). Note that, in this case, both variables must be set.

```yaml
port: int
bind: str
```

Example:

```yaml
port: 6343
bind: "192.168.1.1"
```

`flow_type`: *str* <br>

Default: sflow. options: sflow or netflow (ipfix is supported on netflow). <br><br>

```yaml
flow_type: str
```

Example:

```yaml
flow_type: netflow
```

#### "DNSTAP"
The 3 existing DNSTAP configurations (`dnstap_file`, `socket` and `tcp`) are mutually exclusive, that is, only one can be used in each input and one of them must exist. They are arranged in order of priority. <br>



`dnstap_file`: *str* <br>

One option of using pktvisor is for reading existing network data files. In this case, the path to the file must be passed. This variable is dominant, so if a file is passed, pktvisor will do the entire process based on the file. <br>

 ```yaml
 dnstap_file: "path/to/file"
 ```

`socket`: *str* <br> config

Path to socket file containing port and ip to bind


```yaml
socket: "path/to/file.sock"
```

`tcp`: *str* <br><br>

The other way to inform the ip and port to be monitored is through the 'tcp' configuration. Usage syntax is a string with port:ip (only ipv4 is supported for now). <br>

 ```yaml
 tcp: "ip:port"
 ```

Example:

 ```yaml
 tcp: "192.168.8.2:235"
 ```


**filters**:

`only_hosts`:

`only_hosts` filters data from a specific host.

 ```yaml

 only_hosts: "str"
 ```
Example:

 ```yaml
 only_hosts: "192.168.1.4/32"
 ```


## Handlers section

Handlers are the modules responsible for extracting metrics from inputs and 5 types of handler are supported: `DNS`, `NET`, `DHCP`, `PCAP`, `FLOW`. For each handler type, specific configuration, filters and group of metrics can be defined, and there are also configs (abstract configuration) that can be applied to all handlers: <br><br>
**Abstract Configurations**: <br>

There are general configurations, which can be applied to all handlers. These settings can be reset for each module, within the specific module configs. In this case, the configuration inside the module will override the configuration passed in general handler. <br>

| Abstract Configuration | Type  |     Default      |
|:----------------------:|:-----:|:----------------:|
|   `deep_sample_rate`   | *int* | 100 (per second) |
|     `num_periods`      | *int* |        5         |
|      `topn_count`      | *int* |        10        |


#### deep_sample_rate <br>

`deep_sample_rate` determines the number of data packets that will be analyzed deeply per second. Some metrics are operationally expensive to generate, such as metrics that require string parsing (qname2, qtype, etc.). For this reason, a maximum number of packets per second to be analyzed is determined. If in one second fewer packages than the maximum amount are transacted, all packages will compose the deep metrics sample, if there are more packages than the established one, the value of the variable will be used. Allowed values are in the range [1,100]. Default value is 100. <br>
* If a value less than 1 is passed, the `deep_sample_rate` will be 1. If the value passed is more than 100, `deep_sample_rate` will be 100.


The `deep_sample_rate` filter usage syntax is:<br>
```yaml
num_periods: int
```


#### num_periods <br>

`num_periods` determines the amount of minutes of data that will be available on the metrics endpoint. Allowed values are in the range [2,10]. Default value is 5. <br>

The `num_periods` filter usage syntax is:<br>
```yaml
num_periods: int
```


#### topn_count <br>

`topn_count` sets the maximum amount of elements displayed in top metrics. If there is less quantity than the configured value, the composite metrics will have the existing value. But if there are more metrics than the configured value, the variable will be actively limiting. Any positive integer is valid and the default value is 10. <br>

The `topn_count` filter usage syntax is:<br>
```yaml
num_periods: int
```


Default handler structure:

```yaml
handlers:
  config:
    deep_sample_rate: 100
    num_periods: 5
    topn_count: 10
  modules:
    tap_name:
      type: ...
      config: ...
      filter: ...
      metric_groups:
        enable:
          - ...
        disable:
          - ...
 
 ```

To enable any metric group use the syntax:

```yaml
metric_groups:
  enable:
    - group_to_enable
```

In order to disable any metric group use the syntax:

```yaml
metric_groups:
  disable:
    - group_to_disable
```

* Attention: disabling is dominant over enabling. So if both are passed, the metric will be disabled;


### "DNS"
**Handler Type**: "dns" <br>

**Metrics Group**: <br>

|   Metric Group    | Default  | 
|:-----------------:|:--------:|
|     `top_ecs`     | disabled |
|   `cardinality`   | enabled  |
|    `counters`     | enabled  |
| `dns_transaction` | enabled  |
|   `top_qnames`    | enabled  |
<br>

**Configuration**: <br>
- PublicSuffixList: *bool*. <br>
- Abstract configurations. <br><br>

#### PublicSuffixList <br>

Some names to be resolved by a dns server have public suffixes. These suffixes cause metrics to be generated considering non-relevant data. <br>

The example below illustrates the benefit of using this type of configuration. The qnames consider each part of the name to be resolved. When a name has a public suffix, generic information is generated. Note that in the standard configuration, Qname2 and Qname3 are the same for both domains. With the public suffix setting `true` (which makes the entire public part be considered as a single part), Qname3 already displays relevant information about the name. <br>
The list of suffixes considered public can be accessed [here](https://github.com/ns1labs/pktvisor/blob/develop/src/handlers/dns/PublicSuffixList.h). <br>

|            Name             | Qname2 Standard | Qname3 Standard | Qname2 Public Suffix | Qname3 Public Suffix |
|:---------------------------:|:---------------:|:---------------:|:--------------------:|:--------------------:|
|  `www.imagine.qname.co.uk`  |      co.uk      |   qname.co.uk   |     qname.co.uk      | imagine.qname.co.uk  |
| `other.example.qname.co.uk` |      co.uk      |   qname.co.uk   |     qname.co.uk      | example.qname.co.uk  |


The `PublicSuffixList` filter usage syntax is:<br>
```yaml
PublicSuffixList: true
```



**Filter Options**: <br>

|         Filter         |  Type  | Input  |
|:----------------------:|:------:|:------:|
|      `only_rcode`      | *int*  |  PCAP  |
|   `exclude_noerror`    | *bool* |  PCAP  |
| `only_dnssec_response` | *bool* |  PCAP  |
|     `answer_count`     | *int*  |  PCAP  |
|      `only_qtype`      | *str*  |  PCAP  |
|  `only_qname_suffix`   | *str*  |  PCAP  |
|   `geoloc_notfound`    | *bool* |  PCAP  |
|     `asn_notfound`     | *bool* |  PCAP  |
|   `dnstap_msg_type`    | *str*  | DNSTAP |



#### only_rcode: *int*. <br>

Input: PCAP <br>
When a DNS server returns a response to a query made, one of the properties of the response is the "return code" (rcode), a code that describes what happened to the query that was made. <br>  
Most return codes indicate why the query failed and when the query succeeds, the return is an RCODE:0, whose name is NOERROR. <br>
There are several possible return codes for a DNS server response, which you can access [here](https://help.dnsfilter.com/hc/en-us/articles/4408415850003-DNS-Return-Codes), but currently the policies support 4 return types as filters (if you use any other code that is not in the table below, your policy will fail): <br>

| DNS Return Code | DNS Return Message |                Description                |
|:---------------:|:------------------:|:-----------------------------------------:|
|       `0`       |      NOERROR       |     DNS Query completed successfully      |
|       `2`       |      SERVFAIL      | Server failed to complete the DNS request |
|       `3`       |      NXDOMAIN      |        Domain name does not exist         |
|       `5`       |      REFUSED       |         Function not implemented          |

The `only_rcode` filter usage syntax is:<br>
```yaml
only_rcode: int
```
with the `int` referring to the return code to be filtered. <br>

Example <br>
If you want to filter only successful queries responses you should use (note that all that the query will be discarded and the result will be just the responses): <br>
```yaml
only_rcode: 0
```
Important information is that only one return code is possible for each handler. So, in order to have multiple filters on the same policy, multiple handlers must be created, each with a rcode type;

####  exclude_noerror: *bool* <br>

Input: PCAP <br>

You may still want to filter out only responses with any kind of error. For this, there is the `exclude_noerror` filter, which removes from its results all responses that did not return any type of error.
The `exclude_noerror` filter usage syntax is:<br>
```yaml
exclude_noerror: true
```

Attention: the filter of `exclude_noerror` is dominant in relation to the filter of only_rcode, that is, if the filter of `exclude_noerror` is true, even if the filter of only_rcode is set, the results will be composed only by responses without any type of error (all type of errors will be kept). <br>

####  only_dnssec_response: *bool* <br>

Input: PCAP <br>

When you make a DNS query, the response you get may have a DNSSEC signature, which authenticates that DNS records originate from an authorized sender, thus protecting DNS from falsified information. <br>
To filter only responses signed by an authorized sender, use:
The `only_dnssec_response` filter usage syntax is:<br>
```yaml
only_dnssec_response: true
```

####  answer_count: *int* <br>

Input: PCAP <br>

One of the properties present in the query message structure is `Answer RRs`, which is the count of entries in the responses section (RR stands for “resource record”). <br>
The number of answers in the query is always zero, as a query message has only questions and no answers, and when the server sends the answer to that query, the value is set to the amount of entries in the answers section. <br>

The `answer_count` filter usage syntax is:<br>
```yaml
answer_count: int
```
with the `int` referring to the desired amount of answer. <br>
Note that any value greater than zero that is defined will exclude queries from the results, since in queries the number of answers is always 0. <br>
As the answers count of queries is 0, whenever the value set for the answer_count is 0, both queries and responses will compose the result. <br>


A special case is the concept of `NODATA`, which is one of the possible returns to a query made to a DNS server is known as. This happens when the query is successful (so rcode:0), but there is no data as a response, so the number of answers is 0. <br>
In this case, to have in the results only the cases of `NODATA`, that is, the responses, the filter must be used together with the filter `exclude_noerror`.


Important information is that only one answer_count is possible for each handler. So, in order to have multiple counts on the same policy, multiple handlers must be created, each with an amount of answers;

####  only_qtype: *str* <br>

Input: PCAP <br>

DNS record types are records that provide important information about a hostname or domain. Supported default types can be seen [here](https://github.com/ns1labs/pktvisor/blob/develop/src/handlers/dns/dns.h#L30). <br>

The `only_qtype` filter usage syntax is:<br>
```yaml
only_qtype: array
```

If you want to filter only IPV4 record types, for example, you should use: <br>

```yaml
only_qtype:
  - "A"
```
or

```yaml
only_qtype:
  - 1
```

Multiple types are also supported and both queries and responses that have any of the values in the array will be considered.

```yaml
only_qtype:
  - 1
  - 2 
```

####  only_qname_suffix: *str* <br>

Input: PCAP <br>


The `only_qname_suffix` filters queries and responses whose endings (suffixes) of the names match the strings present in the array. <br>
The `only_qname_suffix` filter usage syntax is:<br>
```yaml
only_qname_suffix:array
```
Examples:
```yaml
only_qname_suffix:
  - .google.com
```

or

```yaml
only_qname_suffix:
  - google.com
  - .nsone.net
```

####  geoloc_notfound: *bool* <br>

Input: PCAP <br>


Based on ECS (EDNS Client Subnet) information, it is possible to determine the geolocation of where the query is being made. When the Subnet refers to a region found in the standard databases, the city, state and country (approximated) are returned. However, when based on the subnet it is not possible to determine the geolocation, a `not found` is returned. <br>
The `geoloc_notfound` filter only keeps responses whose geolocations were not found. <br>
The `geoloc_notfound` filter usage syntax is:<br>
```yaml
geoloc_notfound: true
```


####  asn_notfound: *bool* <br>

Input: PCAP <br>


Based on ECS (EDNS Client Subnet) information, it is possible to determine the ASN (Autonomous System Number). When the IP of the subnet belongs to some not known ASN in the standard databases, a `not found` is returned. <br>
The `asn_notfound` filter only keeps responses whose asn were not found. <br>
The `asn_notfound` filter usage syntax is:<br>
```yaml
asn_notfound: true
```

####  dnstap_msg_type: *str* <br>

Input: DNSTAP <br>

With a dnstap protocol it is possible to know the type of message that must be resolved in the request to the server. This filter therefore allows you to filter by response types.
Supported message types are: `auth`, `resolver`, `client`, `forwarder`, `stub`, `tool` and `update`.
Multiple types are also supported and messages that are any of the values in the array will be considered.

The `dnstap_msg_type` filter usage syntax is:<br>
```yaml
dnstap_msg_type: array
```
Example:

```yaml
dnstap_msg_type:
  - "auth"
  - "resolver"
```


    !!! example "DNS handler section example"
        === "JSON"
              ``` json
                {
                   "handlers":{
                      "modules":{
                         "default_dns":{
                            "filter":{
                               "only_qname_suffix":[
                                  ".orb.live",
                                  ".google.com"
                               ],
                               "only_rcode":3
                            },
                            "configs":{
                               "public_suffix_list":"true"
                            },
                            "metric_groups": {
                                "enable": [
                                  "top_ecs"
                                ]
                              },
                            "type":"dns"
                         }
                      }
                   }
                }
              ```
        === "YAML"
            ``` yaml
            ---
            handlers:
              modules:
                default_dns:
                  filter:
                    only_qname_suffix:
                    - ".orb.live"
                    - ".google.com"
                    only_rcode: 3
                  configs:
                    public_suffix_list: true
                  metric_groups:
                    enable:
                    - top_ips
                  type: dns
            ```


### "NET"
**Handler Type**: "net" <br>

**Metrics Group**: <br>

| Metric Group  | Default | 
|:-------------:|:-------:|
| `cardinality` | enabled |
|  `counters`   | enabled |
|   `top_geo`   | enabled |
|   `top_ips`   | enabled |
<br>

**Configuration**: <br>
- Abstract configurations. <br><br>
**Filter Options**: <br>

|        Filter        |  Type  |    Input     |
|:--------------------:|:------:|:------------:|
|  `geoloc_notfound`   | *bool* | PCAP, DNSTAP |
|    `asn_notfound`    | *bool* | PCAP, DNSTAP |
| `only_geoloc_prefix` | *str*  | PCAP, DNSTAP |
|  `only_asn_number`   | *str*  | PCAP, DNSTAP |


####  geoloc_notfound: *bool* <br>

Input: PCAP <br>

The source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. When the IPs refer to a region found in the standard databases, the city, state and country (approximated) are returned. However, when it is not possible to determine the IP geolocation, a `not found` is returned. <br>

The `geoloc_notfound` filter usage syntax is:<br>
```yaml
geoloc_notfound: true
```

####  asn_notfound: *bool* <br>

Input: PCAP <br>

Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). When the IP of the source or destination belongs to some not known ASN in the standard databases, a `not found` is returned. <br>

The `asn_notfound` filter usage syntax is:<br>
```yaml
asn_notfound: true
```

####  only_geoloc_prefix: *bool* <br>

Input: PCAP <br>

Source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. In this way it is possible to filter the data considering the geolocation using the filter `only_geoloc_prefix`. <br> 

The `only_geoloc_prefix` filter usage syntax is:<br>
```yaml
only_geoloc_prefix: array
```
Example:

```yaml
only_geoloc_prefix:
  - BR
  - US/CA
```

####  only_asn_number: *bool* <br>

Input: PCAP <br>

Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). In this way it is possible to filter the data considering a specific ASN using the filter `only_asn_number`. <br>

The `only_asn_number` filter usage syntax is:<br>
```yaml
only_asn_number: array
```
Example:

```yaml
only_asn_number:
  - 7326
  - 16136
```

!!! example "NET handler section example"
#todo Insert examples

### "DHCP"
**Handler Type**: "dhcp" <br>

**Metrics Group**: No metrics group available<br>

**Configuration**: <br>
- Only abstract configurations. <br><br>

**Filter Options**: No filters available. <br><br>


!!! example "DHCP handler section example"
#todo Insert examples

### "PCAP"
**Handler Type**: "pcap" <br>

**Metrics Group**: No metrics group available<br>

**Configuration**: <br>
- Abstract configurations. <br><br>

**Filter Options**: No filters available. <br><br>


!!! example "PCAP handler section example"
#todo Insert examples


### "FLOW"
**Handler Type**: "flow" <br>

**Metrics Group**: <br>

|   Metric Group   | Default | 
|:----------------:|:-------:|
|  `cardinality`   | enabled |
|    `counters`    | enabled |
|    `top_geo`     | enabled |
| `top_by_packets` | enabled |
|  `top_by_bytes`  | enabled |
<br>

**Configuration**: <br>
- sample_rate_scaling: *bool* <br>
- recorded_stream: <br> #todo
- Abstract configurations. <br><br>

#### sample_rate_scaling

By default, flow metrics are generated by an approximation based on sampling the data. 1 packet every N is analyzed and the prediction of the entire population is made from the sample. If you want to see exactly all the exact data, you can disable `sample_rate_scaling`. <br>

The `sample_rate_scaling` filter usage syntax is:<br>
```yaml
sample_rate_scaling: false
```

**Filter Options**: <br>


|      Filter       |  Type  | Input |
|:-----------------:|:------:|:-----:|
|  `only_devices`   | *str*  | FLOW  |
|    `only_ips`     | *str*  | FLOW  |
|   `only_ports`    | *str*  | FLOW  |
| `only_interfaces` | *str*  | FLOW  |
| `geoloc_notfound` | *bool* | FLOW  |
|  `asn_notfound`   | *bool* | FLOW  |


#### only_devices: *str* <br>

Input: FLOW <br>

Considering a flow traffic, the same port can be monitoring data from different routers or/and switches. If you want to filter the flow data coming from a specific device, the `only_devices` filter should be used. CIDR (Inter-Domain Routing Classes) ranges are supported on this filter.<br>
The difference between `only_devices` and `only_ips` is that while this one filters the ids of the device that is sending the data, the other filters the ips that are communicating with each other (source or destination). <br>

The `only_devices` filter usage syntax is:<br>
```yaml
only_devices: array
```

Example:
```yaml
only_devices:
  - 216.239.38.10/24
  - 192.158.1.38/32
```

#### only_ips: *str* <br>

Input: FLOW <br>

To filter data only from certain source OR destination, you can use `only_ips` filter, for which CIDR (Inter-Domain Routing Classes) ranges are supported. <br>

The `only_ips` filter usage syntax is:<br>
```yaml
only_ips: array
```

Example:
```yaml
only_ips:
  - 192.168.1.1/24
  - 192.158.1.38/32
```

#### only_ports: *str* <br>

Input: FLOW <br>

`only_ports` filter only filters data being sent to or received on one of the selected IP ports (or range of ports). <br>

The `only_ports` filter usage syntax is:<br>
```yaml
only_ports: array
```

Example:
```yaml
only_devices:
  - 10853 #port can be passed as int
  - "10854" #port can be passed as str
  - 10860-10890 #range from 10860 to 10890. All ports in this interval will be accepted

```

#### only_interfaces: *str* <br>

Input: FLOW <br>

`only_interfaces` filters data by device ports (not IP ports). In this way, it is possible to filter data referring to a specific type of network connected to the corresponding device port. <br>

The `only_interfaces` filter usage syntax is:<br>
```yaml
only_interfaces: array
```

Example:
```yaml
only_interfaces:
  - 10 #port can be passed as int
  - "11" #port can be passed as str
  - 12-16 #range from 12 to 16. All ports in this interval will be accepted

```

####  geoloc_notfound: *bool* <br>

Input: FLOW <br>

The source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. When the IPs refer to a region found in the standard databases, the city, state and country (approximated) are returned. However, when it is not possible to determine the IP geolocation, a `not found` is returned. <br>

The `geoloc_notfound` filter usage syntax is:<br>
```yaml
geoloc_notfound: true
```

####  asn_notfound: *bool* <br>

Input: FLOW <br>


Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). When the IP of the source or destination belongs to some not known ASN in the standard databases, a `not found` is returned. <br>

The `asn_notfound` filter usage syntax is:<br>
```yaml
asn_notfound: true
```


  !!! example "FLOW handler section example"
  #todo Insert examples
