This lab covers material from week 3 and will span across the next 2 weeks.
We will install HIVE and practice with simple queries and loading files into external tables. 
Hive can either run on top of a working HDFS / Mapreduce cluster, or in local mode (useful for debugging).

# Lab2 - HIVE

There are 6 key steps to complete in order run Hive:

* HDFS (Hadoop) setup should be present
* Setup environment variables in the appropriate file depending on your system
* Hive need external database to store Hive Metadata called Metastore. Setup either mysql or Derby database for Hive Metastore; we assume here you are using Derby as it comes with Hive and is easier to use. 
   - If you use mysql, you need to Download MySql connector jar and place it in Hive library.
* Setup configuration files for local Hive (.xml files)
* Setup HDFS for storing Hive data (creating the required directories)
* Starting Hive (commandline shell)

## Download and Install Hive
You can find an overall guide [here](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHive). Key steps are summarised below.
Please note you need a working Hadoop installation (See [Lab1](https://github.com/CA4022/Lab1-Configuration-HadoopMR))

If you are working on WSL please also look into [this guide](https://kontext.tech/column/hadoop/309/apache-hive-311-installation-on-windows-10-using-windows-subsystem-for-linux)

Installation guide for Mac OS X are available below (skip to the HIVE section and use DERBY instead of MySQL):

[Hive on Mac OSX El Capitan](https://bigdatalatte.wordpress.com/2017/02/01/install-hadoop-yarn-hive-on-a-macbook-pro-el-capitan/) 

[Hive on Mac OSX Catalina](https://medium.com/@hannahstrakna/installing-hadoop-with-hive-on-macos-catalina-using-homebrew-b4d384d455e4)

* Get and install hive from one of the [mirror sites](http://www.apache.org/dyn/closer.cgi/hive/), copying it into the same directory where you have your hadoop root

   - `$ wget https://ftp.heanet.ie/mirrors/www.apache.org/dist/hive/hive-3.1.2/apache-hive-3.1.2-bin.tar.gz `
   - `$ tar -xvzf apache-hive-3.1.2-bin.tar.gz `
   - `$ cp -avi apache-hive-3.1.2-bin $HADOOP_HOME/../hive-3.1.2`
   
* Note you will need Java >6 and Hadoop 0.20.x or greater  
* Make sure your $HADOOP_HOME environment variable is correctly set
* Set $HIVE_HOME and add hive to PATH
   
   - `$ export HIVE_HOME=<your hive home>`
   - `$ export PATH=$HIVE_HOME:$PATH`
 
* Configuration file
   - `$ cp $HIVE_HOME/conf/hive-env.sh.template $HIVE_HOME/conf/hive-config.sh`
   - `$ nano $HIVE_HOME/conf/hive-config.sh`
   - add the following line of code into `hive-config.sh` file: `$ export HADOOP_HOME=[your $HADOOP_HOME path]`
   - `$ nano $HIVE_HOME/conf/hive-site.xml` creates your configuration file for Hive to run in pseudo-distributed mode (not needed when you run hive locally)
   - Note: check your hive-site.xml and make sure the type of database is set to derby:
   
```xml 
<property>
   <name>javax.jdo.option.ConnectionURL</name>
   <value>jdbc:derby:$HOME/hadoop/metastore_db;create=true </value>
   <description>JDBC connect string for a JDBC metastore </description>
</property>
```
  
* Initialise derby database (mysql also an option but derby is easier to use)
  <!-- `$ $HIVE_HOME/bin/schematool –initSchema –dbType derby`-->
  - `$ $HIVE_HOME/bin/schematool -dbType derby -initSchema`
     
 
* Bootstrap locations used to store files on HDFS (you need to make sure hadoop is running!)

   - Create warehouse folder under hive and provide permission:
     
     `$ bin/hdfs dfs -mkdir -p /user/hive/warehouse`
     
     `$ bin/hdfs dfs -chmod g+w /user/hive/warehouse`


   - Create tmp folder in root and provide permission
     
     `$ bin/hdfs dfs -mkdir -p /tmp` should already exist from hadoop installation
    
     `$ bin/hdfs dfs -chmod g+w /tmp` hould already have the right access rights from hadoop installation
      
     `$ bin/hdfs dfs -mkdir -p /tmp/hive`
     
     `$ bin/hdfs dfs -chmod 777 /tmp/hive`
   

   
* Run the shell (of the three modes to run HIVE, we will use command line)
  - `$ $HIVE_HOME/bin/hive`

* Execute Hive queries (see example file in this repo and links in Hive Eamples and Tutorials below)
  
* Exit hive shell
  - `$ >exit;`

* Where are tables stored? (you need to clean this up if you want to recreate the same table)
  - `$ hdfs dfs -ls /user/hive/warehouse/table-name`


## Hive in local mode
You can run hive in local mode. First make sure you have the right permission and directories locally (create such directories if they do not exist yet):

 - `$ sudo chmod 777 /tmp/hive/*`
 - `$ mkdir /tmp/hive/warehouse`
 - `$ sudo chmod g+w /tmp/hive/warehouse`

Then you need to change some configuration variables. I suggest not to change hive-site.xml or core-site.xml properties, but instead modify the some of the necessary variables for hive local execution before launching the hive shell for a specific terminal session (with EXPORT) and to set other variables for a specific hive session (with SET command) as below:

* Use the EXPORT command to set HIVE_OPTS for a session (note that to unset this for a pseudo-distributed execution of HIVE you need to run the command `$ unset HIVE_OPTS`):

`$ EXPORT  HIVE_OPTS='-hiveconf mapreduce.framework.name=local -hiveconf fs.defaultFS=file:///tmp -hiveconf hive.metastore.warehouse.dir=file:///tmp/hive/warehouse -hiveconf hive.exec.mode.local.auto=true'`

This will will set the default warehouse dir to be /tmp/warehouse and the default file system to be in the local folder /tmp. With the above you have also created a path on local machine for mapreduce to work locally (note this is created in user home not in hadoop home as you write “/tmp…” and not “tmp…”) and you override the core-site.xml setup to run on local FS as opposed to HDFS.

* Use the SET command for other variables to be set for a specific hive session (this can be done from within the hive shell and it is reset once you exit hive’s CLI):

 <!-- can also set this by commandline: `$ hive> SET hive.exec.mode.local.auto=true; ` %(default is false) -->
 
 - `$ hive> SET hive.exec.mode.local.auto.inputbytes.max=50000000;`
 
 - `$ hive> SET hive.exec.mode.local.auto.input.files.max=5;`
 

Note that by default mapred.local.dir=/tmp/hadoop-username/mapred and this is ok. You need to make sure mapred.local.dir points to a valid local path so the default path should be there or else you can specify a different one

Check more info on HIVE configuraion at [Getting Started with Running Hive](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHiveCLI)


## Hive Examples and Tutorials
* Check examples to Create, Load, Query data with Hive:
  - [Simple example queries (from step 15.)](https://www.java-success.com/10-setting-getting-started-hive-mac/)
   
* Create your own tables and load data from a .txt file you have created or downloaded, then practice some queries.
  - [Example: load csv file in HIVE table](https://bigdataprogrammers.com/load-csv-file-in-hive/) 

* You can find more query examples and SQL cheat-sheet [here](https://hortonworks.com/blog/hive-cheat-sheet-for-sql-users/)

* Check manual on Loop for HIVEQL basics with examples

* Additional tutorials:
  - [Hive tutorial for beginners](https://www.guru99.com/hive-tutorials.html)
  - [Hive tutorial and refresher](https://www.analyticsvidhya.com/blog/2020/12/15-basic-and-highly-used-hive-queries-that-all-data-engineers-must-know/)
    
## Hive errors and fixes
Below is a list of possible errors you might encounter when installing and running Hive, and how to fix them.

*  [Guava incompatibility error](https://phoenixnap.com/kb/install-hive-on-ubuntu)
*  "Name node is in safe mode" error:
   - Check if namenode safemode is ON: `$ $HADOOP_HOME/bin/hadoop dfsadmin –safemode get`
   - If this is the case, disable it: `$ $HADOOP_HOME/bin/hadoop dfsadmin –safemode leave`
* URISyntaxException: Check the problematic string into hive-site.xml file and replace it with correct path
* Other troubleshooting tips [here](https://kb.databricks.com/metastore/hive-metastore-troubleshooting.html)


