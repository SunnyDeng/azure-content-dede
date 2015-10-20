<properties
   pageTitle="Erste Schritte: Verbinden mit Azure SQL Data Warehouse | Microsoft Azure"
   description="Erste Schritte zum Herstellen von Verbindungen mit SQL Data Warehouse für das Ausführen von Abfragen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager=""
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/07/2015"
   ms.author="twounder"/>

# Verbinden und Abfragen mit Visual Studio

> [AZURE.SELECTOR]
- [Visual Studio](sql-data-warehouse-get-started-connect-query.md)
- [SQLCMD](sql-data-warehouse-get-started-connect-query-sqlcmd.md)

In dieser exemplarischen Vorgehensweise wird das Verbinden mit und Abfragen einer Azure SQL Data Warehouse-Datenbank veranschaulicht, was in Visual Studio nur wenige Minuten dauert. In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:

+ Installieren der erforderlichen Software
+ Verbinden mit einer Datenbank, die die Beispieldatenbank "AdventureWorksDW" enthält
+ Anwenden einer Abfrage auf die Beispieldatenbank  

## Voraussetzungen

+ Visual Studio 2013/2015: Informationen zum Herunterladen und Installieren von Visual Studio 2015 und/oder SSDT finden Sie unter [Installieren von Visual Studio und SSDT](sql-data-warehouse-install-visual-studio.md).

## Abrufen des vollqualifizierten Namens des SQL Azure-Servers

Um eine Verbindung mit Ihrer Datenbank herzustellen, benötigen Sie den vollständigen Namen des Servers (****Servername**.database.windows.net*), auf dem sich die Datenbank befindet.

1. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com).
2. Navigieren Sie zu der Datenbank, mit der Sie eine Verbindung herstellen möchten.
3. Suchen Sie den vollständigen Servernamen (der in den folgenden Schritten verwendet wird):

![][1]

## Herstellen von Verbindungen mit der SQL-Datenbank

1. Öffnen Sie Visual Studio.
2. Öffnen Sie im Menü "Ansicht" den **SQL Server-Objekt-Explorer**.
 
![][2]

3. Klicken Sie auf die Schaltfläche **SQL Server hinzufügen**

![][3]

4. Geben Sie den zuvor erfassten *Servernamen* ein.
5. Wählen Sie in der Liste **Authentifizierung** den Eintrag **SQL Server-Authentifizierung** aus.
6. Geben Sie den **Benutzernamen** und das **Kennwort** ein, den/das Sie beim Erstellen des SQL-Datenbankservers angegeben haben, und klicken Sie auf **Verbinden**.

## Durchführen von Beispielabfragen

Da wir jetzt unseren Server registriert haben, fahren wir mit dem Schreiben einer Abfrage fort.

1. Klicken Sie auf die Benutzerdatenbank in SSDT.

2. Klicken Sie auf die Schaltfläche **Neue Abfrage**. Ein neues Fenster wird geöffnet.

![][4]

3. Geben Sie den folgenden Code in das Abfragefenster ein.

	```
	SELECT COUNT(*) FROM dbo.FactInternetSales;
	```

4. Führen Sie die Abfrage aus.

	Zum Ausführen der Abfrage klicken Sie auf den grünen Pfeil oder verwenden die folgende Tastenkombination: `CTRL`+`SHIFT`+`E`.

## Nächste Schritte

Nachdem Sie eine Verbindung hergestellt haben und Abfragen senden können, versuchen Sie, [eine Verbindung mit Power BI herzustellen][].

[eine Verbindung mit Power BI herzustellen]: ./sql-data-warehouse-integrate-power-bi.md


<!--Image references-->

[1]: ./media/sql-data-warehouse-get-started-connect-query/get-server-name.png
[2]: ./media/sql-data-warehouse-get-started-connect-query/open-ssdt.png
[3]: ./media/sql-data-warehouse-get-started-connect-query/connection-dialog.png
[4]: ./media/sql-data-warehouse-get-started-connect-query/new-query.png

<!---HONumber=Oct15_HO3-->