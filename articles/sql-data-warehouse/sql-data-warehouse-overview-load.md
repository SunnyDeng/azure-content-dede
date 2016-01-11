   <properties
   pageTitle="Laden von Daten in SQL Data Warehouse | Microsoft Azure"
   description="Lernen Sie die allgemeinen Szenarios für das Laden von Daten in SQL Data Warehouse kennen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="12/17/2015"
   ms.author="lodipalm;barbkess"/>

# Laden von Daten in SQL Data Warehouse
SQL Data Warehouse bietet zahlreiche Optionen zum Laden von Daten. Dazu gehören:

- PolyBase
- Azure Data Factory
- Befehlszeilenprogramm BCP
- SQL Server Integration Services (SSIS)
- Drittanbietertools zum Laden von Daten

Während alle oben genannten Methoden mit SQL Data Warehouse verwendet werden können, ist PolyBase durch die Möglichkeit, Lasten aus Azure Blob-Speichern transparent zu parallelisieren, das schnellste Tool zum Laden von Daten. Weitere Informationen zum [Laden mit PolyBase][]. Da viele Benutzer mit Anfangswerten von Hunderten von Gigabytes bis Dutzenden Terabytes aus lokalen Quellen arbeiten, finden Sie in den folgenden Abschnitten Informationen zum anfänglichen Laden von Daten.

## Anfängliches Laden in SQL Data Warehouse aus SQL Server 
Zum Laden aus einer lokalen SQL Server-Instanz in SQL Data Warehouse werden folgende Schritte empfohlen:

1. Exportieren der SQL Server-Daten in Flatfiles mit **BCP** 
2. Verwenden von **AZCopy** oder **Import/Export** (bei größeren Datasets), um die Dateien in Azure zu verschieben
3. Konfigurieren von PolyBase zum Lesen der Dateien aus dem Speicherkonto
4. Erstellen neuer Tabellen und Laden von Daten mit **PolyBase**

In den folgenden Abschnitten werden die einzelnen Schritte ausführlich beschrieben und Beispiele für den Prozess gezeigt.

> [AZURE.NOTE]Vor dem Verschieben von Daten aus einem System wie SQL Server wird empfohlen, die Artikel zum [Migrieren des Schemas][] und [Migrieren von Code][] in der Dokumentation zu lesen.

## Exportieren von Dateien mit BCP

Um die Dateien für das Verschieben in Azure vorzubereiten, müssen Sie diese in Flatfiles exportieren. Dazu eignet sich besonders das Befehlszeilenprogramm BCP. Wenn Sie noch nicht über dieses Dienstprogramm verfügen, können Sie es mit den [Microsoft-Befehlszeilenprogrammen für SQL Server][] herunterladen. Ein BCP-Beispielbefehl kann wie folgt aussehen:

```
bcp "<Directory><File>" -c -T -S <Server Name> -d <Database Name>
```

Mit diesem Befehl werden die Ergebnisse einer Abfrage übernommen und in eine Datei im Verzeichnis Ihrer Wahl exportiert. Sie können den Prozess parallelisieren, indem Sie mehrere BCP-Befehle für separate Tabellen gleichzeitig ausführen. Dadurch können Sie bis zu einen BCP-Prozess pro Kern des Servers ausführen. Es wird jedoch empfohlen, einige kleinere Vorgänge mit unterschiedlichen Konfigurationen auszuprobieren, um festzustellen, was für Ihre Umgebung am besten geeignet ist.

Beachten Sie außerdem in Hinsicht auf das Laden mit PolyBase, dass UTF-16 noch nicht von PolyBase unterstützt wird und alle Dateien UTF-8 aufweisen müssen. Dies kann auf einfache Weise durch Einfügen des "-c"-Flags in den BCP-Befehl erreicht werden, oder Sie können auch mit dem folgenden Code Flatfiles aus UTF-16 in UTF-8 konvertieren:

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
Nachdem Sie die Daten erfolgreich in Dateien exportiert haben, können Sie sie in Azure verschieben. Dazu können Sie AZCopy oder den Import/Export-Dienst verwenden, wie es im folgenden Abschnitt beschrieben ist.

