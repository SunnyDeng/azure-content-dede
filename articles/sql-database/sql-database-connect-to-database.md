<properties
   pageTitle="Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit SSMS | Microsoft Azure"
   description="Erfahren Sie, wie Sie mit SSMS eine Verbindung mit einer Azure SQL-Datenbank herstellen."
   services="sql-database"
   documentationCenter=""
   authors="sidneyh"
   manager="jeffreyg"
   editor=""
   tags=""/>
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="sidneyh"/>

# Herstellen einer Verbindung mit SQL Server Management Studio (SSMS)

Führen Sie die folgenden Schritte aus, um über SQL Server Management Studio (SSMS) eine Verbindung mit Ihrer SQL-Datenbank herzustellen und diese abzufragen.

## Voraussetzungen

* SQL Server Management Studio (SSMS) – Um die neueste Version von SSMS herunterzuladen, wechseln Sie zu [Herunterladen von SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
* Die AdventureWorks-Beispieldatenbank, wie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md) beschrieben.


## Abrufen des vollqualifizierten Namens des SQL Azure-Servers

Um eine Verbindung mit Ihrer Datenbank herzustellen, benötigen Sie den vollständigen Namen des Servers (****Servername**.database.windows.net*), auf dem sich die Datenbank befindet.

1. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com).
2. Navigieren Sie zu der Datenbank, mit der Sie eine Verbindung herstellen möchten.
3. Suchen Sie nach dem vollständigen Servernamen:

    ![vollqualifizierter Servername][6]

    Verwenden Sie den vollqualifizierten Servernamen in Schritt 3 weiter unten.



## Herstellen von Verbindungen mit der SQL-Datenbank

1. Öffnen Sie SSMS.
2. Klicken Sie auf **Verbinden** > **Datenbankmodul...**

    ![Verbinden > Datenbankmodul][7]

2. Geben Sie im Dialogfeld **Verbindung mit Server herstellen** im Feld **Servername** den Servernamen im Format *&lt;Servername>*.**database.windows.net** ein.
3. Wählen Sie in der Liste **Authentifizierung** den Eintrag **SQL Server-Authentifizierung** aus.
4. Geben Sie den **Benutzernamen** und das **Kennwort** ein, die Sie beim Erstellen des SQL-Datenbankservers angegeben haben, und klicken Sie auf **Verbinden**.

	![Dialogfeld "Verbindung mit Server herstellen"][2]



### Bei Verbindungsfehlern
Stellen Sie sicher, dass die Firewall des von Ihnen erstellten logischen Servers Verbindungen vom lokalen Computer zulässt. Weitere Informationen finden Sie unter [Konfigurieren der Firewalleinstellungen für Azure SQL-Datenbank](sql-database-configure-firewall-settings.md).

## Durchführen von Beispielabfragen

1. Navigieren Sie im **Objekt-Explorer** zur Datenbank **AdventureWorks**.
2. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie dann **Neue Abfrage** aus.

	![Neue Abfrage][4]

3. Kopieren Sie den folgenden Code, und fügen Sie ihn im Abfragefenster ein.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Klicken Sie auf die Schaltfläche **Ausführen**. Der folgende Screenshot zeigt eine erfolgreiche Abfrage.

	![Erfolgreich][5]




## Nächste Schritte
Sie können Transact-SQL-Anweisungen zum Erstellen oder Verwalten von Datenbanken verwenden. Weitere Informationen finden Sie unter [CREATE DATABASE (Azure SQL-Datenbank)](https://msdn.microsoft.com/library/dn268335.aspx) und [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio](sql-database-manage-azure-ssms.md). Sie können Ereignisse auch in Azure Storage protokollieren. Weitere Informationen finden Sie unter [Erste Schritte bei der Überwachung von SQL-Datenbank](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=Nov15_HO2-->