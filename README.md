Quarkus IBM MQ - AMQP 1.0 Support Demo
============================

This project modifies the [Quarkus AMQP 1.0 Quickstart](https://quarkus.io/guides/amqp) to serve as an example of using an IBM MQ queue manager with AMQP 1.0 support with Quarkus and Smallrye Reactive Messaging.

## Build and start the IBM MQ queue manager with AMQP support enabled

If you're following the IBM MQ tutorial on reactive applications with Quarkus, set up a queue manager by following Step 1 in [this IBM Developer tutorial](https://developer.ibm.com/tutorials/mq-running-ibm-mq-apps-on-quarkus-and-graalvm-using-qpid-amqp-jms-classes/#step-1-set-up-the-amqp-channel-in-ibm-mq), where you build the IBM MQ image with AMQP enabled.

You can check the container is working with `docker ps`. Ensure you can see a port mapping to port 5672.

Make sure both applications have `amqp-host` and `amqp-port` configuration properties are set to the running MQ container.

## Start the application in dev mode

In a first terminal, run:

```bash
> mvn -f amqp-quickstart-producer quarkus:dev
```

In a second terminal, run:

```bash
> mvn -f amqp-quickstart-processor quarkus:dev
```  

Then, open your browser to `http://localhost:8080/quotes.html`, and click on the "Request Quote" button.

## Build the application in JVM mode

To build the applications, run:

```bash
> mvn -f amqp-quickstart-producer  clean package -DskipTests
> mvn -f amqp-quickstart-processor clean package -DskipTests 
```

The [docker-compose.yml](docker-compose.yml) file starts the broker and your application.

If you ran an MQ queue manager container to use to build the application in developer mode, stop it now as otherwise the docker-compose file will be unable to bind to the AMQP port.

Start the broker and the applications using:

```bash
> docker compose up --build
```

Then, open your browser to `http://localhost:8080/quotes.html`, and click on the "Request Quote" button.
 

## Build the application in native mode

To build the applications into native executables, install GraalVM [from the GraalVM site](https://www.graalvm.org/22.0/docs/getting-started/#install-graalvm/). You may need to set your PATH and JAVA_HOME environment variables.

Install the native-image technology component by following [this link to the GraalVM project site](https://www.graalvm.org/22.0/reference-manual/native-image/#install-native-image).

Note: make sure you've allocated sufficient memory to Docker or your other container software (at least 4GB). 

Run these commands to build the native images:

```bash
> mvn -f amqp-quickstart-producer  package -Pnative -Dquarkus.native.container-build=true
> mvn -f amqp-quickstart-processor package -Pnative -Dquarkus.native.container-build=true
```

The `-Dquarkus.native.container-build=true` instructs Quarkus to build 64bit native Linux executables, which can run inside containers.  

Then, start the system using:

```bash
> export QUARKUS_MODE=native
> docker compose up
```
Then, open your browser to `http://localhost:8080/quotes.html`, and click on the "Request Quote" button.

## Using an existing queue manager AMQP image

You can use a pre-built MQ image with AMQP if you don't want to build your own.

### Developer mode

When running in developer mode, use this command to build an MQ container:

```bash
> docker run --name=mq --env LICENSE=accept \
--env MQ_QMGR_NAME=QM1 \
--env MQ_APP_PASSWORD=passw0rd \
--publish 1414:1414 \
--publish 9443:9443 \
--publish 5672:5672 \
--detach \
quay.io/ogunalp/mq-amqp:latest
```

### JVM and Native-Image mode

If you don't want to run your own AMQP container build when running in JVM and native-image mode, change Line 6 of your `docker-compose.yml` file to read

```
    image: quay.io/ogunalp/mq-amqp:latest
```

and run as described above.