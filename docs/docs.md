---
hide:
- navigation
---
# Resources 

## Talks

* [ICANN DNS Symposium](https://www.icann.org/ids) 2021 Talk [Recording](https://drive.google.com/file/d/14sDxmwvMV1wF_1cBUBv3qj29Hen_thea/view?usp=sharing) and [Slides](https://drive.google.com/file/d/1fEydsa-suUbE9fNHo87zY88mOmbeZviU/view)
* [DNS-OARC](https://www.dns-oarc.net/) 2020 Talk [Recording](https://www.youtube.com/watch?v=PwEOePOvkug&list=PLCAxS3rufJ1cv93o5VK226HEHo25fv06M&index=3) (first talk) with [Slides](https://indico.dns-oarc.net/event/34/contributions/783/attachments/774/1328/pktvisor3-OARC-sweyrick.pdf)
* O'reilly Velocity San Jose 2019 Talk [Recording](https://drive.google.com/file/d/1AjhbUjkXT5saBP6iYIZjoinYsiZ5LDaV/view?usp=sharing) with [Slides](https://docs.google.com/presentation/d/e/2PACX-1vR984fhii0Pso97RRjSFgZupknwQqf-XMhGuriT8HPHHiLlB1c4SnDtRnJtX66nxYv2GETk4ex81QiU/pub?start=false&loop=false&delayms=3000)

## Usage

### Agent Usage

A collector agent should be installed on each node to be monitored.

Current command line options are described with:

```
docker run --rm ns1labs/pktvisor pktvisord --help
```

or

```
./pktvisor-x86_64.AppImage pktvisord --help
```

```

    Usage:
      pktvisord [options] [IFACE]
      pktvisord (-h | --help)
      pktvisord --version

    pktvisord summarizes data streams and exposes a REST API control plane for configuration and metrics.

    IFACE, if specified, is either a network interface or an IP address (4 or 6). If this is specified,
    a "pcap" input stream will be automatically created, with "net", "dns", and "pcap" handler modules attached.

    Base Options:
      -d                    Daemonize; fork and continue running in the background [default: false]
      -h --help             Show this screen
      -v                    Verbose log output
      --no-track            Don't send lightweight, anonymous usage metrics
      --version             Show version
    Web Server Options:
      -l HOST               Run web server on the given host or IP [default: localhost]
      -p PORT               Run web server on the given port [default: 10853]
      --tls                 Enable TLS on the web server
      --tls-cert FILE       Use given TLS cert. Required if --tls is enabled.
      --tls-key FILE        Use given TLS private key. Required if --tls is enabled.
      --admin-api           Enable admin REST API giving complete control plane functionality [default: false]
                            When not specified, the exposed API is read-only access to summarized metrics.
                            When specified, write access is enabled for all modules.
    Geo Options:
      --geo-city FILE       GeoLite2 City database to use for IP to Geo mapping
      --geo-asn FILE        GeoLite2 ASN database to use for IP to ASN mapping
    Configuration:
      --config FILE         Use specified YAML configuration to configure options, Taps, and Collection Policies      
    Logging Options:
      --log-file FILE       Log to the given output file name
      --syslog              Log to syslog
    Prometheus Options:
      --prometheus          Enable native Prometheus metrics at path /metrics
      --prom-instance ID    Optionally set the 'instance' label to given ID
    Handler Module Defaults:
      --max-deep-sample N   Never deep sample more than N% of streams (an int between 0 and 100) [default: 100]
      --periods P           Hold this many 60 second time periods of history in memory [default: 5]
    pcap Input Module Options:
      -b BPF                Filter packets using the given tcpdump compatible filter expression. Example: "port 53"
      -H HOSTSPEC           Specify subnets (comma separated) to consider HOST, in CIDR form. In live capture this /may/ be detected automatically
                            from capture device but /must/ be specified for pcaps. Example: "10.0.1.0/24,10.0.2.1/32,2001:db8::/64"
                            Specifying this for live capture will append to any automatic detection.

```

### Command Line UI Usage

The command line UI (`pktvisor-cli`) connects directly to a pktvisord agent to visualize the real time stream
summarization, which is by default a sliding 5 minute time window. It can also connect to an agent running on a remote
host.

```
docker run --rm ns1labs/pktvisor pktvisor-cli -h
```

```shell
./pktvisor-x86_64.AppImage pktvisor-cli -h
```

```

Usage:
  pktvisor-cli [-p PORT] [-H HOST]
  pktvisor-cli -h
  pktvisor-cli --version

  -H string
    	Query pktvisord metrics webserver on the given host (default "localhost")
  -h	Show help
  -p int
    	Query pktvisord metrics webserver on the given port (default 10853)
  -version
    	Show client version

```

### pcap File Analysis

`pktvisor-pcap` is a tool that can statically analyze prerecorded packet capture files. It takes many of the same
options, and does all of the same analysis, as the live agent version.

```
docker run --rm ns1labs/pktvisor pktvisor-pcap --help
```

```shell
./pktvisor-x86_64.AppImage pktvisor-pcap --help
```

```

    Usage:
      pktvisor-pcap [options] PCAP
      pktvisor-pcap (-h | --help)
      pktvisor-pcap --version

    Summarize a pcap file. The result will be written to stdout in JSON format, while console logs will be printed
    to stderr.

    Options:
      --max-deep-sample N   Never deep sample more than N% of streams (an int between 0 and 100) [default: 100]
      --periods P           Hold this many 60 second time periods of history in memory. Use 1 to summarize all data. [default: 5]
      -h --help             Show this screen
      --version             Show version
      -v                    Verbose log output
      -b BPF                Filter packets using the given BPF string
      --geo-city FILE       GeoLite2 City database to use for IP to Geo mapping (if enabled)
      --geo-asn FILE        GeoLite2 ASN database to use for IP to ASN mapping (if enabled)
      -H HOSTSPEC           Specify subnets (comma separated) to consider HOST, in CIDR form. In live capture this /may/ be detected automatically
                            from capture device but /must/ be specified for pcaps. Example: "10.0.1.0/24,10.0.2.1/32,2001:db8::/64"
                            Specifying this for live capture will append to any automatic detection.

```

You can use the docker container by passing in a volume referencing the directory containing the pcap file. The standard
output will contain the JSON summarization output, which you can capture or pipe into other tools, for example:
```

$ docker run --rm -v /pktvisor/src/tests/fixtures:/pcaps ns1labs/pktvisor pktvisor-pcap /pcaps/dns_ipv4_udp.pcap | jq .

[2021-03-11 18:45:04.572] [pktvisor] [info] Load input plugin: PcapInputModulePlugin dev.visor.module.input/1.0
[2021-03-11 18:45:04.573] [pktvisor] [info] Load handler plugin: DnsHandler dev.visor.module.handler/1.0
[2021-03-11 18:45:04.573] [pktvisor] [info] Load handler plugin: NetHandler dev.visor.module.handler/1.0
...
processed 140 packets
{
  "5m": {
    "dns": {
      "cardinality": {
        "qname": 70
      },
      "period": {
        "length": 6,
        "start_ts": 1567706414
      },
      "top_nxdomain": [],
      "top_qname2": [
        {
          "estimate": 140,
          "name": ".test.com"
        }
      ],
...     
```

The AppImage can access local files as any normal binary:

```

$ ./pktvisor-x86_64.AppImage pktvisor-pcap /pcaps/dns_ipv4_udp.pcap | jq .

[2021-03-11 18:45:04.572] [pktvisor] [info] Load input plugin: PcapInputModulePlugin dev.visor.module.input/1.0
[2021-03-11 18:45:04.573] [pktvisor] [info] Load handler plugin: DnsHandler dev.visor.module.handler/1.0
[2021-03-11 18:45:04.573] [pktvisor] [info] Load handler plugin: NetHandler dev.visor.module.handler/1.0
...
processed 140 packets
{
  "5m": {
    "dns": {
      "cardinality": {
        "qname": 70
      },
      "period": {
        "length": 6,
        "start_ts": 1567706414
      },
      "top_nxdomain": [],
      "top_qname2": [
        {
          "estimate": 140,
          "name": ".test.com"
        }
      ],
...     
```

### Metrics Collection

#### Metrics from the REST API

The metrics are available from the agent in JSON format via the [REST API](#rest-api).

For most use cases, you will want to collect the most recent full 1-minute bucket, once per minute:

```
curl localhost:10853/api/v1/metrics/bucket/1
```

This can be done with tools like [telegraf](https://docs.influxdata.com/telegraf/) and
the [standard HTTP plugin](https://github.com/influxdata/telegraf/blob/release-1.17/plugins/inputs/http/README.md).
Example telegraf config snippet:

```

[inputs]
[[inputs.http]]
urls = [ "http://127.0.0.1:10853/api/v1/metrics/bucket/1",]
interval = "60s"
data_format = "json"
json_query = "1m"
json_time_key = "period_start_ts"
json_time_format = "unix"
json_string_fields = [
  "dns_*",
  "packets_*",
]

[inputs.http.tags]
t = "pktvisor"
interval = "60"

```

#### Prometheus Metrics

`pktvisord` also has native Prometheus support, which you can enable by passing `--prometheus`. The metrics are
available for collection at the standard `/metrics` endpoint.

```shell
$ ./pktvisor-x86_64.AppImage pktvisord -d --prometheus eth0
$ curl localhost:10853/metrics
# HELP dns_wire_packets_udp Total DNS wire packets received over UDP (ingress and egress)
# TYPE dns_wire_packets_udp gauge
dns_wire_packets_udp{instance="node"} 28
# HELP dns_rates_total Rate of all DNS wire packets (combined ingress and egress) per second
# TYPE dns_rates_total summary
dns_rates_total{instance="node",quantile="0.5"} 0
dns_rates_total{instance="node",quantile="0.9"} 4
dns_rates_total{instance="node",quantile="0.95"} 4
...
```

You can set the `instance` label by passing `--prom-instance ID`

If you are interested in centralized collection
using [remote write](https://prometheus.io/docs/operating/integrations/#remote-endpoints-and-storage), including to
cloud providers, there is a [docker image available](https://hub.docker.com/r/ns1labs/pktvisor-prom-write) to make this
easy. See [centralized_collection/prometheus](centralized_collection/prometheus) for more.

### REST API

REST API documentation is available in [OpenAPI Format](https://app.swaggerhub.com/apis/ns1labs/pktvisor/3.0.0-oas3)

Please note that the administration control plane API (`--admin-api`) is currently undergoing heavy iteration and so is
not yet documented. If you have a use case that requires the administration API, please [contact us](#contact-us) to
discuss.

### Advanced Agent Example

Starting the collector agent from Docker with MaxmindDB GeoIP/GeoASN support and using the Host option to identify
ingress and egress traffic:

```
docker run --rm --net=host -d \
    --mount type=bind,source=/opt/geo,target=/geo \
    ns1labs/pktvisor pktvisord \
    --geo-city /geo/GeoIP2-City.mmdb \
    --geo-asn /geo/GeoIP2-ISP.mmdb \
    -H 192.168.0.54/32,127.0.0.1/32 \
    eth0
```

The same command with AppImage and logging to syslog:

```
./pktvisor-x86_64.AppImage pktvisord -d --syslog \
    --geo-city /geo/GeoIP2-City.mmdb \
    --geo-asn /geo/GeoIP2-ISP.mmdb \
    -H 192.168.0.54/32,127.0.0.1/32 \
    eth0
```

### Further Documentation

We recognize the value of first class documentation, and we are working on further documentation including expanded and
updated REST API documentation, internal documentation for developers of input and handler modules (and those who want
to contribute to pktvisor), and a user manual.

Please [contact us](#contact-us) if you have any questions on installation, use, or development.