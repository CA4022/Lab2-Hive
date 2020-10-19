# Lab2 - Hive and Pig

This lab covers material from week 3. 
We will install HIVE and PIG and start practicing with their basic functions separately. 
Both of these applications can either run on top of a working HDFS / Mapreduce cluster, or they can run in local mode (useful for debugging).

## Download and Install Hive
1. Get and install hive from one of the mirror sites, copying it into the same directory where you have your hadoop root

   - `$ wget https://ftp.heanet.ie/mirrors/www.apache.org/dist/hive/hive-3.1.2/apache-hive-3.1.2-bin.tar.gz `
   - `$ tar -xvzf apache-hive-3.1.2-bin.tar.gz `
   - `$ cp -avi apache-hive-3.1.2-bin /usr/local/Cellar/hive-3.1.2`

   Some key steps:
   
   * Note you will need Java >6 and Hadoop 0.20.x or greater
   
   * Make sure your $HADOOP_HOME environment variable is correctly set
   
   * Set $HIVE_HOME and add hive to PATH
   
   - `$ export HIVE_HOME=/usr/local/Cellar/hive-3.1.2`
   - `$ export PATH=/usr/local/Cellar/hive-3.1.2/bin:$PATH`
   
   * Bootstrap locations used to store files on HDFS
      - `$ bin/hdfs dfs -mkdir /tmp` should already exist from hadoop installation
      - `$ bin/hdfs dfs -mkdir /user/hive/warehouse`
      - `$ bin/hdfs dfs -chmod g+w /tmp`should already have the right access rights from hadoop installation
      - `$ bin/hdfs dfs -chmod g+w /user/hive/warehouse`
   
   * Run the shell (of the three modes to run HIVE, we will use command line)
      - `$ hive`

2. Check examples to Create, Load, Query data with Hive (slide 17 onwards)
   * <http://courses.coreservlets.com/Course-Materials/pdf/hadoop/07-Hive-01.pdf>
   
3. Create your own tables and load data from a .txt file you have created or downloaded, then practice some queries.
   You can find more query examples and SQL cheat-sheet at the links below
   
   * https://hortonworks.com/blog/hive-cheat-sheet-for-sql-users/
   * https://support.treasuredata.com/hc/en-us/articles/360001457347-Hive-Query-Language#hive-example-query-catalog
