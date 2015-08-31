<properties
	pageTitle="Erste Schritte mit Abfragen für elastische Datenbanken"
	description="Verwenden von Abfragen für elastische Datenbanken"
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
	ms.date="06/23/2015"
	ms.author="sidneyh" />

# Erste Schritte mit Abfragen für elastische Datenbanken

Mit Abfragen für elastische Datenbanken (Vorschau) für Azure SQL-Datenbank können Sie T-SQL-Abfragen ausführen, die sich mit einem einzigen Verbindungspunkt über mehrere Datenbanken erstrecken. Weitere Informationen zu Abfragen für elastische Datenbanken finden Sie auf der [Übersichtsseite zum Feature](sql-database-elastic-query-overview.md).

Dieses Thema baut auf dem Beispiel unter [Erste Schritte mit den Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md) auf. Nach Abschluss haben Sie Folgendes gelernt: Konfigurieren und Verwenden einer Azure SQL-Datenbank zum Ausführen von Abfragen, die sich über viele verbundene Datenbanken erstrecken.
## Voraussetzungen

Laden Sie das Beispiel [Erste Schritte mit den Tools für die elastische Datenbank](sql-database-elastic-scale-get-started.md) herunter, und führen Sie es aus.

## Erstellen eines Shardzuordnungs-Managers mithilfe der Beispiel-App

Hier erstellen Sie einen Shardzuordnungs-Manager und mehrere Shards und fügen anschließend Daten in die Shards ein. Wenn Sie bereits über Shards verfügen, die Shardingdaten enthalten, können Sie die folgenden Schritte überspringen und zum nächsten Abschnitt wechseln.

1. Erstellen Sie die Beispielanwendung aus **Erste Schritte mit den Tools für die elastische Datenbank**, und führen Sie sie aus. Führen Sie die Schritte bis Schritt 7 im Abschnitt [Herunterladen und Ausführen der Beispiel-App](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) aus. Am Ende von Schritt 7 wird die folgende Eingabeaufforderung angezeigt:

	![Eingabeaufforderung][1]

2.  Geben Sie im Befehlsfenster "1" ein, und drücken Sie die **EINGABETASTE**. Dadurch wird der Shardzuordnungs-Manager erstellt, und es werden zwei Shards zum Server hinzugefügt. Geben Sie dann "3" ein, und drücken Sie die **EINGABETASTE**. Wiederholen Sie den Vorgang viermal. Dadurch werden Beispieldatenzeilen in die Shards eingefügt.
3.  Im [Azure-Vorschauportal](https://portal.azure.com) sollten drei neue Datenbanken auf dem V12-Server angezeigt werden:

	![Visual Studio-Bestätigung][2]

	Zum diesem Zeitpunkt werden datenbankübergreifende Abfragen durch die Clientbibliotheken für elastische Datenbanken unterstützt. Verwenden Sie z. B. Option 4 im Eingabeaufforderungsfenster. Die Ergebnisse einer Abfrage mit mehreren Shards sind immer ein **UNION ALL** der Ergebnisse aus allen Shards.

	Im nächsten Abschnitt erstellen wir einen Endpunkt für eine Beispieldatenbank, der umfangreichere Abfragen der Daten über Shards hinweg unterstützt.

## Erstellen einer elastischen Datenbankabfrage

1. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com), und melden Sie sich an.
2. Erstellen Sie eine neue Azure SQL-Datenbank auf demselben Server, auf dem sich auch das Shard-Setup befindet. Nennen Sie die Datenbank "ElasticDBQuery". Als Tarif müssen Sie eines der Premium-Angebote auswählen. Abfragen für elastische Datenbanken sind derzeit nur im Premium-Tarif verfügbar.

	![Azure-Portal und -Tarif][3]

	Hinweis: Sie können eine vorhandene Premium-Datenbank verwenden. Wenn Sie diese Konfiguration wählen, darf es sich bei dem Shard nicht um einen der Shards handeln, für die Sie die Abfragen ausführen möchten. Diese Datenbank wird zum Erstellen der Metadatenobjekte für eine Abfrage für elastische Datenbanken verwendet.


## Erstellen von Datenbankobjekten

### Erstellen des Datenbankhauptschlüssels und der Anmeldeinformationen

Diese Informationen werden für die Verbindung mit dem Shard-Zuordnungs-Manager und den Shards verwendet:

