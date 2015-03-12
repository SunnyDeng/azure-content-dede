<properties 
	pageTitle="Das C# Streaming-Wortzählungsbeispiel mit Hadoop in HDInsight | Azure" 
	description="Informationen zum Schreiben von MapReduce-Programmen in C#, die die Hadoop-Streaming-Schnittstelle verwenden, sowie zum Ausführen dieser Programme mit PowerShell-Cmdlets in HDInsight." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# Das C#-Streamingbeispiel zur MapReduce-Wortzählung in Hadoop in HDInsight
 
Hadoop stellt MapReduce eine Streaming-API zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Lernprogramm erstellen Sie ein MapReduce-Programm in C#, das die Hadoop-Streamingschnittstelle verwendet, und führen das Programm mithilfe von Azure PowerShell-Cmdlets in Azure HDInsight aus. 

In diesem Beispiel sind sowohl Mapper als auch Reducer ausführbare Dateien, die die Eingabe von [stdin][stdin-stdout-stderr] (zeilenweise) lesen und die Ausgabe über [stdout][stdin-stdout-stderr] ausgeben. Das Programm zählt alle Wörter im Text.

Wenn eine ausführbare Datei für **Mapper** angegeben wird, startet jede Mapperaufgabe bei der Initialisierung des Mappers die ausführbare Datei als separaten Prozess. Während der Ausführung der Mapperaufgabe werden die Eingaben in Zeilen konvertiert und die Zeilen dem [stdin][stdin-stdout-stderr] des Prozesses zugeführt. In der Zwischenzeit sammelt der Mapper die zeilenbasierten Ausgaben des stdout des Prozesses und konvertiert jede Zeile in ein Schlüssel/Wert-Paar, das als Ausgabe des Mappers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert. Wenn die Zeile kein Tabulatorzeichen enthält, wird die gesamte Zeile als Schlüssel betrachtet, und der Wert ist null. 

Wenn eine ausführbare Datei für **Reducer** angegeben wird, startet jede Reduceraufgabe bei der Initialisierung des Reducers die ausführbare Datei als separaten Prozess. Während der Ausführung der Reduceraufgabe werden die eingegebenen Schlüssel/Wert-Paare in Zeilen konvertiert und die Zeilen dem [stdin][stdin-stdout-stderr] des Prozesses zugeführt. In der Zwischenzeit sammelt der Reducer die zeilenbasierten Ausgaben des [stdout][stdin-stdout-stderr] des Prozesses und konvertiert jede Zeile in ein Schlüssel/Wert-Paar, das als Ausgabe des Reducers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert. 

Weitere Informationen zur Hadoop-Streamingschnittstelle finden Sie unter [Hadoop Streaming][hadoop-streaming]. 
 
**Sie erhalten Informationen zu folgenden Themen:**	
	
* Ausführen eines C#-Streamingprogramms zur Analyse von Daten in einer Datei in HDInsight mithilfe von Azure PowerShell.		
* Schreiben von C#-Code, der die Hadoop-Streaming-Schnittstelle verwendet


**Voraussetzungen**:	

- Sie benötigen ein Azure-Abonnement. Hinweise zum Erstellen eines Kontos finden Sie auf der Seite [Testen Sie Azure noch heute kostenlos](http://azure.microsoft.com/pricing/free-trial/).

- Sie benötigen einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen von HDInsight-Clustern](../hdinsight-provision-clusters/)

- Sie müssen die Azure PowerShell installiert und für die Verwendung mit Ihrem Konto konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].


## Themen in diesem Artikel
In diesem Thema wird die Ausführung des Beispiels demonstriert und der Java-Code für das MapReduce-Programm vorgestellt. Außerdem erhalten Sie eine Zusammenfassung der vermittelten Lerninhalte und einen Ausblick auf nächste Schritte. Der Artikel enthält die folgenden Abschnitte:
	
1. [Ausführen des Beispiels mit Azure PowerShell](#run-sample)	
2. [Der C#-Code für Hadoop-Streaming](#java-code)
3. [Zusammenfassung](#summary)	
4. [Nächste Schritte](#next-steps)	

<h2><a id="run-sample"></a>Ausführen des Beispiels mit Azure PowerShell</h2>

**So führen Sie den MapReduce-Auftrag aus**

1.	Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

2. Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


3. Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu definieren.
 
		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

	Die Parameter geben die Mapper- und Reducer-Funktionen sowie die Eingabedatei und die Ausgabedateien an.
                 
4. Führen Sie die folgenden Befehle aus, um den MapReduce-Job auszuführen, auf den Abschluss des Jobs zu warten und dann den Standardfehler zu drucken:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5. Führen Sie die folgenden Befehle aus, um die Ergebnisse der Wortzählung anzuzeigen.

		$subscriptionName = "<SubscriptionName>"   
		$storageAccountName = "<StorageAccountName>" 
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
              
		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary }
      $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
 
		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.
	
<h2><a id="java-code"></a>Der C#-Code für Hadoop-Streaming</h2>

Das MapReduce-Programm verwendet die Anwendung "cat.exe" als Mapping-Schnittstelle zum Streamen des Texts in die Konsole und die Anwendung "wc.exe" als Reduce-Schnittstelle zum Zählen der Anzahl von Wörtern, die aus dem Dokument gestreamt werden. Sowohl der Mapper als auch der Reducer lesen Zeichen (zeilenweise) aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout). 



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

 

Der Mapper-Code in der Datei "cat.cs" verwendet ein StreamReader-Objekt, um die Zeichen des eingehenden Datenstroms in die Konsole zu lesen, die ihrerseits den Datenstrom mit der statischen Console.Writeline-Methode in den Standard-Ausgabedatenstrom schreibt.


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


Der Reducer-Code in der Datei "wc.cs" verwendet ein [StreamReader][streamreader]-   Objekt, um Zeichen aus dem Standardeingabedatenstrom zu lesen, die vom Mapper "cat.exe" ausgegeben wurden. Beim Lesen der Zeichen mit der [Console.Writeline][console-writeline]-Methode zählt der Code die Wörter, indem Leerzeichen und Zeilenendzeichen am Ende jedes Worts gezählt werden; anschließend wird die Summe mit der [Console.Writeline][console-writeline]-Methode in den Standardausgabedatenstrom geschrieben. 

<h2><a id="summary"></a>Zusammenfassung</h2>

In diesem Lernprogramm haben Sie erfahren, wie ein MapReduce-Job mithilfe von Hadoop-Streaming in HDInsight bereitgestellt wird.

<h2><a id="next-steps"></a>Nächste Schritte</h2>

Lernprogramme mit anderen Beispielen und Anweisungen zur Verwendung von Pig-, Hive- und MapReduce-Jobs in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Themen:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: Wortzählung][hdinsight-sample-wordcount]
* [Beispiel: 10-GB-GraySort][hdinsight-sample-10gb-graysort]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=42-->
