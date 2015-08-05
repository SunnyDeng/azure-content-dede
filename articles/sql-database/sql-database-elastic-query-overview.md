<properties
    pageTitle="Übersicht über die Abfrage für elastische Datenbanken in Azure SQL-Datenbank"
    description="Übersicht über das Abfragefeature für elastische Datenbanken"
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="sidneyh"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/09/2015"
    ms.author="sidneyh" />

# Übersicht über die Abfrage für elastische Datenbanken in Azure SQL-Datenbank (Vorschau)

Mit der Vorschau der **Abfragefunktion für elastische Datenbanken** können Sie eine Transact-SQL-Abfrage ausführen, die mehrere Datenbanken in Azure SQL-Datenbank umfasst. Sie können eine Verbindung zwischen Tools von Microsoft sowie Drittanbietern (Excel, PowerBI, Tableau usw.) und Datenebenen mit mehreren Datenbanken herstellen, insbesondere wenn diese Datenbanken ein gemeinsames Schema (auch bekannt als horizontale Partitionierung oder Sharding) nutzen. Mit dieser Funktion können Sie Abfragen horizontal auf große Datenebenen in SQL-Datenbanken skalieren und die Abfrageergebnisse in Berichten für Business Intelligence (BI) darstellen.

Informationen zum Erstellen einer Anwendung für Abfragen an elastische Datenbanken finden Sie unter [Erste Schritte mit Abfragen für elastische Datenbanken](sql-database-elastic-query-getting-started.md).

## Szenarios mit elastischen Datenbankabfragen

Das Ziel der Abfrage für elastischen Datenbanken ist es, Berichterstellungsszenarios zu erleichtern, in denen mehrere Datenbanken Zeilen für ein einzelnes Gesamtergebnis beitragen. Die Abfrage kann entweder vom Benutzer oder durch die Anwendung direkt oder indirekt über Tools erstellt werden, die mit der Abfragedatenbank verbunden sind. Dies ist besonders hilfreich beim Erstellen von Berichten mit Tools für kommerzielle BI oder zur Datenintegration bzw. jeglicher Software, die nicht geändert werden kann. Mit einer Abfrage für elastische Datenbanken können Sie problemlos Abfragen zwischen mehreren Datenbanken mithilfe der üblichen SQL Server-Verbindungsumgebung in Tools wie Excel, PowerBI, Tableau oder Cognos ausführen.

Außerdem ermöglicht eine Abfrage für elastische Datenbanken den einfachen Zugriff auf eine ganze Sammlung von Datenbanken, die von SQL Server Management Studio oder Visual Studio ausgegeben werden, und sie vereinfacht datenbankübergreifende Abfragen aus Entity Framework oder anderen ORM-Umgebungen. Abbildung 1 zeigt ein Szenario, in dem eine vorhandene Cloudanwendung (die die Bibliothek der Tools für elastische Datenbanken verwendet) auf einer horizontal skalierten Datenebene aufbaut und eine Abfrage für elastische Datenbanken für datenbankübergreifende Berichte verwendet wird.

**Abbildung 1**

![Abfrage für elastische Datenbanken in der horizontal skalierten Datenebene][1]

Die Datenebene ist über viele Datenbanken mit einem gemeinsamen Schema horizontal skaliert. Dieser Ansatz wird auch horizontale Partitionierung oder Sharding genannt. Die Partitionierung kann mit (1)der [Clientbibliothek für elastischen Datenbanken](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) oder (2) mithilfe eines anwendungsspezifischen Modells zur Verteilung von Daten über mehrere Datenbanken ausgeführt und verwaltet werden. Mit dieser Topologie müssen sich Berichte häufig über mehrere Datenbanken erstrecken. Mit einer Abfrage für elastische Datenbanken können Sie nun eine Verbindung mit einer einzelnen SQL-Datenbank herstellen. Die Abfrageergebnisse der Remotedatenbanken werden so angezeigt, als sie ob von einer einzigen virtuellen Datenbank generiert wurden.

