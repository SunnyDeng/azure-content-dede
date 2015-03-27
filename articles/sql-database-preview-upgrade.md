<properties 
	pageTitle="Upgrade auf das aktuelle SQL-Datenbankupdate V12 (Preview)" 
	description="Upgrade auf das aktuelle SQL-Datenbankupdate V12 (Preview)" 
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



#Upgrade auf das aktuelle SQL-Datenbankupdate V12 (Preview)


[Registrieren Sie sich](https://portal.azure.com) für das aktuelle SQL-Datenbankupdate V12, um die Vorteile der nächsten  SQL-Datenbankgeneration in Microsoft Azure zu nutzen. Zunächst benötigen Sie ein Microsoft Azure-Abonnement. Registrieren Sie sich für eine [kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial), und lesen Sie die [Preisinformationen](http://azure.microsoft.com/pricing/details/sql-database). 

##Ausführen eines direkten Serverupgrades mit dem aktuellen SQL-Datenbankupdate ##

| Upgrade  | Screenshot |
| :--- | :--- |
| 1. Melden Sie sich bei [http://portal.azure.com/](http://portal.azure.com/) an. | ![New Azure Portal][1] |
| 2. Klicken Sie auf **DURCHSUCHEN**. | ![Browse Services][2] |
| 3.	Klicken Sie auf **SQL-Server**. Eine Liste der Namen der Computer mit SQL-Server wird angezeigt. | ![Select SQL Server service][3] |
| 4. Wählen Sie den Server aus, den Sie auf einen neuen Server kopieren möchten, auf dem das SQL-Datenbankupdate aktiviert ist. | ![Shows a list of SQL Servers][4] |
| 5. Klicken Sie auf **Upgrade auf das aktuelle SQL-Datenbankupdate V12**. | ![Latest preview feature][5] |
| 6. Klicken Sie auf **UPGRADE FÜR DIESEN SERVER AUSFÜHREN**. | ![Upgrades the SQL Server to the preview][6] |

> [AZURE.NOTE] **WICHTIG**: Nachdem Sie die Upgradeoption ausgewählt haben, werden die Funktionen des SQL-Datenbankupdates V12 für den Server und die Datenbanken auf diesem Server aktiviert. Dieser Vorgang kann nicht rückgängig gemacht werden. Damit ein Upgrade von Servern auf das SQL-Datenbankupdate V12 ausgeführt werden kann, benötigen Sie eine Basic-, Standard- oder Premium-Dienstebene. Weitere Informationen zu den Dienstebenen finden Sie unter [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](http://azure.microsoft.com/documentation/articles/sql-database-upgrade-new-service-tiers/).

> **WICHTIG**: Georeplikation wird mit dem SQL-Datenbankupdate V12 (Preview) nicht unterstützt. Weitere Informationen finden Sie unter [Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade).


Nachdem Sie auf die Option **UPGRADE FÜR DIESEN SERVER AUSFÜHREN** geklickt haben, zeigt der Bereich, der geöffnet wird, eine Meldung zu einem Überprüfungsvorgang an. 

- Der Überprüfungsvorgang überprüft die Dienstebene Ihrer Datenbank und ermittelt, ob Georeplikation aktiviert ist. In diesem Bereich werden auch die Ergebnisse angezeigt, nachdem die Überprüfung abgeschlossen ist. 
- Nachdem der Überprüfungsvorgang abgeschlossen wurde, wir eine Liste der Datenbanknamen angezeigt, für die Aktionen erforderlich sind, um die Anforderungen eines Upgrades auf das SQL-Datenbankupdate V12 zu erfüllen. **Sie müssen diese Aktionen für jede dieser Datenbanken abschließen, damit ein Upgrade auf das SQL-Datenbankupdate V12 erfolgen kann**.
- Wenn Sie auf die einzelnen Datenbanknamen klicken, stellt ein neuer Bereich Empfehlungen für Dienstpreisstufen basierend auf Ihrer aktuellen Nutzung zur Verfügung. Sie können auch verschiedene Preisstufen durchsuchen und die Stufe auswählen, die am besten für Ihre Umgebung geeignet ist. Alle Datenbanken, die für Georeplikation eingerichtet sind, müssen so neu konfiguriert werden, dass die Replikation beendet wird. 
- Beachten Sie, dass keine Empfehlung für die Preisstufe angezeigt wird, wenn nicht genügend Daten gefunden wurden. 

| Aktion | Screenshot |
| :--- | :--- |
| 7. Nachdem Sie die Aktionen abgeschlossen haben, die Ihren Server auf das Upgrade vorbereiten, geben Sie den Namen des Servers ein, für den das Upgrade aufgeführt werden soll. Klicken Sie dann auf **OK**. | ![Confirm the server name to upgrade][7] |
| 8. Der Upgradevorgang wird eingeleitet. Das Upgrade kann bis zu 24 Stunden dauern. Während dieser Zeitspanne bleiben alle Datenbanken auf diesem Server online. Server- und Datenbankverwaltungsaktionen sind jedoch eingeschränkt. Nachdem der Vorgang abgeschlossen wurde, wird der Status **Aktiviert** im Serverbereich angezeigt. | ![Confirms preview features are enabled][8] |
 

#Verwandte Links  #

-  [Neuerungen im SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-whats-new/)
- [Planen und Vorbereiten des Upgrades auf das aktuelle SQL-Datenbankupdate V12 (Preview)](http://azure.microsoft.com/documentation/articles/sql-database-preview-plan-prepare-upgrade/)


<!--Image references-->
[1]: ./media/sql-database-preview-upgrade/firstscreenportal.png
[2]: ./media/sql-database-preview-upgrade/browse.png
[3]: ./media/sql-database-preview-upgrade/sqlserver.png
[4]: ./media/sql-database-preview-upgrade/sqlserverlist.png
[5]: ./media/sql-database-preview-upgrade/latestprview.png
[6]: ./media/sql-database-preview-upgrade/upgrade.png
[7]: ./media/sql-database-preview-upgrade/typeservername.png
[8]: ./media/sql-database-preview-upgrade/enabled.png
<!--HONumber=47-->
