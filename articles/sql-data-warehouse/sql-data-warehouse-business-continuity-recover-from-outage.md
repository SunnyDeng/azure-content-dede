<properties
   pageTitle="Wiederherstellen einer Datenbank nach einem Ausfall in SQL Data Warehouse | Microsoft Azure"
   description="Schritte zum Wiederherstellen einer Datenbank nach einem Ausfall in SQL Data Warehouse"
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
   ms.date="03/09/2016"
   ms.author="sahajs;barbkess"/>

# Wiederherstellen einer Datenbank nach einem Ausfall in SQL Data Warehouse

Die Geowiederherstellung bietet die Möglichkeit der Wiederherstellung einer Datenbank aus einer georedundanten Sicherung, um eine neue Datenbank zu erstellen. Die Datenbank kann auf einem beliebigen Server in jeder Azure-Region erstellt werden. Da die Quelle eine georedundante Sicherung ist, kann mit ihr eine Datenbank selbst dann wiederhergestellt werden, wenn sie aufgrund eines Ausfalls nicht mehr verfügbar ist. Neben der Wiederherstellung nach einem Ausfall kann die Geowiederherstellung auch für andere Szenarien verwendet werden, beispielsweise zum Migrieren oder Kopieren der Datenbank auf einen anderen Server oder in eine andere Region.


## Startzeitpunkt für die Wiederherstellung
Der Wiederherstellungsvorgang erfordert eine Änderung der SQL-Verbindungszeichenfolge nach der Wiederherstellung und kann zu dauerhaftem Datenverlust führen. Er sollte daher nur ausgeführt werden, wenn der Ausfall wahrscheinlich länger als die Anwendungs-RTO dauert. Verwenden Sie die folgenden Datenpunkte, um zu bestätigen, dass die Wiederherstellung erforderlich ist:

- Permanente Verbindungsfehler mit der Datenbank.
- Das Azure-Portal zeigt eine Warnung zu einem Vorfall in der Region mit weit reichenden Auswirkungen.


## Wiederherstellen mit geografischer Wiederherstellung
Durch das Wiederherstellen einer Datenbank wird eine neue Datenbank aus der neuesten georedundanten Sicherung erstellt. Es muss sichergestellt werden, dass der Server, auf dem die Wiederherstellung erfolgt, über ausreichend DTU-Kapazität für die neue Datenbank verfügt. [Wenden Sie sich an den Support][], um dieses Kontingent zu erhöhen.


### Azure-Portal
1. Melden Sie sich beim [Azure-Portal][] an.
2. Wählen Sie auf der linken Bildschirmseite **+NEU**, dann **Daten und Speicher** und dann **SQL Data Warehouse** aus.
3. Wählen Sie **SICHERUNG** als Quelle aus, und wählen Sie dann die georedundante Sicherung aus, die wiederhergestellt werden soll.
4. Geben Sie die restlichen Datenbankeigenschaften an, und klicken Sie auf **Erstellen**.
5. Der Datenbank-Wiederherstellungsvorgang beginnt und kann mithilfe von **BENACHRICHTIGUNGEN** überwacht werden.


### PowerShell
Verwenden Sie Azure PowerShell, um eine Datenbankwiederherstellung programmgesteuert auszuführen. Führen Sie zum Herunterladen des Azure PowerShell-Moduls den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) aus. Sie können die Version überprüfen, indem Sie „Get-Module -ListAvailable -Name Azure“ ausführen. Dieser Artikel basiert auf Microsoft Azure PowerShell Version 1.0.4.

Um eine Datenbank wiederherzustellen, verwenden Sie das Cmdlets [Start-AzureSqlDatabaseRecovery][].

