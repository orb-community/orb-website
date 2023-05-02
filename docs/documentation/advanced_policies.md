# Orb Policy Reference

The policy model has seven top level sections: `name`, `desciption`, `tags`, `backend`, `schema_version`, `policy` and `format`.

=== "JSON"
    ```json
    {
        "name": "",
        "description" : "",
        "tags": {},
        "backend": "",
        "schema_version": "",
        "format": "",
        "policy": {}
    }
    ```

| Policy Sections  | Default | Required |
|:----------------:|:-------:|:--------:|
|      `name`      |    -    |    ✅     |
|    `backend`     |    -    |    ✅     |
|     `policy`     |    -    |    ✅     |
|  `description`   | *None*  |    ❌     |
|      `tags`      | *None*  |    ❌     |
| `schema_version` |  *1.0*  |    ❌     |
|     `format`     | *json*  |    ❌     |


- **Name**<br><br>
Policy name must be unique, must start with a letter and contain only letters, numbers, `"-"` or `"_"`.

!!! example "Examples"

    !!! success

        "name": "my_policy" <br>
        "name": "MY-policy_2
    
    !!! failure
        "name": "1_policy" <br>
        "name": "MY-policy/2

- **Description**<br><br>
Policy description is a free string. You can describe using spaces, letters, numbers and special characters.<br><br>


- **Tags**<br><br>
Tags are intended to facilitate organization and are a dict type. 

The `tags` usage syntax is:<br>

=== "JSON"
    ```json
        "tags": {"key1":"value1", "key2":"value2", "key3":"value3"}
    ```

