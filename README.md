# Hadoop
## Nodes Preparations
### Java 8
```
sudo apt update
sudo apt upgrade
sudo apt install openjdk-8-jdk
```
### hosts
```sh
sudo nano /etc/hosts
```
```
10.0.0.249  node-0
10.0.0.89   node-1
10.0.0.227	node-2
```
### User
```
sudo adduser big
sudo usermod -a -G sudo big
su big
```
### ssh
```sh
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
sudo systemctl restart sshd
ssh localhost
```
### ssh 2
Login into master
```sh
cat ~/.ssh/id_rsa.pub
```
Copy output into clipboard

Login into slave nodes
```sh
nano ~/.ssh/authorized_keys
```
Paste at the end of file
```sh
sudo systemctl restart sshd
```
### Hadoop 3.3.1
```sh
wget https://dlcdn.apache.org/hadoop/common/stable/hadoop-3.3.1.tar.gz
tar -zxf hadoop-3.3.1.tar.gz
rm hadoop-3.3.1.tar.gz
```
### .bashrc
```sh
nano ~/.bashrc
```
```bash
# Hadoop
export HADOOP_HOME=/home/big/hadoop-3.3.1
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
```sh
source ~/.bashrc
```
### hadoop-env.sh
```sh
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
```sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
## Master Node Configurations
### hdfs-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///home/big/dfs/name</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///home/big/dfs/data</value>
  </property>
</configuration>
```
### core-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://node-0:9000/</value>
  </property>
</configuration>
```
### yarn-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.local-dirs</name>
    <value>/home/big/yarn/nm-local-dir</value>
  </property>
  <property>
    <name>yarn.nodemanager.log-dirs</name>
    <value>/home/big/yarn/userlogs</value>
  </property>
  <property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>node-0:8031</value>
  </property>
</configuration>
```
### mapred-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.address</name>
    <value>node-0:10020</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.webapp.address</name>
    <value>node-0:19888</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.intermediate-done-dir</name>
    <value>/mapred-history/done-intermediate</value>
  </property>
  <property>
    <name>mapreduce.jobhistory.done-dir</name>
    <value>/mapred-history/done</value>
  </property>
</configuration>
```
### workers
```sh
nano $HADOOP_HOME/etc/hadoop/workers
```
```
node-0
node-1
node-2
```
## Slave Nodes Configurations
### hdfs-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
```xml
<configuration>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///home/big/dfs/data</value>
  </property>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
</configuration>
```
### core-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/core-site.xml
```
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://node-0:9000/</value>
  </property>
</configuration>
```
### yarn-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.resourcemanager.resource-tracker.address</name>
    <value>node-0:8031</value>
  </property>
</configuration>
```
### mapred-site.xml
```sh
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
</configuration>
```
## Firewall
```bash
sudo nano /etc/default/ufw
```
Set everything to `ACCEPT`
```bash
sudo ufw reset
sudo ufw enable
```
## Format
Login into master
```sh
hdfs namenode –format
```
## Start
```sh
start-dfs.sh
start-yarn.sh
```
## Test
```sh
jps
```
```sh
hdfs dfsadmin -report
```
namenode:

http://168.138.179.18:9870

yarn:

http://168.138.179.18:8088
## Reset
If things get wrong, don't forget to reset after making changes
```bash
rm -r $HADOOP_HOME/logs/*
rm -r dfs/name/*
rm -r dfs/data/*
rm -r dfs/node/*

```
# HBase
```
wget https://dlcdn.apache.org/hbase/stable/hbase-2.4.9-bin.tar.gz
tar -zxf hbase-2.4.9-bin.tar.gz
rm hbase-2.4.9-bin.tar.gz
```
### .bashrc
```bash
nano ~/.bashrc
```
```bash
# HBase
export HBASE_HOME=/home/big/hbase-2.4.9
export PATH=$PATH:$HBASE_HOME/bin
```
```
source ~/.bashrc
```
### hbase-env.sh
```sh
nano $HBASE_HOME/conf/hbase-env.sh
```
```sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
### hbase-site.xml
```sh
nano $HBASE_HOME/conf/hbase-site.xml
```
```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://node-0:9000/hbase</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>node-0,node-1,node-2</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>hdfs://node-0:9000/zookeeper</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>9181</value>
  </property>
</configuration>
```
### regionservers
```sh
nano $HBASE_HOME/conf/regionservers
```
```
node-1
node-2
```
## Start
```bash
start-hbase.sh
```
webui:
http://168.138.179.18:16010
### Reset
```
rm -r $HBASE_HOME/logs/*
rm -r zookeeper/*
```
