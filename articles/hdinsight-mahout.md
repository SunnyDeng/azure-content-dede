<properties title="Generate movie recommendations using Mahout" pageTitle="Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit HDInsight (Hadoop)" description="Erfahren Sie, wie Sie Sensordaten mithilfe der Apache Mahout-Bibliothek für maschinelles Lernen und HDInsight (Hadoop) Filmempfehlungen erstellen." metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

#Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit HDInsight (Hadoop)

Erfahren Sie, wie Sie Sensordaten mithilfe der [Apache Mahout](http://mahout.apache.org) -Bibliothek für maschinelles Lernen und Microsoft Azure HDInsight (Hadoop) Filmempfehlungen erstellen.

> [WACOM.NOTE] Sie benötigen zur Umsetzung der Informationen in diesem Artikel einen HDInsight-Cluster. Weitere Informationen dazu finden Sie unter [Erste Schritte mit Hadoop in HDInsight][getstarted].
>
> Mahout wird mit HDInsight 3.1-Clustern bereitgestellt. Wenn Sie eine frühere Version von HDInsight verwenden, sehen Sie sich [Installieren von Mahout] an,(#install) ehe Sie fortfahren.

##<a name="learn"></a>Sie lernen Folgendes

Mahout ist eine [Bibliothek für maschinelles Lernen][ml] für Apache Hadoop. Mahout enthält Algorithmen zur Verarbeitung von Daten wie etwa Filtern, Klassifizierung und Clustering. In diesem Artikel verwenden Sie ein Empfehlungsmodul zum Generieren von Filmempfehlung auf Grundlage von Filmen, die Freunde gesehen haben. Sie erfahren außerdem, wie Sie mithilfe eines Decision Forests Klassifizierungen vornehmen. Dabei lernen Sie Folgendes:

* Ausführen von Mahout-Aufträgen von PowerShell aus

* Ausführen von Mahout-Aufträgen von der Hadoop-Befehlszeile aus

* Installieren von Mahout auf HDInsight 2.0- und 3.0-Clustern

##Themen in diesem Artikel

* [Generieren von Empfehlungen mithilfe von PowerShell](#recommendations)
* [Klassifizieren von Daten mit der Hadoop-Befehlszeile](#classify)
* [Problembehandlung](#troubleshooting)


##<a name="recommendations"></a>Generieren von Empfehlungen mithilfe von PowerShell

> [WACOM.NOTE] Zwar funktioniert der in diesem Abschnitt verwendete Auftrag mit PowerShell, doch arbeiten viele der mit Mahout bereitgestellten Klassen aktuell nicht mit PowerShell und müssen mithilfe der Hadoop-Befehlszeile ausgeführt werden. Eine Liste der Klassen, die nicht mit PowerShell funktionieren, finden Sie im Abschnitt [Problembehandlung](#troubleshooting) .
>
> Ein Beispiel für den Einsatz der Hadoop-Befehlszeile für das Ausführen von Mahout-Aufträgen finden Sie unter [Klassifizieren von Daten mit der Hadoop-Befehlszeile](#classify).

Eine der von Mahout bereitgestellten Funktionen ist ein Empfehlungsmodul. Es akzeptiert Daten im Format "userID", "itemId" und "prefValueserID", "itemId", "prefValue" (die Voreinstellungen des Benutzers für den Artikel). Mahout kann dann eine Analyse des gemeinsamen Auftretens durchführen, dass Benutzer, die eine Vorliebe für einen Artikel haben, auch eine für diese anderen Artikel haben. Mahout will dann Benutzer mit ähnlichen Artikelpräferenzen bestimmen, aus denen sich dann Empfehlungen erstellen lassen.

Im Folgenden sehen Sie ein extrem einfaches Beispiel mit Spielfilmen:

* Gemeinsames Auftreten - Joe, Alice und Bob gefällt "Krieg der Sterne", "Das Imperium schlägt zurück" und "Rückkehr der Jedi-Ritter". Mahout stellt fest, dass Benutzer, denen einer dieser Filme gefällt, auch die beiden anderen mögen.

* "Gemeinsames Auftreten" - Bob und Alice hat auch "Die dunkle Bedrohung", "Angriff der Klonkrieger" und "Die Rache der Sith" gefallen. Mahout stellt fest, dass Benutzer, denen die vorherigen drei Filme gefallen, auch diese drei mögen.

* Vergleichbare Empfehlung - Da Joe die ersten drei gefallen haben, sucht Mahout nach Filmen, die den anderen dreien mit ähnlichen Vorlieben gefallen haben, die Joe aber noch nicht gesehen (mit "Gefällt mir" markiert oder bewertet) hat. In diesem Fall empfiehlt Mahout "Die dunkle Bedrohung", "Angriff der Klonkrieger" und "Die Rache der Sith".

###Laden der Daten

Praktischerweise stellt GroupLens Research [Bewertungsdaten für Filme][movielens] in einem Mahout-kompatiblen Format zur Verfügung.

1. Laden Sie das Archiv [MovieLens 100k][100k] herunter, das 100.000 Bewertungen von 1.000 Benutzern zu 1.700 Filmen enthält.

2. Extrahieren Sie das Archiv. Es sollte ein Verzeichnis namens "ml-100k" enthalten, das viele Dateien mit dem Präfix "u." enthält. Die Datei, die von Mahout analysiert wird, ist "u.data". Die Datenstruktur dieser Datei ist "userID", "movieID", "userRating" und "timestamp". Hier ist ein Beispiel für die Daten.


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596


3. Laden Sie die Datei "u.data" in "example/data/u.data" in Ihrem HDInsight-Cluster hoch. Wenn Sie [Azure PowerShell][aps] haben, können Sie das [HDInsight-Tools][tools] PowerShell-Modul zum Hochladen der Datei verwenden. Andere Möglichkeiten zum Hochladen von Dateien finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight][upload]. Im Folgenden wird gezeigt, wie "Add-HDInsightFile" zum Hochladen der Datei verwendet wird.

    	PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"

    Dabei wird die Datei "u.data" in "example/data/u.data" in den Standardspeicher Ihres Clusters hochgeladen. Es ist dann möglich, mithilfe des URI "wasb:///example/data/u.data" in HDInsight-Aufträgen auf diese Daten zuzugreifen.

###Ausführen des Auftrags

Verwenden Sie das folgende PowerShell-Skript zum Ausführen eines Auftrags mithilfe des Mahout-Empfehlungsmoduls mit der bereits hochgeladenen Datei "u.data".

	# The HDInsight cluster name.
	$clusterName = "the cluster name"

	# NOTE: The version number portion of the file path
	# may change in future versions of HDInsight.
	# So dynamically grab it using Hive.
	$mahoutPath = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar' | where {$_.startswith("C:\apps\dist")}
	$jarFile = "file:///$mahoutPath"
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
	$jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
	                "--input", "wasb:///example/data/u.data",
	                "--output", "wasb:///example/out",
	                "--tempDir", "wasb:///temp/mahout"

	# Create the job definition
	$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job

	# Write out any error information
	Write-Host "STDERR"
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] Mahout-Aufträge entfernen keine temporären Daten, die bei der Verarbeitung des Auftrags erstellt wurden. Deshalb ist der "--tempDir"-Parameter im Beispielauftrag festgelegt - so werden die temporären Dateien in einem spezifischen Pfad zum einfachen Löschen isoliert.
>
> Zum Entfernen dieser Dateien können Sie eine der in [Hochladen von Daten für Hadoop-Aufträge in HDInsight][upload] erwähnten Upload-Daten für Hadoop-Aufträge in HDInsight verwenden. Oder verwenden Sie die Funktion "Remove-HDInsightFile" im [HDInsight-Tools][tools] PowerShell-Skript.
>
> Wenn Sie die temporären Dateien nicht entfernen, erhalten Sie beim erneuten Ausführen des Auftrags eine Fehlermeldung.

Der Mahout-Auftrag gibt keine Ausgabe an STDOUT zurück, sondern speichert sie stattdessen im festgelegten Ausgabeverzeichnis als "part-r-00000". Zum Herunterladen und Anzeigen der Datei verwenden Sie die Funktion "Get-HDInsightFile" im [HDInsight-Tools][tools] PowerShell-Modul.

Im Folgenden sehen Sie ein Beispiel für den Inhalt der Datei:

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

Die erste Spalte ist "userID". Die in "[" und "]" enthaltenen Werte sind "movieId":"recommendationScore".

###Anzeigen der Ausgabe

Zwar ist die generierte Ausgabe in Ordnung für die Nutzung in einer Anwendung; sie ist allerdings von Menschen nicht gut lesbar. Einige der anderen Dateien, die im Vorfeld in den Ordner "ml-100k" extrahiert wurden, können für die Auflösung von "movieId" in den Namen eines Films verwendet werden. Im Ordner ml-100k ist ein Python-Skript enthalten, das das erledigt (show_recommendations.py). Sie können aber auch das folgende PowerShell-Skript verwenden.

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

Damit sie es nutzen können, müssen Sie im Vorfeld den Ordner "ml-100k" extrahiert haben. Sie benötigen außerdem eine lokale Kopie der Ausgabedatei "part-r-00000", die vom Mahout-Auftrag generiert wurde. Nachfolgend sehen Sie ein Beispiel für das Ausführen des Skripts.

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt


> [WACOM.NOTE] Das Python-Beispielskript "show_recommendations.py" verwendet dieselben Parameter.

Die Ausgabe sollte in etwa wie folgt aussehen.

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

Eine der in Mahout verfügbaren Klassifizierungsmethoden besteht darin, einen [Random Forest][forest] zu erstellen. Das ist ein mehrstufiger Prozess, bei dem mithilfe von Trainingsdaten Decision Trees generiert werden, die dann zum Klassifizieren von Daten verwendet werden. Dabei wird die von Mahout bereitgestellte Klasse "org.apache.mahout.classifier.df.tools.Describe" verwendet, die derzeit mit der Hadoop-Befehlszeile ausgeführt werden muss.

###Laden der Daten

Die aktuelle Mahout-Implementierung ist mit dem Repository-Format der University of California, Irvine (UCI). [Warum ist das wichtig, was ist das für ein Format?]

1. Laden Sie folgende Dateien von [http://nsl.cs.unb.ca/NSL-KDD/] herunter(http://nsl.cs.unb.ca/NSL-KDD/).

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff) - die Schulungsdatei

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff) - die Testdaten

2. Öffnen Sie jede Datei und entfernen Sie die Zeilen oben, die mit '@' beginnen. Speichern Sie dann die Dateien. Werden sie nicht entfernt, erhalten Sie Fehler, wenn Sie die Daten mit Mahout verwenden.

2. Laden Sie die Daten in "example/data" hoch. Sie können das mithilfe der Funktion "Add-HDInsightFile" im [HDInsight-Tools][tools] PowerShell-Modul tun.

###Ausführen des Auftrags

1. Da dieser Auftrag die Hadoop-Befehlszeile benötigt, müssen Sie zunächst Remotedesktop über das [Azure-Verwaltungsportal][management] aktivieren. Wählen Sie im Portal Ihren HDInsight-Cluster und dann "Remote aktivieren" unten auf der Seite "Konfiguration" aus.

    ![enable remote][enableremote]

    Wenn Sie aufgefordert werden, geben Sie einen Benutzernamen und ein Kennwort für Remotesitzungen ein.

2. Nachdem der Remotezugriff aktiviert ist, wählen Sie "Verbinden", um die Verbindung zu starten. Dabei wird eine RDP-Datei heruntergeladen, mit der eine Remotedesktopsitzung gestartet werden kann.

    ![connect][connect]

3. Nachdem die Verbindung aufgebaut wurde, verwenden Sie das Symbol "Hadoop-Befehlszeile" zum Öffnen der Hadoop-Befehlszeile.

	![hadoop cli][hadoopcli]

3. Verwenden Sie den folgenden Befehl zum Erstellen des Dateideskriptors (KDDTrain+.info,) mithilfe von Mahout.

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L` beschreibt die Attribute der Daten in der Datei. Ein numerisches Attribut, zwei kategorische usw. L gibt eine Bezeichnung an.

4. Erstellen Sie einen Forest aus Decision Trees mithilfe des folgenden Befehls.

		hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    Die Ausgabe dieses Vorgangs wird im Verzeichnis "nsl-forest" gespeichert, das sich im Speicher Ihres HDInsight-Clusters unter "wasb://user/&lt;username>/nsl-forest/nsl-forest.seq" befindet. <username> ist der für die Remotedesktopsitzung verwendete Benutzername. Die Datei ist nicht für Menschen lesbar.

5. Testen Sie den Forest durch Klassifizieren des "KDDTest+.arff"-Datasets mithilfe des folgenden Befehls.

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

  Der Auftrag generiert außerdem eine Datei, die sich unter "wasb:///example/data/predictions/KDDTest+.arff.out" befindet, aber nicht für Menschen lesbar ist.

> [WACOM.NOTE] Mahout-Aufträge überschreiben keine Dateien. Wenn Sie diese Aufträge noch einmal ausführen möchten, müssen Sie die vom vorherigen Auftrag erstellten Dateien löschen.

##<a name="troubleshooting"></a>Problembehandlung

###<a name="install"></a>Installieren von Mahout

Mahout wird auf HDInsight 3.1-Clustern installiert. Die Installation kann manuell auf 3.0 oder 2.1-Clustern mit folgenden Schritten erfolgen.

1. Die zu verwendende Version von Mahout hängt von der Mahout-Version auf Ihrem Cluster ab. Die Clusterversion finden Sie heraus, wenn Sie Folgendes mit [Azure PowerShell][aps] verwenden:

    	PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select version


  * Für HDInsight 2.1 können Sie eine JAR-Datei herunterladen, die [Mahout 0.9] enthält(http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar).

  * Für HDInsight 3.0 müssen Sie [Mahout aus dem Quellcode erstellen][build] und die von HDInsight bereitgestellte Hadoop-Version festlegen. Installieren Sie die auf der Build-Seite aufgeführten erforderlichen Dinge, laden Sie die Quelldateien herunter und verwenden Sie dann folgenden Befehl zum Erstellen der Mahout jar-Dateien.

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	Nachdem der Build fertig gestellt wurde, finden Sie die JAR-Datei unter "mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar".

    	> [WACOM.NOTE] Sobald Mahout 1.0 veröffentlicht ist, sollten Sie die vordefinierten Pakete mit HDInsight 3.0 verwenden können.

2. Laden Sie die jar-Datei in "example/jars" in den Standardspeicher Ihres Clusters hoch. Das folgende Beispiel verwendet das Skript [send-hdinsight][sendhdinsight] für das Hochladen der Datei

    	PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

###Dateien können nicht überschrieben werden

Mahout-Auftrags räumen keine temporären Dateien auf, die während der Verarbeitung erzeugt werden. Außerdem überschreiben Aufträge keine vorhandenen Ausgabedateien.

Um Fehler beim Ausführen von Mahout-Aufträgen zu vermeiden, löschen Sie entweder temporäre und Ausgabedateien zwischen dem Ausführen oder verwenden Sie eindeutige Namen für temporäre und Ausgabeverzeichnisse.

###jar-Datei wird nicht gefunden

Zwar enthält HDInsight 3.1 Mahout, der Pfad- und Dateiname beinhaltet aber die Versionsnummer der auf dem Cluster installierten Mahout-Version. Das PowerShell-Beispielskript in diesem Lernprogramm verwendet einen Pfad, der im Juli 2014 gültig ist. Die Versionsnummer wird sich aber mit künftigen Aktualisierungen von HDInsight ändern. Den aktuellen Pfad zur Mahout jar-Datei Ihres Clusters stellen Sie mithilfe folgender PowerShell-Befehle fest. Ändern Sie dann das Skript und referenzieren Sie den ausgegebenen Pfad.

	Use-AzureHDInsightCluster -Name $clusterName
	$jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

###<a name="nopowershell"></a>Klassen, die nicht mit PowerShell funktionieren

Mahout-Aufträge, die die folgenden Klassen verwenden, geben eine Menge Fehlermeldungen zurück, wenn sie von PowerShell aus verwendet werden.

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

Wenn Sie Aufträge mit diesen Klassen ausführen möchten, bauen Sie eine eine Verbindung zum HDInsight-Cluster auf und führen Sie die Aufträge mit der Hadoop-Befehlszeile aus. Ein Beispiel finden Sie unter [Klassifizieren von Daten mit der Hadoop-Befehlszeile](#classify) .


[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-get-started/
[upload]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-upload-data/
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools

<!--HONumber=35.1-->
