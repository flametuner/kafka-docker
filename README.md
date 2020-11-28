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

docker run -it --rm --network kafka-docker_app-tier -e KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181 -e DISABLE_WELCOME_MESSAGE=true bitnami/kafka:2-debian-10 ${0##*/}.sh $@
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

![](https://i.imgur.com/cflG5Dn.png)

We list the topics using this:

```
$ ./kafka-topics --list --zookeeper zookeeper:2181
```

![](https://i.imgur.com/EAkC7Uv.png)

And we described the topics:

```
$ ./kafka-topics --describe --zookeeper zookeeper:2181 --topic amazingTopic
```

![](https://i.imgur.com/nfWORfa.png)

Then, we created a topic with multiple replicas:

```
$ ./kafka-topics --create --zookeeper zookeeper:2181 -replication-factor 2 --partitions 1 --topic redundantTopic
```

![](https://i.imgur.com/U4dQMlp.png)

Describing it:

```
$ ./kafka-topics --describe --zookeeper zookeeper:2181 --topic redundantTopic
```

![](https://i.imgur.com/Blk3TcT.png)

### Message producer

We can produce in whatever broker we want, but like before, we won't use localhost and we will use the docker hostname instead.

Production of some messages:

```
$ ./kafka-console-producer --broker-list broker-1:9092 --topic amazingTopic
```

![](https://i.imgur.com/OUIf8GR.png)


If we wanted to use files, we would have to call the docker run withou the `-t` option like the following, that's why we created a custom `kafka-console-producer-pipe` script:

```
$ cat TESTE.txt | ./kafka-console-producer-pipe --broker-list broker-1:9092 --topic amazingTopic
```

![](https://i.imgur.com/1c0ISzL.png)

### Message consumer

Consuming a message is easy, using the docker hostname:

```
$ ./kafka-console-consumer --topic amazingTopic --bootstrap-server broker-1:9092 --from-beginning
```

![](https://i.imgur.com/uLwuHtV.png)

We could also limit the number max of messages seen:

```
$ ./kafka-console-consumer --topic amazingTopic --bootstrap-server broker-1:9092 --from-beginning --max-messages 1
```

![](https://i.imgur.com/votvbAc.png)

If you want to use a formatter, you could use:

```
$ ./kafka-console-consumer --topic amazingTopic --bootstrap-server broker-1:9092 --from-beginning --max-messages 1 --formatter 'kafka.coordinator.group.GroupMetadataManager$OffsetsMessageFormatter'
```
![](https://i.imgur.com/xwGywo8.png)


To consume messages from a specific consumer group, use the following:
```
$ ./kafka-console-consumer --topic amazingTopic --bootstrap-server broker-1:9092 --new-consumer --consumer-property group.id=my-group
```

## Shutdown

After running all the tutorial, you can shutdown you docker-compose running:

```
$ docker-compose down
```

![](https://i.imgur.com/6ucT6pG.png)


You can clean all the saved configuration and topics created by deleting the volumes created:

```
$ docker volume ls -f "name=kafka-docker*" | grep "kafka-docker" | awk '{print $2}' | xargs docker volume rm
```

![](https://i.imgur.com/gzgUTFl.png)