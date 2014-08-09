<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Interactive Console" pageTitle="How to work with the interactive console in HDInsight | Azure" metaKeywords="" description="In this guide, you'll learn how to perform common tasks such as file upload, run jobs, and visualize data using the interactive console for HDInsight Service." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight Interactive JavaScript and Hive Consoles" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Interaktive JavaScript- und Hive-Konsolen in HDInsight
======================================================

Der Microsoft Azure HDInsight-Dienst enthält interaktive Konsolen für JavaScript und Hive. Diese Konsolen bieten eine einfache interaktive Lesen-Auswerten-Ausdrucken-Schleife (read-evaluate-print loop REPL), in der Benutzer Ausdrücke eingeben, diese Ausdrücke auswerten und anschließend das Ergebnis eines MapReduce-Jobs sofort abfragen und anzeigen können. Die JavaScript-Konsole führt Pig Latin-Anweisungen aus. Die Hive-Konsole wertet Hive Query Language (Hive QL)-Anweisungen aus. Beide Arten von Anweisungen werden zu MapReduce-Programmen kompiliert. Die Verwaltung von Hadoop-Jobs in diesen Konsolen ist viel einfacher als eine Remoteverbindung zum Hauptknoten des Hadoop-Clusters und direktes Arbeiten mit MapReduce-Programmen.

**Die JavaScript-Konsole**: eine Befehlsaufforderung mit einer flüssigen Schnittstelle zum Hadoop-Ökosystem. Die flüssige Schnittstelle verkettet Anweisungen und leitet den Kontext eines Aufrufs in einer Sequenz an den nächsten Aufruf in der Sequenz über. Die JavaScript-Konsole bietet Folgendes:

-   Zugriff auf das Hadoop-Cluster und dessen Ressourcen sowie auf Hadoop Distributed File System (HDFS)-Befehle.
-   Verwaltung und Manipulation von ein- und ausgehenden Daten im Hadoop-Cluster.
-   Diese flüssige Schnittstelle wertet Pig Latin- und JavaScript-Anweisungen aus, mit denen eine Reihe von MapReduce-Programmen zur Erstellung von Datenverarbeitungs-Abläufen definiert werden kann.

**Die Hive-Konsole**: Hive ist ein Data Warehouse-Framework, das auf Hadoop aufbaut und Datenverwaltung, -Abfragen und -Analyse bietet. Hive verwendet HiveQL, einen SQL-Dialekt für die Abfrage von Daten aus einem Hadoop-Cluster. Die Hive-Konsole bietet Folgendes:

-   Zugriff auf das Hadoop-Cluster und dessen Ressourcen sowie auf HDFS-Befehle.
-   Eine Implementierung des Hive-Frameworks, die HiveQL-Anweisungen auf einem Hadoop-Cluster ausführen kann.
-   Ein relationales Datenbankmodell für HDFS, über das Sie mit den im verteilten Dateisystem gespeicherten Daten so interagieren können, als wären diese Daten in Tabellen gespeichert. Die JavaScript-Konsole verwendet Pig Latin, eine Datenfluss-Sprache, und die Hive-Konsole verwendet HiveQL, eine Abfragesprache.

Pig (und die JavaScript-Konsole) werden von Entwicklern bevorzugt, die an einen skriptgesteuerten Ansatz gewöhnt sind, bei dem eine Sequenz verketteter (oder fließender) Transformationen zur Definition eines Datenverarbeitungs-Ablaufs verwendet wird. Dieser Ansatz eignet sich ebenfalls für stark unstrukturierte Daten.

Hive (und die Hive-Konsole) werden von Entwicklern bevorzugt, die mit SQL vertraut sind und die Arbeit mit relationalen Datenbanken gewöhnt sind. Dank der Verwendung von Schemata und Tabellenabstraktionen in Hive ist die Funktionsweise der von RDBMS sehr ähnlich.

Pig und Hive bieten Sprachen höherer Ebenen, die anschließend in MapReduce-Programme in Java kompiliert und auf dem HDFS ausgeführt werden. Falls Sie eine sehr direkte Kontrolle oder besonders gute Leistung benötigen, sollten Sie die MapReduce-Programme direkt schreiben.

Dieses Lernprogramm umfasst folgende Punkte
-------------------------------------------

