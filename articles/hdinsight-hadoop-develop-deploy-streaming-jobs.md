<properties urlDisplayName="" pageTitle="Entwickeln von C# Hadoop-Streamingprogrammen f&uuml;r HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Erfahren Sie, wie Sie MapReduce-Programme f&uuml;r Hadoop-Streaming in C# entwickeln und in Azure HDInsight bereitstellen k&ouml;nnen." metaCanonical="" services="hdinsight" documentationCenter="" title="Entwickeln von C# Hadoop-Streamingprogrammen f&uuml;r HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight

Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. Dieses Lernprogramm behandelt ein Wortzählungs-Szenario, in dem Sie die Häufigkeit bestimmter Wörter in den angegebenen Eingangsdaten zählen. Die folgende Abbildung beschreibt die Wortzählung im MapReduce-Framework.

![HDI.WordCountDiagram][HDI.WordCountDiagram]

Dieses Thema beschreibt Folgendes:

-   Entwickeln und Testen eines Hadoop-Streaming-MapReduce-Programms mit C# im HDInsight-Emulator
-   Ausführen desselben MapReduce-Jobs in Azure HDInsight
-   Abrufen der Ergebnisse des MapReduce-Jobs

**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen, benötigten Sie Folgendes:

-   Installieren Sie den Azure HDInsight Emulator. Informationen dazu finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][Erste Schritte mit dem HDInsight-Emulator].
-   Installieren Sie Azure PowerShell auf dem Emulator-Computer. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].
-   Schließen Sie ein Azure-Abonnement ab. Informationen dazu finden Sie unter [Erste Schritte mit Azure][Erste Schritte mit Azure], [Spezielle Angebote][Spezielle Angebote] oder [Kostenlose einmonatige Testversion][Kostenlose einmonatige Testversion].

## Themen in diesem Artikel

-   [Entwickeln eines Hadoop-Streaming-Wortzählprogramms in C#][Entwickeln eines Hadoop-Streaming-Wortzählprogramms in C#]
-   [Testen des Programms auf dem Emulator][Testen des Programms auf dem Emulator]
-   [Hochladen von Daten und Anwendungen in den Azure-Blob-Speicher][Hochladen von Daten und Anwendungen in den Azure-Blob-Speicher]
-   [Ausführen des MapReduce-Programms auf Azure HDInsight][Ausführen des MapReduce-Programms auf Azure HDInsight]
-   [Abrufen der MapReduce-Ergebnisse][Abrufen der MapReduce-Ergebnisse]
-   [Nächste Schritte][Nächste Schritte]

## <a name="develop"></a>Entwickeln eines Hadoop-Streaming-Wortzählprogramms in C#

Diese Wortzählungslösung besteht aus zwei Konsolenanwendungsprojekten: Mapper und Reducer. Die Anwendung "Mapper" streamt jedes Wort in die Konsole, und die Anwendung "Reducer" zählt die Wörter, die aus einem Dokument gestreamt werden. Sowohl der Mapper als auch der Reducer lesen Zeichen (zeilenweise) aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout).

**So erstellen Sie eine C#-Konsolenanwendung**

1.  Öffnen Sie Visual Studio 2013.
2.  Klicken Sie auf **DATEI**, auf **Neu** und anschließend auf **Projekt**.
3.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    |-------------------|--------------------------------------|
    | Feld              | Wert                                 |
    | Vorlage           | Visual C#/Windows-Konsolenanwendung |
    | Name              | WordCountMapper                      |
    | Verzeichnis       | C:\\Tutorials                        |
    | Projektmappenname | Wortzählung                          |

4.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

**So erstellen Sie das Mapper-Programm**

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

**So erstellen Sie das Reducer-Programm**

1.  Klicken Sie in Visual Studio 2013 auf **DATEI**, auf **Hinzufügen** und auf **Neues Projekt**.
2.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    |-------------|--------------------------------------|
    | Feld        | Wert                                 |
    | Vorlage     | Visual C#/Windows-Konsolenanwendung |
    | Name        | WordCountReducer                     |
    | Verzeichnis | C:\\Tutorials\\WordCount             |

3.  Deaktivieren Sie das Kontrollkästchen **Projektmappenverzeichnis erstellen** und klicken Sie auf **OK**, um das Projekt zu erstellen.
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

10. Klicken Sie auf **BUILD** und anschließend auf **Projektmappe erstellen**, um das Reducer-Programm zu kompilieren.

Die ausführbaren Dateien für den Mapper und den Reducer haben folgende Pfade:

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>Testen des Programms auf dem Emulator

Führen Sie die folgenden Schritte aus, um das Programm im Emulator zu testen:

