
<properties
	pageTitle="Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie MapReduce-Programme für Hadoop-Streaming in C# entwickeln und in Azure HDInsight bereitstellen können."
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
	ms.date="01/28/2016"
	ms.author="jgao"/>



# Entwickeln von C# Hadoop-Streamingprogrammen für HDInsight

Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. Dieses Lernprogramm begleitet Sie durch die Erstellung eines C#-Wortzählprogramms, mit dem Sie die Häufigkeit bestimmter Wörter in den angegebenen Eingangsdaten zählen. Die folgende Abbildung veranschaulicht die Wortzählung im MapReduce-Framework:

![HDI.Wortzähldiagramm][image-hdi-wordcountdiagram]

> [AZURE.NOTE] Die Schritte in diesem Artikel gelten nur für Windows-basierte Azure HDInsight-Cluster. Ein Beispiel für das Streaming bei Linux-basiertem HDInsight finden Sie unter [Entwickeln von Python-Streamingprogrammen für HDInsight](hdinsight-hadoop-streaming-python.md).

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

- Entwickeln eines Hadoop-Streaming-MapReduce-Programms in C# 
- Ausführen des MapReduce-Jobs auf Azure HDInsight
- Abrufen der Ergebnisse des MapReduce-Jobs

###Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, benötigten Sie Folgendes:

