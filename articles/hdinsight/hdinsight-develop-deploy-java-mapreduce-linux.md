<properties
	pageTitle="Entwickeln von Java MapReduce-Programmen für Linux-basiertes HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie MapReduce-Programme entwickeln und in Linux-basiertem HDInsight bereitstellen können."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-maven-mapreduce-selector.md)]

In diesem Dokument wird erläutert, wie Apache Maven zum Erstellen einer MapReduce-Anwendung und zum anschließenden Bereitstellen und Ausführen der Anwendung auf einem Linux-basierten Hadoop-Cluster in HDInsight verwendet wird. Informationen zur Verwendung eines Windows-basierten Hadoop auf HDInsight-Clusters finden Sie unter [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight (Windows)](hdinsight-develop-deploy-java-mapreduce.md)

##<a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 oder höher (oder eine entsprechende Anwendung wie OpenJDK)

- [Apache Maven](http://maven.apache.org/)

- **Ein Azure-Abonnement**

- **Azure-Befehlszeilenschnittstelle**: Weitere Informationen finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md)

##Konfigurieren von Umgebungsvariablen

Bei der Installation von Java und dem JDK können die folgenden Umgebungsvariablen festgelegt werden. Sie sollten dennoch prüfen, ob die Variablen vorhanden sind und korrekte Werte für Ihr System enthalten.

* **JAVA\_HOME** – sollte auf das Verzeichnis verweisen, in dem die Java-Laufzeitumgebung (Java Runtime Environment, JRE) installiert ist. Für ein OS X-, Unix- oder Linux-System sollte z. B. ein Wert wie `/usr/lib/jvm/java-7-oracle` verwendet werden. Unter Windows sollte der Wert so ähnlich sein wie `c:\Program Files (x86)\Java\jre1.7`

* **PATH** – sollte die folgenden Pfade enthalten:

	* **JAVA\_HOME** (oder den entsprechenden Pfad)

	* **JAVA\_HOME\\bin** (oder den entsprechenden Pfad)

	* Das Verzeichnis, in dem Maven installiert ist.

##Erstellen eines neuen Maven-Projekts

1. Ändern Sie über eine Terminalsitzung oder über die Befehlszeile in Ihrer Entwicklungsumgebung die Verzeichnisse in den Speicherort, an dem Sie dieses Projekt speichern möchten.

3. Verwenden Sie den Befehl __mvn__, der mit Maven installiert wird, um das Gerüst für das Projekt zu erstellen.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Daraufhin wird ein neues Unterverzeichnis mit dem im __artifactID__-Parameter (**wordcountjava** in diesem Beispiel) angegebenen Namen im aktuellen Verzeichnis erstellt. Dieses Verzeichnis enthält die folgenden Elemente:

	* __pom.xml__ – Das [Projektobjektmodell (POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)), das die Informationen und Konfigurationsdetails für das Erstellen des Projekts enthält.

	* __src__ – Das Verzeichnis, das das __main\\java\\org\\apache\\hadoop\\examples__-Verzeichnis enthält, in dem Sie die Anwendung erstellen werden.

3. Löschen Sie die Datei __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__, da diese in diesem Beispiel nicht verwendet wird.

##Hinzufügen von Abhängigkeiten

1. Bearbeiten Sie die Datei __pom.xml__, und fügen Sie Folgendes im Abschnitt `<dependencies>` ein.

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
	      <artifactId>hadoop-mapreduce-examples</artifactId>
	      <version>2.5.1</version>
		  <scope>provided</scope>
	    </dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-mapreduce-client-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>

	Damit teilen Sie Maven mit, dass das Projekt die Bibliotheken (aufgelistet in &lt;artifactId>) in bestimmten Versionen (aufgelistet in &lt;version>) benötigt. Beim Kompilieren wird er aus dem standardmäßigen Maven-Repository heruntergeladen. Sie können die [Maven-Repositorysuche](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) verwenden, um weitere Komponenten anzuzeigen.

	`<scope>provided</scope>` gibt für Maven an, dass diese Abhängigkeiten nicht mit der Anwendung zusammengepackt werden sollen, da sie durch den HDInsight-Cluster zur Laufzeit bereitgestellt werden.

