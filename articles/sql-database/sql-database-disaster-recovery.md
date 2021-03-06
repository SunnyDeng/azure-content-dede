<properties 
   pageTitle="Notfallwiederherstellung einer SQL-Datenbank" 
   description="Hier erhalten Sie Informationen zum Wiederherstellen einer Datenbank nach Störungen in einem regionalen Rechenzentrum oder dessen Ausfall mit den Funktionen zur aktiven Georeplikation, zur standardmäßigen Georeplikation und zur geografischen Wiederherstellung von Azure SQL-Datenbank." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="10/20/2015"
   ms.author="elfish"/>

# Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall

Azure SQL-Datenbank bietet die folgenden Funktionen zur Wiederherstellung nach einem Ausfall:

- Aktive Georeplikation [(Blog, in englischer Sprache)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Standardmäßige Georeplikation [(Blog, in englischer Sprache)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Geografische Wiederherstellung [(Blog, in englischer Sprache)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

Informationen zur Vorbereitung auf Notfälle und zur Wiederherstellung der Datenbanken finden Sie auf der Seite [Entwurf für Geschäftskontinuität](sql-database-business-continuity-design.md).

##Startzeitpunkt für die Wiederherstellung 

Der Wiederherstellungsvorgang wirkt sich auf die Anwendung aus. Er erfordert eine Änderung der SQL-Verbindungszeichenfolge und kann zu dauerhaftem Datenverlust führen. Er sollte daher nur ausgeführt werden, wenn der Ausfall wahrscheinlich länger als die Anwendungs-RTO dauert. Wenn die Anwendung in der Produktionsumgebung bereitgestellt wird, sollten Sie regelmäßige Überwachungen der Anwendungsintegrität durchführen und die folgenden Datenpunkte verwenden, um zu bestätigen, dass die Wiederherstellung erforderlich ist:

1. Permanente Verbindungsfehler zwischen Anwendungsebene und Datenbank.
2. Das Azure-Portal zeigt eine Warnung zu einem Vorfall in der Region mit weit reichenden Auswirkungen.

## Failover zu einer geografisch replizierten sekundären Datenbank
> [AZURE.NOTE]Sie müssen die [standardmäßige Georeplikation](https://msdn.microsoft.com/library/azure/dn758204.aspx) oder die [aktive Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) für eine sekundäre Datenbank für Failover konfigurieren. Die Georeplikation ist nur für Standard- und Premium-Datenbanken verfügbar.

Bei einem Ausfall der primären Datenbank können Sie ein Failover zu einer sekundären Datenbank zum Wiederherstellen der Verfügbarkeit durchführen. Sie müssen dazu das Beenden der fortlaufenden Kopierbeziehung erzwingen. Eine vollständige Beschreibung der Beendigung einer fortlaufenden Kopierbeziehung finden Sie [hier](https://msdn.microsoft.com/library/azure/dn741323.aspx).

###Azure-Portal
Verwenden Sie das Azure-Portal, um die fortlaufende Kopierbeziehung mit der georeplizierten sekundären Datenbank zu beenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **DURCHSUCHEN** und dann **SQL-Datenbanken** aus.
3. Navigieren Sie zu Ihrer Datenbank, und wählen Sie sie aus. 
4. Wählen Sie am unteren Rand des Datenbankblatts die **Geo Replication map** aus.
4. Klicken Sie unter **Sekundärzonen** mit der rechten Maustaste auf die Zeile mit dem Namen der Datenbank, die Sie wiederherstellen möchten, und wählen Sie **Beenden** aus.

Nach dem Beenden der fortlaufenden Kopierbeziehung können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md).

###PowerShell
Verwenden Sie PowerShell, um die fortlaufende Kopierbeziehung mit der georeplizierten sekundären Datenbank mithilfe des Cmdlets [Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223) zu beenden.
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
Nach dem Beenden der fortlaufenden Kopierbeziehung können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md).

###REST-API 
Verwenden Sie die REST-API, um die fortlaufende Kopierbeziehung mit der georeplizierten sekundären Datenbank programmgesteuert zu beenden.

1. Rufen Sie die fortlaufende Kopie für die Datenbank mit dem Vorgang [Get Database Copy](https://msdn.microsoft.com/library/azure/dn509570.aspx) ab.
2. Beenden Sie die fortlaufende Kopie für die Datenbank mit dem Vorgang [Stop Database Copy](https://msdn.microsoft.com/library/azure/dn509573.aspx). Verwenden Sie den sekundären Servernamen und Datenbanknamen im Anforderungs-URI von "Stop Database Copy".

 Nach dem Beenden der fortlaufenden Kopierbeziehung können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md).

## Wiederherstellen mit geografischer Wiederherstellung

Bei einem Ausfall einer Datenbank können Sie diese aus der aktuellen geografisch redundanten Sicherung mit der geografischen Wiederherstellung wiederherstellen.

> [AZURE.NOTE]Beim Wiederherstellen einer Datenbank wird eine neue Datenbank erstellt. Es muss sichergestellt werden, dass der Server, auf dem die Wiederherstellung erfolgt, über ausreichend DTU-Kapazität für die neue Datenbank verfügt. [Wenden Sie sich an den Support](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/), um dieses Kontingent zu erhöhen.

###Azure-Portal
Führen Sie die folgenden Schritte aus, um eine SQL-Datenbank mithilfe der geografischen Wiederherstellung im Azure-Portal wiederherzustellen. Zudem können [Sie sich dieses Verfahren in einem Video ansehen](https://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore/).

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **NEU**, dann **Daten und Speicher** und dann **SQL-Datenbank** aus.
2. Wählen Sie **BACKUP** als Quelle aus und dann die geografisch redundante Sicherung aus der wiederhergestellt werden soll.
3. Geben Sie die restlichen Datenbankeigenschaften an, und klicken Sie auf **Erstellen**.
4. Der Datenbank-Wiederherstellungsvorgang beginnt und kann mithilfe von **BENACHRICHTIGUNGEN** auf der linken Bildschirmseite überwacht werden.

Nachdem die Wiederherstellung abgeschlossen ist, können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md).

###PowerShell 
Um eine SQL-Datenbank mithilfe der geografischen Wiederherstellung unter Verwendung von PowerShell wiederherzustellen, starten Sie eine Anforderung zur geografischen Wiederherstellung mit dem Cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx). Ausführliche Anleitungen [finden Sie im Video zu diesem Verfahren](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

Nachdem die Wiederherstellung abgeschlossen ist, können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md).

###REST-API 

Verwenden Sie REST für eine programmgesteuerte Durchführung der Datenbankwiederherstellung.

1.	Rufen Sie die Liste der wiederherstellbaren Datenbanken mithilfe des Vorgangs [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx) ab.
	
2.	Rufen Sie die Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx) ab.
	
3.	Erstellen Sie die Wiederherstellungsanforderung mithilfe des Vorgangs [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx) nach.

Nachdem die Wiederherstellung abgeschlossen ist, können Sie die wiederhergestellte Datenbank für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md).
 

<!---HONumber=Oct15_HO4-->