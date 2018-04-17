# 使用 CentOS7 Linux 系統架設 Hadoop 單節點測試環境。

## Java 開發與執行環境

先安裝JAVA
```
wget --no-cookie --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracl.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm
rpm -ivh jdk-8u131-linux-x64.rpm
```
安裝完後，檢查一下
```
java -version
```

## 設定Hadoop專用帳號

創設hadoop群組
```
groupadd hadoop
```
創設hadoop帳號並將帳號指定給hadoop群組
```
useradd hadoop -g hadoop
```
檢查hadoop帳號是否創建成功
```
ll -d /home/hadoop
```
設定hadoop密碼
```
passwd hadoop
```
以root權限設定hadoop帳號sudo權限
```
visudo
```
修改成以下這樣:
```
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
hadoop  ALL=(ALL)       ALL
```
轉換成hadoop帳號
```
su hadoop
```

## 設定 SSH 金鑰認證登入

安裝 ssh 伺服器：
```
sudo yum install openssh* -y
```
設定 ssh 金鑰認證登入：
```
ssh-keygen
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```
檢查 ssh 登入是否需要密碼：
```
ssh localhost
```

## 安裝 Hadoop

從 Hadoop 的官方網站下載 Hadoop：
```
wget ftp://ftp.twaren.net/Unix/Web/apache/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz
```
解壓縮後，將 Hadoop 的目錄放置在要安裝的位置：
```
tar -zxvf hadoop-2.7.3.tar.gz
mv hadoop-2.7.3 hadoop
```

## 設定 PATH 環境變數

將 Hadoop 的執行檔加入自己的 PATH 環境變數中，這樣在執行 Hadoop 時會比較方便。
```
vi ~/.bashrc

export JAVA_HOME=/usr/java/jdk1.8.0_131
export HADOOP_HOME=/home/hadoop/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"

source ~/.bashrc
```
測試執行 hadoop 指令：
```
hadoop version
```
會看到如下的畫面:
> Hadoop 2.7.3
Subversion Hadoop 2.7.3
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r baa91f7c6bc9cb92be5982de4719c1c8af91ccff
Compiled by root on 2016-08-18T01:41Z
Compiled with protoc 2.5.0
From source with checksum 2e4ce5f957ea4db193bce3734ff29ff4
This command was run using /home/hadoop/hadoop/share/hadoop/common/hadoop-common-2.7.3.jar

## 關閉防火牆及selinux安全性

設定安全性
```
setenforce 0
```
永久停止selinux安全性
```
sudo vi /etc/selinux/config
SELINUX=disabled
```
停止防火牆
```
sudo systemctl stop firewalld
sudo systemctl disable firewalld
```
設定SSH登入免詢問
```
sudo vi /etc/ssh/ssh_config
StrictHostKeyChecking no
systemctl restart sshd
```

## 修改Hadoop組態設定檔

設定hadoop-env.sh
```
vi /home/hadoop/hadoop/etc/hadoop/hadoop-env.sh
```
將
```
export JAVA_HOME=${JAVA_HOME}
```
修改為
```
export JAVA_HOME=/usr/java/jdk1.8.0_131
```

設定core-site.xml
```
vi /home/hadoop/hadoop/etc/hadoop/core-site.xml
```
在`<configuration></configuration>`間，輸入下列內容:
```
<property>
	<name>fs.default.name</name>
	<value>hdfs://localhost:9000</value>
</property>
```

設定yarn-site.xml
```
vi /home/hadoop/hadoop/etc/hadoop/yarn-site.xml
```
在`<configuration></configuration>`間，輸入下列內容:
```
<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
</property>
<property>
	<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
	<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
```

設定mapred-site.xml
```
cp /home/hadoop/hadoop/etc/hadoop/mapred-site.xml.template /home/hadoop/hadoop/etc/hadoop/mapred-site.xml
vi /home/hadoop/hadoop/etc/hadoop/mapred-site.xml
```
在`<configuration></configuration>`間，輸入下列內容:
```
<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
</property>
```

設定hdfs-site.xml
```
vi /home/hadoop/hadoop/etc/hadoop/hdfs-site.xml
```
在`<configuration></configuration>`間，輸入下列內容:
```
<property>
	<name>dfs.replication</name>
	<value>3</value>
</property>
<property>
	<name>dfs.namenode.name.dir</name>
	<value>file:///home/hadoop/hadoop_data/hdfs/namenode</value>
</property>
<property>
	<name>dfs.datanode.name.dir</name>
	<value>file:///home/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```

## 建立與格式化HDFS目錄

建立namenode目錄
```
mkdir –p /home/hadoop/hadoop_data/hdfs/namenode
```
建立data目錄
```
mkdir –p /home/hadoop/hadoop_data/hdfs/datanode
```
將HDFS進行格式化
```
hadoop namenode –format
```
啟動Hadoop
```
start-all.sh
```
檢查Hadoop是否啟動
```
jps
```
關閉Hadoop
```
stop-all.sh
```
