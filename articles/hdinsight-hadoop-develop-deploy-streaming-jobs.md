<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, dhinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Entwickeln von C\# Hadoop-Streamingprogrammen für HDInsight
===========================================================

Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Lernprogramm werden Sie durch ein End-to-End-Szenario vom Entwickeln und Testen eines Hadoop-Streaming-MapReduce-Programms unter Verwendung von C\# auf einem HDInsight Emulator über die Ausführung von MapReduce-Jobs auf Azure HDInsight bis zum Abrufen der Ergebnisse geführt.

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   Installieren Sie den Azure HDInsight Emulator. Informationen dazu finden Sie unter [Get Started using HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).](/de-de/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).
-   Installieren Sie Azure PowerShell auf dem Emulator-Computer. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).
-   Schließen Sie ein Azure-Abonnement ab. Informationen dazu finden Sie unter [Erste Schritte mit Azure](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Spezielle Angebote](https://www.windowsazure.com/en-us/pricing/member-offers/) oder [Kostenlose einmonatige Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/).

Themen in diesem Artikel
------------------------

-   [Entwickeln eines Hadoop-Streaming-Wortzählprogramms in C\#](#develop)
-   [Testen des Programms auf dem Emulator](#test)
-   [Hochladen von Daten und Anwendungen in den Azure-Blob-Speicher](#upload)
-   [Ausführen des MapReduce-Programms auf Azure HDInsight](#run)
-   [Abrufen der MapReduce-Ergebnisse](#retrieve)
-   [Nächste Schritte](#nextsteps)

Entwickeln eines Hadoop-Streaming-Wortzählprogramms in C\#
----------------------------------------------------------

Diese Wortzählungslösung besteht aus zwei Konsolenanwendungsprojekten: Mapper und Reducer. Die Anwendung "Mapper" streamt jedes Wort in die Konsole, und die Anwendung "Reducer" zählt die Wörter, die aus einem Dokument gestreamt werden. Sowohl der Mapper als auch der Reducer lesen Zeichen (zeilenweise) aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout).

**Erstellung einer C\#-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2013.
2.  Klicken Sie auf **DATEI**, auf **Neu** und anschließend auf **Projekt**.
3.  Geben Sie folgende Werte ein bzw. wählen Sie diese aus:

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><td data-morhtml="true">Feld</td><td data-morhtml="true">Wert</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Vorlage</td><td data-morhtml="true">Visual C#/Windows-Konsolenanwendung</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Name</td><td data-morhtml="true">WordCountMapper</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Standort</td><td data-morhtml="true">C:\Tutorials</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">Projektmappenname</td><td data-morhtml="true">WordCount</td></tr>
	</table>


4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

**Erstellen des Mapper-Programms**

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Program.cs**. Klicken Sie dann auf **Umbenennen**.
2.  Benennen Sie die Datei in **WordCountMapper.cs** um, und drücken Sie dann die **Eingabetaste**.
3.  Klicken Sie auf **Ja**, um die Umbenennung aller Verweise zu bestätigen.
4.  Doppelklicken Sie auf die Datei **WordCountMapper.cs**, um sie zu öffnen.
5.  Fügen Sie die folgende using-Anweisung hinzu:

         using System.IO;

6.  Ersetzen Sie die Main()-Funktion durch Folgendes:

        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }
            
            string line;
            string[] words;
            
            while ((line = Console.ReadLine()) != null)
            {
                words = line.Split(' ');
            
                foreach (string word in words)
                    Console.WriteLine(word.ToLower());
            }
        }

7.  Klicken Sie auf **BUILD** und anschließend auf **Projektmappe erstellen**, um das Mapper-Programm zu kompilieren.

**Erstellen des Reducer-Programms**

1.  Klicken Sie in Visual Studio 2013 auf **DATEI**, auf **Hinzufügen** und auf **Neues Projekt**.
2.  Geben Sie folgende Werte ein bzw. wählen Sie diese aus:

	<table data-morhtml="true" border="1">
	<tr data-morhtml="true"><td data-morhtml="true">Ordner</td><td data-morhtml="true">Hinweis</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">\WordCount</td><td data-morhtml="true">Der Stammordner f&uuml;r das Wortz&auml;hlungsprojekt. </td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">\WordCount\Apps</td><td data-morhtml="true">Der Ordner f&uuml;r die Mapper- und Reducer-Programme.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">\WordCount\Input</td><td data-morhtml="true">Der Ordner f&uuml;r die MapReduce-Quelldateien.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">\WordCount\Output</td><td data-morhtml="true">Der Ordner f&uuml;r die MapReduce-Ausgabedateien.</td></tr>
	<tr data-morhtml="true"><td data-morhtml="true">\WordCount\MRStatusOutput</td><td data-morhtml="true">Der Ordner f&uuml;r die Jobergebnisse.</td></tr>
	</table>

3.  Klicken Sie auf **OK**, um das Projekt zu erstellen.
4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Program.cs**. Klicken Sie dann auf **Umbenennen**.
5.  Benennen Sie die Datei in **WordCountReducer.cs** um, und drücken Sie dann die **Eingabetaste**.
6.  Klicken Sie auf **Ja**, um die Umbenennung aller Verweise zu bestätigen.
7.  Doppelklicken Sie auf die Datei **WordCountReducer.cs**, um sie zu öffnen.
8.  Fügen Sie die folgende using-Anweisung hinzu:

         using System.IO;

9.  Ersetzen Sie die Main()-Funktion durch Folgendes:

        static void Main(string[] args)
        {
            string word, lastWord = null;
            int count = 0;
            
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }
            
            while ((word = Console.ReadLine()) != null)
            {
                if (word != lastWord)
                {
                    if(lastWord != null)
                        Console.WriteLine("{0}[{1}]", lastWord, count);
            
                    count = 1;
                    lastWord = word;
                }
                else
                {
                    count += 1; 
                }
            }
            Console.WriteLine(count);
        }

10. Klicken Sie auf **BUILD** und anschließend auf **Projektmappe erstellen**, um die Lösung zu kompilieren.

Die ausführbaren Dateien für den Mapper und den Reducer haben folgende Pfade:

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

Testen des Programms auf dem Emulator
-------------------------------------

Dieser Abschnitt enthält die folgenden Verfahren:

1.  Hochladen der Daten in das Emulator-HDFS
2.  Hochladen des Mappers und des Reducers in das Emulator-HDFS
3.  Einen MapReduce-Wortzähljob übermitteln
4.  Prüfen des Jobstatus
5.  Abrufen der Ergebnisse des Jobs

Standardmäßig verwendet der HDInsight Emulator als Dateisystem HDFS. Optional können Sie den HDInsight Emulator für eine Verwendung von Azure-Blob-Speicher konfigurieren. Informationen dazu finden Sie unter [Get Started with HDInsight Emulator (Erste Schritte mit dem HDInsight-Emulator, in englischer Sprache).](/de-de/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage). In diesem Abschnitt verwenden Sie den HDFS-Befehl copyFromLocal, um die Dateien hochzuladen. Im nächsten Abschnitt wird gezeigt, wie Sie Dateien mit Azure PowerShell hochladen. Weitere Methoden finden Sie unter [Hochladen von Daten zu HDInsight](/de-de/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

In diesem Lernprogramm wird die folgende Ordnerstruktur verwendet:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><td data-morhtml="true">Ordner</td><td data-morhtml="true">Hinweis</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\WordCount</td><td data-morhtml="true">Der Stammordner f&uuml;r das Wortz&auml;hlungsprojekt. </td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\WordCount\Apps</td><td data-morhtml="true">Der Ordner f&uuml;r die Mapper- und Reducer-Programme.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\WordCount\Input</td><td data-morhtml="true">Der Ordner f&uuml;r die MapReduce-Quelldateien.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\WordCount\Output</td><td data-morhtml="true">Der Ordner f&uuml;r die MapReduce-Ausgabedateien.</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">\WordCount\MRStatusOutput</td><td data-morhtml="true">Der Ordner f&uuml;r die Jobergebnisse.</td></tr>
</table>


In diesem Lernprogramm werden die .txt-Dateien im Verzeichnis %hadoop\_home% verwendet.

> [WACOM.NOTE] Bei den Hadoop-HDFS-Befehlen muss die Groß- und Kleinschreibung beachtet werden.

**Kopieren der Textdateien in das Emulator-HDFS**

1.  Führen Sie im Hadoop-Befehlszeilenfenster den folgenden Befehl aus, um ein Verzeichnis für die Eingabedateien anzulegen:

         hadoop fs -mkdir /WordCount/Input

    Der hier verwendete Pfad ist der relative Pfad. Er ist gleichwertig mit folgendem Pfad:

         hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  Führen Sie den folgenden Befehl aus, um einige Textdateien in den Eingabeordner im HDFS zu kopieren:

         hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

         hadoop fs -ls \WordCount\Input

    Es werden ca. acht .txt-Dateien angezeigt.

**Bereitstellen des Mappers und des Reducers im Emulator-HDFS**

1.  Öffnen Sie die Hadoop-Befehlszeile auf Ihrem Desktop.
2.  Führen Sie die folgenden Befehle aus:

         hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
         hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

         hadoop fs -lsr /WordCount/Apps

    Sie sollten die zwei .exe-Dateien sehen.

**Ausführen des MapReduce-Jobs mit HDInsight PowerShell**

1.  Öffnen Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).
2.  Führen Sie die folgenden Befehle aus, um die Variablen festzulegen:

         $clusterName = "http://localhost:50111"
            
         $mrMapper = "WordCountMapper.exe"
         $mrReducer = "WordCountReducer.exe"
         $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
         $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
         $mrInput = "/WordCount/Input/"
         $mrOutput = "/WordCount/Output"
         $mrStatusOutput = "/WordCount/MRStatusOutput"

    Der Clustername im HDInsight Emulator ist "http://localhost:50111".

3.  Führen Sie die folgenden Befehle aus, um den Streaming-Job zu definieren:

         $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
         $mrJobDef.Files.Add($mrMapperFile)
         $mrJobDef.Files.Add($mrReducerFile)

4.  Führen Sie den folgenden Befehl aus, um eine Anmeldeinformationen-Objekt zu erstellen:

         $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    Sie werden aufgefordert, das Kennwort einzugeben. Das Kennwort kann eine beliebige Zeichenfolge sein. Der Benutzername muss "hadoop" lauten.

5.  Führen Sie die folgenden Befehle aus, um den MapReduce-Job zu übermitteln und auf den Abschluss des Jobs zu warten:

         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
         Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

    Wenn der Job abgeschlossen ist, sieht Ihr Bildschirm ungefähr folgendermaßen aus:

         StatusDirectory : /WordCount/MRStatusOutput
         ExitCode        : 
         Name            : mrWordCountStreamingJob
         Query           : 
         State           : Completed
         SubmissionTime  : 11/15/2013 7:18:16 PM
         Cluster         : http://localhost:50111
         PercentComplete : map 100%  reduce 100%
         JobId           : job_201311132317_0034

    Sie können z. B. sehen, dass die Job-ID job-201311132317-0034 lautet.

**Prüfen des Jobstatus**

1.  Klicken Sie auf dem Desktop auf **Hadoop MapReduce Status**, oder navigieren Sie zu **http://localhost:50030/jobtracker.jsp**.
2.  Suchen Sie anhand der Job-ID in einem der drei folgenden Abschnitte nach dem Job: **Completed Jobs**, **Running Jobs**, **Retired Jobs**.
3.  Wenn ein Job fehlgeschlagen ist, können Sie die Jobdetails öffnen, um Debugging-Informationen zu erhalten.

**Anzeigen der Ergebnisse im HDFS**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Führen Sie den folgenden Befehl aus, um die Ergebnisse anzuzeigen:

         hadoop fs -ls /WordCount/Output/
         hadoop fs -cat /WordCount/Output/part-00000

    Sie können auch "|more" an das Ende der Befehlszeile anhängen, um eine Seitenansicht zu öffnen.

Hochladen von Daten in Azure-Blob-Speicher
------------------------------------------

Azure HDInsight verwendet Azure-Blob-Speicher als Standarddateisystem. Sie können ein HDInsight-Cluster für die Verwendung von zusätzlichem Blob-Speicher für die Datendateien verwenden. In diesem Abschnitt erstellen Sie ein Speicherkonto und laden die Datendateien in den Blob-Speicher hoch. Die Datendateien sind die .txt-Dateien im Verzeichnis %hadoop\_home%.

**Erstellen eines Blob-Speichers und eines Containers**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName = "<AzureStorageAccountName>"  
         $containerName = "<ContainerName>"
         $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  Führen Sie den folgenden Befehl aus, um ein Speicherkonto und einen Blob-Speichercontainer auf diesem Konto zu erstellen.

         # Azure-Abonnement auswählen
         Select-AzureSubscription $subscriptionName
            
         # Ein Speicherkonto erstellen
         New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
                    
         # Einen Blob-Speichercontainer erstellen
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
         New-AzureStorageContainer -Name $containerName -Context $destContext

4.  Führen Sie die folgenden Befehle aus, um das Speicherkonto und den Container zu prüfen:

         Get-AzureStorageAccount -StorageAccountName $storageAccountName
         Get-AzureStorageContainer -Context $destContext

**Hochladen der Datendateien**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName = "<AzureStorageAccountName>"  
         $containerName = "<ContainerName>"

         $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
         $destFolder = "WordCount/Input"

    Bitte beachten Sie: Der Quellordner ist **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT**, und der Zielordner ist **WordCount/Input**.

3.  Führen Sie die folgenden Befehle aus, um eine Liste der .txt-Dateien im Quelldateiordner abzurufen:

         # Eine Liste der txt-Dateien abrufen
         $filesAll = Get-ChildItem $localFolder
         $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Führen Sie die folgenden Befehle aus, um ein Speicherkontextobjekt zu erstellen:

         # Ein Speicherkontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5.  Führen Sie den folgenden Befehl aus, um die Dateien zu kopieren:

         # Datei von der lokalen Arbeitsstation in den Blob-Container kopieren        
         foreach ($file in $filesTxt){
             
             $fileName = "$localFolder\$file"
             $blobName = "$destFolder/$file"
            
             write-host "Copying $fileName to $blobName"
            
             Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
         }

6.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

         # In den Blob-Speichercontainer hochgeladene Dateien auflisten
         Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**Hochladen der Wortzählanwendungen**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die ersten drei Variablen fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName = "<AzureStorageAccountName>"  
         $containerName = "<ContainerName>"

         $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
         $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
         $blobFolder = "WordCount/Apps"

    Bitte beachten Sie: Der Zielordner ist **WordCount/Apps**.

3.  Führen Sie die folgenden Befehle aus, um ein Speicherkontextobjekt zu erstellen:

         # Ein Speicherkontextobjekt erstellen
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

4.  Führen Sie die folgenden Befehle aus, um die Anwendungen zu kopieren:

         Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
         Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

5.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

         # In den Blob-Speichercontainer hochgeladene Dateien auflisten
         Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    Dort sollten beide Dateien aufgelistet sein.

Ausführen des MapReduce-Jobs auf Azure HDInsight
------------------------------------------------

Das folgende PowerShell-Skript führt die folgenden Aufgaben aus:

1.  Bereitstellen eines HDInsight-Clusters

    1.  Erstellen eines Speicherkontos, das als Standard-Dateisystem für das HDInsight-Cluster verwendet wird
    2.  Erstellen eines Blob-Speichercontainers
    3.  Erstellen eines HDInsight-Clusters

2.  Übermitteln des MapReduce-Jobs

    1.  Erstellen einer Streaming-MapReduce-Jobdefinition
    2.  Übermitteln eines MapReduce-Jobs
    3.  Warten auf den Abschluss des Jobs
    4.  Anzeigen von Standardfehlern
    5.  Anzeigen der Standardausgabe

3.  Löschen des Clusters

    1.  Löschen des HDInsight-Clusters
    2.  Löschen des Speicherkontos, das als Standarddateisystem für das HDInsight-Cluster verwendet wird

**Ausführen des PowerShell-Skripts**

1.  Öffnen Sie Notepad.
2.  Kopieren und fügen Sie den folgenden Code ein:

         # Das Speicherkonto und die HDInsight-Cluster-Variablen
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $serviceNameToken = "<ServiceNameTokenString>"
         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
         $location = "East US"     ### müssen mit dem Speicherort des Datenspeicherkontos übereinstimmen
         $clusterNodes = 1
            
         $clusterName = $serviceNameToken + "hdicluster"
            
         $storageAccountName_Default = $serviceNameToken + "hdistore"
         $containerName_Default =  $serviceNameToken + "hdicluster"

         # Die Streaming-MapReduce-Job-Variablen
         $mrMapper = "WordCountMapper.exe"
         $mrReducer = "WordCountReducer.exe"
         $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
         $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
         $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
         $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
         $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
            
         Select-AzureSubscription $subscriptionName
            
         #=============================
         # Ein Speicherkonto erstellen
         Write-Host "Create a storage account" -ForegroundColor Green
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
            
         #=============================
         # Einen Blob-Speichercontainer erstellen
         Write-Host "Create a Blob storage container" -ForegroundColor Green
         $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
            
         New-AzureStorageContainer -Name $containerName_Default -Context $destContext
            
         #=============================
         # Ein HDInsight-Cluster erstellen
         Write-Host "Create an HDInsight cluster" -ForegroundColor Green
         $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
            
         $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
             Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
             Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
            
         New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config
            
         #=============================
         # Eine Streaming-MapReduce-Jobdefinition erstellen
         Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
            
         $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
         $mrJobDef.Files.Add($mrMapperFile)
         $mrJobDef.Files.Add($mrReducerFile)
            
         #=============================
         # Einen Streaming-MapReduce-Job ausführen
         Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
         Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 
            
         Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
         Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput
            
         #=============================
         # Das HDInsight-Cluster löschen
         Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
         Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 
            
         # Das Speicherkonto löschen
         Write-Host "Delete the storage account" -ForegroundColor Green
         Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Legen Sie die ersten vier Variablen in dem Skript fest. \$serviceNameToken wird als Name des HDInsight-Clusters, als Name des Speicherkontos und als Name des Blob-Speichercontainers verwendet. Da der Dienstname zwischen 3 und 24 Zeichen lang sein muss und das Skript Zeichenfolgen von bis zu 10 Zeichen Länge an die Namen anhängt, müssen Sie die Zeichenfolge auf höchstens 14 Zeichen begrenzen. Verwenden Sie für \$serviceNameToken ausschließlich Kleinbuchstaben. \$storageAccountName\_Data und \$containerName\_Data sind das Speicherkonto und der Container, die zur Speicherung der Datendateien und der Anwendungen verwendet werden. \$location muss mit dem Speicherort des Datenspeicherkontos übereinstimmen.
4.  Prüfen Sie auch die restlichen Variablen.
5.  Speichern Sie die Skript-Datei.
6.  Öffnen Sie Azure PowerShell.
7.  Führen Sie den folgenden Befehl aus, um die Ausführungsrichtlinie auf "RemoteSigned" zu setzen:

         PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Wenn Sie dazu aufgefordert werden, geben Sie den Benutzernamen und das Kennwort für das HDInsight-Cluster ein. Da Sie das Cluster am Ende des Skripts löschen und den Benutzernamen und das Kennwort dann nicht mehr benötigen, können Benutzername und Kennwort beliebige Zeichenfolgen sein. Wenn Sie die Eingabe der Anmeldeinformationen überspringen möchten, siehe [Working with Passwords, Secure Strings and Credentials in Windows PowerShell (Arbeiten mit Kennwörtern, sicheren Zeichenfolgen und Anmeldeinformationen in Windows PowerShell, in englischer Sprache)](http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx)

Abrufen der Ergebnisse des MapReduce-Jobs
-----------------------------------------

In diesem Abschnitt wird erläutert, wie Sie die Ergebnisse herunterladen und anzeigen. Informationen zur Anzeige der Ergebnisse in Excel finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight](/de-de/manage/services/hdinsight/connect-excel-with-hive-ODBC/) und [Verbinden von Excel mit HDInsight über Power Query](/de-de/manage/services/hdinsight/connect-excel-with-power-query/).

**Abrufen der Ergebnisse**

1.  Öffnen Sie das Azure PowerShell-Fenster.
2.  Legen Sie die Werte fest, und führen Sie dann folgende Befehle aus:

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName = "<TheDataStorageAccountName>"
         $containerName = "<TheDataBlobStorageContainerName>"
         $blobName = "WordCount/Output/part-00000"

3.  Führen Sie die folgenden Befehle aus, um ein Azure-Speicherkontextobjekt zu erstellen:

         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  Führen Sie die folgenden Befehle aus, um die Ergebnisse herunterzuladen und anzuzeigen:

         Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
         cat "./$blobName" | findstr "there"

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie gelernt, wie ein Hadoop-Streaming-MapReduce-Job entwickelt wird, wie die Anwendung auf dem HDInsight Emulator getestet wird und wie ein PowerShell-Skript geschrieben wird, um ein HDInsight-Cluster bereitzustellen und den MapReduce-Job auf dem Cluster aufzuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Erste Schritte mit dem HDInsight Emulator](/de-de/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/manage/services/hdinsight/howto-blob-store/)
-   [Verwalten von HDInsight mit PowerShell](/de-de/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Hochladen von Daten zu HDInsight](/de-de/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/de-de/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Verwenden von Pig mit HDInsight](/de-de/manage/services/hdinsight/using-pig-with-hdinsight/)