> [AZURE.NOTE]Die flexible Datenbankabfrage funktioniert am besten bei gelegentlichen Reporting-Szenarien, bei denen der Großteil der Verarbeitung auf der Datenebene ausgeführt werden kann. Bei hohen Reporting-Arbeitslasten oder Data Warehousing-Szenarien mit komplexeren Abfragen, sollten Sie auch die Verwendung von [Azure SQL Data Warehouse](http://azure.microsoft.com/services/sql-data-warehouse/) in Betracht ziehen.


## Topologie für Abfragen für elastische Datenbanken

Die Ausführung von Berichtsaufgaben mit einer Abfrage für elastische Datenbanken über eine horizontal partitionierte Datenebene erfordert eine Shard-Zuordnung mit elastischer Skalierung, um die Datenbanken der Datenebene darzustellen. Normalerweise wird nur eine einzige Shard-Zuordnung in diesem Szenario verwendet, und eine dedizierte Datenbank mit Abfragefunktionen für elastische Datenbanken dient als Einstiegspunkt für Berichtsabfragen. Nur diese dedizierte Datenbank muss mit Abfrageobjekten für elastische Datenbanken konfiguriert werden, wie im Folgenden beschrieben. Abbildung 2 zeigt diese Topologie und die Konfiguration mit der Abfragedatenbank und -Shard-Zuordnung für elastische Datenbanken.

> [AZURE.NOTE]Die dedizierte Abfragedatenbank für elastische Datenbanken muss eine SQL-DB-v12-Datenbank sein, und es wird zunächst nur der Premium-Tarif unterstützt. Es gibt keine Einschränkungen für die Shards selbst.

**Abbildung 2**

![Verwenden von Abfragen für elastische Datenbanken zu Berichterstattung über Sharding-Ebenen][2]

(Ein **Shardlet** umfasst alle Daten, die mit einem Wert eines Sharding-Schlüssels in einem Shard verknüpft sind. Ein **Sharding-Schlüssel** ist ein Spaltenwert, der bestimmt, wie Daten über Shards hinweg verteilt werden. Beispielsweise kann eine Datenverteilung nach Region über Regions-IDs als Sharding-Schlüssel verfügen. Weitere Informationen finden Sie im [Glossar zu elastischer Skalierbarkeit](sql-database-elastic-scale-glossary.md).)


In Zukunft werden weitere Topologien von der Abfragefunktion für elastische Datenbanken unterstützt. Dieser Artikel wird mit Informationen zu neuen Funktionen aktualisiert, sobald sie verfügbar sind.

## Abfragen elastischer Datenbanken durch Konfiguration einer Shard-Zuordnung

Zum Erstellen einer Abfragelösung für elastische Datenbanken muss die [**Shard-Zuordnung**](sql-database-elastic-scale-shard-map-management.md) der Tools für elastische Datenbanken die Remotedatenbank repräsentieren. Wenn Sie bereits die Clientbibliothek für elastische Datenbanken verwenden, können Sie Ihre vorhandene Shard-Zuordnung nutzen. Andernfalls müssen Sie mithilfe der Tools für elastische Datenbanken eine Shard-Zuordnung erstellen.

Im folgenden C#-Beispielcode wird gezeigt, wie eine Shard-Zuordnung mit einer einzigen Remotedatenbank als Shard hinzugefügt wird.

    ShardMapManagerFactory.CreateSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerCreateMode.ReplaceExisting, RetryBehavior.DefaultRetryBehavior);
    smm = ShardMapManagerFactory.GetSqlShardMapManager(
      "yourconnectionstring",
      ShardMapManagerLoadPolicy.Lazy,
      RetryBehavior.DefaultRetryBehavior);
    map = smm.CreateRangeShardMap<int>("yourshardmapname");
    shard = map.CreateShard(new ShardLocation("yoursqldbserver", "yoursqldbdatabasename"));

Weitere Informationen zu Shard-Zuordnungen, finden Sie unter [Verwalten von Shard-Zuordnungen](sql-database-elastic-scale-shard-map-management.md).

