# Docker Image with Telegraf, InfluxDB and Grafana
This is a Docker image based on the [Docker Image with Telegraf, InfluxDB and Grafana] from [Arthur Kono](https://github.com/artlov/docker-telegraf-influxdb-grafana) The main purpose of this image is to be used for personal lab.

The main points of this image are:

* Persistence is supported via mounting volumes to a Docker container.
* Grafana will store its data in SQLite files instead of a MySQL table on the container, so MySQL is not installed.

## Versions

# Default versions
ENV TELEGRAF_VERSION 1.16.3-1
ENV INFLUXDB_VERSION 1.8.3
ENV GRAFANA_VERSION  7.3.5
ENV CHRONOGRAF_VERSION 1.8.9.1

## Quick Start

To start the container the first time launch:

```sh
docker run --ulimit nofile=66000:66000 -d \
  --name telegraf-influxdb-grafana \
  --hostname telegraf-influxdb-grafana \
  -p 3003:3003 \
  -p 3004:8888 \
  -p 8086:8086 \
  -p 22022:22 \
  -p 8125:8125/udp \
  -v /opt/Docker/telegraf-influxdb-grafana/influxdb:/var/lib/influxdb \
  -v /opt/Docker/telegraf-influxdb-grafana/grafana:/var/lib/grafana \
  -e TZ=America/Chicago \
  kellosmith/telegraf-influxdb-grafana
```

You can replace `latest` with the desired version listed in changelog file.
Please replace the variable `TZ` with your local time zone or remove this line at all to stay in the UTC time zone.

To stop the container launch:

```sh
docker stop telegraf-influxdb-grafana
```

To start the container again launch:

```sh
docker start telegraf-influxdb-grafana
```

## Mapped Ports

```
Host		Container	Service

3003		3003		grafana
3004		8888		influxdb-admin (chronograf)
8086		8086		influxdb HTTP API port
8125		8125		telegraf
22022		22		sshd
```
## SSH

```sh
ssh root@localhost -p 22022
```
By default root password login disabled. If ssh connection needed, please configure key authentication.

Access from docker host:
```
docker exec -it <container id> bash
```

## Grafana

Open <http://localhost:3003>

```
Username: root
Password: root
```

### Add data source on Grafana

1. Using the wizard click on `Add data source`
2. Choose a `name` for the source and flag it as `Default`
3. Choose `InfluxDB` as `type`
4. Choose `direct` as `access`
5. Fill remaining fields as follows and click on `Add` without altering other fields

```
Url: http://localhost:8086
Database:	telegraf
User:		telegraf
Password:	telegraf
```

Basic auth and credentials must be left unflagged. Proxy is not required.

Now you are ready to add your first dashboard and launch some query on database.

## InfluxDB

### Chronograf Web Interface

Open <http://localhost:3004>

InfluxDB Connection:

```
Username: root
Password: root
Port: 8086
```

### InfluxDB Shell (CLI)

1. Establish a ssh or docker exec connection with the container
2. Launch `influx` to open InfluxDB Shell (CLI)
