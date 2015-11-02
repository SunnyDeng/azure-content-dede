<properties
    pageTitle="Elastische Datenbankabfragen für Sharding (horizontale Partitionierung) | Microsoft Azure"
    description="Informationen zum Einrichten elastischer Abfragen horizontal partitionierter Datenbanken"    
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
    ms.date="10/15/2015"
    ms.author="sidneyh;torsteng" />

# Elastische Datenbankabfragen bei Sharding (horizontaler Partitionierung)

In diesem Dokument wird die Einrichtung von elastischen Datenbankabfragen für Szenarien mit horizontaler Partitionierung und die Ausführung von Abfragen erläutert. Eine Definition des Szenarios mit horizontaler Partitionierung finden Sie in der [Übersicht über elastische Datenbankabfragen](sql-database-elastic-query-overview.md).

Die Funktionalität ist Teil des Azure SQL-Datenbankfeatures [Elastische Datenbank](sql-database-elastic-scale.md).
 
## Erstellen von Datenbankobjekten

Mit einer elastischen Datenbankabfragen wird die T-SQL-Syntax so erweitert, dass auf Datenebenen verwiesen wird, die Sharding (bzw. horizontale Partitionierung) verwenden, um Daten auf viele Datenbanken zu verteilen. Dieser Abschnitt bietet eine Übersicht über die DDL-Anweisungen, die elastischen Abfragen von Shardingtabellen zugeordnet sind. Diese Anweisungen erstellen die Metadatendarstellung Ihrer Shardingdatenebene in der elastischen Abfragedatenbank. Eine Voraussetzung für die Ausführung dieser Anweisungen ist das Erstellen einer Shardzuordnung mithilfe der Clientbibliothek für elastische Datenbanken. Weitere Informationen finden Sie unter [Verwaltung von Shardzuordnungen](sql-database-elastic-scale-shard-map-management.md), oder verwenden Sie das Beispiel im Thema [Erste Schritte mit elastischen Datenbanktools](sql-database-elastic-scale-get-started.md), um eine zu erstellen.

Das Definieren der Datenbankobjekte für eine elastische Datenbankabfrage beruht auf den folgenden T-SQL-Anweisungen, die für das Szenario mit horizontaler Partitionierung weiter unten erläutert werden:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen 

Benutzer-ID und Kennwort bilden die Anmeldeinformationen, die von der elastischen Abfrage zur Verbindung mit Ihren Remotedatenbanken in Azure SQL-Datenbank verwendet werden. So erstellen Sie den erforderlichen Hauptschlüssel und die Anmeldeinformationen mit der folgenden Syntax:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]

Geben Sie zum Löschen der Anmeldeinformationen und des Schlüssels Folgendes ein:

    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
**Hinweis** Stellen Sie sicher, dass *< username>* kein *@servername*-Suffix enthält.

### 1\.2 Externe Datenquellen

