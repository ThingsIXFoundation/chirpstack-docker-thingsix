# ChirpStack Docker example

This repository contains a skeleton to setup the [ChirpStack](https://www.chirpstack.io)
open-source LoRaWAN Network Server (v4) using [Docker Compose](https://docs.docker.com/compose/) as a 
network server for the ThingsIX network together with the [ThingsIX Router](https://github.com/ThingsIXFoundation/packet-handling).

This repository is based on the original [ChirpStack docker repository](https://github.com/chirpstack/chirpstack-docker).

> :warning: Please use this `docker-compose.yml` file as a starting point for testing
but keep in mind that for production usage this most probably will not work. Especially
for the ThingsIX network you possibly receive traffic from thousands of gateways. A
single server may not be enough. 

## Directory layout

* `docker-compose.yml`: the docker-compose file containing the services
* `configuration/thingsix-router`: directory containing the ThingsIX router configuration files
* `configuration/chirpstack`: directory containing the ChirpStack configuration files
* `configuration/mosquitto`: directory containing the Mosquitto (MQTT broker) configuration
* `configuration/postgresql/initdb/`: directory containing PostgreSQL initialization scripts

## Getting started
Before starting, make sure you have a LoRaWAN NetID and have configured it in `configuration/chirpstack/chirpstack.toml` in the `[network]` section. If you have this configured e-mail `info@thingsix.com` with:
- Your NetID
- The address (FQDN) + port of your router

If you don't have a NetID yet:

- If you are a LoRa Alliance member: Request a NetID allocation from your contact
- If you are not a LoRa Alliance member: Please see the details under 'non-member' section of the [LoRa Alliance page on LoRaWAN](https://lora-alliance.org/lorawan-coverage/) to get a NetID allocation (requires a yearly payment, at the time of writing is 500 USD per year, for a block of 16 Type 7 NetIDs). Or consider becoming a LoRa Alliance member. 

Make sure you have `docker` and `docker-compose` installed. 

After you have changed the config file, run 

```bash
$ docker-compose up
```
To start all services.

After all the components have been initialized and started, you should be able
to open `http://<ip-of-server>:8080/` in your browser.  The example includes the [ChirpStack REST API](https://github.com/chirpstack/chirpstack-rest-api). You should be able to access the UI by opening `http://<ip-of-server>:8090/` in your browser.

Login using the default credentials `admin` as both username and password **and directly change them**. Go to the `API keys` section under `Network Server` (not the one under `Tenant`). Add an API-key (we use `thingsix-router` as name) and click submit. Copy the value over `configuration/thingsix-router/config.yaml` in the `api_key` field  under `chirpstack`. This API-key is used by the ThingsIX router to get the DevEUIs/JoinEUIs of devices and build a filter that is communicated to ThingsIX forwards for routing Join packets.

Next make sure to open port 3200/tcp in the firewall. We recommend using a reverse-proxy with SSL/TLS such as NGINX to protect the web-interface and API of ChirpStack (running on port 8080 and 8090 respectively)

The ChirpStack in this setup is pre-configured for all regions. However the ThingsIX Router currently only supports
EU868 as is configured as such. This repository will be updated once multiple frequency plans are supported.

## Data persistence

PostgreSQL and Redis data is persisted in Docker volumes, see the `docker-compose.yml`
`volumes` definition.

## Importing TTN device repository

To import the TTN [lorawan-devices](https://github.com/TheThingsNetwork/lorawan-devices)
repository (optional step), run the following command:

```bash
make import-lorawan-devices
```

This will clone the `lorawan-devices` repository and execute the `import-ttn-lorawan-devices`
command of ChirpStack. Please note that for this step you need to have the `make` command installed.


##


**Note:** It is recommended to use the [gRPC](https://www.chirpstack.io/docs/chirpstack/api/grpc.html)
interface over the [REST](https://www.chirpstack.io/docs/chirpstack/api/rest.html) interface.
