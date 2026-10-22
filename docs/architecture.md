# Architecture

## Components

| Component | Responsibility |
|---|---|
| Debian host | Runs Docker and provides host resources |
| Node Exporter | Exposes supported host CPU, memory, and filesystem metrics |
| Blackbox Exporter | Performs configured TCP connectivity probes |
| Prometheus | Scrapes targets and stores time-series metrics |
| Grafana | Queries Prometheus and renders dashboards |
| SMB service | Target checked on TCP port 445 |

## Data Flow

1. Node Exporter exposes host metrics on port 9100 inside the monitoring network.
2. Prometheus scrapes Node Exporter and its own metrics endpoint.
3. Prometheus requests a TCP probe of `host.docker.internal:445` through Blackbox Exporter using the `tcp_connect` module.
4. Blackbox Exporter returns probe results and latency metrics.
5. Grafana queries Prometheus using PromQL and renders the provisioned dashboard.
6. Grafana and Prometheus web interfaces bind to host loopback addresses by default.

## Limitations

- A successful TCP probe does not verify SMB authentication or file operations.
- Filesystem metrics describe the filesystem visible to Node Exporter. If the NAS directory shares the host root filesystem, it is not an independent filesystem capacity measurement.
- Alert rules and notification delivery require separate configuration and testing.