Stellen Sie die Informationen zu Ihrer Shardzuordnung und Datenebene bereit, indem Sie eine externe Datenquelle definieren. Die externe Datenquelle verweist auf Ihre Shardzuordnung. Eine elastische Abfrage verwendet anschließend die externe Datenquelle und zugrunde liegende Shardzuordnung zum Auflisten der Datenbanken, die zur Datenebene gehören. Die Syntax zum Erstellen einer externen Datenquelle ist wie folgt:

	<External_Data_Source> ::=    
	CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                               	           
			(TYPE = SHARD_MAP_MANAGER,
                   	LOCATION = '<fully_qualified_server_name>',
			DATABASE_NAME = ‘<shardmap_database_name>',
			CREDENTIAL = <credential_name>, 
			SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 
 
So sieht die Syntax zum Löschen einer externen Datenquelle aus:

	DROP EXTERNAL DATA SOURCE <data_source_name>[;] 

#### Berechtigungen für CREATE/DROP EXTERNAL DATA SOURCE 

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

Beachten Sie, dass die gleichen Anmeldeinformationen während der Verarbeitung der elastischen Abfrage zum Lesen der Shardzuordnung und für den Zugriff auf die Daten in den Shards verwendet werden.

### 1\.3 Externe Tabellen 
 
Die elastische Abfrage erweitert die DDL für externe Tabellen so, dass auf externe Tabellen verwiesen wird, die über mehrere Datenbanken horizontal partitioniert sind. Die Definition der externen Tabelle deckt die folgenden Aspekte ab:

* **Schema**: Die DDL für externe Tabellen definiert ein Schema, das Ihre Abfragen verwenden können. Das in Ihrer Definition für externe Tabellen angegebene Schema muss mit dem Schema der Tabellen in den Shards übereinstimmen, in denen die eigentlichen Daten gespeichert werden. 

* **Datenverteilung**: Die DDL für externe Tabellen definiert die Verteilung der Daten auf Ihrer Datenebene. Beachten Sie, dass Azure SQL-Datenbank die Verteilung, die Sie für die externe Tabelle definieren, nicht mit Blick auf die tatsächliche Verteilung in den Shards überprüft. Sie müssen sicherstellen, dass die Verteilung der tatsächlichen Daten auf die Shards Ihrer Definition für externe Tabellen entspricht.

* **Datenebenenverweis**: Die DDL für externe Tabellen verweist auf eine externe Datenquelle. Die externe Datenquelle gibt eine Shardzuordnung an, die der externen Tabelle die Informationen bereitstellt, die benötigt werden, um alle Datenbanken in Ihrer Datenebene zu finden.

Bei Verwenden einer externen Datenquelle ist die Syntax zum Erstellen und Löschen externer Tabellen, wie im vorherigen Abschnitt beschrieben, wie folgt:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
	    { WITH ( <sharded_external_table_options> ) }
	) [;]  
	
	<sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
	  [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

Die DATA\_SOURCE-Klausel definiert die externe Datenquelle (bei horizontaler Partitionierung eine Shardzuordnung), die für die externe Tabelle verwendet wird.

Die Klauseln SCHEMA\_NAME und OBJECT\_NAME bieten die Möglichkeit, die Definition der externen Tabelle einer Tabelle in einem anderen Schema im Shard bzw. einer Tabelle mit einem anderen Namen zuzuordnen. Falls nicht angegeben, wird davon ausgegangen, dass das Schema des Remoteobjekts „dbo“ und sein Name mit dem definierten Namen der externen Tabelle identisch ist.

Die Klauseln SCHEMA\_NAME und OBJECT\_NAME sind besonders nützlich, wenn der Name der Remotetabelle bereits in der Datenbank verwendet wird, in der Sie die externe Tabelle erstellen möchten. Ein Beispiel für dieses Problem liegt vor, wenn Sie eine externe Tabelle zum Abrufen einer aggregrierten Sicht von Katalogsichten oder DMVs für Ihre horizontal hochskalierte Datenebene definieren möchten. Da Katalogsichten und DMVs bereits lokal vorhanden sind, können Sie ihre Namen nicht für die Definition der externen Tabelle verwenden. Verwenden Sie stattdessen in den Klauseln SCHEMA\_NAME und/oder OBJECT\_NAME einen anderen Namen und den Namen der Katalogsicht oder DMV. (Siehe das folgende Beispiel.)

Die DISTRIBUTION-Klausel gibt die Datenverteilung für diese Tabelle an:

* SHARDED bedeutet, dass die Daten dieser Tabelle horizontal auf die Datenbanken in Ihrer Shardzuordnung partitioniert werden. Der Partitionierungsschlüssel für die Datenverteilung wird im <sharding_column_name>-Parameter erfasst.  

* REPLICATED bedeutet, dass identische Kopien der Tabelle für jede Datenbank in der Shardzuordnung vorhanden sind. Azure SQL-Datenbank speichert keine Kopien der Tabelle. Sie müssen sicherstellen, dass das Replikat in allen Datenbanken identisch ist.

* ROUND\_ROBIN bedeutet, dass die Tabelle mithilfe der horizontalen Partitionierung verteilt wird. Es wurde jedoch eine anwendungsabhängige Verteilung verwendet.

Der Abfrageprozessor nutzt die Informationen in der DISTRIBUTION-Klausel, um die effizientesten Abfragepläne zu erstellen.

Mit der folgenden Anweisung können Sie eine externe Tabellen löschen:

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

Es sind die Berechtigungen **CREATE/DROP EXTERNAL TABLE**: ALTER ANY EXTERNAL DATA SOURCE erforderlich, die auch für den Verweis auf die zugrunde liegende Datenquelle benötigt werden.

**Sicherheitsüberlegungen:** Benutzer mit Zugriff auf die externe Tabelle erhalten automatisch Zugriff auf die zugrunde liegenden Remotetabellen gemäß den Anmeldeinformationen, die in der externen Datenquellendefinition angegeben sind. Sie müssen den Zugriff auf die externe Tabelle sorgfältig verwalten, um eine unerwünschte Erhöhung von Berechtigungen über die Anmeldeinformationen für die externe Datenquelle zu vermeiden. Herkömmliche SQL-Berechtigungen können zum Gewähren (GRANT) oder Widerrufen (REVOKE) des Zugriffs auf eine externe Tabelle wie bei einer normalen Tabelle verwendet werden.

**Beispiel**: Das folgende Beispiel veranschaulicht, wie Sie eine externe Tabelle erstellen:

	CREATE EXTERNAL TABLE [dbo].[order_line]( 
		 [ol_o_id] int NOT NULL, 
		 [ol_d_id] tinyint NOT NULL,
		 [ol_w_id] int NOT NULL, 
		 [ol_number] tinyint NOT NULL, 
		 [ol_i_id] int NOT NULL, 
		 [ol_delivery_d] datetime NOT NULL, 
		 [ol_amount] smallmoney NOT NULL, 
		 [ol_supply_w_id] int NOT NULL, 
		 [ol_quantity] smallint NOT NULL, 
		 [ol_dist_info] char(24) NOT NULL 
	) 
	
	WITH 
	( 
		DATA_SOURCE = MyExtSrc, 
	 	SCHEMA_NAME = 'orders', 
	 	OBJECT_NAME = 'order_details', 
		DISTRIBUTION=SHARDED(ol_w_id)
	); 

Das folgende Beispiel zeigt, wie Sie die Liste der externen Tabellen aus der aktuellen Datenbank abrufen:

	select * from sys.external_tables; 

## Abfragen 

### 2\.1 T-SQL-Abfragen mit voller Vertraulichkeit 

Nachdem Sie die externe Datenquelle und die externen Tabellen definiert haben, können Sie jetzt T-SQL vollständig auf die externen Tabellen anwenden.

**Beispiel für horizontale Partitionierung**: Die folgende Abfrage führt eine Dreiwegeverknüpfung zwischen Lagern, Aufträgen und Auftragspositionen aus und nutzt mehrere Aggregate und einen selektiven Filter. Es wird Folgendes vorausgesetzt: 1.) eine horizontale Partitionierung, 2.) dass für Lager, Aufträge und Auftragspositionen ein Sharding anhand der Spalte „warehouse id“ erfolgt ist, und 3.) dass die elastische Abfrage die Verknüpfungen für die Shards anordnen und den aufwendigen Teil der Abfrage in den Shards parallel verarbeiten kann.

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
 
