<properties  linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hive with HDInsight" pageTitle="Use Hive with HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hive with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Verwenden von Hive mit HDInsight

[Apache Hive][1] ermöglicht das Ausführen von MapReduce-Jobs über eine
SQL-ähnliche Skriptsprache namens *HiveQL*, mit der große Datenmengen zusammengefasst, abgefragt und analysiert werden können. In diesem Artikel verwenden Sie HiveQL zur Datenabfrage in einer Apache log4j-Protokolldatei und zur Erstellung von Berichten zu grundlegenden Statistiken.

**Voraussetzungen:**

* Sie müssen ein **HDInsight-Cluster** bereitgestellt haben. Eine
  Anleitung zur entsprechenden Vorgehensweise mit dem Azure-Portal
  finden Sie unter [Erste Schritte mit DInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/).Informationen zu den verschiedenen anderen Arten, auf die solche Cluster erstellt werden können, finden Sie unter [Bereitstellen von
  HDInsight-Clustern](/en-us/manage/services/hdinsight/provision-hdinsight clusters/).

* Sie müssen **Azure PowerShell** auf Ihrer Arbeitsstation installiert
  haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter
  [Installieren und Konfigurieren von Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

**Geschätzter Zeitaufwand:** 30 Minuten

## Themen in diesem Artikel

* [Der Hive-Anwendungsbereich](#usage)
* [Hochladen von Datendateien in Azure Blob-Speicher](#uploaddata)
* [Ausführen von Hive-Abfragen mit PowerShell](#runhivequeries)
* [Nächste Schritte](#nextsteps)

## <a id="usage" ></a>Der Hive-Anwendungsbereich

Datenbanken sind hilfreich bei der Verwaltung kleinerer Datensätze, bei denen Abfragen mit geringer Wartezeit möglich sind. Wenn es allerdings um große Datensätze mit Terabytes von Daten geht, sind herkömmliche SQL-Datenbanken häufig nicht die ideale Lösung. Datenbankadministratoren nehmen häufig Aufwärtsskalierungen vor, um diese größeren Datensätze bearbeiten zu können, und kaufen größere Hardware, um der höheren Datenbanklast und der langsameren Leistung zu begegnen.

Hive löst die Probleme mit großen Datenmengen, indem es eine horizontale Skalierung bei der Abfrage großer Datensätze erlaubt. Hive fragt Daten mit MapReduce parallel über mehrere Knoten ab und verteilt die Datenbank mit steigender Last auf immer mehr Hosts.

Außerdem bieten Hive und HiveQL bei der Datenabfrage eine Alternative zum Schreiben von MapReduce-Jobs in Java. Diese besteht in einem einfachen SQL-ähnlichen Wrapper, der das Schreiben von Abfragen in HiveQL erlaubt. Diese werden dann von HDInsight für Sie in MapReduce kompiliert und auf dem Cluster ausgeführt.

Hive ermöglicht Programmierern, die mit dem MapReduce-Framework vertraut sind, auch die Einbindung benutzerdefinierter Mapper und Reducer, um wesentlich ausgefeiltere Analysen durchzuführen, die von den integrierten Funktionen der Sprache HiveQL eventuell nicht unterstützt werden.

Hive eignet sich hervorragend für die Stapelverarbeitung großer Mengen unveränderter Daten (wie in Webprotokollen). Es ist nicht für Transaktionsanwendungen geeignet, für die sehr schnelle Reaktionszeiten erforderlich sind, wie z. B. Datenbankverwaltungssysteme. Hive bietet optimierte Skalierbarkeit (mehr Computer können dynamisch zum Hadoop-Cluster hinzugefügt werden), Erweiterbarkeit (innerhalb des MapReduce-Frameworks und mit anderen Programmierschnittstellen) und Fehlertoleranz. Die Wartezeit wurde bei der Entwicklung nicht als wichtiger Aspekt betrachtet.

Im Allgemeinen speichern Anwendungen Fehler, Ausnahmen und andere codierte Probleme in einer Protokolldatei, und Administratoren verwenden die Daten in den Protokolldateien, um die auftretenden Probleme zu untersuchen und Metriken zu entwickeln, die für Fehler oder andere Probleme, z. B. hinsichtlich der Leistung, relevant sind. Diese Protokolldateien werden normalerweise recht groß und enthalten sehr viele Daten, die in der Anwendung verarbeitet und auf Relevanz überprüft werden müssen.

Deshalb sind Protokolldateien ein typisches Beispiel für große Datenmengen. HDInsight stellt ein Hive-Data Warehouse-System bereit, das eine einfache Datenzusammenfassung, Ad-hoc-Abfragen und eine Analyse der großen Datensätze ermöglicht, die in Hadoop-kompatiblen Dateisystemen wie Azure Blob-Speicher gespeichert sind.

## <a id="uploaddata" ></a>Hochladen von Datendateien in Azure Blob-Speicher

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Weitere Informationen erhalten Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/en-us/manage/services/hdinsight/howto-blob-store).

In diesem Beitrag verwenden Sie eine über das HDInsight-Cluster verteilte log4j-Musterdatei, die unter *\example\data\sample.log* gespeichert ist. Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens `[LOG LEVEL]` befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt. Beispiel:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Verwenden Sie für den Zugriff auf die Dateien die folgende Syntax:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Beispiel:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Ersetzen Sie *mycontainer* durch den Container-Namen und *mystorage* durch den Namen des Blob-Speicherkontos.

Da die Datei im Standard-Dateisystem gespeichert ist, können Sie auch auf folgendem Weg darauf zugreifen.

    wasb:///example/data/sample.log
    /example/data/sample.log

Wenn Sie Ihre eigenen log4j-Dateien erstellen möchten, verwenden Sie dafür das Protokollierungsprogramm [Apache Log4j][2]. Informationen zum Hochladen von Daten in Azure Blob-Speicher erhalten Sie unter [Hochladen von Daten in HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

## <a id="runhivequeries" ></a> Ausführen von Hive-Abfragen mit PowerShell

Im letzten Abschnitt haben Sie eine log4j-Datei namens sample.log in den Standard-Dateisystem-Container hochgeladen. In diesem Abschnitt führen Sie HiveQL aus, um eine Hive-Tabelle zu erstellen, Daten in diese Hive-Tabelle zu laden und diese Daten dann abzufragen, um herauszufinden, wie viele Fehlerprotokolle vorliegen.

Dieser Beitrag stellt die Informationen bereit, die Sie für die Verwendung von Azure-PowerShell-Cmdlets zur Ausführung einer Hive-Abfrage benötigen. Bevor Sie diesen Abschnitt durcharbeiten, müssen Sie zuerst die lokale Umgebung einrichten und die Verbindung mit Azure konfigurieren. Dies wird im Abschnitt **Voraussetzungen** am Anfang dieses Themas erklärt.

Hive-Abfragen können in PowerShell entweder über das Cmdlet
**Start-AzureHDInsightJob** oder über das Cmdlet **Invoke-Hive**
ausgeführt werden.

**Ausführen der Hive-Abfragen mit Start-AzureHDInsightJob**

1.  Öffnen Sie ein Azure PowerShell-Konsolenfenster. Die Informationen
    dazu finden Sie unter [Installieren und Konfigurieren von Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).
2.  Führen Sie den folgenden Befehl aus, um eine Verbindung zu Ihrem
    Azure-Abonnement herzustellen:
    
         Add-AzureAccount
    
    Sie werden aufgefordert, die Anmeldeinformationen für Ihr
    Azure-Konto einzugeben.

3.  Legen Sie die Variablen im folgenden Skript fest, und führen Sie es
    aus:

         # Den Azure-Abonnementsnamen, das Azure-Speicherkonto und den Container angeben, die für das HDInsight-Standard-Dateisystem verwendet werden.
         $subscriptionName = "<SubscriptionName>"
         $storageAccountName = "<AzureStorageAccountName>"
         $containerName = "<AzureStorageContainerName>"

         # Den Namen des HDInsight-Clusters angeben, in dem Sie den Hive-Auftrag ausführen möchten
         $clusterName = "<HDInsightClusterName>"

4.  Führen Sie das folgende Skript aus, um die HiveQL-Abfragen zu
    definieren:

         # HiveQL-Abfragen

         # Die interne Tabellenoption verwenden. 
         $queryString = "DROP TABLE log4jLogs;" +
                        "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                        "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

         # Die externe Tabellenoption verwenden. 
         $queryString = "DROP TABLE log4jLogs;" +
                         "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                         "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
    
    Der Befehl LOAD DATA HiveQL führt zur Verschiebung der Datendatei in
    den Ordner \hive\warehouse\. Mit dem Befehl DROP TABLE werden die
    Tabelle und die Datendatei gelöscht. Wenn Sie die interne
    Tabellenoption verwenden und das Skript erneut ausführen möchten,
    müssen Sie die Datei sample.log erneut hochladen. Wenn Sie die
    Datendatei behalten möchten, müssen Sie den Befehl CREATE EXTERNAL
    TABLE wie im Skript gezeigt verwenden.
    
    Sie können die externe Tabelle auch verwenden, wenn die Datendatei
    sich in einem anderen Container oder Speicherkonto befindet.
    
    Verwenden Sie, wenn Sie das Skript erneut ausführen und die Tabelle
    log4jlogs bereits existiert, zuerst den Befehl DROP TABLE.

5.  Führen Sie das folgende Skript aus, um eine Hive-Jobdefinition zu
    erstellen:

         # Eine Hive-Jobdefinition erstellen 
         $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 
    
    Sie kĂ¶nnen auch den Schalter "-File" verwenden, um eine
    HiveQL-Skriptdatei auf HDFS anzugeben.

6.  Führen Sie das folgende Skript aus, um den Hive-Job zu senden:

         # Den Job an das Cluster senden 
         Select-AzureSubscription $subscriptionName
         $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Führen Sie das folgende Skript aus, um auf die Ausführung des
    Hive-Jobs zu warten:

         # Auf die Ausführung des Hive-Jobs warten
         Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Führen Sie das folgende Skript aus, um die Standardausgabe zu
    drucken:


        # Den Standardfehler und die Standardausgabe des Hive-Jobs drucken.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

 ![HDI.HIVE.PowerShell](./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png)     

     Das Ergebnis ist:
    
    	[ERROR] 3

**Senden von Hive-Abfragen mit Invoke-Hive**

1.  Öffnen Sie ein Azure PowerShell-Konsolenfenster. 
2.  Führen Sie den folgenden Befehl aus, um eine Verbindung zu Ihrem
    Azure-Abonnement herzustellen:
    
         Add-AzureAccount
    
    Sie werden aufgefordert, die Anmeldeinformationen für Ihr
    Azure-Konto einzugeben.

3.  Legen Sie die Variable fest, und führen Sie sie aus:
    
         $clusterName = "<HDInsightClusterName>"

4.  Führen Sie das folgende Skript aus, um HiveQL-Abfragen zu erzeugen:
    
         Use-AzureHDInsightCluster $clusterName 
         $response = Invoke-Hive -Query @"
             SELECT * FROM hivesampletable
                 WHERE devicemake LIKE "HTC%"
                 LIMIT 10; 
         "@
        	
         Write-Host $response
    
    Die Ausgabe ist:
    
    ![PowerShell
    Invoke-Hive-Ausgabe](./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png)
    
    Für längere HiveQL-Abfragen wird empfohlen, PowerShell Here-Strings
    oder die HiveQL-Skriptdatei zu verwenden. Die folgenden Beispiele
    zeigen, wie das Cmdlet Invoke-Hive zur Ausführung einer
    HiveQL-Skriptdatei verwendet wird. Die HiveQL-Skriptdatei muss auf
    WASB hochgeladen werden.
    
         Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"
    
    Weitere Informationen zu Here-Strings erhalten Sie unter [Verwenden von Windows PowerShell-Here-Strings][3].

## <a id="nextsteps" ></a>Nächste Schritte

Während Hive die Datenabfrage mit einer SQL-ähnlichen Abfragesprache erleichtert, bieten andere in HDInsight verfügbare Komponenten ergänzende Funktionen wie Datenverschiebung und -transformation. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
* [Analyze flight delay data using HDInsight (Analyse von
  Flugverzögerungsdaten mit
  HDInsight)](/en-us/documentation/articles/hdinsight-analyze-flight-delay-data/)
* [Verwenden von Oozie mit
  HDInsight](/en-us/documentation/articles/hdinsight-use-oozie/)
* [Programmgesteuerte Ăśbermittlung von
  Hadoop-Jobs](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
* [Hochladen von Daten in
  HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
* [Verwenden von Pig mit
  HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
* [SDK-Dokumentation für Azure HDInsight][4]



[1]: http://hive.apache.org/
[2]: http://en.wikipedia.org/wiki/Log4j
[3]: http://technet.microsoft.com/en-us/library/ee692792.aspx
[4]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx