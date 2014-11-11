<properties linkid="manage-services-hdinsight-submit-hadoop-jobs" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to submit Hadoop jobs to Azure HDInsight Hadoop." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit  Hadoop jobs in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Übermitteln von Hadoop-Aufträgen in HDInsight

In diesem Artikel lernen Sie, wie MapReduce- und Hive-Jobs mit PowerShell und HDInsight .NET SDK übermittelt werden.

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

-   Einen Azure HDInsight-Cluster. Anweisungen hierzu finden Sie unter [Erste Schritte mit HDInsight][Erste Schritte mit HDInsight] oder unter [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern].
-   Installieren und konfigurieren Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

## Themen in diesem Artikel

-   [Übermitteln von MapReduce-Jobs mit PowerShell][Übermitteln von MapReduce-Jobs mit PowerShell]
-   [Übermitteln von Hive-Jobs mit PowerShell][Übermitteln von Hive-Jobs mit PowerShell]
-   [Übermitteln von Sqoop-Jobs mit PowerShell][Übermitteln von Sqoop-Jobs mit PowerShell]
-   [Übermitteln von MapReduce-Jobs mit HDInsight .NET SDK][Übermitteln von MapReduce-Jobs mit HDInsight .NET SDK]
-   [Übermitteln von Hadoop Streaming MapReduce-Aufträgen mit dem HDInsight .NET SDK][Übermitteln von Hadoop Streaming MapReduce-Aufträgen mit dem HDInsight .NET SDK]
-   [Übermitteln von Hive-Jobs mit HDInsight .NET SDK][Übermitteln von Hive-Jobs mit HDInsight .NET SDK]
-   [Nächste Schritte][Nächste Schritte]

## <span id="mapreduce-powershell"></span></a> Übermitteln von MapReduce-Jobs mit PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen zum Verwenden von PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell].

Hadoop MapReduce ist ein Software-Framework zum Schreiben von Anwendungen, die riesige Datenmengen (Big Data) verarbeiten. HDInsight-Cluster sind in einer JAR-Datei enthalten, die unter *\\example\\jars\\hadoop-examples.jar* gespeichert ist und mehrere MapReduce-Beispiele enthält. Diese Datei wurde für HDInsight-Cluster der Version 3.0 in hadoop-mapreduce-examples.jar umbenannt. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien. In dieser Sitzung lernen Sie die Verwendung von PowerShell auf einer Arbeitsstation, auf der das Wortzählungsbeispiel ausgeführt wird. Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight][Verwenden von MapReduce mit HDInsight].

**So führen Sie das MapReduce-Programm zum Zählen von Wörtern mit PowerShell aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

2.  Legen Sie die beiden folgenden Variablen fest, indem Sie die folgenden PowerShell-Befehle ausführen:

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"    

    Bei dem Abonnement handelt es sich um dasjenige, das zum Erstellen des HDInsight-Clusters verwendet wurde. Der HDInsight-Cluster wiederum ist derjenige, den Sie zum Ausführen des MapReduce-Jobs verwenden möchten.

3.  Führen Sie die folgenden Befehle aus, um eine MapReduce-Jobdefinition zu erstellen:

        # Define the word count MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das Präfix wasb finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight][Verwenden von Azure-Blob-Speicher mit HDInsight].

4.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job auszuführen:

        # Submit the MapReduce job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen möchten.

5.  Führen Sie den folgenden Befehl aus, um den Abschluss des MapReduce-Jobs zu überprüfen:

        # Wait for the job to complete
        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler beim Ausführen des MapReduce-Jobs aufgetreten sind:

        # Get the job standard error output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

    Der folgende Screenshot zeigt die Ausgabe bei erfolgreicher Ausführung. Andernfalls werden Fehlermeldungen angezeigt.

    ![HDI.GettingStarted.RunMRJob][HDI.GettingStarted.RunMRJob]

**So rufen Sie die Ergebnisse des MapReduce-Jobs ab**

1.  Öffnen Sie **Azure PowerShell**.
2.  Legen Sie die drei folgenden Variablen fest, indem Sie die folgenden PowerShell-Befehle ausführen:

        $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"          

    Bei dem Azure-Speicherkonto handelt es sich um dasjenige, das während der Bereitstellung des HDInsight-Clusters angegeben wurde. Das Speicherkonto wird verwendet, um den Blob-Container zu hosten, der als Standarddateisystem für HDInsight-Cluster verwendet wird. Der Blob-Speichercontainer trägt normalerweise denselben Namen wie der HDInsight-Cluster, es sei denn, Sie haben bei der Bereitstellung des Clusters einen anderen Namen angegeben.

