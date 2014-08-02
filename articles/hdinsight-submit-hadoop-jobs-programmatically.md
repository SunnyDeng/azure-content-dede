<properties  linkid="manage-services-hdinsight-submit-hadoop-jobs-programmatically" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs programmatically | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to programmatically submit Hadoop jobs to Azure HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit Hadoop jobs programmatically" authors="jgao" />

# Programmgesteuerte Übermittlung von Hadoop-Jobs

In diesem Artikel lernen Sie, wie MapReduce- und Hive-Jobs mit PowerShell und HDInsight .NET SDK übermittelt werden.

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* Ein Azure-HDInsight-Cluster. Anweisungen hierzu finden Sie unter
  [Erste Schritte mit
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
  oder unter [Bereitstellen von
  HDInsight-Clustern](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).
* Installieren und konfigurieren Sie Azure PowerShell. Anweisungen
  hierzu finden Sie unter [Installieren und Konfigurieren von Azure
  PowerShell](/en-us/documentation/articles/install-configure-powershell/).

## Themen in diesem Artikel

* [Übermitteln von MapReduce-Jobs mit PowerShell](#mapreduce-powershell)
* [Übermitteln von Hive-Jobs mit PowerShell](#hive-powershell)
* [Übermitteln von Sqoop-Jobs mit PowerShell](#sqoop-powershell)
* [Übermitteln von MapReduce-Jobs mit HDInsight .NET
  SDK](#mapreduce-sdk)
* [Übermitteln von Hive-Jobs mit HDInsight .NET SDK](#hive-sdk)
* [Nächste Schritte](#nextsteps)

## <a id="mapreduce-powershell" ></a> Übermitteln von MapReduce-Jobs mit PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Weitere Informationen zum Verwenden von PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/).

Hadoop MapReduce ist ein Software-Framework zum Schreiben von Anwendungen, die riesige Datenmengen verarbeiten. HDInsight-Cluster sind in einer JAR-Datei enthalten, die unter
*\example\jars\hadoop-examples.jar* gespeichert ist und mehrere
MapReduce-Beispiele enthält. Diese Datei wurde für HDInsight-Cluster der Version 3.0 in hadoop-mapreduce-examples.jar umbenannt. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien. In dieser Sitzung lernen Sie die Verwendung von PowerShell auf einer Arbeitsstation, auf der das Wortzählungsbeispiel ausgeführt wird. Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/).

**So führen Sie das MapReduce-Programm zum Zählen von Wörtern mit
PowerShell aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des
    Konsolenfensters von Azure PowerShell finden Sie unter [Installieren
    und Konfigurieren von Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Legen Sie die beiden folgenden Variablen fest, indem Sie die
    folgenden PowerShell-Befehle ausführen:
    
         $subscriptionName = "<SubscriptionName>"   
         $clusterName = "<HDInsightClusterName>"    
    
    Bei dem Abonnement handelt es sich um dasjenige, das zum Erstellen
    des HDInsight-Clusters verwendet wurde. Das HDInsight-Cluster
    wiederum ist dasjenige, das Sie zum Ausführen des MapReduce-Jobs
    verwenden möchten.

3.  Führen Sie die folgenden Befehle aus, um eine
    MapReduce-Jobdefinition zu erstellen:

         # MapReduce-Job zur Wortzählung definieren
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
    
    Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das
    zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das
    Präfix wasb finden Sie unter [Verwenden von Azure-Blob-Speicher mit
    HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

4.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job
    auszuführen:

         # MapReduce-Job übermitteln
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
    
    Zusätzlich zur MapReduce-Jobdefinition müssen Sie auch den Namen des
    HDInsight-Clusters angeben, auf dem Sie den MapReduce-Job ausführen
    möchten.

5.  Führen Sie den folgenden Befehl aus, um den Abschluss des
    MapReduce-Jobs zu überprüfen:

         # Auf Abschluss des Jobs warten
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Fehler
    beim Ausführen des MapReduce-Jobs aufgetreten sind:

         # Standardfehler-Ausgabe des Jobs abrufen
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
    
    Der folgende Screenshot zeigt die Ausgabe bei erfolgreicher
    Ausführung. Andernfalls werden Fehlermeldungen angezeigt.
    
    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png)

**So erhalten Sie die Ergebnisse des MapReduce-Jobs**

1.  Öffnen Sie **Azure PowerShell**. 
2.  Legen Sie die drei folgenden Variablen fest, indem Sie die folgenden
    PowerShell-Befehle ausführen:
    
         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<ContainerName>"			
    
    Bei dem Azure-Speicherkonto handelt es sich um dasjenige, das
    während der Bereitstellung des HDInsight-Clusters angegeben wurde.
    Das Speicherkonto wird verwendet, um den Blob-Container zu hosten,
    der als Standarddateisystem für HDInsight-Cluster verwendet wird.
    Der Name des Blob-Speichercontainers trägt normalerweise denselben
    Namen wie der HDInsight-Cluster, außer, Sie haben einen anderen
    Namen bei der Bereitstellung des Clusters verwendet.

3.  Führen Sie folgende Befehle aus, um ein
    Azure-Speicherkonto-Kontextobjekt zu erstellen:

         # Speicherkonto-Kontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    Select-AzureSubscription wird verwendet, um das aktuelle Abonnement
    festzulegen, falls sie mehrere Abonnements haben und das
    Standardabonnement nicht verwendet werden soll.

4.  Führen Sie den folgenden Befehl aus, um die MapReduce-Jobausgabe vom
    Blob-Container zur Arbeitstation herunterzuladen:

         # Blob-Inhalt abrufen
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
    
    Der Ordner *example/data/WordCountOutput* ist der angegebene
    Ausgabeordner für die Ausführung des MapReduce-Jobs. *part-r-00000*
    ist der Standarddateiname für MapReduce-Jobausgaben. Die Datei wird
    in dieselbe Ordnerstruktur auf dem lokalen Ordner heruntergeladen.
    Im folgenden Screenshot ist der aktuelle Ordner der Stammordner C.
    Die Datei wird in den Ordner 'C:\example\data\WordCountOutput'
    heruntergeladen.

5.  Führen Sie den folgenden Befehl aus, um die
    MapReduce-Jobausgabedatei auszudrucken:
    
         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    
    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png)
    
    Der MapReduce-Job produziert eine Datei namens *part-r-00000*,
    welche die Wörter und deren Anzahl enthält. Das Skript verwendet den
    Befehl findstr, um alle Wörter aufzulisten, die "there" enthalten.

> [WACOM.NOTE] Wenn Sie ./example/data/WordCountOutput/part-r-00000,
> eine mehrzeilige Ausgabe eines MapReduce-Jobs, im Editor öffnen,
> werden Sie feststellen, dass die Zeilenumbrüche nicht korrekt sind.
> Dies entspricht dem erwarteten Verhalten.

## <a id="hive-powershell" ></a> Übermitteln von Hive-Jobs mit PowerShell

Apache [Hive][1] ermöglicht das Ausführen von MapReduce-Jobs über eine SQL-ähnliche Skriptsprache namens *HiveQL*, mit der große Datenmengen zusammengefasst, abgefragt und analysiert werden können.

HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen
*hivesampletable*. In dieser Sitzung verwenden Sie PowerShell, um einen
Hive-Job zum Auflisten einiger Daten aus der Hive-Tabelle auszuführen.

**So führen Sie einen Hive-Job mit PowerShell aus**

1.  Öffnen Sie **Azure PowerShell**. Anweisungen zum Öffnen des
    Konsolenfensters von Azure PowerShell finden Sie unter [Installieren
    und Konfigurieren von Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Legen Sie die ersten beiden Variablen in den folgenden Befehlen
    fest, und führen Sie danach die Befehle aus:
    
         $subscriptionName = "<SubscriptionName>"   
         $clusterName = "<HDInsightClusterName>"             
         $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"
    
    $querystring ist die HiveQL-Abfrage.

3.  Führen Sie die folgenden Befehle aus, um das Azure-Abonnement und
    das Cluster zum Ausführen des Hive-Jobs auszuwählen:
    
         Select-AzureSubscription -SubscriptionName $subscriptionName

4.  Übermitteln Sie den Hive-Job:
    
         Use-AzureHDInsightCluster $clusterName
         Invoke-Hive -Query $queryString
    
    Mit dem Parameter '-File' können Sie eine HiveQL-Skriptdatei im HDFS
    angeben.

Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/).

## <a  ></a>Übermitteln von Sqoop-Jobs mit PowerShell

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight](../hdinsight-use-sqoop/).

## <a id="mapreduce-sdk" ></a> Übermitteln von MapReduce-Jobs mit HDInsight .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. HDInsight-Cluster sind in einer JAR-Datei enthalten, die unter *\example\jars\hadoop-examples.jar* gespeichert ist und mehrere MapReduce-Beispiele enthält. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien. In dieser Sitzung lernen Sie, wie Sie eine .NET-Anwendung erstellen, um das Wortzählungsbeispiel auszuführen. Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/).

Die folgenden Verfahren werden benötigt, um ein HDInsight-Cluster mit dem SDK bereitzustellen:

* Installieren des HDInsight .NET SDK
* Erstellen einer Konsolenanwendung
* Ausführen der Anwendung

**So installieren Sie das HDInsight .NET SDK** Sie können den aktuellen
veröffentlichen Build des SDK von [NuGet][2] installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2012.

2.  Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3.  Geben Sie unter 'Neues Projekt' die folgenden Werte ein, oder wählen
    Sie diese aus:
    
    <table  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
     <tr>
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
    
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td>
    </tr>
    
     </table>

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **Library Package Manager** und
    danach auf **Paket-Manager-Konsole**.

6.  Führen Sie in der Konsole die folgenden Befehle aus, um die Pakete
    zu installieren.
    
         Install-Package Microsoft.WindowsAzure.Management.HDInsight
    
    Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt
    .NET-Bibliotheken und Verweise darauf hinzugefügt. Die Version muss
    0.11.0.1 oder höher sein.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um
    die Datei zu öffnen.

8.  Fügen Sie die folgenden using-Anweisungen am Anfang der Datei hinzu:
    
         using System.IO;
         using System.Threading;
         using System.Security.Cryptography.X509Certificates;
        	
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Blob;
        	
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.Hadoop.Client;

9.  Fügen Sie der Klasse die folgende Funktionsdefinition hinzu. Die
    Funktion dient dazu, auf den Abschluss eines Hadoop-Jobs zu warten.
    
         private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
         {
             JobDetails jobInProgress = client.GetJob(jobResults.JobId);
             while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
             {
                 jobInProgress = client.GetJob(jobInProgress.JobId);
                 Thread.Sleep(TimeSpan.FromSeconds(10));
             }
         }

10. Kopieren Sie in der Main()-Funktion den folgenden Code, und fügen
    Sie ihn ein:
    
        // Die Variablen festlegen
        string subscriptionID = "<Azure  subscription ID>";
        string certFriendlyName = "<certificate  friendly name>";
        
        string clusterName = "<HDInsight  cluster name>";
        	
        string storageAccountName = "<Azure  storage account name>";
        string storageAccountKey = "<Azure  storage account key>";
        string containerName = "<Blob  container name>";
    
    Dies sind alle Variablen, die Sie für das Programm festlegen müssen.
    Sie können den Azure-Abonnementnamen über das
    [Azure-Verwaltungsportal][3] abrufen.
    
    Informationen über Zertifikate finden Sie unter [Erstellen und
    Hochladen eines Verwaltungszertifikats für Azure][4]. Eine einfache
    Methode zum Konfigurieren des Zertifikats ist die Ausführung der
    PowerShell-Cmdlets *Get-AzurePublishSettingsFile* und
    *Import-AzurePublishSettingsFile*. Damit wird das
    Verwaltungszertifikat automatisch erstellt und hochgeladen. Nach der
    Ausführung der PowerShell-Cmdlets können Sie *certmgr.msc* auf der
    Arbeitsstation öffnen und das Zertifikat suchen, indem Sie
    *Persönlich/Zertifikate* erweitern. Sowohl das Feld *Ausgestellt
    für* als auch das Feld *Ausgestellt von* des von den
    PowerShell-Cmdlets erstellten Zertifikats enthält den Wert
    *Azure-Tools*.
    
    Den Namen des Azure-Speicherkontos geben Sie an, wenn Sie das
    HDInsight-Cluster bereitstellen. Der Containername entspricht
    standardmäßig dem Namen des HDInsight-Clusters.

11. Hängen Sie in der Main()-Funktion den folgenden Code an, um den
    MapReduce-Job zu definieren:
    
        // Den MapReduce-Job definieren
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };
        
        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");
    
    Es gibt zwei Argumente. Das erste ist der Name der Quelldatei, das
    zweite ist der Pfad der Ausgabedatei. Weitere Informationen über das
    Präfix wasb finden Sie unter [Verwenden von Azure-Blob-Speicher mit
    HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

12. Hängen Sie in der Main()-Funktion den folgenden Code an, um ein
    JobSubmissionCertificateCredential-Objekt zu erstellen:
    
        // Zertifikatobjekt aus dem Zertifikatspeicher abrufen und mit dem
        Anzeigenamen bezeichnen X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);



13. Hängen Sie in der Main()-Funktion den folgenden Code an, um den Job
    auszuführen und auf den Abschluss des Jobs zu warten:
    
        // Einen Hadoop-Client erstellen, um eine Verbindung mit HDInsight herzustellen
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        
        // Den MapReduce-Job ausführen
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);
        
        // Auf Abschluss des Jobs warten
        WaitForJobCompletion(mrJobResults, jobClient);

14. Hängen Sie in der Main()-Funktion den folgenden Code an, um die
    Ausgabe des MapReduce-Jobs zu drucken:
    
        // Die Ausgabe des MapReduce-Jobs drucken
        Stream stream = new MemoryStream();
        	
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
        	
        blockBlob.DownloadToStream(stream);
        stream.Position = 0;
        	
        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());
        	
        Console.WriteLine("Eingabetaste drücken, um fortzufahren.");
        Console.ReadLine();
    
    Der Ausgabeordner wird während der Definition des MapReduce-Jobs
    angegeben. Der Standarddateiname lautet *part-r-00000*.

**So führen Sie die Anwendung aus**

Während die Anwendung in Visual Studio geöffnet ist, drücken Sie **F5**, um die Anwendung auszuführen. Ein Konsolenfenster wird geöffnet und zeigt den Status der Anwendung sowie die Ausgabe der Anwendung an.

## <a id="hive-sdk" ></a> Übermitteln von Hive-Jobs mit HDInsight .NET SDK

HDInsight-Cluster enthalten eine Hive-Beispieltabelle mit dem Namen
*hivesampletable*. In dieser Sitzung erstellen Sie eine .NET-Anwendung,
um einen Hive-Job zum Auflisten der im HDInsight-Cluster erstellten Hive-Tabellen auszuführen. Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/).

Die folgenden Verfahren werden benötigt, um ein HDInsight-Cluster mit dem SDK bereitzustellen:

* Installieren des HDInsight .NET SDK
* Erstellen einer Konsolenanwendung
* Ausführen der Anwendung

**So installieren Sie das HDInsight .NET SDK** Sie können den aktuellen
veröffentlichen Build des SDK von [NuGet][2] installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie eine Visual Studio-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2012.

2.  Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3.  Geben Sie unter 'Neues Projekt' die folgenden Werte ein, oder wählen
    Sie diese aus:
    
    <table  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
     <tr>
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
    
     <th  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Templates/Visual C#/Windows</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td>
    </tr>
    
     <tr>
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
    
     <td  style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td>
    </tr>
    
     </table>

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

5.  Klicken Sie im Menü **Extras** auf **Library Package Manager** und
    danach auf **Paket-Manager-Konsole**.

6.  Führen Sie in der Konsole die folgenden Befehle aus, um die Pakete
    zu installieren.
    
         Install-Package Microsoft.WindowsAzure.Management.HDInsight
    
    Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt
    .NET-Bibliotheken und Verweise darauf hinzugefügt.

7.  Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um
    die Datei zu öffnen.

8.  Fügen Sie die folgenden using-Anweisungen am Anfang der Datei hinzu:
    
         using System.IO;
         using System.Threading;
         using System.Security.Cryptography.X509Certificates;
        
         using Microsoft.WindowsAzure.Management.HDInsight;
         using Microsoft.Hadoop.Client;

9.  Fügen Sie der Klasse die folgende Funktionsdefinition hinzu. Die
    Funktion dient dazu, auf den Abschluss eines Hadoop-Jobs zu warten.
    
         private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
         {
             JobDetails jobInProgress = client.GetJob(jobResults.JobId);
             while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
             {
                 jobInProgress = client.GetJob(jobInProgress.JobId);
                 Thread.Sleep(TimeSpan.FromSeconds(10));
             }
         }

10. Kopieren Sie in der Main()-Funktion den folgenden Code, und fügen
    Sie ihn ein:
    
        // Die Variablen festlegen
        string subscriptionID = "<Azure  subscription ID>";
        string clusterName = "<HDInsight  cluster name>";
        string certFriendlyName = "<certificate  friendly name>";		
    
    Dies sind alle Variablen, die Sie für das Programm festlegen müssen.
    Die Azure-Abonnement-ID erhalten Sie vom Systemadministrator.
    
    Informationen über Zertifikate finden Sie unter [Erstellen und
    Hochladen eines Verwaltungszertifikats für Azure][4]. Eine einfache
    Methode zum Konfigurieren des Zertifikats ist die Ausführung der
    PowerShell-Cmdlets *Get-AzurePublishSettingsFile* und
    *Import-AzurePublishSettingsFile*. Damit wird das
    Verwaltungszertifikat automatisch erstellt und hochgeladen. Nach der
    Ausführung der PowerShell-Cmdlets können Sie *certmgr.msc* auf der
    Arbeitsstation öffnen und das Zertifikat suchen, indem Sie
    *Persönlich/Zertifikate* erweitern. Sowohl das Feld *Ausgestellt
    für* als auch das Feld *Ausgestellt von* des von den
    PowerShell-Cmdlets erstellten Zertifikats enthält den Wert
    *Azure-Tools*.

11. Hängen Sie in der Main()-Funktion den folgenden Code an, um den
    Hive-Job zu definieren:
    
        // Den Hive-Job definieren
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "Job zum Anzeigen von Tabellen",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };
    
    Mit dem Parameter 'File' können Sie eine HiveQL-Skriptdatei im HDFS
    angeben. Beispiel:
    
        // Den Hive-Job definieren
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "Job zum Anzeigen von Tabellen",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