-   [Verwenden der JavaScript-Konsole zur Ausführung eines MapReduce-Jobs](#runjob)
-   [Verwenden der JavaScript-Konsole zur grafischen Anzeige der Ergebnisse](#displayresults)
-   [Verwenden der Hive-Konsole, um die Ergebnisse in eine Hive-Tabelle zu exportieren](#createhivetable)
-   [Verwenden der Hive-Konsole, um die Daten in der Hive-Tabelle abzufragen](#queryhivetable)

Verwenden der JavaScript-Konsole zur Ausführung eines MapReduce-Jobs
--------------------------------------------------------------------

In diesem Abschnitt verwenden Sie die JavaScript-Konsole, um das Wortzählungs-Beispiel auszuführen, das mit dem HDInsight-Dienst ausgeliefert wird. Die hier ausgeführte JavaScript-Abfrage verwendet die flüssige API auf Basis von Pig, die von der interaktiven Konsole angeboten wird. Bei dem hier analysierten Text handelt es sich um die Project Gutenberg eBook-Auflage der *Notizbücher von Leonardo Da Vinci*. Ein Filter gibt an, dass die Ergebnisse des MapReduce-Jobs nur die zehn am häufigsten vorkommenden Wörter enthalten soll.

1.  Melden Sie sich beim [Management Portal](https://manage.windowsazure.com) an.
2.  Klicken Sie auf **HDINSIGHT**. Eine Liste der bereitgestellten Hadoop-Cluster wird angezeigt.
3.  Klicken Sie auf den Namen des HDInsight-Clusters, mit dem Sie sich verbinden möchten.
4.  Klicken Sie auf **Manage Cluster**.
5.  Geben Sie Ihre Anmeldeinformationen ein, und klicken Sie auf **Log On**.
6.  Klicken Sie im HDInsight-Portal auf **Samples**.

    ![HDI.Tiles.Samples](./media/hdinsight-interactive-console/HDI.TileSamples.PNG)

7.  Klicken Sie auf der Seite **Hadoop Sample Gallery** auf das **WordCount**-Tile.
8.  Klicken Sie oben rechts auf **WordCount.js** und speichern Sie die Datei in einem lokalen Verzeichnis, z. B. dem Ordner ../downloads.

    ![HDI.JsConsole.WordCountDownloads](./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG)

9.  Klicken Sie oben links auf **Azure HDInsight**, um zum Dashboard des Clusters zurückzukehren.
10. Klicken Sie auf **Interactive Cluster**, um die JavaScript-Konsole zu öffnen.

    ![HDI.Tiles.InteractiveConsole](./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG)

11. Klicken Sie oben rechts auf **JavaScript**.
12. Führen Sie den folgenden Befehl aus:

    fs.put()

13. Geben Sie im Fenster **Upload a file** die folgenden Parameter ein:

    -   **Source:** \_..\\downloads\\Wordcount.js - **Destination:** ./WordCount.js/

    ![HDI.JsConsole.UploadJs](./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG)

    Navigieren Sie zum Speicherort der WordCount.js-Datei. Sie benötigen den kompletten lokalen Pfad. Der einzelne Punkt am Ende des Zielpfads wird als Teil der relativen Adresse in HDFS benötigt.

14. Klicken Sie auf die Schaltfläche **Upload**.

15. Führen Sie die folgenden Befehle aus, um die Datei aufzulisten und den Inhalt anzuzeigen:

        #ls
        #cat WordCount.js

    ![HDI.JsConsole.JsCode](./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG)

    Beachten Sie, dass die JavaScript-Map-Funktion die Großbuchstaben aus dem Text mit der "toLowerCase()"-Methode entfernt, bevor die Auftretenshäufigkeit eines Wortes in der Reduce-Funktion gezählt wird.

16. Führen Sie den folgenden Befehl aus, um die Datendatei aufzulisten, die vom Wortzählungs-MapReduce-Job verarbeitet wird:

 		#ls /example/data/gutenberg

1.  Führen Sie den folgenden Befehl aus, um das MapReduce-Programm auszuführen:

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")

    Ersetzen Sie "admin" durch den aktuellen Benutzernamen.

    Beachten Sie, dass die Anweisungen mit dem Punkt-Operator verkettet werden, und dass *DaVinciTop10Words* als Ausgabedatei festgelegt wird. Im nächsten Abschnitt werden Sie auf die Ausgabedatei zugreifen.

    Nun sollte Folgendes angezeigt werden:

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
        2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! (View Log)

2.  Blättern Sie nach rechts und klicken Sie auf **View Log**, falls Sie den Job-Fortschritt verfolgen möchten. Das Protokoll enthält außerdem Diagnoseinformationen, falls beim Job ein Fehler auftritt. Nach Abschluss des Jobs sehen Sie die folgende Nachricht am Ende es Protokolls:

        [main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.

3.  Führen Sie den folgenden Befehl aus, um die Ausgabedatei aufzulisten:

        #ls

    Beachten Sie, dass ein DavinciTop10Words-Ordner erstellt wurde.

Verwenden der JavaScript-Konsole zur grafischen Anzeige der Ergebnisse
----------------------------------------------------------------------

Im letzten Abschnitt haben Sie die 10 häufigsten Worte in einer Textdatei mithilfe eines MapReduce-Jobs ermittelt. Die Datei ./DaVinciTop10Words enthält die Ausgabe.

1.  Führen Sie den folgenden Befehl aus, um die Ergebnisse im DaVinciTop10Words-Ordner anzuzeigen:

         file = fs.read("DaVinciTop10Words")

    Das Ergebnis sieht folgendermaßen aus:

         js> file=fs.read("DaVinciTop10Words")
         the    22966
         of 11228
         and    8428
         in 5737
         to 5296
         a  4791
         is 4261
         it 3073
         that   2903
         which  2544

2.  Führen Sie den folgenden Befehl aus, um den Inhalt der Datei in eine Datendatei einzulesen:

         data = parse(file.data, "word, count:long")

3.  Führen Sie den folgenden Befehl aus, um Daten grafisch anzuzeigen

         graph.bar(data)

    ![HDI.JsConsole.BarGraphTop10Words](./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG)

Verwenden der Hive-Konsole, um die Ergebnisse in eine Hive-Tabelle zu exportieren
---------------------------------------------------------------------------------

In diesem Abschnitt lernen Sie die interaktive Hive-Konsole kennen. Sie werden eine Hive-Tabelle mit der Ausgabe des MapReduce-Jobs erstellen. Der folgende Abschnitt beschreibt, wie Sie die Daten aus dieser Tabelle abfragen können.

**Erstellen der Hive-Tabelle**

1.  Klicken Sie oben rechts auf Hive, um die Hive-Konsole zu öffnen.

2.  Geben Sie den folgenden Befehl ein, um eine Tabelle mit zwei Spalten namens *DaVinciWordCountTable* mit der Ausgabe des Wortzählungs-Beispiels im "DaVinciTop10Words"-Ordner zu erstellen:

         CREATE EXTERNAL TABLE DaVinciWordCountTable     
         (word STRING,
         count INT) 
         ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
         STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';   

    Ersetzen Sie "admin" durch den aktuellen Benutzernamen.

    Beachten Sie, dass die Tabelle als EXTERNE Tabelle erstellt wird, um die Unabhängigkeit zwischen Zielordner und Tabelle zu erhalten. Beachten Sie außerdem, dass Sie nur den Ordner der Ausgabedatei angeben müssen, und nicht deren Dateinamen.

3.  Klicken Sie unten links auf **EVALUATE**.

4.  Geben Sie die folgenden Befehle ein, um zu bestätigen, dass die Tabelle mit zwei Spalten erstellt wurde:

         SHOW TABLES;
         DESCRIBE DaVinciWordCountTable;

5.  Klicken Sie auf **EVALUATE**.

    ![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

Verwenden der Hive-Konsole, um die Daten in der Hive-Tabelle abzufragen
-----------------------------------------------------------------------

1.  Führen Sie den folgenden Befehl aus, um die zehn häufigsten Wörter abzufragen:

         SELECT word, count
         FROM DaVinciWordCountTable
         ORDER BY count DESC LIMIT 10

    Das Abfrageergebnis sieht folgendermaßen aus:

         SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
         the 22966
         of 11228
         and 8428
         in 5737
         to 5296
         a 4791
         is 4261
         it 3073
         that 2903
         which 2544

Nächste Schritte
----------------

Sie sind nun in der Lage, einen Hadoop-Job aus der interaktiven JavaScript-Konsole auszuführen und die Ergebnisse des Jobs mit der Konsole anzuzeigen. Sie haben außerdem gelernt, wie Sie die Ergebnisse eines Hadoop-Jobs mit der interaktiven Hive-Konsole abfragen und verarbeiten können, indem Sie eine Tabelle erstellen und abfragen, welche die Ausgabe eines MapReduce-Programms enthält. Sie haben Beispiele für Pig Latin- und Hive QL-Anweisungen in beiden Konsolen gesehen. Außerdem haben Sie erfahren, wie die interaktive REPL-Natur der JavaScript- und Hive-Konsolen die Verwendung von Hadoop-Clustern vereinfacht. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Verwenden von Pig mit HDInsight](/de-de/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Verwenden von Hive mit HDInsight](/de-de/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Verwenden von MapReduce mit HDInsight](/de-de/manage/services/hdinsight/using-mapreduce-with-hdinsight/)

[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive Table Confirmation")

