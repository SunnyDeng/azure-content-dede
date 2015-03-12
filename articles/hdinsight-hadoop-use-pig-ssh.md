<properties
   pageTitle="Verwenden von Hadoop Pig in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Pig mit Hadoop in HDInsight über SSH verwenden."
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
 
# Ausführen von Pig-Aufträgen mithilfe des Pig-Befehls (SSH)

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

In diesem Dokument durchlaufen Sie den Prozess zum Herstellen einer Verbindung mit einem Linux-basierten HDInsight-Cluster über SSH. Anschließend führen Sie mithilfe des Pig-Befehls Pig Latin-Anweisungen interaktiv oder als Batchauftrag aus.

Mit der Programmiersprache Pig Latin können Sie Transformationen beschreiben, die auf die Eingabedaten angewendet werden, um die gewünschte Ausgabe zu generieren.

> [AZURE.NOTE] Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, Ihnen HDInsight jedoch neu ist, finden Sie weitere Informationen unter: <a href="../hdinsight-hadoop-linux-information/" target="_blank">Was Sie über Linux-basiertes Hadoop in HDInsight wissen müssen</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Linux-basierten HDInsight-Cluster (Hadoop in HDInsight)

* Einen SSH-Client Linux, Unix und Mac OS sollten über einen SSH-Client verfügbar sein. Windows-Benutzer müssen einen Client herunterladen, z. B. <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a>

## <a id="ssh"></a>Verbinden mit SSH

Stellen Sie mithilfe des SSH-Befehls eine Verbindung zum vollqualifizierten Domänennamen (FQDN) des HDInsight-Clusters her. Der FQDN entspricht dem Namen, den Sie dem Cluster zugewiesen haben, gefolgt von **.azurehdinsight.net**. Mithilfe des Folgenden wird z. B. eine Verbindung zu einem Cluster namens **myhdinsight** hergestellt.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Wenn Sie beim Erstellen des HDInsight-Clusters einen Zertifikatschlüssel für die SSH-Authentifizierung angegeben haben**, müssen Sie möglicherweise den Speicherort des privaten Schlüssel im Clientsystem angeben.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Wenn Sie beim Erstellen des HDInsight-Clusters ein Kennwort für die SSH-Authentifizierung angegeben haben**, müssen Sie nach der entsprechenden Aufforderung das Kennwort angeben.

### PuTTY (Windows-Clients)

Windows bietet keinen integrierten SSH-Client. Es wird die Verwendung von **PuTTY** empfohlen, das unter <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>heruntergeladen werden kann.

Weitere Informationen zum Verwenden von PuTTY finden Sie im Abschnitt **Verwenden von Putty für eine Verbindung mit einem Linux-Computer** unter <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Verwenden von SSH mit Linux auf Azure</a>.

> [AZURE.NOTE] Wenn Sie für die SSH-Authentifizierung für den HDInsight-Cluster ein Zertifikat verwendet haben, müssen Sie auch den Abschnitt **Erstellen eines PPK für Putty** lesen, der sich unter folgendem Thema befindet: <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Verwenden von SSH mit Linux auf Azure</a>

## <a id="pig"></a>Verwenden des Pig-Befehls

2. Nachdem die Verbindung hergestellt ist, starten Sie die Pig-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl.

        pig

	Nach einem kurzen Moment sollte eine `grunt>`-Eingabeaufforderung angezeigt werden.

3. Geben Sie die folgende Anweisung ein.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Dieser Befehl lädt die Inhalte der Datei "sample.log" in LOGS. Sie können den Inhalt der Datei wie folgt anzeigen.

		DUMP LOGS;

4. Als Nächstes transformieren Sie die Daten durch Anwendung eines regulären Ausdrucks, um nur den Protokolliergrad aus jedem Datensatz wie folgt zu extrahieren.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	Sie können **DUMP** zum Anzeigen der Daten nach der Transformation verwenden. In diesem Fall `DUMP LEVELS;`.

5. Fahren Sie mit der Anwendung der Transformationen mithilfe der folgenden Anweisungen fort. Verwenden Sie `DUMP`, um das Ergebnis der Transformation nach jedem Schritt anzuzeigen.

	<table>
	<tr>
	<th>Anweisung</th><th>Funktionsbeschreibung</th>
	</tr>
	<tr>
	<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Entfernt Zeilen mit NULL-Wert für die Protokollebene und speichert die Ergebnisse in FILTEREDLEVELS.</td>
	</tr>
	<tr>
	<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Gruppiert die Zeilen nach Protokollebene und speichert die Ergebnisse in GROUPEDLEVELS.</td>
	</tr>
	<tr>
	<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Erstellt eine neue Gruppe von Daten, die jeden eindeutigen Protokollebenenwert und die Häufigkeit seines Auftretens enthält. Dies wird in FREQUENCIES gespeichert.</td>
	</tr>
	<tr>
	<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordnet die Protokollebenen nach Anzahl (absteigend) und speichert sie in RESULT.</td>
	</tr>
	</table>

6. Sie können die Ergebnisse einer Transformation auch mithilfe der Anweisung `STORE` speichern. Das folgende Beispiel speichert `RESULT` im Verzeichnis **/example/data/pigout** des Standardspeichercontainers für Ihren Cluster.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE] Die Daten werden im angegebenen Verzeichnis in Dateien namens **part-nnnnn** gespeichert. Wenn das Verzeichnis bereits vorhanden ist, erhalten Sie einen Fehler.

7. Geben Sie die folgende Anweisung ein, um die grunt-Eingabeaufforderung zu beenden.

		QUIT;

### Pig Latin-Batchdateien

Den Pig-Befehl können Sie auch zum Ausführen von Pig Latin verwenden, das in einer Datei enthalten ist.

3. Nach dem Beenden der grunt-Eingabeaufforderung verwenden Sie den folgenden Befehl, um STDIN in eine Datei namens **pigbatch.pig** zu leiten. Diese Datei wird im Stammverzeichnis des Kontos erstellt, bei dem Sie für die SSH-Sitzung angemeldet sind.

		cat > ~/pigbatch.pig

4. Geben oder fügen Sie die folgenden Zeilen ein, und drücken Sie STRG+D, wenn Sie fertig sind.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Gehen Sie wie folgt vor, um die Datei **pigbatch.pig** mit dem Pig-Befehl auszuführen.

		pig ~/pigbatch.pig

	Wenn der Batchauftrag abgeschlossen ist, sollte die folgende Ausgabe angezeigt werden, die mit der Ausgabe identisch sein sollte, die Sie mit `DUMP RESULT;` in den vorherigen Schritten erhalten haben.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

## <a id="summary"></a>Zusammenfassung

Wie Sie sehen, können Sie mit dem Pig-Befehl MapReduce-Vorgänge mithilfe von Pig Latin interaktiv ausführen sowie die in einer Batchdatei gespeicherten Anweisungen starten.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Pig in HDInsight:

* [Verwenden von Pig mit Hadoop in HDInsight](../hdinsight-use-pig/)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](../hdinsight-use-hive/)

* [Verwenden von MapReduce mit Hadoop in HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
