<properties urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Verwenden von Pig in HDInsight | Azure" metaKeywords="" description="Erfahren Sie, wie Sie Pig mit HDInsight verwenden k&ouml;nnen. Schreiben Sie Pig Latin-Anweisungen, um eine Anwendungsprotokolldatei zu analysieren und f&uuml;hren Sie Abfragen auf den Daten aus, um Ausgaben f&uuml;r die Analyse zu generieren." metaCanonical="" services="hdinsight" documentationCenter="" title="Verwenden von Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Verwenden von Pig mit Hadoop in HDInsight

In diesem Lernprogramm erfahren Sie, wie Sie [Apache Pig][Apache Pig]-Jobs in HDInsight zum Analysieren großer Datendateien ausführen können. Pig ist eine Skriptsprache zum Ausführen von *MapReduce*-Jobs als Alternative zu Java-Code. Die Pig-Skriptsprache heißt *Pig Latin*.

**Voraussetzungen**

-   Sie benötigen ein bereitgestelltes Azure HDInsight-Cluster. Anweisungen finden Sie unter [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight] or [Bereitstellen eines HDInsight-Clusters][Bereitstellen eines HDInsight-Clusters]. Sie benötigen den Namen des Clusters, um das Lernprogramm abzuschließen.

-   Sie müssen Azure PowerShell auf Ihrer Arbeitsstation installiert und konfiguriert haben. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

**Geschätzter Zeitaufwand:** 30 Minuten

## Themen in diesem Artikel

-   [Warum Pig?][Warum Pig?]
-   [Was lerne ich in diesem Lernprogramm?][Was lerne ich in diesem Lernprogramm?]
-   [Identifizieren der zu analysierenden Daten][Identifizieren der zu analysierenden Daten]
-   [Verständnis von Pig Latin][Verständnis von Pig Latin]
-   [Übermitteln von Pig-Aufträgen in PowerShell][Übermitteln von Pig-Aufträgen in PowerShell]
-   [Übermitteln von Pig-Aufträgen mit dem .NET SDK][Übermitteln von Pig-Aufträgen mit dem .NET SDK]
-   [Nächste Schritte][Nächste Schritte]

## <span id="usage"></span></a>Warum Pig?

Die Verarbeitung großer Datenmengen mit relationalen Datenbanken und Statistik-/Visualisierungslösungen ist oft schwierig. Aufgrund der großen Datenmengen und des benötigten Rechenaufwands wird oft parallele Software auf Hunderten oder Tausenden von Servern benötigt, um diese Daten in einem angemessenen Zeitraum zu verarbeiten. Hadoop bietet ein *MapReduce*-Framework für die Erstellung von Anwendungen zur parallelen Verarbeitung großer Mengen strukturierter und unstrukturierter Daten in großen Rechnerclustern auf höchst zuverlässige und fehlertolerante Weise.

![HDI.Pig.Architecture][HDI.Pig.Architecture]

[Apache *Pig*][Apache Pig] bietet eine Abstraktionsebene über dem Java-basierten MapReduce-Framework, und Benutzer können ohne Java- oder MapReduce-Kenntnisse Daten analysieren. Pig ist eine Plattform zum Analysieren großer Datensätze mit einer leicht zu bedienenden Sprache namens *Pig Latin*, einer sogenannten Datenflusssprache. Pig beschleunigt die Erstellung von Mapper- und Reducer-Programmen. Es sind keinerlei Java-Kenntnisse erforderlich. Außerdem können Sie Java-Code mit Pig kombinieren. Viele komplexe Algorithmen lassen sich in weniger als fünf Zeilen mit gut lesbarem Pig-Code schreiben.

Pig Latin-Anweisungen verwenden den folgenden allgemeinen Ablauf:

-   **Laden**: Die zu verändernden Daten werden aus dem Dateisystem gelesen
-   **Transformation**: Manipulation der Daten
-   **Ablage oder Speicherung**: Ausgabe der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

Weitere Informationen zu Pig Latin finden Sie unter [Pig Latin-Referenzhandbuch 1][Pig Latin-Referenzhandbuch 1] und [Pig Latin-Referenzhandbuch 2][Pig Latin-Referenzhandbuch 2].

## <span id="what"></span></a>Was lerne ich in diesem Lernprogramm?

In diesem Lernprogramm werden Sie eine Apache-Protokolldatei (*sample.log*) analysieren, um die Anzahl der Protokollebenen (INFO, DEBUG, TRACE usw.) zu ermitteln. In den folgenden zwei Abbildungen finden Sie eine visuelle Darstellung der Lernziele dieses Artikels. Die erste Abbildung zeigt einen Ausschnitt der sample.log-Datei.

![Dateiausschnitt][Dateiausschnitt]

Die zweite Abbildung veranschaulicht den Fluss und die Transformation der Daten beim Durchlaufen des Pig-Codes im Skript:

![HDI.PIG.Data.Transformation][HDI.PIG.Data.Transformation]

Der Pig-Job, den Sie in diesem Lernprogramm erstellen, folgt demselben Fluss.

