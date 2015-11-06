<properties
	pageTitle="Generieren von Empfehlungen mit Mahout und Windows-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Filmempfehlungen mit der Apache Mahout-Bibliothek für maschinelles Lernen und Windows-basiertem HDInsight (Hadoop) erstellen können."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="larryfr"/>

#Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit Hadoop in HDInsight

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Erfahren Sie, wie Sie Filmempfehlungen mit der [Apache Mahout](http://mahout.apache.org)-Bibliothek für maschinelles Lernen und Azure HDInsight erstellen können.

> [AZURE.NOTE]Die Schritte in diesem Dokument erfordern einen Windows-Client und einen Windows-basierten HDInsight-Cluster. Informationen zur Verwendung von Mahout mit einem Linux-, OS X- oder Unix-Client und einem Linux-basierten Cluster finden Sie unter [Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit Linux-basiertem Hadoop in HDInsight](hdinsight-hadoop-mahout-linux-mac.md).


##<a name="learn"></a>Sie lernen Folgendes

Mahout ist eine [Bibliothek für maschinelles Lernen][ml] für Apache Hadoop. Mahout enthält Algorithmen zur Verarbeitung von Daten wie etwa Filtern, Klassifizierung und Clustering. In diesem Artikel verwenden Sie ein Empfehlungsmodul zum Generieren von Filmempfehlungen auf der Grundlage von Filmen, die Ihre Freunde gesehen haben. Sie erfahren außerdem, wie Sie mithilfe eines Entscheidungswalds Klassifizierungen vornehmen. Dabei lernen Sie Folgendes:

* Ausführen von Mahout-Aufträgen mit Windows PowerShell

* Ausführen von Mahout-Aufträgen von der Hadoop-Befehlszeile aus

* Installieren von Mahout auf HDInsight 3.0- und HDInsight 2.0-Clustern

	> [AZURE.NOTE]Mahout wird mit HDInsight 3.1-Clustern bereitgestellt. Wenn Sie eine frühere Version von HDInsight verwenden, finden Sie unter [Installieren von Mahout](#install) entsprechende Informationen, bevor Sie fortfahren.

##Voraussetzungen

* **Ein Windows-basierter Hadoop-Cluster in HDInsight**. Weitere Informationen zur Erstellung finden Sie unter [Erste Schritte mit Hadoop in HDInsight][getstarted].

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/) (Installieren und Verwenden von Azure PowerShell, in englischer Sprache).


##<a name="recommendations"></a>Generieren von Empfehlungen mithilfe von PowerShell

> [AZURE.NOTE]Zwar kann der in diesem Abschnitt verwendete Auftrag mit Windows PowerShell ausgeführt werden, jedoch funktionieren viele der mit Mahout bereitgestellten Klassen derzeit nicht mit Windows PowerShell und müssen über die Hadoop-Befehlszeile ausgeführt werden. Eine Liste der Klassen, die nicht mit Windows PowerShell funktionieren, finden Sie im Abschnitt [Problembehandlung](#troubleshooting).
>
> Ein Beispiel für den Einsatz der Hadoop-Befehlszeile für das Ausführen von Mahout-Aufträgen finden Sie unter [Klassifizieren von Daten mit der Hadoop-Befehlszeile](#classify).

Eine der von Mahout bereitgestellten Funktionen ist ein Empfehlungsmodul. Dieses Modul akzeptiert Daten im Format `userID`, `itemId` und `prefValue` (Benutzervoreinstellung für das Element). Mahout kann dann eine Analyse des gemeinsamen Vorkommens durchführen: _Benutzer, die eine Vorliebe für ein bestimmtes Element haben, haben auch eine Vorliebe für andere Elemente_. Mahout bestimmt dann Benutzer mit ähnlichen Vorlieben für Elemente, aus denen sich Empfehlungen erstellen lassen.

Im Folgenden sehen Sie ein extrem einfaches Beispiel mit Spielfilmen:

* __Gemeinsames Vorkommen__: Joe, Alice und Bob gefällt _Krieg der Sterne_, _Das Imperium schlägt zurück_ und _Rückkehr der Jedi-Ritter_. Mahout stellt fest, dass Benutzer, denen einer dieser Filme gefällt, auch die beiden anderen mögen.

* __Gemeinsames Vorkommen__: Bob und Alice haben auch _Die dunkle Bedrohung_, _Angriff der Klonkrieger_ und _Die Rache der Sith_ gefallen. Mahout stellt fest, dass Benutzer, denen die vorherigen drei Filme gefallen, auch diese drei mögen.

* __Vergleichbare Empfehlung__: Da Joe die ersten drei Filme gefallen haben, sucht Mahout nach Filmen, die anderen Personen mit ähnlichen Vorlieben gefallen haben, die Joe aber noch nicht gesehen (mit "Gefällt mir" markiert oder bewertet) hat. In diesem Fall empfiehlt Mahout _Die dunkle Bedrohung_, _Angriff der Klonkrieger_ und _Die Rache der Sith_.

###Laden der Daten

Praktischerweise stellt [GroupLens Research][movielens] Bewertungsdaten für Filme in einem Mahout-kompatiblen Format zur Verfügung.

1. Laden Sie das Archiv [MovieLens 100k][100k] herunter, das 100.000 Bewertungen von 1.000 Benutzern zu 1.700 Filmen enthält.

2. Extrahieren Sie das Archiv. Es sollte ein Verzeichnis namens __ml-100k__ enthalten, das viele Dateien mit dem Präfix __u.__ enthält. Die Datei, die von Mahout analysiert wird, ist __u.data__. Die Datenstruktur dieser Datei ist `userID`, `movieID`, `userRating` und `timestamp`. Hier sehen Sie ein Beispiel für die Daten:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. Laden Sie die Datei __u.data__ in __example/data/u.data__ in Ihrem HDInsight-Cluster hoch. Der folgende Befehl verwendet PowerShell zum Hochladen der Datei. Andere Möglichkeiten zum Hochladen von Dateien finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight][upload].

        # Put your cluster name below
        $clusterName="Your HDInsight cluster name"
        # Put the path to the u.data file below
        $fileToUpload="The path to the u.data file"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/data/u.data" `
            -Container $container `
            -Context $context
    
    Dabei wird die Datei __u.data__ in __example/data/u.data__ im Standardspeicher Ihres Clusters hochgeladen. Sie können dann über den __wasb:///example/data/u.data__ URI von HDInsight-Aufträgen auf diese Daten zugreifen.

###Ausführen des Auftrags

Verwenden Sie das folgende Windows PowerShell-Skript zum Ausführen eines Auftrags mithilfe des Mahout-Empfehlungsmoduls mit der zuvor hochgeladenen Datei __u.data__.

	# The HDInsight cluster name.
	$clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	$jarFile = "file:///C:/apps/dist/mahout-0.9.0.2.1.15.1-1234/examples/target/mahout-examples-0.9.0.2.1.15.1-1234-job.jar"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///example/temp"

	# Create the job definition
	$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
	# Write out any error information
	Write-Host "STDERR"
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE]Mahout-Aufträge entfernen keine temporären Daten, die bei der Verarbeitung des Auftrags erstellt wurden. Der `--tempDir`-Parameter wird im Beispielauftrag festgelegt, sodass die temporären Dateien in einem spezifischen Pfad isoliert abgelegt werden.

Der Mahout-Auftrag gibt keine Ausgabe an STDOUT zurück. Stattdessen wird diese im angegebenen Ausgabeverzeichnis unter __part-r-00000__ gespeichert. Das Skript lädt diese Datei in __output.txt__ in das aktuelle Verzeichnis auf Ihrer Arbeitsstation herunter.

Im Folgenden sehen Sie ein Beispiel für den Inhalt der Datei:

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Die erste Spalte ist die `userID`. Die in "[" und "]" enthaltenen Werte sind `movieId`:`recommendationScore`.

###Anzeigen der Ausgabe

Obwohl die generierte Ausgabe in einer Anwendung verwendet werden kann, ist sie für Benutzer nicht gut lesbar. Einige der anderen Dateien, die im Vorfeld in den Ordner __ml-100k__ extrahiert wurden, können für die Auflösung von `movieId` in den Namen eines Films verwendet werden. Dies wird mit dem folgenden Skript erreicht:

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "u.data"
	        -movieFile "u.item"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

Um dieses Skript verwenden zu können, müssen Sie zuvor den Ordner __ml-100k__ extrahiert haben. Nachfolgend sehen Sie ein Beispiel für das Ausführen des Skripts:

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

Die Ausgabe sollte in etwa folgendermaßen aussehen:

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Klassifizieren von Daten mit der Hadoop-Befehlszeile

Eine der in Mahout verfügbaren Klassifizierungsmethoden besteht darin, einen [Random Forest][forest] zu erstellen. Das ist ein mehrstufiger Prozess, bei dem mithilfe von Trainingsdaten Entscheidungsstrukturen generiert werden, die dann zum Klassifizieren von Daten verwendet werden. Dabei wird die von Mahout bereitgestellte Klasse __org.apache.mahout.classifier.df.tools.Describe__ verwendet, die derzeit mit der Hadoop-Befehlszeile ausgeführt werden muss.

###Laden der Daten

1. Laden Sie folgende Dateien des [Datasets "NSL-KDD"](http://nsl.cs.unb.ca/NSL-KDD/) herunter.

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): die Trainingsdatei

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): die Testdatei

2. Öffnen Sie jede Datei und entfernen Sie die Zeilen oben, die mit '@' beginnen. Speichern Sie dann die Dateien. Werden sie nicht entfernt, erhalten Sie Fehlermeldungen, wenn Sie die Daten mit Mahout verwenden.

2. Laden Sie die Dateien in __example/data__ hoch. Sie können einen Container mit dem folgenden Skript erstellen. Ersetzen Sie __CLUSTERNAME__ durch den Namen des HDInsight-Clusters. Ersetzen Sie „FILENAME“ durch den Namen der hochzuladenden Datei.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###Ausführen des Auftrags

1. Dieser Auftrag erfordert die Hadoop-Befehlszeile. Aktivieren Sie Remotedesktop für den HDInsight-Cluster, und stellen Sie anschließend mithilfe der Anweisungen unter [Verbinden mit HDInsight-Clustern über RDP](hdinsight-administer-use-management-portal.md#rdp) eine Verbindung her.

3. Klicken Sie nach dem Herstellen der Verbindung auf das Symbol __Hadoop-Befehlszeile__, um die Hadoop-Befehlszeile öffnen:

	![hadoop cli][hadoopcli]

3. Verwenden Sie den folgenden Befehl zum Erstellen des Dateideskriptors (__KDDTrain+.info__) mithilfe von Mahout.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L` beschreibt die Attribute der Daten in der Datei. "L" gibt z. B. eine Bezeichnung an.

4. Erstellen Sie mit dem folgenden Befehl einen Entscheidungswald aus Entscheidungsstrukturen:

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Die Ausgabe dieses Vorgangs wird im Verzeichnis __nsl-forest__ gespeichert, das sich im Speicher Ihres HDInsight-Clusters unter __wasb://user/&lt;username>/nsl-forest/nsl-forest.seq befindet. &lt;username> ist der für die Remotedesktopsitzung verwendete Benutzername. Die Datei ist für Benutzer nicht lesbar.

5. Testen Sie den Entscheidungswald durch Klassifizieren des __KDDTest+.arff__-Datasets. Verwenden Sie den folgenden Befehl:

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    Die Ausgabe dieses Befehls besteht aus zusammenfassenden Informationen zum Klassifizierungsprozess und sieht etwa so aus:

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  Dieser Auftrag generiert auch eine Datei im Verzeichnis __wasb:///example/data/predictions/KDDTest+.arff.out__. Diese Datei ist jedoch für Benutzer nicht lesbar.

> [AZURE.NOTE]Mahout-Aufträge überschreiben keine Dateien. Wenn Sie diese Aufträge noch einmal ausführen möchten, müssen Sie die von vorherigen Aufträgen erstellten Dateien löschen.

##<a name="troubleshooting"></a>Problembehandlung

###<a name="install"></a>Installieren von Mahout

Mahout ist in HDInsight 3.1-Clustern bereits installiert. Die Installation kann mit folgenden Schritten manuell in HDInsight 3.0- oder HDInsight 2.1-Clustern durchgeführt werden:

1. Die zu verwendende Version von Mahout hängt von der HDInsight-Version auf Ihrem Cluster ab. Die Clusterversion finden Sie heraus, wenn Sie den folgenden [Azure PowerShell][aps]-Befehl verwenden:

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * __Für HDInsight 2.1__ können Sie eine JAR-Datei (Java Archive) herunterladen, die [Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar) enthält.

  * __Für HDInsight 3.0__ müssen Sie [Mahout aus dem Quellcode erstellen][build] und die von HDInsight bereitgestellte Hadoop-Version angeben. Installieren Sie die auf der Buildseite aufgeführten erforderlichen Komponenten, laden Sie den Quellcode herunter, und verwenden Sie dann den folgenden Befehl zum Erstellen der Mahout-JAR-Dateien.

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	After the build completes, you can find the JAR file at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

    	> [AZURE.NOTE] Wenn Mahout 1.0 veröffentlicht wird, sollten Sie die vorgefertigten Pakete mit HDInsight 3.0 verwenden können.

2. Laden Sie die jar-Datei hoch in __example/jars__ in den Standardspeicher Ihres Clusters. Ersetzen Sie „CLUSTERNAME“ im folgenden Skript durch den Namen des HDInsight-Clusters, und ersetzen Sie „FILENAME“ durch den Pfad zur __mahout-coure-0.9-job.jar__-Datei.

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###Dateien können nicht überschrieben werden

Mahout-Aufträge bereinigen keine temporären Dateien, die während der Verarbeitung erstellt wurden. Außerdem überschreiben Aufträge keine vorhandenen Ausgabedateien.

Um Fehler beim Ausführen von Mahout-Aufträgen zu vermeiden, löschen Sie temporäre und Ausgabedateien zwischen dem Ausführen der Aufträge, oder verwenden Sie eindeutige Namen für temporäre und Ausgabeverzeichnisse.

###JAR-Datei kann nicht gefunden werden

Mahout ist in HDInsight 3.1-Clustern enthalten. Der Pfad- und der Dateiname enthalten die Versionsnummer der im Cluster installierten Mahout-Version. Das Windows PowerShell-Beispielskript in diesem Lernprogramm verwendet einen Pfad, der seit Juli 2014 gültig ist. Die Versionsnummer wird sich aber mit künftigen Updates von HDInsight ändern. Den aktuellen Pfad zur Mahout-JAR-Datei für Ihren Cluster können Sie mit dem folgenden Windows PowerShell-Befehl ermitteln. Ändern Sie dann das Skript, um den ausgegebenen Pfad zu referenzieren.

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar'

###<a name="nopowershell"></a>Klassen, die nicht mit Windows PowerShell funktionieren

Mahout-Aufträge, die die folgenden Klassen verwenden, geben zahlreiche Fehlermeldungen zurück, wenn sie von Windows PowerShell aus verwendet werden.

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Wenn Sie Aufträge mit diesen Klassen ausführen möchten, stellen Sie eine Verbindung zum HDInsight-Cluster her, und führen Sie die Aufträge mit der Hadoop-Befehlszeile aus. Ein Beispiel finden Sie unter [Klassifizieren von Daten mit der Hadoop-Befehlszeile](#classify).

##Nächste Schritte

Nachdem Sie sich mit Mahout vertraut gemacht haben, können Sie sich anderen Methoden der Datenverarbeitung in HDInsight zuwenden:

* [Hive mit HDInsight](../hadoop-use-hive.md)
* [Pig mit HDInsight](../hadoop-use-pig.md)
* [MapReduce mit HDInsight](../hadoop-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: ../hdinsight-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!----HONumber=Oct15_HO4-->