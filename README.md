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

If outside the MITRE network, the cypress-inferno image will need to be build locally. This can be done by cloning the [source code repository](https://git.codev.mitre.org/projects/ABA/repos/cypress-inferno/browse), changing into the root directory of the repository, and running:

```sh
docker-compose build ruby_server
```

Then update the docker-compose.yml to use the built image instead of the one from artifacts.mitre.org:8200.

### Usage

The Inferno and CQF Ruler containers will be able to communicate, as they are on the same Docker network. Within that network, the CQF Ruler container will be given the hostname alias `cqf_ruler`. If you want to point Inferno at this instance of CQF Ruler, the URL you enter should look like `http://cqf_ruler:8080/cqf-ruler-dstu3`. 

## CQF Ruler

For STU3, the base URL is `http://<host>:8080/cqf-ruler-dstu3/`.
For R4, the base URL is `http://<host>:8080/cqf-ruler-r4/`.

### Preloading CQF Ruler

To generate the cqf-ruler docker image with data already on it, the following
steps were taken:

1. Run the base cqf-ruler docker container:

```
docker run --name cqf-ruler --rm -dit -p 8080:8080 contentgroup/cqf-ruler
```

2. Wait for the server to initalize and be able to accept HTTP requests:

```
until `curl --output /dev/null --silent --head --fail http://localhost:8080/cqf-ruler-dstu3`; do printf '.'; sleep 5; done
```

3. Clone the DBCG/connectathon repository. Note that a specific commit was used
   so that the filepaths are predicable, as they chance once in a while.

```
git clone https://github.com/DBCG/connectathon.git
cd connectathon && git checkout 425795fb8dd39e213140493f22e77880a9257f00
```

4. POST or PUT the resources desired to be present on the cqf-ruler image:

POST will not preserve ids for resources, but PUT will if the id matches the
resource id in the json.

Use POST for bundles and have PUT in the bundle request, i.e.:
```
"request": {
  "method": "PUT",
  "url": "Library/library-mat-global-common-functions-FHIR3-2.0.000"
}

```

Use PUT with matching id for individual resources (see measure directly below).

```
# EXM165 Measure
curl -X PUT \
http://localhost:8080/cqf-ruler-dstu3/fhir/Measure/measure-exm165-FHIR3 \
-H 'Content-Type: application/json' \
-d @connectathon/fhir3/resources/measure/measure-EXM165_FHIR3-8.5.000.json

# Libraries
curl -X POST \
http://localhost:8080/cqf-ruler-dstu3/fhir \
-H 'Content-Type: application/json' \
-d @connectathon/fhir3/resources/library/all-libraries-bundle.json

# Valuesets
curl -X POST \
http://localhost:8080/cqf-ruler-dstu3/fhir \
-H 'Content-Type: application/json' \
-d @connectathon/fhir3/resources/valuesets/valuesets-EXM165_FHIR3-8.5.000.json
```

5. Commit the changes and add the new image to the artifacts repository. Note
   that this one uses tag 0.1.1, which should be different for future
   iterations.

```
docker login artifacts.mitre.org:8200
(Enter Username/Password)

docker commit <CONTAINER_ID> # outputs IMAGE_ID

docker tag <IMAGE_ID> artifacts.mitre.org:8200/cqf-ruler-preloaded:0.1.1

docker push artifacts.mitre.org:8200/cqf-ruler-preloaded
```

## NDJSON File Server

This serves the files at `http://<host>:7070/<file-name>`.

## Keycloak

The admin dashboard can be accessed at `http://<host>:9090/auth/admin/`.

Default admin credentials are: Username: `admin`, Password: `Pa55w0rd`.

## FHIR Bulk Data Proxy

### Build

If outside the mitre network, the fhir-bulk-proxy server will need to be built locally. Clone the `connectathon` repo

``` bash
git clone https://github.com/DBCG/connectathon.git
```

Change into the `fhir-bulk-proxy` directory and build the image

``` bash
cd fhir-bulk-proxy
docker-compose build
```

Then update the docker-compose.yml to use the built image instead of the one from artifacts.mitre.org:8200.

### Usage

To use the proxy during an Inferno sequence, point the Inferno instance at `http://fhir-bulk-proxy:5000` when selecting a module, then run the bulk data reporting sequence
