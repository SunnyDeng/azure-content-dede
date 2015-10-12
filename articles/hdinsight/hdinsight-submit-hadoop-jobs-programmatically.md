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
	ms.date="09/22/2015"
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

* **Ein Azure HDInsight-Cluster**. Anweisungen hierzu finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/) (Installieren und Verwenden von Azure PowerShell, in englischer Sprache).



##Übermitteln von MapReduce-Aufträgen mithilfe von Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell].

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

	Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das Präfix "wasb://" finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].

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

	Der Speicherkontoname gehört zu dem Azure-Speicherkonto, das während der Bereitstellung des HDInsight-Clusters angegeben wurde. Über das Speicherkonto wird der Blobcontainer gehostet, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Container trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

3. Führen Sie die folgenden Befehle aus, um ein Kontextobjekt für den Azure-Blobspeicher zu erstellen:

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


Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit dem SDK bereitzustellen:

- Installieren des HDInsight .NET SDK
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK** Sie können den aktuellen veröffentlichen Build des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**Erstellen einer Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Unter **Neues Projekt** können Sie die folgenden Werte eingeben bzw. auswählen:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.


5. Klicken Sie im Menü **Extras** auf **Bibliothekspaket-Manager** und danach auf **Paket-Manager-Konsole**.

6. Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt. Die Version muss 0.11.0.1 oder höher sein.

7. Doppelklicken Sie im **Projektmappen-Explorer** auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. Fügen Sie der Klasse die folgende Funktionsdefinition hinzu. Die Funktion dient dazu, auf den Abschluss eines Hadoop-Auftrags zu warten.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Fügen Sie in der **Main()**-Funktion den folgenden Code ein:

		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";

		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";


	Dies sind alle Variablen, die Sie für das Programm festlegen müssen. Sie können den Azure-Abonnementnamen über das [Azure-Vorschauportal][azure-management-portal] abrufen.

	Informationen über das Zertifikat finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure][azure-certificate]. Eine einfache Methode zum Konfigurieren des Zertifikats ist die Ausführung der Azure PowerShell-Cmdlets **Get-AzurePublishSettingsFile** und **Import-AzurePublishSettingsFile**. Damit wird das Verwaltungszertifikat automatisch erstellt und hochgeladen. Nach der Ausführung dieser Cmdlets können Sie **certmgr.msc** auf der Arbeitsstation öffnen und das Zertifikat suchen, indem Sie **Persönlich** > **Zertifikate** erweitern. Die Felder **Ausgestellt für** und **Ausgestellt von** des von den Azure PowerShell-Cmdlets erstellten Zertifikats enthalten den Wert "Azure-Tools".

	Den Namen des Azure-Speicherkontos geben Sie an, wenn Sie den HDInsight-Cluster bereitstellen. Der Containername entspricht standardmäßig dem Namen des HDInsight-Clusters.

11. Hängen Sie in der **Main()**-Funktion den folgenden Code an, um den MapReduce-Auftrag zu definieren:


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das Präfix "wasb://" finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage].

12. Hängen Sie in der **Main()**-Funktion den folgenden Code an, um ein JobSubmissionCertificateCredential-Objekt zu erstellen:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Hängen Sie in der **Main()**-Funktion den folgenden Code an, um den Auftrag auszuführen und auf den Abschluss des Auftrags zu warten:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Hängen Sie in der **Main()**-Funktion den folgenden Code an, um die Ausgabe des MapReduce-Auftrags zu drucken:

		// Print the MapReduce job output
		Stream stream = new MemoryStream();

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");

		blockBlob.DownloadToStream(stream);
		stream.Position = 0;

		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

	Der Ausgabeordner wird während der Definition des MapReduce-Auftrags angegeben. Der Standarddateiname lautet **part-r-00000**.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. Ein Konsolenfenster wird geöffnet und zeigt den Status der Anwendung sowie die Ausgabe der Anwendung an.

##Übermitteln von Hadoop Streaming-Aufträgen mit dem HDInsight .NET SDK
HDInsight-Cluster werden mit einem Hadoop-Streamingprogramm zur Wortzählung erstellt, das in C# entwickelt wurde. Das Zuordnungsprogramm ist */example/apps/cat.exe*, und das Reduce-Programm ist */example/apps/wc.exe*. In dieser Sitzung erfahren Sie, wie Sie eine .NET-Anwendung zum Ausführen des Wortzählungsbeispiels erstellen können.

Details zum Erstellen einer .NET-Anwendung für die Übermittlung von MapReduce-Aufträgen finden Sie unter [Übermitteln von MapReduce-Aufträgen mit dem HDInsight .NET SDK](#mapreduce-sdk).

Weitere Informationen zum Entwickeln und Bereitstellen von Hadoop-Streamingaufträgen finden Sie unter [Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight][hdinsight-develop-streaming-jobs].

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;

	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;

	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;

	namespace SubmitStreamingJob
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {

				// Set the variables
				string subscriptionID = "<Azure subscription ID>";
				string certFriendlyName = "<certificate friendly name>";

				string clusterName = "<HDInsight cluster name>";
				string statusFolderName = @"/tutorials/wordcountstreaming/status";

	            // Define the Hadoop streaming MapReduce job
	            StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
	            {
	                JobName = "my word counting job",
	                StatusFolder = statusFolderName,
	                Input = "/example/data/gutenberg/davinci.txt",
	                Output = "/tutorials/wordcountstreaming/output",
	                Reducer = "wc.exe",
	                Mapper = "cat.exe"
	            };

	            myJobDefinition.Files.Add("/example/apps/wc.exe");
	            myJobDefinition.Files.Add("/example/apps/cat.exe");

	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

	            // Create a hadoop client to connect to HDInsight
	            var jobClient = JobSubmissionClientFactory.Connect(creds);

	            // Run the MapReduce job
	            Console.WriteLine("----- Submit the Hadoop streaming job ...");
	            JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);

	            // Wait for the job to complete
	            Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
	            WaitForJobCompletion(mrJobResults, jobClient);

	            // Display the error log
	            Console.WriteLine("----- The hadoop streaming job error log.");
	            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }

	            // Display the output log
	            Console.WriteLine("----- The hadoop streaming job output log.");
	            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }

	            Console.WriteLine("----- Press ENTER to continue.");
	            Console.ReadLine();
	        }

	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}






##Übermitteln von Hive-Aufträgen mit dem HDInsight .NET SDK
HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. In dieser Sitzung erstellen Sie eine .NET-Anwendung zum Ausführen eines Hive-Auftrags, mit dem die in einem HDInsight-Cluster erstellten Hive-Tabellen aufgelistet werden. Weitere Informationen zur Verwendung von Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].

Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit dem SDK bereitzustellen:

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
In diesem Artikel haben Sie mehrere Möglichkeiten zu Bereitstellung von HDInsight-Clustern kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Bereitstellen von HDInsight-Clustern][hdinsight-provision]
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

<!---HONumber=Oct15_HO1-->