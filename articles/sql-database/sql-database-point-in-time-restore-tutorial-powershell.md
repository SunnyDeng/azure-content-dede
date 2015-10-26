<properties 
   pageTitle="Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung in Azure PowerShell" 
   description="Zeitpunktwiederherstellung, Microsoft Azure SQL-Datenbank, Datenbank wiederherstellen, Azure PowerShell" 
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
   ms.date="10/08/2015"
   ms.author="elfish; v-romcal; sstein"/>

# Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung in Azure PowerShell

> [AZURE.SELECTOR]
- [Point in Time Restore - portal](sql-database-point-in-time-restore-tutorial-management-portal.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md) 

## Übersicht

In diesem Lernprogramm wird gezeigt, wie Sie eine Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung in [Azure PowerShell](../powershell-install-configure.md) wiederherstellen. Azure SQL-Datenbank verfügt über integrierte Sicherungen zur Unterstützung einer eigenständigen Zeitpunktwiederherstellung für die Dienstebenen Basic, Standard und Premium.

Bei einer Zeitpunktwiederherstellung wird eine neue Datenbank erstellt. Der Dienst wählt die Dienstebene basierend auf der beim Wiederherstellungspunkt verwendeten Sicherung automatisch aus. Stellen Sie sicher, dass Ihnen auf dem logischen Server ein Kontingent zum Erstellen einer weiteren Datenbank zur Verfügung steht. Wenn Sie ein erhöhtes Kontingent anfordern möchten, wenden Sie sich an den [Azure-Support](http://azure.microsoft.com/support/options/).

## Einschränkungen und Sicherheit

Siehe [Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung im Azure-Portal](sql-database-point-in-time-restore-tutorial-management-portal.md).

## Vorgehensweise: Wiederherstellen einer Azure SQL-Datenbank mithilfe der Zeitpunktwiederherstellung in Azure PowerShell

> [AZURE.VIDEO restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell]

Sie müssen zum Ausführen der folgenden Cmdlets die zertifikatbasierte Authentifizierung verwenden. Weitere Informationen finden Sie im Abschnitt *Verwenden der Zertifikatmethode* unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md#use-the-certificate-method).

> [AZURE.IMPORTANT]Dieser Artikel enthält Befehle für Azure PowerShell-Versionen *vor* Version 1.0. Sie können Ihre Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen.

1. Rufen Sie die Datenbank, die Sie wiederherstellen möchten, mithilfe des Cmdlets [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) ab. Geben Sie die folgenden Parameter an:
	* **ServerName**: Der Server, auf dem sich die Datenbank befindet.
	* **DatabaseName**: Name der Datenbank, die Sie wiederherstellen möchten.	

	`$Database = Get-AzureSqlDatabase -ServerName "myserver" –DatabaseName “mydb”`

2. Beginnen Sie die Wiederherstellung mithilfe des Cmdlets [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx). Geben Sie die folgenden Parameter an:
	* **SourceDatabase**: Die Quelldatenbank, aus der die Wiederherstellung erfolgen soll.
	* **TargetDatabaseName**: Name der Zieldatenbank, in die die Wiederherstellung erfolgen soll.
	* **PointInTime**: Zeitpunkt, auf den sich die Wiederherstellung beziehen soll.

	Speichern Sie die Rückgabe in einer Variablen namens **$RestoreRequest**. Diese Variable enthält die Wiederherstellungsanforderungs-ID zum Überwachen des Status einer Wiederherstellung.

	`$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “myrestoredDB” –PointInTime “2015-01-01 06:00:00”`

Eine Wiederherstellung kann einige Zeit in Anspruch nehmen. Verwenden Sie zum Überwachen des Status der Wiederherstellung das Cmdlet [Get-AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx), und geben Sie die folgenden Parameter an:

* **ServerName**: Name des Servers, auf dem die Datenbank wiederhergestellt werden soll.
* **OperationGuid**: GUID des Vorgangs, wobei es sich um die Wiederherstellungsanforderungs-ID handelt, die in Schritt 2 in der Variablen **$RestoreRequest** gespeichert wurde.

	`Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

Die Felder **Status** und **ProzentAbgeschlossen** zeigen den Status der Wiederherstellung.

## Nächste Schritte

Weitere Informationen finden Sie unter den folgenden Links:

[Geschäftskontinuität in Azure SQL-Datenbank](sql-database-business-continuity.md)

[Azure SQL-Datenbank – Zeitpunktwiederherstellung (Blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
 

<!---HONumber=Oct15_HO3-->