### 2\.2 Gespeicherte Prozedur SP\_EXECUTE\_FANOUT 

Mit der elastischen Abfrage wurde auch eine gespeicherte Prozedur eingeführt, die einen Direktzugriff auf die Shards bietet. Die gespeicherte Prozedur heißt „sp\_execute\_fanout“ und verwendet die folgenden Parameter:

* Servername (nvarchar): Vollqualifizierter Name des logischen Servers, auf dem die Shardzuordnung gehostet wird. 
* Datenbankname der Shardzuordnung (nvarchar): Der Name der Shardzuordnungsdatenbank. 
* Benutzername (nvarchar): Der Benutzername für die Anmeldung bei der Shardzuordnungsdatenbank. 
* Kennwort (nvarchar): Das Kennwort des Benutzers. 
* Shardzuordnungsname (nvarchar): Der Name der Shardzuordnung für die Abfrage. 
*  Abfrage: Die T-SQL-Abfrage, die für die einzelnen Shards ausgeführt wird. 
*  Parameterdeklaration (nvarchar) – optional: Zeichenfolge mit Datentypdefinitionen für die Parameter, die im „Query“-Parameter verwendet werden (z. B. sp\_executesql). 
*  Parameterwertliste – optional: Durch Trennzeichen getrennte Liste von Parameterwerten (z. B. sp\_executesql).  

