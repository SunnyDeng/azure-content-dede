<properties
   pageTitle="Tabellenentwurf in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Entwerfen von Tabellen in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Tabellenentwurf in SQL Data Warehouse #
SQL Data Warehouse ist ein verteiltes Datenbanksystem mit paralleler Massenverarbeitung (Massively Parallel Processing, MPP). Aus diesem Grund werden Daten an vielen verschiedenen Orten gespeichert, die als **Verteilungen** (Englisch: Distributions) bezeichnet werden. Jede **Verteilung** fungiert wie ein „Bucket“. Darin werden eindeutige Teilmengen von Daten im Data Warehouse gespeichert. Indem die Daten- und Verarbeitungsfunktionen über mehrere Knoten verteilt werden, ist in SQL Data Warehouse eine umfassende Skalierbarkeit möglich, die die Skalierbarkeit eines einzelnen Systems weit übersteigt.

Wenn eine Tabelle in SQL Data Warehouse erstellt wird, wird sie dabei auf alle Verteilungen verteilt.

In diesem Artikel werden die folgenden Themen behandelt:

- Unterstützte Datentypen
- Prinzipien der Datenverteilung
- Roundrobin-Verteilung
- Hashverteilung
- Tabellenpartitionierung
- Statistiken
- Nicht unterstützte Funktionen

## Unterstützte Datentypen
In SQL Data Warehouse werden die folgenden allgemeinen Geschäftsdatentypen unterstützt:

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

Sie können Spalten in Ihrem Data Warehouse identifizieren, die inkompatible Typen enthalten, indem Sie die folgende Abfrage verwenden:

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

Die Abfrage enthält alle benutzerdefinierten Datentypen, die auch nicht unterstützt werden.

Wenn Ihre Datenbank nicht unterstützte Datentypen enthält, ist das kein Grund zur Sorge. Unten sind einige Alternativen aufgeführt, die Sie stattdessen wählen können.

Alternativen:

- **geometry**: ein varbinary-Typ
- **geography**: stattdessen einen varbinary-Typ
- **hierarchyid**: CLR-Typ, nicht systemeigen
- **image**, **text**, **ntext**: falls textbasiert, varchar/nvarchar verwenden (je kleiner desto besser)
- **nvarchar(max)**: varchar(4000) oder kleiner zur Verbesserung der Leistung
- **numeric**: decimal
- **sql_variant**: Spalte in mehrere Spalten mit starker Typisierung unterteilen
- **sysname**: nvarchar(128)
- **table**: in temporäre Tabellen konvertieren
- **timestamp**: Code anpassen, sodass datetime2 und die `CURRENT_TIMESTAMP`-Funktion verwendet wird. Beachten Sie, dass Sie current_timestamp nicht als Standardeinschränkung verwenden können und dass der Wert nicht automatisch aktualisiert wird. Wenn Sie rowversion-Werte aus einer Spalte mit timestamp-Typ migrieren müssen, sollten Sie BINARY(8) oder VARBINARY(8) für NOT NULL- oder NULL-Zeilenversionswerte verwenden.
- **varchar(max)**: varchar(8000) oder kleiner zur Verbesserung der Leistung
- **uniqueidentifier**: varbinary(8)
- **user defined types**: zurück in systemeigene Typen konvertieren, falls möglich
- **xml**: varchar(8000) oder kleiner zur Verbesserung der Leistung verwenden – bei Bedarf auf Spalten aufteilen

Teilweise unterstützt:

- Standardeinschränkungen unterstützen nur Literale und Konstanten. Nicht deterministische Ausdrücke oder Funktionen, z. B. `GETDATE()` oder `CURRENT_TIMESTAMP`, werden nicht unterstützt.

> [AZURE.NOTE]Definieren Sie Ihre Tabellen so, dass die maximal mögliche Zeilengröße, einschließlich der vollständigen Länge der Spalten mit variabler Länge, 32.767 Byte nicht überschreitet. Sie können zwar eine Zeile mit Daten variabler Länge definieren, bei der dieser Wert überschritten wird, aber Sie können keine Daten in die Tabelle einfügen. Versuchen Sie außerdem, die Größe Ihrer Spalten mit variabler Länge zu beschränken, um beim Ausführen von Abfragen einen noch besseren Durchsatz zu erzielen.

## Prinzipien der Datenverteilung

Es gibt zwei Möglichkeiten für das Verteilen von Daten in SQL Data Warehouse:

1. Verteilen von Daten basierend auf Hashwerten aus einer einzelnen Spalte
2. Gleichmäßiges zufälliges Verteilen von Daten  

Die Datenverteilung wird auf Tabellenebene entschieden. Alle Tabellen werden so verteilt, dass Sie diese Entscheidung für jede Tabelle in Ihrer SQL Data Warehouse-Datenbank treffen können.

