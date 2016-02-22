<properties 
    pageTitle="Konfigurieren der Georeplikation für Azure SQL-Datenbank mit PowerShell | Microsoft Azure" 
    description="Georeplikation für Azure SQL-Datenbank mit PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="12/01/2015"
    ms.author="sstein"/>

# Konfigurieren der Georeplikation für die Azure SQL-Datenbank mit PowerShell



> [AZURE.SELECTOR]
- [Azure portal](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


In diesem Artikel wird beschrieben, wie Sie die Georeplikation der SQL-Datenbank mit PowerShell konfigurieren.

Die Georeplikation ermöglicht das Erstellen von bis zu vier (sekundären) Replikatdatenbanken in verschiedenen Datencenterregionen. Sekundäre Datenbanken stehen zur Verfügung, wenn ein Datencenter ausgefallen ist oder keine Verbindung mit der primären Datenbank möglich ist.

Die Georeplikation ist nur für Standard- und Premium-Datenbanken verfügbar.

Standard-Datenbanken können über eine nicht lesbare sekundäre Datenbank verfügen und müssen die empfohlene Region verwenden. Premium-Datenbanken können bis zu vier lesbare sekundäre Datenbanken in beliebigen der verfügbaren Regionen aufweisen.

Zum Konfigurieren der Georeplikation benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Eine Azure SQL-Datenbank: Die primäre Datenbank, die in eine andere geografische Region repliziert werden soll.
- Azure PowerShell 1.0 oder höher Sie können die Azure-PowerShell-Module herunterladen und installieren, indem Sie Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) befolgen.




## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Starten Sie also PowerShell, und führen Sie dann das folgende Cmdlet aus. Geben Sie auf dem Anmeldebildschirm die E-Mail-Adresse und das Kennwort wie für die Anmeldung beim Azure-Portal ein.


	Login-AzureRmAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zum Auswählen des Abonnements benötigen Sie Ihre Abonnement-ID. Sie können die Abonnement-ID aus den Informationen im vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen und mehr Details benötigen, können Sie auch das Cmdlet **Get-AzureRmSubscription** ausführen und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Das folgende Cmdlet verwendet die Abonnement-ID, um das aktuelle Abonnement festzulegen:

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Nach dem erfolgreichen Ausführen von **Select-AzureRMSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück.



## Hinzufügen einer sekundären Datenbank


Mit den folgenden Schritten wird eine neue sekundäre Datenbank in einer Partnerschaft für die Georeplikation erstellt.
  
Zum Aktivieren einer sekundären Datenbank müssen Sie der Besitzer oder Mitbesitzer des Abonnements sein.

Sie können das Cmdlet **New-AzureRmSqlDatabaseSecondary** nutzen, um eine sekundäre Datenbank auf einem Partnerserver in einer lokalen Datenbank auf dem Server hinzuzufügen, mit dem Sie verbunden sind (die primäre Datenbank).

Dieses Cmdlet ersetzt **Start AzureSqlDatabaseCopy** durch den **–IsContinuous**-Parameter. Es gibt ein **AzureRmSqlDatabaseSecondary**-Objekt aus, das von anderen Cmdlets für die eindeutige Bezeichnung einer bestimmten Replikationsverknüpfung verwendet werden kann. Dieses Cmdlet wird zurückgegeben, wenn die sekundäre Datenbank erstellt wurde und das Seeding erfolgt ist Je nach Größe der Datenbank liegt der Zeitaufwand zwischen wenigen Minuten und mehreren Stunden.

Die replizierte Datenbank auf dem zweiten Server hat den gleichen Namen wie die primäre Datenbank und standardmäßig auch die gleiche Dienstebene. Die sekundäre Datenbank kann lesbar oder nicht lesbar und eine Einzeldatenbank oder eine elastische Datenbank sein. Weitere Informationen finden Sie unter [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) und [Dienstebenen](sql-database-service-tiers.md). Nachdem die sekundäre Datenbank erstellt und das Seeding ausgeführt wurde, beginnt die Replikation der Daten von der primären Datenbank in die neue sekundäre Datenbank. Nachfolgend erfahren Sie, wie Sie mithilfe von PowerShell nicht lesbare und lesbare sekundäre Datenbanken erstellen – mit einer Einzeldatenbank oder einer elastischen Datenbank.

Wenn die Partnerdatenbank bereits vorhanden ist (z. B. aufgrund der Beendigung einer vorherigen Georeplikationsbeziehung), tritt für den Befehl ein Fehler auf.



