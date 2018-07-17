# Introduction

Creates a development environment for use with sbt, yarn, node, postgres, kafak, zookeeper.

# Instructions

- clone the repo, cd into it
- set some environment variables - see the docker-compose.yml file. You will need environment variables for the following to be created: databases, database username, database password, kafka topic, etc. Here are some examples:

```
export API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_DRIVER=org.postgresql.Driver
export API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_NAME=apigateway
export API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_URL=jdbc:postgresql://db:5432/${API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_NAME}
export API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_USERNAME=postgres
export API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_PASSWORD=postgres

export API_GATEWAY_SCALIKEJDBC_TEST_DB_DRIVER=org.postgresql.Driver
export API_GATEWAY_SCALIKEJDBC_TEST_DB_NAME=${API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_NAME}test
export API_GATEWAY_SCALIKEJDBC_TEST_DB_URL=jdbc:postgresql://db:5432/${API_GATEWAY_SCALIKEJDBC_TEST_DB_NAME}
export API_GATEWAY_SCALIKEJDBC_TEST_DB_USERNAME=postgres
export API_GATEWAY_SCALIKEJDBC_TEST_DB_PASSWORD=postgres

export API_GATEWAY_MESSAGE_BROKER_PRODUCER_TOPIC=some-topic
```

- run the following command to start the containers, and enter the SBT container:

```
docker-compose down; DEV_DIR=/path/to/your/sbt/project/ docker-compose up -d && docker container exec sbt mkdir /path/to/running-pid-file/ && sleep 5 && docker container exec kafka-zookeeper /opt/kafka/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic $API_GATEWAY_MESSAGE_BROKER_PRODUCER_TOPIC; docker container exec sbt mkdir /path/to/sbt/log/dir/ && docker container exec sbt psql -U postgres -h db -c "create database ${API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_NAME} owner ${API_GATEWAY_SCALIKEJDBC_DEFAULT_DB_USERNAME};"; docker container exec sbt psql -U postgres -h db -c "create database ${API_GATEWAY_SCALIKEJDBC_TEST_DB_NAME} owner ${API_GATEWAY_SCALIKEJDBC_TEST_DB_USERNAME};"; docker container exec -it sbt /bin/bash
```

This should result in you entering the sbt container, in a directory that is mapped to your SBT project directory on the host. From there, enter the following:
```
sbt
```
This will get you to the sbt prompt. From there you can do:
```
;clean;compile
```

or 

```
;clean;stage;~run
```