- **Backend**<br><br>
Backend determine to which backend the policy will be attached. Check the available backends [here](/documentation/orb_agent_configs/#backends). <br><br>

- **Schema version**<br><br>
Each backend supported on Orb must have a policy schema to be validated, parsed and applied. `schema_version` is the field responsible for allowing different schema versions and backward compatibility. Default should be "1.0".<br><br>

- **Format**<br><br>
The `format` specifies in which format the policy data will be written. The options are `json` and `yaml`. `json` is the default value.<br><br>

- **Policy**<br><br>
Each supported backend has specific structures for its policy data. Check the available options below.


## Pktvisor policy
The policy data for pktvisor can be written in either YAML or JSON, and has four top level sections: “input”, “handlers”, "config" and “kind”.

=== "YAML"
    ```yaml
    input: ..
    config: ...
    kind: collection
    handlers: ...
    ```
=== "JSON"
    ```json
    {
      "input": {       
       },
      "config": {
       },
      "kind": "collection"
       },
      "handlers": {        
    }
    ```
## Input section

The input section specifies what data streams the policy will be using for analysis, in other words, this specifies what data the agent should be listening in on, and is defined at the [agent level](/documentation/orb_agent_configs/#pktvisor-configuration). <br><br>
<span style="color:blue">Required fields:</span><br>
`input_type` - the type of input.  This field will be validated with the type of tap indicated by the `tap` parameter or by the `tap selector` . If the types are incompatible, the policy will fail.<br>

`tap` - the name given to this input in the tap/agent configuration  or `tap_selector` -  tags to match existing agent [taps](/documentation/orb_agent_configs/#pktvisor-configuration).
If `tap_selector` is used, it can be chosen whether taps with any of the tags or with all tags will be attached.


<span style="color:blue">Optional fields:</span><br>
`filter` - to specify what data to include from the input <br>
`config` -  how the input will be used

!!! note

    Every configuration set at the input can be reset at the tap level, with the one set on the tap dominant over the one set on the input.<br>

### Default input structure

*Using specific tap ([check the application in a policy here](#DnsPolicyExample)):*

=== "YAML"
    ```yaml
    input:
      tap: tap_name
      input_type: type_of_input
      filter:
        bpf: ...
      config:
        ...
    ```
=== "JSON"
    ```json
    {
      "input": {
        "tap": "tap_name",
        "input_type": "type_of_input",
        "filter": {
          "bpf": "..."
        },
        "config": "..."
      }
    }
    ```

*or using tap selector matching any ([check the application in a policy here](#NetPolicyExample)):*

=== "YAML"
    ```yaml
    input:
      tap_selector:
        any:
          - key1: value1
          - key2: value2
      input_type: type_of_input
      filter:
        bpf: ...
      config:
        ...
    ```

=== "JSON"
    ```json
    {
      "input": {
        "tap_selector": {
          "any": [
            {
              "key1": "value1"
            },
            {
              "key2": "value2"
            }
          ]
        },
        "input_type": "type_of_input",
        "filter": {
          "bpf": "..."
        },
        "config": "..."
      }
    }
    ```
*or using tap selector matching all ([check the application in a policy here](#DhcpPolicyExample)):*

=== "YAML"
    ```yaml
    input:
      tap_selector:
        all:
          - key1: value1
          - key2: value2
      input_type: type_of_input
      filter:
        bpf: ...
      config:
        ...
    ```

=== "JSON"
    ```json
    {
      "input": {
        "tap_selector": {
          "all": [
            {
              "key1": "value1"
            },
            {
              "key2": "value2"
            }
          ]
        },
        "input_type": "type_of_input",
        "filter": {
          "bpf": "..."
        },
        "config": "..."
      }
    }
    ```


## Config section

There is the possibility of defining settings on the policy level. Currently, the only configuration available is the `merge_like_handlers`. 


| Policy Configuration  |  Type  | Default |
|:---------------------:|:------:|:-------:|
| `merge_like_handlers` | *bool* |  false  |


#### merge_like_handlers

When `merge_like_handlers` config is true, metrics from all handlers of the same type are scraped together. This is useful when the [tap_selector](#input-section) is used, as, by default, metrics are generated separately for each tap in the policy and this can be very expensive, depending on the number of taps.

The `merge_like_handlers` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    config:
      merge_like_handlers: true
    ```
=== "JSON"
    ```json
    {
      "config": {
        "merge_like_handlers": true
      }
    }
    ```

## Kind section

What kind of object you want to create
The only option for now is `"collection"`.

## Handlers section (Analysis)

Handlers are the modules responsible for extracting metrics from inputs. For each handler type, specific configuration, filters and group of metrics can be defined, and there are also configs (abstract configuration) that can be applied to all handlers: <br><br>

**Default handler structure:**

=== "YAML"
    ```yaml
    handlers:
      config:
        deep_sample_rate: 100
        num_periods: 5
        topn_count: 10
        topn_percentile_threshold: 0
      modules:
        tap_name:
          type: ...
          require_version: ...
          config: ...
          filter: ...
          metric_groups:
            enable:
              - ...
              - ....
            disable:
              - .....
              - ......     
    ```

=== "JSON"
    ```json
    {
      "handlers": {
        "config": {
          "deep_sample_rate": 100,
          "num_periods": 5,
          "topn_count": 10,
          "topn_percentile_threshold": 0
        },
        "modules": {
          "tap_name": {
            "type": "...",
            "require_version": "...",
            "config": "...",
            "filter": "...",
            "metric_groups": {
              "enable": [
                "...",
                "...."
              ],
              "disable": [
                ".....",
                "......"
              ]
            }
          }
        }
      }
    }
    ```
<br>

To enable any metric group use the syntax:

=== "YAML"
    ```yaml
    metric_groups:
      enable:
        - group_to_enable
    ```

=== "JSON"
    ```json
    {
      "metric_groups": {
        "enable": [
          "group_to_enable"
        ]
      }
    }
    ```

To enable all available metric groups use the syntax:

=== "YAML"
    ```yaml
    metric_groups:
      enable:
        - all
    ```

=== "JSON"
    ```json
    {
      "metric_groups": {
        "enable": [
          "all"
        ]
      }
    }
    ```

In order to disable any metric group use the syntax:

=== "YAML"
    ```yaml
    metric_groups:
      disable:
        - group_to_disable
    ```

=== "JSON"
    ```json
    {
      "metric_groups": {
        "disable": [
          "group_to_disable"
        ]
      }
    }
    ```

To disable all metric groups use the syntax:

=== "YAML"
    ```yaml
    metric_groups:
      disable:
        - all
    ```

=== "JSON"
    ```json
    {
      "metric_groups": {
        "disable": [
          "all"
        ]
      }
    }
    ```

* Attention: enable is dominant over disable. So if both are passed, the metrics group will be enabled;


### Abstract Configurations

There are general configurations, which can be applied to all handlers. These settings can be reset for each module, within the specific module configs. In this case, the configuration inside the module will override the configuration passed in general handler. <br>

|                  Abstract Configuration                   | Type  |     Default      |
|:---------------------------------------------------------:|:-----:|:----------------:|
|          [`deep_sample_rate`](#deep_sample_rate)          | *int* | 100 (per second) |
|               [`num_periods`](#num_periods)               | *int* |        5         |
|                [`topn_count`](#topn_count)                | *int* |        10        |
 | [`topn_percentile_threshold`](#topn_percentile_threshold) | *int* |        0         |


**deep_sample_rate** <a name="deep_sample_rate"></a><br>
<font size="1">[Back to Top - Abstract Configurations](#abstract-configurations)</font>

`deep_sample_rate` determines the number of data packets that will be analyzed deeply per second. Some metrics are operationally expensive to generate, such as metrics that require string parsing (qname2, qtype, etc.). For this reason, a maximum number of packets per second to be analyzed is determined. If in one second fewer packages than the maximum amount are transacted, all packages will compose the deep metrics sample, if there are more packages than the established one, the value of the variable will be used. Allowed values are in the range [1,100]. Default value is 100. <br>
!!! Note
    If a value less than 1 is passed, the `deep_sample_rate` will be 1. If the value passed is more than 100, `deep_sample_rate` will be 100.


The `deep_sample_rate` usage syntax is:<br>

=== "YAML"
    ```yaml
    deep_sample_rate: int
    ```
=== "JSON"
    ```json
    {
      "deep_sample_rate": int
    }
    ```
**num_periods** <a name="num_periods"></a><br>
<font size="1">[Back to Top - Abstract Configurations](#abstract-configurations)</font>

`num_periods` determines the amount of minutes of data that will be available on the metrics endpoint. Allowed values are in the range [2,10]. Default value is 5. <br>

The `num_periods` usage syntax is:<br>

=== "YAML"
    ```yaml
    num_periods: int
    ```
=== "JSON"
    ```json
    {
    "num_periods": int
    }
    ```

**topn_count** <a name="topn_count"></a><br>
<font size="1">[Back to Top - Abstract Configurations](#abstract-configurations)</font>

`topn_count` sets the maximum amount of elements displayed in top metrics. If there is less quantity than the configured value, the composite metrics will have the existing value. But if there are more metrics than the configured value, the variable will be actively limiting. Any positive integer is valid and the default value is 10. <br>

The `topn_count` usage syntax is:<br>

=== "YAML"
    ```yaml
    topn_count: int
    ```
=== "JSON"
    ```json
    {
    "topn_count": int
    }
    ```

**topn_percentile_threshold** <a name="topn_percentile_threshold"></a><br>
<font size="1">[Back to Top - Abstract Configurations](#abstract-configurations)</font>

`topn_percentile_threshold` sets the threshold of data to be considered based on the percentiles, so allowed values are in the range [0,100].
The default value is 0, that is, all data is considered. If, for example, the value 10 is set, scraped topn metrics will only consider data from the 10th percentile, that is, data between the highest 90%.

The `topn_percentile_threshold` usage syntax is:<br>

=== "YAML"
    ```yaml
    topn_percentile_threshold: int
    ```
=== "JSON"
    ```json
    {
    "topn_percentile_threshold": int
    }
    ```


### DNS Analyzer (dns)

- [Example of policy](#DnsPolicyExample)
- [Metrics Group](#dnsv2_metrics_group)
- [Filters](#dns_filters_v2)
- [Configurations](#dns_configurations)

=== " DNS(v2)"


    !!! warning
    
        Status: `Beta`. The metric names and configuration options may still change
        
    ###### Example of policy with input pcap and handler DNS(v2) <a name="DnsPolicyExample"></a>
        
    === "YAML"
        ``` yaml
        handlers:
          config:
            deep_sample_rate: 100
            num_periods: 5
            topn_count: 10
          modules:
            default_dns:
              type: dns
              require_version: "2.0"
              config:
                public_suffix_list: true
                deep_sample_rate: 50
                num_periods: 2
                topn_count: 25
                topn_percentile_threshold: 10
              filter:
                only_rcode: 0
                only_dnssec_response: true
                answer_count: 1
                only_qtype: [1, 2]
                only_qname_suffix: [".google.com", ".orb.live"]
                geoloc_notfound: false
                asn_notfound: false
                dnstap_msg_type: "auth"
              metric_groups:
                enable:
                  - cardinality
                  - counters
                  - quantiles
                  - top_rcodes
                  - top_qnames
                  - top_qtypes
                disable:
                  - top_size
                  - top_ports
                  - xact_times
                  - top_ecs
        input:
          input_type: pcap
          tap: default_pcap
          filter:
            bpf: udp port 53
          config:
            iface: wlo1
            host_spec: 192.168.1.167/24
            pcap_source: libpcap
            debug: true
        config:
            merge_like_handlers: true
        kind: collection
        ```
    
    === "JSON"
        ``` json
        {
          "handlers": {
            "config": {
              "deep_sample_rate": 100,
              "num_periods": 5,
              "topn_count": 10
            },
            "modules": {
              "default_dns": {
                "type": "dns",
                "require_version": "2.0",
                "config": {
                  "public_suffix_list": true,
                  "deep_sample_rate": 50,
                  "num_periods": 2,
                  "topn_count": 25,
                  "topn_percentile_threshold": 10
                },
                "filter": {
                  "only_rcode": 0,
                  "only_dnssec_response": true,
                  "answer_count": 1,
                  "only_qtype": [
                    1,
                    2
                  ],
                  "only_qname_suffix": [
                    ".google.com",
                    ".orb.live"
                  ],
                  "geoloc_notfound": false,
                  "asn_notfound": false,
                  "dnstap_msg_type": "auth"
                },
                "metric_groups": {
                  "enable": [
                    "cardinality",
                    "counters",
                    "quantiles",
                    "top_rcodes",
                    "top_qnames",
                    "top_qtypes"
                  ],
                  "disable": [
                    "top_size",
                    "top_ports",
                    "xact_times",
                    "top_ecs"
                  ]
                }
              }
            }
          },
          "input": {
            "input_type": "pcap",
            "tap": "default_pcap",
            "filter": {
              "bpf": "udp port 53"
            },
            "config": {
              "iface": "wlo1",
              "host_spec": "192.168.1.167/24",
              "pcap_source": "libpcap",
              "debug": true
            }
          },
          "config": {
            "merge_like_handlers": true
          },
          "kind": "collection"
        }
        ```
    <br>
    
    **Handler Type**: "dns" <br>
    
    #### Metrics Group  - <font size="1">[Check the dns metrics belonging to each group](/documentation/pktvisor_metrics/#dns-metrics)</font><br> <a name="dnsv2_metrics_group"></a>
    
    | Metric Group  | Default  |
    |:-------------:|:--------:|
    |   `top_ecs`   | disabled |
    |  `top_ports`  | disabled |
    |  `top_size`   | disabled |
    | `xact_times`  | disabled |
    | `cardinality` | enabled  |
    |  `counters`   | enabled  |
    | `top_qnames`  | enabled  |
    |  `quantiles`  | enabled  |
    | `top_qtypes`  | enabled  |
    | `top_rcodes`  | enabled  | 

    
    #### Filters <a name="dns_filters_v2"></a><br>
    
    
    |                         Filter                          |  Type   | Input  |
    |:-------------------------------------------------------:|:-------:|:------:|
    |             [`only_rcode`](#only_rcode_v2)              | *str[]* |  PCAP  |
    |        [`exclude_noerror`](#exclude_noerror_v2)         | *bool*  |  PCAP  |
    |   [`only_dnssec_response`](#only_dnssec_response_v2)    | *bool*  |  PCAP  |
    |           [`answer_count`](#answer_count_v2)            |  *int*  |  PCAP  |
    |             [`only_qtype`](#only_qtype_v2)              | *str[]* |  PCAP  |
    |             [`only_qname`](#only_qname_v2)              | *str[]* |  PCAP  |
    |      [`only_qname_suffix`](#only_qname_suffix_v2)       | *str[]* |  PCAP  |
    |        [`geoloc_notfound`](#geoloc_notfound_v2)         | *bool*  |  PCAP  |
    |           [`asn_notfound`](#asn_notfound_v2)            | *bool*  |  PCAP  |
    | [`only_xact_directions`](#only_xact_directions_type_v2) | *str[]* |  PCAP  |
    |        [`dnstap_msg_type`](#dnstap_msg_type_v2)         |  *str*  | DNSTAP |
    
    
    **only_rcode:** *str[]*. <a name="only_rcode_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    When a DNS server returns a response to a query made, one of the properties of the response is the "response code" (rcode), a code that describes what happened to the query that was made. <br>  
    Most response codes indicate why the query failed and when the query succeeds, the return is an RCODE:0, whose name is NOERROR. <br>
    Supported types are in the table below (if you use any other code that is not in the table below, your policy will fail): <br>
    
    | DNS response code |      Name      |                                                                                                                          Description                                                                                                                          |                         Reference                         |
    |:-----------------:|:--------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------------:|
    |        `0`        |    NOERROR     |                                                                                                                      No error condition                                                                                                                       | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `1`        |    FORMERR     |                                                                                               Format error - The name server was unable to interpret the query.                                                                                               | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `2`        |    SERVFAIL     |                                                                           Server failure - The name server was unable to process this query due to a problem with the name server.                                                                            | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `3`        |    NXDOMAIN    |                                                Name Error - Meaningful only for responses from an authoritative name server, this code signifies that the domain name referenced in the query does not exist.                                                 | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `4`        |     NOTIMP     |                                                                                        Not Implemented - The name server does not support the requested kind of query.                                                                                        | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `5`        |    REFUSED     | The name server refuses to perform the specified operation for  policy reasons.  For example, a name server may not wish to provide the information to the particular requester, or a name server may not wish to perform a particular operation (e.g., zone) | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `6`        |    YXDOMAIN    |                                                                                                            Name that should not exist, does exist                                                                                                             | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |        `7`        |    YXRRSET     |                                                                                                           RR set that should not exist, does exist                                                                                                            | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |        `8`        |    NXRRSET     |                                                                                                           RR Set that should exist, does not exist                                                                                                            | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |        `9`        |    NOTAUTH     |                                                                                                      Server Not Authoritative for zone or Not Authorized                                                                                                      | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |       `10`        |    NOTZONE     |                                                                                                                  Name not contained in zone                                                                                                                   | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |       `11`        |   DSOTYPENI    |                                                                                                                   DSO-TYPE Not Implemented                                                                                                                    |       [[RFC8490]](https://www.iana.org/go/rfc8490)        |
    |       `16`        | BADVERS/BADSIG |                                                                                                           Bad OPT Version or TSIG Signature Failure                                                                                                           | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `17`        |     BADKEY     |                                                                                                                      Key not recognized                                                                                                                       | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `18`        |    BADTIME     |                                                                                                                 Signature out of time window                                                                                                                  | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `19`        |    BADMODE     |                                                                                                                         Bad TKEY Mode                                                                                                                         | [[RFC2930]](https://www.rfc-editor.org/rfc/rfc2930.html) |
    |       `20`        |    BADNAME     |                                                                                                                      Duplicate key name                                                                                                                       | [[RFC2930]](https://www.rfc-editor.org/rfc/rfc2930.html) |
    |       `21`        |     BADALG     |                                                                                                                    Algorithm not supported                                                                                                                    | [[RFC2930]](https://www.rfc-editor.org/rfc/rfc2930.html) |
    |       `22`        |    BADTRUNC    |                                                                                                                        Bad Truncation                                                                                                                         | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `23`        |   BADCOOKIE    |                                                                                                                   Bad/missing Server Cookie                                                                                                                   | [[RFC7873]](https://www.rfc-editor.org/rfc/rfc7873.html)  |
      
    The `only_rcode` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_rcode:
          - str
          - str
        ```
    
    === "JSON"
        ```json
        {
          "only_rcode": ["str", "int"]
        }
        ```
    with the `int` referring to the response code to be filtered, written as string. <br>
    
    Example: <br>
    If you want to filter only successful queries responses you should use (note that all that the query will be discarded and the result will be just the responses): <br>
    === "YAML"
        ```yaml
        only_rcode:
          - "NXDOMAIN"
          - "2"
        ```
    
    === "JSON"
        ```json
        {
        "only_rcode": ["NXDOMAIN", "2"]
        }
        ```
    Important information is that only one response code is possible for each handler. So, in order to have multiple filters on the same policy, multiple handlers must be created, each with a rcode type.
    
    **exclude_noerror:** *bool* <a name="exclude_noerror_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    You may still want to filter out only responses with any kind of error. For this, there is the `exclude_noerror` filter, which removes from its results all responses that did not return any type of error.
    The `exclude_noerror` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        exclude_noerror: true
        ```
    
    === "JSON"
        ```json
        {
          "exclude_noerror": true
        }
        ```
    
    Attention: the filter of `exclude_noerror` is dominant in relation to the filter of only_rcode, that is, if the filter of `exclude_noerror` is true, even if the filter of only_rcode is set, the results will be composed only by responses without any type of error (all type of errors will be kept). <br>
    
    **only_dnssec_response:** *bool* <a name="only_dnssec_response_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    When you make a DNS query, the response you get may have a DNSSEC signature, which authenticates that DNS records originate from an authorized sender, thus protecting DNS from falsified information. <br>
    To filter only responses signed by an authorized sender, use:
    The `only_dnssec_response` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_dnssec_response: true
        ```
    
    === "JSON"
        ```json
        {
        "only_dnssec_response": true
        }
        ```
    <br>
    **answer_count:** *int* <a name="answer_count_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    One of the properties present in the query message structure is `Answer RRs`, which is the count of entries in the responses section (RR stands for “resource record”). <br>
    The number of answers in the query is always zero, as a query message has only questions and no answers, and when the server sends the answer to that query, the value is set to the amount of entries in the answers section. <br>
    
    The `answer_count` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        answer_count: int
        ```
    === "JSON"
        ```json
        {
          "answer_count": int
        }
        ```
    
    with the `int` referring to the desired amount of answer. <br>
    Note that any value greater than zero that is defined will exclude queries from the results, since in queries the number of answers is always 0. <br>
    As the answers count of queries is 0, whenever the value set for the answer_count is 0, both queries and responses will compose the result. <br>
    
    
    A special case is the concept of `NODATA`, which is one of the possible returns to a query made to a DNS server is known as. This happens when the query is successful (so rcode:0), but there is no data as a response, so the number of answers is 0. <br>
    In this case, to have in the results only the cases of `NODATA`, that is, the responses, the filter must be used together with the filter `exclude_noerror`.
    
    
    Important information is that only one answer_count is possible for each handler. So, in order to have multiple counts on the same policy, multiple handlers must be created, each with an amount of answers.
    
    **only_qtype:** *str[]* <a name="only_qtype_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    DNS record types are records that provide important information about a hostname or domain. Supported default types can be seen [here](https://github.com/orb-community/pktvisor/blob/develop/libs/visor_dns/dns.h#L30). <br>
    
    The `only_qtype` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_qtype: 
          - str
          - str
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            "str", "str"
          ]
        }
        ```
    If you want to filter only IPV4 record types, for example, you should use: <br>
    
    === "YAML"
        ```yaml
        only_qtype:
          - "A"
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            "A"
          ]
        }
        ```
    or
    
    === "YAML"
        ```yaml
        only_qtype:
          - 1
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            1
          ]
        }
        ```
    Multiple types are also supported and both queries and responses that have any of the values in the array will be considered.
    
    === "YAML"
        ```yaml
        only_qtype:
          - 1
          - 2
          - "A"
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            1,
            2,
            "A"
          ]
        }
        ```
    <br>

    **only_qname:** *str[]* <a name="only_qname_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    
    The `only_qname` filters dns packets based on queries and responses whose names exactly matches the strings present in the array. <br>
    The `only_qname` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_qname:
          - str
          - str
        ```
    === "JSON"
        ```json
        {
          "only_qname": [
            "str", "str"
          ]
        }
        ```
    Examples:
     
    === "YAML"
        ```yaml
        only_qname:
          - www.google.com
          - .nsone.net
        ```
    === "JSON"
        ```json
        {
          "only_qname": [
            "www.google.com",
            ".nsone.net"
          ]
        }
        ```
    <br>

    **only_qname_suffix:** *str[]* <a name="only_qname_suffix_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    
    The `only_qname_suffix` filters queries and responses whose endings (suffixes) of the names match the strings present in the array. <br>
    The `only_qname_suffix` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_qname_suffix:
          - str
          - str
        ```
    === "JSON"
        ```json
        {
          "only_qname_suffix": [
            "str", "str"
          ]
        }
        ```
    Examples:
    
    === "YAML"
        ```yaml
        only_qname_suffix:
          - .google.com
        ```
    === "JSON"
        ```json
        {
          "only_qname_suffix": [
            ".google.com"
          ]
        }
        ```
    or
    
    === "YAML"
        ```yaml
        only_qname_suffix:
          - google.com
          - .nsone.net
        ```
    === "JSON"
        ```json
        {
          "only_qname_suffix": [
            "google.com",
            ".nsone.net"
          ]
        }
        ```
    <br>
    **geoloc_notfound:** *bool* <a name="geoloc_notfound_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    
    Based on ECS (EDNS Client Subnet) information, it is possible to determine the geolocation of where the query is being made. When the Subnet refers to a region found in the standard databases, the city, state and country (approximated) are returned. However, when based on the subnet it is not possible to determine the geolocation, a `not found` is returned. <br>
    The `geoloc_notfound` filter only keeps responses whose geolocations were not found. <br>
    The `geoloc_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        geoloc_notfound: true
        ```
    === "JSON"
        ```json
        {
          "geoloc_notfound": true
        }
        ```
    <br>
    **asn_notfound:** *bool* <a name="asn_notfound_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    
    Based on ECS (EDNS Client Subnet) information, it is possible to determine the ASN (Autonomous System Number). When the IP of the subnet belongs to some not known ASN in the standard databases, a `not found` is returned. <br>
    The `asn_notfound` filter only keeps responses whose asn were not found. <br>
    The `asn_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        asn_notfound: true
        ```
    === "JSON"
        ```json
        {
          "asn_notfound": true
        }
        ```
    <br>
    **only_xact_directions:** *str[]* <a name="only_xact_directions_type_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: PCAP <br>
    
    Filters metrics according to the direction of the transaction. Options are: `in`, `out` and `unknown`.
    === "YAML"
        ```yaml
        only_xact_directions:
          - str
          - str
        ```
        Example:
        ```yaml
        only_xact_directions:
        - in
        - unknown
        ```
    === "JSON"
        ```json
        {
          "only_xact_directions": [
            "str",
            "str"
          ]
        }
        ```
        Example:
        ```json
        {
          "only_xact_directions": [
            "in",
            "unknown"
          ]
        }
        ```

    <br>
    **dnstap_msg_type:** *str* <a name="dnstap_msg_type_v2"></a><br>
    <font size="1">[Back to DNS-v2 filters list](#dns_filters_v2)</font>
    
    Input: DNSTAP <br>
    
    With a dnstap protocol it is possible to know the type of message that must be resolved in the request to the server. This filter therefore allows you to filter by response types.
    Supported message types are: `auth`, `resolver`, `client`, `forwarder`, `stub`, `tool` and `update`.
    
    The `dnstap_msg_type` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        dnstap_msg_type: str
        ```
        Example:
        ```yaml
        dnstap_msg_type: auth
        ```
    === "JSON"
        ```json
        {
          "dnstap_msg_type": "str"
        }
        ```
        Example:
        ```json
        {
          "dnstap_msg_type": "auth"
        }
        ```
    <br>

=== " DNS(v1)"
    
    ###### Example of policy with input pcap and handler DNS(v1)
    
        
    === "YAML"
        ``` yaml
        handlers:
          config:
            deep_sample_rate: 100
            num_periods: 5
            topn_count: 10
          modules:
            default_dns:
              type: dns
              config:
                public_suffix_list: true
                deep_sample_rate: 50
                num_periods: 2
                topn_count: 25
                topn_percentile_threshold: 10
              filter:
                only_rcode: 0
                only_dnssec_response: true
                answer_count: 1
                only_qtype: [1, 2]
                only_qname_suffix: [".google.com", ".orb.live"]
                geoloc_notfound: false
                asn_notfound: false
                dnstap_msg_type: "auth"
              metric_groups:
                enable:
                  - top_ecs
                  - top_qnames_details
                disable:
                  - histograms
                  - quantiles
                  - cardinality
                  - counters
                  - dns_transaction
                  - top_qnames
                  - top_ports
        input:
          input_type: pcap
          tap: default_pcap
          filter:
            bpf: udp port 53
          config:
            iface: wlo1
            host_spec: 192.168.1.167/24
            pcap_source: libpcap
            debug: true
        config:
            merge_like_handlers: true
        kind: collection
        ```
    
    === "JSON"
        ``` json
        {
          "handlers": {
            "config": {
              "deep_sample_rate": 100,
              "num_periods": 5,
              "topn_count": 10
            },
            "modules": {
              "default_dns": {
                "type": "dns",
                "config": {
                  "public_suffix_list": true,
                  "deep_sample_rate": 50,
                  "num_periods": 2,
                  "topn_count": 25,
                  "topn_percentile_threshold": 10
                },
                "filter": {
                  "only_rcode": 0,
                  "only_dnssec_response": true,
                  "answer_count": 1,
                  "only_qtype": [
                    1,
                    2
                  ],
                  "only_qname_suffix": [
                    ".google.com",
                    ".orb.live"
                  ],
                  "geoloc_notfound": false,
                  "asn_notfound": false,
                  "dnstap_msg_type": "auth"
                },
                "metric_groups": {
                  "enable": [
                    "top_ecs",
                    "top_qnames_details"
                  ],
                  "disable": [
                    "cardinality",
                    "counters",
                    "dns_transaction",
                    "top_qnames",
                    "top_ports"
                  ]
                }
              }
            }
          },
          "input": {
            "input_type": "pcap",
            "tap": "default_pcap",
            "filter": {
              "bpf": "udp port 53"
            },
            "config": {
              "iface": "wlo1",
              "host_spec": "192.168.1.167/24",
              "pcap_source": "libpcap",
              "debug": true
            }
          },
          "config": {
            "merge_like_handlers": true
          },
          "kind": "collection"
        }
        ```
    
    **Handler Type**: "dns" <br>
    
    #### Metrics Group - <font size="1">[Check the dns metrics belonging to each group](/documentation/pktvisor_metrics/#dns-metrics)</font><br> <a name="dnsv1_metrics_group"></a>

        
    |     Metric Group     | Default  |
    |:--------------------:|:--------:|
    |      `top_ecs`       | disabled |
    | `top_qnames_details` | disabled |
    |     `histograms`     | disabled |
    |    `cardinality`     | enabled  |
    |      `counters`      | enabled  |
    |  `dns_transaction`   | enabled  |
    |     `top_qnames`     | enabled  |
    |     `top_ports`      | enabled  |
    |     `quantiles`      | enabled  |

    
    #### Filters <a name="dns_filters_v1"></a><br>
    
            
    |                       Filter                       |  Type   | Input  |
    |:--------------------------------------------------:|:-------:|:------:|
    |           [`only_rcode`](#only_rcode_v1)           | *str[]* |  PCAP  |
    |      [`exclude_noerror`](#exclude_noerror_v1)      | *bool*  |  PCAP  |
    | [`only_dnssec_response`](#only_dnssec_response_v1) | *bool*  |  PCAP  |
    |         [`answer_count`](#answer_count_v1)         |  *int*  |  PCAP  |
    |           [`only_qtype`](#only_qtype_v1)           | *str[]* |  PCAP  |
    |           [`only_qname`](#only_qname_v1)           | *str[]* |  PCAP  |
    |    [`only_qname_suffix`](#only_qname_suffix_v1)    | *str[]* |  PCAP  |
    |      [`geoloc_notfound`](#geoloc_notfound_v1)      | *bool*  |  PCAP  |
    |         [`asn_notfound`](#asn_notfound_v1)         | *bool*  |  PCAP  |
    |         [`only_queries`](#only_queries_v1)         | *bool*  |  PCAP  |
    |       [`only_responses`](#only_responses_v1)       | *bool*  |  PCAP  |
    |      [`dnstap_msg_type`](#dnstap_msg_type_v1)      |  *str*  | DNSTAP |
    
    
    **only_rcode:** *str[]*. <a name="only_rcode_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    When a DNS server returns a response to a query made, one of the properties of the response is the "response code" (rcode), a code that describes what happened to the query that was made. <br>  
    Most response codes indicate why the query failed and when the query succeeds, the return is an RCODE:0, whose name is NOERROR. <br>
    Supported types are in the table below (if you use any other code that is not in the table below, your policy will fail): <br>
        
    | DNS response code |      Name      |                                                                                                                          Description                                                                                                                          |                         Reference                         |
    |:-----------------:|:--------------:|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------------------------------------------:|
    |        `0`        |    NOERROR     |                                                                                                                      No error condition                                                                                                                       | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `1`        |    FORMERR     |                                                                                               Format error - The name server was unable to interpret the query.                                                                                               | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `2`        |    SERVFAIL     |                                                                           Server failure - The name server was unable to process this query due to a problem with the name server.                                                                            | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `3`        |    NXDOMAIN    |                                                Name Error - Meaningful only for responses from an authoritative name server, this code signifies that the domain name referenced in the query does not exist.                                                 | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `4`        |     NOTIMP     |                                                                                        Not Implemented - The name server does not support the requested kind of query.                                                                                        | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `5`        |    REFUSED     | The name server refuses to perform the specified operation for  policy reasons.  For example, a name server may not wish to provide the information to the particular requester, or a name server may not wish to perform a particular operation (e.g., zone) | [[RFC1035]](https://www.rfc-editor.org/rfc/rfc1035.html)  |
    |        `6`        |    YXDOMAIN    |                                                                                                            Name that should not exist, does exist                                                                                                             | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |        `7`        |    YXRRSET     |                                                                                                           RR set that should not exist, does exist                                                                                                            | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |        `8`        |    NXRRSET     |                                                                                                           RR Set that should exist, does not exist                                                                                                            | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |        `9`        |    NOTAUTH     |                                                                                                      Server Not Authoritative for zone or Not Authorized                                                                                                      | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |       `10`        |    NOTZONE     |                                                                                                                  Name not contained in zone                                                                                                                   | [[RFC2136]](https://www.rfc-editor.org/rfc/rfc2136.html)  |
    |       `11`        |   DSOTYPENI    |                                                                                                                   DSO-TYPE Not Implemented                                                                                                                    |       [[RFC8490]](https://www.iana.org/go/rfc8490)        |
    |       `16`        | BADVERS/BADSIG |                                                                                                           Bad OPT Version or TSIG Signature Failure                                                                                                           | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `17`        |     BADKEY     |                                                                                                                      Key not recognized                                                                                                                       | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `18`        |    BADTIME     |                                                                                                                 Signature out of time window                                                                                                                  | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `19`        |    BADMODE     |                                                                                                                         Bad TKEY Mode                                                                                                                         | [[RFC2930]](https://www.rfc-editor.org/rfc/rfc2930.html) |
    |       `20`        |    BADNAME     |                                                                                                                      Duplicate key name                                                                                                                       | [[RFC2930]](https://www.rfc-editor.org/rfc/rfc2930.html) |
    |       `21`        |     BADALG     |                                                                                                                    Algorithm not supported                                                                                                                    | [[RFC2930]](https://www.rfc-editor.org/rfc/rfc2930.html) |
    |       `22`        |    BADTRUNC    |                                                                                                                        Bad Truncation                                                                                                                         | [[RFC8945]](https://www.rfc-editor.org/rfc/rfc8945.html)  |
    |       `23`        |   BADCOOKIE    |                                                                                                                   Bad/missing Server Cookie                                                                                                                   | [[RFC7873]](https://www.rfc-editor.org/rfc/rfc7873.html)  |
        
    The `only_rcode` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_rcode:
          - str
          - str
        ```
    
    === "JSON"
        ```json
        {
          "only_rcode": ["str", "int"]
        }
        ```
    with the `int` referring to the response code to be filtered, written as string. <br>
    
    Example: <br>
    If you want to filter only successful queries responses you should use (note that all that the query will be discarded and the result will be just the responses): <br>
    === "YAML"
        ```yaml
        only_rcode:
          - "NXDOMAIN"
          - "2"
        ```
    
    === "JSON"
        ```json
        {
        "only_rcode": ["NXDOMAIN", "2"]
        }
        ```
    Important information is that only one response code is possible for each handler. So, in order to have multiple filters on the same policy, multiple handlers must be created, each with a rcode type.
    
    **exclude_noerror:** *bool* <a name="exclude_noerror_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    The `exclude_noerror` filter removes from its results all responses that did not return any type of error (RCODE=0)
    
    The `exclude_noerror` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        exclude_noerror: true
        ```
    
    === "JSON"
        ```json
        {
          "exclude_noerror": true
        }
        ```
    
    `exclude_noerror` takes precedence over the "only_rcode" filter. If exclude_noerror = True, then the results will be composed only of responses WITH an error, that is, responses with RCODE=0 will not be returned.
 <br>
    
    **only_dnssec_response:** *bool* <a name="only_dnssec_response_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    When you make a DNS query, the response you get may have a DNSSEC signature, which authenticates that DNS records originate from an authorized sender, thus protecting DNS from falsified information. <br>
    To filter only responses signed by an authorized sender, use:
    The `only_dnssec_response` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_dnssec_response: true
        ```
    
    === "JSON"
        ```json
        {
        "only_dnssec_response": true
        }
        ```
    <br>
    **answer_count:** *int* <a name="answer_count_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    One of the properties present in the query message structure is `Answer RRs`, which is the count of entries in the responses section (RR stands for “resource record”). <br>
    The number of answers in the query is always zero, as a query message has only questions and no answers, and when the server sends the answer to that query, the value is set to the amount of entries in the answers section. <br>
    
    The `answer_count` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        answer_count: int
        ```
    === "JSON"
        ```json
        {
          "answer_count": int
        }
        ```
    
    with the `int` referring to the desired amount of answer. <br>
    Note that any value greater than zero that is defined will exclude queries from the results, since in queries the number of answers is always 0. <br>
    As the answers count of queries is 0, whenever the value set for the answer_count is 0, both queries and responses will compose the result. <br>
    
    
    A special case is the concept of `NODATA`, which is one of the possible returns to a query made to a DNS server is known as. This happens when the query is successful (so rcode:0), but there is no data as a response, so the number of answers is 0. <br>
    In this case, to have in the results only the cases of `NODATA`, that is, the responses, the filter must be used together with the filter `exclude_noerror`.
    
    
    Important information is that only one answer_count is possible for each handler. So, in order to have multiple counts on the same policy, multiple handlers must be created, each with an amount of answers.
    
    **only_qtype:** *str[]* <a name="only_qtype_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    DNS record types are records that provide important information about a hostname or domain. Supported default types can be seen [here](https://github.com/orb-community/pktvisor/blob/develop/libs/visor_dns/dns.h#L30). <br>
    
    The `only_qtype` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_qtype: 
          - str
          - str
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            "str", "str"
          ]
        }
        ```
    If you want to filter only IPV4 record types, for example, you should use: <br>
    
    === "YAML"
        ```yaml
        only_qtype:
          - "A"
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            "A"
          ]
        }
        ```
    or
    
    === "YAML"
        ```yaml
        only_qtype:
          - 1
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            1
          ]
        }
        ```
    Multiple types are also supported and both queries and responses that have any of the values in the array will be considered.
    
    === "YAML"
        ```yaml
        only_qtype:
          - 1
          - 2
          - "A"
        ```
    === "JSON"
        ```json
        {
          "only_qtype": [
            1,
            2,
            "A"
          ]
        }
        ```
    <br>

    **only_qname:** *str[]* <a name="only_qname_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    
    The `only_qname` filters dns packets based on queries and responses whose names exactly matches the strings present in the array. <br>
    The `only_qname` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_qname:
          - str
          - str
        ```
    === "JSON"
        ```json
        {
          "only_qname": [
            "str", "str"
          ]
        }
        ```
    Examples:
     
    === "YAML"
        ```yaml
        only_qname:
          - www.google.com
          - .nsone.net
        ```
    === "JSON"
        ```json
        {
          "only_qname": [
            "www.google.com",
            ".nsone.net"
          ]
        }
        ```
    <br>

    **only_qname_suffix:** *str[]* <a name="only_qname_suffix_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    
    The `only_qname_suffix` filters queries and responses whose endings (suffixes) of the names match the strings present in the array. <br>
    The `only_qname_suffix` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_qname_suffix:
          - str
          - str
        ```
    === "JSON"
        ```json
        {
          "only_qname_suffix": [
            "str", "str"
          ]
        }
        ```
    Examples:
    
    === "YAML"
        ```yaml
        only_qname_suffix:
          - .google.com
        ```
    === "JSON"
        ```json
        {
          "only_qname_suffix": [
            ".google.com"
          ]
        }
        ```
    or
    
    === "YAML"
        ```yaml
        only_qname_suffix:
          - google.com
          - .nsone.net
        ```
    === "JSON"
        ```json
        {
          "only_qname_suffix": [
            "google.com",
            ".nsone.net"
          ]
        }
        ```
    <br>
    **geoloc_notfound:** *bool* <a name="geoloc_notfound_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    
    Based on ECS (EDNS Client Subnet) information, it is possible to determine the geolocation of where the query is being made. When the Subnet refers to a region found in the standard databases, the city, state and country (approximated) are returned. However, when based on the subnet it is not possible to determine the geolocation, a `not found` is returned. <br>
    The `geoloc_notfound` filter only keeps responses whose geolocations were not found. <br>
    The `geoloc_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        geoloc_notfound: true
        ```
    === "JSON"
        ```json
        {
          "geoloc_notfound": true
        }
        ```
    <br>
    **asn_notfound:** *bool* <a name="asn_notfound_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    
    Based on ECS (EDNS Client Subnet) information, it is possible to determine the ASN (Autonomous System Number). When the IP of the subnet belongs to some not known ASN in the standard databases, a `not found` is returned. <br>
    The `asn_notfound` filter only keeps responses whose asn were not found. <br>
    The `asn_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        asn_notfound: true
        ```
    === "JSON"
        ```json
        {
          "asn_notfound": true
        }
        ```
    <br>


    **only_queries:** *bool* <a name="only_queries_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    
    The `only_queries` filters out all dns response packets and its usage syntax is:<br>

    
    === "YAML"
        ```yaml
        only_queries: true
        ```
    === "JSON"
        ```json
        {
          "only_queries": true
        }
        ```
    <br>



    **only_responses:** *bool* <a name="only_responses_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: PCAP <br>
    
    
    The `only_responses` filters out all dns queries packets and its usage syntax is: <br>
    
    === "YAML"
        ```yaml
        only_responses: true
        ```
    === "JSON"
        ```json
        {
          "only_responses": true
        }
        ```
    <br>


    **dnstap_msg_type:** *str* <a name="dnstap_msg_type_v1"></a><br>
    <font size="1">[Back to DNS-v1 filters list](#dns_filters_v1)</font>
    
    Input: DNSTAP <br>
    
    With a dnstap protocol it is possible to know the type of message that must be resolved in the request to the server. This filter therefore allows you to filter by response types.
    Supported message types are: `auth`, `resolver`, `client`, `forwarder`, `stub`, `tool` and `update`.
    
    The `dnstap_msg_type` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        dnstap_msg_type: str
        ```
        Example:
        ```yaml
        dnstap_msg_type: auth
        ```
    === "JSON"
        ```json
        {
          "dnstap_msg_type": "str"
        }
        ```
        Example:
        ```json
        {
          "dnstap_msg_type": "auth"
        }
        ```
    <br>


#### Configurations <a name="dns_configurations"></a><br>
- [public_suffix_list](#public_suffix_list): *bool*.
- [recorded_stream](#recorded_stream): *bool*. 
- [xact_ttl_secs](#xact_ttl): *int*.
- [xact_ttl_ms](#xact_ttl): *int*.
- [Abstract configurations](#abstract-configurations). <br><br>

**public_suffix_list** <a name="public_suffix_list"></a><br>
<font size="1">[Back to DNS configurations list](#dns_configurations)</font>

Some names to be resolved by a dns server have public suffixes. These suffixes cause metrics to be generated considering non-relevant data. <br>

The example below illustrates the benefit of using this type of configuration. The qnames consider each part of the name to be resolved. When a name has a public suffix, generic information is generated. Note that in the standard configuration, Qname2 and Qname3 are the same for both domains. With the public suffix setting `true` (which makes the entire public part be considered as a single part), Qname3 already displays relevant information about the name. <br>
The list of suffixes considered public can be accessed [here](https://github.com/orb-community/pktvisor/blob/develop/libs/visor_dns/PublicSuffixList.h). <br>

|            Name             | Qname2 Standard | Qname3 Standard | Qname2 Public Suffix | Qname3 Public Suffix |
|:---------------------------:|:---------------:|:---------------:|:--------------------:|:--------------------:|
|  `www.imagine.qname.co.uk`  |      co.uk      |   qname.co.uk   |     qname.co.uk      | imagine.qname.co.uk  |
| `other.example.qname.co.uk` |      co.uk      |   qname.co.uk   |     qname.co.uk      | example.qname.co.uk  |


The `public_suffix_list` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    public_suffix_list: true
    ```

=== "JSON"
    ```json
    {
      "public_suffix_list": true
    }
    ```

**recorded_stream** <a name="recorded_stream"></a><br>
<font size="1">[Back to DNS configurations list](#dns_configurations)</font>

This configuration is useful when a [pcap_file](/documentation/orb_agent_configs/#pcap_file_input_pcap) is used in taps/input configuration. Set it to True when you want to load an offline traffic (from a pcap_file). <br>

The `recorded_stream` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    recorded_stream: true
    ```

=== "JSON"
    ```json
    {
      "recorded_stream": true
    }
    ```

**xact_ttl_ms** OR **xact_ttl_secs** <a name="xact_ttl"></a><br>
<font size="1">[Back to DNS configurations list](#dns_configurations)</font>

Both configurations have the same functionality, that is, defines the time to live of transactions, and only change the unit of measurement to be configured. This configuration causes the metrics to be generated for complete transactions (query and response) within the established time limit.<br>
> - xact_ttl_ms: Defines the time to live of transactions in milliseconds.
 - xact_ttl_secs: Defines the time to live of transactions in seconds. <br>

Note that `xact_ttl_ms` is dominant over `xact_ttl_secs`, that is, if `xact_ttl_ms` exists, even if `xact_ttl_secs` also exists, the value of `xact_ttl_ms` will be considered.

The `xact_ttl_ms` or `xact_ttl_secs` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    xact_ttl_ms: 5000
    ```

    or

    ```yaml
    xact_ttl_secs: 5
    ```

=== "JSON"
    ```json
    {
      "xact_ttl_ms": 5000
    }
    ```

    or

    ```json
    {
      "xact_ttl_secs": 5
    }
    ```

### Network (L2-L3) Analyzer (net)

- [Example of policy](#NetPolicyExample)
- [Metrics Group](#netv2_metrics_group)
- [Filters](#net_filters_v2)
- [Configurations](#net_configurations)

=== "NET(v2)"

    !!! warning
    
        Status: `Beta`. The metric names and configuration options may still change

    ###### Example of policy with input pcap and handler NET(v2) <a name="NetPolicyExample"></a>
    
    === "YAML"
        ```yaml
        handlers:
          config:
            deep_sample_rate: 100
            num_periods: 5
            topn_count: 10
          modules:
            default_net:
              type: net
              require_version: "2.0"
              config:
                deep_sample_rate: 1
                num_periods: 2
                topn_count: 25
              filter:
                geoloc_notfound: true
                asn_notfound: true
                only_geoloc_prefix:
                  - BR
                  - US/CA
                only_asn_number:
                  - 7326
                  - 16136
              metric_groups:
                disable:
                  - cardinality
                  - counters
                  - top_geo
                  - top_ips
                  - quantiles
        input:
          input_type: pcap
          tap_selector:
            any:
              - key1: value1
              - key2: value2
          filter:
            bpf: net 192.168.1.0/24
          config:
            iface: wlo1
            host_spec: 192.168.1.0/24
            pcap_source: libpcap
            debug: true
        kind: collection
        ```
    
    === "JSON"
        ```json
        {
          "handlers": {
            "config": {
              "deep_sample_rate": 100,
              "num_periods": 5,
              "topn_count": 10
            },
            "modules": {
              "default_net": {
                "type": "net",
                "require_version": "2.0",
                "config": {
                  "deep_sample_rate": 1,
                  "num_periods": 2,
                  "topn_count": 25
                },
                "filter": {
                  "geoloc_notfound": true,
                  "asn_notfound": true,
                  "only_geoloc_prefix": [
                    "BR",
                    "US/CA"
                  ],
                  "only_asn_number": [
                    7326,
                    16136
                  ]
                },
                "metric_groups": {
                  "disable": [
                    "cardinality",
                    "counters",
                    "top_geo",
                    "top_ips",
                    "quantiles"
                  ]
                }
              }
            }
          },
          "input": {
            "input_type": "pcap",
            "tap_selector": {
              "any": [
                {
                  "key1": "value1"
                },
                {
                  "key2": "value2"
                }
              ]
            },
            "filter": {
              "bpf": "net 192.168.1.0/24"
            },
            "config": {
              "iface": "wlo1",
              "host_spec": "192.168.1.0/24",
              "pcap_source": "libpcap",
              "debug": true
            }
          },
          "kind": "collection"
        }
        ```
    <br>
    
    **Handler Type**: "net" <br>
    
    #### Metrics Group - <font size="1">[Check the net metrics belonging to each group](/documentation/pktvisor_metrics/#network-metrics)</font><br> <a name="netv2_metrics_group"></a>
    
    | Metric Group  | Default | 
    |:-------------:|:-------:|
    | `cardinality` | enabled |
    |  `counters`   | enabled |
    |   `top_geo`   | enabled |
    |   `top_ips`   | enabled |
    |  `quantiles`  | enabled |
    
    <br>
    
    
    #### Filters <a name="net_filters_v2"></a><br>
    
    |                     Filter                     |  Type   |    Input     |
    |:----------------------------------------------:|:-------:|:------------:|
    |  [`geoloc_notfound`](#geoloc_notfound_net_v2)  | *bool*  | PCAP, DNSTAP |
    |     [`asn_notfound`](#asn_notfound_net_v2)     | *bool*  | PCAP, DNSTAP |
    | [`only_geoloc_prefix`](#only_geoloc_prefix_v2) | *str[]* | PCAP, DNSTAP |
    |    [`only_asn_number`](#only_asn_number_v2)    | *str[]* | PCAP, DNSTAP |
    
    **geoloc_notfound:** *bool* <a name="geoloc_notfound_net_v2"></a><br>
    <font size="1">[Back to net-v2 filters list](#net_filters_v2)</font>
    
    Input: PCAP <br>
    
    The source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. When the IPs refer to a region found in the standard databases, the city, state and country (approximated) are returned. However, when it is not possible to determine the IP geolocation, a `not found` is returned. <br>
    
    The `geoloc_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        geoloc_notfound: true
        ```
    === "JSON"
        ```json
        {
          "geoloc_notfound": true
        }
        ```
    <br>
    **asn_notfound:** *bool* <a name="asn_notfound_net_v2"></a><br>
    <font size="1">[Back to net-v2 filters list](#net_filters_v2)</font>
    
    Input: PCAP <br>
    
    Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). When the IP of the source or destination belongs to some not known ASN in the standard databases, a `not found` is returned. <br>
    
    The `asn_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        asn_notfound: true
        ```
    === "JSON"
        ```json
        {
          "asn_notfound": true
        }
        ```
    <br>
    **only_geoloc_prefix:** *str[]* <a name="only_geoloc_prefix_v2"></a><br>
    <font size="1">[Back to net-v2 filters list](#net_filters_v2)</font>
    
    Input: PCAP <br>
    
    Source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. In this way it is possible to filter the data considering the geolocation using the filter `only_geoloc_prefix`. <br>
    
    The `only_geoloc_prefix` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_geoloc_prefix: 
          - str
          - str
        ```
        Example:
        ```yaml
        only_geoloc_prefix:
          - BR
          - US/CA
        ```
    === "JSON"
        ```json
        {
          "only_geoloc_prefix": [
            "str", "str"
          ]
        }
        ```
        Example:
        ```json
        {
          "only_geoloc_prefix": [
            "BR",
            "US/CA"
          ]
        }
        ```
    <br>
    **only_asn_number:** *str[]* <a name="only_asn_number_v2"></a><br>
    <font size="1">[Back to net-v2 filters list](#net_filters_v2)</font>
    
    Input: PCAP <br>
    
    Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). In this way it is possible to filter the data considering a specific ASN using the filter `only_asn_number`. <br>
    
    The `only_asn_number` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_asn_number:
          - str
          - str
        ```
        Example:    
        ```yaml
        only_asn_number:
          - 7326
          - 16136
        ```
    === "JSON"
        ```json
        {
          "only_asn_number": [
            "str", "str"
          ]
        }
        ```
        Example:
        ```json
        {
          "only_asn_number": [
            7326,
            16136
          ]
        }
        ```

=== "NET(v1)"

    ###### Example of policy with input pcap and handler NET(v1)

    === "YAML"
        ```yaml
        handlers:
          config:
            deep_sample_rate: 100
            num_periods: 5
            topn_count: 10
          modules:
            default_net:
              type: net
              config:
                deep_sample_rate: 1
                num_periods: 2
                topn_count: 25
              filter:
                geoloc_notfound: true
                asn_notfound: true
                only_geoloc_prefix:
                  - BR
                  - US/CA
                only_asn_number:
                  - 7326
                  - 16136
              metric_groups:
                disable:
                  - cardinality
                  - counters
                  - top_geo
                  - top_ips
        input:
          input_type: pcap
          tap_selector:
            any:
              - key1: value1
              - key2: value2
          filter:
            bpf: net 192.168.1.0/24
          config:
            iface: wlo1
            host_spec: 192.168.1.0/24
            pcap_source: libpcap
            debug: true
        kind: collection
        ```
    
    === "JSON"
        ```json
        {
          "handlers": {
            "config": {
              "deep_sample_rate": 100,
              "num_periods": 5,
              "topn_count": 10
            },
            "modules": {
              "default_net": {
                "type": "net",
                "config": {
                  "deep_sample_rate": 1,
                  "num_periods": 2,
                  "topn_count": 25
                },
                "filter": {
                  "geoloc_notfound": true,
                  "asn_notfound": true,
                  "only_geoloc_prefix": [
                    "BR",
                    "US/CA"
                  ],
                  "only_asn_number": [
                    7326,
                    16136
                  ]
                },
                "metric_groups": {
                  "disable": [
                    "cardinality",
                    "counters",
                    "top_geo",
                    "top_ips"
                  ]
                }
              }
            }
          },
          "input": {
            "input_type": "pcap",
            "tap_selector": {
              "any": [
                {
                  "key1": "value1"
                },
                {
                  "key2": "value2"
                }
              ]
            },
            "filter": {
              "bpf": "net 192.168.1.0/24"
            },
            "config": {
              "iface": "wlo1",
              "host_spec": "192.168.1.0/24",
              "pcap_source": "libpcap",
              "debug": true
            }
          },
          "kind": "collection"
        }
        ```
    <br>
    
    **Handler Type**: "net" <br>
    
    #### Metrics Group  - <font size="1">[Check the net metrics belonging to each group](/documentation/pktvisor_metrics/#network-metrics)</font><br> <a name="netv1_metrics_group"></a>
    
    | Metric Group  | Default | 
    |:-------------:|:-------:|
    | `cardinality` | enabled |
    |  `counters`   | enabled |
    |   `top_geo`   | enabled |
    |   `top_ips`   | enabled |
    
    <br>
    
    
    #### Filters <a name="net_filters_v1"></a><br>
    
    |                     Filter                     |  Type   |    Input     |
    |:----------------------------------------------:|:-------:|:------------:|
    |  [`geoloc_notfound`](#geoloc_notfound_net_v1)  | *bool*  | PCAP, DNSTAP |
    |     [`asn_notfound`](#asn_notfound_net_v1)     | *bool*  | PCAP, DNSTAP |
    | [`only_geoloc_prefix`](#only_geoloc_prefix_v1) | *str[]* | PCAP, DNSTAP |
    |    [`only_asn_number`](#only_asn_number_v1)    | *str[]* | PCAP, DNSTAP |
    
    **geoloc_notfound:** *bool* <a name="geoloc_notfound_net_v1"></a><br>
    <font size="1">[Back to net-v1 filters list](#net_filters_v1)</font>
    
    Input: PCAP <br>
    
    The source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. When the IPs refer to a region found in the standard databases, the city, state and country (approximated) are returned. However, when it is not possible to determine the IP geolocation, a `not found` is returned. <br>
    
    The `geoloc_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        geoloc_notfound: true
        ```
    === "JSON"
        ```json
        {
          "geoloc_notfound": true
        }
        ```
    <br>
    **asn_notfound:** *bool* <a name="asn_notfound_net_v1"></a><br>
    <font size="1">[Back to net-v1 filters list](#net_filters_v1)</font>
    
    Input: PCAP <br>
    
    Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). When the IP of the source or destination belongs to some not known ASN in the standard databases, a `not found` is returned. <br>
    
    The `asn_notfound` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        asn_notfound: true
        ```
    === "JSON"
        ```json
        {
          "asn_notfound": true
        }
        ```
    <br>
    **only_geoloc_prefix:** *str[]* <a name="only_geoloc_prefix_v1"></a><br>
    <font size="1">[Back to net-v1 filters list](#net_filters_v1)</font>
    
    Input: PCAP <br>
    
    Source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. In this way it is possible to filter the data using the geolocation using the filter `only_geoloc_prefix`. <br>.  The filter supports the following strings:
* Continents:  two-character continent code, as follows:
AF - Africa
AN - Antarctica
AS - Asia
EU - Europe
NA - North America
OC - Oceania
SA - South America

* Country: the two-character ISO 3166-1 country code
* Subdivision: the region-portion of the ISO 3166-2 code for the region
    
    The `only_geoloc_prefix` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_geoloc_prefix: 
          - str
          - str
        ```
        Example:
        ```yaml
        only_geoloc_prefix:
          - BR
          - US/CA
        ```
    === "JSON"
        ```json
        {
          "only_geoloc_prefix": [
            "str", "str"
          ]
        }
        ```
        Example:
        ```json
        {
          "only_geoloc_prefix": [
            "BR",
            "US/CA"
          ]
        }
        ```
    <br>
    **only_asn_number:** *str[]* <a name="only_asn_number_v1"></a><br>
    <font size="1">[Back to net-v1 filters list](#net_filters_v1)</font>
    
    Input: PCAP <br>
    
    Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). In this way it is possible to filter the data considering a specific ASN using the filter `only_asn_number`. <br>
    
    The `only_asn_number` filter usage syntax is:<br>
    
    === "YAML"
        ```yaml
        only_asn_number:
          - str
          - str
        ```
        Example:    
        ```yaml
        only_asn_number:
          - 7326
          - 16136
        ```
    === "JSON"
        ```json
        {
          "only_asn_number": [
            "str", "str"
          ]
        }
        ```
        Example:
        ```json
        {
          "only_asn_number": [
            7326,
            16136
          ]
        }
        ```


#### Configurations <a name="net_configurations"></a><br>
- [recorded_stream](#recorded_stream_net): *bool*.
- [Abstract configurations](#abstract-configurations). <br><br>

**recorded_stream** <a name="recorded_stream_net"></a><br>
<font size="1">[Back to net configurations list](#net_configurations)</font>

This configuration is useful when a [pcap_file](/documentation/orb_agent_configs/#pcap_file_input_pcap) is used in taps/input configuration. Set it to True when you want to load an offline traffic (from a pcap_file). <br>

The `recorded_stream` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    recorded_stream: true
    ```

=== "JSON"
    ```json
    {
      "recorded_stream": true
    }
    ```

### DHCP Analyzer (dhcp)

- [Example of policy](#DhcpPolicyExample)
- [Metrics Group](#dhcp_metrics_group)
- [Filters](#dhcp_filters)
- [Configurations](#dhcp_configurations)

###### Example of policy with input pcap and handler DHCP <a name="DhcpPolicyExample"></a>

=== "YAML"
    ```yaml
    handlers:
      config:
        deep_sample_rate: 100
        num_periods: 8
        topn_count: 10
      modules:
        default_dhcp:
          type: dhcp
          config:
            deep_sample_rate: 1
            num_periods: 8
            topn_count: 25
    input:
      input_type: pcap
      tap_selector:
        all:
          - key1: value1
          - key2: value
      filter:
        bpf: net 192.168.1.0/24
      config:
        iface: wlo1
        host_spec: 192.168.1.0/24
        pcap_source: libpcap
        debug: true
    kind: collection
    ```
=== "JSON"
    ```json
    {
      "handlers": {
        "config": {
          "deep_sample_rate": 100,
          "num_periods": 8,
          "topn_count": 10
        },
        "modules": {
          "default_dhcp": {
            "type": "dhcp",
            "config": {
              "deep_sample_rate": 1,
              "num_periods": 8,
              "topn_count": 25
            }
          }
        }
      },
      "input": {
        "input_type": "pcap",
        "tap_selector": {
          "all": [
            {
              "key1": "value1"
            },
            {
              "key2": "value"
            }
          ]
        },
        "filter": {
          "bpf": "net 192.168.1.0/24"
        },
        "config": {
          "iface": "wlo1",
          "host_spec": "192.168.1.0/24",
          "pcap_source": "libpcap",
          "debug": true
        }
      },
      "kind": "collection"
    }
    ```
<br>

**Handler Type**: "dhcp" <br>

#### Metrics Group - <font size="1">[Check dhcp metrics](/documentation/pktvisor_metrics/#dhcp-metrics)</font><br> <a name="dhcp_metrics_group"></a>

- No metrics group available <br>

#### Filters <a name="dhcp_filters"></a><br>
- No filters available. <br><br>

#### Configurations <a name="dhcp_configurations"></a><br>
- [Abstract configurations](#abstract-configurations). <br><br>


### BGP Analyzer (bgp)

- [Example of policy](#BgpPolicyExample)
- [Metrics Group](#bgp_metrics_group)
- [Filters](#bgp_filters)
- [Configurations](#bgp_configurations)

###### Example of policy with input pcap and handler BGP <a name="BgpPolicyExample"></a>


=== "YAML"
    ```yaml
    handlers:
      config:
        deep_sample_rate: 100
        num_periods: 8
        topn_count: 10
      modules:
        default_bgp:
          type: bgp
          config:
            deep_sample_rate: 1
            num_periods: 8
            topn_count: 25
    input:
      input_type: pcap
      tap_selector:
        all:
          - key1: value1
          - key2: value
      filter:
        bpf: net 192.168.1.0/24
      config:
        iface: wlo1
        host_spec: 192.168.1.0/24
        pcap_source: libpcap
        debug: true
    config:
      merge_like_handlers: true        
    kind: collection
    ```
=== "JSON"
    ```json
    {
      "handlers": {
        "config": {
          "deep_sample_rate": 100,
          "num_periods": 8,
          "topn_count": 10
        },
        "modules": {
          "default_bgp": {
            "type": "bgp",
            "config": {
              "deep_sample_rate": 1,
              "num_periods": 8,
              "topn_count": 25
            }
          }
        }
      },
      "input": {
        "input_type": "pcap",
        "tap_selector": {
          "all": [
            {
              "key1": "value1"
            },
            {
              "key2": "value"
            }
          ]
        },
        "filter": {
          "bpf": "net 192.168.1.0/24"
        },
        "config": {
          "iface": "wlo1",
          "host_spec": "192.168.1.0/24",
          "pcap_source": "libpcap",
          "debug": true
        }
      },
      "config": {
        "merge_like_handlers": true
      },
      "kind": "collection"
    }
    ```
<br>

**Handler Type**: "bgp" <br>

#### Metrics Group - <font size="1">[Check BGP metrics](/documentation/pktvisor_metrics/#bgp-metrics)</font><br> <a name="bgp_metrics_group"></a>

- No metrics group available <br>


#### Filters <a name="bgp_filters"></a><br>
- No filters available. <br><br>

#### Configurations <a name="bgp_configurations"></a><br>
- [Abstract configurations](#abstract-configurations). <br><br>


### Packet Capture Analyzer (pcap)

- [Example of policy](#PcapPolicyExample)
- [Metrics Group](#pcap_metrics_group)
- [Filters](#pcap_filters)
- [Configurations](#pcap_configurations)

###### Example of policy with input pcap and handler PCAP <a name="PcapPolicyExample"></a>

=== "YAML"
    ```yaml
    handlers:
      config:
        deep_sample_rate: 100
        num_periods: 8
        topn_count: 10
      modules:
        default_pcap:
          type: pcap
          config:
            deep_sample_rate: 6
            num_periods: 3
            topn_count: 25
    input:
      input_type: pcap
      tap: default_pcap
      filter:
        bpf: net 192.168.1.0/24
      config:
        iface: wlo1
        host_spec: 192.168.1.0/24
        pcap_source: libpcap
        debug: true
    kind: collection
    ```
=== "JSON"
    ```json
    {
      "handlers": {
        "config": {
          "deep_sample_rate": 100,
          "num_periods": 8,
          "topn_count": 10
        },
        "modules": {
          "default_pcap": {
            "type": "pcap",
            "config": {
              "deep_sample_rate": 6,
              "num_periods": 3,
              "topn_count": 25
            }
          }
        }
      },
      "input": {
        "input_type": "pcap",
        "tap": "default_pcap",
        "filter": {
          "bpf": "net 192.168.1.0/24"
        },
        "config": {
          "iface": "wlo1",
          "host_spec": "192.168.1.0/24",
          "pcap_source": "libpcap",
          "debug": true
        }
      },
      "kind": "collection"
    }
    ```
<br>

**Handler Type**: "pcap" <br>

#### Metrics Group - <font size="1">[Check pcap metrics](/documentation/pktvisor_metrics/#pcap-metrics)</font><br> <a name="pcap_metrics_group"></a>
- No metrics group available. <br>


#### Filters <a name="pcap_filters"></a><br>
- No filters available. <br>

#### Configurations <a name="pcap_configurations"></a><br>
- [Abstract configurations](#abstract-configurations). <br>



### Flow Analyzer (flow) [BETA]

!!! warning

    Status: `Beta`. The metric names and configuration options may still change

- [Example of policy](#FlowPolicyExample)
- [Metrics Group](#flow_metrics_group)
- [Filters](#flow_filters)
- [Configurations](#flow_configurations)

###### Example of policy with input flow and handler FLOW <a name="FlowPolicyExample"></a>

=== "YAML"
    ```yaml
    handlers:
        config:
            deep_sample_rate: 95
            num_periods: 6
            topn_count: 8
        modules:
            my_flow:
                type: flow
                config:
                    sample_rate_scaling: false
                    deep_sample_rate: 85
                    num_periods: 5
                    topn_count: 7
                    first_filter_if_as_label: true
                    enrichment: true
                    device_map:
                      192.168.3.32:
                        name: Device1
                        description: This is a device map example
                        interfaces:
                          2:
                            name: Prov1
                            description: This is an interface map example
                    summarize_ips_by_asn: true
                    exclude_unknown_asns_from_summarization: true
                    exclude_asns_from_summarization:
                      - 16509
                    exclude_ips_from_summarization_flow:
                      - 192.168.3.32/32
                metric_groups:
                    enable:
                        - cardinality
                        - counters
                        - top_geo
                        - by_packets
                        - by_bytes
                        - conversations
                        - top_ports
                        - top_ips
                        - top_interfaces
                        - top_ips_ports
                        - top_tos
                filter:
                    only_ports:
                        - 10853
                        - 10860-10890
                    only_device_interfaces:
                        - 216.239.38.10:
                            - 2
                    only_directions: "in"
                    only_ips:
                      - 192.168.2.1/24
                      - 192.158.1.38/32
                    geoloc_notfound: true
                    asn_notfound: true
    input:
      input_type: flow
      tap: default_flow
    kind: collection
    ```
=== "JSON"
    ```json
    {
      "handlers": {
        "config": {
          "deep_sample_rate": 95,
          "num_periods": 6,
          "topn_count": 8
        },
        "modules": {
          "my_flow": {
            "type": "flow",
            "config": {
              "sample_rate_scaling": false,
              "deep_sample_rate": 85,
              "num_periods": 5,
              "topn_count": 7,
              "first_filter_if_as_label": true,
              "enrichment": true,
              "device_map": {
                "192.168.3.32": {
                  "name": "Device1",
                  "description": "This is a device map example",
                  "interfaces": {
                    "2": {
                      "name": "Prov1",
                      "description": "This is an interface map example"
                    }
                  }
                }
              },
              "summarize_ips_by_asn": true,
              "exclude_unknown_asns_from_summarization": true,
              "exclude_asns_from_summarization": [
                16509
              ],
              "exclude_ips_from_summarization_flow": [
                "192.168.3.32/32"
              ]
            },
            "metric_groups": {
              "enable": [
                "cardinality",
                "counters",
                "top_geo",
                "by_packets",
                "by_bytes",
                "conversations",
                "top_ports",
                "top_ips",
                "top_interfaces",
                "top_ips_ports",
                "top_tos"
              ]
            },
            "filter": {
              "only_ports": [
                10853,
                "10860-10890"
              ],
              "only_device_interfaces": [
                {
                  "216.239.38.10": [
                    2
                  ]
                }
              ],
              "only_directions": "in",
              "only_ips": [
                "192.168.2.1/24",
                "192.158.1.38/32"
              ],
              "geoloc_notfound": true,
              "asn_notfound": true
            }
          }
        }
      },
      "input": {
        "input_type": "flow",
        "tap": "default_flow"
      },
      "kind": "collection"
    }
    ```
<br>

**Handler Type**: "flow" <br>

#### Metrics Group - <font size="1">[Check the flow metrics belonging to each group](/documentation/pktvisor_metrics/#flow-metrics-beta)</font><br> <a name="flow_metrics_group"></a>

|   Metric Group   | Default  | 
|:----------------:|:--------:|
|  `cardinality`   | enabled  |
|    `counters`    | enabled  |
|   `by_packets`   | enabled  |
|    `by_bytes`    | enabled  |
|    `top_ips`     | enabled  |
|   `top_ports`    | enabled  |
| `top_ips_ports`  | enabled  |
|    `top_geo`     | disabled |
| `conversations`  | disabled |
| `top_interfaces` | disabled |
|    `top_tos`     | disabled | 


#### Filters <a name="flow_filters"></a><br>


|                       Filter                        |  Type   | Input |
|:---------------------------------------------------:|:-------:|:-----:|
| [`only_device_interfaces`](#only_device_interfaces) | *str[]* | FLOW  |
|        [`only_directions`](#only_directions)        |  *str*  | FLOW  |
|            [`only_ips`](#only_ips_flow)             | *str[]* | FLOW  |
|          [`only_ports`](#only_ports_flow)           | *str[]* | FLOW  |
|     [`geoloc_notfound`](#geoloc_notfound_flow)      | *bool*  | FLOW  |
|        [`asn_notfound`](#asn_notfound_flow)         | *bool*  | FLOW  |


**only_device_interfaces:** *str[]* <a name="only_device_interfaces"></a><br>
<font size="1">[Back to flow filters list](#flow_filters)</font>

Input: FLOW <br>

`only_device_interfaces` filters data by only retaining flows coming from the specific devices and interfaces defined in this filter. <br>
The difference between `only_device_interfaces` and `only_ips` is that `only_ips` filters based on the IPs observed *inside* the flows, while `only_device_interfaces` filters based on the device and interface *sending* the flows. <br>

The `only_device_interfaces` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    only_device_interfaces:
      - device:
        - interface
    ```
    Example:
    ```yaml
    only_device_interfaces:
      - 216.239.38.10:
        - 2 #port can be passed as int
        - 4-10 #port can be passed as range. Ports from 4 to 10: all ports in this interval will be accepted.
        - "1" #port can be passed as str
      - 192.158.1.38: [9, 4-10]
      - 192.168.2.32:
        - "*" #all ports
    ```
=== "JSON"
    ```json
    {
      "only_device_interfaces": [
        {
          "device": [
            "interface"
          ]
        }
      ]
    }
    ```
    Example:
    ```json
    {
      "only_device_interfaces": [
        {
          "216.239.38.10": [
            2,
            "4-10",
            "10853"
          ]
        },
        {
          "192.158.1.38": [
            3,
            "10860-10890"
          ]
        },
        {
          "192.168.2.32": [
            "*"
          ]
        }
      ]
    }
    ```
<br>
**only_directions:** *str[]* <a name="only_directions"></a><br>
<font size="1">[Back to flow filters list](#flow_filters)</font>

`only_directions` filters data by its direction. Options are: "in" and/or "out". 

The `only_directions` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    only_directions: [str]
    ```
    Example:
    ```yaml
    only_directions:
        - "in"
    ```
=== "JSON"
    ```json
    {
      "only_directions": [
        "str"
      ]
    }
    ```
    Example:
    ```json
    {
      "only_directions": [
        "in"
      ]
    }
    ```

<br>

**only_ips:** *str[]* <a name="only_ips_flow"></a><br>
<font size="1">[Back to flow filters list](#flow_filters)</font>

Input: FLOW <br>

To filter data only from certain source OR destination, you can use `only_ips` filter, for which CIDR (Inter-Domain Routing Classes) ranges are supported. <br>

The `only_ips` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    only_ips:
      - array
    ```
    Example:
    ```yaml
    only_ips:
      - 192.168.1.1/24
      - 192.158.1.38/32
    ```
=== "JSON"
    ```json
    {
      "only_ips": [
        "array"
      ]
    }
    ```
    Example:
    ```json
    {
      "only_ips": [
        "192.168.2.1/24",
        "192.158.1.38/32"
      ]
    }
    ```
<br>
**only_ports:** *str[]* <a name="only_ports_flow"></a><br>
<font size="1">[Back to flow filters list](#flow_filters)</font>

Input: FLOW <br>

`only_ports` filter only filters data being sent to or received on one of the selected TCP/UDP ports (or range of ports). <br>

The `only_ports` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    only_ports:
      - array
    ```
    Example:
    ```yaml
    only_ports:
      - 10853 #port can be passed as int
      - "10854" #port can be passed as str
      - 10860-10890 #range from 10860 to 10890. All ports in this interval will be accepted
    ```
=== "JSON"
    ```json
    {
      "only_ports": [
        "array"
      ]
    }
    ```
    Example:
    ```json
    {
      "only_ports": [
        10853,
        "10854",
        "10860-10890"
      ]
    }
    ```
<br>
**geoloc_notfound:** *bool* <a name="geoloc_notfound_flow"></a><br>
<font size="1">[Back to flow filters list](#flow_filters)</font>

Input: FLOW <br>

The source and destination IPs are used to determine the geolocation to know where the data is from and where it is going. When the IPs refer to a region found in the standard databases, the city, state and country (approximated) are returned. However, when it is not possible to determine the IP geolocation, a `not found` is returned. <br>

The `geoloc_notfound` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    geoloc_notfound: true
    ```
=== "JSON"
    ```json
    {
      "geoloc_notfound": true
    }
    ```
<br>
**asn_notfound:** *bool* <a name="asn_notfound_flow"></a><br>
<font size="1">[Back to flow filters list](#flow_filters)</font>

Input: FLOW <br>

Based on source and destination IP, it is possible to determine the ASN (Autonomous System Number). When the IP of the source or destination belongs to some not known ASN in the standard databases, a `not found` is returned. <br>

The `asn_notfound` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    asn_notfound: true
    ```
=== "JSON"
    ```json
    {
      "asn_notfound": true
    }
    ```

<br>

#### Configurations <a name="flow_configurations"></a><br>
- [sample_rate_scaling](#sample_rate_scaling_flow): *bool* <br>
- [first_filter_if_as_label](#first_filter_if_as_label_flow): *bool* <br>
- [enrichment](#enrichment_flow): *bool* <br>
- [device_map](#device_map_flow): *map* <br>
- [summarize_ips_by_asn](#summarize_ips_by_asn_flow): *bool* <br>
- [exclude_asns_from_summarization](#exclude_asns_from_summarization_flow): *str[]* <br>
- [exclude_unknown_asns_from_summarization](#exclude_unknown_asns_from_summarization_flow): *bool* <br>
- [subnets_for_summarization](#subnets_for_summarization_flow): *str[]* <br>
- [exclude_ips_from_summarization](#exclude_ips_from_summarization_flow) *str[]* <br>
- [recorded_stream](#recorded_stream_flow): *bool*<br>
- [Abstract configurations](#abstract-configurations). <br><br>

**sample_rate_scaling** <a name="sample_rate_scaling_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

By default, flow metrics are generated by an approximation based on sampling the data. 1 packet every N is analyzed and the prediction of the entire population is made from the sample. If you want to see exactly all the exact data, you can disable `sample_rate_scaling`. <br>

The `sample_rate_scaling` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    sample_rate_scaling: false
    ```
=== "JSON"
    ```json
    {
      "sample_rate_scaling": false
    }
    ```

**first_filter_if_as_label** <a name="first_filter_if_as_label_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration requires the `only_interfaces` filter to be active (true). If this setting is `true`, the interfaces will be used as labels for the metrics.

The `first_filter_if_as_label` filter usage syntax is:<br>

=== "YAML"
    ```yaml
    first_filter_if_as_label: true
    ```
=== "JSON"
    ```json
    {
    "first_filter_if_as_label": true
    }
    ```
**enrichment** <a name="enrichment_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

When true, uses device map settings.

The `enrichment` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    enrichment: True
    ```
=== "JSON"
    ```json
    {
    "enrichment": true
    }
    ```

**device_map** <a name="device_map_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration allows the user to assign a custom name to devices and interfaces, and the proper functioning of this configuration depends on the [enrichment](#enrichment_flow) being True.


The `device_map` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    device_map:
      device_ip:
        name: "str" #name is required
        description: "Optionally set a description"
        interfaces: #Interfaces are optional
            interface:
                name: "str" #required
                description: "Optionally set a description"
    ```
    Example:
    ```yaml
    device_map:
      192.168.2.32:
        name: Cisco
        description: This is a device map example
        interfaces:
          2:
            name: GoogleProv
            description: This is an interface map example
    ```
=== "JSON"
    ```json
    {
      "device_map": {
        "device_ip": {
          "name": "str",
          "description": "Optionally set a description",
          "interfaces": {
            "interface": {
              "name": "str",
              "description": "Optionally set a description"
            }
          }
        }
      }
    }
    ```
    Example:
    ```json
    {
      "device_map": {
        "192.168.2.32": {
          "name": "Cisco",
          "description": "This is a device map example",
          "interfaces": {
            "2": {
              "name": "GoogleProv",
              "description": "This is an interface map example"
            }
          }
        }
      }
    }
    ```

Summarization is a useful strategy for visualization, but also for decreasing the cardinality of the data, and two types of summarization are supported: by [asn](#summarize_ips_by_asn_flow) and by [subnets](#subnets_for_summarization_flow), and summarization by ASN is dominant over subnet, i.e. If both configurations are present, only the IPs of an excluded asn or an unknown asn (if [exclude_unknown_asns_from_summarization](#exclude_unknown_asns_from_summarization_flow) is true) will be summarized by subnet.



**summarize_ips_by_asn** <a name="summarize_ips_by_asn_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

When True, it summarizes data by ASN (Autonomous System Number).

The `summarize_ips_by_asn` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    summarize_ips_by_asn: true
    ```

=== "JSON"
    ```json
    {
      "summarize_ips_by_asn": true
    }
    ```

**exclude_asns_from_summarization** <a name="exclude_asns_from_summarization_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration must be used in conjunction with [summarize_ips_by_asn](#summarize_ips_by_asn_flow), in order to exclude ASNs from summarization. In this case, packets transacted by excluded ASNs will be exposed by IPs.

The `exclude_asns_from_summarization` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    exclude_asns_from_summarization:
      - 8075
      - 16509
    ```

=== "JSON"
    ```json
    {
      "exclude_asns_from_summarization": [
        8075,
        16509
      ]
    }
    ```

**exclude_unknown_asns_from_summarization** <a name="exclude_unknown_asns_from_summarization_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration must be used in conjunction with [summarize_ips_by_asn](#summarize_ips_by_asn_flow), in order to expose IPs from packets transacted by unknown ASNs.

The `exclude_unknown_asns_from_summarization` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    exclude_unknown_asns_from_summarization: true
    ```

=== "JSON"
    ```json
    {
      "exclude_unknown_asns_from_summarization": true
    }
    ```

**subnets_for_summarization** <a name="subnets_for_summarization_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration allows the summarization of flow data by subnets. Attention: This configuration will only work properly if the [summarize_ips_by_asn](#summarize_ips_by_asn_flow) configuration is not set for the IP, since [summarize_ips_by_asn](#summarize_ips_by_asn_flow) is dominant.

The `subnets_for_summarization` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    subnets_for_summarization:
      - 192.168.2.1/24
    ```

=== "JSON"
    ```json
    {
      "subnets_for_summarization": [
        "192.168.2.1/24"
      ]
    }
    ```

!!! tip

    It is possible to define a summary pattern by defining the CIDR only. In this way, all present IPs will be summarized. 
    For this to be done, just pass the default host mask for IPv4 or/and IPv6 and the desired CIDR for grouping. 
    This pattern has less priority than the explicitly defined subnets, so only IPs not belonging to any set subnet will be summarized following the general pattern.


    === "YAML"
        ```yaml
        subnets_for_summarization:
          - 0.0.0.0/16
          - ::/64
        ```
    
    === "JSON"
        ```json
        {
          "subnets_for_summarization": [
            "0.0.0.0/16",
            "::/64"
          ]
        }
        ```
 
**exclude_ips_from_summarization_flow** <a name="exclude_ips_from_summarization_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration must be used in conjunction with [summarize_ips_by_asn](#summarize_ips_by_asn_flow) or [subnets_for_summarization](#subnets_for_summarization_flow) and will remove the specified IPs from the summarization.

The `exclude_ips_from_summarization_flow` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    exclude_ips_from_summarization_flow:
      - 192.168.2.1/31
    ```

=== "JSON"
    ```json
    {
      "exclude_ips_from_summarization_flow": [
        "192.168.2.1/31"
      ]
    }
    ```

**recorded_stream** <a name="recorded_stream_flow"></a><br>
<font size="1">[Back to flow configurations list](#flow_configurations)</font>

This configuration is useful when a [pcap_file](/documentation/orb_agent_configs/#pcap_file_input_pcap) is used in taps/input configuration. Set it to True when you want to load an offline traffic (from a pcap_file). <br>

The `recorded_stream` configuration usage syntax is:<br>

=== "YAML"
    ```yaml
    recorded_stream: true
    ```

=== "JSON"
    ```json
    {
      "recorded_stream": true
    }
    ```


### Netprobe [BETA]

!!! warning

    Status: `Beta`. The metric names and configuration options may still change

- [Example of policy](#NetprobePolicyExample)
- [Metrics Group](#netprobe_metrics_group)
- [Filters](#netprobe_filters)
- [Configurations](#netprobe_configurations)

###### Example of policy with input netprobe and handler NETPROBE <a name="NetprobePolicyExample"></a>


=== "YAML"
    ```yaml
    handlers:
      modules:
        default_netprobe:
          type: netprobe
          metric_groups:
            enable:
              - counters
              - quantiles
              - histograms
    input:
      input_type: netprobe
      tap: default_netprobe
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
    kind: collection
    ```
=== "JSON"
    ```json
    {
      "handlers": {
        "modules": {
          "default_netprobe": {
            "type": "netprobe",
            "metric_groups": {
              "enable": [
                "counters",
                "quantiles",
                "histograms"
              ]
            }
          }
        }
      },
      "input": {
        "input_type": "netprobe",
        "tap": "default_netprobe",
        "config": {
          "test_type": "ping",
          "interval_msec": 2000,
          "timeout_msec": 1000,
          "packets_per_test": 10,
          "packets_interval_msec": 25,
          "packet_payload_size": 56,
          "targets": {
            "www.google.com": {
              "target": "www.google.com"
            },
            "orb_community": {
              "target": "orb.community"
            }
          }
        }
      },
      "kind": "collection"
    }
    ```
<br>

**Handler Type**: "netprobe" <br>

#### Metrics Group - <font size="1">[Check the netprobe metrics belonging to each group](/documentation/pktvisor_metrics/#netprobe-metrics-beta)</font><br> <a name="netprobe_metrics_group"></a>


| Metric Group | Default  | 
|:------------:|:--------:|
| `quantiles`  | disabled |
|  `counters`  | enabled  |
| `histograms` | enabled  |


#### Filters <a name="netprobe_filters"></a><br>
- No filters available. <br><br>


#### Configurations <a name="netprobe_configurations"></a><br>
- [Abstract configurations](#abstract-configurations). <br><br>
- In netprobe policies it makes a lot of sense to use the settings from the input directly in the policy, since the settings are more related to the probe than the device the orb agent is running on. Therefore, it is worth reinforcing here the ability to override all tap settings in the policy. See [here](/documentation/orb_agent_configs/#netprobe) the available configurations for netprobe.


