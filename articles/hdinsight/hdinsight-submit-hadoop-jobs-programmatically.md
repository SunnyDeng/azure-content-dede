<properties
	pageTitle="Übermitteln von Hadoop-Aufträgen in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Hadoop-Aufträge an Azure HDInsight Hadoop übermitteln können."
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
	ms.date="10/02/2015"
	ms.author="jgao"/>

# Übermitteln von Hadoop-Aufträgen in HDInsight

Erfahren Sie, wie Sie mithilfe von Azure PowerShell MapReduce- und Hive-Aufträge übermitteln und das HDInsight .NET SDK verwenden, um MapReduce-, Hadoop-Streaming- und Hive-Aufträge zu übermitteln.

> [AZURE.NOTE]Die Schritte in diesem Artikel müssen auf einem Windows-Client ausgeführt werden. Informationen zur Verwendung eines Linux-, OS X- oder Unix-Clients für die Arbeit mit MapReduce, Hive oder Pig in HDInsight finden Sie in den folgenden Artikeln. Wählen Sie innerhalb der Artikel entweder die Links für **SSH** oder **Curl** aus:
>
> - [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
> - [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
> - [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Ein Azure HDInsight-Cluster**. Anweisungen hierzu finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] und unter [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision].
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/) (Installieren und Verwenden von Azure PowerShell, in englischer Sprache).



##Übermitteln von MapReduce-Aufträgen mithilfe von Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell].

Hadoop MapReduce ist ein Softwareframework zum Schreiben von Anwendungen, die riesige Datenmengen verarbeiten. HDInsight-Cluster sind in einer JAR-Datei enthalten, die unter *\\example\\jars\\hadoop-mapreduce-examples.jar* gespeichert ist und mehrere MapReduce-Beispiele enthält.

Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien. In dieser Sitzung erfahren Sie, wie Sie mithilfe von Azure PowerShell auf einer Arbeitsstation das Wortzählungsbeispiel ausführen. Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Aufträgen finden Sie unter [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce].

**So führen Sie das MapReduce-Programm zum Zählen von Wörtern mit Azure PowerShell aus**

1.	Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Azure PowerShell-Konsolenfensters finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

3. Legen Sie die folgenden Variablen fest, indem Sie diese Azure PowerShell-Befehle ausführen:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"

	Beim Abonnementnamen handelt es sich um den Namen, der zum Erstellen des HDInsight-Clusters verwendet wurde. Der HDInsight-Cluster ist der Cluster, den Sie zum Ausführen des MapReduce-Auftrags verwenden möchten.

5. Führen Sie die folgenden Befehle aus, um eine MapReduce-Auftragsdefinition zu erstellen:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das Präfix "wasb://" finden Sie unter [Verwenden von Azure-BLOB-Speicher mit HDInsight][hdinsight-storage].

6. Führen Sie den folgenden Befehl aus, um den MapReduce-Auftrag auszuführen:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

	Zusätzlich zur MapReduce-Auftragsdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Auftrag ausführen möchten.

7. Führen Sie den folgenden Befehl aus, um den Abschluss des MapReduce-Auftrags zu überprüfen:

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600


8. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Auftrags aufgetreten sind:

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

	Der folgende Screenshot zeigt die Ausgabe bei erfolgreicher Ausführung. Andernfalls werden Fehlermeldungen angezeigt.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]


**So rufen Sie die Ergebnisse des MapReduce-Auftrags ab**

1. Öffnen Sie **Azure PowerShell**.
2. Legen Sie die folgenden Variablen fest, indem Sie diese Azure PowerShell-Befehle ausführen:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

	Der Speicherkontoname gehört zu dem Azure-Speicherkonto, das beim Erstellen des HDInsight-Clusters angegeben wurde. Über das Speicherkonto wird der Blobcontainer gehostet, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Container trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben beim Erstellen des Clusters einen anderen Namen angegeben.

3. Führen Sie die folgenden Befehle aus, um ein Kontextobjekt für den Azure-BLOB-Speicher zu erstellen:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** wird verwendet, um das aktuelle Abonnement festzulegen, falls Sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

4. Führen Sie den folgenden Befehl aus, um die Ausgabe des MapReduce-Auftrags aus dem Blobcontainer auf die Arbeitsstation herunterzuladen:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Der Ordner *example/data/WordCountOutput* ist der angegebene Ausgabeordner für die Ausführung des MapReduce-Aufrags. *part-r-00000* ist der Standarddateiname für MapReduce-Auftragsausgaben. Die Datei wird in dieselbe Ordnerstruktur im lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner "C:". Die Datei wird in folgenden Ordner heruntergeladen:

**C:\\example\\data\\WordCountOutput*

5. Führen Sie den folgenden Befehl aus, um die MapReduce-Auftragsausgabedatei auszudrucken:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	Der MapReduce-Auftrag erzeugt die Datei *part-r-00000*, die die Wörter und deren Anzahl enthält. Das Skript verwendet den **findstr**-Befehl, um alle Wörter aufzulisten, die "there" enthalten.


> [AZURE.NOTE]Wenn Sie "./example/data/WordCountOutput/part-r-00000" (eine mehrzeilige Ausgabe eines MapReduce-Auftrags) im Editor öffnen, werden Sie feststellen, dass die Zeilenumbrüche nicht korrekt dargestellt werden. Dies entspricht dem erwarteten Verhalten.









































































































































##Übermitteln von Hive-Aufträgen mithilfe von Azure PowerShell
[Apache Hive][apache-hive] ermöglicht das Ausführen von MapReduce-Aufträgen über eine SQL-ähnliche Skriptsprache namens *HiveQL*, mit der große Datenmengen zusammengefasst, abgefragt und analysiert werden können.

HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. In dieser Sitzung verwenden Sie Azure PowerShell, um einen Hive-Auftrag zum Auflisten einiger Daten aus der Hive-Tabelle auszuführen.

**So übermitteln Sie einen Hive-Auftrag mithilfe von Azure PowerShell**

1.	Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Azure PowerShell-Konsolenfensters finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

2. Legen Sie die ersten beiden Variablen in den folgenden Befehlen fest, und führen Sie danach die Befehle aus:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring ist die HiveQL-Abfrage.

3. Führen Sie die folgenden Befehle aus, um das Azure-Abonnement und den Cluster zum Ausführen des Hive-Auftrags auszuwählen:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Führen Sie die folgenden Befehle aus, um den Hive-Auftrag zu übermitteln:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	Mit dem Schalter **-File** können Sie eine HiveQL-Skriptdatei im Hadoop Distributed File System (HDFS) angeben.

Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].


## Übermitteln von Hive-Aufträgen mithilfe von Visual Studio

Weitere Informationen finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio][hdinsight-visual-studio-tools].

##Übermitteln von Sqoop-Aufträgen mithilfe von Azure PowerShell

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][hdinsight-use-sqoop].

##Übermitteln von MapReduce-Aufträgen mit dem HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. HDInsight-Cluster sind in einer JAR-Datei enthalten, die unter *\\example\\jars\\hadoop-mapreduce-examples.jar* gespeichert ist und mehrere MapReduce-Beispiele enthält. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien. In dieser Sitzung lernen Sie, wie Sie eine .NET-Anwendung erstellen, um das Wortzählungsbeispiel auszuführen. Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Aufträgen finden Sie unter [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce].

**So übermitteln Sie den MapReduce-Auftrag für die Wortzählung**

1. Erstellen Sie eine C#-Konsolenanwendung in Visual Studio.
2. Führen Sie in der NuGet-Paket-Manager-Konsole den folgenden Befehl aus.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Verwenden Sie die folgenden using-Anweisungen in der Datei "Program.cs":

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Fügen Sie der Main()-Funktion den folgenden Code hinzu.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. Fügen Sie der Klasse die folgende Hilfsfunktion hinzu.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Drücken Sie **F5**, um die Anwendung auszuführen.