1. Öffnen Sie Windows PowerShell.
2. Stellen Sie eine Verbindung mit Ihrem Azure-Konto her, und listen Sie alle Abonnements auf, die Ihrem Konto zugeordnet sind.
3. Wählen Sie das Abonnement aus, in dem die wiederherzustellende Datenbank enthalten ist.
4. Rufen Sie die Datenbank ab, die Sie wiederherstellen möchten.
5. Erstellen Sie die Wiederherstellungsanforderung für die Datenbank.
6. Überwachen Sie den Fortschritt der Wiederherstellung.

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$Database = Get-AzureRmSqlRecoverableDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Recover database
$RecoveryRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourSourceServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -TargetServerName "<YourTargetServerName>"

# Monitor progress of recovery operation
Get-AzureSqlDatabaseOperation -ServerName "<YourTargetServerName>" –OperationGuid $RecoveryRequest.RequestID

```

Hinweis: Wenn Ihr Server „foo.database.windows.net“ heißt, verwenden Sie in den obigen PowerShell-Cmdlets für „-ServerName“ den Namen „foo“.

### REST-API
Verwenden Sie REST für eine programmgesteuerte Durchführung der Datenbankwiederherstellung.

1. Rufen Sie die Liste der wiederherstellbaren Datenbanken mithilfe des Vorgangs [List Recoverable Databases][] ab.
2. Rufen Sie die Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Get Recoverable Database][] ab.
3. Erstellen Sie die Wiederherstellungsanforderung mithilfe des Vorgangs [Create Database Recovery Request][].
4. Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Database Operation Status][] nach.



## Konfigurieren der Datenbank nach der Wiederherstellung
Dies ist eine Prüfliste, mit der Sie die wiederhergestellte Datenbank für die Produktion vorbereiten können.

1. **Aktualisieren von Verbindungszeichenfolgen**: Überprüfen Sie, ob die Verbindungszeichenfolgen Ihrer Clienttools auf die neu wiederhergestellte Datenbank verweisen.
2. **Ändern von Firewallregeln**: Überprüfen Sie die Firewallregeln auf dem Zielserver, und stellen Sie sicher, dass Verbindungen von Ihren Clientcomputern oder Azure mit dem Server und der neu wiederhergestellten Datenbank aktiviert sind.
3. **Überprüfen von Serveranmeldungen und Datenbankbenutzern**: Überprüfen Sie, ob alle von der Anwendung verwendeten Anmeldungen auf dem Server vorhanden sind, der die wiederhergestellte Datenbank hostet. Erstellen Sie die fehlenden Anmeldungen erneut, und gewähren Sie ihnen entsprechende Berechtigungen auf der wiederhergestellten Datenbank. 
4. **Aktivieren der Überwachung**: Wenn der Zugriff auf die Datenbank überwacht werden muss, müssen Sie nach der Wiederherstellung der Datenbank die Überwachung aktivieren.

Für die wiederhergestellte Datenbank ist TDE aktiviert, wenn für die Quelldatenbank TDE aktiviert ist.


## Nächste Schritte
Informationen zu den Geschäftskontinuitätsfeatures anderer Azure SQL-Datenbank-Editionen finden Sie in der [Azure SQL-Datenbank-Übersicht zur Geschäftskontinuität][].


<!--Image references-->

<!--Article references-->
[Azure SQL-Datenbank-Übersicht zur Geschäftskontinuität]: sql-database/sql-database-business-continuity.md
[Finalize a recovered database]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[Start-AzureSqlDatabaseRecovery]: https://msdn.microsoft.com/library/azure/dn720224.aspx
[List Recoverable Databases]: http://msdn.microsoft.com/library/azure/dn800984.aspx
[Get Recoverable Database]: http://msdn.microsoft.com/library/azure/dn800985.aspx
[Create Database Recovery Request]: http://msdn.microsoft.com/library/azure/dn800986.aspx
[Database Operation Status]: http://msdn.microsoft.com/library/azure/dn720371.aspx

<!--Other Web references-->
[Azure-Portal]: https://portal.azure.com/
[Wenden Sie sich an den Support]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0316_2016-->