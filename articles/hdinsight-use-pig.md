<properties 
	pageTitle="Verwenden von Pig in HDInsight | Azure" 
	description="Erfahren Sie, wie Sie Pig mit HDInsight verwenden können. Schreiben Sie Pig Latin-Anweisungen, um eine Anwendungsprotokolldatei zu analysieren und führen Sie Abfragen auf den Daten aus, um Ausgaben für die Analyse zu generieren." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# Verwenden von Pig mit Hadoop in HDInsight

In diesem Lernprogramm erfahren Sie, wie Sie [Apache Pig][apachepig-home]-Aufträge in HDInsight zum Analysieren großer Datendateien ausführen können. Pig ist eine Skriptsprache zum Ausführen von  *MapReduce*-Aufträgen als Alternative zu Java-Code. Die Pig-Skriptsprache heißt *Pig Latin*.

**Voraussetzungen**

* Sie benötigen ein bereitgestelltes Azure HDInsight-Cluster. Anweisungen finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] oder [Bereitstellen eines HDInsight-Clusters][hdinsight-provision]. Sie benötigen den Namen des Clusters, um das Lernprogramm abzuschließen.

* Sie müssen Azure PowerShell auf Ihrer Arbeitsstation installiert und konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure]. 

**Geschätzter Zeitaufwand:** 30 Minuten

## Themen in diesem Artikel

