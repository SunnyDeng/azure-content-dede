<properties
	pageTitle="Herstellen einer Verbindung mit SQL-Datenbank über SSMS | Microsoft Azure"
	description="Erfahren Sie, wie Sie über SQL Server Management Studio eine Verbindung mit einer Azure SQL-Datenbank herstellen. Führen Sie dann eine Beispielabfrage mithilfe von Transact-SQL (T-SQL) aus."
	metaCanonical=""
	keywords="Verbinden mit SQL-Datenbank, SQL Server Management Studio"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="sstein" />

# Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit SQL Server Management Studio und Ausführen einer T-SQL-Beispielabfrage

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

In diesem Artikel wird das Herstellen einer Verbindung mit einer Azure SQL-Datenbank und anschließende Durchführen einer einfachen Abfrage mithilfe von Transact-SQL-Anweisungen veranschaulicht.

Sie benötigen zuerst eine SQL-­Datenbank in Azure. Mithilfe der Anweisungen unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md) können Sie schnell eine erstellen. Die hier aufgeführten Beispiele basieren auf der AdventureWorks-Beispieldatenbank, die Sie in diesem Artikel erstellen. Bis zur Durchführung der Abfrage gelten jedoch für jede SQL-Datenbank die gleichen Schritte.

## Installieren und Starten von SQL Server Management Studio (SSMS)

Bei der Arbeit mit SQL-Datenbank sollten Sie die aktuelle Version von SSMS verwenden. Informationen dazu finden Sie unter [Herunterladen von SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx). Mit der aktuellen Version informiert SSMS Sie automatisch darüber, wenn das aktuelle Update verfügbar ist.

## Starten von SSMS und Herstellen einer Verbindung mit dem SQL-Datenbankserver

1. Geben Sie im Windows-Suchfeld „Microsoft SQL Server Management Studio“ ein, und klicken Sie dann auf die Desktop-App, um SSMS zu starten.
2. Geben Sie im Dialogfeld **Verbindung mit Server herstellen** im Feld **Servername** den Namen des Servers, der Ihre SQL-Datenbank hostet, im Format *&lt;Servername>*.**database.windows.net** ein.
3. Wählen Sie in der Liste **Authentifizierung** den Eintrag **SQL Server-Authentifizierung** aus.
4. Geben Sie den **Anmeldenamen** und das **Kennwort** ein, den/das Sie beim Erstellen des Servers eingerichtet haben, und klicken Sie dann auf **Verbinden**, um eine Verbindung mit der SQL-Datenbank herzustellen.

	![SQL Server Management Studio: Verbinden mit einem SQL-Datenbankserver](./media/sql-database-connect-query-ssms/1-connect.png)

### Wenn die Verbindung mit der SQL-Datenbank nicht hergestellt werden kann

Der häufigsten Gründe für Verbindungsfehler sind Fehler im Server- oder Benutzernamen bzw. im Kennwort sowie dass der Server aus Sicherheitsgründen keine Verbindungen zulässt. Stellen Sie sicher, dass die Firewalleinstellungen des Servers Verbindungen von der IP-Adresse Ihres lokalen Computers und der vom SSMS-Client verwendeten IP-Adresse zulassen. Diese unterscheiden sich bisweilen.

Wenn aufgrund von Firewalleinstellungen ein Verbindungsfehler auftritt, erstellt die neueste Version von SSMS nach vorheriger Anfrage die Firewallregel. Sie finden sie unter [SSMS herunterladen](https://msdn.microsoft.com/library/mt238290.aspx). Wenn Sie eine frühere Version verwenden, wird Ihnen die IP-Adresse in einer Fehlermeldung mitgeteilt, und Sie müssen diese IP-Adresse der Firewallregel des Servers hinzufügen. Weitere Informationen finden Sie unter [Vorgehensweise: Konfigurieren von Firewalleinstellungen (Azure SQL-Datenbank)](sql-database-configure-firewall-settings.md).

## Durchführen von Beispielabfragen

Nach dem Herstellen der Verbindung mit SQL-Datenbank können Sie eine Beispielabfrage durchführen. Wenn Sie die Datenbank nicht mithilfe des AdventureWorks-Beispiels in [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md) erstellt haben, funktioniert diese Abfrage nicht. Fahren Sie direkt mit den nächsten Schritten fort, um mehr zu erfahren.

1. Navigieren Sie im **Objekt-Explorer** zur Datenbank **AdventureWorks**.
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie dann **Neue Abfrage** aus.

	![Neue Abfrage](./media/sql-database-connect-query-ssms/4-run-query.png)

3. Kopieren Sie den folgenden Code, und fügen Sie ihn im Abfragefenster ein.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Klicken Sie auf die Schaltfläche **Ausführen**. Der folgende Screenshot zeigt eine erfolgreiche Abfrage.

	![Erfolgreich](./media/sql-database-connect-query-ssms/5-success.png)

## Nächste Schritte

Mit T-SQL-Anweisungen können Sie Datenbanken in Azure auf gleiche Weise wie mit SQL Server erstellen und verwalten. Wenn Sie mit der Verwendung von T-SQL mit SQL Server vertraut sind, finden Sie unter [Azure SQL-Datenbank – Transact-SQL-Informationen](sql-database-transact-sql-information.md) eine Zusammenfassung der Unterschiede.

Wenn Sie mit T-SQL nicht vertraut sind, siehe [Tutorial: Schreiben von Transact-SQL-Anweisungen](https://msdn.microsoft.com/library/ms365303.aspx) und [Transact-SQL-Referenz (Datenbankmodul)](https://msdn.microsoft.com/library/bb510741.aspx).

<!---HONumber=Nov15_HO4-->