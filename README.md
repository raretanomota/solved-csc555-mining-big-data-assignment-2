Download Link: https://assignmentchef.com/product/solved-csc555-mining-big-data-assignment-2
<br>
Suggested reading: <em>Mining of Massive Datasets</em>: Chapter 2.

<em>Hadoop: The Definitive Guide</em>: Chapter 17 (Hive).

<ul>

 <li>Describe how you would implement a MapReduce job consisting of Map and Reduce description. You can describe it in your own words or as pseudo-code. Keep in mind that map task reads the input file and produces (key, value) pairs. Reduce task takes a list of (key, value) pairs for each key and combines all values for each key.</li>

</ul>

Remember that Map operates on individual blocks and Reduce on individual keys with a set of values. Thus, for Mapper you need to state what your code does given a block of data and for Reduce you need to state what your reducer does <u>for each key</u>. You can assume that you know how to parse the file and extract numbers/names.




<ol>

 <li>For a Student table (ID, FullName, Address, Phone, Year, Month), convert</li>

</ol>

SELECT Year, Month, COUNT(FullName)

FROM Student

GROUP BY Year, Month;




<ol>

 <li>For Employee(EID, First, Last, Phone, Age) and Agent(AID, First, Last, Address), find everyone with the same name using MapReduce:</li>

</ol>

SELECT a.First, a.Last, EID, AID, Phone

FROM Employee as e, Agent as a

WHERE e.Last = a.Last AND e.First = a.First;




<ol>

 <li>Same tables:</li>

</ol>

SELECT Age, COUNT(DISTINCT a.Last)

FROM Employee, Agent

WHERE EID = AID

GROUP BY Age;




<ul>

 <li>Suppose you are tasked with analysis of the company’s web server logs. The log dump contains a large amount of information with up to 10 different attributes (columns). You regularly run a Hadoop job to perform analysis pertaining to 3 specific attributes – TimeOfAccess, OriginOfAccess and FileName.</li>

</ul>




<ol>

 <li>If a Mapper task fails while processing a block of data – what is the best location to restart it?</li>

</ol>




<ol>

 <li>If the job is executed with 5 Reducers</li>

</ol>




<ol>

 <li>How many files does the output generate?</li>

</ol>




<ol>

 <li>Suggest one possible hash function that may be used to assign keys to reducers.</li>

</ol>




<ol>

 <li>True or False?</li>

</ol>




<ol>

 <li>A message that was encrypted with a public key can be decrypted with a corresponding private key</li>

</ol>




<ol>

 <li>A message that was encrypted with a private key can be decrypted with a corresponding public key</li>

</ol>




<ul>

 <li>A message that was encrypted with a public key can only be read by its intended recipient, the holder of the private key</li>

</ul>




<u> </u>

<ul>

 <li>Consider a Hadoop job that processes an input data file of size equal to 38 disk blocks (38 different blocks, you can assume that HDFS replication factor is set to 1). The mapper in this job requires 1 minute to read and fully process a single block of data.  For the purposes of this assignment, you can assume that the reduce part of this job takes zero time.</li>

</ul>




<ol>

 <li>Approximately how long will it take to process the file if you only had one Hadoop worker node? You can assume that that only one mapper is created on every node.</li>

</ol>




<ol>

 <li>20 Hadoop worker nodes?</li>

</ol>




<ol>

 <li>50 Hadoop worker nodes?</li>

</ol>




<ol>

 <li>75 Hadoop worker nodes?</li>

</ol>




<ol>

 <li>Now suppose you were told that the replication factor has been changed to 3? That is, each block is stored in triplicate, but file size is still 38 blocks. Which of the answers (if any) in a)-c) above will have to change?</li>

</ol>




You can ignore the network transfer costs and other potential overheads as well as the possibility of node failure. If you feel some information is missing please be sure to state your assumptions.

<u> </u>

<ul>

 <li>In this section we are going to use Hive to run a few queries over the Hadoop framework. These instructions assume that you are starting from a working Hadoop installation. It should be sufficient to start your instance and the Hadoop framework on it.</li>

</ul>




Hive commands are listed in <strong>Calibri bold font</strong>




<ol>

 <li>Download and install Hive:</li>

</ol>




cd

(this command is there to make sure you start from home directory, on the same level as where hadoop is located)

wget <u>http://rasinsrv07.cstcis.cti.depaul.edu/CSC555/apache-hive-2.0.1-bin.tar.gz</u>

gunzip apache-hive-2.0.1-bin.tar.gz

tar xvf apache-hive-2.0.1-bin.tar




set the environment variables (can be automated by adding these lines in ~/.bashrc). If you don’t, you will have to set these variables every time you use Hive.