Die erste Option wird als **Roundrobin**-Verteilung bezeichnet (oder auch als „Random Hash“). Sie können sich dies wie eine Standardoption oder Absicherungsoption vorstellen.

Die zweite Option wird als **Hash**verteilung bezeichnet. Dies ist eine optimierte Form der Datenverteilung. Sie wird dann vorgezogen, wenn Cluster mit Tabellen über gemeinsame Verknüpfungs- bzw. Aggregationskriterien verfügen.

## Roundrobin-Verteilung

Die Roundrobin-Verteilung ist eine Methode, mit der Daten so gleichmäßig wie möglich über alle Verteilungen verteilt werden können. Puffer, die Zeilen mit Daten enthalten, werden dann wiederum jeder Verteilung zugeordnet (daher der Name „Roundrobin“). Der Vorgang wird wiederholt, bis alle Datenpuffer zugeordnet wurden. Bei einer verteilten Roundrobin-Tabelle werden die Daten zu keinem Zeitpunkt sortiert oder in eine besondere Reihenfolge gebracht. Aus diesem Grund wird eine Roundrobin-Verteilung auch als „Random Hash“ (zufälliger Hash) bezeichnet. Die Daten werden einfach möglichst gleichmäßig auf die Verteilungen verteilt.

Es folgt ein Beispiel für eine verteilte Roundrobin-Tabelle:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

