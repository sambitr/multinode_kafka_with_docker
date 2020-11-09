# multinode_kafka_with_docker_setup

#############################################################################<br>
Run the Zookeeper and Kafka Brokers one by one on each of UNIX hosts. <br>
Here for testing purposes we are using 3 UNIX hosts and we will be creating 3 node Docker Kafka Cluster<br>
#############################################################################<br>

N.B: <b>10.24.32.147, 10.24.32.146 & 10.24.32.80</b> are the IP addresses of the 3 Redhat Virtual Machines on which the 3 nodes of the cluster will be created


#### Zookeeper-1

```
docker run -d \
   --net=host \
   --name=zk-1 \
   -e ZOOKEEPER_SERVER_ID=1\
   -e ZOOKKEEPER_CLIENT_ID=22181 \
   -e ZOOKEEPER_TICK_TIME=2000 \
   -e ZOOKEEPER_INIT_LIMIT=5 \
   -e ZOOKEEPER_SYNC_LIMIT=2 \
   -e ZOOKEEPER_SERVERS="10.24.32.147:22888:23888;10.24.32.146:32888:33888;10.24.32.80:42888:43888" \
   confluentinc/cp-kafka-connect:5.4.1
 ```
 
 #### Zookeper-2
 
 ```
  docker run -d \
   --net=host \
   --name=zk-2 \
   -e ZOOKEEPER_SERVER_ID=2 \
   -e ZOOKKEEPER_CLIENT_ID=32181 \
   -e ZOOKEEPER_TICK_TIME=2000 \
   -e ZOOKEEPER_INIT_LIMIT=5 \
   -e ZOOKEEPER_SYNC_LIMIT=2 \
   -e ZOOKEEPER_SERVERS="10.24.32.147:22888:23888;10.24.32.146:32888:33888;10.24.32.80:42888:43888" \
   confluentinc/cp-kafka-connect:5.4.1
 ```
 
#### Zookeper-3

```
docker run -d \
   --net=host \
   --name=zk-3 \
   -e ZOOKEEPER_SERVER_ID=3 \
   -e ZOOKKEEPER_CLIENT_ID=42181 \
   -e ZOOKEEPER_TICK_TIME=2000 \
   -e ZOOKEEPER_INIT_LIMIT=5 \
   -e ZOOKEEPER_SYNC_LIMIT=2 \
   -e ZOOKEEPER_SERVERS="10.24.32.147:22888:23888;10.24.32.146:32888:33888;10.24.32.80:42888:43888" \
   confluentinc/cp-kafka-connect:5.4.1
 ```
 
 #### Broker-1
 
 ```
 docker run -d \
   --net=host \
   --name=kafka-1 \
   -e KAKFA_ZOOKEEPER_CONNECT="10.24.32.147:22181;10.24.32.146:32181;10.24.32.80:42181" \
   -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://10.24.32.147:29092 \
   confluentinc/cp-kafka-connect:5.4.1
 ```
 
 #### Broker-2
 
 ```
 docker run -d \
   --net=host \
   --name=kafka-2 \
   -e KAKFA_ZOOKEEPER_CONNECT="10.24.32.147:22181;10.24.32.146:32181;10.24.32.80:42181" \
   -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://10.24.32.146:39092 \
   confluentinc/cp-kafka-connect:5.4.1
 ```
 
 #### Broker-3
 
 ```
  docker run -d \
   --net-host \
   --name=kafka-2 \
   -e KAKFA_ZOOKEEPER_CONNECT="10.24.32.147:22181;10.24.32.146:32181;10.24.32.80:42181" \
   -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://10.24.32.80:49092 \
   confluentinc/cp-kafka-connect:5.4.1
 ```
 
#############################################################################<br>
                                &emsp;&emsp;&emsp;KAFKA TOPIC CREATION<br>
#############################################################################<br>

```
docker run \
   --net=host \
   --rm \
   confluentinc/cp-kafka-connect:5.4.1 \
   kafka-topics --create --topic sambit_test_topic --partitions 3 --replication-factor 3 --if-not-exists --zookeeper 10.24.32.146:32181
```

#############################################################################<br>
                                KAFKA TOPIC CREATION STATUS CHECK<br>
#############################################################################<br>

```
docker run \
   --net=host \
   --rm \
   confluentinc/cp-kafka-connect:5.4.1 \
   kafka-topics --describe --topic sambit_test_topic --zookeeper 10.24.32.146:32181
```

#############################################################################<br>
                                KAFKA PRODUCER <br>
#############################################################################<br>

```
docker run \
  --net=host \
  --rm    confluentinc/cp-kafka-connect:5.4.1 \
  bash -c "seq 42 | kafka-console-producer --broker-list localhost:29092 --topic sambit-test-topic && echo 'produced 42 messages'"
```

#############################################################################<br>
                                KAFKA CONSUMER <br>
#############################################################################<br>

```
docker run \
  --net=host \
  --rm    confluentinc/cp-kafka-connect:5.4.1 \
  kafka-console-consumer --bootstrap-server  10.24.32.147:29092 --topic sambit-test-topic --from-beginning
```
