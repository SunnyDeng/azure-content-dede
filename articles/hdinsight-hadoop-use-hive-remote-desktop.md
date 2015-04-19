<properties
   pageTitle="Verwenden von Hadoop Hive in HDInsight | Azure"
   description="Erfahren Sie, wie Sie Hive mit HDInsight über den Remotedesktop verwenden."
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

# Verwenden von Hive mit Hadoop in HDInsight über den Remotedesktop

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

In diesem Artikel erfahren Sie, wie Sie die Verbindung zu einem HDInsight-Cluster über den Remotedesktop herstellen und dann die Hive-Abfragen mithilfe der Hive-Befehlszeilenschnittstelle (CLI) ausführen.

> [AZURE.NOTE] Dieses Dokument bietet keine detaillierte Beschreibung dazu, wie die in diesem Beispiel verwendeten HiveQL-Anweisungen vorgehen. Informationen zu der in diesem Beispiel verwendeten HiveQL finden Sie unter <a href="../hdinsight-use-hive/" target="_blank">Verwenden von Hive mit Hadoop unter HDInsight</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Windows-basierten HDInsight-Cluster (Hadoop in HDInsight)

* Einen Client mit Windows 7 oder neuerem Betriebssystem

## <a id="connect"></a>Verbinden mit dem Remotedesktop

Aktivieren Sie Remotedesktop für den HDInsight-Cluster, und stellen Sie dann anhand der Anweisungen unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Verbinden mit HDInsight-Clustern über RDP</a> eine Verbindung mit dem Cluster her.

## <a id="hive"></a>Verwenden des Hive-Befehls

Nachdem die Verbindung mit dem Desktop für den HDInsight-Cluster hergestellt wurde, führen Sie die folgenden Schritte aus, um mit Hive zu arbeiten.

1. Starten Sie auf dem HDInsight-Desktop die **Hadoop-Befehlszeile**.

2. Geben Sie den folgenden Befehl ein, um die Hive-Befehlszeilenschnittstelle zu starten.

        %hive_home%\bin\hive

    Nach dem die Befehlszeilenschnittstelle gestartet wurde, wird die Eingabeaufforderung der Hive-Befehlszeilenschnittstelle angezeigt:  `hive>`.

3. Geben Sie die folgenden Anweisungen zum Erstellen einer neuen Tabelle namens **log4jLogs** mithilfe der Befehlszeilenschnittstelle ein, wobei Sie die Beispieldaten verwenden.

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


4. Verwenden Sie die folgenden Anweisungen zum Erstellen einer neuen  'internen' Tabelle namens **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    Diese Anweisungen führen die folgenden Aktionen aus.

    * **CREATE TABLE IF NOT EXISTS** erstellt eine Tabelle, sofern sie noch nicht vorhanden ist. Da das Schlüsselwort **EXTERNAL** nicht verwendet wird, ist dies eine interne Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.
    
		> [AZURE.NOTE] Anders als bei **externen** Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.
		
    * **STORED AS ORC** speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    
    * **INSERT OVERWRITE ... SELECT**: wählt Zeilen aus der Tabelle **log4jLogs** aus, die **[ERROR]** enthalten, dann werden die Daten in die Tabelle **errorLogs** eingefügt

    Um zu überprüfen, dass nur Zeilen, die **[ERROR]** in Spalte "t4" enthalten, in der Tabelle **errorLogs** gespeichert wurden, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben.

        SELECT * from errorLogs;

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte "t4" **[ERROR]** enthalten.

## <a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet der Hive-Befehl eine einfache Möglichkeit, um Hive-Abfragen auf einem HDInsight-Cluster interaktiv auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

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
