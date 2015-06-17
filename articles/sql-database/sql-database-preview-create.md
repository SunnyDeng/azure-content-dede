<properties 
	pageTitle="Erstellen einer Datenbank im aktuellen SQL-Datenbankupdate V12 (Preview)" 
	description="Erstellen einer Datenbank im aktuellen SQL-Datenbankupdate V12 (Preview)" 
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
	ms.date="12/11/2014" 
	ms.author="sonalm"/>


#Erstellen einer Datenbank im aktuellen SQL-Datenbankupdate V12 (Preview)

[Registrieren Sie sich](https://portal.azure.com) für das aktuelle SQL-Datenbankupdate V12, um die Vorteile der nächsten SQL-Datenbankgeneration in Microsoft Azure zu nutzen. Zum Einstieg benötigen Sie ein Microsoft Azure-Abonnement. Registrieren Sie sich für eine [kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial), und lesen Sie die [Preisinformationen](http://azure.microsoft.com/pricing/details/sql-database). 


| Erstellen einer Datenbank | Screenshot |
| :--- | :--- |
| 1. Melden Sie sich bei [http://portal.azure.com/](http://portal.azure.com/) an. | ![New Azure Portal][1] |
| 2. Klicken Sie unten links auf der Seite auf **Neu**. | ![Initiate New service][2]|
| 3. Klicken Sie auf **SQL-Datenbank**.| ![Different services to select from][3] |
| 4. Ein Bereich **SQL-Datenbank** wird geöffnet. Geben Sie im Feld **Name** einen Datenbanknamen an. | ![Name the database][4] |
| 5. Klicken Sie im Fenster **SQL-Datenbank** auf **SERVER**. Ein Bereich **Server** wird geöffnet, der zwei Auswahlmöglichkeiten bietet: Sie können einen neuen Server erstellen oder einen vorhandenen Server verwenden.| ![select type of server][4] |
|5a. Bei Auswahl von **Vorhandenen Server verwenden** wählen Sie einen Server Ihrer Wahl, und klicken Sie auf **Auswählen**. Führen Sie dann alle Aktionen ab Schritt 6 aus.| ![select a server from the list][5]| 
|5b.   Bei Auswahl von **Neuen Server erstellen** wird das Fenster **Neuer Server** geöffnet. Geben Sie einen Servernamen, eine Serveradministratoranmeldung und ein Kennwort ein. Klicken Sie auf **Speicherort**,   um den Serverstandort auszuwählen. | ![Complete create new server options][9]| 
|5c. Im Fenster **Neuer Server** können Sie den neuen Server mit V12-Updates erstellen. Weitere Informationen über die Features in V12-Servern finden Sie unter [Neuerungen in der Azure SQL-Datenbank](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/).| ![Select V12 server][6]|
|5d. Treffen Sie im Fenster **Neuer Server** Ihre Auswahl, und klicken Sie auf **OK**. Sie kehren zum Fenster **SQL-Datenbank** zurück. Führen Sie dort die übrigen Aktionen zum Erstellen einer Datenbank aus. | ![Complete New Server blade actions][8]|
|6. Klicken Sie auf **Quelle auswählen**. Sie können eine Datenbank aus verschiedenen Arten von Datenquellen erstellen: aus einer leeren Datenbank, einer Beispieldatenbank oder aus der Sicherung einer Datenbank.| ![Select the source for the database][10]|
|7. Klicken Sie im Fenster **SQL-Datenbank** auf **PREISSTUFE**. Sie können eine empfohlene Preisstufe auswählen oder alle verfügbaren Preisstufen durchsuchen. Nachdem Sie Ihre Wahl getroffen haben, klicken Sie auf **Auswählen**. <p> Weitere Informationen zu Preisstufen finden Sie unter [Ausführen eines Upgrades für Web-/Business-Datenbanken von SQL-Datenbank auf neue Dienstebenen](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/) und [Azure SQL-Datenbankdienstebenen und -Leistungsstufen](http://msdn.microsoft.com/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. Als Nächstes klicken Sie im Fenster **SQL-Datenbank** auf **Optionale Konfiguration**, treffen Ihre Auswahl und klicken auf **OK**. 
| 9. Klicken Sie im Fenster **SQL-Datenbank** auf **Ressourcengruppe**. Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe aus der Liste aus. Klicken Sie auf **OK**. Weitere Informationen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups).|![Specify Resource group][11]
| 10. Klicken Sie im Fenster **SQL-Datenbank** auf **ABONNEMENT**. Ein Fenster **Abonnement** wird geöffnet. Wählen Sie ein Azure-Abonnement, das Ihre Serverauswahl unterstützt. Beachten Sie, dass sich die Servereinstellungen basierend auf der Abonnementauswahl möglicherweise ändern.| ![Select subscription.][13]
| 11. Klicken Sie auf **Erstellen**. Eine neue Datenbank mit den Funktionen des SQL-Datenbankupdates V12 wird erstellt. |![Creates a new database][12]

#Verwandte Links  #

-  [Neuerungen in der Azure SQL-Datenbank](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)

<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/useexistingserver.PNG
[6]: ./media/sql-database-preview-create/v12server.PNG
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/finishnewserverblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/selectsource.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png
[13]: ./media/sql-database-preview-create/subscription.PNG


<!--HONumber=47-->
 