1. Öffnen Sie SQL Server Management Studio oder SQL Server Data Tools in Visual Studio.
2. Stellen Sie eine Verbindung mit der ElasticDBQuery-Datenbank her, und führen Sie die folgenden T-SQL-Befehle aus:

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>';

		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
		WITH IDENTITY = '<username>',
		SECRET = '<password>';

	"username" und "password" sollten mit den Anmeldeinformationen aus Schritt 6 unter [Herunterladen und Ausführen der Beispiel-App](sql-database-elastic-scale-get-started.md#Getting-started-with-elastic-database-tools) in [Erste Schritte mit den Tools für elastische Datenbanken](sql-database-elastic-scale-get-started.md) übereinstimmen.

### Externe Datenquellen

Um eine externe Datenquelle zu erstellen, führen Sie den folgenden Befehl für die ElasticDBQuery-Datenbank aus:

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
      (TYPE = SHARD_MAP_MANAGER,
      LOCATION = '<server_name>.database.windows.net',
      DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
	  CREDENTIAL = ElasticDBQueryCred,
 	  SHARD_MAP_NAME = 'CustomerIDShardMap'
    ) ;

 "CustomerIDShardMap" ist der Name der Shard-Zuordnung, wenn Sie die Shard-Zuordnung und den Shard-Zuordnungs-Manager mithilfe des Beispiels für die Tools für elastische Datenbanken erstellt haben. Wenn Sie jedoch Ihre benutzerdefinierte Konfiguration für dieses Beispiel verwendet haben, sollte dies der Name Shard-Zuordnung sein, den Sie in der Anwendung ausgewählt haben.

### Externe Tabellen

Erstellen Sie eine externe Tabelle, die mit der Customers-Tabelle in den Shards übereinstimmt, indem Sie den folgenden Befehl für die ElasticDBQuery-Datenbank ausführen:

	CREATE EXTERNAL TABLE [dbo].[Customers]
	( [CustomerId] [int] NOT NULL,
	  [Name] [nvarchar](256) NOT NULL,
	  [RegionId] [int] NOT NULL)
	WITH
	( DATA_SOURCE = MyElasticDBQueryDataSrc,
      DISTRIBUTION = SHARDED([CustomerId])
	) ;

## Ausführen einer T-SQL-Abfrage für eine elastische Beispieldatenbank

Nachdem Sie die externe Datenquelle und die externen Tabellen definiert haben, können Sie jetzt vollständiges T-SQL in den externen Tabellen verwenden.

Führen Sie die folgende Abfrage für die ElasticDBQuery-Datenbank aus:

	select count(CustomerId) from [dbo].[Customers]

Sie werden feststellen, dass die Abfrage Ergebnisse aus allen Shards aggregiert und die folgende Ausgabe ausgibt:

![Ausgabedetails][4]

## Importieren der Ergebnisse der Abfrage für die elastische Datenbank in Excel

 Sie können die Ergebnisse einer Abfrage in eine Excel-Datei importieren.

1. Öffnen Sie Excel 2013.
2. 	Navigieren Sie zum Menüband **Daten**.
3. 	Klicken Sie auf **Aus anderen Quellen** und dann auf **Von SQL Server**.

	![Excel-Import aus anderen Quellen][5]
4. 	Geben Sie im **Datenverbindungs-Assistenten** den Servernamen und die Anmeldeinformationen ein. Klicken Sie auf **Next**.
5. 	Wählen Sie im Dialogfeld **Wählen Sie die Datenbank, die die benötigten Daten enthält** die Datenbank **ElasticDBQuery** aus.
6. 	Wählen Sie die Tabelle **Customers** in der Listenansicht aus, und klicken Sie auf **Weiter**. Klicken Sie auf **Fertig stellen**.
7. 	Wählen Sie im Formular **Daten importieren** unter **Wählen Sie das Format aus, in dem Sie diese Daten in der Arbeitsmappe anzeigen möchten** die Option **Tabelle** aus, und klicken Sie auf **OK**.

Die Excel-Tabelle wird mit allen Zeilen aus der Tabelle **Customers** gefüllt, die in verschiedenen Shards gespeichert sind.

## Nächste Schritte
Sie können jetzt die leistungsstarken Datenfunktionen von Excel verwenden. Sie können die Verbindungszeichenfolge mit dem Servernamen, Datenbanknamen und den Anmeldeinformationen zur Verbindung der BI und der Datenintegrationstools mit der elastischen Abfragedatenbank verwenden. Stellen Sie sicher, dass SQL Server als Datenquelle für das Tool unterstützt wird. Sie können auf die elastische Abfragedatenbank und die externen Tabellen wie auf jede andere SQL Server-Datenbank und SQL Server-Tabelle verweisen, zu denen Sie mit dem Tool eine Verbindung herstellen würden.

### Kosten
Es gibt keine zusätzlichen Gebühren für die Verwendung der Abfragefunktion für elastische Datenbanken. Zum jetzigen Zeitpunkt steht dieses Feature nur für Premium-Datenbanken als Endpunkt zur Verfügung, die Shards können jedoch aus jeder Dienstebene stammen.

Preisinformationen finden Sie in der [SQL-Datenbank – Preisdetails](http://azure.microsoft.com/pricing/details/sql-database/).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->

<!---HONumber=August15_HO8-->