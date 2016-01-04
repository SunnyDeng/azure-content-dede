<properties
	pageTitle="Ausführen der Hadoop-Beispiele in HDInsight | Microsoft Azure"
	description="Erste Schritte mit dem Azure HDInsight-Dienst mit den mitgelieferten Beispielen. Verwenden von PowerShell-Skripts, die MapReduce-Programme auf Datenclustern ausführen."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="jgao"/>

#Ausführen von Hadoop MapReduce-Beispielen in Windows-basiertem HDInsight

[AZURE.INCLUDE [Beispielauswahl](../../includes/hdinsight-run-samples-selector.md)]

Wir haben verschiedene Beispiele bereitgestellt, um Ihnen die ersten Schritte beim Ausführen von MapReduce-Aufträgen auf Hadoop-Clustern in Azure HDInsight zu erleichtern. Diese Beispiele sind in allen von HDInsight verwalteten Clustern verfügbar, die Sie erstellen. Führen Sie diese Beispiele aus, um sich mit der Verwendung der Azure PowerShell-Cmdlets für die Ausführung von Aufträgen in Hadoop-Clustern vertraut zu machen.

- [**Wortzählung**][hdinsight-sample-wordcount]\: Zählt die Vorkommen von Wörtern in einer Textdatei.
- [**Wortzählung – C#-Streaming**][hdinsight-sample-csharp-streaming]\: Zählt die Vorkommen von Wörtern in einer Textdatei mithilfe der Hadoop-Streamingschnittstelle.
- [**Pi-Schätzung**][hdinsight-sample-pi-estimator]\: Verwendet eine statistische (Quasi-Monte Carlo) Methode, um den Wert von Pi zu schätzen.
- [**10-GB-Graysort**][hdinsight-sample-10gb-graysort]\: Zeigt die Ausführung eines allgemeinen GraySort für eine 10-GB-Datei mithilfe von HDInsight. Drei Aufträge müssen ausgeführt werden: Teragen zum Generieren der Daten, Terasort zum Sortieren der Daten und Teravalidate zur Überprüfung der richtigen Sortierung der Daten.

>[AZURE.NOTE]Den Quellcode finden Sie im Anhang.

Im Internet finden Sie umfangreiche Dokumentationen zu Hadoop-verwandten Technologien, wie z. B. javabasierte MapReduce-Programmierung und Streaming, sowie Dokumentationen zu Cmdlets, die in Windwos PowerShell-Skripts verwendet werden. Weitere Informationen zu diesen Ressourcen finden Sie unter:

- [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Übermitteln von Hadoop-Aufträgen in HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Einführung in Azure HDInsight][hdinsight-introduction]

Mittlerweile entscheiden sich viele Benutzer lieber für Hive und Pig als für MapReduce. Weitere Informationen finden Sie unter:

- [Verwenden von Hive in HDInsight](hdinsight-use-hive.md)
- [Verwenden von Pig in HDInsight](hdinsight-use-pig.md)
 
**Voraussetzungen**:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Einen HDInsight-Cluster**. Anweisungen zu den verschiedenen Optionen beim Erstellen solcher Cluster finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren von Azure PowerShell 1.0 und höher](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater).

## Wortzählung – Java 

Um ein MapReduce-Projekt zu übermitteln, erstellen Sie zunächst eine MapReduce-Auftragsdefinition. In der Auftragsdefinition geben Sie die JAR-Datei des MapReduce-Programms und den Speicherort der JAR-Datei, d. h. „**wasb:///example/jars/hadoop-mapreduce-examples.jar**“, den Klassennamen und die Argumente an. Das MapReduce-Programm für die Wortzählung akzeptiert zwei Argumente: die Quelldatei, die zum Zählen von Wörtern verwendet wird, und den Speicherort der Ausgabe.

