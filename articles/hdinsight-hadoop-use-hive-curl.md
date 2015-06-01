<properties
   pageTitle="Verwenden von Hadoop Pig in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Pig-Aufträge mithilfe von Curl remote an HDInsight übermitteln."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Ausführen von Hive-Abfragen mit Hadoop in HDInsight mit Curl

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In diesem Dokument erfahren Sie, wie mithilfe von Curl Hive-Abfragen auf einem Hadoop-Cluster in HDInsight ausgeführt werden. 

Curl wird verwendet, um zu veranschaulichen, wie Sie über unformatierte HTTP-Anforderungen zum Ausführen, Überwachen und Abrufen der Ergebnisse der Hive-Abfragen mit HDInsight interagieren können. Dies funktioniert mithilfe der WebHCat REST-API (ehemals Templeton), die von Ihrem HDInsight-Cluster bereitgestellt wird.

> [AZURE.NOTE] Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, jedoch noch nicht mit HDInsight, finden Sie weitere Informationen unter <a href="../hdinsight-hadoop-linux-information/" target="_blank">Was Sie über Hadoop auf Linux-basiertem HDInsight wissen müssen</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Hadoop-Cluster in HDInsight (Linux- oder Windows-basiert)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Ausführen von Hive-Abfragen mit Curl

> [AZURE.NOTE] Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URIs verwendet, um die Anforderungen an den Server zu senden.
> 
> Ersetzen Sie für die Befehle in diesem Abschnitt die Option **BENUTZERNAME** für die Authentifizierung im Cluster durch den Benutzer und die Option **KENNWORT** durch das Kennwort für das Benutzerkonto. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters.
> 
> Die REST-API wird durch <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">Standardauthentifizierung</a> gesichert. Sie sollten Anforderungen immer über HTTPS stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung zum HDInsight-Cluster herstellen können.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Sie sollten eine Antwort erhalten, die der folgenden ähnelt:

        {"status":"ok","version":"v1"}

    Folgende Parameter werden in diesem Befehl verwendet:

    * **-u**: der Benutzername und das Kennwort für die Authentifizierung der Anforderung
    * **-G**: gibt an, dass dies eine GET-Anforderung ist

    Der Anfang der URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, ist für alle Anforderungen identisch. Der Pfad, **/status**, gibt an, dass die Anforderung den Status von WebHCat (auch bekannt als Templeton) für den Server zurückgibt. Sie können mit dem folgenden Befehl auch die Version von Hive anfordern.

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive

    Daraufhin sollte eine Antwort ähnlich der folgenden zurückgegeben werden:

        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}

2. Verwenden Sie Folgendes, um eine neue Tabelle namens **log4jLogs** zu erstellen.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'wasb:///example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+GROUP+BY+t4;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Folgende Parameter werden in diesem Befehl verwendet:

    * **-d**: Da `-G` nicht verwendet wird, verwendet die Anforderung standardmäßig die POST-Methode. Die Option `-d` gibt die Datenwerte an, die mit der Anforderung gesendet werden.

        * **benutzer.name**: Der Benutzer, der den Befehl ausführt.
        
        * **execute**: Die auszuführenden HiveQL-Anweisungen.
        
        * **statusdir**: Das Verzeichnis, in das die Statusangaben für diesen Auftrag geschrieben werden.

    Diese Anweisungen führen die folgenden Aktionen aus.

    * **DROP TABLE**: löscht Tabelle und Datendatei, falls die Tabelle bereits existiert
    
    * **CREATE EXTERNAL TABLE** erstellt eine neue externe Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort.

		> [AZURE.NOTE] Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden.
		>
		> Durch das Ablegen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

    * **ROW FORMAT**: teilt Hive mit, wie die Daten formatiert werden In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
    
    * **STORED AS TEXTFILE LOCATION** teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Informationen mit, dass die Speicherung als Text erfolgt.
    
    * Mit **SELECT** wählen Sie die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.

    > [AZURE.NOTE] Beachten Sie, dass Leerzeichen zwischen HiveQL-Anweisungen bei Curl durch das Zeichen `+` ersetzt werden. Werte in Anführungszeichen, die ein Leerzeichen enthalten, z. B. das Trennzeichen, dürfen nicht durch `+` ersetzt werden.

    Dieser Befehl sollte eine Auftrags-ID zurückgeben, mit der der Status des Auftrags überprüft werden kann.

        {"id":"job_1415651640909_0026"}

3. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen. Ersetzen Sie **JOBID** durch den Wert, der im vorherigen Schritt zurückgegeben wurde. Wenn der Rückgabewert z. B. `{"id":"job_1415651640909_0026"}` lautet, ist die JOBID `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Wenn der Auftrag abgeschlossen ist, wird der Status "SUCCEEDED" angezeigt.

    > [AZURE.NOTE] Diese Curl-Anforderung gibt ein JSON-Dokument mit Informationen zum Auftrag zurück. Mithilfe von "jq" wird nur der Statuswert abgerufen. 

4. Sobald der Status des Auftrags zu **SUCCEEDED** wechselt, können Sie die Ergebnisse des Auftrags aus dem Azure Blob-Speicher abrufen. Der mit der Abfrage übergebene Parameter `statusdir` enthält den Speicherort der Ausgabedatei. In diesem Fall **wasb:///example/curl**. Diese Adresse speichert die Ausgabe des Auftrags im Verzeichnis **example/curl** des Standardspeichercontainers, der von Ihrem HDInsight-Cluster verwendet wird.

    Sie können diese Dateien unter <a href="../xplat-cli/" target="_blank">Installieren und Konfigurieren der plattformübergreifenden Azure-Befehlszeilenschnittstelle</a> auflisten und herunterladen. Wenn Sie z. B. Dateien im Verzeichnis **example/curl** auflisten möchten, verwenden Sie den folgenden Befehl:

		azure storage blob list <container-name> example/curl

	Verwenden Sie den folgenden Befehl, um eine Datei herunterzuladen:

		azure storage blob download <container-name> <blob-name> <destination-file>

	> [AZURE.NOTE] Sie müssen entweder den Namen des Speicherkontos, das den Blob enthält, mithilfe der Parameter `-a` und `-k` angeben, oder die Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** festlegen. Weitere Informationen finden Sie unter <a href="../hdinsight-upload-data/" target="_blank" for more information.

6. Verwenden Sie die folgenden Anweisungen zum Erstellen einer neuen "internen" Tabelle namens **errorLogs**.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="CREATE+TABLE+IF+NOT+EXISTS+errorLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+STORED+AS+ORC;INSERT+OVERWRITE+TABLE+errorLogs+SELECT+t1,t2,t3,t4,t5,t6,t7+FROM+log4jLogs+WHERE+t4+=+'[ERROR]';SELECT+*+from+errorLogs;" -d statusdir="wasb:///example/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/hive

    Diese Anweisungen führen die folgenden Aktionen aus.

    * **CREATE TABLE IF NOT EXISTS**  erstellt eine Tabelle, sofern sie noch nicht vorhanden ist. Da das Schlüsselwort **EXTERNAL** nicht verwendet wird, ist dies eine "interne" Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.

		> [AZURE.NOTE] Im Gegensatz zu **EXTERNAL**-Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

    * **STORED AS ORC** speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    * **INSERT OVERWRITE ... SELECT**: wählt Zeilen aus der **log4jLogs**-Tabelle aus, die **[ERROR]** enthalten. Anschließend werden die Daten in die Tabelle **errorLogs** eingefügt.
    * <strong>SELECT *</strong> wählt alle Zeilen aus der neuen **errorLogs**-Tabelle aus.

7. Verwenden Sie die zurückgegebene Auftrags-ID, um den Status des Auftrags zu überprüfen. Nach der erfolgreichen Ausführung verwenden Sie xplat-cli wie zuvor beschrieben, um die Ergebnisse herunterzuladen und anzuzeigen. Die Ausgabe sollte drei Zeilen mit dem Inhalt **[ERROR]** enthalten.


## <a id="summary"></a>Zusammenfassung

Wie in diesem Dokument veranschaulicht, können Sie unformatierte HTTP-Anforderungen dazu verwenden, um Hive-Aufträge auf dem HDInsight-Cluster auszuführen, zu überwachen und deren Ergebnisse anzuzeigen.

Weitere Informationen zur REST-Schnittstelle, die in diesem Artikel verwendet wird, finden Sie in der <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference" target="_blank">WebHCat-Referenz</a>.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive mit HDInsight:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)


[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
