<properties
   pageTitle="Verwenden von Hive in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Hive mit HDInsight über Visual Studio verwenden."
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

# Ausführen von Hive-Abfragen mit HDInsight Tools für Visual Studio

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In diesem Artikel erfahren Sie, wie Sie mit HDInsight Tools für Visual Studio Hive-Abfragen remote an einen HDInsight-Cluster übermitteln.

> [AZURE.NOTE] Dieses Dokument bietet keine detaillierte Beschreibung dazu, wie die in diesem Beispiel verwendeten HiveQL-Anweisungen vorgehen. Informationen zu der in diesem Beispiel verwendeten HiveQL finden Sie unter <a href="../hdinsight-use-hive/" target="_blank">Verwenden von Hive mit Hadoop unter HDInsight</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Azure HDInsight-Cluster (Hadoop in HDInsight), der auf Linux oder Windows basiert

* Visual Studio 2012 <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Update 4</a>, Visual Studio 2013 <a href="http://www.microsoft.com/download/details.aspx?id=43721" target="_blank">Update 3</a> oder <a href="http://www.microsoft.com/download/details.aspx?id=43722" target="_blank">Visual Studio Express 2013</a>.

## <a id="run"></a> Ausführen von Hive-Abfragen mit HDInsight-Tools für Visual Studio

1. Öffnen Sie **Visual Studio**, wählen Sie **Neu**, **Projekt**, **HDInsight** und dann **Hive-Anwendung**. Geben Sie einen Namen für dieses Projekt an.

2. Öffnen Sie die Datei **Script.hql**, die mit diesem Projekt erstellt wird, und fügen Sie dann die folgenden HiveQL-Anweisungen ein.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Diese Anweisungen führen die folgenden Aktionen aus.

    * **DROP TABLE**: löscht Tabelle und Datendatei, falls die Tabelle bereits existiert
    * **CREATE EXTERNAL TABLE** erstellt eine neue  'externe' Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort.

        > [AZURE.NOTE] Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden.
        >
        > Durch das Ablegen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

    * **ROW FORMAT**: teilt Hive mit, wie die Daten formatiert werden In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
    * **STORED AS TEXTFILE LOCATION** teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Informationen mit, dass die Speicherung als Text erfolgt.
    * Mit **SELECT** wählen Sie die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.

3. Wählen Sie auf der Symbolleiste den **HDInsight-Cluster** aus, den Sie für diese Abfrage verwenden möchten, und wählen Sie dann **Übermitteln** aus, um die Anweisungen als Hive-Auftrag auszuführen. Die **Hive-Auftragszusammenfassung** wird mit Informationen zum aktiven Auftrag angezeigt. Verwenden Sie den Link **Aktualisieren**, um die Auftragsinformationen zu aktualisieren, bis der **Auftragsstatus** zu **Abgeschlossen** wechselt.

4. Verwenden Sie den Link **Auftragsausgabe**, um die Ausgabe dieses Auftrags anzuzeigen. Es sollte `[ERROR] 3` angezeigt werden, was dem Wert entspricht, der von der SELECT-Anweisung zurückgegeben wurde.

5. Sie können Hive-Abfragen auch ohne Erstellung eines Projekts ausführen. Erweitern Sie im **Server-Explorer** die Option **Azure**, **HDInsight**, klicken Sie dann mit der rechten Maustaste auf den HDInsight-Server, und wählen Sie anschließend **Hive-Abfrage schreiben** aus.

6. Fügen Sie im sich daraufhin öffnenden Dokument **temp.hql** die folgenden HiveQL-Anweisungen hinzu.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Diese Anweisungen führen die folgenden Aktionen aus.

    * **CREATE TABLE IF NOT EXISTS** erstellt eine Tabelle, sofern sie noch nicht vorhanden ist. Da das Schlüsselwort **EXTERNAL** nicht verwendet wird, ist dies eine interne Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.

        > [AZURE.NOTE] Anders als bei **externen** Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

    * **STORED AS ORC** speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    * **INSERT OVERWRITE ... SELECT**: wählt Zeilen aus der Tabelle **log4jLogs** aus, die **[ERROR]** enthalten, dann werden die Daten in die Tabelle **errorLogs** eingefügt

7. Wählen Sie auf der Symbolleiste das Dropdownmenü für **Übermitteln** aus, um den Auftrag auszuführen. Verwenden Sie **Auftragsstatus**, um zu ermitteln, ob der Auftrag erfolgreich abgeschlossen wurde.

8. Verwenden Sie den **Server-Explorer** und erweitern Sie **Azure**, **HDInsight** sowie Ihren HDInsight-Cluster, **Hive-Datenbanken** und **Standard**, um zu überprüfen, ob der Auftrag abgeschlossen und eine neue Tabelle erstellt wurde. Es sollten die beiden Tabellen **errorLogs** und **log4jLogs** angezeigt werden.

## <a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bieten die HDInsight Tools für Visual Studio eine einfache Möglichkeit, um Hive-Abfragen auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight:

* [Verwenden von Hive mit Hadoop in HDInsight](../hdinsight-use-hive/)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Pig mit Hadoop in HDInsight](../hdinsight-use-pig/)

* [Verwenden von MapReduce mit Hadoop in HDInsight](../hdinsight-use-mapreduce/)

Weitere Informationen zu den HDInsight Tools für Visual Studio:

* [Erste Schritte mit HDInsight-Tools für Visual Studio](../hdinsight-hadoop-visual-studio-tools-get-started/)


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