Um die Funktion für elastische Datenbankabfragen zu verwenden, müssen Sie zunächst die Shard-Zuordnung erstellen und Ihre Remotedatenbanken als Shards registrieren. Dies ist eine einmalige Aufgabe. Sie müssen Ihre Shard-Zuordnung nur ändern, wenn Sie Remotedatenbanken hinzufügen oder entfernen. Dies sind inkrementelle Vorgänge einer vorhandenen Shard-Zuordnung.


## Erstellen von Datenbankobjekten für Abfragen für elastische Datenbanken

Um die Remotetabellen zu beschreiben, auf die von einem Abfrageendpunkt für elastische Datenbanken zugegriffen werden kann, wurde die Möglichkeit eingeführt, externe Tabellen zu definieren, die Ihrer Anwendung und Tools von Drittanbietern wie lokale Tabellen angezeigt werden. Abfragen können für diese Datenbankobjekte implizit über die Tools oder explizit von SQL Server Management Studio, Visual Studio Data Tools oder einer Anwendung gesendet werden. Die Abfrage für elastische Datenbanken wird wie jede andere Transact-SQL-Anweisung ausgeführt – mit dem wesentlichen Unterschied, dass die Verarbeitung dieser Abfrage auf die möglicherweise vielen Remotedatenbanken verteilt wird, die den externen Objekten zugrunde liegen.

Die Abfragefunktion für elastische Datenbanken stützt sich auf diese vier DDL-Anweisungen. Normalerweise werden diese DDL-Anweisungen nur einmal oder selten verwendet, wenn das Schema der Anwendung geändert wird.

