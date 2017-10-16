# 下載python2.7.13並解壓縮
```
wget https://www.python.org/ftp/python/2.7.13/Python-2.7.13.tgz
tar -zxvf Python-2.7.13.tgz
```
# 安裝編譯相關套件
```
sudo yum install gcc openssl openssl-devel ncurses-devel.x86_64  bzip2-devel sqlite-devel python-devel zlib zlib-devel -y
```
# 安裝並執行編譯
```
cd Python-2.7.13
sudo ./configure --prefix=/home/hadoop/python
sudo make
````
# 不要使用make install，否則會覆蓋系統自帶python
```
sudo make altinstall
```
# 備份舊版本，連接新版本
```
sudo mv /usr/bin/python /usr/bin/python2.7.5
sudo ln -s /home/hadoop/python/bin/python2.7 /usr/bin/python

sudo chown -hR hadoop:hadoop /home/hadoop/
```

# 安裝完後yum無法正常使用解法
```
sudo vi /usr/bin/yum

將 #!/usr/bin/python
改為 #!/usr/bin/python2.7
```
sudo vi /usr/libexec/urlgrabber-ext-down

將 #!/usr/bin/python
改為 #!/usr/bin/python2.7
```
