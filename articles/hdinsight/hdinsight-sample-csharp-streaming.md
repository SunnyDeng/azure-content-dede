<properties
	pageTitle="C#-Streamingbeispiel zur Wortzählung in Hadoop | Microsoft Azure"
	description="Informationen zum Schreiben von MapReduce-Programmen in C#, die die Hadoop-Streaming-Schnittstelle verwenden, sowie zum Ausführen dieser Programme mit PowerShell-Cmdlets in HDInsight."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# Das C#-Streamingbeispiel zur MapReduce-Wortzählung in Hadoop in HDInsight

Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Lernprogramm erfahren Sie, wie Sie mithilfe der Hadoop-Streaming-Schnittstelle MapReduce-Programme in C# schreiben und die Programme mithilfe von Azure PowerShell-Cmdlets in Azure HDInsight ausführen.

> [AZURE.NOTE]Die Schritte in diesem Lernprogramm gelten nur für Windows-basierte HDInsight-Cluster. Ein Beispiel für das Streaming bei Linux-basierten HDInsight-Clustern finden Sie unter [Entwickeln von Python-Streamingprogrammen für HDInsight](hdinsight-hadoop-streaming-python.md).

In diesem Beispiel sind sowohl Mapper als auch Reducer ausführbare Dateien, die die Eingabe (zeilenweise) von [stdin][stdin-stdout-stderr] lesen und die Ausgabe über [stdout][stdin-stdout-stderr] ausgeben. Das Programm zählt alle Wörter im Text.

Wenn eine ausführbare Datei für **Mapper** angegeben wird, ruft jede Mapper-Task die ausführbare Datei als separaten Prozess auf, wenn der Mapper initialisiert wird. Während der Ausführung der Mapper-Task wird die Eingabe in Zeilen konvertiert, und diese Zeilen werden an die Eingabe ([stdin][stdin-stdout-stderr]) des Prozesses übermittelt.

In der Zwischenzeit sammelt der Mapper die zeilenorientierte Ausgabe von der Ausgabe (stdout) des Prozesses. Jede Zeile wird in ein Schlüssel/Wert-Paar konvertiert, das als Ausgabe des Mappers erfasst wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert. Wenn die Zeile kein Tabulatorzeichen enthält, wird die gesamte Zeile als Schlüssel betrachtet, und der Wert ist NULL.

Wenn eine ausführbare Datei für **Reducer** angegeben wird, ruft jede Reducer-Task die ausführbare Datei als separaten Prozess auf, wenn der Reducer initialisiert wird. Während der Ausführung der Reducer-Task werden die eingegebenen Schlüssel/Wert-Paare in Zeilen konvertiert, und diese Zeilen werden an die Eingabe ([stdin][stdin-stdout-stderr]) des Prozesses übermittelt.

In der Zwischenzeit sammelt der Reducer die zeilenorientierte Ausgabe von der Ausgabe ([stdout][stdin-stdout-stderr]) des Prozesses. Jede Zeile wird in ein Schlüssel/Wert-Paar konvertiert, das als Ausgabe des Reducers erfasst wird. Standardmäßig ist das Präfix einer Zeile bis zum ersten Tabulatorzeichen der Schlüssel, und der Rest der Zeile (mit Ausnahme des Tabulatorzeichens) ist der Wert.

Weitere Informationen zur Hadoop-Streaming-Schnittstelle finden Sie unter [Hadoop Streaming][hadoop-streaming].

**In diesem Lernprogramm lernen Sie Folgendes:**

* Ausführen eines C#-Streamingprogramms zur Analyse von Daten in einer Datei in HDInsight mithilfe von Azure PowerShell.
* Schreiben von C#-Code, der die Hadoop-Streaming-Schnittstelle verwendet.


**Voraussetzungen**:

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Einen HDInsight-Cluster**. Anweisungen zu den verschiedenen Optionen beim Erstellen eines solchen Clusters finden Sie unter [Bereitstellung eines HDInsight-Clusters](hdinsight-provision-clusters.md).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



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

6. Führen Sie die folgenden Befehle aus, um die Ergebnisse der Wortzählung anzuzeigen:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Beachten Sie, dass die Ausgabedateien eines MapReduce-Jobs nicht geändert werden können. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.


## <a id="java-code"></a>Der C#-Code für Hadoop-Streaming


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


## <a id="summary"></a>Zusammenfassung

In diesem Lernprogramm haben Sie erfahren, wie ein MapReduce-Auftrag mithilfe von Hadoop-Streaming in HDInsight bereitgestellt wird.

## <a id="next-steps"></a>Nächste Schritte


Lernprogramme mit anderen Beispielen und Anweisungen zur Ausführung von Pig-, Hive- und MapReduce-Aufträgen in Azure HDInsight mit Azure PowerShell finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Beispiel: Pi-Schätzung][hdinsight-sample-pi-estimator]
* [Beispiel: Wortzählung][hdinsight-sample-wordcount]
* [Beispiel: 10-GB-GraySort][hdinsight-sample-10gb-graysort]
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
 

<!---HONumber=August15_HO6-->