- Eine Arbeitsstation mit [Azure PowerShell][powershell-install] und [Microsoft Visual Studio](https://www.visualstudio.com/).
- Schließen Sie ein Azure-Abonnement ab. Informationen dazu finden Sie unter [Erste Schritte mit Azure][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [Kostenlose einmonatige Testversion][azure-free-trial].


##Entwickeln eines Hadoop-Streamingprogramms zum Zählen von Wörtern in C&#35;

Diese Wortzähllösung besteht aus zwei Konsolenanwendungsprojekten: Mapper und Reducer. Die Anwendung "Mapper" streamt jedes Wort in die Konsole, und die Anwendung "Reducer" zählt die Wörter, die aus einem Dokument gestreamt werden. Sowohl Mapper als auch Reducer lesen Zeichen Zeile für Zeile aus dem Standard-Eingabedatenstrom (stdin) und schreiben in den Standard-Ausgabedatenstrom (stdout).

**So erstellen Sie das Mapper-Programm**

1. Öffnen Sie Visual Studio, und erstellen Sie eine neue C#-Konsolenanwendung mit dem Namen **WordCountMapper**.
2. Benennen Sie im Projektmappen-Explorer **Program.cs** in **WordCountMapper.cs** um. Klicken Sie auf **Ja**, um die Umbenennung aller Verweise zu bestätigen.
3. Ersetzen Sie den Code in „WordCountMapper.cs“ durch den folgenden Code:

        using System;
        using System.IO;

        namespace WordCountMapper
        {
            class WordCountMapper
            {
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
            }
        }

4. Erstellen Sie die Projektmappe, und stellen Sie sicher, dass keine Kompilierungsfehler vorhanden sind.

**So erstellen Sie das Reducer-Programm**

1. Fügen Sie der Projektmappe eine weitere C#-Konsolenanwendung namens **WordCountReducer** hinzu. Speicherort: C:\\Tutorials\\WordCount
2. Benennen Sie im Projektmappen-Explorer **Program.cs** in **WordCountReducer.cs** um. Klicken Sie auf **Ja**, um die Umbenennung aller Verweise zu bestätigen.
3. Ersetzen Sie den Code in „WordCountReducer.cs“ durch den folgenden Code:

        using System;
        using System.IO;

        namespace WordCountReducer
        {
            class WordCountReducer
            {
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
                            if (lastWord != null)
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
            }
        }

4. Erstellen Sie die Projektmappe, und stellen Sie sicher, dass keine Kompilierungsfehler vorhanden sind.

Sie erhalten die ausführbaren Dateien für Mapper und Reducer:

- ..\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
- ..\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe


##Hochladen von Daten in Azure-Blobspeicher
Azure HDInsight verwendet Azure-Blobspeicher als Standarddateisystem. Sie können ein HDInsight-Cluster für die Verwendung von zusätzlichem Blobspeicher für die Datendateien verwenden. In diesem Abschnitt erstellen Sie ein Azure-Speicherkonto und laden die Datendateien in den Blobspeicher hoch. Als Datendateien werden die .txt-Dateien im Verzeichnis %hadoop\_home%\\share\\doc\\hadoop\\common verwendet.


**So erstellen Sie einen Blobspeicher und Container**

1. Öffnen Sie Azure PowerShell.
2. Legen Sie die Variablen fest, und führen Sie dann folgende Befehle aus:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Führen Sie den folgenden Befehl aus, um ein Speicherkonto und einen Blobspeichercontainer für dieses Konto zu erstellen:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Führen Sie die folgenden Befehle aus, um das Speicherkonto und den Container zu überprüfen:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**So laden Sie die Datendateien hoch**

1. Legen Sie die Werte für den lokalen Ordner und die Zielordner im Azure PowerShell-Fenster fest:

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Beachten Sie: Der lokale Quellordner ist **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common**, und der Zielordner ist **WordCount/Input**. Der Quellordner ist der Speicherort der .txt-Dateien im HDInsight-Emulator. Als Ziel wird die Ordnerstruktur verwendet, die im Azure-Blobcontainer abgebildet wird.

3. Führen Sie die folgenden Befehle aus, um eine Liste der .txt-Dateien im Quellordner abzurufen:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

5. Führen Sie das folgende Skript aus, um die Dateien zu kopieren:

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**So laden Sie die Wortzählanwendungen hoch**

1. Setzen Sie die folgenden Variablen im Azure PowerShell-Fenster:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Als Zielordner wird **WordCount/Apps** verwendet. Dieser Ordner enthält die Struktur, die im Azure-Blobcontainer abgebildet wird.

2. Führen Sie die folgenden Befehle aus, um die Anwendungen zu kopieren:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Führen Sie den folgenden Befehl aus, um die hochgeladenen Dateien aufzulisten:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Dort sollten beide Anwendungsdateien aufgelistet sein.


##Ausführen des MapReduce-Jobs auf Azure HDInsight

Dieser Abschnitt enthält ein Azure PowerShell-Skript, das alle zur Ausführung eines MapReduce-Auftrags benötigten Aufgaben enthält. Diese Aufgabenliste umfasst Folgendes:

1. Bereitstellen eines HDInsight-Clusters

	1. Erstellen eines Speicherkontos, das als Standarddateisystem für den HDInsight-Cluster verwendet wird
	2. Erstellen eines Blobspeicher-Containers
	3. Erstellen eines HDInsight-Clusters

2. Übermitteln des MapReduce-Jobs

	1. Erstellen einer Streaming-MapReduce-Jobdefinition
	2. Übermitteln eines MapReduce-Jobs
	3. Warten auf den Abschluss des Auftrags
	4. Anzeigen von Standardfehlern
	5. Anzeigen der Standardausgabe

3. Löschen des Clusters

	1. Löschen des HDInsight-Clusters
	2. Löschen des Speicherkontos, das als Standarddateisystem für den HDInsight-Cluster verwendet wurde


**So führen Sie das Azure PowerShell-Skript aus**

1. Öffnen Sie Notepad.
2. Kopieren und fügen Sie den folgenden Code ein:

		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### Prefix to cluster, Storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data storage account location
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

3. Legen Sie die ersten vier Variablen in dem Skript fest. Die Variable **$stringPrefix** wird den angegebenen Zeichenfolgen für die Namen des HDInsight-Clusters, Speicherkontos und Blobspeichercontainers vorangestellt. Da diese Namen zwischen 3 und 24 Zeichen enthalten müssen, müssen Sie sicherstellen, dass die angegebene Zeichenfolge und die im Skript verwendeten Namen zusammen diese Längeneinschränkung nicht überschreiten. Verwenden Sie für **$stringPrefix** nur Kleinbuchstaben.

	Die Variablen **$storageAccountName\_Data** und **$containerName\_Data** sind das Speicherkonto und der Container, das/den Sie in den vorigen Schritten erstellt haben. Geben Sie die entsprechenden Namen an. Diese Komponenten werden zur Speicherung von Datendateien und Anwendungen verwendet. Die Variable **$location** muss mit dem Speicherort des Datenspeicherkontos übereinstimmen.

4. Prüfen Sie auch die restlichen Variablen.
5. Speichern Sie die Skript-Datei.
6. Öffnen Sie Azure PowerShell.
7. Führen Sie den folgenden Befehl aus, um die Ausführungsrichtlinie auf "RemoteSigned" festzulegen:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Wenn Sie dazu aufgefordert werden, geben Sie den Benutzernamen und das Kennwort für den HDInsight-Cluster ein. Das Kennwort muss mindestens 10 Zeichen lang sein und mindestens eine Ziffer, einen Groß- und Kleinbuchstaben sowie ein Sonderzeichen enthalten. Wenn Sie die Eingabe der Anmeldeinformationen überspringen möchten, siehe [Arbeiten mit Kennwörtern, sicheren Zeichenfolgen und Anmeldeinformationen in Windows PowerShell][powershell-PSCredential].

Ein Beispiel für die Übermittlung von Hadoop-Streamingaufträgen mit dem HDInsight .NET SDK finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs].


##Abrufen der Ergebnisse des MapReduce-Jobs
In diesem Abschnitt wird erläutert, wie Sie die Ergebnisse herunterladen und anzeigen. Informationen zur Anzeige der Ergebnisse in Excel finden Sie unter [Verbinden von Excel mit HDInsight über den Microsoft Hive-ODBC-Treiber][hdinsight-ODBC] und [Verbinden von Excel mit HDInsight über Power Query][hdinsight-power-query].


**So rufen Sie die Ausgabe ab**

1. Öffnen Sie das Azure PowerShell-Fenster.
2. Legen Sie die Werte fest, und führen Sie dann folgende Befehle aus:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"

3. Führen Sie folgende Befehle aus, um ein Azure-Speicherkontextobjekt zu erstellen:

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. Führen Sie die folgenden Befehle aus, um die Ergebnisse herunterzuladen und anzuzeigen:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"



##Nächste Schritte
In diesem Lernprogramm haben Sie gelernt, wie ein Hadoop-Streaming-MapReduce-Auftrag entwickelt wird, wie die Anwendung mit dem HDInsight-Emulator getestet wird und wie ein Azure PowerShell-Skript geschrieben wird, um einen HDInsight-Cluster bereitzustellen und den MapReduce-Auftrag im Cluster aufzuführen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Erste Schritte mit dem HDInsight-Emulator][hdinsight-get-started-emulator]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]
- [Verwenden von Azure-Blobspeicher mit HDInsight][hdinsight-storage]
- [Verwalten von HDInsight mit Azure PowerShell][hdinsight-admin-powershell]
- [Hochladen von Daten zu HDInsight][hdinsight-upload-data]
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "Anwendungsfluss der MapReduce-Wortzählung"

<!---HONumber=AcomDC_0218_2016-->