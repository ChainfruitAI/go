sudo apt update
sudo apt install default-jdk openssh-server

ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub localhost

#hadoop
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz

tar zxvf hadoop-3.3.6.tar.gz

sudo mv hadoop-3.3.6 /usr/local/hadoop
ls /usr/local/
sudo chown -R $USER:$USER /usr/local/hadoop


nano ~/.bashrc

# --- JAVA HOME --- BOTTOM
export JAVA_HOME=/usr/lib/jvm/default-java

# --- HADOOP HOME and PATH ---
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

source ~/.bashrc

cd /usr/local/hadoop/etc/hadoop

#link java
nano hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/default-java


# Inside CONFIG <> section
nano core-site.xml
<property>
    <name>fs.defaultFS</name>
    <value>hdfs://localhost:9000</value>
</property>

nano hdfs-site.xml
<property>
    <name>dfs.replication</name>
    <value>1</value>
</property>
<property>
    <name>dfs.namenode.name.dir</name>
    <value>file:///usr/local/hadoop/data/namenode</value>
</property>
<property>
    <name>dfs.datanode.data.dir</name>
    <value>file:///usr/local/hadoop/data/datanode</value>
</property>

ls -l | grep mapred
sudo nano mapred-site.xml

#cp mapred-site.xml.template mapred-site.xml
nano mapred-site.xml
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
</property>

nano yarn-site.xml
<property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
</property>
<property>
    <name>yarn.resourcemanager.hostname</name>
    <value>localhost</value>
</property>

mkdir -p /usr/local/hadoop/data/namenode
mkdir -p /usr/local/hadoop/data/datanode

#hdfs namenode -format
bin/hdfs namenode -format
#sbin/start-dfs.sh
start-dfs.sh
start-yarn.sh

jps

