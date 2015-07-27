<properties 
   pageTitle="Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung und REST-API" 
   description="Zeitpunktwiederherstellung, Microsoft Azure SQL-Datenbank, Datenbank wiederherstellen, REST-API" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="03/17/2015"
   ms.author="elfish; v-romcal"/>

# Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung und REST-API

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md) 

## Übersicht

In dieser Anleitung wird gezeigt, wie Sie eine Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung und REST-API wiederherstellen. Links zu ausführlicheren Vorgängen werden bereitgestellt.

Bei einer Zeitpunktwiederherstellung wird eine neue Datenbank erstellt. Der Dienst wählt die Dienstebene basierend auf der beim Wiederherstellungspunkt verwendeten Sicherung automatisch aus. Stellen Sie sicher, dass Ihnen auf dem logischen Server ein Kontingent zum Erstellen einer weiteren Datenbank zur Verfügung steht. Wenn Sie ein erhöhtes Kontingent anfordern möchten, wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/).

## Einschränkungen und Sicherheit

Siehe [Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung im Azure-Portal](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Vorgehensweise: Wiederherstellen einer Azure SQL-Datenbank mithilfe der REST-API

1.	Rufen Sie die Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Datenbank abrufen](http://msdn.microsoft.com/library/azure/dn505708.aspx) ab.

2.	Erstellen Sie die Wiederherstellungsanforderung mithilfe des Vorgangs [Datenbankwiederherstellungsanforderung erstellen](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
3.	Verfolgen Sie die Wiederherstellungsanforderung mithilfe des Vorgangs [Datenbank-Betriebsstatus](http://msdn.microsoft.com/library/azure/dn720371.aspx) nach.

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

[Geschäftskontinuität in Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sichern und Wiederherstellen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL-Datenbank – Zeitpunktwiederherstellung (Blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx)
 

<!---HONumber=July15_HO3-->