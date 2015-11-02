<properties
	pageTitle="Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung) | Microsoft Azure"	
	description="Sie erfahren, wie Sie Abfragen für elastische Datenbanken für vertikal partitionierte Datenbanken verwenden."
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
	ms.date="10/19/2015"
	ms.author="torsteng" />

# Erste Schritte mit datenbankübergreifenden Abfragen (vertikale Partitionierung) 

Mit Abfragen für elastische Datenbanken (Vorschau) für Azure SQL-Datenbank können Sie T-SQL-Abfragen ausführen, die sich mit einem einzigen Verbindungspunkt über mehrere Datenbanken erstrecken. Dieses Thema gilt für [vertikal partitionierte Datenbanken](sql-database-elastic-query-vertical-partitioning.md).

Nach Abschluss des Themas haben Sie Folgendes gelernt: Konfigurieren und Verwenden einer Azure SQL-Datenbank zum Ausführen von Abfragen, die sich über mehrere verwandte Datenbanken erstrecken.

Weitere Informationen zur Abfragefunktion für elastische Datenbanken finden Sie unter [Übersicht über die Abfrage für elastische Datenbanken in Azure SQL-Datenbank](sql-database-elastic-query-overview.md).

## Erstellen der Beispieldatenbanken

Zuerst müssen die beiden Datenbanken **Customers** (Kunden) und **Orders** (Bestellungen) erstellt werden, und zwar entweder auf demselben oder auf unterschiedlichen logischen Servern.

Führen Sie die folgenden Abfragen in der Datenbank **Orders** aus, um die Tabelle **OrderInformation** zu erstellen und die Beispieldaten einzugeben.

	CREATE TABLE [dbo].[OrderInformation]( 
		[OrderID] [int] NOT NULL, 
		[CustomerID] [int] NOT NULL 
		) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Führen Sie die folgende Abfrage in der Datenbank „Customers“ aus, um die Tabelle „CustomerInformation“ zu erstellen und die Beispieldaten einzugeben.

	CREATE TABLE [dbo].[CustomerInformation]( 
		[CustomerID] [int] NOT NULL, 
		[CustomerName] [varchar](50) NULL, 
		[Company] [varchar](50) NULL 
		CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
	) 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## Erstellen von Datenbankobjekten
### Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen


Diese Informationen werden für die Verbindung mit dem Shard-Zuordnungs-Manager und den Shards verwendet:

1. Öffnen Sie SQL Server Management Studio oder SQL Server Data Tools in Visual Studio.
2. Stellen Sie eine Verbindung mit der Datenbank „Orders“ her, und führen Sie die folgenden T-SQL-Befehle aus:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
		WITH IDENTITY = '<username>', 
		SECRET = '<password>';  

	Als „username“ und „password“ sollten der Benutzername und das Kennwort verwendet werden, die zum Anmelden an der Datenbank „Customers“ genutzt werden.

### Externe Datenquellen
Um eine externe Datenquelle zu erstellen, führen Sie den folgenden Befehl für die Datenbank „Orders“ aus:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
		(TYPE = RDBMS, 
		LOCATION = '<server_name>.database.windows.net', 
		DATABASE_NAME = 'Customers', 
		CREDENTIAL = ElasticDBQueryCred, 
	) ;

### Externe Tabellen
Erstellen Sie eine externe Tabelle in der Datenbank „Orders“, die der Definition der Tabelle „CustomerInformation“ entspricht:

	CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
	( [CustomerID] [int] NOT NULL, 
	  [CustomerName] [varchar](50) NOT NULL, 
	  [Company] [varchar](50) NOT NULL) 
	WITH 
	( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## Ausführen einer T-SQL-Abfrage für eine elastische Beispieldatenbank

Nachdem Sie die externe Datenquelle und die externen Tabellen definiert haben, können Sie jetzt T-SQL verwenden, um externe Tabellen abzufragen. Führen Sie für die Datenbank „Orders“ diese Abfrage aus:

	SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
	FROM OrderInformation 
	INNER JOIN CustomerInformation 
	ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## Kosten

Derzeit ist die Abfragefunktion für elastische Datenbanken in den Kosten für Ihre Azure SQL-Datenbank enthalten.

Preisinformationen finden Sie unter [Preise für SQL-Datenbank](/pricing/details/sql-database).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->

<!---HONumber=Oct15_HO4-->