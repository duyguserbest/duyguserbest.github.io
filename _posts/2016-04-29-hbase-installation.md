---
layout: post
title:  "Installing HBase on a Distributed Hadoop Cluster"
date:   2016-04-29 16:23:00
tags:   hbase installation
---

Installation guide for distributed HBase with Zookeeper(which is a requirement for HBase, is being managed by HBase itself.).

## Prerequisites:
Hadoop must be installed successfully.

## Installation:
Do everything listed below for every instance which is a node on hdfs network, you want to run HBase on.

1. Create a directory and navigate to created directory.
    * mkdir hbase
2. Change folder’s owner if needed: 
    * sudo chown -R hadoop:hadoop hbase/
    * cd hbase
3. Download hbase-0.94.12 to current directory.
    * wget "https://archive.apache.org/dist/hbase/hbase-0.94.12/hbase-0.94.12.tar.gz"
4. Extract hbase files to current directory.
    * tar -xvzf hbase-0.94.12.tar.gz
5. Navigate to extracted directory.
    * cd hbase-0.94.12/conf
6. Edit hbase-site.xml using vim and set proper values to configuration properties.
    * vim hbase-site.xml
7. Edit regionservers file and list all hosts that you would have running Zookeeper.
    * vim regionservers
    * server30
      server31
      server32
      server33
8. Add HBASE_HOME environment variable to bashrc.
    * vim ~/.bashrc
    * export HBASE_HOME=/opt/hbase/hbase-0.94.12
    * export PATH=$PATH:$HADOOP_HOME/bin:$HBASE_HOME/bin:$JAVA_HOME/bin
    * To test if variable is added successfully
        1. Logout from user session: 
            * exit
        2. Log back in:
            * sudo su - hadoop
        3. Echo environment variable:
            * echo $HBASE_HOME

9. Edit hbase-env.sh, uncomment and set proper values to properties.
    * export JAVA_HOME=/usr/lib/jvm/java-8-oracle
    * export HBASE_HEAPSIZE=4096
    * export HBASE_MANAGES_ZK=true
10. Symlink hdfs-site.xml under ${HBASE_HOME}/conf.
    * ln -sv /opt/hadoop/hadoop-1.0.4/conf/hdfs-site.xml hdfs-site.xml
11. Create HBase temp directory and Zookeeper data directory stated on hbase-site.xml.



hbase-site.xml:

~~~~~~~~
  <configuration>
      ...
    <property>
      <name>hbase.rootdir</name>
      <value>hdfs://{hdfs.namenode.address}:{hdfs.port}/hbase</value>
      <description>The directory shared by RegionServers.
      </description>
    </property>

    <property>
      <name>hbase.tmp.dir</name>
      <value>/work/hbase/tmp</value>
    </property>

    <property>
      <name>hbase.cluster.distributed</name>
      <value>true</value>
      <description>The mode the cluster will be in. Possible
        values are false: standalone and
        pseudo-distributed setups with managed
        Zookeeper true: fully-distributed with
        unmanaged Zookeeper Quorum (see hbase-env.sh)
      </description>
    </property>
    <property>
      <name>hbase.zookeeper.quorum</name>
      <value>{zookeeper.quorum.address}</value>
      <description>Comma separated list of servers in the
        ZooKeeper Quorum. For example, 
        "host1.mydomain.com,host2.mydomain.com,host3.mydomain.com".
        By default this is set to localhost for local
        and pseudo-distributed modes of operation. For
        a fully-distributed setup, this should be set
        to a full list of ZooKeeper quorum servers. If
        HBASE_MANAGES_ZK is set in hbase-env.sh this
        is the list of servers which we will
        start/stop ZooKeeper on.
      </description>
    </property>
    <property>
      <name>hbase.zookeeper.property.dataDir</name>
      <value>/opt/zookeeper/zk-data</value>
      <description>Property from ZooKeepers config zoo.cfg.
        The directory where the snapshot is stored.
      </description>
    </property>
    ...
  </configuration>
~~~~~~~~



## Starting HBase:

To start HBase run “./bin/start-hbase.sh” on “/opt/hbase/hbase-0.94.12” directory. The instance you have run the starter script becomes the master of the network. You must start Hadoop’s DFS and Map-Red frameworks before running this command.

## Testing Installation:

### Checking if HBase initialised successfully:

Run “jps” on terminal and check if “HQuorumPeer” and “HRegionServer” processes running for every instance in your HBase network. When you run “jps” command on master you should also see the master process named “HMaster”.

### Running hbase check:

When you run “hbase hbck”, there must be no inconsistencies detected. If an inconsistency has been detected, run “hbase hbck -repair” and/or “hbase hbck -fix” for easy fix.

### Checking HBase Shell:

If HBase initialised successfully, you should be able to run “hbase shell” command to access tables. After running “hbase shell” if you come across 

~~~~~~~~

java.lang.RuntimeException: java.lang.UnsatisfiedLinkError: /tmp/jffi3876542469706275102.tmp: /tmp/jffi3876542469706275102.tmp: failed to map segment from shared object: Operation not permitted

~~~~~~~~

this exception then this means you can “not execute” on your /tmp directory.  This can mean that your company security policy block execution on tmp directory. To solve this problem you have two options: first one is giving execution permission on tmp to users, or changing java tmp directory by setting a parameter. 
To solve the problem by changing java tmp directory:
    1. Create a new directory where the user you started HBase with has execution permission.
    2. Add -Djava.io.tmpdir parameter to HBASE_OPTS environment variable on hbase-env.sh.
    3. export HBASE_OPTS="-Djava.io.tmpdir=/work/hbase/hbase-java-temp -XX:+UseConcMarkSweepGC"
    4. Restart HBase.

### Stopping HBase:

Run “./bin/stop-hbase.sh” on “/opt/hbase/hbase-0.94.12” directory.

