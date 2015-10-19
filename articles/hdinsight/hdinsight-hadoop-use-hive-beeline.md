<properties
   pageTitle="Verwenden von Hadoop Hive und SSH in HDInsight | Microsoft Azure"
   description="In diesem Artikel erfahren Sie, wie Sie mit SSH eine Verbindung zu einem Hadoop-Cluster in HDInsight herstellen und dann Hive-Abfragen mithilfe des Beeline-Tools interaktiv übermitteln."
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
   ms.date="10/05/2015"
   ms.author="larryfr"/>

#Verwenden von Hive mit Hadoop in HDInsight über Beeline

[AZURE.INCLUDE [Hive-Selektor](../../includes/hdinsight-selector-use-hive.md)]

In diesem Artikel erfahren Sie, wie Sie mit SSH (Secure Shell) eine Verbindung mit einem Linux-basierten HDInsight-Cluster herstellen und dann Hive-Abfragen mithilfe des [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)-Befehlszeilentools interaktiv übermitteln.

> [AZURE.NOTE]Für Beeline wird JDBC für die Verbindung mit Hive verwendet. Weitere Informationen zur Verwendung von JDBC mit Hive finden Sie unter [Herstellen einer Verbindung mit Hive unter Azure HDInsight per Hive-JDBC-Treiber](hdinsight-connect-hive-jdbc-driver.md).

##<a id="prereq"></a>Voraussetzungen

Um die in diesem Artikel aufgeführten Schritte auszuführen, benötigen Sie Folgendes:

* Einen Linux-basierten Hadoop auf einem HDInsight-Cluster.

* Einen SSH-Client Linux, Unix und Mac OS sollten über einen SSH-Client verfügbar sein. Windows-Benutzer müssen einen Client herunterladen, z. B [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Verbinden mit SSH

Stellen Sie mithilfe des SSH-Befehls eine Verbindung zum vollqualifizierten Domänennamen (FQDN) des HDInsight-Clusters her. Der FQDN entspricht dem Namen, den Sie dem Cluster zugewiesen haben, gefolgt von **.azurehdinsight.net**. Mithilfe des Folgenden wird z. B. eine Verbindung zu einem Cluster namens **myhdinsight** hergestellt:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Wenn Sie beim Erstellen des HDInsight-Clusters einen Zertifikatschlüssel für die SSH-Authentifizierung** angegeben haben, müssen Sie möglicherweise den Speicherort des privaten Schlüssel im Clientsystem angeben:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Wenn Sie beim Erstellen des HDInsight-Clusters ein Kennwort für die SSH-Authentifizierung** angegeben haben, müssen Sie nach der entsprechenden Aufforderung das Kennwort angeben.

Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, OS X und Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PUTTY (Windows-basierte Clients)

Windows bietet keinen integrierten SSH-Client. Wir empfehlen die Verwendung von **PuTTY**. Sie können das Programm unter [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) herunterladen.

Weitere Informationen zum Verwenden von PuTTY finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="beeline"></a>Verwenden des Beeline-Befehls

2. Nachdem die Verbindung hergestellt wurde, starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl:

        beeline

2. Verwenden Sie an der `beeline>`-Eingabeaufforderung Folgendes, um eine Verbindung mit dem HiveServer2-Dienst herzustellen:

        !connect jdbc:hive2://headnode0:10001/;transportMode=http admin

    Geben Sie nach Aufforderung das Kennwort für das Administratorkonto (admin) für den HDInsight-Cluster ein. Nach dem Herstellen der Verbindung wird die Aufforderung wie folgt geändert:
    
        jdbc:hive2://headnode0:10001/>

3. Beeline-Befehle beginnen normalerweise mit dem Zeichen `!`, z. B. `!help` zum Anzeigen der Hilfe. Das `!` kann aber auch häufig weggelassen werden. `help` funktioniert beispielsweise auch.

    Beim Anzeigen der Hilfe sehen Sie `!sql` zum Ausführen von HiveQL-Anweisungen. HiveQL wird aber so häufig genutzt, dass Sie das vorangestellte `!sql` weglassen können. Die folgenden beiden Anweisungen liefern genau die gleichen Ergebnisse. Es werden die Tabellen angezeigt, die derzeit über Hive verfügbar sind:
    
        !sql show tables;
        show tables;
    
    In einem neuen Cluster sollte nur eine Tabelle aufgeführt sein: __hivesampletable__.

4. Verwenden Sie Folgendes, um das Schema für „hivesampletable“ anzuzeigen:

        describe hivesampletable;
        
    Die folgenden Informationen werden zurückgegeben:
    
        +-----------------------+------------+----------+--+
        |       col_name        | data_type  | comment  |
        +-----------------------+------------+----------+--+
        | clientid              | string     |          |
        | querytime             | string     |          |
        | market                | string     |          |
        | deviceplatform        | string     |          |
        | devicemake            | string     |          |
        | devicemodel           | string     |          |
        | state                 | string     |          |
        | country               | string     |          |
        | querydwelltime        | double     |          |
        | sessionid             | bigint     |          |
        | sessionpagevieworder  | bigint     |          |
        +-----------------------+------------+----------+--+

    Hiermit werden die Spalten in der Tabelle angezeigt. Obwohl wir einige Abfragen für diese Daten ausführen konnten, werden wir stattdessen eine ganz neue Tabelle erstellen, um das Laden von Daten in Hive und das Anwenden eines Schemas zu veranschaulichen.
    
5. Geben Sie die folgenden Anweisungen ein, um eine neue Tabelle mit dem Namen **log4jLogs** zu erstellen, indem Sie die über das HDInsight-Cluster bereitgestellten Beispieldaten verwenden:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Mit diesen Anweisungen werden die folgenden Aktionen ausgeführt:

    * **TABELLE LÖSCHEN**: Löscht Tabelle und Datendatei, falls die Tabelle bereits existiert.
    * **EXTERNE TABELLE ERSTELLEN**: Erstellt eine neue "externe" Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.
    * **ZEILENFORMAT**: Teilt Hive mit, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
    * **SPEICHERORT DER TEXTDATEI**: Teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Information mit, dass die Speicherung als Text erfolgt.
    * **AUSWÄHLEN**: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[FEHLER]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'**: Teilt Hive mit, dass nur Daten aus Dateien mit der Erweiterung „.log“ zurückgegeben werden sollen. Normalerweise befinden sich in einem Ordner beim Abfragen mit Hive nur Daten mit dem gleichen Schema. Diese Beispielprotokolldatei wird aber mit anderen Datenformaten gespeichert.

    > [AZURE.NOTE]Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden.
    >
    > Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.
    
    Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:
    
        INFO  : Tez session hasn't been created yet. Opening session
        INFO  :
        
        INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)
        
        INFO  : Map 1: -/-      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0/1      Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0/1
        INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
        INFO  : Map 1: 1/1      Reducer 2: 1/1
        +----------+--------+--+
        |   sev    | count  |
        +----------+--------+--+
        | [ERROR]  | 3      |
        +----------+--------+--+
        1 row selected (47.351 seconds)

