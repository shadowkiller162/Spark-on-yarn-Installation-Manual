# 下載並安裝scala-2.12.2
```
wget https://downloads.lightbend.com/scala/2.12.2/scala-2.12.2.rpm
sudo rpm -ivh scala-2.12.2.rpm
```

# 加入scala環境變數
```
vi ~/.bashrc

export SCALA_HOME=/usr/share/scala
export PATH=$PATH:$SCALA_HOME/bin
```