3.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

        # Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Select-AzureSubscription wird verwendet, um das aktuelle Abonnement festzulegen, falls sie mehrere Abonnements haben und das Standardabonnement nicht verwendet werden soll.

4.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe aus dem Blob-Container auf die Arbeitsstation herunterzuladen:

        # Get the blob content
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Der Ordner *example/data/WordCountOutput* ist der angegebene Ausgabeordner für die Ausführung des MapReduce-Jobs. *part-r-00000* ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird in dieselbe Ordnerstruktur auf dem lokalen Ordner heruntergeladen. Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C. Die Datei wird in den Ordner "C:\\example\\data\\WordCountOutput" heruntergeladen.

5.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabedatei auszudrucken:

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput][HDI.GettingStarted.MRJobOutput]

    Der MapReduce-Job produziert eine Datei namens *part-r-00000*, die die Wörter und deren Anzahl enthält. Das Skript verwendet den Befehl findstr, um alle Wörter aufzulisten, die "there" enthalten.

> [WACOM.NOTE] Wenn Sie ./example/data/WordCountOutput/part-r-00000, eine mehrzeilige Ausgabe eines MapReduce-Jobs, im Editor öffnen, werden Sie feststellen, dass die Zeilenumbrüche nicht korrekt sind. Dies entspricht dem erwarteten Verhalten.

## <span id="hive-powershell"></span></a> Übermitteln von Hive-Jobs mit PowerShell

Mit Apache [hdinsight-use-hive][hdinsight-use-hive] können Sie MapReduce-Aufträge in Form einer SQL-ähnlichen Skriptsprache namens *HiveQL* ausführen und große Datenmengen zusammenfassen, abfragen und analysieren.

HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. In dieser Sitzung verwenden Sie PowerShell, um einen Hive-Job zum Auflisten einiger Daten aus der Hive-Tabelle auszuführen.

**So führen Sie einen Hive-Job mit PowerShell aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des Konsolenfensters von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

2.  Legen Sie die ersten beiden Variablen in den folgenden Befehlen fest, und führen Sie danach die Befehle aus:

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"             
        $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

    $querystring ist die HiveQL-Abfrage.

3.  Führen Sie die folgenden Befehle aus, um das Azure-Abonnement und den Cluster zum Ausführen des Hive-Jobs auszuwählen:

        Select-AzureSubscription -SubscriptionName $subscriptionName

4.  Übermitteln Sie den Hive-Job:

        Use-AzureHDInsightCluster $clusterName
        Invoke-Hive -Query $queryString

    Mit dem Parameter '-File' können Sie eine HiveQL-Skriptdatei im HDFS angeben.

Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight].

## <span id="sqoop-powershell"></span></a>Übermitteln von Sqoop-Jobs mit PowerShell

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight][Verwenden von Sqoop mit HDInsight].

## <span id="mapreduce-sdk"></span></a> Übermitteln von MapReduce-Jobs mit HDInsight .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. HDInsight-Cluster sind in einer JAR-Datei enthalten, die unter *\\example\\jars\\hadoop-examples.jar* gespeichert ist und mehrere MapReduce-Beispiele enthält. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien. In dieser Sitzung lernen Sie, wie Sie eine .NET-Anwendung erstellen, um das Wortzählungsbeispiel auszuführen. Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight][Verwenden von MapReduce mit HDInsight].

Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit dem SDK bereitzustellen:

-   Installieren des HDInsight .NET SDK
-   Erstellen einer Konsolenanwendung
-   Ausführen der Anwendung

**So installieren Sie das HDInsight .NET SDK**
 Sie können den aktuellen veröffentlichen Build des SDK von [NuGet][NuGet] installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2012.

2.  Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3.  Unter Neues Projekt können Sie die folgenden Werte eingeben bzw. auswählen:

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr><th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;"> Eigenschaft </th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;"> Wert    </th></tr>
    <tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie  </td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;"> Vorlagen/Visual C#/Windows </td></tr>
    <tr><td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Vorlage </td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Konsolenanwendung</td></tr>
    <tr><td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Name    </td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> SubmitMapReduceJob          </td></tr>
	</table>

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **Paketverwaltung** und anschließend auf **Paketverwaltungskonsole**.

