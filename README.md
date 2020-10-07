# docker-apache-kafka

## Pre-requisites:
1.	[Docker Desktop](https://www.docker.com/products/docker-desktop) installed and started
2.	[Kafkacat](https://github.com/edenhill/kafkacat) installed

## Instructions to start Kafka Servers + ZooKeeper Ensemble:
1.	Clone the github repository into your local drive
https://github.com/Russell-Loh-NUS/docker-apache-kafka.
2.	Open Command Prompt/Terminal and `cd` into the cloned repository.
3.	Type `docker-compose up`. 
Note: You can run in detached mode using `docker-compose up -d` however, it is not advisable as we will not be notified of when the servers are up.
4.	Wait for a few seconds for the servers to be up.
 
5.	Open a new Command Prompt/Terminal window and type `kafkacat -L -b localhost:29092,localhost:29093,localhost:29094` to verify the servers are up and running.

6.	Open a new Command Prompt/Terminal window and type `kafkacat -P -b localhost:29092,localhost:29093,localhost:29094 -t MyTopic` which will start a producer.
 
7.	Open a new Command Prompt/Terminal window and type `kafkacat -C -b localhost:29092,localhost9093,localhost:29094 -t MyTopic` which will start a consumer.

8.	Type anything into the producer window and hit `Enter`. You should see the same entry being output on the consumer side.
 
## Instructions to test election of new controller:
1.	Start the Kafka servers as instructed above under `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

2.	Open a new Command Prompt/Terminal window and type `kafkacat -L -b localhost:29092,localhost:29093,localhost:29094` to view the metadata for our brokers and topics. Take note of the port number e.g 29094.

3.	Type `docker ps` and find the corresponding container ID with the port number e.g 0.0.0.0:29094->29094/tcp.

4.	Type `docker stop [CONTAINER ID]` substituting [CONTAINER ID] with the container ID we found in the previous step to stop the container of the leader.
 
5.	Verify the container has been stopped by typing `docker ps`.

6.	Verify that a new controller has been elected by typing `kafkacat -L -b localhost:29092,localhost:29093,localhost:29094`.
 
## Instructions to test election of new leader for topic:
1.	Start the Kafka servers as instructed above under `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

2.	Open a new Command Prompt/Terminal window and type `kafkacat -L -b localhost:29092,localhost:29093,localhost:29094` and find the leader of the topic by referencing the broker id of ’leader’ under `MyTopic`. Take note of the port number e.g 29094.

3.	Type `docker ps` and find the corresponding container ID with the port number e.g 0.0.0.0:29094->29094/tcp.

4.	Type `docker stop [CONTAINER ID]` substituting [CONTAINER ID] with the container ID we found in the previous step to stop the container of the leader.
 
5.	Verify the container has been stopped by typing `docker ps`.

6.	Verify that a new leader has been elected by typing `kafkacat -L -b localhost:29092,localhost:29093,localhost:29094`. 
 
7.	Start typing on the producer window, you should still be able to receive messages on the consumer side.

## FAQ
Q: How do I stop the servers?

A: Open a new Command Prompt/Terminal window, `cd` into the cloned repository and type `docker-compose down`. Alternatively, on the Command Prompt/Terminal window that is currently running the servers, press Ctrl/Command + C.
