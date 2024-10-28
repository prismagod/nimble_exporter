
# Nimble Exporter

## Introduction

Nimble Exporter is a tool designed to collect Secure Reliable Transport (SRT) metrics from a Nimble Server instance and expose them to Prometheus. This exporter enables comprehensive monitoring of streaming services, facilitating performance analysis and troubleshooting. Pre-configured Grafana dashboards are provided to visualize the collected metrics effectively.

## Table of Contents

1.  [Installation](#installation)
    -   [Building from Source](#building-from-source)
    -   [Docker Deployment](#docker-deployment)
    -   [Kubernetes Deployment](#kubernetes-deployment)
2.  [Configuration and Execution](#configuration-and-execution)
    -   [Command-Line Parameters](#command-line-parameters)
3.  [Prometheus Integration](#prometheus-integration)
4.  [Grafana Dashboard Configuration](#grafana-dashboard-configuration)
5.  [References](#references)


## Installation

Nimble Exporter can be installed using one of the following methods: building from source, deploying with Docker, or deploying within a Kubernetes cluster.

### Building from Source

To build the exporter from source using Go, execute the following commands:

``` bash
git clone https://github.com/deckhouse/nimble_exporter.git
cd nimble_exporter
go build -o nimble_exporter main.go 
```

Ensure that Go is installed and properly configured on your system before building the exporter.

### Docker Deployment

To build and run the exporter using Docker, use the following commands:

```bash
docker build -t nimble_exporter github.com/deckhouse/nimble_exporter
docker run -d -p 9017:9017 nimble_exporter
```

This process builds a Docker image named `nimble_exporter` and runs it as a detached container, exposing port `9017` for metric collection.

### Kubernetes Deployment

For deployment in a Kubernetes environment, include the exporter in your deployment configuration as shown below:

```yaml
      - name: srt-exporter
        image: {{ $.Values.werf.image.exporter }}
        command:
        - /nimble_exporter
        - -auth_salt=590
        - -auth_hash=xxxx
        ports:
        - containerPort: 9017
          name: exporter
          protocol: TCP
        resources:
          {{ toYaml $.Values.resources.exporter |  nindent 10 }}
```

## Configuration and Execution

To run the exporter, execute the binary with the required command-line parameters:

```bash
./nimble_exporter -listen ":9017" -nimble "http://127.0.0.1:8082"
```
### Command-Line Parameters

The exporter accepts the following command-line flags:

-   `-listen`: Specifies the listening address (default is `:9017`).
    -   Usage: `-listen <address>`
-   `-nimble`: Specifies the Nimble server address (default is `http://127.0.0.1:8082`).
    -   Usage: `-nimble <address>`
-   `-auth_salt`: Sets the authentication salt (default is empty).
    -   Usage: `-auth_salt <salt>`
-   `-auth_hash`: Sets the authentication hash (default is empty).
    -   Usage: `-auth_hash <hash>`
-   `-loglevel`: Specifies the log level (default is `info`).
    -   Usage: `-loglevel <level>`
   
## Prometheus Integration

To integrate the exporter with Prometheus, add the following job configuration to your `prometheus.yml` file:

```yaml
scrape_configs:
  - job_name: 'nimble_exporter'
    static_configs:
      - targets: ['localhost:9017']` 
```
Replace `localhost:9017` with the appropriate address and port if the exporter is hosted on a different server or within a container.

## Grafana Dashboard Configuration

Pre-configured Grafana dashboards can be used to monitor various performance and health metrics. 

To import the dashboards:

1.  Download nimble.json or nimble-external.json from [Grafana Dashboards directory](https://github.com/deckhouse/nimble_exporter/tree/main/monitoring/grafana-dashboards).
2.  In your Grafana instance, navigate to the **Dashboards** section and select **Import**.

## References

-   **Prometheus Documentation**: https://prometheus.io/docs
-   **Grafana Documentation**: https://grafana.com/docs

----------

By adhering to this documentation, you can effectively deploy the Nimble Exporter, integrate it with Prometheus, and utilize Grafana dashboards for comprehensive monitoring of your Nimble Server's SRT metrics.
