# ChirpStack Docker example

This repository contains a skeleton to setup the [ChirpStack](https://www.chirpstack.io)
open-source LoRaWAN Network Server (v4) using [Docker Compose](https://docs.docker.com/compose/).

**Note:** Please use this `docker-compose.yml` file as a starting point for testing
but keep in mind that for production usage it might need modifications. 

Includes IoT tool for node-red and homeassistant

## Directory layout

* `docker-compose.yml`: the docker-compose file containing the services
* `configuration/chirpstack`: directory containing the ChirpStack configuration files
* `configuration/chirpstack-gateway-bridge`: directory containing the ChirpStack Gateway Bridge configuration
* `configuration/mosquitto`: directory containing the Mosquitto (MQTT broker) configuration
* `configuration/postgresql/initdb/`: directory containing PostgreSQL initialization scripts

## Configuration

This setup is pre-configured for all regions. You can either connect a ChirpStack Gateway Bridge
instance (v3.14.0+) to the MQTT broker (port 1883) or connect a Semtech UDP Packet Forwarder.
Please note that:

* You must prefix the MQTT topic with the region
  Please see the region configuration files in the `configuration/chirpstack` for a list
  of topic prefixes (e.g. eu868, us915_0, au915, as923_2, ...).
* The protobuf marshaler is configured.

This setup also comes with a ChirpStack Gateway Bridge instance which is configured to the
eu868 topic prefix. You can connect your UDP packet-forwarder based gateway to port 1700.

# Data persistence

PostgreSQL and Redis data is persisted in Docker volumes, see the `docker-compose.yml`
`volumes` definition.

## Requirements

Before using this `docker-compose.yml` file, make sure you have [Docker](https://www.docker.com/community-edition)
installed. 

engine https://docs.docker.com/engine/install/
desktop https://docs.docker.com/get-docker/
compose https://docs.docker.com/compose/install/

##Backup:

docker save $(docker images -q) -o /path/to/save/mydockersimages.tar

##Remove everything

docker container stop $(docker container ls -aq) && docker container rm -f $(docker container ls -aq) && docker rmi -f $(docker images -aq) && docker volume prune && docker network prune

##Create a directory for the repo composition

mkdir ~/docker && cd ~/docker

##Clone git to working docker directory

git clone https://github.com/GrayHatGuy/chirpstack-iot-tools-docker.git
cd chirpstack-iot-tools-docker

## Importing device repository (TBD)

To import the [lorawan-devices](https://github.com/TheThingsNetwork/lorawan-devices)
repository (optional step), run the following command:

```bash
make import-lorawan-devices
```

This will clone the `lorawan-devices` repository and execute the import command of ChirpStack.
Please note that for this step you need to have the `make` command installed.

**Note:** an older snapshot of the `lorawan-devices` repository is cloned as the
latest revision no longer contains a `LICENSE` file.

## Usage

To start the ChirpStack simply run:

```bash
$ docker compose up -d
```

After all the components have been initialized and started, you should be able
to open http://localhost:8080/ in your browser.

## Add a gateway
Prerequisites
	Setup gateway packet forwarder per OEM.  Example RAK7246
	Set region gateway server and UDP port settings and channel plan in 
	sudo gateway-config
	Point the configuration file for your chirpstack-gateway-server container IP and port 1700.
Application/Network Server
	http://localhost:8080 localhost is the machine running docker.
	Log in to chirpstack application portal.
ssh into the gateway and find gatewayID using	

sudo gateway-version

Add new and update gateway id in portal gui then wait 30-60 seconds to connect.
See LoRaWAN frame tab for details the gateway will also appear on the dashboard.

IoT tools accessible ports
	Grafana
http://localhost:1880/
	node-red 
http://localhost:1800
	homeassistant
http://localhost:1800
  REST/API
http://localhost:8090/

Minimum required ports for WAN access
8080 8090 1700 1883

Localnet only ports until TLS added
1880, 8123, 3000

##

The example includes the [ChirpStack REST API](https://github.com/chirpstack/chirpstack-rest-api).
You should be able to access the UI by opening http://localhost:8090 in your browser.

**Note:** It is recommended to use the [gRPC](https://www.chirpstack.io/docs/chirpstack/api/grpc.html)
interface over the [REST](https://www.chirpstack.io/docs/chirpstack/api/rest.html) interface.
