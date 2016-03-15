<properties
   pageTitle="Herstellen einer Verbindung mit SQL Data Warehouse über Visual Studio | Microsoft Azure"
   description="Erste Schritte zum Herstellen von Verbindungen mit SQL Data Warehouse für das Ausführen von Abfragen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;barbkess;sonyama"/>

# Herstellen einer Verbindung mit SQL Data Warehouse über Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-sqlcmd.md)

In dieser exemplarischen Vorgehensweise wird das Verbinden mit einer Azure SQL Data Warehouse-Datenbank veranschaulicht, was mit den SQL Server Data Tools in Visual Studio nur wenige Minuten dauert. Nachdem die Verbindung hergestellt wurde, führen Sie eine einfache Abfrage aus.

## Voraussetzungen

+ AdventureWorksDW-Beispieldatenbank in SQL Data Warehouse. Informationen zum Erstellen dieser Datenbank finden Sie unter [Erstellen einer SQL Data Warehouse-Datenbank](sql-data-warehouse-get-started-provision.md).
+ SQL Server Data Tools für Visual Studio. Installationshinweise und Optionen finden Sie unter [Installieren von Visual Studio und/oder SSDT](sql-data-warehouse-install-visual-studio.md).

## Schritt 1: Ermitteln des vollqualifizierten Namens des SQL Azure-Servers

Ihre Datenbank ist einem Azure SQL-Server zugeordnet. Um eine Verbindung mit Ihrer Datenbank herzustellen, benötigen Sie den vollqualifizierten Namen des Servers (**Servername**.database.windows.net*).

So ermitteln Sie den vollqualifizierten Servernamen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Klicken Sie auf **SQL-Datenbanken**, und klicken Sie auf die Datenbank, mit der Sie eine Verbindung herstellen möchten. In diesem Beispiel wird die AdventureWorksDW-Beispieldatenbank verwendet.
3. Suchen Sie den vollständigen Servernamen.

    ![Vollständiger Servername][1]

## Schritt 2: Herstellen einer Verbindung mit der SQL-Datenbank

1. Öffnen Sie Visual Studio.
2. Öffnen Sie den SQL Server-Objekt-Explorer. Wählen Sie zu diesem Zweck **Ansicht** > **SQL Server-Objekt-Explorer** aus.

    ![SQL Server-Objekt-Explorer][2]

3. Klicken Sie auf das Symbol **SQL Server hinzufügen**.

    ![SQL Server-Instanz hinzufügen][3]

1. Füllen Sie die Felder im Fenster zum Herstellen einer Verbindung mit dem Server aus.

    ![Verbindung mit dem Server herstellen][4]

    - **Servername**. Geben Sie den zuvor ermittelten *Servernamen* ein.
    - **Authentifizierung**. Wählen Sie SQL Server-Authentifizierung.
    - **Benutzername** und **Kennwort**. Geben Sie den Benutzernamen und das Kennwort für den Azure SQL-Server ein.
    - Klicken Sie auf **Verbinden**.

1. Erweitern Sie den Azure SQL-Server. Sie können die dem Server zugeordneten Datenbanken anzeigen. Erweitern Sie „AdventureWorksDW“, um die Tabellen in Ihrer Beispieldatenbank anzuzeigen.

    ![AdventureWorksDW erkunden][5]


## Schritt 3: Ausführen einer Beispielabfrage

Nachdem jetzt eine Verbindung mit dem Server hergestellt wurde, fahren wir mit dem Schreiben einer Abfrage fort.

1. Klicken Sie mit der rechten Maustaste im SQL Server-Objekt-Explorer auf Ihre Datenbank.

2. Wählen Sie **Neue Abfrage** aus. Ein neues Abfragefenster wird geöffnet.

    ![Neue Abfrage][6]

3. Kopieren Sie die folgende TSQL-Abfrage in das Abfragefenster:

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Führen Sie die Abfrage aus. Zu diesem Zweck klicken Sie auf den grünen Pfeil oder verwenden die folgende Tastenkombination: `CTRL`+`SHIFT`+`E`.

    ![Abfrage ausführen][7]

1. Sehen Sie sich die Abfrageergebnisse an. In diesem Beispiel weist die Tabelle „FactInternetSales“ 60398 Zeilen auf.

    ![Abfrageergebnisse][8]

## Nächste Schritte

Nachdem Sie eine Verbindung hergestellt haben und Abfragen senden können, versuchen Sie, [die Daten mit Power BI zu visualisieren][].

[die Daten mit Power BI zu visualisieren]: ./sql-data-warehouse-get-started-visualize-with-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect/add-server.png
[4]: ./media/sql-data-warehouse-get-started-connect/connection-dialog.png
[5]: ./media/sql-data-warehouse-get-started-connect/explore-sample.png
[6]: ./media/sql-data-warehouse-get-started-connect/new-query2.png
[7]: ./media/sql-data-warehouse-get-started-connect/run-query.png
[8]: ./media/sql-data-warehouse-get-started-connect/query-results.png

<!---HONumber=AcomDC_0309_2016-->