„sp\_execute\_fanout“ verwendet die in den Aufrufparametern angegebenen Shardzuordnungsinformationen, um die gegebene T-SQL-Anweisung für alle in der Shardzuordnung registrierten Shards auszuführen. Alle Ergebnisse werden mithilfe von UNION ALL-Semantik zusammengeführt. Das Ergebnis enthält auch die zusätzliche Spalte „virtual“ mit dem Shardnamen.

Beachten Sie, dass die gleichen Anmeldeinformationen für die Verbindung mit der Shard-Zuordnungsdatenbank und den Shards verwendet werden.

Beispiel:

	sp_execute_fanout 
		’myserver.database.windows.net', 
		N'ShardMapDb', 
		N'myuser', 
		N'MyPwd', 
		N'ShardMap', 
		N'select count(w_id) as foo from warehouse' 

## Konnektivität für Tools  

Verwenden Sie herkömmliche SQL Server-Verbindungszeichenfolgen, um Ihre Anwendung sowie Ihre BI- und Datenintegrationstools für die Datenbank mit Ihren Definitionen externer Tabellen zu verbinden. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Verweisen Sie dann auf die elastische Abfragedatenbank wie auf beliebige andere mit dem Tool verbundenen SQL Server-Datenbanken, und nutzen Sie externe Tabellen in Ihren Tools oder Anwendungen, als wären es lokale Tabellen.

## Bewährte Methoden 

* Stellen Sie sicher, dass die Endpunktdatenbank für elastische Abfragen durch die Firewalls von Azure SQL-Datenbank hindurch Zugriff auf die Datenbank mit der Shardzuordnung und alle Shards hat.  

* Eine elastische Abfrage überprüft die Verteilung der in der externen Tabelle definierten Daten nicht und erzwingt diese auch nicht. Wenn Ihre tatsächliche Datenverteilung sich von der Verteilung in der Tabellendefinition unterscheidet, können Ihre Abfragen zu unerwarteten Ergebnissen führen.

* Die elastische Abfrage führt derzeit keine Shardlöschung durch, wenn Prädikate für Shardingschlüssel ein gefahrloses Ausschließen der Verarbeitung bestimmter Shards zulassen würden.

* Eine elastische Abfrage funktioniert am besten für Abfragen, in denen der größte Teil der Berechnung in den Shards erfolgt. In der Regel erhalten Sie optimale Abfrageleistung mit benutzerdefinierten Filterprädikaten, die in den Shards oder Verknüpfungen über die Partitionierungsschlüssel ausgewertet werden können, die auf partitionsbezogene Weise auf allen Shards ausgeführt werden können. Für andere Abfragemuster müssen möglicherweise große Mengen von Daten aus den Shards auf den Hauptknoten geladen werden, wodurch Leistungseinbußen auftreten.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
<!--anchors-->

<!---HONumber=Oct15_HO4-->