4. Verwenden Sie `!quit`, um Beeline zu beenden.

##<a id="file"></a>Ausführen einer HiveQL-Datei

Beeline kann auch verwendet werden, um eine Datei auszuführen, die HiveQL-Anweisungen enthält. Verwenden Sie die folgenden Schritte, um eine Datei zu erstellen und sie dann mit Beeline auszuführen.

1. Verwenden Sie den folgenden Befehl, um eine neue Datei mit dem Namen __query.hql__ zu erstellen:

        nano query.hql
        
2. Verwenden Sie nach dem Öffnen des Editors Folgendes als Inhalt für die Datei. Mit dieser Abfrage wird eine neue „interne“ Tabelle mit dem Namen **errorLogs** erstellt:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Mit diesen Anweisungen werden die folgenden Aktionen ausgeführt:

    * **TABELLE ERSTELLEN, FALLS NICHT VORHANDEN** – Erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das Schlüsselwort **EXTERN** nicht verwendet wird, ist dies eine "interne" Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.
    * **ALS ORC GESPEICHERT** – Speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    * **ÜBERSCHREIBEN EINFÜGEN ... AUSWÄHLEN**: Wählt die Zeilen aus der Tabelle **log4jLogs** aus, die **[FEHLER]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.
    
    > [AZURE.NOTE]Im Gegensatz zu externen Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.
    
3. Verwenden Sie __STRG__+___\_X__, um die Datei zu speichern, und geben Sie dann __J__ ein, und drücken Sie die __EINGABETASTE__.

4. Verwenden Sie Folgendes, um die Datei mit Beeline auszuführen:

        beeline -u 'jdbc:hive2://headnode0:10001/;transportMode=http' -n admin -p GiantR0b0! -f query.hql

5. Um zu überprüfen, ob die Tabelle **errorLogs** erstellt wurde, starten Sie Beeline und stellen eine Verbindung mit HiveServer2 her. Verwenden Sie anschließend die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

        SELECT * from errorLogs;

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte „t4“ den Wert **[FEHLER]** enthalten:
    
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

##<a id="summary"></a>Zusammenfassung

Sie haben gesehen, dass der Beeline-Befehl eine einfache Möglichkeit für die interaktive Ausführung von Hive-Abfragen in einem HDInsight-Cluster darstellt.

##<a id="nextsteps"></a>Nächste Schritte

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


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-storage]: hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-get-started.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=Oct15_HO2-->