<properties 
   pageTitle="Wiederherstellen einer Azure SQL-Datenbank mit Geo-Restore in Azure PowerShell" 
   description="Geo-Restore, Microsoft Azure SQL-Datenbank, Datenbank wiederherstellen, Azure PowerShell" 
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
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Wiederherstellen einer Azure SQL-Datenbank mit Geo-Restore in Azure PowerShell

> [AZURE.SELECTOR]
- [Geo-Restore - portal](sql-database-geo-restore-tutorial-management-portal.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie eine Azure SQL-Datenbank mit Geo-Restore in [Azure PowerShell](../powershell-install-configure.md) wiederherstellen. Geo-Restore ist die Hauptschutzfunktion für Notfallwiederherstellung, die auf allen Azure SQL-Datenbankdienstebenen (Basic, Standard und Premium) geboten wird.

## Einschränkungen und Sicherheit

Siehe [Wiederherstellen einer Azure SQL-Datenbank mit Geo-Restore im Azure-Portal](sql-database-geo-restore-tutorial-management-portal.md).

## Vorgehensweise: Wiederherstellen einer Azure SQL-Datenbank mit Geo-Restore in Azure PowerShell

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell]

Sie müssen zum Ausführen der folgenden Cmdlets die zertifikatbasierte Authentifizierung verwenden. Weitere Informationen finden Sie im Abschnitt *Verwenden der Zertifikatmethode* unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md#use-the-certificate-method).

1. Rufen Sie die Liste der wiederherstellbaren Datenbanken mithilfe des Cmdlets [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) ab. Geben Sie den folgenden Parameter an:
	* **ServerName**: Der Server, auf dem sich die Datenbank befindet.	

	`PS C:>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. Wählen Sie die Datenbank, die Sie wiederherstellen möchten, mithilfe des Cmdlets [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) aus. Geben Sie die folgenden Parameter an:
	* **ServerName**: Der Server, auf dem sich die Datenbank befindet.
	* **DatabaseName**: Name, der Datenbank, die wiederhergestellt werden soll.

	`PS C:>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" –DatabaseName “mydb”`
	 
3. Beginnen Sie die Wiederherstellung mithilfe des Cmdlets[Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx). Geben Sie die folgenden Parameter an:
	* **SourceDatabase**: Name der Datenbank, die Sie wiederherstellen möchten.
	* **TargetDatabaseName**: Name der Datenbank, in die die Wiederherstellung erfolgt.
	* **TargetServerName**: Name des Zielservers, auf dem Sie die Datenbank wiederherstellen möchten.

	Speichern Sie die Rückgabe in einer Variablen namens **$RestoreRequest**. Diese Variable enthält die Wiederherstellungsanforderungs-ID zum Überwachen des Status einer Wiederherstellung.

	`PS C:>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “myrecoveredDB” –TargetServerName “mytargetserver”`
	
Eine Datenbankwiederherstellung kann einige Zeit in Anspruch nehmen. Verwenden Sie zum Überwachen des Status der Wiederherstellung das Cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx), und geben Sie die folgenden Parameter an:

* **ServerName**: Name des Servers, auf dem die Datenbank wiederhergestellt werden soll.
* **OperationGuid**: GUID des Vorgangs, wobei es sich um die Wiederherstellungsanforderungs-ID handelt, die in Schritt 3 in der Variablen **$RecoveryRequest** gespeichert wurde.

	`PS C:>Get-AzureSqlDatabaseOperation –ServerName “mytargetserver” –OperationGuid $RecoveryRequest.ID`

Die Felder **Status** und **ProzentAbgeschlossen** zeigen den Status der Wiederherstellung.

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

[Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung im Azure-Portal](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Wiederherstellen einer gelöschten Azure SQL-Datenbank im Azure-Portal](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Geschäftskontinuität in Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sichern und Wiederherstellen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL-Datenbank – Geo-Restore (Blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=July15_HO3-->