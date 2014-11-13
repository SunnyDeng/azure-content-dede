<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Verwenden von Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Erfahren Sie, wie Sie Hive mit HDInsight verwenden k&ouml;nnen. Sie verwenden eine Protokolldatei als Eingabe f&uuml;r eine HDInsight-Tabelle und verwenden anschlie&szlig;end HiveQL, um die Daten abzufragen und einfache Statistiken zu erstellen." metaCanonical="" services="hdinsight" documentationCenter="" title="Verwenden von Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Verwenden von Hive mit Hadoop in HDInsight

[Apache Hive][Apache Hive] unterstützt MapReduce-Jobs mit einer SQL-ähnlichen Skriptsprache namens *HiveQL*. Hive ist ein Data Warehouse-System für Hadoop und ermöglicht Zusammenfassung, Abfrage und Analyse großer Datenmengen. In diesem Artikel verwenden Sie HiveQL, um eine einfache Datendatei abzufragen, die als Teil der HDInsight-Clusterbereitstellung mitgeliefert wird.

**Voraussetzungen:**

-   Sie benötigen einen bereitgestellten **HDInsight-Cluster**. Eine Anleitung zur entsprechenden Vorgehensweise mit dem Azure-Portal finden Sie unter [Erste Schritte mit HDInsight][Erste Schritte mit HDInsight]. Informationen zu den verschiedenen anderen Arten, auf die solche Cluster erstellt werden können, finden Sie unter [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern].

-   Sie müssen **Azure PowerShell** auf Ihrer Arbeitsstation installiert haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].

**Geschätzter Zeitaufwand:** 30 Minuten

## Themen in diesem Artikel

-   [Der Hive-Anwendungsbereich][Der Hive-Anwendungsbereich]
-   [Hochladen von Daten für Hive-Tabellen][Hochladen von Daten für Hive-Tabellen]
-   [Ausführen von Hive-Abfragen mit PowerShell][Ausführen von Hive-Abfragen mit PowerShell]
-   [Verwenden von Tez zur Verbesserung der Leistung][Verwenden von Tez zur Verbesserung der Leistung]
-   [Nächste Schritte][Nächste Schritte]

## <span id="usage"></span></a>Der Hive-Anwendungsbereich

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive projiziert eine Struktur auf größtenteils unstrukturierte Daten (dem Haupt-Anwendungsbereich von Hadoop) und ermöglicht anschließend Abfragen über die Daten per HiveQL. Hive bietet eine Abstraktionsebene über dem Java-basierten MapReduce-Framework, und Benutzer können ohne Java- oder MapReduce-Kenntnisse Daten abfragen. HiveQL bietet einen einfachen SQL-ähnlichen Wrapper für Abfragen in HiveQL, die anschließend von HDInsight nach MapReduce kompiliert und auf dem Cluster ausgeführt werden. Weitere Vorteile von Hive:

-   Mit Hive können Programmierer, die mit dem MapReduce-Framework vertraut sind, benutzerdefinierte Mapper und Reducer verwenden, um komplexere Analysen durchzuführen, die mit den integrierten Funktionen von HiveQL eventuell nicht möglich sind.
-   Hive eignet sich hervorragend für die Stapelverarbeitung großer Mengen unveränderter Daten (wie in Webprotokollen). Es ist nicht für Transaktionsanwendungen geeignet, für die sehr schnelle Reaktionszeiten erforderlich sind, wie z. B. Datenbankverwaltungssysteme.
-   Hive bietet optimierte Skalierbarkeit (mehr Computer können dynamisch zum Hadoop-Cluster hinzugefügt werden), Erweiterbarkeit (innerhalb des MapReduce-Frameworks und mit anderen Programmierschnittstellen) und Fehlertoleranz. Die Wartezeit wurde bei der Entwicklung nicht als wichtiger Aspekt betrachtet.

## <span id="uploaddata"></span></a>Hochladen von Daten für Hive-Tabellen

HDInsight verwendet Azure-Blobspeicher als Standard-Dateisystem für Hadoop-Cluster. Einige Beispiel-Datendateien werden im Rahmen der Clusterbereitstellung zum Blobspeicher hinzugefügt. Sie können diese Datendateien zum Ausführen von Hive-Abfragen auf dem Cluster verwenden. Bei Bedarf können Sie auch eigene Datendateien in das Blob-Speicherkonto hochladen, dem das Cluster zugeordnet ist. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight][Hochladen von Daten in HDInsight]. Weitere Informationen zur Verwendung des Azure-Blobspeichers mit HDInsight finden Sie unter [Verwenden des Azure Blobspeichers mit HDInsight][Verwenden des Azure Blobspeichers mit HDInsight].

