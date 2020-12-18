# docker-compose-telegraf-influxdb-grafana

Multi-container Docker app built from the following services:

* [Telegraf](https://github.com/influxdata/telegraf) - agent for collecting, processing, aggregating, and writing metrics.
* [InfluxDB](https://github.com/influxdata/influxdb) - time series database
* [Chronograf](https://github.com/influxdata/chronograf) - admin UI for InfluxDB
* [Grafana](https://github.com/grafana/grafana) - visualization UI for InfluxDB

Useful for quickly setting up an ephemeral Model Driven Telemetry stack.  
This project has been forked from [Jeff Kehres repository](https://github.com/jkehres/docker-compose-influxdb-grafana) to add missing pieces.

## Quick Start

To start the app:

1. Install [docker-compose](https://docs.docker.com/compose/install/) on the docker host.
1. Clone this repo on the docker host.
1. Optionally, change default credentials or Grafana provisioning.
1. Run the following command from the root of the cloned repo:
```
docker-compose up -d
```

To stop the app:

1. Run the following command from the root of the cloned repo:
```
docker-compose down
```

## Ports

The services in the app run on the following ports:

| Host Port | Service |
| - | - |
| 3000 | Grafana |
| 8086 | InfluxDB |
| 57000, 57100, 57500 | Telegraf |
| 127.0.0.1:8888 | Chronograf |

Note that Chronograf does not support username/password authentication. Anyone who can connect to the service has full admin access. Consequently, the service is not publically exposed and can only be access via the loopback interface on the same machine that runs docker.

If docker is running on a remote machine that supports SSH, use the following command to setup an SSH tunnel to securely access Chronograf by forwarding port 8888 on the remote machine to port 8888 on the local machine:

```
ssh [options] <user>@<docker-host> -L 8888:localhost:8888 -N
```

## Volumes

No volume is created or used in this ephemeral stack. Data is lost on purpose when the app is stopped. If you want to retain data (metrics, dashboards, etc.)  you can check how to implement volumes on [Jeff Kehres initial project](https://github.com/jkehres/docker-compose-influxdb-grafana).


## Users

The app creates two admin users - one for InfluxDB and one for Grafana. By default, the username and password of both accounts is `admin`. To override the default credentials, set the following environment variables before starting the app:

* `INFLUXDB_USERNAME`
* `INFLUXDB_PASSWORD`
* `GRAFANA_USERNAME`
* `GRAFANA_PASSWORD`

## Database

The app creates a default InfluxDB database called `telemetry`. No retention policy has been configured as metrics disappear as soon as the container is killed.

## Data Sources

The app creates a Grafana data source called `InfluxDB` that's connected to the default IndfluxDB database (e.g. `telemetry`).

To provision additional data sources, see the Grafana [documentation](http://docs.grafana.org/administration/provisioning/#datasources) and add a config file to `./grafana-provisioning/datasources/` before starting the app.

## Dashboards

A sample dashboard is located at `./grafana-provisioning/dashboards/power.json`. This dashboard represents power consumption of several IOS-XR based device using Model Driven Telemetry with TCP and gRPC transports.

To provision additional dashboards, see the Grafana [documentation](http://docs.grafana.org/administration/provisioning/#dashboards) and add a config file to `./grafana-provisioning/dashboards/` before starting the app.
