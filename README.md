# Real-Time Streaming Project with Smartphone Sensor Data

This project demonstrates how to set up a real-time streaming pipeline using smartphone sensor data, AWS Lambda, API Gateway, Kafka, and Python. The pipeline allows for the ingestion of ambient brightness data from a smartphone sensor, processes it through AWS Lambda, and streams it to a Kafka topic hosted on an EC2 instance.

## Architecture Overview

- **Sensor Logging Application on Smartphone**: Collects ambient brightness data. Link : 
- **AWS Lambda and API Gateway**: Processes incoming sensor data and routes it.
- **Kafka on EC2**: For processing and analyzing streaming data.

## Step 1: Sensor Logging Application Setup

1. **Download and Install a Sensor Logging Application**: Choose a compatible application for your smartphone and install it.
2. **Configure Logging for Ambient Brightness Data**: Adjust the application's settings to log ambient brightness data.
3. **Set Endpoint URL**: In the app's HTTP or API settings, input the endpoint URL from your API Gateway to post the sensor data.



## Step 2: AWS Lambda and API Gateway Configuration

### AWS Lambda

1. **Create a New Lambda Function**
    - Go to the AWS Management Console.
    - Navigate to Lambda and click on "Create function".
    - Choose "Author from scratch", provide a function name, and select a Python runtime.

2. **Deploy Python Code**
    - After creating your function, upload your Python code. This code should process the incoming sensor data and forward it to Kafka.
    ```python
    # Example Python code snippet for Lambda function
    def lambda_handler(event, context):
        # Your code to process the event and forward to Kafka
        return {
            'statusCode': 200,
            'body': json.dumps('Success')
        }
    ```
    - Adjust the above snippet according to your logic.

3. **Configure KafkaProducer**
    - In the function configuration, set the environment variables for your Kafka broker address and topic, or include them directly in your code.

4. **Create a Lambda Layer for Kafka Library**
    - Download `kafka_yt_demo.zip` from the provided link.
    - In your Lambda function, click on "Layers" at the bottom, then "Add a layer", and select "Upload a .zip" to upload your `kafka_yt_demo.zip`.
    - This layer will include the necessary libraries to interact with Kafka.

### API Gateway

1. **Create a New API Gateway**
    - Open the API Gateway service from the AWS Console.
    - Click on "Create API" and select the appropriate type (REST or HTTP).
    - Follow the setup wizard to create a new API.

2. **Configure HTTP Endpoint**
    - Create a new resource and method (POST) within your API.
    - The resource path could be `/publishtokafka`, and the POST method will receive the sensor data.

3. **Connect to Lambda Function**
    - Link the POST method directly to your Lambda function.
    - In the Integration Request settings, select your Lambda function as the backend service.
    - Ensure the request and response mappings are correctly configured to handle your sensor data format.


## Step 3: Kafka Setup on EC2

### Launch EC2 Instance

1. **Start a New EC2 Instance**: Host your Kafka broker here.
2. **Connect to the Ec2 Instance Using Putty** : Use the key-pair to login as "ec2-user"
3. **Install Java**: Run `sudo yum install java-1.8.0-openjdk`.
4. **Verify Java Installation**: Check with `java -version`.

5. **Download Kafka**: Use `wget https://dlcdn.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz`.
6. **Extract Kafka Package**: Execute `tar -xvf kafka_2.13-3.4.0.tgz`.
7. **Navigate to Kafka Directory**: Use `cd kafka_2.13-3.4.0`.


8. **Start Zookeeper**: Run `bin/zookeeper-server-start.sh config/zookeeper.properties`.

## Step 4: Configuration for Public Access

**Configure for Public Access**: Edit `server.properties` for public IP access. It is pointing to private server , change server.properties so that it can run in public IP 



### To do this , you can follow any of the 2 approaches shared below:
- Do a vi config/server.properties in insert mode -- change ADVERTISED_LISTENERS to public ip of the EC2 instance
- You can modify the file using Winscp also

## Step 5: Start Kafka-server:
### Duplicate the current session and enter in a new Console

1. **Configure Kafka Server Heap Options**: (Optional) `export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M"`.
2. Navigate to the Kafka directory by using `cd kafka_2.13-3.4.0`.
2. **Start Kafka Server**: Execute `bin/kafka-server-start.sh config/server.properties`.

## Step 6 : Creating Kafka Topic and starting the Kafka Console Consumer
### Duplicate the current session and enter in a new Console

1. Navigate to Kafka directory by : `cd kafka_2.13-3.4.0`.
2. **Create Topic for Sensor Data**: Use `bin/kafka-topics.sh --create --topic sensor_data_consumer --bootstrap-server <Your-EC2-Public-IP>:9092 --replication-factor 1 --partitions 1`.
3. **Start Kafka Console Consumer**: Use `bin/kafka-console-consumer.sh --topic sensor_data_consumer --bootstrap-server <Your-EC2-Public-IP>:9092`.


This setup streamlines the process of collecting, processing, and analyzing real-time sensor data through a robust AWS and Kafka infrastructure.
