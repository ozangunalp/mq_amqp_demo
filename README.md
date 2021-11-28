Quarkus IBM MQ - AMQP 1.0 Support Demo
============================

This project modifies the [Quarkus AMQP 1.0 Quickstart](https://quarkus.io/guides/amqp) to serve as an example of using IBM MQ Broker with AMQP 1.0 support with Quarkus and MicroProfile Reactive Messaging.

## Pull and start the IBM MQ Broker with AMQP support enabled

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
> mvn -f amqp-quickstart-producer package
> mvn -f amqp-quickstart-processor package
```

Because we are running in _prod_ mode, we need to provide an AMQP 1.0 broker.
The [docker-compose.yml](docker-compose.yml) file starts the broker and your application.

Start the broker and the applications using:

```bash
> docker compose up --build
```

Then, open your browser to `http://localhost:8080/quotes.html`, and click on the "Request Quote" button.
 

## Build the application in native mode

To build the applications into native executables, run:

```bash
> mvn -f amqp-quickstart-producer package -Pnative  -Dquarkus.native.container-build=true
> mvn -f amqp-quickstart-processor package -Pnative -Dquarkus.native.container-build=true
```

The `-Dquarkus.native.container-build=true` instructs Quarkus to build Linux 64bits native executables, who can run inside containers.  

Then, start the system using:

```bash
> export QUARKUS_MODE=native
> docker compose up
```
Then, open your browser to `http://localhost:8080/quotes.html`, and click on the "Request Quote" button.