## <span id="data"></span></a>Identifizieren der zu analysierenden Daten

HDInsight verwendet Azure-Blobspeicher als Standard-Dateisystem für Hadoop-Cluster. Einige Beispiel-Datendateien werden im Rahmen der Clusterbereitstellung zum Blobspeicher hinzugefügt. Sie können diese Datendateien zum Ausführen von Hive-Abfragen auf dem Cluster verwenden. Bei Bedarf können Sie auch eigene Datendateien in das Blob-Speicherkonto hochladen, dem das Cluster zugeordnet ist. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight][Hochladen von Daten in HDInsight]. Weitere Informationen zur Verwendung des Azure-Blobspeichers mit HDInsight finden Sie unter [Verwenden des Azure Blobspeichers mit HDInsight][Verwenden des Azure Blobspeichers mit HDInsight].

Die Syntax für den Zugriff auf die Dateien im Blobspeicher ist wie folgt:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] In der HDInsight-Clusterversion 3.0 wird nur die *wasb://*-Syntax unterstützt. Die ältere *asv://*-Syntax wird in HDInsight-Clustern der Versionen 2.1 und 1.6 unterstützt, aber nicht in den Clusterversionen 3.0 und höher.

Dateien im Standard-Dateisystemcontainer sind ebenfalls unter den folgenden URIs aus HDInsight verfügbar (mit sample.log als Beispiel. Dies ist die Datendatei, die für dieses Lernprogramm verwendet wird):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

Wenn Sie direkt aus dem Speicherkonto auf die Datei zugreifen möchten, lautet der Blob-Name für die Datei:

    example/data/sample.log

Dieser Artikel verwendet eine im HDInsight-Cluster mitgelieferte *log4j*-Musterdatei, die unter *\\example\\data\\sample.log* liegt. Ein Beispiel zum Hochladen Ihrer eigenen Datendateien finden Sie unter [Hochladen von Daten nach HDInsight][Hochladen von Daten in HDInsight].

## <span id="understand"></span></a> Verständnis von Pig Latin

In diesem Abschnitt werden Sie einige Pig Latin-Anweisungen und die Ausgabe nach deren Ausführung betrachten. Im nächsten Abschnitt werden Sie die Pig-Anweisungen in PowerShell ausführen, um die Beispiel-Protokolldatei zu analysieren. Die einzelnen Pig Latin-Anweisungen müssen direkt auf dem HDInsight-Cluster ausgeführt werden.

1.  Aktivieren Sie Remotedesktop für das HDInsight-Cluster anhand der Anweisungen in [Verbinden mit HDInsight-Clusters über RDP][Verbinden mit HDInsight-Clusters über RDP]. Melden Sie sich beim Clusterknoten an und klicken Sie auf dem Desktop auf **Hadoop-Befehlszeile**.

2.  Navigieren Sie in der Befehlszeile zum Installationsverzeichnis von **Pig**. Geben Sie Folgendes ein:

        C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3.  Geben Sie in der Befehlszeile *pig* ein und drücken Sie die EINGABETASTE, um zur *grunt*-Shell zu gelangen.

        C:\apps\dist\pig-<version>\bin>pig
        ...
        grunt>  

4.  Geben Sie Folgendes ein, um Daten aus der Beispieldatei in das Dateisystem zu laden und anschließend die Ergebnisse anzuzeigen:

        grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
        grunt> DUMP LOGS;

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

5.  Betrachten Sie die einzelnen Zeilen der Datendatei und achten Sie auf die 6 Protokollebenen:

        grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

6.  Filtern Sie die Zeilen ohne Übereinstimmung heraus und zeigen Sie das Ergebnis an. Damit schließen Sie die leeren Zeilen aus.

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

7.  Gruppieren Sie alle Protokollebenen in eigene Zeilen und zeigen Sie das Ergebnis an:

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

8.  Zählen Sie die Vorkommnisse der Protokollebenen für jede Gruppe und zeigen Sie das Ergebnis an:

        grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        grunt> DUMP FREQUENCIES;

    Die Ausgabe sieht in etwa wie folgt aus:

        (INFO,96)
        (WARN,11)
        (DEBUG,434)
        (ERROR,6)
        (FATAL,2)
        (TRACE,816)

9.  Sortieren Sie die Häufigkeiten in absteigender Reihenfolge und zeigen Sie das Ergebnis an:

        grunt> RESULT = order FREQUENCIES by COUNT desc;
        grunt> DUMP RESULT;   

    Die Ausgabe sieht in etwa wie folgt aus:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <span id="powershell"></span></a>Übermitteln von Pig-Aufträgen in PowerShell

Dieser Abschnitt enthält Anweisungen für PowerShell-Cmdlets. Zuvor müssen Sie jedoch Ihre lokale Umgebung einrichten und die Verbindung zu Azure konfigurieren. Details finden Sie unter [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight] oder [Verwalten von HDInsight mit PowerShell][Verwalten von HDInsight mit PowerShell].

**So führen Sie Pig Latin in der PowerShell aus**