### Hinzufügen einer nicht lesbaren sekundären Datenbank (Einzeldatenbank)

Mit dem folgenden Befehl erstellen Sie eine nicht lesbare sekundäre Datenbank der „mydb“-Datenbank des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### Hinzufügen einer lesbaren sekundären Datenbank (Einzeldatenbank)

Mit dem folgenden Befehl erstellen Sie eine lesbare sekundäre Datenbank der „mydb“-Datenbank des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### Hinzufügen einer nicht lesbaren sekundären Datenbank (elastische Datenbank)

Mit dem folgenden Befehl erstellen Sie eine nicht lesbare sekundäre Datenbank der „mydb“-Datenbank im elastischen Datenbankpool „ElasticPool1“ des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### Hinzufügen einer lesbaren sekundären Datenbank (elastische Datenbank)

Mit dem folgenden Befehl erstellen Sie eine lesbare sekundäre Datenbank der „mydb“-Datenbank im elastischen Datenbankpool „ElasticPool1“ des Servers „srv2“ in der Ressourcengruppe „rg2“:

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## Entfernen einer sekundären Datenbank

Mit dem Cmdlet **Remove-AzureRmSqlDatabaseSecondary** können Sie die Replikationspartnerschaft zwischen einer sekundären Datenbank und ihrer primären Datenbank dauerhaft beenden. Nach dem Beenden der Beziehung wird die sekundäre Datenbank eine Datenbank mit Lese-/Schreibzugriff. Wenn die Verbindung mit der sekundären Datenbank unterbrochen wird, ist der Befehl zwar erfolgreich, aber die sekundäre Datenbank wird erst mit Lese-/ Schreibzugriff versehen, nachdem die Verbindung wiederhergestellt wurde. Weitere Informationen finden Sie unter [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) und [Tarife](sql-database-service-tiers.md).

Dieses Cmdlet ersetzt „Stop-AzureSqlDatabaseCopy“ für die Replikation.

Dieser Vorgang ähnelt einer erzwungenen Beendigung, die die Replikationsverknüpfung entfernt. Die sekundäre Datenbank ist nun eine eigenständige Datenbank, die vor der Beendigung nicht komplett repliziert wird. Alle verknüpften Daten werden auf den vorherigen primären und sekundären Datenbanken bereinigt, falls oder wenn sie verfügbar sind. Dieses Cmdlet gibt zurück, wenn die Replikationsverknüpfung entfernt wird.


Zum Entfernen der sekundären Datenbank benötigen die Benutzer Schreibzugriff auf primäre und sekundäre Datenbanken entsprechend RBAC. Weitere Informationen finden Sie im Abschnitt zur rollenbasierten Zugriffssteuerung.

Mit dem folgenden Code entfernen Sie die Replikationsverknüpfung der „mydb“-Datenbank zum Server „srv2“ der Ressourcengruppe „rg2“.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 




## Initiieren eines geplanten Failovers

Mit dem Cmdlet **Set-AzureRmSqlDatabaseSecondary** und dem **-Failover**-Parameter können Sie eine sekundäre Datenbank zur neuen primären Datenbank heraufstufen und die vorhandene primäre Datenbank zu einer sekundären Datenbank herabstufen. Diese Funktionalität ist auf ein geplantes Failover ausgelegt, z. B. bei Notfallwiederherstellungsverfahren, und erfordert, dass die primäre Datenbank verfügbar ist.

Der Befehl hat den folgenden Workflow:

1. Für die Replikation wird vorübergehend in den synchronen Modus gewechselt. Dadurch werden alle ausstehenden Transaktionen in die sekundäre Datenbank übertragen.

2. Vertauschen Sie die Rollen der beiden Datenbanken in der Georeplikationspartnerschaft.

Durch diese Abfolge wird sichergestellt, dass kein Datenverlust auftritt. Es gibt einen kurzer Zeitraum, in dem beide Datenbanken während des Rollenwechsels (ca. 0 bis 25 Sekunden) nicht verfügbar sind. Unter normalen Umständen dauert der gesamte Vorgang nicht länger als 1 Minute. Weitere Informationen finden Sie unter [Set- AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx).


> [AZURE.NOTE] Falls die primäre Datenbank bei Aufruf des Befehls nicht verfügbar ist, misslingt dieser mit der Fehlermeldung, dass der primäre Server nicht verfügbar ist. In seltenen Fällen ist es möglich, dass der Vorgang nicht abgeschlossen werden kann und festzustecken scheint. In diesem Fall kann der Benutzer den Befehl zum Erzwingen des Failovers (ungeplantes Failover) aufrufen und den Datenverlust akzeptieren.