*    [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx)
*    [CREATE CREDENTIAL](https://msdn.microsoft.com/library/ms189522.aspx)
*    [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)
*    [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen

Die Anmeldeinformationen umfassen Benutzer-ID und Kennwort, die von der Abfrage für elastische Datenbanken zur Verbindung mit der elastisch skalierten Shard-Zuordnung und den Remotedatenbanken in Azure SQL-Datenbank verwendet werden. Sie können den erforderlichen Hauptschlüssel und die Anmeldeinformationen mit der folgenden Syntax erstellen:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';  
    CREATE CREDENTIAL <credential_name> ON DATABASE
    WITH IDENTITY = '<shard_map_username>',
    SECRET = '<shard_map_password>'
     [;]
Stellen Sie sicher, dass &lt;shard_map_username> kein "@servername"-Suffix enthält.

Verwenden Sie die folgende Syntax, um Hauptschlüssel und Anmeldeinformationen zu übergeben:

    DROP CREDENTIAL <credential_name> ON DATABASE;
    DROP MASTER KEY;  

### Externe Datenquellen

Im nächsten Schritt müssen Sie Ihre Shard-Zuordnung als externe Datenquelle registrieren. Die Syntax zum Erstellen und Löschen von externen Datenquellen wird wie folgt definiert:

      CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
                     (TYPE = SHARD_MAP_MANAGER,
                     LOCATION = '<fully_qualified_server_name>',
                     DATABASE_NAME = '<shardmap_database_name>',
                     CREDENTIAL = <credential_name>,
                     SHARD_MAP_NAME = '<shardmapname>'
    ) [;]

Mit der folgenden Anweisung können Sie eine externe Datenquelle übergeben:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

Der Benutzer muss über die Berechtigung ALTER ANY EXTERNAL DATA SOURCE verfügen. Diese Berechtigung ist in der ALTER DATABASE-Berechtigung enthalten.

**Beispiel**

Das folgende Beispiel veranschaulicht die Verwendung der CREATE-Anweisung für externe Datenquellen.

    CREATE EXTERNAL DATA SOURCE MyExtSrc
    WITH
    (
    TYPE=SHARD_MAP_MANAGER,
    LOCATION='myserver.database.windows.net',
    DATABASE_NAME='ShardMapDatabase',
    CREDENTIAL= SMMUser,
    SHARD_MAP_NAME='ShardMap'
    );

Sie können die Liste der aktuellen externen Datenquellen aus der folgenden Katalogsicht abrufen:

    select * from sys.external_data_sources;

Beachten Sie, dass die gleichen Anmeldeinformationen während der Verarbeitung der Abfrage zum Lesen der Shard-Zuordnung und für den Zugriff auf die Daten in den Remotedatenbanken verwendet werden.


### Externe Tabellen

Mit der elastischen Datenbankabfrage erweitern wir die vorhandene externe Tabellensyntax, sodass auf Tabellen verwiesen wird, die in (mehrere) Remotedatenbanken in Azure SQL-Datenbank partitioniert sind. Mit dem externen Quellenkonzept von oben gestaltet sich die Syntax zum Erstellen und Übergeben von externen Tabellen wie folgt:

    CREATE EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name
        ( { <column_definition> } [ ,...n ])
        { WITH ( <sharded_external_table_options> ) }
    )[;]

    <sharded_external_table_options> ::=
          DATA_SOURCE = <External_Data_Source>,
          DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED | ROUND_ROBIN

Die Sharding-Richtlinie steuert, ob eine Tabelle als Shard-Tabelle oder replizierte Tabelle behandelt wird. Bei einer Shard-Tabelle werden Datenüberschneidungen aus verschiedenen Shards vermieden. Replizierte Tabellen wiederum verfügen auf jedem Shard über die gleichen Daten. Der Abfrageprozessor verwendet diese Informationen zur richtigen und effizienteren Verarbeitung. Die Roundrobin-Verteilung gibt an, dass eine anwendungsspezifische Methode zur Verteilung der Daten dieser Tabelle verwendet wird.

    DROP EXTERNAL TABLE [ database_name . [ dbo ] . | dbo. ] table_name[;]

Es sind Berechtigungen für **CREATE/DROP EXTERNAL TABLE**: ALTER ANY EXTERNAL DATA SOURCE erforderlich, die auch für den Verweis auf die zugrunde liegende Datenquelle benötigt werden.

**Beispiel**: Das folgende Beispiel veranschaulicht, wie Sie eine externe Tabelle erstellen:

    CREATE EXTERNAL TABLE [dbo].[order_line](
        [ol_o_id] [int] NOT NULL,
        [ol_d_id] [tinyint] NOT NULL,
        [ol_w_id] [int] NOT NULL,
        [ol_number] [tinyint] NOT NULL,
        [ol_i_id] [int] NOT NULL,
        [ol_delivery_d] [datetime] NOT NULL,
        [ol_amount] [smallmoney] NOT NULL,
        [ol_supply_w_id] [int] NOT NULL,
        [ol_quantity] [smallint] NOT NULL,
        [ol_dist_info] [char](24) NOT NULL
    )
    WITH
    (
        DATA_SOURCE = MyExtSrc,
        DISTRIBUTION=SHARDED(ol_w_id)
    );

Das folgende Beispiel zeigt, wie Sie die Liste der externen Tabellen aus der aktuellen Datenbank abrufen:

    select * from sys.external_tables;


## Berichterstellung und Abfrage

### Abfragen
Sobald Sie Ihre externen Tabellen und die externe Datenquelle definiert haben, können Sie gebräuchliche SQL-Datenbank-Verbindungszeichenfolgen zum Verbinden mit der Datenbank verwenden, für die die Abfragefunktion für elastische Datenbanken aktiviert ist. Sie können jetzt vollständige schreibgeschützte Abfragen für Ihre externen Tabellen ausführen. Dabei gelten jedoch einige Einschränkungen, die im Abschnitt [Einschränkungen](#preview-limitations) unten erläutert werden.

**Beispiel**: Die folgende Abfrage führt eine Verknüpfung in drei Richtungen zwischen Lagern, Bestellungen und Auftragspositionen aus. Sie nutzt mehrere Aggregate und einen selektiven Filter. Es wird davon ausgegangen, dass Lager, Bestellungen und Auftragspositionen durch die Warehouse-ID-Spalte partitioniert sind. Eine Abfrage für elastische Datenbanken kann die Verknüpfungen in den Remotedatenbanken zusammenführen und die Verarbeitung des ressourcenintensiven Teils der Abfrage horizontal skalieren.

    select
        w_id as warehouse,
        o_c_id as customer,
        count(*) as cnt_orderline,
        max(ol_quantity) as max_quantity,
        avg(ol_amount) as avg_amount,
        min(ol_delivery_d) as min_deliv_date
    from warehouse
    join orders
    on w_id = o_w_id
    join order_line
    on o_id = ol_o_id and o_w_id = ol_w_id
    where w_id > 100 and w_id < 200
    group by w_id, o_c_id

### Gespeicherte Prozedur SP_EXECUTE_FANOUT

SP_EXECUTE_FANOUT ist eine gespeicherte Prozedur, die Zugriff auf die Datenbanken bietet, die durch eine Shard-Zuordnung dargestellt werden. Die folgenden Parameter können für diese gespeicherte Prozedur verwendet werden:

-    **Servername** (nvarchar): Vollqualifizierter Name des logischen Servers, auf dem die Shard-Zuordnung gehostet wird.
-    **Datenbankname der Shard-Zuordnung** (nvarchar): Der Name der Shard-Zuordnungsdatenbank.
-    **Benutzername** (nvarchar): Der Benutzername für die Anmeldung bei der Shard-Zuordnungsdatenbank und den Remotedatenbanken.
-    **Kennwort** (nvarchar): Das Kennwort für den Benutzer.
-    **Shard-Zuordnungsname** (nvarchar): Der Name der Shard-Zuordnung für die Abfrage.
-    **Abfrage**: Die Abfrage, die für die einzelnen Shards ausgeführt wird.

Es werden die in den Aufrufparametern angegebenen Shard-Zuordnungsinformationen verwendet, um die gegebene Anweisung für alle in der Shard-Zuordnung registrierten Shards auszuführen. Alle Ergebnisse werden mithilfe von UNION ALL-Semantik zusammengeführt wie bei mehrfachen Shard-Abfragen. Das Ergebnis enthält auch die zusätzliche Spalte "virtual" mit dem Namen der Remotedatenbank.

Beachten Sie, dass die gleichen Anmeldeinformationen für die Verbindung mit der Shard-Zuordnungsdatenbank und den Shards verwendet werden.

**Beispiel**:

    sp_execute_fanout 'myserver.database.windows.net', N'ShardMapDb', N'myuser', N'MyPwd', N'ShardMap', N'select count(w_id) as foo from warehouse'

## Konnektivität für Tools
Sie können herkömmliche SQL-Datenbank-Verbindungszeichenfolgen für Ihre Abfragedatenbank für elastische Datenbanken zur Verbindung Ihrer BI und Datenintegrationstools verwenden. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Verwenden Sie anschließend externe Objekte in der Abfragedatenbank für elastische Datenbanken wie mit jeder anderen SQL Server-Datenbank, die Sie mit dem Tool verbinden würden.

## Bewährte Methoden
*    Stellen Sie sicher, dass die Verwaltungsdatenbank für die Shard-Zuordnung und die Datenbanken, die in der Shard-Zuordnung definiert sind, Zugriff von Microsoft Azure über Firewallregeln zulassen. Dies ist notwendig, damit die Abfragedatenbank für elastische Datenbanken eine Verbindung herstellen kann. Weitere Informationen finden Sie unter [Firewall für Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).
*    Eine Abfrage für elastische Datenbanken überprüft die Verteilung der in der externen Tabelle definierten Daten nicht und erzwingt diese auch nicht. Wenn Ihre tatsächliche Datenverteilung sich von der Verteilung in der Tabellendefinition unterscheidet, können Ihre Abfragen zu unerwarteten Ergebnissen führen.
*    Eine Abfrage für elastische Datenbanken funktioniert am besten für Abfragen, in denen der größte Teil der Berechnung in den Shards durchgeführt wird. In der Regel erhalten Sie optimale Abfrageleistung mit benutzerdefinierten Filterprädikaten, die in den Shards oder Verknüpfungen über die Partitionierungsschlüssel ausgewertet werden können, die auf partitionsbezogene Weise auf allen Shards ausgeführt werden können. Andere Abfragemuster müssen möglicherweise große Mengen an Daten aus den Shards auf den Hauptknoten laden, wodurch Leistungseinbußen auftreten.

## Kosten

Die Abfrage für elastische Datenbanken ist in den Kosten für Azure SQL-Datenbank-Datenbanken enthalten. Beachten Sie, dass auch Topologien unterstützt werden, bei denen die Remotedatenbanken sich in einem anderen Rechenzentrum befinden als der Endpunkt für die Abfrage für elastische Datenbanken. Aus Remotedatenbanken ausgehende Daten werden jedoch zu regulären Preisen für ausgehende Azure-Daten berechnet.

## Einschränkungen der Vorschau

Bedenken Sie in Bezug auf die Vorschau Folgendes:

*    Die Abfrage für elastische Datenbanken wird zunächst nur im SQL-DB-V12-Premium-Tarif verfügbar sein, obwohl die Remotedatenbanken, auf die mit einer Abfrage für elastischen Datenbanken zugegriffen wird, aus jedem Tarif sein können.
* Externe Tabellen, auf die durch die externe Datenquelle verwiesen wird, unterstützen nur Lesevorgänge über die Remotedatenbanken. Sie können jedoch alle Transact-SQL-Funktionen auf die elastische Abfragedatenbank zeigen lassen, in der sich die Definition der externen Tabelle selbst befindet. Dies kann z. B. hilfreich sein, um temporäre Ergebnisse mit SELECT column_list INTO local_table beizubehalten oder um gespeicherte Prozeduren für die Datenbank der elastischen Datenbankabfrage zu definieren, die auf externe Tabellen verweisen.
*    Abfrageparameter können derzeit nicht auf Remotedatenbanken angewendet werden. Abfragen mit Parametern müssen alle Daten an den Hauptknoten übergeben und erzielen dadurch je nach Datengröße eine geringere Leistung. Eine temporäre Problemumgehung besteht darin, Parameter in Abfragen zu vermeiden oder die RECOMPILE-Option zu verwenden, sodass Parameter automatisch durch ihre aktuellen Werte ersetzt werden.
* Statistiken auf Spaltenebene werden für externe Tabellen derzeit nicht unterstützt.
* Die Abfrage für elastische Datenbanken führt derzeit keine Shard-Löschung durch, wenn Prädikate für Sharding-Schlüssel ein gefahrloses Ausschließen der Verarbeitung bestimmter Remotedatenbanken zulassen würden. Daher werden Abfragen immer alle Remotedatenbanken bearbeiten, die durch die externen Datenquellen der Abfrage dargestellt werden.
* Alle Abfragen, die Verknüpfungen zwischen Tabellen in unterschiedlichen Datenbanken einschließen, können eine große Anzahl von Zeilen zur Verarbeitung zurück in die Abfragedatenbank für elastische Datenbanken übertragen, was zu Leistungseinbußen führen kann. Es wird empfohlen, Abfragen zu entwickeln, die für jede Remotedatenbank lokal verarbeitet werden können, oder WHERE-Klauseln zu verwenden, um Zeilen aus den einzelnen Datenbanken zu beschränken, bevor die Verknüpfung ausgeführt wird.
*    Die Syntax für die Metadatendefinition der Abfrage für elastischer Datenbanken wird innerhalb der Vorschauversion geändert.
*    Transact-SQL-Skriptfunktionen in SSMS oder SSDT funktionieren zurzeit nicht mit Abfrageobjekten für elastische Datenbanken.

## Feedback
Teilen Sie uns Ihr Feedback auf Disqus oder Stackoverflow mit. Für uns sind alle Arten von Feedback zum Dienst (Fehler, Designprobleme und fehlende Features) interessant.

## Nächste Schritte
Führen Sie zum Einstieg in Abfragen für elastische Datenbanken unser schrittweises Lernprogramm aus, um innerhalb von Minuten zu einem umfassenden Arbeitsbeispiel zu gelangen: [Erste Schritte mit Abfragen für elastische Datenbanken](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png

<!--anchors-->

<!---HONumber=July15_HO4-->