12. Hängen Sie in der Main()-Funktion den folgenden Code an, um ein
    JobSubmissionCertificateCredential-Objekt zu erstellen:
    
        // Zertifikatobjekt aus dem Zertifikatspeicher abrufen und mit dem
        Anzeigenamen bezeichnen X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);


13. Hängen Sie in der Main()-Funktion den folgenden Code an, um den Job
    auszuführen und auf den Abschluss des Jobs zu warten:
    
        // Den Hive-Job übermitteln
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);
        
        // Auf Abschluss des Jobs warten
        WaitForJobCompletion(jobResults, jobClient);

14. Hängen Sie in der Main()-Funktion den folgenden Code an, um die
    Ausgabe des Hive-Jobs zu drucken:
    
        // Die Ausgabe des Hive-Jobs drucken
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);
        
        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());
        
        Console.WriteLine("Eingabetaste drücken, um fortzufahren.");
        Console.ReadLine();

**So führen Sie die Anwendung aus**

Während die Anwendung in Visual Studio geöffnet ist, drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Ausgabe sieht wie folgt aus:

    hivesampletable

## <a id="nextsteps" ></a>Nächste Schritte

In diesem Artikel haben Sie verschiedene Methoden zum Bereitstellen eines HDInsight-Clusters kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
* [Bereitstellen von
  HDInsight-Clustern](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)
* [Verwalten von HDInsight mit
  PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
* [HDInsight-Cmdlet-Referenzdokumentation][5]
* [Verwenden von Hive mit
  HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
* [Verwenden von Pig mit
  HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)



[1]: http://hive.apache.org/
[2]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
[3]: http://manage.windowsazure.com/
[4]: http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx
[5]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx