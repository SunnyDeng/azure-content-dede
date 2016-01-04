<properties 
	pageTitle="Hinzufügen von Benutzern zu einem elastischen Datenbankpool" 
	description="Sie müssen einen Benutzer mit Berechtigungen für jede Datenbank im Pool hinzufügen." 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# Hinzufügen von Benutzern zu einem elastischen Datenbankpool

Das (in der Vorschau verfügbare) Feature **Elastische Datenbankaufträge** ermöglicht das Anwenden eines Transact-SQL-Skripts auf eine Gruppe von Datenbanken, einschließlich einer benutzerdefinierten Sammlung von Datenbanken, einen **elastischen Datenbankpool** oder einen **elastischen Datenbank-Shardsatz** in Azure SQL-Datenbank. Um ein Skript für elastische Datenbankaufträge ausführen zu können, muss ein Benutzer mit den entsprechenden Berechtigungen jeder Datenbank hinzugefügt werden, in denen der Auftrag ausgeführt wird. Weitere Informationen finden Sie unter [Verwalten von Datenbanken, Anmeldungen und Benutzern in der Azure SQL-Datenbank](sql-database-manage-logins.md) oder [Adding Users to Your SQL Azure Database](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/) (Hinzufügen von Benutzern zur Azure SQL-Datenbank, in englischer Sprache).

## Voraussetzungen
* Installieren Sie die [Komponenten für elastische Aufträge](sql-database-elastic-jobs-service-installation.md). 

## Hinzufügen von Benutzern zu Datenbanken

1.	Stellen Sie zunächst eine Verbindung mit dem **master** des Azure SQL-Datenbankservers her, auf dem sich die Datenbanken des elastischen Datenbankpools befinden, und erstellen Sie eine neue Anmeldung mit denselben Anmeldeinformationen, die Sie bei der Installation der **elastischen Datenbankaufträge** angegeben haben.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Melden Sie sich bei jeder Datenbank im Pool, an, und erstellen Sie einen Benutzer mit dem gleichen Namen und Kennwort. Der Benutzer muss über ausreichende Berechtigungen zum Ausführen des Auftrags verfügen. Dieser Code muss für jede Datenbank ausgeführt werden.

		CREATE USER admin1 FROM LOGIN login1;
		
3. Der Benutzer muss außerdem über ausreichende Berechtigungen zum Ausführen des für das Projekt festgelegten Skripts verfügen. Verwenden Sie [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx), um den Benutzer mit den erforderlichen Mindestberechtigungen zum erfolgreichen Ausführen des Skripts auszustatten.

## Nächste Schritte

Informationen zum Erstellen und Verwalten von Aufträgen mit dem Azure-Portals finden Sie unter [Erstellen und Verwalten von elastischen Datenbankaufträgen](sql-database-elastic-jobs-create-and-manage.md). Alternativ können Sie Aufträge auch mit PowerShell erstellen. Nähere Informationen hierzu finden Sie unter [Erstellen und Verwalten von Aufträgen für die elastische SQL-Datenbank mithilfe von PowerShell (Vorschau)](sql-database-elastic-jobs-powershell.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->