Dieses Cmdlet wird zurückgegeben, wenn der Wechsel von der sekundären zur primären Datenbank abgeschlossen ist.

Mit dem folgenden Befehl werden die Rollen der „mydb“-Datenbank auf dem Server „srv2“ in der Ressourcengruppe „rg2“ mit der primären Datenbank getauscht. Die ursprüngliche primäre Datenbank, mit der „db2“ verbunden war, wird nach der vollständigen Synchronisierung der beiden Datenbanken zur sekundären Datenbank.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover



## Auslösen eines ungeplanten Failovers von der primären Datenbank zur sekundären Datenbank


Mit dem Cmdlet **Set-AzureRmSqlDatabaseSecondary** mit den Parametern **-Failover** und **-AllowDataLoss** können Sie eine sekundäre Datenbank auf ungeplante Weise zur neuen primären Datenbank heraufstufen und das Herabstufen der vorhandenen primären Datenbank zu einer sekundären Datenbank erzwingen, sobald die primäre Datenbank nicht mehr verfügbar ist.

Diese Funktion dient zur Notfallwiederherstellung, wenn das Wiederherstellen der Verfügbarkeit der Datenbank überaus wichtig und ein gewisser Datenverlust akzeptabel ist. Beim Auslösen eines erzwungenen Failovers wird die angegebene sekundäre Datenbank sofort zur primären Datenbank und beginnt mit dem Akzeptieren von Schreibtransaktionen. Sobald sich die ursprüngliche primäre Datenbank wieder mit dieser neuen primären Datenbank verbinden kann, wird eine inkrementelle Sicherung der ursprünglichen Datenbank erstellt. Die alte primäre Datenbank wird zu einer sekundären Datenbank der neuen primären Datenbank. Anschließend ist sie lediglich ein Replikat der neuen primären Datenbank.

Da die Point-in-Time-Wiederherstellung jedoch nicht auf sekundären Datenbanken unterstützt wird, müssen Sie zum Wiederherstellen von Daten aus der alten primären Datenbank, die nicht in der neuen primären Datenbank repliziert wurden, CSS erfassen, um eine Datenbank in der bekannten Protokollsicherung wiederherstellen.

> [AZURE.NOTE] Falls der Befehl aufgerufen wird, wenn die primäre und sekundäre Datenbank online sind, wird die alte primäre Datenbank zur neuen sekundären Datenbank. Da jedoch kein Synchronisierungsversuch stattfindet, ist ein Datenverlust möglich.


Falls die primäre Datenbank mehrere sekundäre Instanzen hat, wird der Befehl teilweise erfolgreich ausgeführt. Die sekundäre Datenbank, auf der der Befehl ausgeführt wurde, wird zur primären Datenbank. Die alten primären Datenbanken bleiben jedoch primäre Datenbanken. Die beiden primären Datenbanken befinden sich dadurch in einem inkonsistenten Zustand und sind über eine ausgesetzte Replizierungsverknüpfung miteinander verbunden. Der Benutzer muss diese Konfiguration mit einer „remove secondary“-API auf einer dieser primären Datenbanken manuell reparieren.


Mit dem folgenden Befehl tauschen Sie die Rollen der „mydb“-Datenbank, wenn die primäre Datenbank nicht verfügbar ist. Die ursprüngliche primäre Datenbank, mit der „mydb“ verbunden war, wird zur sekundären Datenbank, sobald sie wieder online ist. Zu diesem Zeitpunkt kann bei der Synchronisierung Datenverlust entstehen.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss



## Überprüfen der Konfiguration und Integrität der Georeplikation

Zu den Überwachungsaufgaben gehören die Überwachung der Konfiguration der Georeplikation und der Integrität der Datenreplikation.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) kann verwendet werden, um die Informationen zu den „forward“-Replikationsverknüpfungen in der Katalogsicht „sys.geo\_replication\_links“ abzurufen.

Der folgende Befehl ruft den Status der Replikationsverknüpfung zwischen der primären „mydb“-Datenbank und der sekundären Datenbank auf Server „srv2“ von der Ressourcengruppe „rg2“ auf.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”



   

## Nächste Schritte

- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)




## Zusätzliche Ressourcen

- [Spotlight auf die neuen Georeplikationsfunktionen](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [Entwerfen von Cloudanwendungen zum Sicherstellen der Geschäftskontinuität mithilfe der Georeplikation](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [SQL-Datenbankdokumentation](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0211_2016-->