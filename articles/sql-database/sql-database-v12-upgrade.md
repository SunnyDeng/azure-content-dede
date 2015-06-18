<properties 
	pageTitle="Upgrade auf SQL-Datenbank V12" 
	description="Erläutert, wie von einer früheren Version von Azure SQL-Datenbank ein Upgrade auf Azure SQL-Datenbank V12 durchgeführt wird." 
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
	ms.date="05/15/2015" 
	ms.author="sonalm"/>


# Upgrade auf SQL-Datenbank V12 vorhanden


[Registrieren Sie sich](https://portal.azure.com) für SQL-Datenbank V12, um die Vorteile der nächsten SQL-Datenbankgeneration in Microsoft Azure zu nutzen. Zunächst benötigen Sie ein Microsoft Azure-Abonnement. Registrieren Sie sich für eine [kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial), und lesen Sie die [Preisinformationen](http://azure.microsoft.com/pricing/details/sql-database).


## Schritte für das Upgrade auf SQL-Datenbank V12


| Upgradeschritte | Screenshot |
| :--- | :--- |
| 1. Melden Sie sich bei [http://portal.azure.com/](http://portal.azure.com/) an. | ![Neues Azure-Portal][1] |
| 2. Klicken Sie auf **DURCHSUCHEN**. | ![Durchsuchen von Diensten][2] |
| 3. Klicken Sie auf **SQL-Server**. Eine Liste der Namen der Computer mit SQL-Server wird angezeigt. | ![Auswählen des SQL Server-Dienstes][3] |
| 4. Wählen Sie den Server aus, den Sie auf einen neuen Server kopieren möchten, auf dem das SQL-Datenbankupdate aktiviert ist. | ![Zeigt eine Liste der SQL-Server an][4] |
| 5. Klicken Sie auf **Upgrade auf das aktuelle SQL-Datenbankupdate V12**. | ![Aktuelles Vorschaufeature][5] |
| 6. Klicken Sie auf **UPGRADE FÜR DIESEN SERVER AUSFÜHREN**. | ![SQL Server-Upgrade auf die Preview][6] |


> [AZURE.NOTE]Nachdem Sie die Upgradeoption ausgewählt haben, werden die Funktionen der SQL-Datenbank V12 für den Server und die Datenbanken auf diesem Server aktiviert. Dieser Vorgang kann nicht rückgängig gemacht werden. Damit für Server ein Upgrade auf SQL-Datenbank V12 durchgeführt werden kann, ist die Dienstebene "Basic", "Standard" oder "Premium" erforderlich. Weitere Informationen zu den Dienstebenen finden Sie unter [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](sql-database-upgrade-new-service-tiers.md).


> [AZURE.IMPORTANT]Georeplikation wird von SQL-Datenbank V12 (Preview) nicht unterstützt. Weitere Informationen finden Sie unter [Planen und Vorbereiten des Upgrades auf die Azure SQL-Datenbank V12 (Preview)](sql-database-v12-plan-prepare-upgrade.md).


Nachdem Sie auf die Option **UPGRADE FÜR DIESEN SERVER AUSFÜHREN** geklickt haben, wird ein Blatt geöffnet, das eine Meldung zu einem Überprüfungsvorgang anzeigt.


- Der Überprüfungsvorgang überprüft die Dienstebene Ihrer Datenbank und ermittelt, ob Georeplikation aktiviert ist. In diesem Bereich werden auch die Ergebnisse angezeigt, nachdem die Überprüfung abgeschlossen ist. 
- Nachdem der Überprüfungsvorgang abgeschlossen wurde, wird eine Liste der Datenbanknamen angezeigt, für die Aktionen erforderlich sind, um die Anforderungen eines Upgrades auf SQL-Datenbank V12 zu erfüllen.
 - **Sie müssen diese Aktionen für jede dieser Datenbanken durchführen, damit ein Upgrade auf SQL-Datenbank V12 erfolgen kann**.
- Wenn Sie auf die einzelnen Datenbanknamen klicken, stellt ein neuer Bereich Empfehlungen für Dienstpreisstufen basierend auf Ihrer aktuellen Nutzung zur Verfügung. Sie können auch verschiedene Preisstufen durchsuchen und die Stufe auswählen, die am besten für Ihre Umgebung geeignet ist. Alle Datenbanken, die für Georeplikation eingerichtet sind, müssen so neu konfiguriert werden, dass die Replikation beendet wird. 
- Beachten Sie, dass keine Empfehlung für die Preisstufe angezeigt wird, wenn nicht genügend Daten gefunden wurden. 


| Aktion | Screenshot |
| :--- | :--- |
| 7. Nachdem Sie die Aktionen abgeschlossen haben, die Ihren Server auf das Upgrade vorbereiten, geben Sie den Namen des Servers ein, für den das Upgrade durchgeführt werden soll. Klicken Sie dann auf **OK**. | ![Bestätigen Sie den Namen des Servers, für den ein Upgrade durchgeführt werden soll][7] |
| 8. Der Upgradevorgang wird eingeleitet. Das Upgrade kann bis zu 24 Stunden dauern. Während dieser Zeitspanne bleiben alle Datenbanken auf diesem Server online. Server- und Datenbankverwaltungsaktionen sind jedoch eingeschränkt. Nachdem der Vorgang abgeschlossen wurde, wird der Status **Aktiviert** im Serverbereich angezeigt. | ![Bestätigt, dass Vorschaufeatures aktiviert sind][8] |


## Powershell-Cmdlets


Powershell-Cmdlets sind verfügbar, um ein Upgrade auf Azure SQL-Datenbank V12 von V11 oder einer anderen Version niedriger als V12 zu starten, zu beenden oder zu überwachen.


Referenzdokumentation zu diesen Powershell-Cmdlets finden Sie unter:


- [Get-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143621.aspx)
- [Start-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143623.aspx)
- [Stop-AzureSqlServerUpgrade](http://msdn.microsoft.com/library/mt143622.aspx)


Mit dem Cmdlet "Stop-" wird der Vorgang abgebrochen, nicht angehalten. Es ist nicht möglich, ein Upgrade nach einem Abbruch fortzusetzen, sondern es muss wieder von vorne begonnen werden. Das Cmdlet "Stop-" bereinigt und setzt alle entsprechenden Ressourcen frei.


## Verwandte Links

-  [Neuerungen in SQL-Datenbank V12](sql-database-v12-whats-new.md)
- [Planen und Vorbereiten des Upgrades auf die SQL-Datenbank V12](sql-database-v12-plan-prepare-upgrade.md)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/firstscreenportal.png
[2]: ./media/sql-database-v12-upgrade/browse.png
[3]: ./media/sql-database-v12-upgrade/sqlserver.png
[4]: ./media/sql-database-v12-upgrade/sqlserverlist.png
[5]: ./media/sql-database-v12-upgrade/latestprview.png
[6]: ./media/sql-database-v12-upgrade/upgrade.png
[7]: ./media/sql-database-v12-upgrade/typeservername.png
[8]: ./media/sql-database-v12-upgrade/enabled.png

<!---HONumber=58--> 