1.  Öffnen Sie Windows PowerShell ISE. Geben Sie im Windows 8-Startbildschirm **PowerShell\_ISE** ein und klicken Sie auf **Windows PowerShell ISE**. Weitere Informationen finden Sie unter [Starten von Windows PowerShell unter Windows 8 und Windows][Starten von Windows PowerShell unter Windows 8 und Windows].

2.  Führen Sie im unteren Bereich den folgenden Befehl aus, um eine Verbindung mit Ihrem Azure-Abonnement herzustellen:

        Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Dieses Verfahren zum Hinzufügen einer Abonnementverbindung läuft nach einer bestimmten Zeit ab. Daher müssen Sie das Cmdlet nach 12 Stunden erneut ausführen.

    > [WACOM.NOTE] Wenn Sie über mehrere Azure-Abonnements verfügen und nicht das Standard-Abonnement verwenden möchten, wählen Sie das aktuelle Abonnement mithilfe des Cmdlets **Select-AzureSubscription** aus.

3.  Kopieren Sie die folgenden Zeilen und fügen Sie sie in den Skriptbereich ein:

        $clusterName = "<HDInsightClusterName>"     #Specify the cluster name
        $statusFolder = "/tutorials/usepig/status"  #Specify the folder to dump results

    Das Skript erstellt den angegebenen Statusordner, falls dieser noch nicht existiert.

4.  Fügen Sie die folgenden Zeilen am Ende des Skriptbereichs ein. Diese Zeilen definieren die Pig Latin-Abfragezeichenfolge und erstellen eine Pig-Auftragsdefinition:

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

    Mit dem Parameter **-File** können Sie eine Pig-Skriptdatei unter HDFS angeben. Die Kennzeichnung **-StatusFolder** gibt den Ordner für das Standard-Fehlerprotokoll und die Standard-Ausgabedatei an.

5.  Fügen Sie die folgenden Zeilen zur Übermittlung des Pig-Auftrags ein:

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

6.  Fügen Sie die folgenden Zeilen ein, um auf den Abschluss des Pig-Auftrags zu warten:

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Fügen Sie die folgenden Zeilen ein, um die Ausgabe des Pig-Auftrags auszugeben:

        # Print the standard error and the standard output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] Eines der Get-AzureHDInsightJobOut-Cmdlet ist im folgenden Screenshot auskommentiert, um die Ausgabe abzukürzen.

8.  Drücken Sie **F5**, um das Skript auszuführen:

    ![HDI.Pig.PowerShell][HDI.Pig.PowerShell]

    Die Pig-Aufgabe berechnet die Häufigkeiten der einzelnen Protokollebenen.

## <span id="sdk"></span></a>Übermitteln von Pig-Aufträgen mit dem .NET SDK

Führen Sie die hier aufgelisteten Schritte aus, um einen Pig-Job mit einer C#-Anwendung zu übermitteln. Anweisungen zur Entwicklung von C#-Anwendungen für die Übermittlung von Hadoop-Aufträgen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][Programmgesteuerte Übermittlung von Hadoop-Aufträgen].

1.  Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats][Erstellen eines selbstsignierten Zertifikats].

2.  Erstellen Sie eine Visual Studio-Konsolenanwendung und installieren Sie das HDInsight-Paket. Klicken Sie im Menü Extras auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Geben Sie in der Befehlszeile Folgendes ein:

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

3.  Doppelklicken Sie auf Program.cs und fügen Sie den folgenden Code ein, um einen Pig-Job zu übermitteln. Setzen Sie Werte für die Variablen ein.

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

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten in HDInsight]
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs][Programmgesteuerte Übermittlung von Hadoop-Aufträgen]
-   [Verwenden von Hive mit HDInsight][Verwenden von Hive mit HDInsight]

  [Apache Pig]: http://pig.apache.org/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Bereitstellen eines HDInsight-Clusters]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Warum Pig?]: #usage
  [Was lerne ich in diesem Lernprogramm?]: #what
  [Identifizieren der zu analysierenden Daten]: #data
  [Verständnis von Pig Latin]: #understand
  [Übermitteln von Pig-Aufträgen in PowerShell]: #powershell
  [Übermitteln von Pig-Aufträgen mit dem .NET SDK]: #sdk
  [Nächste Schritte]: #nextsteps
  [HDI.Pig.Architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png
  [Pig Latin-Referenzhandbuch 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Pig Latin-Referenzhandbuch 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Dateiausschnitt]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Hochladen von Daten in HDInsight]: ../hdinsight-upload-data/
  [Verwenden des Azure Blobspeichers mit HDInsight]: ../hdinsight-use-blob-storage/
  [Verbinden mit HDInsight-Clusters über RDP]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Verwalten von HDInsight mit PowerShell]: ../hdinsight-administer-use-powershell/
  [Starten von Windows PowerShell unter Windows 8 und Windows]: http://technet.microsoft.com/de-de/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [Programmgesteuerte Übermittlung von Hadoop-Aufträgen]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [Erstellen eines selbstsignierten Zertifikats]: http://go.microsoft.com/fwlink/?LinkId=511138
  [Verwenden von Hive mit HDInsight]: ../hdinsight-use-hive/
