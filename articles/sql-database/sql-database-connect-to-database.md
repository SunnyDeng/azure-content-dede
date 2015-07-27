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
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# Herstellen von Verbindungen mit einer Azure SQL-Datenbank mit SQL Server Management Studio

Mithilfe der folgenden Schritte können Sie eine Verbindung mit einer Microsoft Azure SQL-Datenbank mit SQL Server Management Studio (SSMS) herstellen.

## Voraussetzungen
* Eine bereitgestellte und ausgeführte Azure SQL-Datenbank. Informationen zum Erstellen einer neuen SQL-Datenbank finden Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md).
* Der Anmeldename und das Kennwort des Administrators
* SQL Server Management Studio 2014 Sie finden das Tool unter [Download SQL Server Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx) (in englischer Sprache).
* Konfigurieren Sie die Firewalleinstellungen für die Datenbank. Weitere Informationen finden Sie unter [Konfigurieren der Firewalleinstellungen (Azure SQL-Datenbank)](sql-database-configure-firewall-settings.md).

## So stellen Sie eine Verbindung mit einer Instanz von SQL-Datenbank her
1. Melden Sie sich beim [Azure-Verwaltungsportal](https://portal.azure.com) an.
2. Klicken Sie auf die Schaltfläche **Durchsuchen** und dann auf **SQL-Datenbanken** (b).

	![Klicken auf "Durchsuchen" und "SQL-Datenbank"][1]
3. Klicken Sie nach dem Auswählen von **SQL-Datenbanken** (a) auf den Namen einer Datenbank auf dem Server, mit der Sie eine Verbindung herstellen möchten (b).

	![Klicken auf den Namen einer Datenbank][2]
4. Klicken Sie nach der Auswahl des Namens (a) auf "Eigenschaften" (b). Kopieren Sie den Namen des Servers (c) und den Namen des Administrators (d). Der Administratorname und das zugehörige Kennwort werden bei der Erstellung der SQL-Datenbank erstellt. Sie benötigen das Kennwort im nächsten Schritt.

	![Klicken auf "SQL Server", "Einstellungen" und "Eigenschaften"][3]
5. Öffnen Sie SQL Server Management Studio 2014.
6. Fügen Sie im Dialogfeld "Verbindung mit Server herstellen" den Namen des Servers im Feld **Servername** (a) ein. Legen Sie die Authentifizierung auf **SQL Server-Authentifizierung** (b) fest. Fügen Sie den Namen des Serveradministrators im Feld **Anmeldung** (c) ein, und geben Sie dann das zugehörige Kennwort (d) ein. Klicken Sie dann auf **Optionen** (e).

	![SSMS-Anmeldung (Dialogfeld)][4]
7. Legen Sie auf der Registerkarte "Verbindungseigenschaften" das Feld **Verbindung mit Datenbank herstellen** auf **master** (oder eine andere Datenbank, mit der Sie eine Verbindung herstellen möchten) fest. Klicken Sie dann auf **Verbinden**.

	![Festlegen auf "master" und Klicken auf "Verbinden"][5]

## Beheben von Verbindungsproblemen

Im Fall von Problemen lesen Sie unter [Behandeln von Verbindungsproblemen mit Azure SQL-Datenbank](https://support.microsoft.com/kb/2980233/) nach. Eine Liste der möglichen Probleme und Antworten finden Sie unter [Troubleshoot Microsoft Azure SQL Database connectivity](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1) (Behandeln von Verbindungsproblemen in Microsoft Azure SQL-Datenbank, in englischer Sprache).


## Nächste Schritte
Sie können Transact-SQL-Anweisungen zum Erstellen oder Verwalten von Datenbanken verwenden. Weitere Informationen finden Sie unter [CREATE DATABASE (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx) und [Verwalten von Azure SQL-Datenbank mit SQL Server Management Studio](sql-database-manage-azure-ssms.md). Sie können Ereignisse auch in Azure Storage protokollieren. Weitere Informationen finden Sie unter [Erste Schritte mit SQL Database-Auditing](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->