# Setup Kafka in Docker

# Command Summary
```sh
kafka-consumer-groups --bootstrap-server localhost:9094 --describe --group my-group
kafka-topics --describe --zookeeper zoo1 --topic my.topic.01
```

## 1. Setup

- Demo Environment
```bash
export YOUR_LOCAL_IP=10.124.56.100
export YOUR_FRIST_TOPIC=topic.01
export YOUR_CONSUMER_GROUP_TOPIC=group.topic.02
export YOUR_FIRST_CONSUMER_GROUP=consumer.group.01
export YOUR_SECOND_CONSUMER_GROUP=consumer.group.02
```

### 1.1 Create zookeeper instance

```sh
docker run -d \
--name zookeeper \
-p 2181:2181 \
miendinh/zookeeper
```

### 1.2. Run kafka instance

```sh
docker run -d \
--name kafka \
-p 7203:7203 \
-p 9092:9092 \
-e KAFKA_ADVERTISED_HOST_NAME=$YOUR_LOCAL_IP \
-e ZOOKEEPER_IP=$YOUR_LOCAL_IP \
miendinh/kafka
```

### 1.3. Create a topic

```sh
docker run \
--rm kafka1 kafka-topics.sh \
--create \
--topic $YOUR_CONSUMER_GROUP_TOPIC \
--replication-factor 3 \
--partitions 10 \
--zookeeper $YOUR_LOCAL_IP:2181
```

- Increase partition to a topic

```sh
docker run \
--rm miendinh/kafka kafka-topics.sh \
--alter \
--zookeeper $YOUR_LOCAL_IP:2181 \
--topic $YOUR_FRIST_TOPIC \
--partitions 5
```

### 1.4 Describe a created topic

```sh
docker run \
--rm miendinh/kafka kafka-topics.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--describe \
--topic $YOUR_FRIST_TOPIC
```

### 1.5 List all created topics

```sh
docker run \
--rm miendinh/kafka kafka-topics.sh \
--list \
--zookeeper $YOUR_LOCAL_IP:2181
```

### 1.6 Mobidfy topic configuration

```sh
docker run \
--rm miendinh/kafka kafka-topics.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--create \
--topic $YOUR_CONSUMER_GROUP_TOPIC \
--partitions 3 \
--replication-factor 1
```


#### 1.6.1 look for existing configurations

```sh
docker run \
--rm miendinh/kafka kafka-topics.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--describe \
--topic $YOUR_CONSUMER_GROUP_TOPIC
```

#### 1.6.2 documentation of kafka-configs command

```sh
docker run \
--rm miendinh/kafka kafka-configs.sh
```

#### 1.6.3 Describe configs for the topic with the command

```sh
docker run \
--rm miendinh/kafka kafka-configs.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--entity-type topics \
--entity-name $YOUR_CONSUMER_GROUP_TOPIC \
--describe
```

#### 1.6.4 add a config to our topic

```sh
docker run \
--rm miendinh/kafka kafka-configs.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--entity-type topics \
--entity-name $YOUR_CONSUMER_GROUP_TOPIC \
--add-config min.insync.replicas=2 \
--alter
```

#### 1.6.5 Describe configs using kafka-configs

```sh
docker run \
--rm miendinh/kafka kafka-configs.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--entity-type topics \
--entity-name $YOUR_CONSUMER_GROUP_TOPIC \
--describe
```

#### 1.6.6 Describe configs using kafka-topics

```sh
docker run \
--rm miendinh/kafka kafka-topics.sh \
--zookeeper $YOUR_LOCAL_IP:2181 \
--describe \
--topic $YOUR_CONSUMER_GROUP_TOPIC
```

#### 1.6.7 Delete a config

```sh
    docker run \
    --rm miendinh/kafka kafka-configs.sh \
    --zookeeper $YOUR_LOCAL_IP:2181 \
    --entity-type topics \
    --entity-name $YOUR_CONSUMER_GROUP_TOPIC \
    --delete-config min.insync.replicas \
    --alter
```

### 1.7 Create a Publisher

```sh
docker run --rm --interactive \
miendinh/kafka kafka-console-producer.sh \
--topic $YOUR_FRIST_TOPIC \
--broker-list $YOUR_LOCAL_IP:9092
```

### 1.8 Create a Consumer

```sh
docker run --rm \
miendinh/kafka kafka-console-consumer.sh \
--topic $YOUR_FRIST_TOPIC \
--from-beginning \
--zookeeper $YOUR_LOCAL_IP:2181
```

## 2 Consumer Group

### 2.1 documentation for the command 
```sh
docker run --rm \
miendinh/kafka kafka-consumer-groups.sh
```

### 2.2 list consumer groups
```sh
docker run --rm \
miendinh/kafka kafka-consumer-groups.sh \
--bootstrap-server $YOUR_LOCAL_IP:9092 \
--list
```
 
### 2.3 start a consumer

```sh
docker run --rm \
miendinh/kafka kafka-console-consumer.sh \
--bootstrap-server $YOUR_LOCAL_IP:9092 \
--topic $YOUR_CONSUMER_GROUP_TOPIC \
--consumer-property group.id=$YOUR_FIRST_CONSUMER_GROUP
```

### 2.4 describe the group now

```sh
docker run \
--rm miendinh/kafka kafka-consumer-groups.sh \
--bootstrap-server $YOUR_LOCAL_IP:9092 \
--describe \
--group $YOUR_FIRST_CONSUMER_GROUP
```

### 2.5 start a console consumer

```sh
docker run \
--rm miendinh/kafka kafka-console-consumer.sh \
--bootstrap-server $YOUR_LOCAL_IP:9092 \
--topic $YOUR_CONSUMER_GROUP_TOPIC \
--consumer-property group.id=$YOUR_FIRST_CONSUMER_GROUP
```
