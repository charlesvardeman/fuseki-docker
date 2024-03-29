## Docker Container for Jena-Fuseki
Docker container build workflow for Jena-Fuseki forked from [blankdots](https://hub.docker.com/r/blankdots/jena-fuseki) configuration.

### SHACL service operation
In Jena-Fuseki 3.13.0 SHACL validation has been added as an option for a service endpoint. [Instructions](https://jena.apache.org/documentation/shacl/) are available for configuring the endpoint in the Fuseki configuration file. The /ds endpoint is configured for SHACL validation.

To upload data set fu:

```
curl -XPOST --data-binary @fu-data.ttl    \  
     --header 'Content-type: text/turtle' \  
     'http://localhost:3030/ds?default'
```

Validate with shapes in fu-shapes.ttl and get back a validation report:
```
curl -XPOST --data-binary @fu-shapes.ttl  \  
     --header 'Content-type: text/turtle' \  
     'http://localhost:3030/ds/shacl?graph-default'
```


## Original Readme updated for Jena-Fuseki 3.13.0
Current container is based on https://registry.hub.docker.com/u/stain/jena-fuseki/

In order to make it more production friendly it adds:
* gosu (running with limited permissions as non-root user) - https://github.com/tianon/gosu
* configuration loader from file - and it provides two endpoints; one for test and one for working with
* FUSEKI 3.13.0

### Run image

Skip the build and just run with plain configuration:
* `docker pull blankdots/jena-fuseki`
* `docker run -p 3030:3030 blankdots/jena-fuseki` vanilla run
* `docker run -p 3030:3030 -v /data/fuseki:/data/fuseki/fuseki_DB blankdots/jena-fuseki` run with volume attached
* `docker run -p 3030:3030 -v /data/fuseki:/data/fuseki/fuseki_DB -d blankdots/jena-fuseki` run volume attached in detached mode
* `docker run -p 3030:3030 -v /data/fuseki:/data/fuseki/fuseki_DB -e ADMIN_PASSWORD=pass blankdots/jena-fuseki` preset the password.

**A password will be generated if one not provided at runtime (see container logs).**

For other parameters see: https://hub.docker.com/r/stain/jena-fuseki/

### Build and Configuration Instructions

The configuration of the store is done via the `config.ttl` file. which contains two datasets `/test` (in memory) and `/ds` (persistent on disk if the image is run with a volume attached) and provides two named graphs.

TO DO: provide other configuration scenarios. Some envisioned scenarios could be envisioned where:
* an endpoint is used for just the sparql queries and there is one for uploading the data and materialising the results in the SPARQL endpoint;
* there is a unuion model type of endpoint which provides via SPARQL materialised results of an inference type of endpoint (which only works in memory);
* an endpoint that provides search based like functionalities;
* etc.

Data asscoiated to the configuration is present in the `data` folder.

Once the configuration and associated data building the image is as follows:
* `docker build -t blankdots/jena-fuseki .`
