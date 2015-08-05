<properties 
	pageTitle="Installieren von elastischen Datenbankaufträgen" 
	description="Schrittweise Anleitung für die Installation der Funktion für elastische Aufträge" 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# Installieren der Komponente für elastische Datenbankaufträge

Der [elastische Datenbankpool (Vorschau)](sql-database-elastic-pool-portal.md) bietet ein vorhersagbares Modell zur Bereitstellung einer großen Anzahl von Datenbanken. Nachdem Sie einen elastischen Datenbankpool erstellt haben, können Sie **elastische Datenbankaufträge** zum Ausführen von Verwaltungsaufgaben für die einzelnen Datenbanken im elastischen Datenbankpool verwenden. Beispielsweise können Sie neues Schema bereitstellen, wie z. B. die Festlegung einer RLS-Richtlinie für jede Datenbank zum Einschränken der Daten auf eine einzelne Person mit den richtigen Anmeldeinformationen zum Anzeigen vertraulicher Daten. Im Folgenden wird das Installieren von **elastischen Datenbankaufträgen** beschrieben.

**Geschätzter Zeitaufwand:** 10 Minuten.

## Voraussetzungen
* Ein Azure-Abonnement. Eine kostenlose Testversion finden Sie unter [Einen Monat kostenlos testen](http://azure.microsoft.com/pricing/free-trial/).
* Ein elastischer Datenbankpool. Siehe [Erstellen elastischer Datenbankpools in Azure SQL-Datenbank (Vorschau)](sql-database-elastic-pool-portal.md).

## Installieren der Dienstkomponenten
Wechseln Sie zunächst zum [Azure-Vorschauportal](https://ms.portal.azure.com/#).


1. Klicken Sie in der Dashboardansicht des elastischen Datenbankpools auf **Auftrag erstellen**.
2. Wenn Sie zum ersten Mal einen Auftrag erstellen, müssen Sie **elastische Datenbankaufträge** installieren, indem Sie auf **PREVIEW TERMS** klicken. 
3. Akzeptieren Sie die Bedingungen, indem Sie das Kontrollkästchen aktivieren.
4. Klicken Sie in der Ansicht "Dienste installieren" auf **JOB CREDENTIALS**.

	![Installieren der Dienste][1]

5. Geben Sie einen Benutzernamen und ein Kennwort für einen Datenbankadministrator ein. Wenn bereits ein allgemeiner Benutzer für die Skriptausführung in jeder Datenbank im elastischen Datenbankpool vorhanden ist, sollten Sie möglicherweise diesen Benutzer verwenden, um die Anforderung aufzuheben, dass jeder Datenbank für die Skriptausführung ein neuer Benutzer hinzugefügt werden muss. Im Rahmen der Installation wird ein neuer Azure SQL-Datenbank-Server erstellt. Auf diesem neuen Server wird eine neue Datenbank erstellt, die als Verwaltungsdatenbank bezeichnet wird und die Metadaten für elastische Datenbankaufträge enthält. Den Benutzernamen und das Kennwort, die hier erstellt wurden, werden für zwei Zwecke verwendet: (1) zur Anmeldung bei der Verwaltungsdatenbank und (2) zur Anmeldung bei den einzelnen Datenbanken im elastischen Pool, wenn Sie einen Auftrag für die Skriptausführung durchführen.
 
	![Erstellen von Benutzername und Kennwort][2]
6. Klicken Sie auf die Schaltfläche "OK". Die Komponenten werden in wenigen Minuten in einer neuen [Ressourcengruppe](../resource-group-portal.md) erstellt. Die neue Ressourcengruppe wird an Start angeheftet, wie unten dargestellt. Nach der Erstellung werden alle elastischen Datenbankaufträge (Cloud Service, SQL-Datenbank, Service Bus und Storage) in der Gruppe erstellt.

	![Ressourcengruppe unter Start][3]


7. Wenn Sie versuchen, einen Auftrag zu erstellen oder zu verwalten, während die elastischen Datenbankaufträge installiert werden, wird bei der Eingabe der **Anmeldeinformationen** die folgende Meldung angezeigt.

	![Bereitstellung noch nicht abgeschlossen][4]

8. Wenn die Installation aus irgendeinem Grund fehlschlägt, löschen Sie die Ressourcengruppe. Weitere Informationen finden Sie unter [Deinstallieren der Komponenten für elastische Datenbankaufträge](sql-database-elastic-jobs-uninstall.md).


## Nächste Schritte

Wenn neue Anmeldeinformationen bei der Installation der elastischen Datenbankaufträge angegeben wurden, die noch nicht in den einzelnen Datenbanken im elastischen Datenbankpool mit den entsprechenden Berechtigungen für die Skriptausführung vorhanden waren, müssen die Anmeldeinformationen für jede Datenbank erstellt werden. Weitere Informationen finden Sie unter [Hinzufügen von Benutzern zu einem elastischen Datenbankpool](sql-database-elastic-jobs-add-logins-to-dbs.md). Weitere Informationen zur Auftragserstellung finden Sie unter [Erstellen und Verwalten eines elastischen Datenbankauftrags](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=July15_HO4-->