# Parse data using Kafka stream with Yarn

This is a distributed streaming application that reads data using Python, and pipes it to Kafka streams. Jobs are run using Yarn, Samza and Zookeeper on Hadoop 2.0


### RUNNING THE CODE

### 1.Prerequisites
 a) make sure JAVA_HOME is set in .bashrc.
```
echo $JAVA_HOME

```

b) Make sure you have write persmission in /home directory or wherever you are cloning the repo.

```
git clone https://github.com/Shivansh1805/KHWY.git

```

c) For now we generate the data by creating a twitter app through python. So kindly update you twitter app credentials in credentials.py file which is present in python-scripts directory.

```
consumer_key = ""
consumer_secret = ""
access_key = ""
access_secret = ""

```

### 2.Give execution permission to the below mentioned scripts
```
chmod +x /home/path/to/KWHY/deploy-script/grid
chmod +x /home/path/to/KWHY/deploy/bin/run-job.sh
chmod +x /home/path/to/KWHY/deploy/bin/run-class.sh

```
If you face permission related problem with any script, just make it executable by above mentioned commands.
### 3.Build Samza distribution artifacts in deploy directory

```
mvn clean install

```

### 4.Start the grid script in bootstrap mode(It will automatically install and starts zookeeper, kafka, yarn)
Install and Start yarn, zookeeper, kafka

```
deploy-script/grid bootstrap

```

To start/stop zookeeper,yarn,kafka by a single command

```

deploy-script/grid start all
deploy-script/grid stop all

```

To start/stop zookeeper,yarn,kafka separately

```
deploy-script/grid start/stop kafka
deploy-script/grid start/stop yarn
deploy-script/grid start/stop zookeeper

```
Note - If you face some error while starting kafka just remove the kafka-logs directory from /tmp/ and start it again.


### 5. Update path accoridng to your local system
Update path in both files present in src/resources directory

```
yarn.package.path=/home/path/to/KWHY/deploy/deploy.tar.gz
```

### 6.Start kafka stream

Pull data from twitter using python script and pipe it to kafka producer and bind to 'tweets' topic
Go to python-scripts directory and run the below command.
```
pip3 install tweepy
python3 raw-stream.py | /home/path/to/KWHY/deploy/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic tweets 

```
You can simply run the producer also and can provide mannual inputs.

```

/home/path/to/KWHY/deploy/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic <topic-name>

```
if  you encounter error due to impropoer yarn shut down, simply delete /tmp/kafka-logs directory

### 7.Create a consumer without submitting job to yarn

In a new terminal type, to view the stream data run the following command

```
/home/path/to/KWHY/deploy/kafka/bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic <topic-name>

```

### 8.Run the Samza deployment script with correct paths and attributes

```
/home/path/to/KWHY/deploy/bin/run-job.sh --config-factory=org.apache.samza.config.factories.PropertiesConfigFactory --config-path=/home/path/to/KWHY/deploy/conf/twitter-parser.properties

```

Open browser and go to local yarn resource manager: http://localhost:8088/cluster to check status

### 9.Create a consumer after submitting job to yarn and view the stream.

```
/home/path/to/KWHY/deploy/kafka/bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic tweets-parsed

```

## NOTE
Before pushing the code

```
rm -rf deploy
mvn clean

``` 



