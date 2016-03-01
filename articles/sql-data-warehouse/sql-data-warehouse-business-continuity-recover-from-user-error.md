<properties
   pageTitle="Wiederherstellen einer Datenbank nach einem Benutzerfehler in SQL Data Warehouse | Microsoft Azure"
   description="Schritte zum Wiederherstellen einer Datenbank nach einem Benutzerfehler in SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/17/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# Wiederherstellen einer Datenbank nach einem Benutzerfehler in SQL Data Warehouse

SQL Data Warehouse verfügt über zwei Hauptfunktionen für die Wiederherstellung nach einem Benutzerfehler, bei dem versehentlich Daten beschädigt oder gelöscht werden:

- Wiederherstellen einer Livedatenbank
- Wiederherstellen einer gelöschten Datenbank

Bei beiden Funktionen wird eine neue Datenbank auf demselben Server wiederhergestellt.

Es gibt zwei verschiedene APIs, die eine Wiederherstellung der SQL Data Warehouse-Datenbank unterstützen: Azure PowerShell und REST-API. Sie können beide für den Zugriff auf die Wiederherstellungsfunktion von SQL Data Warehouse verwenden.

## Wiederherstellen einer Livedatenbank
Wenn es durch einen Benutzerfehler zu einer unbeabsichtigten Änderung von Daten kommt, können Sie für die Datenbank alle Wiederherstellungspunkte innerhalb der Aufbewahrungsdauer wiederherstellen. Die Datenbankmomentaufnahmen für eine Livedatenbank werden mindestens alle acht Stunden erstellt und sieben Tage lang aufbewahrt.

### PowerShell

Verwenden Sie Azure PowerShell, um eine Datenbankwiederherstellung programmgesteuert auszuführen. Führen Sie zum Herunterladen des Azure PowerShell-Moduls den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus.

Zur Wiederherstellung einer Datenbank verwenden Sie das [Start-AzureSqlDatabaseRestore][]-Cmdlet.

1. Öffnen Sie Microsoft Azure PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
4. Listen Sie die Wiederherstellungspunkte für die Datenbank auf (erfordert den Azure-Ressourcenverwaltungsmodus).
5. Wählen Sie den gewünschten Wiederherstellungspunkt mit RestorePointCreationDate aus.
6. Stellen Sie die Datenbank mit dem gewünschten Wiederherstellungspunkt wieder her.
7. Überwachen Sie den Fortschritt der Wiederherstellung.

```

Add-AzureAccount
Get-AzureSubscription
Select-AzureSubscription -SubscriptionName "<Subscription_name>"

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Hinweis: Wenn Ihr Server „foo.database.windows.net“ heißt, verwenden Sie in den obigen PowerShell-Cmdlets für „-ServerName“ den Namen „foo“.

### REST-API
Verwenden Sie REST für eine programmatische Durchführung der Datenbankwiederherstellung.

1. Rufen Sie die Liste mit den Wiederherstellungspunkten für die Datenbank ab, indem Sie den Get Database Restore Points-Vorgang verwenden.
2. Beginnen Sie die Wiederherstellung mit dem Vorgang [Datenbankwiederherstellungsanforderung erstellen][].
3. Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Datenbank-Betriebsstatus][] nach.

Nachdem die Wiederherstellung abgeschlossen ist, können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank][].

## Wiederherstellen einer gelöschten Datenbank
Wenn eine Datenbank gelöscht wurde, können Sie den Zustand der gelöschten Datenbank zum Löschzeitpunkt wiederherstellen. Azure SQL Data Warehouse erstellt eine Datenbankmomentaufnahme, bevor die Datenbank verworfen wird, und bewahrt sie sieben Tage lang auf.

### PowerShell
Verwenden Sie Azure PowerShell für eine programmgesteuerte Wiederherstellung einer gelöschten Datenbank. Führen Sie zum Herunterladen des Azure PowerShell-Moduls den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus.

Zur Wiederherstellung einer gelöschten Datenbank mithilfe der Zeitpunktwiederherstellung verwenden Sie das Cmdlet [Start-AzureSqlDatabaseRestore][].

1. Öffnen Sie Microsoft Azure PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, das die gelöschte Datenbank enthält, die wiederhergestellt werden soll.
4. Suchen Sie in der Liste der gelöschten Datenbanken die Datenbank und das zugehörige Löschdatum.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. Rufen Sie die gelöschte Datenbank ab, und starten Sie die Wiederherstellung.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

Hinweis: Wenn Ihr Server „foo.database.windows.net“ heißt, verwenden Sie in den obigen PowerShell-Cmdlets für „-ServerName“ den Namen „foo“.

### REST-API
Verwenden Sie REST für eine programmatische Durchführung der Datenbankwiederherstellung.

1.	Listen Sie alle gelöschten wiederherstellbaren Datenbanken mithilfe des Vorgangs [Wiederherstellbare gelöschte Datenbanken auflisten][] auf.
2.	Rufen Sie die Details der gelöschten Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Wiederherstellbare gelöschte Datenbank abrufen][] ab.
3.	Beginnen Sie die Wiederherstellung mit dem Vorgang [Datenbankwiederherstellungsanforderung erstellen][].
4.	Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Datenbank-Betriebsstatus][] nach.

Nachdem die Wiederherstellung abgeschlossen ist, können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank][].


## Nächste Schritte
Informationen zu den Geschäftskontinuitätsfeatures anderer Azure SQL-Datenbank-Editionen finden Sie in der [Azure SQL-Datenbank-Übersicht zur Geschäftskontinuität][].


<!--Image references-->

<!--Article references-->
[Azure SQL-Datenbank-Übersicht zur Geschäftskontinuität]: sql-database/sql-database-business-continuity.md
[Abschließen einer wiederhergestellten Datenbank]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Datenbankwiederherstellungsanforderung erstellen]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[Datenbank-Betriebsstatus]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[Wiederherstellbare gelöschte Datenbank abrufen]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[Wiederherstellbare gelöschte Datenbanken auflisten]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0218_2016-->