Den Quellcode finden Sie in [Anhang A](#apendix-a---the-word-count-MapReduce-program-in-java).

Anweisungen zum Entwickeln eines Java MapReduce-Programms finden Sie unter [Entwickeln von Java MapReduce-Programmen für Hadoop in HDInsight](hdinsight-develop-deploy-java-mapreduce.md).
 
**So übermitteln Sie einen MapReduce-Auftrag zur Wortzählung**

1. Öffnen Sie **Windows PowerShell ISE**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].
2. Fügen Sie das folgende PowerShell-Skript ein:

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$mrJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$mrJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $mrJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $mrJob.JobId 
		
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
			-JobId $mrJob.JobId `
			-DisplayOutputType StandardError

		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	Der MapReduce-Auftrag erzeugt die Datei *part-r-00000*, welche die Wörter und deren Anzahl enthält. Das Skript verwendet den **findstr**-Befehl, um alle Wörter aufzulisten, die *"there"* enthalten.

3. Legen Sie die ersten drei Variablen fest, und führen Sie das Skript aus.

## Wortzählung – C#-Streaming

Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können.

> [AZURE.NOTE]Die Schritte in diesem Lernprogramm gelten nur für Windows-basierte HDInsight-Cluster. Ein Beispiel für das Streaming bei Linux-basierten HDInsight-Clustern finden Sie unter [Entwickeln von Python-Streamingprogrammen für HDInsight](hdinsight-hadoop-streaming-python.md).

In diesem Beispiel sind Mapper und Reducer ausführbare Dateien, die die Eingabe von [stdin][stdin-stdout-stderr] (zeilenweise) lesen und die Ausgabe in [stdout][stdin-stdout-stderr] ausgeben. Das Programm zählt alle Wörter im Text.

Wenn eine ausführbare Datei für **Mapper** angegeben wird, ruft jede Mapper-Task die ausführbare Datei als separaten Prozess auf, wenn der Mapper initialisiert wird. Während der Ausführung der Mapperaufgabe werden die Eingaben in Zeilen konvertiert und die Zeilen zu [stdout][stdin-stdout-stderr] im Prozess zugeführt.

In der Zwischenzeit sammelt der Mapper die zeilenorientierte Ausgabe von der Ausgabe (stdout) des Prozesses. Jede Zeile wird in ein Schlüssel/Wert-Paar konvertiert, das als Ausgabe des Mappers erfasst wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert. Wenn die Zeile kein Tabulatorzeichen enthält, wird die gesamte Zeile als Schlüssel betrachtet, und der Wert ist NULL.

Wenn eine ausführbare Datei für **Reducer** angegeben wird, ruft jede Reducer-Task die ausführbare Datei als separaten Prozess auf, wenn der Reducer initialisiert wird. Während der Ausführung der Reduceraufgabe werden die eingegebenen Schlüssel/Wert-Paare in Zeilen konvertiert und die Zeilen zu [stdin][stdin-stdout-stderr] im Prozess zugeführt.

In der Zwischenzeit sammelt der Reducer die zeilenorientierte Ausgabe von [stdout][stdin-stdout-stderr] im Prozess. Jede Zeile wird in ein Schlüssel/Wert-Paar konvertiert, das als Ausgabe des Reducers erfasst wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert.

Weitere Informationen zur Hadoop-Streamingschnittstelle finden Sie unter [Hadoop-Streaming][hadoop-streaming].

**So übermitteln Sie einen C#-Streaming-Wortzählungsauftrag**

- Befolgen Sie das Verfahren unter [Wortzählung – Java](#word-count-java), und ersetzen Sie die Auftragsdefinition durch Folgendes:

		$mrJobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
									-File "/example/apps/" `
									-Mapper "cat.exe" `
									-Reducer "wc.exe" `
									-InputPath "/example/data/gutenberg/davinci.txt" `
									-OutputPath "/example/data/StreamingOutput/wc.txt"


	Die Ausgabedatei muss wie folgt lauten:
	
		example/data/StreamingOutput/wc.txt/part-00000		
								
## Pi-Schätzung

Die Pi-Schätzung verwendet eine statistische (Quasi-Monte Carlo) Methode, um den Wert von Pi zu schätzen. Zufällig platzierte Punkte in einem Einheitsquadrat liegen mit einer Wahrscheinlichkeit gleich der Kreisoberfläche Pi/4 innerhalb eines Kreises, der sich im Quadrat befindet. Der Wert für Pi kann aus dem Wert 4R ermittelt werden, wobei R das Verhältnis zwischen der Anzahl der Punkte innerhalb des Kreises zur Gesamtanzahl der Punkte innerhalb des Quadrats ist. Je größer die Anzahl der Punkte, desto genauer die Schätzung.

Das Skript in diesem Beispiel übermittelt einen Hadoop JAR-Auftrag und wird mit 16 Maps ausgeführt, die gemäß der Parameterwerte jeweils 10 Millionen Beispielpunkte berechnen. Sie können diese Parameterwerte ändern, um die Genauigkeit der Pi-Schätzung zu verbessern. Als Referenz: die ersten 10 Dezimalstellen von Pi lauten 3,1415926535.

**So übermitteln Sie einen Auftrag zur Pi-Schätzung**

- Befolgen Sie das Verfahren unter [Wortzählung – Java](#word-count-java), und ersetzen Sie die Auftragsdefinition durch Folgendes:

		$mrJobJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "pi" `
									-Arguments "16", "10000000"

