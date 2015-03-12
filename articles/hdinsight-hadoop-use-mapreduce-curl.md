<properties
   pageTitle="Verwenden von MapReduce mit Hadoop in HDInsight | Azure"
   description="Erfahren Sie, wie Sie MapReduce-Aufträge mit Hadoop in HDInsight mithilfe von Curl remote ausführen."
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

# Ausführen von MapReduce-Aufträgen mit Hadoop in HDInsight mithilfe von Curl

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

In diesem Dokument erfahren Sie, wie mithilfe von Curl MapReduce-Aufträge auf einem Hadoop-Cluster in HDInsight ausgeführt werden. 

Curl dient zum Veranschaulichen der Interaktion mit HDInsight über unformatierte HTTP-Anforderungen zum Ausführen von MapReduce-Aufträgen. Dies funktioniert mithilfe der WebHCat REST-API (ehemals Templeton), die von Ihrem HDInsight-Cluster bereitgestellt wird.

> [AZURE.NOTE] Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, Ihnen HDInsight jedoch neu ist, finden Sie weitere Informationen unter: <a href="../hdinsight-hadoop-linux-information/" target="_blank">Was Sie über Linux-basiertes Hadoop in HDInsight wissen müssen</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Hadoop-Cluster in HDInsight (Linux- oder Windows-basiert)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

## <a id="curl"></a>Ausführen von MapReduce-Aufträgen mit Curl

> [AZURE.NOTE] Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URIs verwendet, um die Anforderungen an den Server zu senden.
> 
> Ersetzen Sie für die Befehle in diesem Abschnitt die Option **BENUTZERNAME** für die Authentifizierung im Cluster durch den Benutzer und die Option **KENNWORT** durch das Kennwort für das Benutzerkonto. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters.
> 
> Die REST-API wird mithilfe der <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">Standardauthentifizierung</a>geschützt. Sie sollten Anforderungen immer über HTTPS stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung zum HDInsight-Cluster herstellen können. 

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Sie sollten eine Antwort erhalten, die der folgenden ähnelt:

        {"status":"ok","version":"v1"}

    Folgende Parameter werden in diesem Befehl verwendet:

    * **-u**: der Benutzername und das Kennwort für die Authentifizierung der Anforderung
    * **-G**: gibt an, dass dies eine GET-Anforderung ist

    Der Anfang des URIs, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, ist für alle Anforderungen identisch. 

2. Gehen Sie wie folgt vor, um einen MapReduce-Auftrag zu übermitteln.

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    Das Ende des URIs (/mapreduce/jar) weist WebHCat darauf hin, dass diese Anforderung einen MapReduce-Auftrag aus einer Klasse in einer JAR-Datei startet. Folgende Parameter werden in diesem Befehl verwendet:

	* **-d**: da `-G` nicht verwendet wird, verwendet die Anforderung standardmäßig die POST-Methode Die Option `-d` gibt die Datenwerte an, die mit der Anforderung gesendet werden.

        * **benutzer.name**: der Benutzer, der den Befehl ausführt
        * **jar**: der Speicherort der JAR-Datei, die die auszuführende Klasse enthält
        * **class**: die Klasse, die die MapReduce-Logik enthält
        * **arg**: die an den MapReduce-Auftrag zu übergebenden Argumente In diesem Fall die Eingabetextdatei und das für die Ausgabe verwendete Verzeichnis.

    Dieser Befehl sollte eine Auftrags-ID zurückgeben, mit der der Status des Auftrags überprüft werden kann.

        {"id":"job_1415651640909_0026"}

3. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen. Ersetzen Sie **JOBID** durch den Wert, der im vorherigen Schritt zurückgegeben wurde. Wenn der Rückgabewert z. B. `{"id":"job_1415651640909_0026"}` lautet, dann würde JOBID den Wert `job_1415651640909_0026` ergeben.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Wenn der Auftrag abgeschlossen ist, wird der Status "SUCCEEDED" angezeigt.

    > [AZURE.NOTE] Diese Curl-Anforderung gibt ein JSON-Dokument mit Informationen zum Auftrag zurück. Mithilfe von "jq" wird nur der Statuswert abgerufen. 

4. Sobald der Status des Auftrags zu **SUCCEEDED** wechselt, können Sie die Ergebnisse des Auftrags aus dem Azure Blob-Speicher abrufen. Der mit der Abfrage übergebene Parameter `statusdir` enthält den Speicherort der Ausgabedatei. In diesem Fall ist das **wasb:///example/curl**. Diese Adresse speichert die Ausgabe des Auftrags im Verzeichnis **example/curl** des Standardspeichercontainers, der von Ihrem HDInsight-Cluster verwendet wird.

Sie können diese Dateien mithilfe der <a href="../xplat-cli/" target="_blank">plattformübergreifenden Azure-Befehlszeilenschnittstelle (xplat-cli)</a>auflisten und herunterladen. Wenn Sie z. B. Dateien im Verzeichnis **example/curl** auflisten möchten, verwenden Sie den folgenden Befehl:

	azure storage blob list <container-name> example/curl

Verwenden Sie den folgenden Befehl, um eine Datei herunterzuladen:

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE] Sie müssen entweder den Namen des Speicherkontos, das den Blob enthält, mithilfe der Parameter `-a` und `-k` angeben oder die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY** festlegen. Weitere Informationen finden Sie unter <a href="../hdinsight-upload-data/" target="_blank" for more information..

## <a id="summary"></a>Zusammenfassung

Wie in diesem Dokument veranschaulicht, können Sie unformatierte HTTP-Anforderungen dazu verwenden, um Hive-Aufträge auf dem HDInsight-Cluster auszuführen, zu überwachen und deren Ergebnisse anzuzeigen.

Weitere Informationen zur REST-Schnittstelle, die in diesem Artikel verwendet wird, finden Sie in der [WebHCat-Referenz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Hadoop in HDInsight](../hdinsight-use-mapreduce/)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](../hdinsight-use-hive/)

* [Verwenden von Pig mit Hadoop in HDInsight](../hdinsight-use-pig/)
<!--HONumber=45--> 