In diesem Beitrag verwenden Sie eine im HDInsight-Cluster mitgelieferte *log4j*-Musterdatei, die unter *\\example\\data\\sample.log* in Ihrem Blobspeicher-Container liegt. Außerdem generieren Sie Ihre eigenen log4j-Dateien mit dem [Apache Log4j][Apache Log4j]-Protokollierungstool und laden diese Dateien in den Blob-Container hoch. Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens `[LOG LEVEL]` befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt. Beispiel:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Die Protokollebene im obigen Beispiel ist ERROR.

Verwenden Sie die folgende Syntax in Ihrer Anwendung, um auf die Dateien zuzugreifen:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Für die Datei sample.log gilt also die folgende Syntax:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Ersetzen Sie *mycontainer* durch den Namen des Azure-Blob-Containers und *mystorage* durch den Namen des Azure-Speicherkontos.

Da die Datei im Standarddateisystem gespeichert ist, können Sie auch auf folgende Weise darauf zugreifen:

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> Ausführen von Hive-Abfragen mit PowerShell

Im letzten Abschnitt haben Sie die Datei *sample.log* identifiziert, für die Sie nun Hive-Abfragen ausführen werden. In diesem Abschnitt verwenden Sie HiveQL, um eine Hive-Tabelle zu erstellen, Daten in diese Tabelle zu laden und diese Daten dann abzufragen, um die Anzahl der enthaltenen Fehler zu ermitteln.

Dieser Beitrag stellt die Informationen bereit, die Sie für die Verwendung von Azure-PowerShell-Cmdlets zur Ausführung einer Hive-Abfrage benötigen. Bevor Sie diesen Abschnitt durcharbeiten, müssen Sie zuerst die lokale Umgebung einrichten und die Verbindung mit Azure konfigurieren, wie im Abschnitt **Voraussetzungen** am Anfang dieses Themas beschrieben.

Hive-Abfragen können in PowerShell über die Cmdlets **Start-AzureHDInsightJob** und **Invoke-Hive** ausgeführt werden.

**So führen Sie die Hive-Abfragen mit Start-AzureHDInsightJob aus**

1.  Öffnen Sie ein Azure PowerShell-Konsolenfenster. Die Informationen dazu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell].
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

        Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Legen Sie die Variablen im folgenden Skript fest, und führen Sie es aus:

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Führen Sie das folgende Skript aus, um die HiveQL-Abfragen zu definieren. Sie können entweder eine *interne* oder eine *externe* Hive-Tabelle erstellen.

    -   Mit internen Tabellen werden die verwendeten Beispieldaten vom aktuellen Ort in den Ordner \\hive\\warehouse\\\<*Tabellenname\>* verschoben. Wenn Sie eine interne Tabelle löschen, werden die entsprechenden Daten also ebenfalls gelöscht. Wenn Sie interne Tabellen verwenden und das Skript erneut ausführen möchten, müssen Sie die Datei *sample.log* erneut in den Blobspeicher hochladen.
    -   Mit externen Tabellen bleiben die Daten an ihrem Ausgangsort. Externe Tabellen sind also hilfreich, wenn sich die Datendatei in einem anderen Container oder einem anderen Speicherkonto befindet.

            # HiveQL queries
            # Use the internal table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                           "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                           "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                           "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

            # Use the external table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                            "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                            "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    Der DROP TABLE-Befehl löscht Tabelle und Datendatei, falls die Tabelle bereits existiert. Der HiveQL-Befehl LOAD DATA verschiebt die Datendatei von \\example\\data in den Ordner \\hive\\warehouse\\\<*Tabellenname\>*.

5.  Führen Sie das folgende Skript aus, um eine Hive-Jobdefinition zu erstellen:

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    Sie können auch den Schalter "-File" verwenden, um eine HiveQL-Skriptdatei auf HDFS anzugeben.

6.  Führen Sie das folgende Skript aus, um den Hive-Job zu senden:

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Führen Sie das folgende Skript aus, um auf die Ausführung des Hive-Jobs zu warten:

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Führen Sie das folgende Skript aus, um die Standardausgabe zu drucken:

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    Das Ergebnis lautet:

        [ERROR] 3

    Dies bedeutet, dass die Datei *sample.log* drei ERROR-Protokolle enthält.

Bei Bedarf können Sie die Ausgabe Ihrer Abfragen zur weiteren Analyse in Excel importieren. Weitere Hinweise hierzu finden Sie unter [Verbinden von Excel über Power Query mit Hadoop][Verbinden von Excel über Power Query mit Hadoop].

**So senden Sie Hive-Abfragen mit Invoke-Hive**

1.  Öffnen Sie ein Azure PowerShell-Konsolenfenster.
2.  Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

        Add-AzureAccount

    Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

3.  Legen Sie die Variablen im folgenden Skript fest, und führen Sie es aus:

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Verbinden Sie sich mit dem HDInsight-Cluster.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  Führen Sie das folgende Skript aus, um HiveQL-Abfragen zu erzeugen:

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  Geben Sie das Resultat in der Konsole aus.

        # print the output on the console
        Write-Host $response

    Die Ausgabe sieht wie folgt aus:

    ![PowerShell Invoke-Hive-Ausgabe][PowerShell Invoke-Hive-Ausgabe]

    Für längere HiveQL-Abfragen können Sie PowerShell Here-Zeichenfolgen oder HiveQL-Skriptdateien verwenden. Der folgende Ausschnitt zeigt, wie Sie eine HiveQL-Skriptdatei mit dem *Invoke-Hive*-Cmdlet ausführen können. Die HiveQL-Skriptdatei muss auf WASB hochgeladen werden.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Weitere Informationen zu Here-Strings erhalten Sie unter [Verwenden von Windows PowerShell-Here-Strings][Verwenden von Windows PowerShell-Here-Strings].

Bei Bedarf können Sie die Ausgabe Ihrer Abfragen zur weiteren Analyse in Excel importieren. Weitere Hinweise hierzu finden Sie unter [Verbinden von Excel über Power Query mit Hadoop][Verbinden von Excel über Power Query mit Hadoop].

## <span id="usetez"></span></a>Verwenden von Tez zur Verbesserung der Leistung

[Apache Tez][Apache Tez] ist ein Framework, mit dem datenintensive Anwendungen wie Hive wesentlich effizienter ausgeführt und skaliert werden können. In der neuesten Version von HDInsight unterstützt Hive die Ausführung in Tez. Diese Funktion ist momentan standardmäßig deaktiviert und muss aktiviert werden. In zukünftigen Clusterversionen wird diese Version standardmäßig aktiviert sein. Um die Vorteile von Tez zu nutzen, muss der folgende Wert in einer Hive-Abfrage gesetzt sein:

        set hive.execution.engine=tez;
        

Dieser Wert kann pro Abfrage gesetzt werden, indem Sie diesen Befehl am Anfang der Abfrage stellen. Diese Funktion kann auch standardmäßig für ein Cluster aktiviert werden, indem Sie den Konfigurationswert bei der Erstellung des Clusters setzen. Weitere Details finden Sie unter [Bereitstellen von HDInsight-Clustern][Bereitstellen von HDInsight-Clustern].

Die [Hive mit Tez-Designdokumente][Hive mit Tez-Designdokumente] (in englischer Sprache) enthalten weitere Details zu Implementierungen und Konfigurationseinstellungen.

## <span id="nextsteps"></span></a>Nächste Schritte

Während Hive die Datenabfrage mit einer SQL-ähnlichen Abfragesprache erleichtert, bieten andere in HDInsight verfügbare Komponenten ergänzende Funktionen wie Datenverschiebung und -transformation. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Verwenden von Oozie mit HDInsight][Verwenden von Oozie mit HDInsight]
-   [Programmgesteuerte Übermittlung von Hadoop-Jobs][Programmgesteuerte Übermittlung von Hadoop-Jobs]
-   [Verwenden von Pig mit HDInsight][Verwenden von Pig mit HDInsight]
-   [Analysieren von Daten zu Flugverspätungen mit HDInsight][Analysieren von Daten zu Flugverspätungen mit HDInsight]
-   [Dokumentation zum Azure HDInsight SDK][Dokumentation zum Azure HDInsight SDK]
-   [Hochladen von Daten zu HDInsight][Hochladen von Daten in HDInsight]
-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Der Hive-Anwendungsbereich]: #usage
  [Hochladen von Daten für Hive-Tabellen]: #uploaddata
  [Ausführen von Hive-Abfragen mit PowerShell]: #runhivequeries
  [Verwenden von Tez zur Verbesserung der Leistung]: #usetez
  [Nächste Schritte]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [Hochladen von Daten in HDInsight]: ../hdinsight-upload-data/
  [Verwenden des Azure Blobspeichers mit HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Verbinden von Excel über Power Query mit Hadoop]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-connect-excel-power-query/
  [PowerShell Invoke-Hive-Ausgabe]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Verwenden von Windows PowerShell-Here-Strings]: http://technet.microsoft.com/de-de/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [Hive mit Tez-Designdokumente]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Verwenden von Oozie mit HDInsight]: ../hdinsight-use-oozie/
  [Programmgesteuerte Übermittlung von Hadoop-Jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Verwenden von Pig mit HDInsight]: ../hdinsight-use-pig/
  [Analysieren von Daten zu Flugverspätungen mit HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Dokumentation zum Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx
