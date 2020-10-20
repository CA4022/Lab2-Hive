# Lab2 - Hive and Pig

This lab covers material from week 3. 
We will install HIVE and PIG and start practicing with their basic functions separately. 
Both of these applications can either run on top of a working HDFS / Mapreduce cluster, or they can run in local mode (useful for debugging).

## Download and Install Hive
You can find an overall guide [here](https://cwiki.apache.org/confluence/display/Hive/GettingStarted#GettingStarted-RunningHive). This can be a good starting point to solve errors you might encounter in the process. The key steps are summarised below.
<!-- install on mac: https://bigdatalatte.wordpress.com/2017/02/01/install-hadoop-yarn-hive-on-a-macbook-pro-el-capitan/-->

1. Get and install hive from one of the [mirror sites](http://www.apache.org/dyn/closer.cgi/hive/), copying it into the same directory where you have your hadoop root

   - `$ wget https://ftp.heanet.ie/mirrors/www.apache.org/dist/hive/hive-3.1.2/apache-hive-3.1.2-bin.tar.gz `
   - `$ tar -xvzf apache-hive-3.1.2-bin.tar.gz `
   - `$ cp -avi apache-hive-3.1.2-bin /usr/local/Cellar/hive-3.1.2`

   Some key steps:
   
   * Note you will need Java >6 and Hadoop 0.20.x or greater  
   * Make sure your $HADOOP_HOME environment variable is correctly set
   * Set $HIVE_HOME and add hive to PATH
   
   - `$ export HIVE_HOME=/usr/local/Cellar/hive-3.1.2`
   - `$ export PATH=/usr/local/Cellar/hive-3.1.2/bin:$PATH`
   
   * Bootstrap locations used to store files on HDFS (you need to be in your hive home directory)
      - `$ bin/hdfs dfs -mkdir /tmp` should already exist from hadoop installation
      - `$ bin/hdfs dfs -mkdir /user/hive/warehouse`
      - `$ bin/hdfs dfs -chmod g+w /tmp`should already have the right access rights from hadoop installation
      - `$ bin/hdfs dfs -chmod g+w /user/hive/warehouse`
   
   * Configuration file (optional)
      - `$ cp $HIVE_HOME/conf/hive-default.xml.template $HIVE_HOME/conf/hive-site.xml`
   
   * Initialise derby database (mysql also an option but derby is easier to use)
     - `$ $HIVE_HOME/bin/schematool –initSchema –dbType derby`
     - Note: you might need to check your hive-site.xml if you get errors on the type of database and make sure it is set to be derby
   
   * Run the shell (of the three modes to run HIVE, we will use command line)
      - `$ $HIVE_HOME/bin/hive`
   <!-- see this tutorial for guava files errors: https://phoenixnap.com/kb/install-hive-on-ubuntu -->
   <!-- see this for :Name node is in safe mode" error: -->
   <!-- for safemode error: `$ hadoop dfsadmin –safemode [get/enter/leave]` -->
2. Check examples to Create, Load, Query data with Hive (slide 17 onwards)
   * <http://courses.coreservlets.com/Course-Materials/pdf/hadoop/07-Hive-01.pdf>
   <!-- import CSV and generate database: http://djkooks.github.io/hadoop-hive-setup>
   <!-- more example queries: https://www.java-success.com/10-setting-getting-started-hive-mac/ -->
   
3. Create your own tables and load data from a .txt file you have created or downloaded, then practice some queries.
   You can find more query examples and SQL cheat-sheet at the links below
   
   * https://hortonworks.com/blog/hive-cheat-sheet-for-sql-users/
