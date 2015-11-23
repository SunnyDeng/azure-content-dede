<properties
    pageTitle="Abfrage für elastische Datenbanken bei datenbankübergreifenden Abfragen (vertikale Partitionierung) | Microsoft Azure"
    description="Einrichten von datenbankübergreifenden Abfragen über vertikale Partitionen"    
    services="sql-database"
    documentationCenter=""  
    manager="jeffreyg"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="torsteng;sidneyh" />

# Abfrage für elastische Datenbanken bei datenbankübergreifenden Abfragen (vertikale Partitionierung)

In diesem Dokument werden die Einrichtung der elastischen Abfrage für Szenarien mit datenbankübergreifenden Abfragen (vertikale Partitionierung) und die Ausführung von Abfragen erläutert. Eine Definition des Szenarios mit vertikaler Partitionierung finden Sie in der [Übersicht über die Abfrage für elastische Datenbanken in Azure SQL-Datenbank (Vorschau)](sql-database-elastic-query-overview.md).

## Erstellen von Datenbankobjekten

In Szenarien mit vertikaler Partitionierung erweitert die elastische Abfrage die aktuelle T-SQL-DDL, um auf Tabellen zu verweisen, die in Remotedatenbanken gespeichert sind. Dieser Abschnitt enthält eine Übersicht über die DDL-Anweisungen zum Konfigurieren der elastischen Abfrage für transparenten Zugriff auf Remotetabellen. Diese DDL-Anweisungen ermöglichen die Metadatendarstellung der Remotetabellen in der lokalen Datenbank.

**HINWEIS**: Im Gegensatz zur horizontalen Partitionierung hängen diese DDL-Anweisungen nicht von der Festlegung einer Datenebene mit einer Shard-Zuordnung über die Clientbibliothek für elastische Datenbanken ab.

Das Definieren der Datenbankobjekte für die Abfrage einer elastische Datenbank beruht auf den folgenden T-SQL-Anweisungen, die für das Szenario mit vertikaler Partitionierung weiter unten erläutert werden:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) 

* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx)

* [CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx)

* [CREATE/DROP EXTERNAL TABLE](https://msdn.microsoft.com/library/dn935021.aspx)

### 1\.1 Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen 

Benutzer-ID und Kennwort bilden die Anmeldeinformationen, die von der elastischen Abfrage zur Verbindung mit Ihren Remotedatenbanken in Azure SQL-Datenbank verwendet werden. Den erforderlichen Hauptschlüssel und die Anmeldeinformationen erstellen Sie mit der folgenden Syntax:

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = ’password’;
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = ‘<username>’,  
    SECRET = ‘<password>’
    [;]
    
So löschen Sie die Anmeldeinformationen:
    
    DROP DATABASE SCOPED CREDENTIAL <credential_name>;  
    DROP MASTER KEY;   

 
Stellen Sie sicher, dass *< username>* kein *@servername*-Suffix enthält.

### 1\.2 Externe Datenquellen

Sie geben die Informationen zu Ihren Remotedatenbanken für die Abfrage für elastische Datenbanken durch Festlegen externer Datenbanken an. Die Syntax zum Erstellen und Löschen von externen Datenquellen wird wie folgt definiert:

    <External_Data_Source> ::=
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH 
               (TYPE = RDBMS,
                LOCATION = ’<fully_qualified_server_name>’,
                DATABASE_NAME = ‘<remote_database_name>’,  
                CREDENTIAL = <credential_name> 
                ) [;] 

Beachten Sie den TYPE-Parameter, der diese Datenquelle als RDBMS definiert.

Mit der folgenden Anweisung können Sie eine externe Datenquelle übergeben:

    DROP EXTERNAL DATA SOURCE <data_source_name>[;]

#### Berechtigungen für CREATE/DROP EXTERNAL DATA SOURCE 

Der Benutzer muss über die Berechtigung ALTER ANY EXTERNAL DATA SOURCE verfügen. Diese Berechtigung ist in der ALTER DATABASE-Berechtigung enthalten.

**Beispiel**

Das folgende Beispiel veranschaulicht die Verwendung der CREATE-Anweisung für externe Datenquellen.

	CREATE EXTERNAL DATA SOURCE RemoteReferenceData 
	WITH 
	( 
		TYPE=RDBMS, 
		LOCATION='myserver.database.windows.net', 
		DATABASE_NAME='ReferenceData', 
		CREDENTIAL= SqlUser 
	); 
 
So rufen Sie die Liste der aktuellen externen Datenquellen aus der folgenden Katalogsicht ab:

    select * from sys.external_data_sources; 

### 1\.3 Externe Tabellen 

Die Abfrage für elastische Datenbanken erweitert die vorhandene externe Tabellensyntax zum Definieren von externen Tabellen, die externe Datenquellen vom Typ RDBMS verwenden. Eine externe Tabellendefinition für die vertikale Partitionierung behandelt die folgenden Aspekte:

* **Schema**: Die DDL für externe Tabellen definiert ein Schema, das Ihre Abfragen verwenden kann. Das in Ihrer Definition für externe Tabellen angegebene Schema muss mit dem Schema der Tabellen in der Remotedatenbank übereinstimmen, in der die eigentlichen Daten gespeichert werden. 

* **Remotedatenbankverweis**: Die DDL für externe Tabellen verweist auf eine externe Datenquelle. Die externe Datenquelle gibt den logischen Servernamen und Datenbanknamen der Remotedatenbank an, in der die eigentlichen Tabellendaten gespeichert sind.

Wenn Sie eine externe Datenquelle gemäß der Beschreibung im vorherigen Abschnitt verwenden, lautet die Syntax zum Erstellen externer Tabellen wie folgt:

	CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])     
	{ WITH ( <rdbms_external_table_options> ) } 
	)[;] 
	
	<rdbms_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>, 
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 

