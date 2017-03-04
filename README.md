# Installing-Hadoop-in-PseudoDistributed-Mode

Installing Packages for the linux 
==================================	
sudo apt-get update --> updates the list of packages and versions
sudo apt-get upgrade --> it installs the newer versions of packages

sudo add-apt-repository ppa:webupd8team/java --> download the ppa repository from webupd8team
sudo apt-get update --> Update Command

installing java
==================================

sudo apt-get install oracle-java8-installer --> install java (PATH: usr/lib/jvm/java-8-oracle)
java -verison --> check installation of java

creating hadoop user
===================================

sudo addgroup hadoop --> creating hadoop user for mapreduce and hdfs. To avoid security issues it is recommended to create dedicated user for hadoop
sudo adduser --ingroup hadoop hduser --> creating a hadoop user named hduser ,others are optional


Giving root access to the created user 'hduser'
===============================================

sudo su root --> the terminal goes inside the root
sudo gedit /etc/sudoers	--> editing /etc/sudoers file for giving root privileges to 'hduser'

installing openssh server and ssh client
================================================

sudo apt-get install openssh-server -->installing openssh server

ssh access to hduser
================================================

sudo su hduser --> login with hduser
cd --> change the current dir
ssh-keygen -t rsa -P "" --> generate ssh key for hduser. If asked for the filename leave it blank..
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys --> adding newly created keys to authorized keys so that hadoop can use ssh without prompting for password


note: hadoop doesnot work on ipv6 so we need to update the file /etc/sysctl.conf
gedit /etc/sysctl.conf  --> opens the sysctl.conf file in gedit and we need to update the file.

Adding the folling lines in sysctl.conf file
=============================================
# disable ipv6
net.ipv6.conf.all.disable_ipv6
l.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1

sudo reboot --> reboot machine

NOTE : Now download apache hadoop from apache mirror

Installing hadoop
====================================
sudo su hduser --> change the user to hduser
sudo mv <path of hadoop file> </usr/local/hadoop> --> move the file to usr/local/hadoop
sudo chown hduser:hadoop -R /usr/local/hadoop --> assigning the ownership of this folder to hadoop
sudo mkdir -p /usr/local/hadoop_temp/hdfs/namenode --> making namenode dir
sudo mkdir -p /usr/local/hadoop_temp/hdfs/datanode --> making datanode dir
sudo chown hduser:hadoop -R /usr/local/hadoop_temp/ --> assigning the ownership of this folder to hadoop

updating environment variables for hadoop
====================================
sudo gedit .bashrc --> opens the .bashrc file in gedit

 Add below lines in .bashrc file
#hadoop environment variables
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export HADOOP_HOME=/usr/local/hadoop/hadoop-2.7.0
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
export PATH=$PATH:/usr/local/hadoop/bin/

configure hadoop-env.sh
===================================
export JAVA_HOME=/usr/lib/jvm/java-8-oracle

configure core-site.xml
====================================
<property>
	<name>fs.default.name</name>
	<value>hdfs://localhost:9000</value>
</property>

configure hdfs-site.xml
====================================
<property>
	<name>dfs.replication</name>
	<value>1</value>
</property>
<property>
	<name>dfs.namenode.name.dir</name>
	<value>/usr/local/hadoop_temp/hdfs/namenode</value>
</property>
<property>
	<name>dfs.datanode.data.dir</name>
	<value>/usr/local/hadoop_temp/hdfs/datanode</value>
</property>

configure yarn-site.xml
====================================

<property>
	<name>yarn.nodemanager.aux-services</name>
	<value>mapreduce_shuffle</value>
</property>
<property>
	<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
	<value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>

NOTE : for mapred-site.xml first copy mapred-site.xml.template file to mapred-site.xml

configure mapred-site.xml
====================================
<property>
	<name>mapreduce.framework.name</name>
	<value>yarn</value>
</property>


hdfs namenode -format --> Formats the namenode 
cd $HADOOP_HOME/sbin
./start.dfs.sh --> start services for namenode datanode secondary namenode
./start.yarn.sh --> start services for nodemanager and resource manager


# Congratulations your Hadoop cluster is configured













 
