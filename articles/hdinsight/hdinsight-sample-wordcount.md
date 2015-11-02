<properties
	pageTitle="Hadoop MapReduce-Wortzählungsbeispiel in HDInsight | Microsoft Azure"
	description="Führen Sie ein MapReduce-Wortzählungsbeispiel auf einem Hadoop-Cluster in HDInsight aus. Das in Java geschriebene Programm zählt Worthäufigkeiten in einer Textdatei."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="jgao"/>

#Ausführen eines MapReduce-Wortzählungsprogramm auf einem Hadoop-Cluster in HDInsight

Erfahren Sie, wie Sie die Azure PowerShell zur Ausführung eines MapReduce-Programms in einem Hadoop-Cluster in HDInsight verwenden. Das Programm wurde in Java geschrieben. Es zählt die Wortvorkommen in einer Textdatei und gibt eine neue Textdatei aus, die die einzelnen Wörter zusammen mit der Häufigkeit ihres Vorkommens enthält.

Das Programm wird auf den Clustern installiert. In diesem Lernprogramm werden wir die Project Gutenberg-eBook-Ausgabe der Notizbücher von Leonardo Da Vinci als Textdatei analysieren.

> [AZURE.NOTE]Für die in diesem Dokument beschriebenen Schritte ist ein Windows-Client erforderlich. Schrittweise Anleitungen zur Verwendung des Wortzählungsbeispiels von einem Linux-, OS X- oder Unix-Client mit einem Linux-basierten HDInsight-Cluster finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight über SSH](hdinsight-hadoop-use-mapreduce-ssh.md) oder [Verwenden von MapReduce mit Hadoop in HDInsight mithilfe von Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Weitere verwandte Artikel:**

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Übermitteln von Hadoop-Aufträgen in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Beispiel: 10-GB-GraySort][hdinsight-sample-10gb-graysort]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: C#-Streaming][hdinsight-sample-cs-streaming]

**Voraussetzungen**:

- **Einen HDInsight-Cluster**. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] oder [Bereitstellen eines HDInsight-Clusters](hdinsight-provision-clusters.md).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Ausführen des Beispiels mit Azure PowerShell

**So übermitteln Sie den MapReduce-Auftrag**

1. Öffnen Sie **Windows PowerShell ISE**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].
2. Fügen Sie das folgende PowerShell-Skript ein:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. Legen Sie die ersten drei Variablen fest, und führen Sie das Skript aus.
		
**So rufen Sie die Ergebnisse des MapReduce-Auftrags ab**

1. Öffnen Sie **Windows PowerShell ISE**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].
2. Fügen Sie das folgende PowerShell-Skript ein:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	Der MapReduce-Auftrag erzeugt die Datei *part-r-00000*, welche die Wörter und deren Anzahl enthält. Das Skript verwendet den **findstr**-Befehl, um alle Wörter aufzulisten, die *"there"* enthalten.

Die Ausgabe des WordCount-Skripts wird im Befehlsfenster angezeigt:

![Ergebnisse der Wortzählung in PowerShell aus dem Hadoop-MapReduce-Wortzählungsbeispiel in HDInsight.][image-hdi-sample-wordcount-output]

Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

##Java-Quellcode

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

## Nächste Schritte

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Übermitteln von Hadoop-Aufträgen in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Beispiel: 10-GB-GraySort][hdinsight-sample-10gb-graysort]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: C#-Streaming][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->