# Installation

## Oracle Java

Download

	wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/7u71-b14/jdk-7u71-linux-x64.tar.gz"

Extract

	tar -xzf jdk-7u71-linux-x64.tar.gz
	sudo mv jdk-7u71-linux-x64 /usr/java

## install alternatives

Change java to Oracle :

	alternatives --install /usr/bin/java java /usr/java/jdk1.7.0_71/bin/java 2
	alternatives --config java

Vérifier version :
	
	java -version

Same for javac :

	alternatives --install /usr/bin/javac javac /usr/java/jdk1.7.0_71/bin/javac 2
	alternatives --config javac

# install cassandra

	wget http://www.apache.org/dyn/closer.cgi?path=/cassandra/2.1.0/apache-cassandra-2.1.0-bin.tar.gz

	tar -xzf apache-cassandra-2.1.0-bin.tar.gz
	sudo mv apache-cassandra-2.1.0-bin /opt/
	sudo ln -s /opt/apache-cassandra-2.1.0-bin

	export PATH=/opt/cassandra/bin:$PATH

# install spark

	wget http://d3kbcqa49mib13.cloudfront.net/spark-1.1.0-bin-hadoop2.4.tgz
	tar -zxf spark-1.1.0-bin-hadoop2.4.tgz
	sudo mv spark-1.1.0-bin-hadoop2.4 /opt/
	sudo ln -s spark-1.1.0-bin-hadoop2.4/ spark

# compile spark-cassandra-connector

Cloner le repository

	git clone https://github.com/datastax/spark-cassandra-connector.git

	cd spark-cassandra-connector

Installer SBT

	wget https://dl.bintray.com/sbt/rpm/sbt-0.13.6.rpm
	chmod u+x sbt-0.13.6.rpm
	./sbt-0.13.6.rpm

Builder le Jar Assembly :

	sbt assembly

Les packages jar sont situés aux emplacement suivant :

	spark-cassandra-connector/target/scala-2.10/
	spark-cassandra-connector-java/target/scala-2.10/

Déplacer le jar scala à un emplacement défini

	sudo mkdir /opt/spark/connector
	sudo mv spark-cassandra-connector-assembly-1.2.0-SNAPSHOT.jar /opt/spark/connector

Ajouter les jar au classpath de Spark
	
	sudo echo "SPARK_CLASSPATH=/opt/spark/connector/*" >> /opt/spark/conf/spark-env.sh

# spark cassandra connector

# Configuration spark

Ajouter le 


# Lancer Cassandra

	cassandra

Ouvrir le shell Cassandra
	
	cqlsh

Créer un keyspace, une table et insérer des données
	
	CREATE KEYSPACE test WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1 };
	CREATE TABLE test.kv(key text PRIMARY KEY, value int);

	INSERT INTO test.kv(key, value) VALUES ('key1', 1);
	INSERT INTO test.kv(key, value) VALUES ('key2', 2);

# Test Spark-Shell

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




# Test spark-cassandra-connector-java

Lancer JavaDemo avec les paramètres 
	
	local[2] 127.0.0.1