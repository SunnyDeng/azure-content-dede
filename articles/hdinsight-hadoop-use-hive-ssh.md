<properties
   pageTitle="Verwenden von Hadoop Hive in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Hive mit HDInsight über SSH verwenden."
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

# Verwenden von Hive mit Hadoop in HDInsight über SSH

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In diesem Artikel erfahren Sie, wie Sie mit SSH (Secure Shell) eine Verbindung zu einem Hadoop-Cluster, der auf Azure HDInsight aufsetzt, herstellen und dann Hive-Abfragen mithilfe der Hive-Befehlszeilenschnittstelle (CLI) interaktiv übermitteln.

> [AZURE.NOTE] Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, jedoch noch nicht mit HDInsight, finden Sie weitere Informationen unter <a href="../hdinsight-hadoop-linux-information/" target="_blank">Was Sie über Hadoop auf Linux-basiertem HDInsight wissen müssen</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Linux-basierten Hadoop-auf-HDInsight-Cluster.

* Einen SSH-Client Linux, Unix und Mac OS sollten bereits im SSH-Client enthalten sein. Windows-Benutzer müssen einen Client herunterladen, z. B. <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

## <a id="ssh"></a>Verbinden mit SSH

Stellen Sie mithilfe des SSH-Befehls eine Verbindung zum vollqualifizierten Domänennamen (FQDN) des HDInsight-Clusters her. Der FQDN entspricht dem Namen, den Sie dem Cluster zugewiesen haben, gefolgt von **.azurehdinsight.net**. Eine Verbindung zu einem Cluster namens **myhdinsight** wird beispielsweise wie folgt hergestellt:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

Wenn Sie beim Erstellen des HDInsight-Clusters **einen Zertifikatschlüssel für die SSH-Authentifizierung angegeben haben**, müssen Sie möglicherweise den Speicherort des privaten Schlüssel im Clientsystem angeben:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Wenn Sie beim Erstellen des HDInsight-Clusters ein Kennwort für die SSH-Authentifizierung angegeben haben**, müssen Sie nach der entsprechenden Aufforderung das Kennwort angeben.

### Putty (Windows-basierte Clients)

Windows bietet keinen integrierten SSH-Client. Es wird die Verwendung von **Putty** empfohlen, das unter <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a> heruntergeladen werden kann.

Weitere Informationen zum Verwenden von Putty finden Sie im Abschnitt **Verwenden von Putty für eine Verbindung mit einem Linux-Computer** unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Verwenden von SSH mit Linux auf Azure</a>.

> [AZURE.NOTE] Wenn Sie für die SSH-Authentifizierung für den HDInsight-Cluster ein Zertifikat verwendet haben, müssen Sie auch den Abschnitt **Erstellen eines PPK für Putty** unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Verwenden von SSH mit Linux auf Azure</a> lesen.

## <a id="hive"></a>Verwenden des Hive-Befehls

2. Nachdem die Verbindung hergestellt wurde, starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl:

        hive

3. Geben Sie die folgenden Anweisungen zum Erstellen einer neuen Tabelle namens **log4jLogs** mithilfe der Befehlszeilenschnittstelle ein, wobei Sie die Beispieldaten verwenden:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Diese Anweisungen führen die folgenden Aktionen aus:

    * **DROP TABLE** löscht die Tabelle und Datendatei, falls die Tabelle bereits vorhanden ist.
    * **CREATE EXTERNAL TABLE** erstellt eine neue 'externe' Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.
    * **ROW FORMAT** teilt Hive mit, wie die Daten formatiert werden sollen. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
    * **STORED AS TEXTFILE LOCATION** teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Informationen mit, dass die Speicherung als Text erfolgt.
    * **SELECT** wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.

    > [AZURE.NOTE] Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden.
    >
    > Durch das Ablegen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

4. Verwenden Sie die folgenden Anweisungen zum Erstellen einer neuen  'internen' Tabelle namens **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Diese Anweisungen führen die folgenden Aktionen aus:

    * **CREATE TABLE IF NOT EXISTS** erstellt eine Tabelle, sofern sie noch nicht vorhanden ist. Da das Schlüsselwort **EXTERNAL** nicht verwendet wird, ist dies eine interne Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.
    * **STORED AS ORC** speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    * **INSERT OVERWRITE ... SELECT** wählt Zeilen aus der **log4jLogs**-Tabelle aus, die [ERROR** enthalten. Anschließend werden die Daten in die Tabelle **errorLogs** eingefügt.

    Um zu prüfen, dass nur Zeilen, die **[ERROR]** in Spalte "t4" enthalten, in der Tabelle **errorLogs** gespeichert wurden, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

        SELECT * from errorLogs;

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte "t4" **[ERROR]** enthalten.

    > [AZURE.NOTE] Im Gegensatz zu externen Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

## <a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet der Hive-Befehl eine einfache Möglichkeit, um Hive-Abfragen auf einem HDInsight-Cluster interaktiv auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight:

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

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
