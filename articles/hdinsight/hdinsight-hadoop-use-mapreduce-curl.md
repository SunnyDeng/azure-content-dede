<properties
   pageTitle="Verwenden von MapReduce und Curl mit Hadoop in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie MapReduce-Aufträge mit Hadoop in HDInsight mithilfe von Curl remote ausführen."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Ausführen von MapReduce-Aufträgen mit Hadoop in HDInsight mithilfe von Curl

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In diesem Dokument erfahren Sie, wie mithilfe von Curl MapReduce-Aufträge auf einem Hadoop-Cluster in HDInsight ausgeführt werden.

Curl dient zum Veranschaulichen der Interaktion mit HDInsight über unformatierte HTTP-Anforderungen zum Ausführen von MapReduce-Aufträgen. Dies funktioniert mithilfe der WebHCat REST-API (ehemals Templeton), die von Ihrem HDInsight-Cluster bereitgestellt wird.

> [AZURE.NOTE]Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, nicht jedoch mit HDInsight, finden Sie weitere Informationen unter [Was Sie über Linux-basiertes Hadoop in HDInsight wissen müssen](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Hadoop-Cluster in HDInsight (Linux- oder Windows-basiert)

* [Curl](http://curl.haxx.se/)

* [jq](http://stedolan.github.io/jq/)

##<a id="curl"></a>Ausführen von MapReduce-Aufträgen mit Curl

> [AZURE.NOTE]Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URI verwenden, der zum Senden der Anforderungen an den Server genutzt wird.
>
> Ersetzen Sie für die Befehle in diesem Abschnitt die Option **BENUTZERNAME** für die Authentifizierung im Cluster durch den Benutzer und die Option **KENNWORT** durch das Kennwort für das Benutzerkonto. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters.
>
> Die REST-API wird durch [Standardauthentifizierung](http://en.wikipedia.org/wiki/Basic_access_authentication) gesichert. Sie sollten Anforderungen immer über HTTPS stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung mit dem HDInsight-Cluster herstellen können:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Sie sollten eine Antwort empfangen, die in etwa der im Folgenden aufgeführten entspricht:

        {"status":"ok","version":"v1"}

    Folgende Parameter werden in diesem Befehl verwendet:

    * **-u**: Gibt den Benutzernamen und das Kennwort für die Authentifizierung der Anforderung an
    * **-G**: Gibt an, dass dies eine GET-Anforderung ist

    Der Anfang des URI, ****https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, ist für alle Anforderungen identisch.

2. Verwenden Sie den folgenden Befehl, um einen MapReduce-Auftrag zu übermitteln.

		curl -u USERNAME:PASSWORD -d user.name=USERNAME -d jar=wasb:///example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=wasb:///example/data/gutenberg/davinci.txt -d arg=wasb:///example/data/CurlOut https://CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar

    Das Ende des URIs (/mapreduce/jar) weist WebHCat darauf hin, dass diese Anforderung einen MapReduce-Auftrag aus einer Klasse in einer JAR-Datei startet. Folgende Parameter werden in diesem Befehl verwendet:

	* **-d**: `-G` wird nicht verwendet, also ist die Anforderung standardmäßig die POST-Methode. `-d` gibt die Datenwerte an, die mit der Anforderung gesendet werden.

        * **user.name**: Der Benutzer, der den Befehl ausführt
        * **jar**: Der Speicherort der JAR-Datei, die die auszuführende Klasse enthält
        * **class**: Die Klasse, die die MapReduce-Logik enthält
        * **arg**: Die Argumente, die an den MapReduce-Auftrag übergeben werden, in diesem Fall die Eingabetextdatei und das Verzeichnis für die Ausgabe

    Dieser Befehl sollte eine Auftrags-ID zurückgeben, mit der der Status des Auftrags überprüft werden kann:

        {"id":"job_1415651640909_0026"}

3. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen. Ersetzen Sie **JOBID** durch den Wert, der im vorherigen Schritt zurückgegeben wurde. Wenn der Rückgabewert z. B. `{"id":"job_1415651640909_0026"}` lautet, ist die JOBID `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Wenn der Auftrag abgeschlossen ist, wird der Status "SUCCEEDED" angezeigt.

    > [AZURE.NOTE]Diese Curl-Anforderung gibt ein JSON-Dokument mit Informationen zum Auftrag zurück. Mithilfe von "jq" wird nur der Statuswert abgerufen.

4. Sobald der Status des Auftrags zu **SUCCEEDED** wechselt, können Sie die Ergebnisse des Auftrags aus dem Azure-Blobspeicher abrufen. Der mit der Abfrage übergebene `statusdir`-Parameter enthält den Speicherort der Ausgabedatei. In diesem Fall ****wasb:///example/curl**. Diese Adresse speichert die Ausgabe des Auftrags im Verzeichnis **example/curl** des Standardspeichercontainers, der von Ihrem HDInsight-Cluster verwendet wird.

Sie können diese Dateien mithilfe von [CLI Mac, Linux und Windows Azure](../xplat-cli-install.md) auflisten und herunterladen. Wenn Sie z. B. Dateien im Verzeichnis **example/curl** auflisten möchten, verwenden Sie den folgenden Befehl:

	azure storage blob list <container-name> example/curl

Verwenden Sie den folgenden Befehl, um eine Datei herunterzuladen:

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Sie müssen den Namen des Speicherkontos, das den Blob enthält, mithilfe der Parameter `-a` und `-k` angeben oder die Umgebungsvariablen **AZURE\_STORAGE\_ACCOUNT** und **AZURE\_STORAGE\_ACCESS\_KEY** festlegen. Weitere Informationen finden Sie unter [Hochladen von Daten in HDInsight](hdinsight-upload-data.md).

##<a id="summary"></a>Zusammenfassung

Wie in diesem Dokument veranschaulicht, können Sie unformatierte HTTP-Anforderungen dazu verwenden, um Hive-Aufträge im HDInsight-Cluster auszuführen, zu überwachen und deren Ergebnisse anzuzeigen.

Weitere Informationen zur REST-Schnittstelle, die in diesem Artikel verwendet wird, finden Sie in der [WebHCat-Referenz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

<!---HONumber=Oct15_HO1-->