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
	ms.date="03/30/2014" 
	ms.author="bradsev"/>

# Das C#-Streamingbeispiel zur MapReduce-Wortzählung in Hadoop in HDInsight

Hadoop stellt MapReduce eine Streaming-API zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Lernprogramm erstellen Sie ein MapReduce-Programm in C# mithilfe der Hadoop-Streamingschnittstelle und führen das Programm mit Azure PowerShell-Cmdlets in Azure HDInsight aus.

> [AZURE.NOTE]Die Schritte in diesem Lernprogramm gelten nur für Windows-basierte HDInsight-Cluster. Ein Beispiel für das Streaming bei Linux-basierten HDInsight-Clustern finden Sie unter [Entwickeln von Python-Streamingprogrammen für HDInsight](hdinsight-hadoop-streaming-python.md).

Im Beispiel sind der Mapper und der Reducer ausführbare Dateien, die eine Eingabe aus [stdin][stdin-stdout-stderr] lesen (zeilenweise) und die Ausgabe in [stdout][stdin-stdout-stderr] schreiben. Das Programm zählt alle Wörter im Text.

Wenn eine ausführbare Datei für **Mapper** angegeben wird, ruft jede Mapper-Task die ausführbare Datei als separaten Prozess auf, wenn der Mapper initialisiert wird. Die Mapper-Task konvertiert ihre Eingabe in Zeilen und übermittelt die Zeilen an den [stdin][stdin-stdout-stderr] des Prozesses.

In der Zwischenzeit sammelt der Mapper die zeilenbasierte Ausgabe aus dem "stdout" des Prozesses. Er konvertiert jede Zeile in ein Schlüssel-Wert-Paar, das als Ausgabe des Mappers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (ohne das Tabulatorzeichen) ist der Wert. Wenn die Zeile kein Tabulatorzeichen enthält, wird die gesamte Zeile als Schlüssel betrachtet und der Wert ist null.

Wenn eine ausführbare Datei für **Reducer** angegeben wird, ruft jede Reducer-Task die ausführbare Datei als separaten Prozess auf, wenn der Reducer initialisiert wird. Die Reducer-Task konvertiert die Schlüssel-Wert-Eingabepaare in Zeilen und übermittelt die Zeilen an den [stdin][stdin-stdout-stderr] des Prozesses.

In der Zwischenzeit sammelt der Reducer die zeilenbasierte Ausgabe aus dem [stdout][stdin-stdout-stderr] des Prozesses. Er konvertiert jede Zeile in ein Schlüssel-Wert-Paar, das als Ausgabe des Reducers gesammelt wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (ohne das Tabulatorzeichen) ist der Wert.

Weitere Informationen zur Hadoop-Streaming-Schnittstelle finden Sie unter [HadoopStreaming][hadoop-streaming] (in englischer Sprache).

**In diesem Lernprogramm lernen Sie Folgendes:**

* Verwenden von Azure PowerShell zum Ausführen eines C#-Streamingprogramms zum Analysieren von Daten in einer Datei in HDInsight
* Schreiben von C#-Code, der die Hadoop-Streamingschnittstelle verwendet


**Voraussetzungen**:

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Konto. Hinweise zum Erstellen eines Kontos finden Sie auf der Seite [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/).

- Einen bereitgestellten HDInsight-Cluster. Anweisungen zu den verschiedenen Möglichkeiten zur Erstellung solcher Cluster finden Sie unter [Bereitstellen eines HDInsight-Clusters](hdinsight-provision-clusters.md).

- Azure PowerShell. Diese muss für die Verwendung mit Ihrem Konto konfiguriert werden. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].


## <a id="run-sample"></a>Ausführen des Beispiels mit Azure PowerShell

**So führen Sie den MapReduce-Auftrag aus**

1.	Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Azure PowerShell-Konsolenfensters finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

3. Legen Sie die zwei Variablen in den folgenden Befehlen fest, und führen Sie die Befehle aus:

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Führen Sie den folgenden Befehl aus, um den MapReduce-Job zu definieren:

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	Die Parameter geben die Mapper- und Reducer-Funktionen sowie die Eingabedatei und die Ausgabedateien an.

5. Führen Sie die folgenden Befehle aus, um den MapReduce-Auftrag auszuführen, auf den Abschluss des Auftrags zu warten und dann die Standardfehlermeldung zu drucken:

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. Führen Sie die folgenden Befehle aus, um die Ergebnisse der Wortzählung anzuzeigen.

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

##<a id="java-code"></a>Der C#-Code für Hadoop-Streaming

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



Der Mapper-Code in der Datei "cat.cs" verwendet ein [StreamReader][streamreader]-Objekt, um die Zeichen des eingehenden Datenstroms in die Konsole zu lesen, die dann den Datenstrom mit der statischen [Console.Writeline][console-writeline]-Methode in den Standard-Ausgabedatenstrom schreibt.


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


Der Reducer-Code in der Datei "wc.cs" verwendet ein [StreamReader][streamreader]-Objekt zum Lesen von Zeichen aus dem Standard-Eingabedatenstrom, die vom cat.exe-Mapper ausgegeben wurden. Beim Lesen der Zeichen mit der [Console.Writeline][console-writeline]-Methode zählt er die Wörter durch Zählen der Leerzeichen und Zeilenende-Zeichen am Ende jedes Worts. Anschließend schreibt er die Summe mit der [Console.Writeline][console-writeline]-Methode in den Standardausgabestream.

##<a id="summary"></a>Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie ein MapReduce-Auftrag mithilfe von Hadoop-Streaming in HDInsight bereitgestellt wird.

##<a id="next-steps"></a>Nächste Schritte

Lernprogramme, in denen andere Beispiele ausgeführt und Anweisungen zum Ausführen von Pig-, Hive- und MapReduce-Aufträgen in Azure HDInsight mit Azure PowerShell bereitgestellt werden, finden Sie in den folgenden Themen:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: Wortzählung][hdinsight-sample-wordcount]
* [Beispiel: 10-GB-Graysort][hdinsight-sample-10gb-graysort]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Azure HDInsight SDK-Dokumentation][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!--HONumber=54--> 