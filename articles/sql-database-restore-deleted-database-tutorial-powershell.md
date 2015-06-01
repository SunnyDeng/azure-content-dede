<properties 
   pageTitle="Wiederherstellen einer gelöschten Azure SQL-Datenbank in Azure PowerShell" 
   description="Microsoft Azure SQL-Datenbank, gelöschte Datenbank wiederherstellen, Azure PowerShell" 
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
   ms.author="elfish; v-romcal; v-stste"/>

# Wiederherstellen einer gelöschten Azure SQL-Datenbank in Azure PowerShell

> [AZURE.SELECTOR]
- [Wiederherstellen einer gelöschten Datenbank - Portal](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)
- [Wiederherstellen einer gelöschten Datenbank - REST-API](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-rest/)

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie in [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) eine gelöschte Azure SQL-Datenbank wiederherstellen. Sie können eine Datenbank, die während ihres Aufbewahrungszeitraums gelöscht wurde, zu dem Zeitpunkt wiederherstellen, an dem sie gelöscht wurde. Die Beibehaltungsdauer wird von der Dienstebene der Datenbank bestimmt.

Beim Wiederherstellen einer gelöschten Azure SQL-Datenbank wird eine neue Datenbank erstellt. Der Dienst wählt die Dienstebene basierend auf der beim Wiederherstellungspunkt verwendeten Sicherung automatisch aus. Stellen Sie sicher, dass Ihnen auf dem logischen Server ein Kontingent zum Erstellen einer weiteren Datenbank zur Verfügung steht. Wenn Sie ein erhöhtes Kontingent anfordern möchten, wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/).

## Einschränkungen und Sicherheit

Siehe [Wiederherstellen einer gelöschten Azure SQL-Datenbank im Azure-Portal](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/).

## Vorgehensweise: Wiederherstellen einer gelöschten Azure SQL-Datenbank in Azure PowerShell

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-Deleted-SQL-Database-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Sie müssen zum Ausführen der folgenden Cmdlets die zertifikatbasierte Authentifizierung verwenden. Weitere Informationen finden Sie unter  *Verwenden der Zertifikatmethode* im Abschnitt [Installieren und Konfigurieren von Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method).

1. Rufen Sie die Liste der wiederherstellbaren Datenbanken mithilfe des Cmdlets [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) ab.
	* Verwenden Sie den Schalter **RestorableDropped**, und geben Sie den **ServerName** des Servers an, von dem die Datenbank gelöscht wurde.
	* Mit dem folgenden Befehl werden die Ergebnisse in einer Variablen namens **$RecoverableDBs** speichert.
	
	`PS C:>$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" -RestorableDropped`

2. Wählen Sie in der Liste der gelöschten Datenbanken die gelöschte Datenbank aus, die Sie wiederherstellen möchten.

	* Geben Sie die Nummer der gelöschten Datenbank mithilfe der Liste **$RecoverableDBs** ein.  

	`PS C:>$Database = $RecoverableDBs[<deleted database number>]`

	* Weitere Informationen zum Abrufen eines wiederherstellbaren gelöschten Datenbankobjekts finden Sie unter [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx).

3. Beginnen Sie die Wiederherstellung mithilfe des Cmdlets [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Geben Sie die folgenden Parameter an:	
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName**: Name der Zieldatenbank, in die die Wiederherstellung erfolgen soll.

	Speichern Sie die Rückgabe in einer Variablen namens **$RestoreRequest**. Diese Variable enthält die Wiederherstellungsanforderungs-ID zum Überwachen des Status einer Wiederherstellung.
	
	`PS C:>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database -TargetDatabaseName "myrestoredDB"`

Eine Wiederherstellung kann einige Zeit in Anspruch nehmen. Verwenden Sie zum Überwachen des Status der Wiederherstellung das Cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx), und geben Sie die folgenden Parameter an:

* **ServerName**: Name des Servers, auf dem die Datenbank wiederhergestellt werden soll.
* **OperationGuid**: GUID des Vorgangs, wobei es sich um die Wiederherstellungsanforderungs-ID handelt, die in Schritt 3 in der Variablen **$RecoveryRequest** gespeichert wurde.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

Die Felder **Status** und **PercentComplete** zeigen den Status der Wiederherstellung.

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

[Azure SQL-Datenbank-Geschäftskontinuität](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Sichern und Wiederherstellen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)
<!--HONumber=47-->
