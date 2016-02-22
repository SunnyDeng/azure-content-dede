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
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall

Azure SQL-Datenbank bietet die folgenden Funktionen zur Wiederherstellung nach einem Ausfall:

- Aktive Georeplikation [(Blog, in englischer Sprache)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- Standardmäßige Georeplikation [(Blog, in englischer Sprache)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- Geografische Wiederherstellung [(Blog, in englischer Sprache)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- Neue Möglichkeiten der Georeplikation [(Blog)](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)

Informationen zur Vorbereitung auf Notfälle und zur Wiederherstellung der Datenbanken finden Sie auf der Seite [Entwurf für Geschäftskontinuität](sql-database-business-continuity-design.md).

##Startzeitpunkt für die Wiederherstellung 

Der Wiederherstellungsvorgang wirkt sich auf die Anwendung aus. Er erfordert eine Änderung der SQL-Verbindungszeichenfolge und kann zu dauerhaftem Datenverlust führen. Er sollte daher nur ausgeführt werden, wenn der Ausfall wahrscheinlich länger als die Anwendungs-RTO dauert. Wenn die Anwendung in der Produktionsumgebung bereitgestellt wird, sollten Sie regelmäßige Überwachungen der Anwendungsintegrität durchführen und die folgenden Datenpunkte verwenden, um zu bestätigen, dass die Wiederherstellung erforderlich ist:

1. Permanente Verbindungsfehler zwischen Anwendungsebene und Datenbank.
2. Das Azure-Portal zeigt eine Warnung zu einem Vorfall in der Region mit weit reichenden Auswirkungen.

> [AZURE.NOTE] Nachdem die Wiederherstellung Ihrer Datenbank abgeschlossen ist, können Sie sie für die Verwendung konfigurieren. Befolgen Sie hierzu die Anleitung [Konfigurieren einer Datenbank nach der Wiederherstellung](#postrecovery).

## Failover auf eine georeplizierte sekundäre Datenbank
> [AZURE.NOTE] Sie müssen eine sekundäre Datenbank für das Failover konfigurieren. Die Georeplikation ist nur für Standard- und Premium-Datenbanken verfügbar. Hier finden Sie Informationen zum [Konfigurieren der Georeplikation](sql-database-business-continuity-design.md).

###Azure-Portal
Verwenden Sie das Azure-Portal, um die fortlaufende Kopierbeziehung mit der georeplizierten sekundären Datenbank zu beenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **DURCHSUCHEN** und dann **SQL-Datenbanken** aus.
3. Navigieren Sie zu Ihrer Datenbank, und wählen Sie sie aus. 
4. Wählen Sie am unteren Rand des Datenbankblatts die **Geo Replication map** aus.
4. Klicken Sie unter **Sekundärzonen** mit der rechten Maustaste auf die Zeile mit dem Namen der Datenbank, die Sie wiederherstellen möchten, und wählen Sie **Failover** aus.

###PowerShell
Verwenden Sie PowerShell zum Initiieren des Failovers auf eine georeplizierte sekundäre Datenbank mithilfe des Cmdlets [Set-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###REST-API 
Verwenden Sie REST zum programmgesteuerten Initiieren des Failovers auf eine sekundäre Datenbank.

1. Eine Replikationsverknüpfung mit einer sekundären Datenbank kann mithilfe des Vorgangs [Replikationsverknüpfung abrufen](https://msdn.microsoft.com/library/mt600778.aspx) abgerufen werden.
2. Führen Sie mithilfe von [Sekundäre Datenbank als primäre Datenbank festlegen](https://msdn.microsoft.com/library/mt582027.aspx) ein Failover auf die sekundäre Datenbank mit möglichem Datenverlust aus. 

## Wiederherstellen mit geografischer Wiederherstellung

Bei einem Ausfall einer Datenbank können Sie diese aus der aktuellen geografisch redundanten Sicherung mit der geografischen Wiederherstellung wiederherstellen.

> [AZURE.NOTE] Beim Wiederherstellen einer Datenbank wird eine neue Datenbank erstellt. Es muss sichergestellt werden, dass der Server, auf dem die Wiederherstellung erfolgt, über ausreichend DTU-Kapazität für die neue Datenbank verfügt. [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/), um dieses Kontingent zu erhöhen.

###Azure-Portal (Wiederherstellung in eine eigenständige Datenbank)
Führen Sie die folgenden Schritte aus, um eine SQL-Datenbank mithilfe der geografischen Wiederherstellung im Azure-Portal wiederherzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **NEU**, dann **Daten und Speicher** und dann **SQL-Datenbank** aus.
2. Wählen Sie **BACKUP** als Quelle aus und dann die geografisch redundante Sicherung aus der wiederhergestellt werden soll.
3. Geben Sie die restlichen Datenbankeigenschaften an, und klicken Sie auf **Erstellen**.
4. Der Datenbank-Wiederherstellungsvorgang beginnt und kann mithilfe von **BENACHRICHTIGUNGEN** auf der linken Bildschirmseite überwacht werden.

###Azure-Portal (Wiederherstellung in einen Pool für elastische Datenbanken)
Führen Sie zum Wiederherstellen einer SQL-Datenbank mit der Geowiederherstellung in einen Pool für elastische Datenbanken über das Portal die folgenden Anweisungen aus.

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **Durchsuchen** und dann **Elastische SQL-Pools** aus.
3. Wählen Sie den Pool, in den die Geowiederherstellung der Datenbank durchgeführt werden soll.
4. Wählen Sie oben auf dem Blatt für den elastischen Pool **Datenbank erstellen** aus.
5. Wählen Sie **BACKUP** als Quelle aus und dann die geografisch redundante Sicherung aus der wiederhergestellt werden soll.
6. Geben Sie die restlichen Datenbankeigenschaften an, und klicken Sie auf **Erstellen**.
7. Der Datenbank-Wiederherstellungsvorgang beginnt und kann mithilfe von **BENACHRICHTIGUNGEN** auf der linken Bildschirmseite überwacht werden.

###PowerShell 
> [AZURE.NOTE] Derzeit wird mit PowerShell nur die Geowiederherstellung in eine eigenständige Datenbank unterstützt. Verwenden Sie für die Geowiederherstellung in einen Pool für elastische Datenbanken das [Azure-Portal](https://portal.Azure.com).

Um eine SQL-Datenbank mithilfe der geografischen Wiederherstellung unter Verwendung von PowerShell wiederherzustellen, starten Sie eine Anforderung zur geografischen Wiederherstellung mit dem Cmdlet [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx).

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###REST-API 

Verwenden Sie REST für eine programmgesteuerte Durchführung der Datenbankwiederherstellung.

1.	Rufen Sie die Liste der wiederherstellbaren Datenbanken mithilfe des Vorgangs [List Recoverable Databases](http://msdn.microsoft.com/library/azure/dn800984.aspx) ab.
	
2.	Rufen Sie die Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Get Recoverable Database](http://msdn.microsoft.com/library/azure/dn800985.aspx) ab.
	
3.	Erstellen Sie die Wiederherstellungsanforderung mithilfe des Vorgangs [Create Database Recovery Request](http://msdn.microsoft.com/library/azure/dn800986.aspx).
	
4.	Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Database Operation Status](http://msdn.microsoft.com/library/azure/dn720371.aspx) nach.
 
## Konfigurieren der Datenbank nach der Wiederherstellung<a name="postrecovery"></a>

Hier finden Sie eine Checkliste mit Aufgaben, mit denen Sie die wiederhergestellte Datenbank für die Produktion vorbereiten können.

### Aktualisieren von Verbindungszeichenfolgen

Überprüfen Sie, ob die Verbindungszeichenfolgen Ihrer Anwendung auf die neu wiederhergestellte Datenbank verweisen. Aktualisieren Sie die Verbindungszeichenfolgen, wenn eine der folgenden Situationen zutrifft:

  + Die wiederhergestellte Datenbank verwendet einen anderen Namen als den Namen der Quelldatenbank
  + Die wiederhergestellte Datenbank befindet sich auf einem anderen Server als dem Quellserver

Weitere Informationen zum Ändern von Verbindungszeichenfolgen finden Sie unter [Verbindungen mit der Azure SQL-Datenbank: Zentrale Empfehlungen](sql-database-connect-central-recommendations.md).
 
### Ändern von Firewallregeln
Überprüfen Sie die Firewallregeln auf Server- und Datenbankebene, und stellen Sie sicher, dass Verbindungen von Ihrem Clientcomputer oder Azure zum Server und der neu wiederhergestellten Datenbank aktiviert sind. Weitere Informationen finden Sie unter [Vorgehensweise: Konfigurieren von Firewalleinstellungen (Azure SQL-Datenbank)](sql-database-configure-firewall-settings.md).

### Überprüfen von Server-Anmeldungen und Datenbankbenutzern

Überprüfen Sie, ob alle von der Anwendung verwendeten Anmeldungen auf dem Server vorhanden sind, der die wiederhergestellte Datenbank hostet. Erstellen Sie die fehlenden Anmeldungen erneut, und gewähren Sie ihnen entsprechende Berechtigungen auf der wiederhergestellten Datenbank. Weitere Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in der Azure SQL-Datenbank](sql-database-manage-logins.md).

Überprüfen Sie, ob jedem Datenbankbenutzer in der wiederhergestellten Datenbank eine gültige Server-Anmeldung zugeordnet ist. Verwenden Sie die ALTER USER-Anweisung, um einem Benutzer eine gültige Server-Anmeldung zuzuordnen. Weitere Informationen finden Sie unter [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486).


### Einrichten von Telemetrie-Warnungen

Überprüfen Sie, ob die vorhandenen Warnungsregel-Einstellungen der wiederhergestellten Datenbank zugeordnet sind. Aktualisieren Sie die Einstellung, wenn eine der folgenden Situationen zutrifft:

  + Die wiederhergestellte Datenbank verwendet einen anderen Namen als den Namen der Quelldatenbank
  + Die wiederhergestellte Datenbank befindet sich auf einem anderen Server als dem Quellserver

Weitere Informationen zu Datenbankwarnungsregeln finden Sie unter [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md) und [Nachverfolgen der Dienstintegrität](insights-service-health.md).


### Aktivieren der Überwachung

Wenn für den Zugriff auf die Datenbank Überwachung erforderlich ist, müssen Sie nach der Wiederherstellung der Datenbank die Überwachung aktivieren. Es ist ein guter Indikator für die Notwendigkeit von Überwachung, wenn Clientanwendungen sichere Verbindungszeichenfolgen in einem Muster von *.database.secure.windows.net verwenden. Weitere Informationen finden Sie unter [Erste Schritte mit der SQL-Datenbanküberwachung](sql-database-auditing-get-started.md).

<!---HONumber=AcomDC_0211_2016-->