* [Gründe für Pig](#usage)
* [Was lerne ich in diesem Lernprogramm?](#what)
* [Identifizieren der zu analysierenden Daten](#data)
* [Verständnis von Pig Latin](#understand)
* [Übermitteln von Pig-Aufträgen über PowerShell](#powershell)
* [Übermitteln von Pig-Aufträgen mit dem .NET SDK](#sdk)
* [Nächste Schritte](#nextsteps)
 
## <a id="usage"></a>Gründe für Pig
Die Verarbeitung großer Datenmengen mit relationalen Datenbanken und Statistik-/Visualisierungslösungen ist oft schwierig. Aufgrund der großen Datenmengen und des benötigten Rechenaufwands wird oft parallele Software auf Hunderten oder Tausenden von Servern benötigt, um diese Daten in einem angemessenen Zeitraum zu verarbeiten. Hadoop bietet ein *MapReduce*-Framework für das Schreiben von Anwendungen, das große Mengen von strukturierten und unstrukturierten Daten parallel in großen Clustern von Computern auf höchst zuverlässige und fehlertolerante Weise verarbeitet.

![HDI.Pig.Architecture][image-hdi-pig-architecture]

[Apache *Pig*][apachepig-home] bietet eine Abstraktionsebene über dem Java-basierten MapReduce-Framework, und Benutzer können ohne Java- oder MapReduce-Kenntnisse Daten analysieren. Pig ist eine Plattform zum Analysieren großer Datensätze mit einer leicht zu bedienenden Sprache namens  *Pig Latin*, einer so genannten Datenflusssprache. Pig beschleunigt die Erstellung von Mapper- und Reducer-Programmen. Es sind keinerlei Java-Kenntnisse erforderlich. Außerdem können Sie Java-Code mit Pig kombinieren. Viele komplexe Algorithmen lassen sich in weniger als fünf Zeilen mit gut lesbarem Pig-Code schreiben. 

Pig Latin-Anweisungen verwenden den folgenden allgemeinen Ablauf:   

- **Last**: Die zu verändernden Daten werden aus dem Dateisystem gelesen
- **Transformation**: Manipulation der Daten 
- **Ablage oder Speicherung**: Ausgabe der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

Weitere Informationen zu Pig Latin finden Sie im [Pig Latin-Referenzhandbuch 1][piglatin-manual-1] und [Pig Latin-Referenzhandbuch 2][piglatin-manual-2].

## <a id="what"></a>Was lerne ich in diesem Lernprogramm?
In diesem Lernprogramm werden Sie eine Apache-Protokolldatei (*sample.log*) analysieren, um die Anzahl der Protokollebenen (INFO, DEBUG, TRACE usw.) zu ermitteln. In den folgenden zwei Abbildungen finden Sie eine visuelle Darstellung der Lernziele dieses Artikels. Die erste Abbildung zeigt einen Ausschnitt der sample.log-Datei.

![Whole File Sample][image-hdi-log4j-sample]

Die zweite Abbildung veranschaulicht den Fluss und die Transformation der Daten beim Durchlaufen des Pig-Codes im Skript:

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]

Der Pig-Auftrag, den Sie in diesem Lernprogramm erstellen, folgt demselben Fluss.

## <a id="data"></a>Identifizieren der zu analysierenden Daten

HDInsight verwendet Azure-Blob-Speicher als Standarddateisystem für Hadoop-Cluster. Einige Beispiel-Datendateien werden im Rahmen der Clusterbereitstellung zum Blob-Speicher hinzugefügt. Sie können diese Datendateien zum Ausführen von Hive-Abfragen auf dem Cluster verwenden. Bei Bedarf können Sie auch eigene Datendateien in das Blob-Speicherkonto hochladen, dem das Cluster zugeordnet ist. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight][hdinsight-upload-data]. Weitere Informationen zur Verwendung der Azure-Blob-Speicher mit HDInsight finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage].

Die Syntax für den Zugriff auf die Dateien im Blob-Speicher ist wie folgt:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere  *asv://*-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.0-Clustern; sie werden auch in späteren Versionen nicht unterstützt.

Dateien im Standard-Dateisystemcontainer sind ebenfalls unter den folgenden URIs aus HDInsight verfügbar (mit sample.log als Beispiel.  Dies ist die Datendatei, die für dieses Lernprogramm verwendet wird):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

	example/data/sample.log

Dieser Artikel verwendet eine im HDInsight-Cluster mitgelieferte  *log4j*-Musterdatei, die unter *\example\data\sample.log*. gespeichert ist. Informationen zum Hochladen Ihrer eigenen Datendateien finden Sie unter [Hochladen von Daten in HDInsight][hdinsight-upload-data].




## <a id="understand"></a> Verständnis von Pig Latin

In diesem Abschnitt werden Sie einige Pig Latin-Anweisungen und die Ausgabe nach deren Ausführung betrachten. Im nächsten Abschnitt werden Sie die Pig-Anweisungen in PowerShell ausführen, um die Beispiel-Protokolldatei zu analysieren. Die einzelnen Pig Latin-Anweisungen müssen direkt auf dem HDInsight-Cluster ausgeführt werden.

1. Aktivieren Sie Remotedesktop für den HDInsight-Cluster anhand der Anweisungen unter [Verbinden mit HDInsight-Clustern über RDP](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-administer-use-management-portal/#rdp). Melden Sie sich beim Clusterknoten an, und klicken Sie auf dem Desktop auf **Hadoop-Befehlszeile**.

2. Navigieren Sie in der Befehlszeile zum Installationsverzeichnis von **Pig**. Geben Sie Folgendes ein:

		C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3. Geben Sie in der Befehlszeile *pig* ein, und drücken Sie die EINGABETASTE, um zur  *grunt*-Shell zu gelangen.

		C:\apps\dist\pig-<version>\bin>pig
		...
		grunt>  

4. Geben Sie Folgendes ein, um Daten aus der Beispieldatei in das Dateisystem zu laden und anschließend die Ergebnisse anzuzeigen: 

		grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
		grunt> DUMP LOGS;
	
	The output is similar to the following:
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

5. Betrachten Sie die einzelnen Zeilen der Datendatei und achten Sie auf die 6 Protokollebenen:

		grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
6. Filtern Sie die Zeilen ohne Übereinstimmung heraus und zeigen Sie das Ergebnis an. Damit schließen Sie die leeren Zeilen aus.

		grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		grunt> DUMP FILTEREDLEVELS;

	Die Ausgabe sieht in etwa wie folgt aus:

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

7. Gruppieren Sie alle Protokollebenen in eigene Zeilen und zeigen Sie das Ergebnis an:

		grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		grunt> DUMP GROUPEDLEVELS;

	Die Ausgabe sieht in etwa wie folgt aus:

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


8. Zählen Sie die Vorkommnisse der Protokollebenen für jede Gruppe und zeigen Sie das Ergebnis an:

		grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		grunt> DUMP FREQUENCIES;
 
	Die Ausgabe sieht in etwa wie folgt aus:

		(INFO,96)
		(WARN,11)
		(DEBUG,434)
		(ERROR,6)
		(FATAL,2)
		(TRACE,816)

9. Sortieren Sie die Häufigkeiten in absteigender Reihenfolge und zeigen Sie das Ergebnis an:

		grunt> RESULT = order FREQUENCIES by COUNT desc;
		grunt> DUMP RESULT;   

	Die Ausgabe sieht in etwa wie folgt aus: 

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
	 	(ERROR,6)
		(FATAL,2)

## <a id="powershell"></a>Übermitteln von Pig-Aufträgen über PowerShell

Dieser Abschnitt enthält Anweisungen für PowerShell-Cmdlets. Zuvor müssen Sie jedoch Ihre lokale Umgebung einrichten und die Verbindung zu Azure konfigurieren. Details finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started] oder [Verwalten von HDInsight mit PowerShell][hdinsight-admin-powershell].


**So führen Sie Pig Latin in der PowerShell aus**

1. Öffnen Sie Windows PowerShell ISE. Geben Sie auf dem Windows 8-Startbildschirm **PowerShell_ISE** ein und klicken Sie auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starten von Windows PowerShell unter Windows 8 und Windows][powershell-start].

2. Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Dieses Verfahren zum Hinzufügen einer Abonnementverbindung läuft nach einer bestimmten Zeit ab. Daher müssen Sie das Cmdlet nach 12 Stunden erneut ausführen. 

	> [AZURE.NOTE] Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standardabonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets <strong>Select-AzureSubscription</strong> aus.
2. Kopieren Sie die folgenden Zeilen und fügen Sie sie in den Skriptbereich ein:

		$clusterName = "<HDInsightClusterName>" 	#Specify the cluster name
		$statusFolder = "/tutorials/usepig/status"	#Specify the folder to dump results

	Das Skript erstellt den angegebenen Statusordner, falls dieser noch nicht existiert. 

3. Fügen Sie die folgenden Zeilen am Ende des Skriptbereichs ein. Diese Zeilen definieren die Pig Latin-Abfragezeichenfolge und erstellen eine Pig-Auftragsdefinition:

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

	Mit dem Schalter **-File** können Sie eine Pig-Skriptdatei unter HDFS angeben. Der Schalter **-StatusFolder** gibt den Ordner für das Standardfehlerprotokoll und die Standardausgabedatei an.

4. Fügen Sie die folgenden Zeilen zur Übermittlung des Pig-Auftrags ein:
		
		# Submit the Pig job
		Write-Host "Submit the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

5. Fügen Sie die folgenden Zeilen ein, um auf den Abschluss des Pig-Auftrags zu warten:		

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Fügen Sie die folgenden Zeilen ein, um die Ausgabe des Pig-Auftrags auszugeben:
		
		# Print the standard error and the standard output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	> [AZURE.NOTE] Eines der "Get-AzureHDInsightJobOut"-Cmdlets ist im folgenden Screenshot auskommentiert, um die Ausgabe abzukürzen.   

7. Drücken Sie **F5**, um das Skript auszuführen:

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	Der Pig-Auftrag berechnet die Häufigkeiten der einzelnen Protokollebenen.


## <a id="sdk"></a>Übermitteln von Pig-Aufträgen mit dem .NET SDK

Führen Sie die hier aufgelisteten Schritte aus, um einen Pig-Auftrag mit einer C#-Anwendung zu übermitteln.   Anweisungen zur Entwicklung von C#-Anwendungen für die Übermittlung von Hadoop-Aufträgen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs].

1. Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Anweisungen finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).

2. Erstellen Sie eine Visual Studio-Konsolenanwendung und installieren Sie das HDInsight-Paket. Klicken Sie im Menü Extras auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Geben Sie in der Befehlszeile Folgendes ein:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

3. Doppelklicken Sie auf Program.cs und fügen Sie den folgenden Code ein, um einen Pig-Auftrag zu übermitteln. Setzen Sie Werte für die Variablen ein.

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
		
		namespace SubmitPigJobs
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					// Set the variables
					string subscriptionID = "<Azure subscription ID>";
					string certFriendlyName = "<certificate friendly name>";
			
					string clusterName = "<HDInsight cluster name>";
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
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

## <a id="nextsteps"></a>Nächste Schritte

Pig ist hilfreich für die Datenanalyse, aber andere in HDInsight enthaltene Sprachen sind möglicherweise ebenfalls interessant für Sie. Hive bietet eine SQL-ähnliche Abfragesprache, mit der Sie Daten in HDInsight schnell und einfach abfragen können, und MapReduce-Aufgaben in Java eignen sich gut für komplexe Datenanalysen. Weitere Informationen finden Sie unter den folgenden Links:


* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell/

[powershell-start]: http://technet.microsoft.com/de-de/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png


<!--HONumber=42-->