1.  Laden Sie die Daten in das Emulator-HDFS hoch
2.  Laden Sie die Mapper und Reducer in das Emulator-HDFS hoch
3.  Übermitteln Sie einen Wortzählungs-MapReduce-Job
4.  Überprüfen Sie den Jobstatus
5.  Rufen Sie die Jobergebnisse ab

Standardmäßig verwendet der HDInsight Emulator als Dateisystem HDFS. Optional können Sie angeben, dass der HDInsight-Emulator einen Azure-Blob-Speicher verwenden soll. Informationen dazu finden Sie unter [Erste Schritte mit dem HDInsight-Emulator][1]. In diesem Abschnitt verwenden Sie den HDFS-Befehl *copyFromLocal*, um die Dateien hochzuladen. Im nächsten Abschnitt wird gezeigt, wie Sie Dateien mit Azure PowerShell hochladen. Weitere Methoden finden Sie unter [Hochladen von Daten zu HDInsight][Hochladen von Daten zu HDInsight].

In diesem Lernprogramm wird die folgende Ordnerstruktur verwendet:

|-----------------------------|---------------------------------------------------|
| Ordner                      | Hinweis                                           |
| \\WordCount                 | Der Stammordner für das Wortzählungsprojekt.      |
| \\WordCount\\Apps           | Der Ordner für die Mapper- und Reducer-Programme. |
| \\WordCount\\Input          | Der Ordner für die MapReduce-Quelldateien.        |
| \\WordCount\\Output         | Der Ordner für die MapReduce-Ausgabedateien.      |
| \\WordCount\\MRStatusOutput | Der Ordner für die Jobergebnisse.                 |

</br>

In diesem Lernprogramm werden die .txt-Dateien im Verzeichnis %hadoop\_home% verwendet.

> [WACOM.NOTE] Bei den Hadoop-HDFS-Befehlen muss die Groß- und Kleinschreibung beachtet werden.

**So kopieren Sie die Textdateien in das Emulator-HDFS**

1.  Führen Sie im Hadoop-Befehlszeilenfenster den folgenden Befehl aus, um ein Verzeichnis für die Eingabedateien anzulegen:

        hadoop fs -mkdir /WordCount/
        hadoop fs -mkdir /WordCount/Input

    Der hier verwendete Pfad ist der relative Pfad. Er ist gleichwertig mit folgendem Pfad:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  Führen Sie den folgenden Befehl aus, um einige Textdateien in den Eingabeordner im HDFS zu kopieren:

        hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common\*.txt \WordCount\Input

3.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

        hadoop fs -ls \WordCount\Input

**So stellen Sie den Mapper und den Reducer im Emulator-HDFS bereit**

1.  Öffnen Sie die Hadoop-Befehlszeile auf Ihrem Desktop und erstellen Sie das Verzeichnis /Apps in HDFS
     hadoop fs -mkdir /WordCount/Apps

2.  Führen Sie die folgenden Befehle aus:

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

        hadoop fs -ls /WordCount/Apps

    Sie sollten die zwei .exe-Dateien sehen.

**So führen Sie den MapReduce-Job mit HDInsight PowerShell aus**

1.  Öffnen Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].
2.  Führen Sie die folgenden Befehle aus, um die Variablen festzulegen:

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    Der Name des HDInsight-Clusteremulators lautet "<http://localhost:50111>".

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

    Sie sehen die Job-ID in der Ausgabe, z. B. *job-201311132317-0034*.

**So prüfen Sie den Jobstatus**

1.  Klicken Sie auf dem Desktop auf **Hadoop YARN-Status** oder öffnen Sie die Adresse **<http://localhost:50030/jobtracker.jsp>** in einem Browser.
2.  Suchen Sie anhand der Job-ID nach dem Job in den Kategorien **RUNNING** bzw. **FINISHED**.
3.  Fehlgeschlagene Jobs finden Sie in der Kategorie **FAILED**. Außerdem können Sie die Jobdetails öffnen, um hilfreiche Debugging-Informationen zu erhalten.

**So zeigen Sie die Ergebnisse im HDFS an**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Führen Sie den folgenden Befehl aus, um die Ergebnisse anzuzeigen:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    Sie können auch "|more" an das Ende der Befehlszeile anhängen, um eine Seitenansicht zu öffnen.

## <span id="upload"></span></a>Hochladen von Daten in Azure-Blob-Speicher

Azure HDInsight verwendet Azure-Blob-Speicher als Standarddateisystem. Sie können einen HDInsight-Cluster für die Verwendung von zusätzlichem Blob-Speicher für die Datendateien verwenden. In diesem Abschnitt erstellen Sie ein Speicherkonto und laden die Datendateien in den Blob-Speicher hoch. Als Datendateien werden die .txt-Dateien im Verzeichnis %hadoop\_home%\\share\\doc\\hadoop\\common verwendet.