## Laden in Azure mit AZCopy oder Import/Export
Wenn Sie Daten im Bereich von 5 bis 10 Terabyte oder mehr verschieben, wird empfohlen, den Datenträgerversanddienst [Import/Export][] zum Verschieben zu verwenden. Bei Untersuchungen konnten Daten im einstelligen TB-Bereich jedoch problemlos mit AZCopy über das öffentliche Internet verschoben werden. Dieser Prozess kann auch mit ExpressRoute beschleunigt oder erweitert werden.

In den folgenden Schritten wird ausführlich erläutert, wie Daten mit AZCopy von einem lokalen Konto in ein Azure-Speicherkonto verschoben werden. Wenn Sie über kein Azure-Speicherkonto in der gleichen Region verfügen, können Sie eines mithilfe der [Azure-Speicherdokumentation][] erstellen. Sie können auch Daten aus einem Speicherkonto in einer anderen Region laden, doch ist die Leistung in diesem Fall nicht optimal.

> [AZURE.NOTE]In dieser Dokumentation wird davon ausgegangen, dass Sie das Befehlszeilenprogramm AZCopy installiert haben und es mit Powershell ausführen können. Wenn das nicht der Fall ist, folgen Sie den [Installationsanweisungen für AZCopy][].

Da eine Gruppe von Dateien vorhanden ist, die mit BCP erstellt wurden, kann AZCopy einfach aus Azure Powershell oder durch Ausführen eines Powershell-Skripts ausgeführt werden. Im Allgemeinen weist die zum Ausführen von AZCopy benötigte Eingabeaufforderung die folgende Form auf:

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

Neben der grundlegenden Vorgehensweise werden die folgenden bewährten Methoden für das Laden mit AZCopy empfohlen:


+ **Gleichzeitige Verbindungen**: Zusätzlich zur Erhöhung der Anzahl von AZCopy-Vorgängen, die gleichzeitig ausgeführt werden, kann der AZCopy-Vorgang selbst weiter parallelisiert werden, indem der Parameter "/NC" festgelegt wird, durch den eine Reihe gleichzeitiger Verbindungen zum Ziel geöffnet wird. Zwar kann der Parameter auf einen Wert von bis zu 512 gesetzt werden, doch wurde festgestellt, dass eine optimale Datenübertragung bei einem Wert von 256 gegeben ist. Es wird empfohlen, eine Reihe von Werten auszuprobieren, um festzustellen, welcher für Ihre Konfiguration optimal ist.

+ **ExpressRoute**: Wie oben erwähnt, kann dieser Prozess durch Aktivierung von ExpressRoute beschleunigt werden. Einen Überblick über ExpressRoute und Schritte zum Konfigurieren finden Sie in der [Dokumentation zu ExpressRoute][].

+ **Ordnerstruktur**: Um die Übertragung mit PolyBase zu vereinfachen, stellen Sie sicher, dass jede Tabelle einem eigenen Ordner zugeordnet ist. Dadurch werden die Schritte beim späteren Laden mit PolyBase minimiert und vereinfacht. Im Übrigen hat es keine Auswirkung, wenn eine Tabelle in mehrere Dateien oder sogar Unterverzeichnisse innerhalb des Ordners aufgeteilt wird.
	 

## Konfigurieren von PolyBase 

Da sich die Daten nun in Azure-Speicher-Blobs befinden, werden sie mit PolyBase in die SQL Data Warehouse-Instanz importiert. Die folgenden Schritte betreffen ausschließlich die Konfiguration, und viele davon müssen nur einmal pro SQL Data Warehouse-Instanz, Benutzer oder Speicherkonto ausgeführt werden. Diese Schritte sind noch ausführlicher in der Dokumentation zum [Laden mit PolyBase][] beschrieben.

1. **Erstellen eines Datenbank-Hauptschlüssels.** Dieser Vorgang muss nur einmal pro Datenbank ausgeführt werden. 

2. **Erstellen von datenbankbezogenen Anmeldeinformationen.** Dieser Vorgang ist nur erforderlich, wenn Sie neue Anmeldeinformationen/Benutzer erstellen, andernfalls können zuvor erstellte Anmeldeinformationen verwendet werden.