6.  Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt. Die Version muss 0.11.0.1 oder höher sein.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8.  Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  Fügen Sie der Klasse die folgende Funktionsdefinition hinzu. Die Funktion dient dazu, auf den Abschluss eines Hadoop-Jobs zu warten.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Fügen Sie in der Main()-Funktion den folgenden Code ein:

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string certFriendlyName = "<certificate friendly name>";

        string clusterName = "<HDInsight cluster name>";

        string storageAccountName = "<Azure storage account name>";
        string storageAccountKey = "<Azure storage account key>";
        string containerName = "<Blob container name>";

    Dies sind alle Variablen, die Sie für das Programm festlegen müssen. Sie können den Azure-Abonnementnamen über das [Azure-Verwaltungsportal][Azure-Verwaltungsportal] abrufen.

    Informationen über Zertifikate finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure][Erstellen und Hochladen eines Verwaltungszertifikats für Azure]. Eine einfache Methode zum Konfigurieren des Zertifikats ist die Ausführung der PowerShell-Cmdlets *Get-AzurePublishSettingsFile* und *Import-AzurePublishSettingsFile*. Damit wird das Verwaltungszertifikat automatisch erstellt und hochgeladen. Nach der Ausführung der PowerShell-Cmdlets können Sie *certmgr.msc* auf der Arbeitsstation öffnen und das Zertifikat suchen, indem Sie *Persönlich/Zertifikate* erweitern. Sowohl das Feld *Ausgestellt für* als auch das Feld *Ausgestellt von* des von den PowerShell-Cmdlets erstellten Zertifikats enthält den Wert *Azure-Tools*.

    Den Namen des Azure-Speicherkontos geben Sie an, wenn Sie den HDInsight-Cluster bereitstellen. Der Containername entspricht standardmäßig dem Namen des HDInsight-Clusters.

11. Hängen Sie in der Main()-Funktion den folgenden Code an, um den MapReduce-Job zu definieren:

        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

    Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das Präfix wasb finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight][Verwenden von Azure-Blob-Speicher mit HDInsight].

12. Hängen Sie in der Main()-Funktion den folgenden Code an, um ein JobSubmissionCertificateCredential-Objekt zu erstellen:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Hängen Sie in der Main()-Funktion den folgenden Code an, um den Job auszuführen und auf den Abschluss des Jobs zu warten:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Hängen Sie in der Main()-Funktion den folgenden Code an, um die Ausgabe des MapReduce-Jobs zu drucken:

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

    Der Ausgabeordner wird während der Definition des MapReduce-Jobs angegeben. Der Standarddateiname lautet *part-r-00000*.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. Ein Konsolenfenster wird geöffnet und zeigt den Status der Anwendung sowie die Ausgabe der Anwendung an.

## <span id="streaming-sdk"></span></a> Übermitteln von Hadoop Streaming-Aufträgen mit dem HDInsight .NET SDK

HDInsight-Cluster werden mit einem Hadoop-Streamingprogramm zur Wortzählung erstellt, das in C# geschrieben ist. Das Zuordnungsprogramm ist */example/apps/cat.exe*, und das Reduce-Programm ist */example/apps/wc.exe*. In dieser Sitzung erfahren Sie, wie Sie eine .NET-Anwendung zum Ausführen des Wortzählungs-Beispiels entwickeln können.

Details zum Erstellen von .NET-Anwendungen für die Übermittlung von MapReduce-Aufträgen finden Sie unter [Übermitteln von MapReduce-Aufträgen mit dem HDInsight .NET SDK][Übermitteln von MapReduce-Jobs mit HDInsight .NET SDK].

Weitere Informationen zum Entwickeln und Bereitstellen von Hadoop Streaming-Aufträgen finden Sie unter [Entwickeln von Hadoop-Streamingprogrammen für HDInsight][Entwickeln von Hadoop-Streamingprogrammen für HDInsight].

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

## <span id="hive-sdk"></span></a> Übermitteln von Hive-Jobs mit HDInsight .NET SDK

HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. In dieser Sitzung erstellen Sie eine .NET-Anwendung, um einen Hive-Job zum Auflisten der im HDInsight-Cluster erstellten Hive-Tabellen auszuführen. Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight].

Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit dem SDK bereitzustellen:

-   Installieren des HDInsight .NET SDK
-   Erstellen einer Konsolenanwendung
-   Ausführen der Anwendung

