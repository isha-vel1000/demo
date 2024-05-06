# demo
this is my demo repo
autor-isha velankar hello

# HADOOP:
1. sudo apt-get install openjdk-8-jdk
2. sudo update-alternatives --config java
3. select the option for java-8

sudo deluser hduser
sudo groupdel hadoop
cd /usr/local/
sudo rm -r hadoop
sudo rm -r hadoop_tmp/
Cd

 sudo addgroup hadoop
sudo adduser --ingroup hadoop hduser
sudo adduser hduser sudo
sudo apt-get install openssh-server
su hduser
cd
ssh-keygen -t rsa -P ""
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
ssh localhost
exit
exit
Cd Downloads
tar -xvf hadoop-3.0.0.tar.gz
sudo mv hadoop-3.0.0 /usr/local/hadoop
sudo chown -R hduser /usr/local/
su hduser
sudo nano ~/.bashrc
***
#HADOOP VARIABLES START
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
#HADOOP VARIABLES END
***


source ~/.bashrc
sudo nano /usr/local/hadoop/etc/hadoop/hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64


sudo nano /usr/local/hadoop/etc/hadoop/core-site.xml
<property>
<name>fs.default.name</name>
<value>hdfs://localhost:9000</value>
</property>

sudo nano /usr/local/hadoop/etc/hadoop/mapred-site.xml
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>

sudo mkdir -p /usr/local/hadoop_tmp/hdfs/namenode
sudo mkdir -p /usr/local/hadoop_tmp/hdfs/datanode
sudo chown -R hduser:hadoop /usr/local/hadoop_tmp

sudo nano /usr/local/hadoop/etc/hadoop/hdfs-site.xml
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.namenode.name.dir</name>
<value>file:/usr/local/hadoop_tmp/hdfs/namenode</value>
</property>
<property>
<name>dfs.datanode.data.dir</name>
<value>file:/usr/local/hadoop_tmp/hdfs/datanode</value>
</property>

sudo nano /usr/local/hadoop/etc/hadoop/yarn-site.xml
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
<property>
<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>

hadoop namenode -format

cd /usr/local/hadoop/sbin/
start-all.sh
jps

# Mapreduce:

su hduser
cd
Pwd
mkdir analyzelogs
ls
sudo cp /home/comp119/Desktop/data/* ~/analyzelogs/
cd analyzelogs/
ls
cd
sudo chmod -R 777 analyzelogs/
sudo chown -R hduser analyzelogs/

cd analyzelogs/
sudo chmod +r *.*
export CLASSPATH="$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-client-core-2.9.0.jar:$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-client-common-2.9.0.jar:$HADOOP_HOME/share/hadoop/common/hadoop-common-2.9.0.jar:~/analyzelogs/WebLogger/*:$HADOOP_HOME/lib/*"

sudo gedit Manifest.txt
Main-Class: WebLogger.WebLoggerDriver (enter)
javac -d . WebLoggerMapper.java WebLoggerReducer.java WebLoggerDriver.java
(javac -source 1.8 -target 1.8 -d . WebLoggerMapper.java WebLoggerReducer.java WebLoggerDriver.java)
 jar -cfm analyzelogs.jar Manifest.txt WebLogger/*.class
ls
start-all.sh
jps
hadoop fs -put access_log_short.txt /input10
hadoop jar analyzelogs.jar /input10 /output10
hadoop fs -cat /output10/part-00000

# HIVE:

su hduser
cd
cd $HIVE_HOME
cd bin
hive

sudo mv home/comp/Downloads/airports.csv home/hduser/
sudo mv home/comp/Downloads/airports.csv home/hduser/

SHOW DATABASES;
CREATE DATABASE flight;
CREATE TABLE flight_data(
    >    year INT,
    >    month INT,
    >    day INT,
    >    day_of_week INT,
    >    dep_time INT,
    >    crs_dep_time INT,
    >    arr_time INT,
    >    crs_arr_time INT,
    >    unique_carrier STRING,
    >    flight_num INT,
    >    tail_num STRING,
    >    actual_elapsed_time INT,
    >    crs_elapsed_time INT,
    >    air_time INT,
    >    arr_delay INT,
    >    dep_delay INT,
    >    origin STRING,
    >    dest STRING,
    >    distance INT,
    >    taxi_in INT,
    >    taxi_out INT,
    >    cancelled INT,
    >    cancellation_code STRING,
    >    diverted INT,
    >    carrier_delay STRING,
    >    weather_delay STRING,
    >    nas_delay STRING,
    >    security_delay STRING,
    >    late_aircraft_delay STRING
    > )
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ',';

LOAD DATA LOCAL INPATH '/home/hduser/2008.csv' OVERWRITE INTO TABLE flight_data;
SHOW TABLES;

SELECT
    >    *
    > FROM
    >    flight_data
    > LIMIT 10; 

SELECT
    >    avg(arr_delay)
    > FROM
    >    flight_data
    > WHERE
    >    month=1
    >    AND origin='SFO';

CREATE TABLE airports(
    >    name STRING,
    >    country STRING,
    >    area_code INT,
    >    code STRING)
    > ROW FORMAT DELIMITED
    > FIELDS TERMINATED BY ',';

LOAD DATA LOCAL INPATH '/home/hduser/airports.csv' OVERWRITE INTO TABLE airports;

SELECT
    >    *
    > FROM
    >    airports
    > LIMIT 10;

describe flight_data;
describe  airports;

SELECT
    >    name,
    >    AVG(arr_delay)
    > FROM
    >    flight_data f
    >    INNER JOIN airports a
    >    ON (f.origin=a.code)
    > WHERE
    >    month=1
    > GROUP BY
    >    name;

select day,avg(dep_delay) from flight_data group by day;

CREATE INDEX origin_index ON TABLE flight_data (origin) AS
    > 'COMPACT' WITH DEFERRED REBUILD;

SHOW CREATED INDEX

