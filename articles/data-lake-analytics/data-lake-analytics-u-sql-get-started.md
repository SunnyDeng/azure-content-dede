<properties 
   pageTitle="Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio | Azure" 
   description="Erfahren Sie, wie Sie Data Lake-Tools für Visual Studio installieren und U-SQL-Skripts entwickeln und testen." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="jgao"/>

# Tutorial: Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache

U-SQL ist eine Sprache, bei der die Vorteile von SQL mit der Ausdruckskraft Ihres eigenen Codes zum Verarbeiten aller Daten jedweden Umfangs vereint werden. Mit der skalierbaren verteilten Abfragefunktion von U-SQL können Sie Daten im Speicher und über relationale Speicher, z. B. Azure SQL-Datenbank, hinweg effizient analysieren. Sie können unstrukturierte Daten verarbeiten, indem Sie Schemas auf Lesevorgänge anwenden und benutzerdefinierte Logik und UDFs einfügen. Funktionen zur Erweiterbarkeit sind vorhanden, um eine genaue Kontrolle der Ausführung in größerem Umfang zu ermöglichen. Weitere Informationen zur Entwurfsphilosophie von U-SQL finden Sie in diesem [Visual Studio-Blogbeitrag](http://blogs.msdn.com/b/visualstudio/archive/2015/09/28/introducing-u-sql.aspx).

Es gibt einige Unterschiede zu ANSI SQL oder T-SQL. Beispielsweise müssen Schlüsselwörter wie SELECT in Großbuchstaben geschrieben werden.

Es handelt sich um eine Typsystem- und Ausdruckssprache innerhalb von Select-Anweisungen, wobei Prädikate, usw. in C# verwendet werden. Dies bedeutet, dass die Datentypen die C#-Typen sind und in den Datentypen C#-NULL-Semantik verwendet wird. Die Vergleichsvorgänge innerhalb eines Prädikats basieren auf der C#-Syntax (z. B. a == "foo"). Dies bedeutet auch, dass die Werte vollständige .NET-Objekte sind und dass Sie problemlos alle Methoden für Vorgänge des Objekts verwenden können (z. B. "f o o o".Split(' ')).

Weitere Informationen finden Sie unter [U-SQL-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###Voraussetzungen

Sie müssen das [Tutorial: Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) abgeschlossen haben.

In diesem Tutorial haben Sie einen Data Lake Analytics-Auftrag mit dem folgenden U-SQL-Skript ausgeführt:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Dieses Skript weist keine Transformationsschritte auf. Daten werden aus der Quelldatei **SearchLog.tsv** gelesen und schematisiert, und das Rowset wird in der Datei **SearchLog-from-adltools.csv** ausgegeben.

Beachten Sie das Fragezeichen neben dem Datentyp des Felds „Dauer“. Dies bedeutet, dass das Feld „Dauer“ null sein kann.

Einige Konzepte und Schlüsselwörter im Skript:

- **Rowsetvariablen**: Jeder Abfrageausdruck, der ein Rowset produziert, kann einer Variablen zugewiesen werden. U-SQL basiert im Skript auf dem Benennungsmuster für T-SQL-Variablen, z. B. **@searchlog**. Beachten Sie, dass die Zuweisung keine Erzwingung der Ausführung bedeutet. Der Ausdruck wird lediglich benannt, und Sie haben die Möglichkeit, komplexere Ausdrücke zu erstellen.
- Mit **EXTRACT** können Sie bei einem Lesevorgang ein Schema definieren. Das Schema wird mit einem Spaltennamen und einem C#-Typnamenpaar pro Spalte angegeben. Es wird ein so genannter **Extractor** verwendet, z. B. **Extractors.Tsv()**, um TSV-Dateien zu extrahieren. Sie können benutzerdefinierte Extractors entwickeln.
- Mit **OUTPUT** wird ein Rowset serialisiert. Mit der Datei „Outputters.Csv()“ wird eine kommagetrennte Datei an einem bestimmten Speicherort ausgegeben. Sie können auch benutzerdefinierte Outputter-Dateien entwickeln.
- Beachten Sie, dass es sich bei den beiden Pfaden um relative Pfade handelt. Sie können aber auch absolute Pfade verwenden. Beispiel: 
    
        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Sie müssen einen absoluten Pfad verwenden, um auf die Dateien in den verknüpften Speicherkonten zuzugreifen. Die Syntax für Dateien, die unter dem verknüpften Azure-Speicherkonto gespeichert werden, lautet wie folgt:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Azure-BLOB-Container mit öffentlichen Blobs oder Zugriffsberechtigungen für öffentliche Container werden derzeit nicht unterstützt.

## Verwenden von Skalarvariablen

Sie können auch Skalarvariablen verwenden, um die Skriptverwaltung zu vereinfachen. Das vorherige U-SQL-Skript kann auch wie folgt geschrieben werden:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
    
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();
    
    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();
      
## Transformieren von Rowsets

Verwenden Sie **SELECT**, um Rowsets zu transformieren:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

Für die WHERE-Klausel wird ein [boolescher C#-Ausdruck](https://msdn.microsoft.com/library/6a71f45d.aspx) verwendet. Sie können die C#-Ausdruckssprache nutzen, um Ihre eigenen Ausdrücke und Funktionen zu erstellen. Sie können sogar komplexere Filtervorgänge durchführen, indem Sie sie mit logischen Konjunktionen (und) und Disjunktionen (oder) kombinieren.

Im folgenden Skript werden die DateTime.Parse()-Methode und eine Konjunktion verwendet.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";
    
    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
    
    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();
        
Beachten Sie, dass die zweite Abfrage auf dem Ergebnis des ersten Rowsets aufbaut und das Ergebnis daher eine Komposition der beiden Filter darstellt. Sie können auch einen Variablennamen wiederverwenden, und die Namen werden lexikalisch begrenzt.

## Aggregieren von Rowsets

Bei U-SQL können Sie die vertrauten Elemente **ORDER BY**, **GROUP BY** und Aggregationen verwenden.

In der folgenden Abfrage wird nach der Gesamtdauer pro Region gesucht, und dann werden die ersten fünf Zeiträume ausgegeben.

In U-SQL-Rowsets wird die Reihenfolge für die nächste Abfrage nicht beibehalten. Zum Sortieren einer Ausgabe müssen Sie der OUTPUT-Anweisung also wie unten dargestellt das ORDER BY-Element hinzufügen:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";
    
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;
    
    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
    
    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2 
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
        
Die ORDER BY-U-SQL-Klausel muss mit der FETCH-Klausel in einem SELECT-Ausdruck kombiniert werden.

Die HAVING-U-SQL-Klausel kann verwendet werden, um die Ausgabe auf Gruppen zu beschränken, die die HAVING-Bedingung erfüllen:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();
    
    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## Verknüpfen von Daten

U-SQL enthält häufig verwendete Verknüpfungsoperatoren, z. B. INNER JOIN, LEFT/RIGHT/FULL OUTER JOIN, SEMI JOIN. Damit können nicht nur Tabellen verknüpft werden, sondern auch beliebige Rowsets (auch aus Dateien erzeugte Rowsets).

Im folgenden Skript wird das searchlog-Element mit einem Ankündigungsaufrufprotokoll verknüpft und gibt die Ankündigungen für die Abfragezeichenfolge für ein bestimmtes Datum aus.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();
    
    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s 
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;
    
    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL unterstützt nur die ANSI-kompatible Verknüpfungssyntax: „Rowset1 JOIN Rowset2 ON predicate“. Die alte Syntax „FROM Rowset1, Rowset2 WHERE predicate“ wird NICHT unterstützt. Das Prädikat in einem JOIN-Element muss eine Gleichheitsverknüpfung (kein Ausdruck) sein. Wenn Sie einen Ausdruck verwenden möchten, können Sie ihn der SELECT-Klausel eines vorherigen Rowsets hinzufügen. Wenn Sie einen anderen Vergleich durchführen möchten, können Sie ihn in die WHERE-Klausel verschieben.

        
## Erstellen von Datenbanken, Tabellenwertfunktionen, Ansichten und Tabellen

Bei U-SQL können Sie Daten im Kontext einer Datenbank und eines Schemas verwenden. Es ist dann nicht immer erforderlich, aus Dateien zu lesen oder in Dateien zu schreiben.

Alle U-SQL-Skripts werden mit einer Standarddatenbank (master) und einem Standardschema (dbo) als Standardkontext ausgeführt. Sie können Ihre eigene Datenbank bzw. Ihr eigenes Schema erstellen. Verwenden Sie zum Ändern des Kontexts die **USE**-Anweisung.


### Erstellen einer Tabellenwertfunktion (Table-Valued Function, TVF)

Im letzten U-SQL-Skript haben Sie die Verwendung von EXTRACT zum Lesen aus derselben Quelldatei wiederholt. Mithilfe der Tabellenwertfunktion von U-SQL können Sie die Daten zur späteren Wiederverwendung kapseln.

Im folgenden Skript wird eine Tabellenwertfunktion mit dem Namen *Searchlog()* in der Standarddatenbank und im Standardschema erstellt:

    DROP FUNCTION IF EXISTS Searchlog;
    
    CREATE FUNCTION Searchlog() 
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN 
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;
    
Das folgende Skript veranschaulicht, wie Sie die im obigen Skript definierte Tabellenwertfunktion verwenden:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
        
### Erstellen von Ansichten

Falls Sie nur über einen Abfrageausdruck verfügen, der abstrahiert werden soll, und ihn nicht parametrisieren möchten, können Sie anstelle einer Tabellenwertfunktion eine Ansicht erstellen.

Im folgenden Skript wird die Ansicht *SearchlogView* in der Standarddatenbank und im Standardschema erstellt:

    DROP VIEW IF EXISTS SearchlogView;
    
    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    
Im folgenden Skript wird die Verwendung der definierten Ansicht veranschaulicht:

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;
    
    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### Erstellen von Tabellen. 

Ähnlich wie bei relationalen Datenbanktabellen ermöglicht U-SQL Ihnen die Erstellung einer Tabelle mit einem vordefinierten Schema. Sie können auch eine Tabelle erstellen und das Schema aus der Abfrage ableiten, mit der die Tabelle aufgefüllt wird (auch als CREATE TABLE AS SELECT oder CTAS bezeichnet).

Im folgenden Skript werden eine Datenbank und zwei Tabellen erstellt:

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;
    
    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;
    
    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,
    
                INDEX sl_idx CLUSTERED (UserId ASC) 
                    PARTITIONED BY HASH (UserId)
    );
    
    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;
    
    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC) 
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### Abfragen von Tabellen

Sie können die (im obigen Skript erstellten) Tabellen genauso abfragen, wie Sie dies für Datendateien durchführen. Verweisen Sie einfach auf den Tabellennamen, anstatt mit EXTRACT ein Rowset zu erstellen.

Das Transform-Skript, das Sie zuvor verwendet haben, wird so geändert, dass aus den Tabellen gelesen wird:

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;
    
    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;
    
    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Beachten Sie, dass es derzeit nicht möglich ist, eine SELECT-Klausel für eine Tabelle in demselben Skript auszuführen, in dem Sie diese Tabelle erstellen.


##Zusammenfassung

In diesem Tutorial wurde nur ein kleiner Teil von U-SQL behandelt. Aufgrund des Umfangs dieses Tutorials kann nicht alles behandelt werden, z. B.:

- Verwenden von CROSS APPLY, um Teile von Zeichenfolgen, Arrays und Zuordnungen in Zeilen zu entpacken
- Verwenden von partitionierten Datasets (Dateisätze und partitionierte Tabellen)
- Entwickeln von benutzerdefinierten Operatoren, z. B. Extractors, Outputters, Prozessoren, benutzerdefinierte Aggregatoren in C#
- Verwenden von U-SQL-Windowing-Funktionen
- Verwalten von U-SQL-Code mit Ansichten, Tabellenwertfunktionen und gespeicherten Prozeduren
- Ausführen von beliebigem benutzerdefiniertem Code auf Verarbeitungsknoten 
- Herstellen einer Verbindung mit Azure SQL-Datenbanken und Durchführen von Partnerverbundabfragen für diese Datenbanken und Ihre U-SQL- und Azure Data Lake-Daten

## Weitere Informationen 

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Verwenden von U-SQL-Fensterfunktionen für Azure Data Lake Analytics-Aufträge](data-lake-analytics-use-window-functions.md)
- [Überwachen und Problembehandeln von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## Teilen Sie uns Ihre Meinung mit

- [Neuen Dokumentationsbestand vorschlagen](data-lake-analytics-documentation-backlog.md)
- [Feature anfordern](http://aka.ms/adlafeedback)
- [Hilfe in Foren](http://aka.ms/adlaforums)
- [Feedback zu U-SQL](http://aka.ms/usqldiscuss)

<!---HONumber=AcomDC_0107_2016-->