Dies ist ebenfalls ein Beispiel für eine verteilte Roundrobin-Tabelle:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
)
;
```

> [AZURE.NOTE]Beachten Sie, dass im zweiten Beispiel kein Verteilungsschlüssel erwähnt wird. Roundrobin ist der Standard und daher nicht unbedingt erforderlich. Eine explizite Vorgehensweise wird aber als bewährte Methode angesehen, weil damit sichergestellt ist, dass andere Bearbeiter Ihre Absichten beim Durchsehen des Tabellenentwurfs klar erkennen können.

Dieser Tabellentyp wird häufig verwendet, wenn keine offensichtliche Schlüsselspalte für das Hashing der Daten vorhanden ist. Er kann auch für kleinere oder weniger signifikante Tabellen verwendet werden, bei denen die Verschiebungskosten nicht so hoch sind.

Das Laden von Daten in eine verteilte Roundrobin-Tabelle ist meist schneller als das Laden in eine verteilte Hashtabelle. Bei einer verteilten Roundrobin-Tabelle ist es nicht erforderlich, die Daten genau zu verstehen oder das Hashing vor dem Laden durchzuführen. Daher sind Roundrobin-Tabellen häufig gute Ladeziele.

> [AZURE.NOTE]Wenn Daten per Roundrobin verteilt werden, werden die Daten der Verteilung auf *Puffer*ebene zugeordnet.

### Empfehlungen

Erwägen Sie die Verwendung der Roundrobin-Verteilung für die Tabelle in den folgenden Fällen:

- Wenn kein offensichtlicher Verknüpfungsschlüssel vorhanden ist
- Wenn kein Kandidatenschlüssel für die Hashverteilung bekannt ist
- Wenn die Tabelle nicht über einen gemeinsamen Verknüpfungsschlüssel mit anderen Tabellen verfügt
- Wenn die Verknüpfung weniger signifikant als andere Verknüpfungen in der Abfrage ist
- Wenn die Tabelle eine Tabelle für das anfängliche Laden ist

## Hashverteilung

Bei der Hashverteilung wird eine interne Funktion verwendet, um ein Dataset auf die Verteilungen zu verteilen, indem das Hashing für eine einzelne Spalte durchgeführt wird. Wenn Daten gehasht werden, gibt es keine explizite Reihenfolge für die Zuordnung der Daten zur Verteilung. Der Hashvorgang selbst ist aber ein deterministischer Prozess. Dadurch werden die Ergebnisse des Hashvorgangs vorhersehbar. Beim Hashing einer Ganzzahlspalte mit dem Wert 10 ergibt sich immer derselbe Hashwert. Dies bedeutet, dass ***jede*** gehashte Ganzzahlspalte, die den Wert 10 enthält, derselben Verteilung zugeordnet wird. Dies gilt auch tabellenübergreifend.

Die Vorhersagbarkeit des Hashings ist äußerst wichtig. Dies bedeutet, dass die Hashverteilung der Daten zu Leistungsverbesserungen führen kann, wenn Daten gelesen und Tabellen verknüpft werden.

Wie unten dargestellt, kann die Hashverteilung sehr effektiv für die Abfrageoptimierung sein. Daher wird dies als eine optimierte Form der Datenverteilung angesehen.

> [AZURE.NOTE]Beachten Sie Folgendes: Das Hashing basiert nicht auf dem Wert der Daten, sondern auf dem Typ der Daten, die gehasht werden.

Unten ist eine Tabelle angegeben, für die eine Hashverteilung nach ProductKey durchgeführt wurde.

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE]Wenn für die Daten eine Hashverteilung erfolgt, werden sie der Verteilung auf Zeilenebene zugeordnet.

## Tabellenpartitionen
Tabellenpartitionen werden unterstützt und sind einfach zu definieren.

Beispiel: Partitionierung von SQL Data Warehouse mit dem `CREATE TABLE`-Befehl:

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Beachten Sie, dass in der Definition keine Partitionierungsfunktion oder ein entsprechendes Schema vorhanden ist. Dies wird alles bei der Erstellung der Tabellen erledigt. Sie müssen lediglich die Grenzpunkte für die Spalte identifizieren, die als Partitionierungsschlüssel dienen soll.

## Statistiken

In SQL Data Warehouse wird ein verteilter Abfrageoptimierer verwendet, um den passenden Abfrageplan zu erstellen, wenn Benutzer Tabellen abfragen. Nach der Erstellung werden über den Abfrageplan die Strategie und die Methode bereitgestellt, die von der Datenbank zum Zugreifen auf die Daten und Erfüllen der Benutzeranforderung verwendet werden. Der Abfrageoptimierer von SQL Data Warehouse basiert auf Kosten. Anders ausgedrückt: Es werden verschiedene Optionen (Pläne) basierend auf den relativen Kosten verglichen, und es wird jeweils der effizienteste verfügbare Plan ausgewählt. Daher werden für SQL Data Warehouse viele Informationen benötigt, um fundierte kostenbezogene Entscheidungen treffen zu können. Es sind Statistikinformationen zur Tabelle (für die Tabellengröße) und Informationen in Datenbankobjekten vorhanden, die als `STATISTICS` bezeichnet werden.

Statistiken werden für einzelne oder mehrere Spalten von Indizes oder Tabellen erstellt. Sie liefern dem kostenbasierten Optimierer wichtige Informationen zur Kardinalität und Selektivität von Werten. Dies ist von besonderem Interesse, wenn der Optimierer JOINs, GROUP BY-, HAVING- und WHERE-Klauseln in einer Abfrage auswerten muss. Daher ist es sehr wichtig, dass die in diesen Statistikobjekten enthaltenen Informationen den aktuellen Zustand der Tabelle *genau* widerspiegeln. Es ist wichtig zu verstehen, dass es die Genauigkeit der Kosten ist, die entscheidend ist. Wenn die Statistiken den Zustand der Tabelle genau wiedergeben, können Pläne zur Ermittlung der niedrigsten Kosten verglichen werden. Wenn sie nicht genau sind, wird in SQL Data Warehouse unter Umständen der falsche Plan gewählt.

Statistiken auf Spaltenebene in SQL Data Warehouse sind benutzerdefiniert.

Anders ausgedrückt: Wir müssen sie selbst erstellen. Wie wir gerade erfahren haben, sollte dies nicht übersehen werden. Dies ist ein wichtiger Unterschied zwischen SQL Server und SQL Data Warehouse. Bei SQL Server werden Statistiken automatisch erstellt, wenn Spalten abgefragt werden. Standardmäßig werden diese Statistiken in SQL auch automatisch aktualisiert. In SQL Data Warehouse müssen Statistiken aber manuell erstellt und verwaltet werden.

### Empfehlungen

Beachten Sie die folgenden Empfehlungen zum Generieren von Statistiken:

1. Erstellen Sie Einzelspaltenstatistiken für Spalten, die in `WHERE`-, `JOIN`-, `GROUP BY`-, `ORDER BY`- und `DISTINCT`-Klauseln verwendet werden.
2. Generieren Sie Mehrspaltenstatistiken für zusammengesetzte Klauseln.
3. Aktualisieren Sie Statistiken regelmäßig. Denken Sie daran, dass dies nicht automatisch durchgeführt wird!

>[AZURE.NOTE]In der Regel wird in SQL Server Data Warehouse ausschließlich `AUTOSTATS` genutzt, um die Spaltenstatistiken auf dem aktuellen Stand zu halten. Dies ist auch für SQL Server Data Warehouses nicht die bewährte Methode. `AUTOSTATS` wird bei einer Änderungsrate von 20 % ausgelöst. Dies ist für große Faktentabellen mit Millionen oder Milliarden von Zeilen ggf. nicht ausreichend. Daher ist es immer ratsam, die Aktualisierung der Statistiken im Auge zu behalten. So ist sichergestellt, dass die Statistiken die Kardinalität der Tabelle präzise widerspiegeln.

## Nicht unterstützte Funktionen
In SQL Data Warehouse werden die folgenden Funktionen nicht verwendet oder nicht unterstützt:

- Primärschlüssel
- Fremdschlüssel
- CHECK-Einschränkungen
- UNIQUE-Einschränkungen
- Eindeutige Indizes
- Berechnete Spalten
- Spalten mit geringer Dichte
- Benutzerdefinierte Typen
- Indizierte Sichten
- Identitäten
- Sequenzen
- Trigger
- Synonyme


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->