Die DATA\_SOURCE-Klausel definiert die externe Datenquelle (d. h. die Remotedatenbank bei vertikaler Partitionierung), die für die externe Tabelle verwendet wird.

Die Klauseln SCHEMA\_NAME und OBJECT\_NAME bieten die Möglichkeit, die Definition der externen Tabelle einer Tabelle in einem anderen Schema in der Remotedatenkbank bzw. einer Tabelle mit einem anderen Namen zuzuordnen. Das ist nützlich, wenn Sie eine externe Tabelle für eine Katalogsicht oder DMV in Ihrer Remotedatenbank definieren möchten oder wenn der Name der Remotetabelle bereits lokal verwendet wird.

Die folgende DDL-Anweisung löscht eine vorhandene Definition für eine externe Tabelle aus dem lokalen Katalog. Das wirkt sich nicht auf die Remotedatenbank aus.

	DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]  

**Berechtigungen für CREATE/DROP EXTERNAL TABLE**: Berechtigungen vom Typ ALTER ANY EXTERNAL DATA SOURCE sind für die DDL für externe Tabellen erforderlich, die auch für den Verweis auf die zugrunde liegende Datenquelle benötigt wird.

**Sicherheitsüberlegungen:** Benutzer mit Zugriff auf die externe Tabelle erhalten automatisch Zugriff auf die zugrunde liegenden Remotetabellen gemäß den Anmeldeinformationen, die in der externen Datenquellendefinition angegeben sind. Sie müssen den Zugriff auf die externe Tabelle sorgfältig verwalten, um eine unerwünschte Erhöhung von Berechtigungen über die Anmeldeinformationen für die externe Datenquelle zu vermeiden. Herkömmliche SQL-Berechtigungen können zum Gewähren (GRANT) oder Widerrufen (REVOKE) des Zugriffs auf eine externe Tabelle wie bei einer normalen Tabelle verwendet werden.


 **Beispiel**: Das folgende Beispiel veranschaulicht, wie Sie eine externe Tabelle erstellen:

	CREATE EXTERNAL TABLE [dbo].[customer]( 
		[c_id] int NOT NULL, 
		[c_firstname] nvarchar(256) NULL, 
		[c_lastname] nvarchar(256) NOT NULL, 
		[street] nvarchar(256) NOT NULL, 
		[city] nvarchar(256) NOT NULL, 
		[state] nvarchar(20) NULL, 
		[country] nvarchar(50) NOT NULL, 
	) 
	WITH 
	( 
	       DATA_SOURCE = RemoteReferenceData 
	); 

Das folgende Beispiel zeigt, wie Sie die Liste der externen Tabellen aus der aktuellen Datenbank abrufen:

	select * from sys.external_tables; 

## Abfragen

### 2\.1 T-SQL-Abfragen mit voller Vertraulichkeit 

Nachdem Sie die externe Datenquelle und die externen Tabellen definiert haben, können Sie jetzt vollständiges T-SQL in den externen Tabellen verwenden.

**Beispiel für die vertikale Partitionierung**: Die folgende Abfrage führt eine Dreiwegeverknüpfung zwischen den zwei lokalen Tabellen für Aufträge und Auftragspositionen und der Remotetabelle für Kunden aus. Hier sehen Sie ein Beispiel für den Verweisdatenanwendungsfall für die Abfrage elastischer Datenbanken:

	SELECT  	
	 c_id as customer,
	 c_lastname as customer_name,
	 count(*) as cnt_orderline, 
	 max(ol_quantity) as max_quantity,
	 avg(ol_amount) as avg_amount,
	 min(ol_delivery_d) as min_deliv_date
	FROM customer 
	JOIN orders 
	ON c_id = o_c_id
	JOIN  order_line 
	ON o_id = ol_o_id and o_c_id = ol_c_id
	WHERE c_id = 100

  
## Konnektivität für Tools

Sie können herkömmliche SQL Server-Verbindungszeichenfolgen verwenden, um Ihre BI- und Datenintegrationstools mit Datenbanken auf dem SQL-Datenbankserver zu verbinden, für die die Abfrage für elastische Datenbanken aktiviert ist und für die externe Tabellen definiert sind. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Verweisen Sie dann auf die elastische Abfragedatenbank und die externen Tabellen wie auf jede andere SQL Server-Datenbank, zu der Sie mit dem Tool eine Verbindung herstellen würden.

## Bewährte Methoden 
 
* Stellen Sie sicher, dass die Endpunktdatenbank für elastische Abfragen Zugriff auf die Remotedatenbank hat, indem Sie den Zugriff für Azure-Dienste in der Firewallkonfiguration für SQL-Datenbanken aktivieren. Stellen Sie darüber hinaus sicher, dass mit den in der externen Datenquellendefinition angegebenen Anmeldeinformationen die Anmeldung bei der Remotedatenbank möglich ist und für die Anmeldeinformationen Berechtigungen zum Zugriff auf die Remotetabelle festgelegt wurden.  

* Eine Abfrage für elastische Datenbanken funktioniert am besten für Abfragen, in denen der größte Teil der Berechnung in den Remotedatenbanken erfolgt. In der Regel erhalten Sie optimale Abfrageleistung mit benutzerdefinierten Filterprädikaten, die in den Remotedatenbanken oder Verknüpfungen ausgewertet werden können, die vollständig in der Remotedatenbank ausgeführt werden können. Für andere Abfragemuster müssen möglicherweise große Mengen von Daten aus der Remotedatenbank geladen werden, wodurch Leistungseinbußen auftreten.


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Image references-->
<!--anchors-->

<!---HONumber=Nov15_HO3-->