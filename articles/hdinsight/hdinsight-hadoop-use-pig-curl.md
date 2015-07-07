<properties
   pageTitle="Verwenden von Hadoop Pig mit Curl in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie mithilfe von Curl Pig Latin-Aufträge auf einem Hadoop-Cluster in Azure HDInsight ausgeführt werden."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/03/2015"
   ms.author="larryfr"/>

#Ausführen von Pig-Aufträgen mit Hadoop in HDInsight mithilfe von Curl

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

In diesem Dokument erfahren Sie, wie mithilfe von Curl Pig Latin-Aufträge auf einem HDInsight-Cluster ausgeführt werden. Mit der Programmiersprache Pig Latin können Sie Transformationen beschreiben, die auf die Eingabedaten angewendet werden, um die gewünschte Ausgabe zu generieren.

Mit Curl wird veranschaulicht, wie Sie über unformatierte HTTP-Anforderungen zum Ausführen, Überwachen und Abrufen der Ergebnisse der Pig-Aufträge mit HDInsight interagieren können. Dies funktioniert mithilfe der WebHCat-REST-API (ehemals Templeton), die von Ihrem HDInsight-Cluster bereitgestellt wird.

> [AZURE.NOTE]Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, nicht jedoch mit HDInsight, finden Sie weitere Informationen unter <a href="../hdinsight-hadoop-linux-information/" target="_blank">Wichtige Informationen über Linux-basiertes Hadoop in HDInsight</a>.

##<a id="prereq"></a>Voraussetzungen

Um die in diesem Artikel aufgeführten Schritte auszuführen, benötigen Sie Folgendes:

* Einen Azure HDInsight-Cluster (Hadoop in HDInsight, basierend auf Linux oder Windows)

* <a href="http://curl.haxx.se/" target="_blank">Curl</a>

* <a href="http://stedolan.github.io/jq/" target="_blank">jq</a>

##<a id="curl"></a>Ausführen von Pig-Aufträgen mit Curl

> [AZURE.NOTE]Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URIs (Uniform Resource Identifier) zum Senden der Anforderungen an den Server verwenden.
>
> Ersetzen Sie für die Befehle in diesem Abschnitt **USERNAME** für die Authentifizierung im Cluster durch den Benutzer und **PASSWORD** durch das Kennwort für das Benutzerkonto. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters.
>
> Die REST-API wird durch <a href="http://en.wikipedia.org/wiki/Basic_access_authentication" target="_blank">Standardauthentifizierung</a> geschützt. Sie sollten Anforderungen immer über HTTPS (Secure HTTP) stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung zum HDInsight-Cluster herstellen können:

        curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status

    Sie sollten eine Antwort empfangen, die in etwa der im Folgenden aufgeführten entspricht:

        {"status":"ok","version":"v1"}

    Folgende Parameter werden in diesem Befehl verwendet:

    * **-u**: der Benutzername und das Kennwort für die Authentifizierung der Anforderung
    * **-G**: gibt an, dass dies eine GET-Anforderung ist

    Der Anfang der URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, ist für alle Anforderungen identisch. Der Pfad, **/status**, gibt an, dass die Anforderung den Status von WebHCat (auch bekannt als Templeton) für den Server zurückgibt.

2. Verwenden Sie folgenden Code, um einen Pig Latin-Auftrag an den Cluster zu übermitteln.

        curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'wasb:///example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="wasb:///example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig

    Folgende Parameter werden in diesem Befehl verwendet:

    * **-d**: Da `-G` nicht verwendet wird, ist die Anforderung standardmäßig die POST-Methode. `-d` gibt die Datenwerte an, die mit der Anforderung gesendet werden.

        * **user.name**: der Benutzer, der den Befehl ausführt
        * **execute**: die auszuführenden Pig Latin-Anweisungen
        * **statusdir**: das Verzeichnis, in das die Statusangaben für diesen Auftrag geschrieben werden.

    > [AZURE.NOTE]Beachten Sie, dass Leerzeichen in Pig Latin-Anweisungen bei Curl durch das Zeichen `+` ersetzt werden.

    Dieser Befehl sollte eine Auftrags-ID zurückgeben, mit der der Status des Auftrags überprüft werden kann. Beispiel:

        {"id":"job_1415651640909_0026"}

3. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen. Ersetzen Sie **JOBID** durch den Wert, der im vorherigen Schritt zurückgegeben wurde. Wenn der Rückgabewert z. B. `{"id":"job_1415651640909_0026"}` lautet, ist die **JOBID** `job_1415651640909_0026`.

        curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state

	Wenn der Auftrag abgeschlossen ist, wird der Status **SUCCEEDED** angezeigt.

    > [AZURE.NOTE]Diese Curl-Anforderung gibt ein JSON-Dokument (JavaScript Object Notation) mit Informationen zum Auftrag zurück. Mithilfe von "jq" wird nur der Statuswert abgerufen.

##<a id="results"></a>Anzeigen der Ergebnisse

Wenn der Status des Auftrags zu **SUCCEEDED** wechselt, können Sie die Ergebnisse des Auftrags aus dem Azure-Blob-Speicher abrufen. Der mit der Abfrage übergebene `statusdir`-Parameter enthält den Speicherort der Ausgabedatei, in diesem Fall **wasb:///example/pigcurl**. In dieser Adresse wird die Ausgabe des Auftrags im Verzeichnis **example/pigcurl** des Standardspeichercontainers gespeichert, der von Ihrem HDInsight-Cluster verwendet wird.

Sie können diese Dateien mithilfe der [Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows](../xplat-cli.md) auflisten und herunterladen. Wenn Sie z. B. Dateien im Verzeichnis **example/pigcurl** auflisten möchten, verwenden Sie den folgenden Befehl:

	azure storage blob list <container-name> example/pigcurl

Verwenden Sie den folgenden Befehl, um eine Datei herunterzuladen:

	azure storage blob download <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Sie müssen den Namen des Speicherkontos, das den Blob enthält, mithilfe der Parameter `-a` und `-k` angeben oder die Umgebungsvariablen **AZURE_STORAGE_ACCOUNT** und **AZURE_STORAGE_ACCESS_KEY** festlegen. Weitere Informationen finden Sie unter <a href="../hdinsight-upload-data/" target="_blank">.


##<a id="summary"></a>Zusammenfassung

Wie in diesem Dokument veranschaulicht, können Sie unformatierte HTTP-Anforderungen dazu verwenden, Pig-Aufträge auf dem HDInsight-Cluster auszuführen, zu überwachen und deren Ergebnisse anzuzeigen.

Weitere Informationen zur REST-Schnittstelle, die in diesem Artikel verwendet wird, finden Sie in der [WebHCat-Referenz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) (in englischer Sprache).

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54--> 