## 10-GB-Graysort

Dieses Beispiel verwendet bescheidene 10 GB an Daten, um eine zügige Ausführung zu ermöglichen. Die Anwendung verwendet die MapReduce-Anwendungen von Owen O'Malley und Arun Murthy, die im Jahr 2009 den jährlichen allgemeinen ("daytona") Terabyte-Sortier-Benchmark mit einem Durchsatz von 0,578 TB/Min (100 TB in 173 Minuten) gewonnen haben. Weitere Informationen zu diesem und anderen Sortier-Benchmarks finden Sie unter [Sortbenchmark](http://sortbenchmark.org/).

Dieses Beispiel verwendet drei Sätze von MapReduce-Programmen:

1. **TeraGen** ist ein MapReduce, das Sie zum Generieren der zu sortierenden Datensätze verwenden können.
2. **TeraSort** prüft die Eingangsdaten und verwendet MapReduce, um die Daten in eine Gesamtreihenfolge zu bringen. TeraSort ist eine Standardsortierung mit MapReduce-Funktionen, mit Ausnahme eines eigenen Partitionierers, der eine sortierte Liste mit N-1 geprüften Schlüsseln verwendet, um den Schlüsselbereich für die einzelnen Reduce-Vorgänge zu definieren. Speziell werden alle Schlüssel mit Probe[i-1] <= Schlüssel < Probe[i] an Reduce-Vorgang i gesendet. Dadurch wird sichergestellt, dass die Ausgaben der Reduce-Vorgänge i immer kleiner sind als die Ausgabe des Reduce-Vorgangs i + 1.
3. **TeraValidate** ist ein MapReduce-Programm, das die globale Sortierung der Ausgabe prüft. TeraValidate erstellt eine Map pro Datei im Ausgabeverzeichnis, und jede Map stellt sicher, dass jeder Schlüssel kleiner als der vorherige oder gleich dem vorherigen Schlüssel ist. Die Map-Funktion generiert außerdem Einträge für den ersten und letzten Schlüssel der einzelnen Dateien, und die Reduce-Funktion stellt sicher, dass der erste Schlüssel von Datei i größer als der letzte Schlüssel von Datei i-1 ist. Probleme werden als Ausgabe der Reduce-Funktion zusammen mit den Schlüsseln gemeldet, die nicht in der richtigen Reihenfolge sind.

Mit den von den drei Anwendungen verwendeten Ein- und Ausgabeformaten werden die Textdateien jeweils im richtigen Format gelesen und geschrieben. Der replication-Wert der Reduce-Funktion ist auf 1 anstelle des Standardwerts 3 festgelegt, da die Ausgabedaten für den Benchmark-Wettbewerb nicht auf verschiedene Knoten repliziert werden müssen.

Für das Beispiel müssen drei Aufgaben ausgeführt werden, die den einzelnen MapReduce-Programmen entsprechen, die in der Einführung vorgestellt wurden:

1. Führen Sie den **TeraGen** MapReduce-Auftrag aus, um die zu sortierenden Daten zu generieren.
2. Sortieren der Daten mit dem **TeraSort** MapReduce-Auftrag.
3. Prüfen der korrekten Sortierung der Daten mit dem **TeraValidate** MapReduce-Auftrag.

**So übermitteln Sie die Aufträge**

- Befolgen Sie das Verfahren unter [Wortzählung – Java](#word-count-java), und verwenden Sie die folgenden Auftragsdefinitionen:

	$teragen = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "teragen" ` -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"
	
	$terasort = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "terasort" ` -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"
	
	$teravalidate = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "/example/jars/hadoop-mapreduce-examples.jar" ` -ClassName "teravalidate" ` -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"


##Nächste Schritte 

In diesem Artikel und den Artikeln zu den einzelnen Beispielen haben Sie gelernt, wie Sie die in HDInsight-Clustern enthaltenen Beispiele mithilfe von Azure PowerShell ausführen können. Lernprogramme zu Pig, Hive und MapReduce mit HDInsight finden Sie in den folgenden Themen:

* [Erste Schritte bei der Nutzung von Hadoop mit Hive in HDInsight zur Analyse der Verwendung von Mobiltelefonen][hdinsight-get-started]
* [Verwenden von Pig mit Hadoop in HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit Hadoop in HDInsight][hdinsight-use-hive]
* [Übermitteln von Hadoop-Aufträgen in HDInsight][hdinsight-submit-jobs]
* [Azure HDInsight SDK-Dokumentation][hdinsight-sdk-documentation]
* [Debuggen von Hadoop in HDInsight: Fehlermeldungen][hdinsight-errors]


## Anhang A – Quellcode für die Wortzählung

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


## Anhang B – Quellcode für die Streamingwortzählung

Das MapReduce-Programm verwendet die Anwendung "cat.exe" als Mapping-Schnittstelle zum Streamen des Texts in die Konsole und die Anwendung "wc.exe" als Reduce-Schnittstelle zum Zählen der Anzahl von Wörtern, die aus dem Dokument übertragen werden. Sowohl der Mapper als auch der Reducer lesen Zeichen zeilenweise aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout).

	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



Der Mapper-Code in der Datei "cat.cs" verwendet ein [StreamReader][streamreader]-Objekt, um die Zeichen des eingehenden Datenstroms in die Konsole einzulesen, die ihrerseits den Datenstrom mit der statischen [Console.Writeline][console-writeline]-Methode in den Standard-Ausgabedatenstrom schreibt.


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


Der Reducer-Code in der Datei "wc.cs" verwendet ein [StreamReader][streamreader]-Objekt zum Lesen von Zeichen aus dem Standard-Eingabedatenstrom, die vom cat.exe-Mapper ausgegeben wurden. Während des Einlesens der Zeichen mit der [Console.Writeline][console-writeline]-Methode werden die Wörter gezählt, indem die Leerzeichen und Zeilenendezeichen am Ende der einzelnen Wörter gezählt werden. Die Summe wird mit der [Console.Writeline][console-writeline]-Methode in den Standard-Ausgabedatenstrom geschrieben.





## Anhang C – Quellcode für die Pi-Schätzung

