<properties
	pageTitle="Informationen zum Verschieben von Datenbanken zwischen Servern und Abonnements sowie in und aus Azure."
	description="Kurzanweisungen zum Kopieren, Verschieben und Migrieren von Daten und Datenbanken in Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/10/2015"
	ms.author="daleche"/>

# Verschieben von Datenbanken zwischen Servern und Abonnements sowie in und aus Azure
##So verschieben Sie eine Datenbank auf einen anderen Server im selben Abonnement
- Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL-Datenbanken**, wählen Sie in der Liste eine Datenbank aus, und klicken Sie dann auf **Kopieren**. Weitere Details finden Sie unter [Kopieren einer Azure SQL-Datenbank](sql-database-copy.md).

## So verschieben Sie eine Datenbank zwischen Abonnements
- Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL-Server**, und wählen Sie in der Liste den Server aus, der die Datenbank hostet. Klicken Sie auf **Verschieben**, und wählen Sie die zu verschiebenden Ressourcen und das Abonnement aus, in das dieser Vorgang erfolgen soll.

## So migrieren Sie eine SQL-­Datenbank in Azure
- Bestimmen Sie die Datenbankkompatibilität, und wählen Sie dann basierend auf Ihren Anforderungen die geeignete Migrationsmethode. Befolgen Sie die Anleitungen und Optionen im Thema „Migrieren einer SQL Server-Datenbank“.

## So erstellen Sie eine Kopie einer Datenbank zur Verwendung außerhalb von Azure
- [Exportieren Sie eine BACPAC-Datei.](sql-database-export.md)

<!---HONumber=AcomDC_1210_2015-->