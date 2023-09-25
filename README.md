This lab covers material from week 3 and will span across the next 2 weeks.
We will install HIVE and PIG and start practicing with their basic functions separately. 
Both of these applications can either run on top of a working HDFS / Mapreduce cluster, or they can run in local mode (useful for debugging).

# Lab2 - HIVE

There are 6 key steps to complete in order run Hive:

* HDFS (Hadoop) setup should be present
* Setup environment variables in the appropriate file depending on your system
* Hive need external database to store Hive Metadata called Metastore. Setup either mysql or Derby database for Hive Metastore; we assume here you are using Derby as it comes with Hive and is easier to use. 
   - If you use mysql, you need to Download MySql connector jar and place it in Hive library.
* Setup configuration files for local Hive (.xml files)
* Setup HDFS for storing Hive data (creating the required directories)
* Configure metastore: most guides refer to Mysql but we are going to use Derby metastore
* Starting Hive (commandline shell)

## Download and Install Hive
You can find an overall guide [here](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHive). Key steps are summarised below.
Please note you need a working Hadoop installation (See [Lab1](https://github.com/CA4022/Lab1-Configuration-HadoopMR))

If you are working on WSL please also look into [this guide](https://kontext.tech/column/hadoop/309/apache-hive-311-installation-on-windows-10-using-windows-subsystem-for-linux)

Installation guide for Mac OS X are available below (skip to the HIVE section):

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

* Exit hive shell
  - `$ >exit;`

* Where are tables stored? (you need to clean this up if you want to recreate the same table)
  - `$ hdfs dfs -ls /user/hive/warehouse/table-name`


## Hive Examples
* Check examples to Create, Load, Query data with Hive:
  - [Simple example queries (from step 15.)](https://www.java-success.com/10-setting-getting-started-hive-mac/)
   
* Create your own tables and load data from a .txt file you have created or downloaded, then practice some queries.
  - [Example: load csv file in HIVE table](https://bigdataprogrammers.com/load-csv-file-in-hive/) 
* You can find more query examples and SQL cheat-sheet [here](https://hortonworks.com/blog/hive-cheat-sheet-for-sql-users/)
* Check manual on Loop for HIVEQL basics with examples
   
## Hive errors and fixes
Below is a list of possible errors you might encounter when installing and running Hive, and how to fix them.

*  [Guava incompatibility error](https://phoenixnap.com/kb/install-hive-on-ubuntu)
*  "Name node is in safe mode" error:
   - Check if namenode safemode is ON: `$ $HADOOP_HOME/bin/hadoop dfsadmin –safemode get`
   - If this is the case, disable it: `$ $HADOOP_HOME/bin/hadoop dfsadmin –safemode leave`
* URISyntaxException: Check the problematic string into hive-site.xml file and replace it with correct path
* Other troubleshooting tips [here](https://kb.databricks.com/metastore/hive-metastore-troubleshooting.html)

# Lab2 - PIG
## Download and Install PIG
The official apache manual is availalbe [here](http://pig.apache.org/docs/r0.17.0/start.html). Key steps are summarised below.

* Get and install pig from one of the [mirror sites](http://www.apache.org/dyn/closer.cgi/pig/), copying it into the same directory where you have your hadoop root

   - `$ wget https://ftp.heanet.ie/mirrors/www.apache.org/dist/pig/pig-0.17.0/pig-0.17.0.tar.gz `
   - `$ tar -xvzf pig-0.17.0.tar.gz`
   - `$ cp -avi pig-0.17.0.tar.gz $HADOOP_HOME/../pig-0.17.0`
   
* Note you will need Java >6 and Hadoop 2.x or greater  
* Make sure your $HADOOP_HOME environment variable is correctly set
* Set $PIG_HOME and add pig to PATH
   
   - `$ export PIG_HOME=<your pig home>`
   - `$ export PATH=$PATH:$PIG_HOME/bin`

* Test your installation: `$ pig -help`
* Run the grunt shell
  - in local mode: `$ pig -x local`
  - in mapreduce mode (default): `$ pig -x mapreduce`
* Exit the grunt shell: `$grunt> quit`

## PIG Examples
1. Let's try Wordcount (locally first)

```
lines = LOAD 'local_file.txt' AS (line:chararray);
words = FOREACH lines GENERATE FLATTEN(TOKENIZE(line)) as word;
grouped = GROUP words BY word;
wordcount = FOREACH grouped GENERATE group, COUNT(words);
DUMP wordcount;
```
* NOTE: if you run pig on mapreduce, you need to make sure the input file is on HDFS, using 'hdfs://localhost:8020/user/<username>/input/input_file.txt'

2. A Toy Example:
      - Create two CSV file as follows:
            
            A.txt containing two lines: 0,1,2 and 1,3,4
            
            B.txt containing two lines: 0,5,2 and 1,7,8
   
      - Run the PIG Latin commands below, one by one from shell, and observe what is contained in d, e, f and g after each dump (note the content of g and reflect upon the way pig handles schema on the fly
```
a = LOAD ‘A.txt’ using PigStorage(‘,’); 
b = LOAD ‘B.txt’ using PigStorage(‘,’); 
c = UNION a,b;
SPLIT c INTO d IF $0==0, e IF $0==1; 
dump d;
dump e;
f = FILTER c BY $1>3;
dump f;
A = LOAD ‘A.txt’ using PigStorage(‘,’) as (a1:int, a2:int, a3:int); 
B = LOAD ‘B.txt’ using PigStorage(‘,’) as (b1:int, b2:int, b3:int); 
C = UNION A,B;
G = FOREACH C GENERATE a2, a2*a3;
dump G;
```

3. More examples:
   - [PigLatin basics](http://pig.apache.org/docs/r0.17.0/basic.html#load) 
   - [git script examples](https://gist.github.com/brikis98/1332818)


