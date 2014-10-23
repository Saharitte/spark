# Installation

## Cassandra

Prérequis :
* Java jdk7
* Python 2.7
* Jna


	wget http://wwwftp.ciril.fr/pub/apache/cassandra/2.1.0/apache-cassandra-2.1.0-bin.tar.gz

	tar -xzf apache-cassandra-2.1.0-bin.tar.gz
	mv apache-cassandra-2.1.0 /opt/
	ln -s /opt/apache-cassandra-2.1.0 cassandra

Ajouter à .bashrc
	export PATH=/opt/cassandra/bin:$PATH

## Spark

	wget http://d3kbcqa49mib13.cloudfront.net/spark-1.1.0-bin-hadoop2.4.tgz
	tar -zxf spark-1.1.0-bin-hadoop2.4.tgz
	mv spark-1.1.0-bin-hadoop2.4 /opt/
	ln -s spark-1.1.0-bin-hadoop2.4/ spark

Ajouter à .bashrc
	export PATH=/opt/spark/bin:$PATH


## spark-cassandra-connector

Cloner le repository

	git clone https://github.com/datastax/spark-cassandra-connector.git

	cd spark-cassandra-connector

Builder le Jar Assembly :

	sbt assembly

Les packages jar sont situés aux emplacement suivant :

	spark-cassandra-connector/target/scala-2.10/
	spark-cassandra-connector-java/target/scala-2.10/

Déplacer le jar scala à un emplacement défini

	mkdir /opt/spark/connector
	mv spark-cassandra-connector-assembly-1.2.0-SNAPSHOT.jar /opt/spark/connector

Ajouter les jar au classpath de Spark
	
	echo "SPARK_CLASSPATH=/opt/spark/connector/*" >> /opt/spark/conf/spark-env.sh

# Test
## Lancer Cassandra

	cassandra

Ouvrir le shell Cassandra
	
	cqlsh

Créer un keyspace, une table et insérer des données
	
	CREATE KEYSPACE test WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1 };
	CREATE TABLE test.kv(key text PRIMARY KEY, value int);

	INSERT INTO test.kv(key, value) VALUES ('key1', 1);
	INSERT INTO test.kv(key, value) VALUES ('key2', 2);

## Spark-Shell

	sc.stop

	import com.datastax.spark._
	import com.datastax.spark.connector._
	import com.datastax.spark.connector.cql._
	import org.apache.spark.{SparkConf, SparkContext, Logging}

	val conf = new SparkConf(true).set("spark.cassandra.connection.host", "127.0.0.1")
	val sc = new SparkContext("spark://127.0.0.1:7077", "test", conf)
	val rdd = sc.cassandraTable("test", "kv")
	println(rdd.count)
	println(rdd.first)
	println(rdd.map(_.getInt("value")).sum)




## Test spark-cassandra-connector-java

Lancer JavaDemo avec les paramètres 
	
	local[2] 127.0.0.1