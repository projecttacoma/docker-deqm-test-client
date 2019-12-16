# Abacus Inferno Docker Testing Environment

This repository contains Docker Compose configuration to run the full Inferno testing environment for the Abacus FHIR project.

## Quick Start

1. Install [Docker](https://www.docker.com/) for the host platform as well as the [docker-compose](https://docs.docker.com/compose/install/) tool (which may be included in the distribution, as is the case for Windows and MacOS).
2. Run from the root directory of this repository:

```sh
docker-compose up -d
```

or if using the MITRE docker-compose file:

```sh
docker-compose -f docker-compose-mitre.yml up -d
```

## Components

This Docker Compose includes the following components:
* [Abacus Inferno Fork](https://github.com/projecttacoma/deqm-test-client)
* [CQF Ruler](https://hub.docker.com/u/contentgroup)


## Usage

The Inferno and CQF Ruler containers will be able to communicate, as they are on the same Docker network. Within that network, the CQF Ruler container will be given the hostname alias `cqf_ruler`. If you want to point Inferno at this instance of CQF Ruler, the URL you enter should look like `http://cqf_ruler:8080/cqf-ruler-dstu3`. 

## Inferno

For usage instructions for Inferno see [Inferno Usage Guide](https://github.com/onc-healthit/inferno/wiki/Quick-Start-Guide).

## CQF Ruler

For STU3, the base URL is `http://<host>:8080/cqf-ruler-dstu3/`.
For R4, the base URL is `http://<host>:8080/cqf-ruler-r4/`.