**So installieren Sie das HDInsight .NET SDK**
 Sie können den aktuellen veröffentlichen Build des SDK von [NuGet][NuGet] installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2012.

2.  Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3.  Unter Neues Projekt können Sie die folgenden Werte eingeben bzw. auswählen:

    <table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;"> Eigenschaft</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;"> Wert </th></tr>
    <tr>
    <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Kategorie  </td> <td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows </td></tr>
    <tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Vorlage    </td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Konsolenanwendung           </td></tr>
    <tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> Name  </td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;"> SubmitHiveJob </td></tr>
	</table>

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **Paketverwaltung** und anschließend auf **Paketverwaltungskonsole**.

6.  Führen Sie die folgenden Befehle in der Konsole aus, um die Pakete zu installieren.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8.  Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  Fügen Sie der Klasse die folgende Funktionsdefinition hinzu. Die Funktion dient dazu, auf den Abschluss eines Hadoop-Jobs zu warten.

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Fügen Sie in der Main()-Funktion den folgenden Code ein:

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string clusterName = "<HDInsight cluster name>";
        string certFriendlyName = "<certificate friendly name>";        

    Dies sind alle Variablen, die Sie für das Programm festlegen müssen. Die Azure-Abonnement-ID erhalten Sie vom Systemadministrator.

    Informationen über Zertifikate finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure][Erstellen und Hochladen eines Verwaltungszertifikats für Azure]. Eine einfache Methode zum Konfigurieren des Zertifikats ist die Ausführung der PowerShell-Cmdlets *Get-AzurePublishSettingsFile* und *Import-AzurePublishSettingsFile*. Damit wird das Verwaltungszertifikat automatisch erstellt und hochgeladen. Nach der Ausführung der PowerShell-Cmdlets können Sie *certmgr.msc* auf der Arbeitsstation öffnen und das Zertifikat suchen, indem Sie *Persönlich/Zertifikate* erweitern. Sowohl das Feld *Ausgestellt für* als auch das Feld *Ausgestellt von* des von den PowerShell-Cmdlets erstellten Zertifikats enthält den Wert *Azure-Tools*.

11. Hängen Sie in der Main()-Funktion den folgenden Code an, um den Hive-Job zu definieren:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

    Mit dem Parameter 'File' können Sie eine HiveQL-Skriptdatei im HDFS angeben. Beispiel:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

12. Hängen Sie in der Main()-Funktion den folgenden Code an, um ein JobSubmissionCertificateCredential-Objekt zu erstellen:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Hängen Sie in der Main()-Funktion den folgenden Code an, um den Job auszuführen und auf den Abschluss des Jobs zu warten:

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);

14. Hängen Sie in der Main()-Funktion den folgenden Code an, um die Ausgabe des Hive-Jobs zu drucken:

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Ausgabe sieht wie folgt aus:

    hivesampletable

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit HDInsight]
-   [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern]
-   [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell]
-   [HDInsight-Cmdlet-Referenzdokumentation][HDInsight-Cmdlet-Referenzdokumentation]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]

  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Übermitteln von MapReduce-Jobs mit PowerShell]: #mapreduce-powershell
  [Übermitteln von Hive-Jobs mit PowerShell]: #hive-powershell
  [Übermitteln von Sqoop-Jobs mit PowerShell]: #sqoop-powershell
  [Übermitteln von MapReduce-Jobs mit HDInsight .NET SDK]: #mapreduce-sdk
  [Übermitteln von Hadoop Streaming MapReduce-Aufträgen mit dem HDInsight .NET SDK]: #streaming-sdk
  [Übermitteln von Hive-Jobs mit HDInsight .NET SDK]: #hive-sdk
  [Nächste Schritte]: #nextsteps
  [Verwalten von HDInsight mit PowerShell]: ../hdinsight-administer-use-powershell/
  [Verwenden von MapReduce mit HDInsight]: ../hdinsight-use-mapreduce/
  [Verwenden von Azure-Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [HDI.GettingStarted.RunMRJob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
  [HDI.GettingStarted.MRJobOutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png
  [hdinsight-use-hive]: http://hive.apache.org/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Verwenden von Sqoop mit HDInsight]: ../hdinsight-use-sqoop/
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com/
  [Erstellen und Hochladen eines Verwaltungszertifikats für Azure]: http://msdn.microsoft.com/de-de/library/windowsazure/gg551722.aspx
  [Entwickeln von Hadoop-Streamingprogrammen für HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [HDInsight-Cmdlet-Referenzdokumentation]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