##Übermitteln von Hadoop Streaming-Aufträgen mit dem HDInsight .NET SDK
HDInsight-Cluster werden mit einem Hadoop-Streamingprogramm zur Wortzählung erstellt, das in C# entwickelt wurde. Das Zuordnungsprogramm ist */example/apps/cat.exe*, und das Reduce-Programm ist */example/apps/wc.exe*. In dieser Sitzung erfahren Sie, wie Sie eine .NET-Anwendung zum Ausführen des Wortzählungsbeispiels erstellen können.

Details zum Erstellen einer .NET-Anwendung für die Übermittlung von MapReduce-Aufträgen finden Sie unter [Übermitteln von MapReduce-Aufträgen mit dem HDInsight .NET SDK](#mapreduce-sdk).

Weitere Informationen zum Entwickeln und Bereitstellen von Hadoop-Streamingaufträgen finden Sie unter [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming-jobs].

Das folgende Verfahren funktioniert nur auf HDInsight-Clustern unter Windows. C#-Streaming wird auf Linux-Clustern noch nicht unterstützt. Sie können jedoch .NET-Programme schreiben, um Streamingaufträge in anderen Programmiersprachen zu übermitteln, die von Linux-Clustern unterstützt werden. Dazu gehört beispielsweise Python. Ein Beispiel für Streaming in Python finden Sie unter [Entwickeln von Python-Streamingprogrammen für HDInsight](hdinsight-hadoop-streaming-python.md).

**So übermitteln Sie den MapReduce-Auftrag für die Wortzählung**

1. Führen Sie in der Paket-Manager-Konsole von Visual Studio den folgenden NuGet-Befehl aus, um das Paket zu importieren.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Verwenden Sie die folgenden using-Anweisungen in der Datei "Program.cs":

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Fügen Sie der Main()-Funktion den folgenden Code hinzu.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. Fügen Sie der Klasse eine Hilfsfunktion hinzu.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Drücken Sie **F5**, um die Anwendung auszuführen.

##Übermitteln von Hive-Aufträgen mit dem HDInsight .NET SDK
HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. In dieser Sitzung erstellen Sie eine .NET-Anwendung zum Ausführen eines Hive-Auftrags, mit dem die in einem HDInsight-Cluster erstellten Hive-Tabellen aufgelistet werden. Weitere Informationen zur Verwendung von Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit dem SDK zu erstellen:

- Installieren des HDInsight .NET SDK
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK** Sie können den aktuellen veröffentlichten Build des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**Erstellen einer Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013 oder 2015.

2. Erstellen Sie ein neues Projekt mit den folgenden Einstellungen:

	|Eigenschaft|Wert|
	|--------|-----|
	|Vorlage|Vorlagen/Visual C#/Windows/Konsolenanwendung|
	|Name|SubmitHiveJob|

3. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.
4. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	Diese Befehle installieren .NET-Bibliotheken und fügen Verweise zum aktuellen Visual Studio-Projekt hinzu.

5. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Program.cs**, um sie zu öffnen, fügen Sie den folgenden Code ein, und geben Sie Werte für die Variablen an:

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. Drücken Sie **F5**, um die Anwendung auszuführen.

##Übermitteln von Aufträgen mit den HDInsight-Tools für Visual Studio

Mithilfe der HDInsight-Tools für Visual Studio können Sie Hive-Abfragen und Pig-Skripts ausführen. Weitere Informationen finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Erstellen von Hadoop-Clustern in HDInsight][hdinsight-provision]
* [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell]
* [Referenzdokumentation zum HDInsight-Cmdlet][hdinsight-powershell-reference]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Oct15_HO3-->