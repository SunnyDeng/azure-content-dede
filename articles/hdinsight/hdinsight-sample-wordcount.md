<properties
	pageTitle="Hadoop MapReduce-Wortzählungsbeispiel in HDInsight | Microsoft Azure"
	description="Führen Sie ein MapReduce-Wortzählungsbeispiel auf einem Hadoop-Cluster in HDInsight aus. Das in Java geschriebene Programm zählt Worthäufigkeiten in einer Textdatei."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015" 
	ms.author="bradsev"/>

#Ausführen eines in Java geschriebenen MapReduce-Wortzählungsbeispiels auf einem Hadoop-Cluster in HDInsight

In diesem Lernprogramm erfahren Sie, wie Sie ein MapReduce-Wortzählungsbeispiel auf einem Hadoop-Cluster in HDInsight ausführen. Dieses Programm ist in Java geschrieben. Es zählt die Wortvorkommen in einer Textdatei und gibt eine neue Textdatei aus, die die einzelnen Wörter zusammen mit der Häufigkeit ihres Vorkommens enthält. In diesem Beispiel werden wir die Project Gutenberg-eBook-Ausgabe der Notizbücher von Leonardo Da Vinci als Textdatei analysieren.

> [AZURE.NOTE]Für die in diesem Dokument beschriebenen Schritte ist ein Windows-Client erforderlich. Schrittweise Anleitungen zur Verwendung des Wortzählungsbeispiels von einem Linux-, OS X- oder Unix-Client mit einem Linux-basierten HDInsight-Cluster finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight über SSH](hdinsight-hadoop-use-mapreduce-ssh.md) oder [Verwenden von MapReduce mit Hadoop in HDInsight mithilfe von Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Sie lernen Folgendes**:

* Verwenden der Azure PowerShell zur Ausführung eines MapReduce-Programms in einem Azure HDInsight-Cluster.
* Erstellen von MapReduce-Programmen in Java.


**Voraussetzungen**:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Einen HDInsight-Cluster**. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] oder [Bereitstellen eines HDInsight-Clusters](hdinsight-provision-clusters.md).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Ausführen des Beispiels mit Azure PowerShell</h2>

**So übermitteln Sie den MapReduce-Auftrag**

1.	Öffnen Sie die **Azure PowerShell**-Konsole. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

3. Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. Führen Sie den folgenden Befehl aus, um eine MapReduce-Jobdefinition zu erstellen:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]*hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Die Datei wurde in HDInsight-Clustern der Version 3.0. in *hadoop-mapreduce.jar* umbenannt.

	Die Datei "hadoop-mapreduce-examples.jar" ist im HDInsight-Cluster enthalten. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Die Quelldatei ist im HDInsight-Cluster enthalten, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

6. Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu übermitteln:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Zusätzlich zur MapReduce-Auftragsdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Auftrag ausführen möchten.

8. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Auftrags aufgetreten sind:

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**So rufen Sie die Ergebnisse des MapReduce-Auftrags ab**

1. Öffnen Sie die **Azure PowerShell**-Konsole.
2. Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Über das Speicherkonto wird der Blob gehostet, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Azure Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

3. Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** wird verwendet, um das aktuelle Abonnement festzulegen, falls Sie mehrere Abonnements haben und nicht das Standardabonnement verwenden möchten.

4. Führen Sie den folgenden Befehl aus, um die Ausgabe des MapReduce-Auftrags aus dem Blob auf die Arbeitsstation herunterzuladen:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Der Ordner *example/data/WordCountOutput* ist der Ausgabeordner, den Sie bei der Ausführung des MapReduce-Jobs angegeben haben. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur in den lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner *C:\example\\data\\WordCountOutput* heruntergeladen.

5. Führen Sie den folgenden Befehl aus, um die MapReduce-Auftragsausgabedatei auszudrucken:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	Der MapReduce-Auftrag erzeugt die Datei *part-r-00000*, welche die Wörter und deren Anzahl enthält. Das Skript verwendet den **findstr**-Befehl, um alle Wörter aufzulisten, die *"there"* enthalten.

Die Ausgabe des WordCount-Skripts wird im Befehlsfenster angezeigt:

![Ergebnisse der Wortzählung in PowerShell aus dem Hadoop-Wortzählungsbeispiel in HDInsight.][image-hdi-sample-wordcount-output]

Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

## <a id="java-code"></a>Der Java-Code für das MapReduce-Programm zur Wortzählung</h2>



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



In diesem Lernprogramm wird die Ausführung eines MapReduce-Programms beschrieben, das die Anzahl der Vorkommen der einzelnen Wörter in einem Text mit Azure HDInsight und Azure PowerShell zählt.

## <a id="next-steps"></a>Nächste Schritte</h2>

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Aufträgen in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Beispiel: 10-GB-GraySort][hdinsight-sample-10gb-graysort]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: C#-Streaming][hdinsight-sample-cs-streaming]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Azure HDInsight SDK-Dokumentation][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
 

<!---HONumber=62-->