**So erstellen Sie einen Blob-Speicher und einen Container**

1.  Öffnen Sie Azure PowerShell.
2.  Legen Sie die Variablen fest, und führen Sie dann folgende Befehle aus:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  Führen Sie den folgenden Befehl aus, um ein Speicherkonto und einen Blob-Speichercontainer auf diesem Konto zu erstellen.

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  Führen Sie die folgenden Befehle aus, um das Speicherkonto und den Container zu prüfen:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**So laden Sie die Datendateien hoch**

1.  Setzen Sie die Werte für de lokalen Ordner und die Zielordner im Azure PowerShell-Fenster.

        $localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
        $destFolder = "WordCount/Input"

    Beachten Sie: Der lokale Quellordner ist **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common**, und der Zielordner ist **WordCount/Input**. Der Quellordner ist der Speicherort der .txt-Dateien im HDInsight-Emulator. Als Ziel wird die Ordnerstruktur verwendet, die im Azure-Blob-Container abgebildet wird.

2.  Führen Sie die folgenden Befehle aus, um eine Liste der .txt-Dateien im Quellordner abzurufen:

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

3.  Führen Sie das folgende Skript aus, um die Dateien zu kopieren:

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

4.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**So laden Sie die Wortzählanwendungen hoch**

1.  Setzen Sie die folgenden Variablen im Azure PowerShell-Fenster:

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    Als Zielordner wird **WordCount/Apps** verwendet. Dieser Ordner enthält die Struktur, die im Azure Blob-Container abgebildet wird.

2.  Führen Sie die folgenden Befehle aus, um die Anwendungen zu kopieren:

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3.  Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    Dort sollten beide Anwendungsdateien aufgelistet sein.

## <a name="run"></a>Ausführen des MapReduce-Jobs auf Azure HDInsight

Dieser Abschnitt enthält ein PowerShell-Skript, das alle zur Ausführung eines MapReduce-Jobs benötigten Aufgaben enthält. Diese Aufgabenliste umfasst Folgendes:

1.  Bereitstellen eines HDInsight-Clusters

    1.  Erstellen eines Speicherkontos, das als Standard-Dateisystem für das HDInsight-Cluster verwendet wird
    2.  Erstellen eines Blobspeicher-Containers
    3.  Erstellen eines HDInsight-Clusters

2.  Übermitteln des MapReduce-Jobs

    1.  Erstellen einer Streaming-MapReduce-Jobdefinition
    2.  Übermitteln eines MapReduce-Jobs
    3.  Auf Abschluss des Jobs warten
    4.  Anzeigen von Standardfehlern
    5.  Anzeigen der Standardausgabe

3.  Löschen des Clusters

    1.  Löschen des HDInsight-Clusters
    2.  Löschen des Speicherkontos, das als Standard-Dateisystem für das HDInsight-Cluster verwendet wurde

**So führen Sie das PowerShell-Skript aus**

1.  Öffnen Sie den Editor.
2.  Kopieren und fügen Sie den folgenden Code ein:

        # ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
        $subscriptionName = "<AzureSubscriptionName>"
        $stringPrefix = "<StringForPrefix>"     ### prefix to cluster, storage account, and container names
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $stringPrefix + "hdicluster"

        $storageAccountName_Default = $stringPrefix + "hdistore"
        $containerName_Default =  $stringPrefix + "hdicluster"

        # ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #====== CREATE A STORAGE ACCOUNT ======
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #====== CREATE A BLOB STORAGE CONTAINER ======
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #====== CREATE AN HDINSIGHT CLUSTER ======
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        Select-AzureSubscription $subscriptionName
        New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

        #====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #====== RUN A STREAMING MAPREDUCE JOB ======
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        Select-AzureSubscription $subscriptionName
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

        #====== DELETE THE HDINSIGHT CLUSTER ======
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Select-AzureSubscription $subscriptionName
        Remove-AzureHDInsightCluster -Name $clusterName 

        #====== DELETE THE STORAGE ACCOUNT ======
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Legen Sie die ersten vier Variablen in dem Skript fest. **$stringPrefix** wird den angegebenen Zeichenfolgen für die Namen von HDInsight-Cluster, Speicherkonto und Blobspeicher-Container vorangestellt. Da diese Namen zwischen 3 und 24 Zeichen lang sein dürfen, müssen Sie sicherstellen, dass die angegebene Zeichenfolge und die im Skript verwendeten Namen diese Längeneinschränkung nicht überschreiten. Verwenden Sie für $stringPrefix ausschließlich Kleinbuchstaben.

    **$storageAccountName\_Data** und **$containerName\_Data** sind das Speicherkonto und der Container, die Sie in den vorigen Schritten erstellt haben. Geben Sie die entsprechenden Namen an. Diese Komponenten werden zur Speicherung von Datendateien und Anwendungen verwendet. $location muss mit dem Speicherort des Datenspeicherkontos übereinstimmen.

