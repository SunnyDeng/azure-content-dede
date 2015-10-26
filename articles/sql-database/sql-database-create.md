<properties 
	pageTitle="Erstellen einer Datenbank in SQL-Datenbank-Update V12" 
	description="Veranschaulicht das Erstellen einer Datenbank in Azure SQL-Datenbank-Update V12" 
	services="sql-database" 
	documentationCenter="" 
	authors="sonalmm" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-management" 
	ms.date="04/28/2015" 
	ms.author="sonalm"/>


# Erstellen einer Datenbank in SQL-Datenbank V12


<!--
True author is: authors="sonalmm" , ms.author="sonalm".
-->


[Registrieren Sie sich](https://portal.azure.com) für das SQL-Datenbank-Update V12 [(Vorschau in einigen Regionen)](sql-database-v12-whats-new.md#V12AzureSqlDbPreviewGaTable), um die Vorteile der nächsten SQL-Datenbank-Generation in Microsoft Azure zu nutzen. Zunächst benötigen Sie ein Microsoft Azure-Abonnement. Registrieren Sie sich für [Einen Monat kostenlos testen!](http://azure.microsoft.com/pricing/free-trial), und lesen Sie die [Preisinformationen](http://azure.microsoft.com/pricing/details/sql-database).


| Erstellen einer Datenbank | Screenshot |
| :--- | :--- |
| 1\. Melden Sie sich bei [http://portal.azure.com/](http://portal.azure.com/) an. | ![Neues Azure-Portal][1] |
| 2\. Klicken Sie unten links auf der Seite auf **Neu**. | ![Initiieren eines neuen Diensts][2]|
| 3\. Klicken Sie auf **SQL-Datenbank**.| ![Verschiedene Dienste zur Auswahl][3] |
| 4\. Ein Blatt **SQL-Datenbank** wird geöffnet. Geben Sie im Feld **Name** einen Datenbanknamen an. | ![Name der Datenbank][4] |
| 5\. Klicken Sie auf dem Blatt **SQL-Datenbank** auf **SERVER**. Ein Blatt **Server** wird geöffnet, das zwei Auswahlmöglichkeiten bietet: Erstellen eines neuen Servers oder Verwenden eines vorhandenen Servers.| ![Auswählen des Servertyps][4] |
|5a. Bei Auswahl von **Vorhandenen Server verwenden** wählen Sie einen Server aus, und klicken Sie auf **Auswählen**. Führen Sie dann alle Aktionen ab Schritt 6 aus.| ![Auswählen eines Servers aus der Liste][5]| 
|5b. Bei Auswahl von **Create a new server** wird das Blatt **Neuer Server** geöffnet. Geben Sie einen Servernamen, eine Serveradministratoranmeldung und ein Kennwort ein. Klicken sie auf **Standort**, um den Serverstandort auszuwählen. | ![Abschließen der Erstellung der neuen Serveroptionen][9]| 
|5c. Auf dem Blatt **Neuer Server** können Sie den neuen Server mit V12-Updates erstellen. Weitere Informationen über die Funktionen von V12-Servern finden Sie unter [Neuerungen in Azure SQL-Datenbank V12](sql-database-v12-whats-new.md).| ![Auswählen des V12-Servers][6]|
|5d. Treffen Sie auf dem Blatt **Neuer Server** Ihre Auswahl, und klicken Sie auf **OK**. Sie kehren zum Blatt **SQL-Datenbank** zurück. Führen Sie dort die übrigen Aktionen zum Erstellen einer Datenbank aus. | ![Abschließen der Aktionen auf dem Blatt "Neuer Server"][8]|
|6\. Klicken Sie auf **Quelle auswählen**. Sie können eine Datenbank aus verschiedenen Arten von Datenquellen erstellen: aus einer leeren Datenbank, einer Beispieldatenbank oder aus der Sicherung einer Datenbank.| ![Auswählen der Quelle für die Datenbank][10]|
|7\. Klicken Sie auf dem Blatt **SQL-Datenbank** auf **PRICING TIER**. Sie können einen empfohlenen Tarif auswählen oder mit **Alle anzeigen** alle verfügbaren Tarife durchsuchen. Nachdem Sie Ihre Wahl getroffen haben, klicken Sie auf **Auswählen**. <p> Weitere Informationen zu Tarifen finden Sie unter [Ausführen eines Upgrades für Web-/Business-Datenbanken von SQL-Datenbank auf neue Dienstebenen](./sql-database-upgrade-new-service-tiers/) und [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](sql-database-service-tiers.md). |![Auswählen einer Preisstufe][7]
| 8\. Als Nächstes klicken Sie auf dem Blatt **SQL-Datenbank** auf **Optionale Konfiguration**, treffen Ihre Auswahl, und klicken auf **OK**. 
| 9\. Wenn Sie einen vorhandenen Server ausgewählt haben, sind **Ressourcengruppe** und **Abonnement** bereits ausgewählt. Auf dem Blatt **SQL-Datenbank** wird neben **Ressourcengruppe** und **Abonnement** ein Sperrsymbol angezeigt. Wenn Sie einen neuen Server erstellen, können Sie eine Ressourcengruppe auswählen oder erstellen. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](resource-group-overview.md).|![Angeben der Ressourcengruppe][11]
| 10\. Klicken Sie auf **Erstellen**. Eine neue Datenbank mit den Funktionen von SQL-Datenbank V12 wird erstellt. |![Erstellt eine neue Datenbank][12]

## Verwandte Links

- [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md)
- [Planen und Vorbereiten des Upgrades auf Azure SQL-Datenbank V12](sql-database-v12-plan-prepare-upgrade.md)

<!--Image references-->
[1]: ./media/sql-database-create/firstscreenportal.png
[2]: ./media/sql-database-create/new.png
[3]: ./media/sql-database-create/sqldatabase.png
[4]: ./media/sql-database-create/databasename.png
[5]: ./media/sql-database-create/useexistingserver.PNG
[6]: ./media/sql-database-create/v12server.PNG
[7]: ./media/sql-database-create/pricingtierdetails.png
[8]: ./media/sql-database-create/finishnewserverblade.png
[9]: ./media/sql-database-create/createnewserver.png
[10]: ./media/sql-database-create/selectsource.png
[11]: ./media/sql-database-create/resourcegroup.png
[12]: ./media/sql-database-create/create.png

 

<!---HONumber=Oct15_HO3-->