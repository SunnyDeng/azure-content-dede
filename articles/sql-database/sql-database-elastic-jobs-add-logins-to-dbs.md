<properties 
	pageTitle="Hinzufügen von Benutzern zu einem elastischen Datenbankpool" 
	description="Sie müssen einen Benutzer mit Berechtigungen für jede Datenbank im Pool hinzufügen." 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="sidneyh" />

# Hinzufügen von Benutzern zu einem elastischen Datenbankpool

**Elastische Datenbankaufträge** ermöglichen das Ausführen des gleichen Skripts für jede Datenbank in einem [elastischen Datenbankpool](sql-database-elastic-pool.md). Um ein Skript für elastische Datenbankaufträge ausführen zu können, muss ein Benutzer mit den entsprechenden Berechtigungen zu jeder Datenbank im Pool hinzugefügt werden. Weitere Informationen finden Sie unter [Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) oder [Adding Users to Your SQL Azure Database](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/) \(Hinzufügen von Benutzern zur Azure SQL-Datenbank, in englischer Sprache\).

## Voraussetzungen
* [Erstellen eines elastischen Datenbankpools \(Vorschau\)](sql-database-elastic-pool-portal.md)
* Installieren Sie die [Komponenten für elastische Aufträge](sql-database-elastic-jobs-service-installation.md). 

## Hinzufügen von Benutzern zu Datenbanken

1.	Stellen Sie zunächst eine Verbindung mit dem **master** des Azure SQL-Datenbankservers her, auf dem sich die Datenbanken des elastischen Datenbankpools befinden, und erstellen Sie eine neue Anmeldung mit denselben Anmeldeinformationen, die Sie bei der Installation der **elastischen Datenbankaufträge** angegeben haben.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Melden Sie sich bei jeder Datenbank im Pool, an, und erstellen Sie einen Benutzer mit dem gleichen Namen und Kennwort. Der Benutzer muss über ausreichende Berechtigungen zum Ausführen des Auftrags verfügen. Dieser Code muss für jede Datenbank ausgeführt werden.

		CREATE USER admin1 FROM LOGIN login1;
		
3. Der Benutzer muss außerdem über ausreichende Berechtigungen zum Ausführen des für das Projekt festgelegten Skripts verfügen. Verwenden Sie die **sp\_addrolemember**-Prozedur, um den Benutzer mit den erforderlichen Mindestberechtigungen für das Skript auszustatten.

## Nächste Schritte

Führen Sie einen Auftrag für den elastischen Datenbankpool aus. Siehe [Erstellen und Verwalten von elastischen Datenbankaufträgen](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO5-->