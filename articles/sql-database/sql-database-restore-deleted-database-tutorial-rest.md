<properties 
   pageTitle="Wiederherstellen einer gelöschten Azure SQL-Datenbank mit der REST-API" 
   description="Microsoft Azure SQL-Datenbank, gelöschte Datenbank wiederherstellen, REST-API" 
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

# Wiederherstellen einer gelöschten Azure SQL-Datenbank mit der REST-API

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md) 

## Übersicht

In diese Anleitung wird gezeigt, wie Sie eine gelöschte Azure SQL-Datenbank mithilfe der REST-API wiederherstellen. Links zu ausführlicheren Vorgängen werden bereitgestellt.

Beim Wiederherstellen einer gelöschten Azure SQL-Datenbank wird eine neue Datenbank erstellt. Der Dienst wählt die Dienstebene basierend auf der beim Wiederherstellungspunkt verwendeten Sicherung automatisch aus. Stellen Sie sicher, dass Ihnen auf dem logischen Server ein Kontingent zum Erstellen einer weiteren Datenbank zur Verfügung steht. Wenn Sie ein erhöhtes Kontingent anfordern möchten, wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/).

## Einschränkungen und Sicherheit

Siehe [Wiederherstellen einer gelöschten Azure SQL-Datenbank im Azure-Portal](sql-database-restore-deleted-database-tutorial-management-portal.md).

## Vorgehensweise: Wiederherstellen einer gelöschten Azure SQL-Datenbank mithilfe der REST-API

1.	Listen Sie alle gelöschten wiederherstellbaren Datenbanken mithilfe des Vorgangs [Wiederherstellbare gelöschte Datenbanken auflisten](http://msdn.microsoft.com/library/azure/dn509562.aspx) auf.
	
2.	Rufen Sie die Details der gelöschten Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Wiederherstellbare gelöschte Datenbank abrufen](http://msdn.microsoft.com/library/azure/dn509574.aspx) ab.

3.	Beginnen Sie die Wiederherstellung mit dem Vorgang [Datenbankwiederherstellungsanforderung erstellen](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Datenbank-Betriebsstatus](http://msdn.microsoft.com/library/azure/dn720371.aspx) nach.

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

[Geschäftskontinuität in Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sichern und Wiederherstellen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Referenz zur REST-API der Dienstverwaltung](http://msdn.microsoft.com/library/azure/ee460799.aspx)

<!---HONumber=July15_HO3-->