<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Verwenden von Pig mit Hadoop in HDInsight

Sie erfahren, wie Sie [Apache Pig][]-Aufträge in HDInsight zur Analyse von Apache log4j-Protokolldateien verwenden können.

**Geschätzter Zeitaufwand:** 30 Minuten

## Themen in diesem Artikel

-   [Der Pig-Anwendungsfall][]
-   [Voraussetzungen][]
-   [Verständnis von Pig Latin][]
-   [Übermitteln von Pig-Aufträgen in PowerShell][]
-   [Übermitteln von Pig-Aufträgen mit dem .NET SDK][]
-   [Nächste Schritte][]

## <span id="usage"></span></a>Der Pig-Anwendungsfall

Datenbanken sind praktisch für kleine Mengen von Daten und Abfragen mit niedriger Latenz. Für große Datenmengen und Datensätze im Terabyte-Bereich sind SQL-Datenbanken jedoch keine ideale Lösung. Mit steigender Datenbanklast und abnehmender Leistung müssen Datenbankadministratoren jedoch normalerweise mehr Hardware anschaffen.

Normalerweise schreiben alle Anwendungen Fehler, Ausnahmen und sonstige Probleme in Protokolldateien, und Administratoren können anhand dieser Daten Probleme analysieren und bestimmte Metriken generieren. Diese Protokolldateien sind oft recht groß und enthalten umfangreiche Daten, die verarbeitet und analysiert werden müssen.

Protokolldateien sind ein gutes Beispiel für große Datenmengen. Die Verarbeitung großer Datenmengen mit relationalen Datenbanken und Statistik-/Visualisierungslösungen ist oft schwierig. Aufgrund der großen Datenmengen und des benötigten Rechenaufwands wird oft parallele Software auf Hunderten oder Tausenden von Servern benötigt, um diese Daten in einem angemessenen Zeitraum zu verarbeiten. Hadoop bietet ein MapReduce-Framework für die Erstellung von Anwendungen zur parallelen Verarbeitung großer Mengen strukturierter und unstrukturierter Daten in großen Rechnerclustern auf höchst zuverlässige und fehlertolerante Weise.

[Apache *Pig*][Apache Pig] ist eine Skriptsprache für *MapReduce*-Aufgaben als Alternative zu Java-Code. Die Pig-Skriptsprache heißt *Pig Latin*. Pig Latin-Anweisungen verwenden den folgenden allgemeinen Ablauf:

-   **Laden**: Die zu verändernden Daten werden aus dem Dateisystem gelesen
-   **Transformation**: Manipulation der Daten
-   **Ablage oder Speicherung**: Ausgabe der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

Pig beschleunigt die Erstellung von Mapper- und Reducer-Programmen. Sie benötigten dazu weder Java noch Code-Textbausteine. Außerdem können Sie Java-Code mit Pig kombinieren. Viele komplexe Algorithmen lassen sich in weniger als fünf Zeilen mit gut lesbarem Pig-Code schreiben.

Die folgenden beiden Abbildungen zeigen eine grafische Darstellung der Lerninhalte dieses Artikels. Die Abbildungen zeigen eine repräsentative Probe des Datensatzes, um den Fluss und die Transformation der Daten beim Durchlaufen des Pig-Codes im Skript zu veranschaulichen. Die erste Abbildung zeigt einen Ausschnitt der log4j-Datei:

![Dateiausschnitt][]

Die zweite Abbildung zeigt die Datentransformation:

![HDI.PIG.Data.Transformation][]

Weitere Informationen zu Pig Latin finden Sie unter [Pig Latin-Referenzhandbuch 1][] und [Pig Latin-Referenzhandbuch 2][].

## <span id="prerequisites"></span></a>Voraussetzungen

Beachten Sie die folgenden Voraussetzungen, bevor Sie mit diesem Artikel beginnen:

-   Einen Azure HDInsight-Cluster. Anweisungen finden Sie unter [Erste Schritte mit Azure HDInsight][] or [Bereitstellen eines HDInsight-Clusters][]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Clustereigenschaft</th>
    <th align="left">PowerShell-Variablenname</th>
    <th align="left">Wert</th>
    <th align="left">Beschreibung</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight-Clustername</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Der HDInsight-Cluster, auf dem dieses Lernprogramm ausgeführt wird.</td>
    </tr>
    </tbody>
    </table>

-   Installieren und konfigurieren Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][].

**Grundlagen der HDInsight-Speicherung**

HDInsight verwendet Azure Blob-Speicher zur Datenspeicherung. Der Speicher wird *WASB* oder *Azure Storage-Blob* genannt. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][].

Bei der Bereitstellung eines HDInsight-Clusters wird ein Azure-Speicherkonto definiert, und ein bestimmter Blob-Speichercontainer aus diesem Konto wird als Standard-Dateisystem verwendet, genau wie in HDFS. Neben diesem Speicherkonto können Sie weitere Speicherkonten aus demselben oder aus anderen Azure-Abonnements beim Einrichtungsprozess hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][Bereitstellen eines HDInsight-Clusters]. Zur Vereinfachung des PowerShell-Skripts für dieses Lernprogramm befinden sich alle Dateien im Standard-Systemcontainer unter */tutorials/usepig*. Standardmäßig hat dieser Container denselben Namen wie der HDInsight-Cluster.
Die WASB-Syntax lautet:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere *asv://*-Syntax wird in HDInsight-Clustern der Versionen 2.1 und 1.6 unterstützt, aber nicht in den Clusterversionen 3.0 und höher.

> [WACOM.NOTE] Der WASB-Pfad ist ein virtueller Pfad. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][].

Dateien im Standard-Dateisystemcontainer sind unter den folgenden URIs aus HDInsight verfügbar (mit sample.log als Beispiel. Dies ist die Datendatei, die für dieses Lernprogramm verwendet wird):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

    example/data/sample.log

In diesem Artikel verwenden Sie eine log4j-Beispieldatei, die in jedem HDInsight-Cluster unter *\\example\\data\\sample.log* abgelegt ist. Ein Beispiel zum Hochladen Ihrer eigenen Datendateien finden Sie unter [Hochladen von Daten nach HDInsight][].

## <span id="understand"></span></a> Verständnis von Pig Latin

In dieser Sitzung werden Sie einige Pig Latin-Anweisungen und die Ausgabe nach deren Ausführung betrachten. In der nächsten Sitzung werden Sie die Pig-Anweisungen in der PowerShell ausführen.

1.  Laden von Daten aus dem Dateisystem und Anzeigen der Ergebnisse

        LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

    Die Ausgabe sieht in etwa wie folgt aus:

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

2.  Betrachten Sie die einzelnen Zeilen der Datendatei und achten Sie auf die 6 Protokollebenen:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Herausfiltern der Zeilen ohne Übereinstimmung. Zum Beispiel leere Zeilen.

        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

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

4.  Gruppierung aller Protokollebenen in je eine Zeile:

        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

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

