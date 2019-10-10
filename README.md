# Abacus Inferno Docker Testing Environment

This repository contains Docker Compose configuration to run the full Inferno testing environment for the Abacus FHIR project.

## Quick Start

1. See [Inferno section](#Inferno) below for instructions on building
2. See [Bulk Data Proxy section](#FHIR%20Bulk%20Data%20Proxy) below for instructions on building
3. Run from the root directory of this repository:

```sh
docker-compose up -d
```

## Components

This Docker Compose includes the following components:
* [Abacus Inferno Fork](https://git.codev.mitre.org/projects/ABA/repos/cypress-inferno/browse)
* [CQF Ruler](https://hub.docker.com/u/contentgroup)
* [NGINX](https://hub.docker.com/_/nginx) NDJSON File Server
* [Keycloak Server](https://hub.docker.com/r/jboss/keycloak/)
* [Bellese Bulk Data Proxy](https://github.com/DBCG/connectathon/tree/master/fhir-bulk-proxy)

## Inferno

### Build

This relies on an image that must be built locally, because the image is not yet hosted anywhere. This can be done by cloning the [source code repository](https://git.codev.mitre.org/projects/ABA/repos/cypress-inferno/browse), changing into the root directory of the repository, and running:

```sh
docker-compose build ruby_server
```

### Usage

The Inferno and CQF Ruler containers will be able to communicate, as they are on the same Docker network. Within that network, the CQF Ruler container will be given the hostname alias `cqf_ruler`. If you want to point Inferno at this instance of CQF Ruler, the URL you enter should look like `http://cqf_ruler:8080/cqf-ruler-dstu3`. 

## CQF Ruler

For STU3, the base URL is `http://<host>:8080/cqf-ruler-dstu3/`.
For R4, the base URL is `http://<host>:8080/cqf-ruler-r4/`.

## NDJSON File Server

This serves the files at `http://<host>:7070/<file-name>`.

## Keycloak

The admin dashboard can be accessed at `http://<host>:9090/auth/admin/`.

Default admin credentials are: Username: `admin`, Password: `Pa55w0rd`.

## FHIR Bulk Data Proxy

### Build

Clone the `connectathon` repo

``` bash
git clone https://github.com/DBCG/connectathon.git
```

Change into the `fhir-bulk-proxy` directory and build the image

``` bash
cd fhir-bulk-proxy
docker-compose build
```

**NOTE**: If building the proxy on the MITRE network, add the following lines to the top of the `connectathon/fhir-bulk-proxy/Dockerfile` just before the line `WORKDIR $GOPATH/src/github.com/fhir-bulk-proxy`:

```
WORKDIR /usr/local/share/ca-certificates
ADD http://pki.mitre.org/MITRE%20BA%20Root.crt ./
ADD http://pki.mitre.org/MITRE%20BA%20NPE%20CA-3.crt ./
ADD http://pki.mitre.org/MITRE%20BA%20NPE%20CA-4.crt ./
RUN update-ca-certificates
```

### Usage

To use the proxy during an Inferno sequence, point the Inferno instance at `http://fhir-bulk-proxy:5000` when selecting a module, then run the bulk data reporting sequence
