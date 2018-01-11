# CerebralCortex Data Process
### Assumptions:  
* Machine IP Address: 192.168.0.10
* Data folder:  /home/user/data

Update these two information according to your machine.

### Procedure
1. Open terminal and go to your **CerebralCortex-DockerCompose** folder. Check whether every cerebralcortex service is running with the command `sudo docker-compose ps`. If the services are down, start them by the command `sudo docker-compose up -d`.
2. Now, connnect to cassandra using `sudo docker-compose exec cassandra bash`. After connecting to cassandra, run the following commands:  
    ```
    apt update
    apt install nano
    nano /etc/cassandra/cassandra.yaml
    ```
   Change `batch_size_fail_threshold_in_kb` value from 50 to 5000 and `commitlog_segment_size_in_mb` value from 32 to 512. 
   You can use cnt+w to find a word in nano. Save and close it and exit from cassandra.
2. Similarly, connect to mysql service using `sudo docker-compose exec mysql bash`. After connecting to mysql, run the following commands:  
    ```
    apt update
    apt install nano  
    nano /etc/mysql/my.cnf  
    ```  
    Add the following two lines and save it.  
    ```
    [mysqld]  
    max_connections = 2000  
    ```    
   Exit from mysql service.
3. Open `docker-compose.yml` file in **CerebralCortex-DockerCompose** folder and place your machine's IP in line 109 as follows:  
    `KAFKA_ADVERTISED_HOST_NAME: ${MACHINE_IP:-192.168.0.10}`
4. Open `cc_configuration.yml` file in **CerebralCortex-DockerCompose/cc_config_file/** folder and place `127.0.0.1` as host for **cassandra, influxdb, mysql, minio**, in line 6, 14, 21 and 33. For **kafkaserver** change the host to your machine's IP address in line 51.
5. Restart docker-compose services in terminal.
    ```
    sudo docker-compose stop
    sudo docker-compose up -d
    ```  
6. Open IntelIj and clone CerebralCortex-Scripts as a project in IntelIj  
    `https://github.com/MD2Korg/CerebralCortex-Scripts.git`
7. In `replay_participant.sh` under data_replay folder, change the following:
    * In line 3:  
      `for p in /home/user/data/[a-z0-9]*-[a-z0-9]*`  
      here `/home/user/data/` is the **data folder**, change it according to your data folder.
    * In line 7:  
      `for f in /home/user/data/$participant/*`  
    * In line 12:  
     `python3.6 replay_data.py -b 192.168.0.10:9092 -d "$f/"`  
      here `192.168.0.10` is the machine's IP address, change it according to your machine.
8. In `run.sh` under data_replay folder, change line 3 to the following:  
  `python3 replay_data.py -b "192.168.0.10:9092" -d "/home/user/data/"`
9. Run the replay_participant.sh file in terminal (you can use the terminal inside IntelIj).
    ```
    bash replay_participant.sh
    ```  
   It will take a long to finish, approximately one day (depending on your machine).
10. Open IntelIj and clone CerebralCortex-KafkaStreamPreprocessor as a project in IntelIj  
  `https://github.com/MD2Korg/CerebralCortex-KafkaStreamPreprocessor.git`
11. In `run.sh` under util folder, change the followings:
  * Comment out line 7
  * In line 10, define the correct path for `SPARK_HOME`  
    `export SPARK_HOME=/home/user/spark/spark-2.2.0-bin-hadoop2.7/`  
    Change it according to your spark path. If you don't have spark, download Apache Spark 2.2.0 and extract it and place the path here.
  * In line 27, place your machine's IP address  
    `KAFKA_BROKER="192.168.0.10:9092"`
12. Run the run.sh file in terminal (you can use the terminal inside IntelIj).
    ```
    sh run.sh
    ```  
voilà
