<properties 
   pageTitle="Wiederherstellen einer Azure SQL-Datenbank mithilfe von Geo-Restore und REST-API" 
   description="Geo-Restore, Microsoft Azure SQL-Datenbank, Datenbank wiederherstellen, REST-API" 
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
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# Wiederherstellen einer Azure SQL-Datenbank mithilfe von Geo-Restore und REST-API

> [AZURE.SELECTOR]
- [Geo-Restore - Portal](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [Geo-Restore - PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-powershell/)

## Übersicht

In diese Anleitung wird gezeigt, wie Sie eine Azure SQL-Datenbank mithilfe der REST-API wiederherstellen. Links zu ausführlicheren Vorgängen werden bereitgestellt. Geo-Restore ist die Hauptschutzfunktion für Notfallwiederherstellung, die auf allen Azure SQL-Datenbankdienstebenen (Basic, Standard und Premium) geboten wird.

## Einschränkungen und Sicherheit

Siehe [Wiederherstellen einer Azure SQL-Datenbank mit Geo-Restore im Azure-Portal](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/).

## Vorgehensweise: Wiederherstellen einer Azure SQL-Datenbank mithilfe der REST-API

1.	Rufen Sie die Liste der wiederherstellbaren Datenbanken mithilfe des Vorgangs [Wiederherstellbare Datenbanken auflisten](http://msdn.microsoft.com/library/azure/dn800984.aspx) ab.
	
2.	Rufen Sie die Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Wiederherstellbare Datenbank abrufen](http://msdn.microsoft.com/library/azure/dn800985.aspx) ab.
	
3.	Erstellen Sie die Wiederherstellungsanforderung mithilfe des Vorgangs [Datenbankwiederherstellungsanforderung erstellen](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Datenbank-Betriebsstatus](http://msdn.microsoft.com/library/azure/dn720371.aspx) nach.

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

[Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung im Azure-Portal](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Wiederherstellen einer gelöschten Azure SQL-Datenbank im Azure-Portal](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Azure SQL-Datenbank-Geschäftskontinuität](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sichern und Wiederherstellen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL-Datenbank - Geo-Restore (Blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Referenz zur REST-API der Dienstverwaltung](https://msdn.microsoft.com/library/azure/ee460799.aspx)

<!--HONumber=47-->
 