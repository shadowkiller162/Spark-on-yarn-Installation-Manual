## 出現Neither spark.yarn.jars nor spark.yarn.archive is set, falling back to uploading libraries under SPARK_HOME.錯誤

```
hadoop fs -ls -R /
hadoop fs -mkdir /user/spark_jars/
hadoop fs -copyFromLocal ~/spark/jars/*.jar /user/spark_jars/
```
在~/spark/conf檔中修改spark-defaults.conf，加入
```
spark.yarn.jars hdfs://bthadoopNN:9000/user/spark_jars/*.jar
```
## 如何用jupyter notebook執行pyspark
```
export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'
```
