# spark
Apache Spark Examples

The WordCount.java is a simple Java Spark application which counts the number of words of the file passed as
input argument. 

An additional test file with some random data is also present with the name test.txt. 
The files can be found on GitHub https://github.com/Saharitte/spark/blob/master/test.txt.

Setup the Spark environment:

Spark can be dowloaded at https://spark.apache.org/downloads.html. (pre-build)
Once downloaded and decompressed the package is ready to be used (an Hadoop installation is not required for the standalone execution), the only requirement is to have a Java virtual machine installed.

Run the Java application on Apache Spark

The Java application needs to be compiled first before executing it on Apache Spark. To compile the Java application from Maven:

1. open the command line and move to the root maven project with "cd /<path to the project root>"
2. execute the command "mvn package". 
3/maven will build the java file and save it on the target directory /<path to the project root>/target/spark-example-1.0-SNAPSHOT.jar
Once we have build the Java application first-example-1.0-SNAPSHOT.jar we can execute it locally on Apache Spark, this makes the entire testing process very easy.

On a command shell move to the spark installation directory and use the following command:

./bin/spark-submit --class org.sparkexample.WordCount --master local[2] /<path to maven project>/target/spark-examples-1.0-SNAPSHOT.jar /<path to a demo test file> /<path to output directory>

---where--------- 

"--class org.sparkexample.WordCount" is the main Java class with the public static void main method
"--master local[2]" starts the cluster locally using 2 CPU cores
<path to maven project> is the path to our maven project
<path to a demo test file> is a demo local file which contains some words, an example file can be downloaded at https://github.com/melphi/spark-examples/blob/master/first-example/src/test/resources/loremipsum.txt
<path to output directory> is the directory where the resuls should be saved ( it will be created  automatically)
If everything is fine the output should be similar to the following image and we should find the file part-00000 in the output directory.