3. **Erstellen eines externen Dateiformats.** Externe Dateiformate können ebenfalls wiederverwendet werden und müssen nur erstellt werden, wenn Sie einen neuen Dateityp hochladen.

4. **Erstellen einer externen Datenquelle.** Wenn auf ein Speicherkonto verwiesen wird, kann eine externe Datenquelle beim Laden aus demselben Container verwendet werden. Verwenden Sie für den Parameter "LOCATION" einen Speicherort im Format "wasbs://mycontainer@ test.blob.core.windows.net/path".

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

Das Speicherkonto ist nun ordnungsgemäß konfiguriert, sodass Sie mit dem Laden der Daten in SQL Data Warehouse fortfahren können.

## Laden von Daten mit PolyBase 
Nach dem Konfigurieren von PolyBase können Sie Daten direkt in SQL Data Warehouse laden, indem Sie einfach eine externe Tabelle erstellen, die auf die Daten im Speicher verweist, und dann diese Daten einer neuen Tabelle in SQL Data Warehouse zuordnen. Dies kann mithilfe der beiden folgenden einfachen Befehle erreicht werden.

1. Verwenden Sie den Befehl "CREATE EXTERNAL TABLE" zum Definieren der Struktur Ihrer Daten. Um sicherzustellen, dass der Status Ihrer Daten schnell und effizient erfasst wird, wird empfohlen, ein Skript für die SQL Server-Tabelle in SSMS zu erstellen und dann eine manuelle Anpassung entsprechend den Unterschieden der externen Tabelle vorzunehmen. Nach dem Erstellen einer externen Tabelle in Azure verweist diese weiterhin auf den gleichen Speicherort, selbst wenn Daten aktualisiert oder zusätzliche Daten hinzugefügt werden.  

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. Laden Sie Daten mit einer Anweisung vom Typ "CREATE TABLE...AS SELECT". 

```
CREATE TABLE <Table Name> 
WITH 
(
	CLUSTERED COLUMNSTORE INDEX,
	DISTRIBUTION = <HASH(<Column Name>)>/<ROUND_ROBIN>
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

Beachten Sie, dass Sie mit einer detaillierteren SELECT-Anweisung auch einen Unterabschnitt der Zeilen aus einer Tabelle laden können. Da PolyBase jedoch derzeit keine zusätzlichen Compute-Ressourcen per Push in Speicherkonten überträgt, ist der Vorgang beim Laden eines Unterabschnitts mit einer SELECT-Anweisung nicht schneller als das Laden des gesamten Datasets.

Zusätzlich zur Anweisung "`CREATE TABLE...AS SELECT`" können Sie mit einer Anweisung vom Typ "INSERT...INTO" auch Daten aus externen Tabellen in bereits vorhandene Tabellen laden.

##  Erstellen von Statistiken für die neu geladenen Daten 

Azure SQL Data Warehouse bietet noch keine Unterstützung für die automatische Erstellung oder die automatische Aktualisierung von Statistiken. Um die beste Leistung bei Abfragen zu erhalten, ist es wichtig, dass die Statistiken für alle Spalten aller Tabellen nach dem ersten Laden oder nach allen wesentlichen Datenänderungen erstellt werden. Eine ausführliche Erläuterung der Statistiken finden Sie im Thema [Statistiken][] in der Entwicklungsgruppe der Themen. Es folgt ein kurzes Beispiel, wie Sie Statistiken für die in diesem Beispiel geladene Tabelle erstellen können.


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Laden mit PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md
[Migrieren des Schemas]: sql-data-warehouse-migrate-schema.md
[Migrieren von Code]: sql-data-warehouse-migrate-code.md
[Statistiken]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[Installationsanweisungen für AZCopy]: https://azure.microsoft.com/de-DE/documentation/articles/storage-use-azcopy/
[Microsoft-Befehlszeilenprogrammen für SQL Server]: http://www.microsoft.com/de-DE/download/details.aspx?id=36433
[Import/Export]: https://azure.microsoft.com/de-DE/documentation/articles/storage-import-export-service/
[Azure-Speicherdokumentation]: https://azure.microsoft.com/de-DE/documentation/articles/storage-create-storage-account/
[Dokumentation zu ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=AcomDC_1223_2015-->