Sie finden den Javacode zur Pi-Schätzung mit den Mapper- und Reducer-Funktionen zu Ihrer Information weiter unten. Das Mapper-Programm generiert eine bestimmte Anzahl Punkte an zufälligen Orten innerhalb eines Einheits-Quadrats und zählt anschließend, wie viele dieser Punkte innerhalb des Kreises liegen. Das Reducer-Programm sammelt die von den Mappern gezählten Punkte und schätzt anschließend den Wert von Pi mit der Formel 4R, wobei R das Verhältnis zwischen der Anzahl der Punkte innerhalb des Kreises zur Gesamtanzahl der Punkte innerhalb des Quadrats ist.

 	/**
 	* Licensed to the Apache Software Foundation (ASF) under one
 	* or more contributor license agreements. See the NOTICE file
 	* distributed with this work for additional information
 	* regarding copyright ownership. The ASF licenses this file
 	* to you under the Apache License, Version 2.0 (the
 	* "License"); you may not use this file except in compliance
 	* with the License. You may obtain a copy of the License at
 	*
	* http://www.apache.org/licenses/LICENSE-2.0
 	*
 	* Unless required by applicable law or agreed to in writing, software
 	* distributed under the License is distributed on an "AS IS" BASIS,
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and
 	* limitations under the License.
 	*/

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi
	//using quasi-Monte Carlo method.
	//
	//Mapper:
	//Generate points in a unit square
	//and then count points inside/outside of the inscribed circle of the square.
	//
	//Reducer:
	//Accumulate points inside/outside results from the mappers.
	//Let numTotal = numInside + numOutside.
	//The fraction numInside/numTotal is a rational approximation of
	//the value (Area of the circle)/(Area of the square),
	//where the area of the inscribed circle is Pi/4
	//and the area of unit square is 1.
	//Then, Pi is estimated value to be 4(numInside/numTotal).
	//

 	public class PiEstimator extends Configured implements Tool {
	//tmp directory for input/output
 	static private final Path TMP_DIR = new Path(
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");

	//2-dimensional Halton sequence {H(i)},
	//where H(i) is a 2-dimensional point and i >= 1 is the index.
	//Halton sequence is used to generate sample points for Pi estimation.
 	private static class HaltonSequence {
	// Bases
 	static final int[] P = {2, 3};
	//Maximum number of digits allowed
 	static final int[] K = {63, 40};

 	private long index;
 	private double[] x;
 	private double[][] q;
 	private int[][] d;

	//Initialize to H(startindex),
	//so the sequence begins with H(startindex+1).
 	HaltonSequence(long startindex) {
 	index = startindex;
 	x = new double[K.length];
 	q = new double[K.length][];
 	d = new int[K.length][];
 	for(int i = 0; i < K.length; i++) {
 	q[i] = new double[K[i]];
 	d[i] = new int[K[i]];
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;

 	for(int j = 0; j < K[i]; j++) {
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];
 	d[i][j] = (int)(k % P[i]);
 	k = (k - d[i][j])/P[i];
 	x[i] += d[i][j] * q[i][j];
 	}
 	}
 	}

	//Compute next point.
	//Assume the current point is H(index).
	//Compute H(index+1).
	//@return a 2-dimensional point with coordinates in [0,1)^2
 	double[] nextPoint() {
 	index++;
 	for(int i = 0; i < K.length; i++) {
 	for(int j = 0; j < K[i]; j++) {
 	d[i][j]++;
 	x[i] += q[i][j];
 	if (d[i][j] < P[i]) {
 	break;
 	}
 	d[i][j] = 0;
 	x[i] -= (j == 0? 1.0: q[i][j-1]);
 	}
 	}
 	return x;
 	}
 	}

	//Mapper class for Pi estimation.
	//Generate points in a unit square and then
	//count points inside/outside of the inscribed circle of the square.
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.
	//@param offset samples starting from the (offset+1)th sample.
	//@param size the number of samples for this map
	//@param out output {ture->numInside, false->numOutside}
	//@param reporter
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());
 	long numInside = 0L;
 	long numOutside = 0L;

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.
	//Accumulate points inside/outside results from the mappers.
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.
 	@Override
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.
	// @param isInside Is the points inside?
	// @param values An iterator to a list of point counts
	// @param output dummy, not used here.
	// @param reporter

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}

 	//Reduce task done, write output to a file.
 	@Override
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class,
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}

	//Run a map/reduce job for estimating Pi.
	//@return the estimated value of Pi.
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	)
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());

 	jobConf.setInputFormat(SequenceFileInputFormat.class);

 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);

 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);

 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);

 	// turn off speculative execution, because DFS doesn't handle
 	// multiple writers to the same file.
 	jobConf.setSpeculativeExecution(false);

 	//setup input/output directories
 	final Path inDir = new Path(TMP_DIR, "in");
 	final Path outDir = new Path(TMP_DIR, "out");
 	FileInputFormat.setInputPaths(jobConf, inDir);
 	FileOutputFormat.setOutputPath(jobConf, outDir);

 	final FileSystem fs = FileSystem.get(jobConf);
 	if (fs.exists(TMP_DIR)) {
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");
	 }
	 if (!fs.mkdirs(inDir)) {
	 throw new IOException("Cannot create input directory " + inDir);
	 }

	 //generate an input file for each map task
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);
	 final LongWritable offset = new LongWritable(i * numPoints);
	 final LongWritable size = new LongWritable(numPoints);
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }

	 //start a map/reduce job
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");

	 //read outputs
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }

	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }

	//Parse arguments and then runs a map/reduce job.
	//Print output in standard out.
	//@return a non-zero if there is an error. Otherwise, return 0.
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);

	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);

	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }

	 //main method for running it as a stand alone command.
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
	 
## Anhang D – Quellcode für 10-GB-Graysort

Sie finden den Code für das TeraSort MapReduce-Programm in diesem Abschnitt zu Ihrer Information.


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
	 */

	package org.apache.hadoop.examples.terasort;

	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;

	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;

	/**
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }

	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];

	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }

	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }


	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p,
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }

	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }

	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }

	    public TotalOrderPartitioner() {
	    }

	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }

	  }

	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }

	  /**
	   * @param args
	   */

	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }
	}














 




[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline

<!---HONumber=AcomDC_1203_2015-->