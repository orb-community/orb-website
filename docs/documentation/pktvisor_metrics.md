# Orb Metrics

The Orb metrics currently provided come from the various supported pktvisor handlers and are listed here by handler.

## DHCP Metrics

| Metric             | Prometheus Name                    |
| ------------------ | ---------------------------------- |
|                    | dhcp\_rates\_total                 |
|                    | dhcp\_wire\_packets\_ack           |
|                    | dhcp\_wire\_packets\_deep\_samples |
|                    | dhcp\_wire\_packets\_discover      |
|                    | dhcp\_wire\_packets\_filtered      |
|                    | dhcp\_wire\_packets\_offer         |
|                    | dhcp\_wire\_packets\_request       |
| DORA packet counts | dhcp\_wire\_packets\_total         |

## DNS Metrics

| Metric                                                                                       | Prometheus Name                   |
| -------------------------------------------------------------------------------------------- | --------------------------------- |
| Cardinality of unique QNAMES                                                                 | dns\_cardinality\_qname           |
| Quantiles of rates for DNS packets processed by policy                                       | dns\_rates\_total                 |
| Top ECS ASNs                                                                                 | dns\_top\_asn\_ecs                |
| Top ECS GeoIP locations                                                                      | dns\_top\_geo\_loc\_ecs           |
| Top QNAMES with response code NOERROR and no data in the response (NODATA)                   | dns\_top\_nodata                  |
| Top QNAMES with response code NXDOMAIN                                                       | dns\_top\_nxdomain                |
| Top QNAMES by response volume                                                                | dns\_top\_qname\_by\_resp\_bytes  |
| Top QNAMES aggregated at a depth of two labels                                               | dns\_top\_qname2                  |
| Top QNAMES aggregated at a depth of three labels                                             | dns\_top\_qname3                  |
| Top QTYPEs                                                                                   | dns\_top\_qtype                   |
| Top EDNS Client Subnets (ECS)                                                                | dns\_top\_query\_ecs              |
| Top RCODEs                                                                                   | dns\_top\_rcode                   |
| Top QNAMES with response code REFUSED                                                        | dns\_top\_refused                 |
| Top QNAMES with response code SRVFAIL                                                        | dns\_top\_srvfail                 |
| Top UDP source ports of DNS queries                                                          | dns\_top\_udp\_ports              |
| Total DNS wire packets sampled for deep inspection                                           | dns\_wire\_packets\_deep\_samples |
| Count of DNS packets sent to policy                                                          | dns\_wire\_packets\_events        |
| Count of DNS packets filtered out by policy                                                  | dns\_wire\_packets\_filtered      |
| Count of DNS packets received over IPv4                                                      | dns\_wire\_packets\_ipv4          |
| Count of DNS packets received over IPv6                                                      | dns\_wire\_packets\_ipv6          |
| Count of DNS packets flagged as reply and identified as NODATA                               | dns\_wire\_packets\_nodata        |
| Count of DNS packets flagged as reply with response code NOERROR                             | dns\_wire\_packets\_noerror       |
| Count of DNS packets flagged as reply with response code NXDOMAIN                            | dns\_wire\_packets\_nxdomain      |
| Count of DNS packets flagged as query                                                        | dns\_wire\_packets\_queries       |
| Count of DNS packets with EDNS Client Subnet (ECS) option set                                | dns\_wire\_packets\_query\_ecs    |
| Count of DNS packets flagged as reply with response code REFUSED                             | dns\_wire\_packets\_refused       |
| Count of DNS packets flagged as reply                                                        | dns\_wire\_packets\_replies       |
| Count of DNS packets flagged as reply with response code SRVFAIL                             | dns\_wire\_packets\_srvfail       |
| Count of DNS packets received over TCP                                                       | dns\_wire\_packets\_tcp           |
| Count of DNS packets matched by policy                                                       | dns\_wire\_packets\_total         |
| Count of DNS packets received over UDP                                                       | dns\_wire\_packets\_udp           |
| Total number of DNS transactions that timed out                                              | dns\_xact\_counts\_timed\_out     |
| Total DNS transactions (query/reply pairs)                                                   | dns\_xact\_counts\_total          |
| Quantiles of transaction timing (query/reply pairs) when host is server, in microseconds     | dns\_xact\_in\_quantiles\_us      |
| Top QNAMES in transactions where host is the server and transaction speed is slower than p90 | dns\_xact\_in\_top\_slow          |
| Total ingress DNS transactions (host is server)                                              | dns\_xact\_in\_total              |
| Quantiles of transaction timing (query/reply pairs) when host is client, in microseconds     | dns\_xact\_out\_quantiles\_us     |
| Top QNAMES in transactions where host is the client and transaction speed is slower than p90 | dns\_xact\_out\_top\_slow         |
| Total egress DNS transactions (host is client)                                               | dns\_xact\_out\_total             |
| Distribution of response/query size ratios                                                   | dns\_xact\_ratio\_quantiles       |

## Network Metrics

| Metric                                       | Prometheus Name                     |
| -------------------------------------------- | ----------------------------------- |
| Destination IP cardinality                   | packets\_cardinality\_dst\_ips\_out |
| Source IP cardinality                        | packets\_cardinality\_src\_ips\_in  |
| Count of packets sampled for deep inspection | packets\_deep\_samples              |
| Count of packets sent to policy              | packets\_events                     |
| Count of packets filtered out by policy      | packets\_filtered                   |
| Count of ingress packets                     | packets\_in                         |
| Count of IPv4 packets                        | packets\_ipv4                       |
| Count of IPv6 packets                        | packets\_ipv6                       |
| Count of packets not UDP or TCP              | packets\_other\_l4                  |
| Count of egress packets                      | packets\_out                        |
| Quantiles of packet payload sizes            | packets\_payload\_size              |
| Count of TCP packets with SYN flag set       | packets\_protocol\_tcp\_syn         |
| Quantiles of ingress data rates              | packets\_rates\_bytes\_in           |
| Quantiles of egress data rates               | packets\_rates\_bytes\_out          |
| Quantiles of total data rates                | packets\_rates\_bytes\_total        |
| Quantiles of ingress packet rates            | packets\_rates\_pps\_in             |
| Quantiles of egress packet rates             | packets\_rates\_pps\_out            |
| Quantiles of total packet rates              | packets\_rates\_pps\_total          |
| Count of TCP packets                         | packets\_tcp                        |
| Top ASNs                                     | packets\_top\_ASN                   |
| Top GeoIP locations                          | packets\_top\_geoLoc                |
| Top IPv4 IP addresses                        | packets\_top\_ipv4                  |
| Top IPv6 IP addresses                        | packets\_top\_ipv6                  |
| Count of packets matched by policy           | packets\_total                      |
| Count of UDP packets                         | packets\_udp                        |
| Count of packets of unknown direction        | packets\_unknown\_dir               |

## PCAP Metrics

| Metric                                        | Prometheus Name               |
| --------------------------------------------- | ----------------------------- |
| Total wire packets dropped by the interface   | pcap\_if\_drops               |
| Total wire packets dropped by the OS          | pcap\_os\_drops               |
| Total TCP wire packets that failed reassembly | pcap\_tcp\_reassembly\_errors |