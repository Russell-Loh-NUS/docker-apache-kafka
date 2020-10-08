# docker-apache-kafka

# Pre-requisites:
1.	[Docker Desktop/Machine](https://www.docker.com/products/docker-desktop) installed and started
2.	[Kafkacat](https://github.com/edenhill/kafkacat) installed

Important note about using docker version of kafkacat: 
If you are using the Docker version of kafkacat, please use the command `docker run -it --network=host edenhill/kafkacat:1.6.0` instead of `kafkacat` for the instructions below.

# Instructions
## Starting Kafka Servers + ZooKeeper Ensemble:
1.	Clone the github repository into your local drive
https://github.com/Russell-Loh-NUS/docker-apache-kafka.

2.	Open Command Prompt/Terminal and `cd` into the cloned repository.

3.	Open the `.env` file within the cloned repository and change the `HOST_IP` to your own private network ip. Save the file and exit.
*Note: For docker machine, leave it as the default ip `192.168.99.100`

4.	Type `docker-compose up` on the Commad Prompt/Terminal window. 
Note: You can run in detached mode using `docker-compose up -d` however, it is not advisable as we will not be notified of when the servers are up.

5.	Wait for a few seconds for the servers to be up.

6.	Open a new Command Prompt/Terminal window and type `kafkacat -L -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094` to verify the servers are up and running. Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3.

7.	Open a new Command Prompt/Terminal window and type `kafkacat -P -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094 -t MyTopic` which will start a producer. Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3.

8.	Open a new Command Prompt/Terminal window and type `kafkacat -C -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094 -t MyTopic` which will start a consumer. Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3.

9.	Type anything into the producer window and hit `Enter`. You should see the same entry being output on the consumer side.

## Testing election of new controller:
1.	Start the Kafka servers as instructed above under `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

2.	Open a new Command Prompt/Terminal window and type `kafkacat -L -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094` to view the metadata for our brokers and topics. Take note of the controller’s port number e.g 29093.
*Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3 of `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

3.	Type `docker ps` and find the corresponding container ID with the port number e.g 0.0.0.0:29093->9092/tcp.

4.	Type `docker stop [CONTAINER ID]` substituting [CONTAINER ID] with the container ID we found in the previous step to stop the container of the leader.

5.	Verify the container has been stopped by typing `docker ps`.

6.	Verify that a new controller has been elected by typing `kafkacat -L -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094`.
*Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3 of `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

## Testing election of new leader for topic:
1.	Start the Kafka servers as instructed above under `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

2.	Open a new Command Prompt/Terminal window and type `kafkacat -L -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094` and find the leader of the topic by referencing the broker id of ’leader’ under `MyTopic`. Take note of the port number e.g 29094.
*Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3 of `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

3.	Type `docker ps` and find the corresponding container ID with the port number e.g 0.0.0.0:29094->9092/tcp.

4.	Type `docker stop [CONTAINER ID]` substituting [CONTAINER ID] with the container ID we found in the previous step to stop the container of the leader.
 
5.	Verify the container has been stopped by typing `docker ps`.

6.	Verify that a new leader has been elected by typing `kafkacat -L -b [HOST_IP]:29092,[HOST_IP]:29093,[HOST_IP]:29094`.
*Replace `[HOST_IP]` with the ip you defined in the `.env` file from step 3 of `Instructions to start Kafka Servers + ZooKeeper Ensemble`.

7.	Start typing on the producer window, you should still be able to receive messages on the consumer side.

## FAQ
Q: How do I stop the servers?

A: Open a new Command Prompt/Terminal window, `cd` into the cloned repository and type `docker-compose down`. Alternatively, on the Command Prompt/Terminal window that is currently running the servers, press Ctrl/Command + C.
