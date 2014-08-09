<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Pig with HDInsight" pageTitle="Use Pig with HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Pig with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Verwenden von Pig mit HDInsight
===============================

[Apache *Pig*](http://pig.apache.org/) ist eine Skriptsprache für *MapReduce*-Aufgaben als Alternative zu Java-Code. In diesem Lernprogramm führen Sie Pig Latin-Anweisungen in der PowerShell aus, um eine Apache log4j-Protokolldatei zu analysieren und erzeugen Ausgaben mit verschiedenen Datenabfragen. Dieses Lernprogramm demonstriert die Vorzüge von Pig und dessen Möglichkeiten zur Vereinfachung von MapReduce-Aufgaben.

Die Pig-Skriptsprache heißt *Pig Latin*. Pig Latin-Anweisungen verwenden den folgenden allgemeinen Ablauf:

-   **Laden**: Die zu verändernden Daten werden aus dem Dateisystem gelesen
-   **Transformation**: Manipulation der Daten
-   **Ablage oder Speicherung**: Ausgabe der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

Weitere Informationen zu Pig Latin finden Sie unter [Pig Latin-Referenzhandbuch 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) und [Pig Latin-Referenzhandbuch 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

**Voraussetzungen**

Beachten Sie die folgenden Voraussetzungen, bevor Sie mit diesem Artikel beginnen:

-   Ein Azure HDInsight-Cluster. Anweisungen finden Sie unter [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/) or [Bereitstellen eines HDInsight-Clusters](/de-de/manage/services/hdinsight/provision-hdinsight-clusters/).
-   Installieren und konfigurieren Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).

**Geschätzter Zeitaufwand:** 30 Minuten

Themen in diesem Artikel
------------------------

-   [Der Pig-Anwendungsfall](#usage)
-   [Upload von Daten in einen Azure-Blob-Speicher](#uploaddata)
-   [Verständnis von Pig Latin](#understand)
-   [Ausführen von Pig Latin in der PowerShell](#powershell)
-   [Nächste Schritte](#nextsteps)

Der Pig-Anwendungsfall
----------------------

Datenbanken sind praktisch für kleine Mengen von Daten und Abfragen mit niedriger Latenz. Für große Datenmengen und Datensätze im Terabyte-Bereich sind SQL-Datenbanken jedoch keine ideale Lösung. Mit steigender Datenbanklast und abnehmender Leistung müssen Datenbankadministratoren jedoch normalerweise mehr Hardware anschaffen.

Normalerweise schreiben alle Anwendungen Fehler, Ausnahmen und sonstige Probleme in Protokolldateien, und Administratoren können anhand dieser Daten Probleme analysieren und bestimmte Metriken generieren. Diese Protokolldateien sind oft recht groß und enthalten umfangreiche Daten, die verarbeitet und analysiert werden müssen.

Protokolldateien sind ein gutes Beispiel für große Datenmengen. Die Verarbeitung großer Datenmengen mit relationalen Datenbanken und Statistik-/Visualisierungslösungen ist oft schwierig. Aufgrund der großen Datenmengen und des benötigten Rechenaufwands wird oft parallele Software auf Hunderten oder Tausenden von Servern benötigt, um diese Daten in einem angemessenen Zeitraum zu verarbeiten. Hadoop bietet ein MapReduce-Framework für die Erstellung von Anwendungen zur parallelen Verarbeitung großer Mengen strukturierter und unstrukturierter Daten in großen Rechnerclustern auf höchst zuverlässige und fehlertolerante Weise.

Pig beschleunigt die Erstellung von Mapper- und Reducer-Programmen. Sie benötigten dazu weder Java noch Code-Textbausteine. Außerdem können Sie Java-Code mit Pig kombinieren. Viele komplexe Algorithmen lassen sich in weniger als fünf Zeilen mit gut lesbarem Pig-Code schreiben.

Die folgenden beiden Abbildungen zeigen eine grafische Darstellung der Lerninhalte dieses Artikels. Die Abbildungen zeigen eine repräsentative Probe des Datensatzes, um den Fluss und die Transformation der Daten beim Durchlaufen des Pig-Codes im Skript zu veranschaulichen. Die erste Abbildung zeigt einen Ausschnitt der log4j-Datei:

![Dateiausschnitt](./media/hdinsight-use-pig/HDI.wholesamplefile.png)

Die zweite Abbildung zeigt die Datentransformation:

![HDI.PIG.Data.Transformation](./media/hdinsight-use-pig/HDI.DataTransformation.gif)



Upload der Datendatei in den Blob-Speicher
------------------------------------------

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Weitere Informationen finden Sie unter [Use Azure Blob Storage with HDInsight](/de-de/manage/services/hdinsight/howto-blob-store/) (Verwenden von Azure Blob-Speicher mit HDInsight, in englischer Sprache). In diesem Artikel verwenden Sie eine log4j-Beispieldatei, die mit dem HDInsight-Cluster unter *\\example\\data\\sample.log* verteilt wird. Informationen zum Hochladen von Daten finden Sie unter [Upload Data to HDInsight](/de-de/manage/services/hdinsight/howto-upload-data-to-hdinsight/) (Hochladen von Daten zu HDInsight, in englischer Sprache).

Mit der folgenden Syntax können Sie auf Dateien zugreifen:

     	wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

Beispiel:

     	wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Ersetzen Sie *mycontainer* durch den Containernamen und *mystorage* durch den Namen des Blob-Speicherkontos.

Da die Datei im Standarddateisystem gespeichert ist, können Sie auch auf folgende Weise darauf zugreifen:

     	wasb:///example/data/sample.log
        /example/data/sample.log

Verständnis von Pig Latin
-------------------------

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

Ausführen von Pig Latin in der PowerShell
-----------------------------------------

Dieser Abschnitt enthält Anweisungen für PowerShell-Cmdlets. Zuvor müssen Sie jedoch Ihre lokale Umgebung einrichten und die Verbindung zu Azure konfigurieren. Details finden Sie unter [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/) oder [Administer HDInsight using PowerShell](/en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/) (Verwalten von HDInsight mit PowerShell, in englischer Sprache).

**So führen Sie Pig Latin in der PowerShell aus**

1.  Öffnen Sie ein Azure PowerShell-Konsolenfenster. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

         Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Legen Sie die Variable im folgenden Skript fest und führen es aus:

         $clusterName = "<HDInsightClusterName>" 

4.  Führen Sie die folgenden Befehle aus, um die Pig Latin-Abfragezeichenfolge zu definieren:

         # Pig-Aufgabendefinition erstellen
         $0 = '$0';
         $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                         "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                         "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                         "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                         "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                         "RESULT = order FREQUENCIES by COUNT desc;" +
                         "DUMP RESULT;" 
            
         $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

    Mit dem Parameter -File können Sie eine Pig-Skriptdatei unter HDFS angeben.

5.  Führen Sie das folgende Skript aus, um die Pig-Aufgabe zu übermitteln:

         # Pig-Aufgabe übermitteln
         Select-AzureSubscription $subscriptionName
         $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

6.  Führen Sie das folgende Skript aus, um auf den Abschluss der Pig-Aufgabe zu warten:

         # Auf Abschluss der Pig-Aufgabe warten
         Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Führen Sie das folgende Skript aus, um die Ausgabe der Pig-Aufgabe auszugeben:

         # Standardfehler und -Ausgabe der Pig-Aufgabe ausgeben.
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    ![HDI.Pig.PowerShell](./media/hdinsight-use-pig/hdi.pig.powershell.png)

    Die Pig-Aufgabe berechnet die Häufigkeiten der einzelnen Protokollebenen.



Nächste Schritte
----------------

Pig ist hilfreich für die Datenanalyse, aber andere in HDInsight enthaltene Sprachen sind möglicherweise ebenfalls interessant für Sie. Hive bietet eine SQL-ähnliche Abfragesprache, mit der Sie Daten in HDInsight schnell und einfach abfragen können, und MapReduce-Aufgaben in Java eignen sich gut für komplexe Datenanalysen. Weitere Informationen finden Sie unter den folgenden Links:

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Upload data to HDInsight (Hochladen von Daten zu HDInsight, in englischer Sprache)](/de-de/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/de-de/manage/services/hdinsight/using-hive-with-hdinsight/)

[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: /de-de/manage/services/hdinsight/howto-blob-store/
[hdinsight-upload-data]: /de-de/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-getting-started]: /de-de/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-admin-powershell]: /en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/

[hdinsight-using-hive]: /de-de/manage/services/hdinsight/using-hive-with-hdinsight/

[hdinsight-provision]: /de-de/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-configure-powershell]: /de-de/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 

[Powershell-install-configure]: /de-de/documentation/articles/install-configure-powershell/

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png  
