## 下載spark-2.1.1-hadoop2.7版
```
wget https://archive.apache.org/dist/spark/spark-2.1.1/spark-2.1.1-bin-hadoop2.7.tgz
tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz
```
## 重新命名spark
```
mv spark-2.1.1-bin-hadoop2.7 spark
```
## 加入spark環境變量
```
vi ~/.bashrc

export SPARK_HOME=/home/hadoop/spark
export PATH=$PATH:$SPARK_HOME/bin
```

## 修改log4j.properties
```
cp ~/spark/conf/log4j.properties.template ~/spark/conf/log4j.properties
vi ~/spark/conf/log4j.properties

將 log4j.rootCategory=INFO, console
改為 log4j.rootCategory=WARN, console
```
## 修改spark-env.sh檔案
```
cp ~/spark/conf/spark-env.sh.template ~/spark/conf/spark-env.sh
vi ~/spark/conf/spark-env.sh

加上
export SCALA_HOME=/usr/share/scala
export JAVA_HOME=/usr/java/jdk1.8.0_131
export HADOOP_HOME=/home/hadoop/hadoop

# Options read by executors and drivers running inside the cluster
export SPARK_LOCAL_IP=nn
export SPARK_LOCAL_DIRS=/home/hadoop/spark

# Options read in YARN client mode
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export SPARK_EXECUTOR_INSTANCES=1
export SPARK_EXECUTOR_CORES=1
export SPARK_EXECUTOR_MEMORY=512m
export SPARK_DRIVER_MEMORY=1G

export SPARK_MASTER_IP=nn
```
## 修改spark-defaults.conf
```
cp ~/spark/conf/spark-defaults.conf.template ~/spark/conf/spark-defaults.conf
vi ~/spark/conf/spark-defaults.conf

加入
# 修正spark找不到jar檔警告
spark.yarn.jars                                           hdfs://nn:9000/user/spark_jars/*.jar

# 設定spark序列化，記憶體及CPU相關
spark.serializer                                          org.apache.spark.serializer.KryoSerializer
spark.driver.memory                                       1g
spark.executor.memory                                     512m
```

# 啟動spark on yarn cluster
```
cd ~/spark/sbin
./start-all.sh
pyspark --master yarn
```
