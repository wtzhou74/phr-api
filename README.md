# Patient Health Record  API

The Patient Health Record (PHR) API is responsible for storing patient identities in the Consent2Share (C2S) domain, and for serving patient health records from the health information exchange (HIE) via the Information Exchange Hub (IExHub). Currently, only patient demographics and C2S Medical Record Numbers (MRN) are persisted in PHR domain.

## Build

### Prerequisites

+ [Oracle Java JDK 8 with Java Cryptography Extension (JCE) Unlimited Strength Jurisdiction Policy](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
+ [Docker Engine](https://docs.docker.com/engine/installation/) (for building a Docker image from the project)

### Commands

This is a Maven project and requires [Apache Maven](https://maven.apache.org/) 3.3.3 or greater to build it. It is recommended to use the *Maven Wrapper* scripts provided with this project. *Maven Wrapper* requires an internet connection to download Maven and project dependencies for the very first build.

To build the project, navigate to the folder that contains `pom.xml` file using the terminal/command line.

+ To build a JAR:
    + For Windows, run `mvnw.cmd clean install`
    + For *nix systems, run `mvnw clean install`
+ To build a Docker Image (this will create an image with `bhits/phr:latest` tag):
    + For Windows, run `mvnw.cmd clean package docker:build`
    + For *nix systems, run `mvnw clean package docker:build`

## Run

### Prerequisites

This API uses *[MySQL](https://www.mysql.com/)* for persistence and *[Flyway](https://flywaydb.org/)* for database migration. It requires having a database user account with Object and DDL Rights to a schema with default name `phr`. Please see [Configure](#configure) section for details of configuring the data source.

### Commands

This is a [Spring Boot](https://projects.spring.io/spring-boot/) project and serves the API via an embedded Tomcat instance. Therefore, there is no need for a separate application server to run this service.
+ Run as a JAR file: `java -jar phr-x.x.x-SNAPSHOT.jar <additional program arguments>`
+ Run as a Docker Container: `docker run -d bhits/phr:latest <additional program arguments>`

*NOTE: In order for this API to fully function as a microservice in the C2S application, it is also required to setup the dependency microservices and support level infrastructure. Please refer to the [Consent2Share Deployment Guide](https://github.com/bhits/consent2share/releases/download/2.0.0/c2s-deployment-guide.pdf) for instructions to setup the C2S infrastructure.*

## Configure

This API runs with some default configuration that is primarily targeted for development environment. However, [Spring Boot](https://projects.spring.io/spring-boot/) supports several methods to override the default configuration to configure the API for a certain deployment environment.

Please see the [default configuration](phr/src/main/resources/application.yml) for this API as a guidance and override the specific configuration per environment as needed. Also, please refer to [Spring Boot Externalized Configuration](http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) documentation to see how Spring Boot applies the order to load the properties and [Spring Boot Common Properties](http://docs.spring.io/spring-boot/docs/current/reference/html/common-application-properties.html) documentation to see the common properties used by Spring Boot.

### Examples for Overriding a Configuration in Spring Boot

#### Override a Configuration Using Program Arguments While Running as a JAR:

+ `java -jar phr-x.x.x-SNAPSHOT.jar --server.port=80 --spring.datasource.password=strongpassword`

#### Override a Configuration Using Program Arguments While Running as a Docker Container:

+ `docker run -d bhits/phr:latest --server.port=80 --spring.datasource.password=strongpassword`

+ In a `docker-compose.yml`, this can be provided as:
```yml
version: '2'
services:
...
  phr.c2s.com:
    image: "bhits/phr:latest"
    command: ["--server.port=80","--spring.datasource.password=strongpassword"]
...
```
*NOTE: Please note that these additional arguments will be appended to the default `ENTRYPOINT` specified in the `Dockerfile` unless the `ENTRYPOINT` is overridden.*

### Enable SSL

For simplicity in development and testing environments, SSL is **NOT** enabled by default configuration. SSL can easily be enabled following the examples below:

#### Enable SSL While Running as a JAR

+ `java -jar phr-x.x.x-SNAPSHOT.jar --spring.profiles.active=ssl --server.ssl.key-store=/path/to/ssl_keystore.keystore --server.ssl.key-store-password=strongkeystorepassword`

#### Enable SSL While Running as a Docker Container

+ `docker run -d -v "/path/on/dockerhost/ssl_keystore.keystore:/path/to/ssl_keystore.keystore" bhits/phr:latest --spring.profiles.active=ssl --server.ssl.key-store=/path/to/ssl_keystore.keystore --server.ssl.key-store-password=strongkeystorepassword`
+ In a `docker-compose.yml`, this can be provided as:
```yml
version: '2'
services:
...
  phr.c2s.com:
    image: "bhits/phr:latest"
    command: ["--spring.profiles.active=ssl","--server.ssl.key-store=/path/to/ssl_keystore.keystore", "--server.ssl.key-store-password=strongkeystorepassword"]
    volumes:
      - /path/on/dockerhost/ssl_keystore.keystore:/path/to/ssl_keystore.keystore
...
```

*NOTE: As seen in the examples above, `/path/to/ssl_keystore.keystore` is made available to the container via a volume mounted from the Docker host running this container.*

### Override Java CA Certificates Store In Docker Environment

Java has a default CA Certificates Store that allows it to trust well-known certificate authorities. For development and testing purposes, one might want to trust additional self-signed certificates. In order to override the default Java CA Certificates Store in a Docker container, one can mount a custom `cacerts` file over the default one in the Docker image as `docker run -d -v "/path/on/dockerhost/to/custom/cacerts:/etc/ssl/certs/java/cacerts" bhits/phr:latest`

*NOTE: The `cacerts` references given regarding volume mapping above are files, not directories.*

[//]: # (## API Documentation)

[//]: # (## Notes)

[//]: # (## Contribute)

## Contact

If you have any questions, comments, or concerns please see [Consent2Share](https://bhits.github.io/consent2share/) project site.

## Report Issues

Please use [GitHub Issues](https://github.com/bhits/phr-api/issues) page to report issues.

[//]: # (License)