export HIVE_HOME=/home/ec2-user/apache-hive-2.0.1-bin

export PATH=$HIVE_HOME/bin:$PATH




$HADOOP_HOME/bin/hadoop fs -mkdir /tmp

$HADOOP_HOME/bin/hadoop fs -mkdir /user/hive/warehouse

(if you get an error here, it means that /user/hive does not exist yet. Fix that by running <strong>$HADOOP_HOME/bin/hadoop fs -mkdir -p /user/hive/warehouse instead</strong><strong>)</strong>




$HADOOP_HOME/bin/hadoop fs -chmod g+w /tmp

$HADOOP_HOME/bin/hadoop fs -chmod g+w /user/hive/warehouse




We are going to use Vehicle data (originally from <a href="https://www.fueleconomy.gov/feg/download.shtml">http://www.fueleconomy.gov/feg/download.shtml</a>)




You can get the already unzipped, comma-separated file from here:

wget <a href="http://rasinsrv07.cstcis.cti.depaul.edu/CSC555/vehicles.csv">http://rasinsrv07.cstcis.cti.depaul.edu/CSC555/vehicles.csv</a>




You can take a look at the data file by either

nano vehicles.csv or

more vehicles.csv (you can press space to scroll and q or Ctrl-C to break out)




Note that the first row in the data is the list of column names. What follows after commands that start Hive, is the table that you will create in Hive loading the first 5 columns. Hive is not particularly sensitive about invalid or partial data, hence if we only define the first 5 columns, it will simply load the first 5 columns and ignore the rest.

You can see the description of all the columns here (atvtype was added later)

<a href="https://www.fueleconomy.gov/feg/ws/index.shtml#vehicle">http://www.fueleconomy.gov/feg/ws/index.shtml#vehicle</a>




Create the ec2-user directory on the HDFS side (absolute path commands should work anywhere and not just in Hadoop directory as bin/hadoop does). Here, we are creating the user “home” directory <u>on the HDFS side</u>.




<u>hadoop fs -mkdir /user/ec2-user/</u>




Run hive (from the hive directory because of the first command below):

cd $HIVE_HOME

$HIVE_HOME/bin/schematool -initSchema -dbType derby

(NOTE: This command initializes the database metastore. If you need to restart/reformat or see errors related to meta store, run rm -rf metastore_db/ and then repeat the above initSchema command)

bin/hive




You can now create a table by pasting this into the Hive terminal:




<strong>CREATE TABLE VehicleData ( </strong>

<strong>barrels08 FLOAT, barrelsA08 FLOAT, </strong>

<strong>charge120 FLOAT, charge240 FLOAT,</strong>

<strong>city08 FLOAT) </strong>

<strong>ROW FORMAT DELIMITED FIELDS </strong>

<strong>TERMINATED BY ‘,’ STORED AS TEXTFILE;</strong>




You can load the data (from the local file system, not HDFS) using:




<strong>LOAD DATA LOCAL INPATH ‘/home/ec2-user/vehicles.csv’ </strong>

<strong>OVERWRITE INTO TABLE VehicleData;</strong>




(NOTE: If you downloaded vehicles.csv file into the hive directory, you have to change file name to /home/ec2-user/apache-hive-2.0.1-bin/vehicles.csv instead)




Verify that your table had successfully loaded by running

<strong>SELECT COUNT(*) FROM VehicleData;</strong>

(Copy the query output and report how many rows you got as an answer.)




Run a couple of HiveQL queries to verify that everything is working properly:

<strong> </strong>

<strong>SELECT MIN(barrels08), AVG(barrels08), MAX(barrels08) FROM VehicleData;</strong>

(copy the output from that query)

<strong> </strong>

<strong>SELECT (barrels08/city08) FROM VehicleData;</strong>

(you do not need to report the output from that query, but report “Time taken”)




Next, we are going to output three of the columns into a separate file (as a way to transform data for further manipulation that you may be interested in)




<strong>INSERT OVERWRITE DIRECTORY ‘ThreeColExtract’ </strong>

<strong>SELECT barrels08, city08, charge120</strong>

<strong>FROM VehicleData;</strong>




You can now exit Hive by running <strong>exit;</strong>




And verify that the new output file has been created (the file will be called 000000_0)

The file would be created <u>in HDFS</u> in user home directory (/user/ec2-user/ThreeColExtract)




<u>Report the size of the newly created file</u>.




Next, you should go back to the Hive terminal, create a new table that is going to load 8 columns instead of 5 in our example (i.e. create and load a new table that defines 8 columns by including columns city08U,cityA08,cityA08U) and use Hive to generate a new output file containing only the city08U and cityA08U columns from the vehicles.csv file.  Report the size of that output file as well.


