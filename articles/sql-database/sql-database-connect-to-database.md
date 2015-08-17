<properties
	pageTitle="Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit SSMS" metaKeywords=""
	description="Erfahren Sie, wie Sie mit SSMS eine Verbindung mit einer Azure SQL-Datenbank herstellen."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" 
	manager="jhubbard"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# Herstellen einer Verbindung mit SQL Server Management Studio

Nachfolgend werden die Schritte beschrieben, die zum Installieren von SQL Server Management Studio (SSMS) und zum Herstellen einer Verbindung zur Abfrage der SQL-Datenbank mit SSMS erforderlich sind.

## Voraussetzungen
* Die AdventureWorks-Beispieldatenbank als SQL-Datenbank, wie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md) beschrieben.

## Installieren und Starten von SQL Server Management Studio (SSMS)
1. Wechseln Sie zur Downloadseite für [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299), klicken Sie auf **Download**, und wählen Sie entweder die 32-Bit-Version (X 86) oder 64-Bit-Version (X 64) von MgmtStudio für den Download aus.

	![MgtmtStudio32BIT oder MgmtStudio64BIT][1]
2.	Befolgen Sie beim Installieren von SSMS mit den Standardeinstellungen die Eingabeaufforderungen.
3.	Suchen Sie nach dem Download auf Ihrem PC nach SQL Server 2014 Management Studio, und starten Sie SSMS.


## Herstellen von Verbindungen mit der SQL-Datenbank
1. Öffnen Sie SSMS.
2. Geben Sie im Fenster **Verbindung mit Server herstellen** im Feld **Servername** den Namen des Servers im Format *&lt;servername>*.**database.windows.net** ein.
3. Wählen Sie im Dropdownfeld **Authentifizierung** den Eintrag **SQL Server-Authentifizierung** aus.
4. Geben Sie den **Benutzernamen** und das **Kennwort** ein, die Sie beim Erstellen des SQL-Datenbankservers angegeben haben.

	![Dialogfeld "Verbindung mit Server herstellen"][2]
5. Klicken Sie auf die Schaltfläche **Optionen**.
6. Geben Sie **AdventureWorks** im Feld **Mit Datenbank verbinden** ein, und klicken Sie auf **Verbinden**.

	![Mit Datenbank verbinden][3]

### Bei Verbindungsfehlern
Stellen Sie sicher, dass die Firewall des von Ihnen erstellten logischen Servers Verbindungen vom lokalen Computer zulässt. Weitere Informationen finden Sie unter [Konfigurieren der Firewalleinstellungen (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Durchführen von Beispielabfragen

1. Navigieren Sie im **Objekt-Explorer** zur Datenbank **AdventureWorks**.
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage** aus.

	![Neue Abfrage][4]
3. Kopieren Sie den folgenden Code, und fügen Sie ihn im neu geöffneten Abfragefenster ein:

		SELECT 
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Klicken Sie dann auf die Schaltfläche **Ausführen**. Bei einer erfolgreichen Durchführung sieht das Ergebnis wie folgt aus: ![Erfolgreich][5]


## Nächste Schritte
Sie können Transact-SQL-Anweisungen zum Erstellen oder Verwalten von Datenbanken verwenden. Weitere Informationen finden Sie unter [CREATE DATABASE (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx) und [Verwalten von Azure SQL-Datenbank mit SQL Server Management Studio](sql-database-manage-azure-ssms.md). Sie können Ereignisse auch in Azure Storage protokollieren. Weitere Informationen finden Sie unter [Erste Schritte mit SQL Database-Auditing](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
 

<!---HONumber=August15_HO6-->