4.  Prüfen Sie auch die restlichen Variablen.
5.  Speichern Sie die Skript-Datei.
6.  Öffnen Sie Azure PowerShell.
7.  Führen Sie den folgenden Befehl aus, um die Ausführungsrichtlinie auf "RemoteSigned" zu setzen:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Wenn Sie dazu aufgefordert werden, geben Sie den Benutzernamen und das Kennwort für das HDInsight-Cluster ein. Das Kennwort muss mindestens 10 Zeichen lang sein und mindestens eine Ziffer, einen Groß- und einen Kleinbuchstaben sowie ein Sonderzeichen enthalten. Wenn Sie die Eingabe der Anmeldeinformationen überspringen möchten, siehe [Working with Passwords, Secure Strings and Credentials in Windows PowerShell (Arbeiten mit Kennwörtern, sicheren Zeichenfolgen und Anmeldeinformationen in Windows PowerShell, in englischer Sprache)][Working with Passwords, Secure Strings and Credentials in Windows PowerShell (Arbeiten mit Kennwörtern, sicheren Zeichenfolgen und Anmeldeinformationen in Windows PowerShell, in englischer Sprache)]

Ein Beispiel für die Übermittlung von Hadoop-Streamingaufträgen mit dem Hadoop .NET SDK finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][Programmgesteuerte Übermittlung von Hadoop-Aufträgen].

## <a name="retrieve"></a>Abrufen der Ergebnisse des MapReduce-Jobs

In diesem Abschnitt wird erläutert, wie Sie die Ergebnisse herunterladen und anzeigen. Informationen zur Anzeige der Ergebnisse in Excel finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight] und [Verbinden von Excel mit HDInsight über Power Query][Verbinden von Excel mit HDInsight über Power Query].

**So rufen Sie die Ergebnisse ab**

1.  Öffnen Sie das Azure PowerShell-Fenster.
2.  Legen Sie die Werte fest, und führen Sie dann folgende Befehle aus:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  Führen Sie folgende Befehle aus, um ein Azure-Speicherkonto-Kontextobjekt zu erstellen:

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  Führen Sie die folgenden Befehle aus, um die Ergebnisse herunterzuladen und anzuzeigen:

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, wie ein Hadoop-Streaming-MapReduce-Job entwickelt wird, wie die Anwendung auf dem HDInsight Emulator getestet wird und wie ein PowerShell-Skript geschrieben wird, um einen HDInsight-Cluster bereitzustellen und den MapReduce-Job auf dem Cluster aufzuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]
-   [Erste Schritte mit dem HDInsight-Emulator][Erste Schritte mit dem HDInsight-Emulator]
-   [Entwickeln von Java MapReduce-Programmen für HDInsight][Entwickeln von Java MapReduce-Programmen für HDInsight]
-   [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight]
-   [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten zu HDInsight]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]

  [HDI.WordCountDiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "Anwendungsfluss der MapReduce-Wortzählung"
  [Erste Schritte mit dem HDInsight-Emulator]: ../hdinsight-get-started-emulator/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Erste Schritte mit Azure]: http://azure.microsoft.com/de-de/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/de-de/pricing/member-offers/
  [Kostenlose einmonatige Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Entwickeln eines Hadoop-Streaming-Wortzählprogramms in C#]: #develop
  [Testen des Programms auf dem Emulator]: #test
  [Hochladen von Daten und Anwendungen in den Azure-Blob-Speicher]: #upload
  [Ausführen des MapReduce-Programms auf Azure HDInsight]: #run
  [Abrufen der MapReduce-Ergebnisse]: #retrieve
  [Nächste Schritte]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Hochladen von Daten zu HDInsight]: ../hdinsight-upload-data/
  [Working with Passwords, Secure Strings and Credentials in Windows PowerShell (Arbeiten mit Kennwörtern, sicheren Zeichenfolgen und Anmeldeinformationen in Windows PowerShell, in englischer Sprache)]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Programmgesteuerte Übermittlung von Hadoop-Aufträgen]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Verbinden von Excel mit HDInsight über Power Query]: ../hdinsight-connect-excel-power-query/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Entwickeln von Java MapReduce-Programmen für HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Verwalten von HDInsight mit PowerShell]: ../hdinsight-administer-use-powershell/
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
