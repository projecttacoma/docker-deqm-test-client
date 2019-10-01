# Abacus Inferno Docker Testing Environment

This repository contains Docker Compose configuration to run the full Inferno testing environment for the Abacus FHIR project.

## Quick Start

1. See Inferno section below for instructions on building
2. Run from the root directory of this repository:

```sh
docker-compose up -d
```

## Components

This Docker Compose includes the following components:
* [Abacus Inferno Fork](https://git.codev.mitre.org/projects/ABA/repos/cypress-inferno/browse)
* [CQF Ruler](https://hub.docker.com/u/contentgroup)
* [NGINX](https://hub.docker.com/_/nginx) NDJSON File Server
* [Keycloak Server](https://hub.docker.com/r/jboss/keycloak/)


## Inferno

This relies on an image that must be built locally, because the image is not yet hosted anywhere. This can be done by cloning the [source code repository](https://git.codev.mitre.org/projects/ABA/repos/cypress-inferno/browse), changing into the root directory of the repository, and running:

```sh
docker-compose build ruby_server
```

## CQF Ruler

For STU3, the base URL is `http://<host>:8080/cqf-ruler-dstu3/`.
For R4, the base URL is `http://<host>:8080/cqf-ruler-r4/`.

## NGINX

This serves the files at `http://<host>:7070/<file-name>`.

## Keycloak

The admin dashboard can be accessed at `http://<host>:9090/auth/admin/`.

Default admin credentials are: Username: `admin`, Password: `Pa55w0rd`.