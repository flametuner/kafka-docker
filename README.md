# kafka-docker

This project uses `docker-compose` to execute the tutorial for INE5418 at UFSC.

## Pre-requisites

- Docker
- docker-compose

## Running

To start the cluster with 2 kafka brokers simply run:

```
$ docker-compose up -d
```

After this, you will have:

- zookeper: zookeeper instance
- broker_1: kafka instance
- broker_2: kafka instance

### Client executions

We create three scripts to run the three commands needed to accomplish the tutorial:

- kafka-topics
- kafka-console-producer
- kafka-console-consumer

They run in docker too using following base_script:

```
#!/bin/bash

docker run -it --rm --network kafka-docker_app-tier -e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181 bitnami/kafka:2-debian-10 ${0##*/}.sh $@
```

## Tutorial Report

### Start up
We start runing our cluster:

![](https://i.imgur.com/hwSI8M8.png)

After starting using the docker-compose command, we were asked to create a topic and list.

### Managing topics

We cannot run in localhost because we are passing all these arguments inside a container. Instead of using localhost:2181 for the zookeeper instance, we use its docker hostname.

```
$ ./kafka-topics --create --zookeeper zookeeper:2181 -replication-factor 1 --partitions 1 --topic amazingTopic
```

![](https://i.imgur.com/NuLzMYj.png)

We list the topics using this:

```
$ ./kafka-topics --list --zookeeper zookeeper:2181
```

![](https://i.imgur.com/k56ZWeY.png)

And we described the topics:

![](https://i.imgur.com/FCMyHHg.png)

Then, we created a topic with multiple replicas:

```
$ ./kafka-topics --create --zookeeper zookeeper:2181 -replication-factor 2 --partitions 1 --topic redundantTopic
```

![](https://i.imgur.com/B9YBG86.png)

Describing it:

![](https://i.imgur.com/LRoemsE.png)

### Message producer


### Message consumer