2. Fügen Sie der Datei __pom.xml__ folgenden Code hinzu: Dieser muss sich in der Datei innerhalb der `<project>...</project>`-Tags befinden, z. B. zwischen `</dependencies>` und `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
			<plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
  		  </plugins>
	    </build>

	Das erste Plug-In konfiguriert das [Maven Shade-Plug-In](http://maven.apache.org/plugins/maven-shade-plugin/), das verwendet wird, um ein Uberjar (wird auch als Fatjar bezeichnet) zu erstellen, das die von der Anwendung benötigten Abhängigkeiten enthält. Zudem wird damit eine Duplizierung von Lizenzen innerhalb des JAR-Pakets verhindert, die bei einigen Systemen Probleme verursachen kann.

	Das zweite Plug-In konfiguriert den Maven-Compiler, der verwendet wird, um die Version von Java, die für diese Anwendung erforderlich ist, auf die im HDInsight-Cluster verwendete Version festzulegen.

3. Speichern Sie die Datei __pom.xml__.

##Erstellen der MapReduce-Anwendung

1. Wechseln Sie in das Verzeichnis __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ und benennen Sie die Datei __app.java__ in __WordCount.java__ um.

2. Öffnen Sie die Datei __WordCount.java__ in einem Text-Editor, und ersetzen Sie den Inhalt durch Folgendes:

		package org.apache.hadoop.examples;

		import java.io.IOException;
		import java.util.StringTokenizer;
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.fs.Path;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Job;
		import org.apache.hadoop.mapreduce.Mapper;
		import org.apache.hadoop.mapreduce.Reducer;
		import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
		import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
		import org.apache.hadoop.util.GenericOptionsParser;

		public class WordCount {

		  public static class TokenizerMapper
		       extends Mapper<Object, Text, Text, IntWritable>{

		    private final static IntWritable one = new IntWritable(1);
		    private Text word = new Text();

		    public void map(Object key, Text value, Context context
		                    ) throws IOException, InterruptedException {
		      StringTokenizer itr = new StringTokenizer(value.toString());
		      while (itr.hasMoreTokens()) {
		        word.set(itr.nextToken());
		        context.write(word, one);
		      }
		    }
		  }

		  public static class IntSumReducer
		       extends Reducer<Text,IntWritable,Text,IntWritable> {
		    private IntWritable result = new IntWritable();

		    public void reduce(Text key, Iterable<IntWritable> values,
		                       Context context
		                       ) throws IOException, InterruptedException {
		      int sum = 0;
		      for (IntWritable val : values) {
		        sum += val.get();
		      }
		      result.set(sum);
		      context.write(key, result);
		    }
		  }

		  public static void main(String[] args) throws Exception {
		    Configuration conf = new Configuration();
		    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
		    if (otherArgs.length != 2) {
		      System.err.println("Usage: wordcount <in> <out>");
		      System.exit(2);
		    }
		    Job job = new Job(conf, "word count");
		    job.setJarByClass(WordCount.class);
		    job.setMapperClass(TokenizerMapper.class);
		    job.setCombinerClass(IntSumReducer.class);
		    job.setReducerClass(IntSumReducer.class);
		    job.setOutputKeyClass(Text.class);
		    job.setOutputValueClass(IntWritable.class);
		    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
		    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
		    System.exit(job.waitForCompletion(true) ? 0 : 1);
		  }
		}

	Bitte beachten Sie, dass der Paketname **org.apache.hadoop.examples** und der Klassenname **WordCount** lautet. Diese Namen werden beim Übermitteln des MapReduce-Auftrags verwendet

3. Speichern Sie die Datei.

##Erstellen der Anwendung

1. Wechseln Sie zum Verzeichnis __wordcountjava__, sofern Sie es noch nicht aufgerufen haben.

2. Verwenden Sie den folgenden Befehl, um eine JAR-Datei mit der Anwendung zu erstellen:

		mvn clean package

	Damit werden etwaige frühere Buildartefakte entfernt, alle noch nicht installierten Abhängigkeiten heruntergeladen und die Anwendung erstellt und verpackt.

3. Nachdem der Befehl ausgeführt wurde, enthält das Verzeichnis __wordcountjava\\target__ eine Datei mit dem Namen __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] Die Datei __wordcountjava-1.0-SNAPSHOT.jar__ ist ein Uberjar, das nicht nur den WordCount-Auftrag enthält, sondern auch Abhängigkeiten, die für den Auftrag zur Laufzeit erforderlich sind.


##<a id="upload"></a>Hochladen der JAR-Datei

Verwenden Sie den folgenden Befehl, um die JAR-Datei in den HDInsight-Hauptknoten hochzuladen:

	scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Dadurch werden die Dateien aus dem lokalen System auf den Stammknoten kopiert.

> [AZURE.NOTE] Wenn Sie zum Schutz Ihres SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel verwendet haben, müssen Sie möglicherweise den `-i`-Parameter und den Pfad zum privaten Schlüssel angeben. Beispiel: `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.

##<a name="run"></a>Ausführen des MapReduce-Auftrags

1. Stellen Sie über SSH eine Verbindung mit HDInsight her, wie in den folgenden Artikeln beschrieben:

    - [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Verwenden Sie in der SSH-Sitzung den folgenden Befehl, um die MapReduce-Anwendung auszuführen:

		yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

	Dadurch wird die WordCount MapReduce-Anwendung verwendet, um die Wörter in der Datei "davinci.txt" zu zählen und die Ergebnisse in \_\___wasb:///example/data/wordcountout__ zu speichern. Die Eingabedatei und die Ausgabe werden im Standardspeicher für den Cluster gespeichert.

3. Wenn der Auftrag abgeschlossen ist, können Sie die Ergebnisse wie folgt anzeigen.

		hdfs dfs -cat wasb:///example/data/wordcountout/*

	Sie sollten eine Liste von Wörtern und Zahlen mit ähnlichen Werten wie den folgenden erhalten:

		zeal    1
		zelus   1
		zenith  2

##<a id="nextsteps"></a>Nächste Schritte

In diesem Dokument haben Sie gelernt, wie ein Java MapReduce-Auftrag entwickelt wird. In den folgenden Dokumenten finden Sie andere Möglichkeiten zum Arbeiten mit HDInsight.

- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

Weitere Informationen finden Sie außerdem im [Java Developer Center](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx

<!---HONumber=AcomDC_0323_2016-->