<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="Ausführen eines Hadoop MapReduce-Wortzählungsbeispiel in HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="Führen Sie ein MapReduce-Wortzählungsbeispiels auf einem Hadoop-Cluster in HDInsight aus. Das in Java geschriebene Programm zählt Worthäufigkeiten in einer Textdatei." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="The WordCount Hadoop sample in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />

#Ausführen eines MapReduce-Wortzählungsbeispiels auf einem Hadoop-Cluster in HDInsight
 
In diesem Lernprogramm erfahren Sie, wie Sie ein MapReduce-Wortzählungsbeispiel auf einem Hadoop-Cluster in HDInsight ausführen. Dieses in Java geschriebene Programm zählt Wortvorkommen in einer Textdatei und gibt eine neue Textdatei aus, die die einzelnen Wörter zusammen mit der Häufigkeit ihres Vorkommens enthält. In diesem Beispiel werden wir die Project Gutenberg-eBook-Ausgabe der Notizbücher von Leonardo Da Vinci als Textdatei analysieren.

 
**Sie erhalten Informationen zu folgenden Themen:**
		
* Verwenden der Azure PowerShell zur Ausführung eines MapReduce-Programms in einem Azure HDInsight-Cluster.
* Erstellen von MapReduce-Programmen in Java.


**Voraussetzungen**:	

- Sie benötigen ein Azure-Abonnement. Hinweise zur Erstellung eines Kontos finden Sie auf der Seite [Azure kostenlos ausprobieren](http://azure.microsoft.com/de-de/pricing/free-trial/) .

- Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] oder [Bereitstellen eines HDInsight-Clusters](../hdinsight-provision-clusters/)

- Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure]

##Themen in diesem Artikel	
In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:
	
1. [Ausführen des Beispiels mit Azure PowerShell](#run-sample)	
2. [Der Java-Code für das MapReduce-Programm zur Wortzählung](#java-code)
3. [Zusammenfassung](#summary)	
4. [Nächste Schritte](#next-steps)	

<h2><a id="run-sample"></a>Ausführen des Beispiels mit Azure PowerShell</h2> 

**So übermitteln Sie den MapReduce-Job**

1.	Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

3. Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
		
5. Führen Sie den folgenden Befehl aus, um eine MapReduce-Jobdefinition zu erstellen:

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [WACOM.NOTE] *hadoop-examples.jar* ist in HDInsight-Clustern der Version 2.1 enthalten. Für HDInsight-Cluster der Version 3.0 wurde die Datei in *hadoop-mapreduce.jar* umbenannt.
	
	Die Datei "hadoop-examples.jar" ist im HDInsight-Cluster enthalten. Es gibt zwei Argumente für den MapReduce-Job. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Die Quelldatei ist im HDInsight-Cluster enthalten, und der Pfad der Ausgabedatei wird während der Laufzeit erstellt.

6. Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu übermitteln:

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten.

8. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**So rufen Sie die Ergebnisse des MapReduce-Jobs ab**

1. Öffnen Sie **Azure PowerShell**.
2. Legen Sie die drei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Das Azure-Speicherkonto ist das Konto, das Sie zuvor im Lernprogramm erstellt haben. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird.  Der Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

3. Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Das *Select-AzureSubscription-Cmdlet* wird verwendet, um das aktuelle Abonnement festzulegen, falls Sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll. 

4. Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe aus dem Blob-Container auf die Arbeitsstation herunterzuladen:

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Der Ordner */example/data/WordCountOutput* ist der Ausgabeordner, den Sie bei der Ausführung des MapReduce-Jobs angegeben haben. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben.  Die Datei wird in dieselbe Ordnerstruktur in den lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C.  Die Datei wird in den Ordner *C:\example\data\WordCountOutput* heruntergeladen. 

5. Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	Der MapReduce-Job produziert eine Datei namens *part-r-00000,* die die Wörter und deren Anzahl enthält.  Das Skript verwendet den Befehl findstr, um alle Wörter aufzulisten, die *"there"*enthalten.

Die Ausgabe des WordCount-Skripts wird im Befehlsfenster angezeigt:

![Word frequency results in PowerShell from the Hadoop MapReduce word count example in HDInsight.][image-hdi-sample-wordcount-output]

Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

<h2><a id="java-code"></a>Der Java-Code für das MapReduce-Programm zur Wortzählung</h2>



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



<h2><a id="summary"></a>Zusammenfassung</h2>

Dieses Beispiel beschreibt die Ausführung eines MapReduce-Programms, das die Auftretenshäufigkeit von Wörtern in einem Text mit Azure HDInsight und Azure PowerShell zählt.

<h2><a id="next-steps"></a>Nächste Schritte</h2>

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Beispiel: 10-GB-Graysort][hdinsight-sample-10gb-graysort]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: C# Streaming][hdinsight-sample-cs-streaming]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-cs-streaming]: ../hdinsight-sample-csharp-streaming/


[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
 
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png



<!--HONumber=35.1-->