5.  Zählen der Vorkommnisse von Protokollebenen pro Gruppe. Ermitteln der Häufigkeit der einzelnen Protokollebenen:

        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    Die Ausgabe sieht in etwa wie folgt aus:

        (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  Sortieren der Häufigkeiten in absteigender Reihenfolge:

        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    Die Ausgabe sieht in etwa wie folgt aus:

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
        (ERROR,181)
        (FATAL,37)

## <span id="powershell"></span></a>Übermitteln von Pig-Aufträgen in PowerShell

Dieser Abschnitt enthält Anweisungen für PowerShell-Cmdlets. Zuvor müssen Sie jedoch Ihre lokale Umgebung einrichten und die Verbindung zu Azure konfigurieren. Details finden Sie unter [Erste Schritte mit Azure HDInsight][] oder [Verwalten von HDInsight mit PowerShell][].

**So führen Sie Pig Latin in der PowerShell aus**

1.  Öffnen Sie Windows PowerShell ISE. (Geben Sie auf dem Startbildschirm von Windows 8 **PowerShell\_ISE** ein, und klicken Sie dann auf **Windows PowerShell ISE**.) Siehe [Start Windows PowerShell on Windows 8 and Windows][] (Starten von Windows PowerShell unter Windows 8 und Windows, in englischer Sprache).
2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

        Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Dieses Verfahren zum Hinzufügen einer Abonnementverbindung läuft nach einer bestimmten Zeit ab. Daher müssen Sie das Cmdlet nach 12 Stunden erneut ausführen.

    > [WACOM.NOTE] Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standard-Abonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets **Select-AzureSubscription** aus.

3.  Kopieren Sie die folgenden Zeilen und fügen Sie sie in den Skriptbereich ein:

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  Setzen Sie die Variable $clusterName.

5.  Fügen Sie die folgenden Zeilen am Ende des Skriptbereichs ein. Diese Zeilen definieren die Pig Latin-Abfragezeichenfolge und erstellen eine Pig-Auftragsdefinition:

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

    Mit dem Parameter -File können Sie eine Pig-Skriptdatei unter HDFS angeben. Die Kennzeichnung -StatusFolder gibt den Ordner für das Standard-Fehlerprotokoll und die Standard-Ausgabedatei an.

6.  Fügen Sie die folgenden Zeilen zur Übermittlung des Pig-Auftrags ein:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Fügen Sie die folgenden Zeilen ein, um auf den Abschluss des Pig-Auftrags zu warten:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Fügen Sie die folgenden Zeilen ein, um die Ausgabe des Pig-Auftrags auszugeben:

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Eines der Get-AzureHDInsightJobOut-Cmdlet ist im folgenden Screenshot auskommentiert, um die Ausgabe abzukürzen.

9.  Drücken Sie **F5**, um das Skript auszuführen:
    ![HDI.Pig.PowerShell][]

    Die Pig-Aufgabe berechnet die Häufigkeiten der einzelnen Protokollebenen.

## <span id="sdk"></span></a>Übermitteln von Pig-Aufträgen mit dem .NET SDK

Hier sehen Sie ein Beispiel für die Übermittlung eines Pig-Auftrags mit dem HDInsight .NET SDK. Anweisungen zur Entwicklung von C#-Anwendungen für die Übermittlung von Hadoop-Aufträgen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][].

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

## <span id="nextsteps"></span></a>Nächste Schritte

Pig ist hilfreich für die Datenanalyse, aber andere in HDInsight enthaltene Sprachen sind möglicherweise ebenfalls interessant für Sie. Hive bietet eine SQL-ähnliche Abfragesprache, mit der Sie Daten in HDInsight schnell und einfach abfragen können, und MapReduce-Aufgaben in Java eignen sich gut für komplexe Datenanalysen. Weitere Informationen finden Sie unter den folgenden Links:

-   [Erste Schritte mit Azure HDInsight][]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten nach HDInsight]
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs][Programmgesteuerte Übermittlung von Hadoop-Aufträgen]
-   [Verwenden von Hive mit HDInsight][]

  [Apache Pig]: http://pig.apache.org/
  [Der Pig-Anwendungsfall]: #usage
  [Voraussetzungen]: #prerequisites
  [Verständnis von Pig Latin]: #understand
  [Übermitteln von Pig-Aufträgen in PowerShell]: #powershell
  [Übermitteln von Pig-Aufträgen mit dem .NET SDK]: #sdk
  [Nächste Schritte]: #nextsteps
  [Dateiausschnitt]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Pig Latin-Referenzhandbuch 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Pig Latin-Referenzhandbuch 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Bereitstellen eines HDInsight-Clusters]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Hochladen von Daten nach HDInsight]: ../hdinsight-upload-data/
  [Verwalten von HDInsight mit PowerShell]: ../hdinsight-administer-use-powershell/
  [Start Windows PowerShell on Windows 8 and Windows]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Programmgesteuerte Übermittlung von Hadoop-Aufträgen]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
