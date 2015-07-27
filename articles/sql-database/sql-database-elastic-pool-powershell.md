<properties 
   pageTitle="Erstellen und Verwalten eines elastischen SQL-Datenbankpools mit PowerShell" 
   description="Erstellen und Verwalten eines elastischen Pools in Azure SQL-Datenbank mit PowerShell" 
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
   ms.date="06/24/2015"
   ms.author="adamkr; sstein"/>

# Erstellen und Verwalten eines elastischen SQL-Datenbankpools mit PowerShell (Vorschau)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Übersicht

In diesem Artikel wird das Erstellen und Verwalten eines elastischen SQL-Datenbankpools mithilfe von PowerShell veranschaulicht.


Die Schritte zum Erstellen eines elastischen Pools mit Azure PowerShell sind unterteilt und werden zur Übersichtlichkeit einzeln erläutert. Wenn Sie lediglich eine zusammengefasste Liste der Befehle suchen, finden Sie diese im Abschnitt **Gesamtbild** am Ende dieses Artikels.

In diesem Artikel erfahren Sie, wie Sie alle erforderlichen Elemente mit Ausnahme des Azure-Abonnements erhalten, die Sie zum Erstellen und Konfigurieren eines elastischen Pools benötigen. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.

> [AZURE.NOTE]Elastische Pools sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.


## Voraussetzungen

Um einen elastischen Pool mit PowerShell zu erstellen, muss Azure PowerShell installiert sein und ausgeführt werden. Wechseln Sie in den Ressourcen-Manager-Modus, um auf die PowerShell-Cmdlets für Azure-Ressourcen-Manager zuzugreifen.

Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) ausführen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

Die Cmdlets zum Erstellen und Verwalten von Azure SQL-Datenbanken und elastischen Pools befinden sich im Azure-Ressourcen-Manager-Modul. Wenn Sie Azure PowerShell starten, werden die Cmdlets im Azure-Modul standardmäßig importiert. Um zum Azure-Ressourcen-Manager-Modul zu wechseln, verwenden Sie das Cmdlet "Switch-AzureMode".

	PS C:>Switch-AzureMode -Name AzureResourceManager

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).


## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Da jetzt das Azure-Ressourcen-Manager-Modul ausgeführt wird, haben Sie Zugriff auf alle erforderlichen Cmdlets zum Erstellen und Konfigurieren eines elastischen Pools. Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Führen Sie Folgendes aus. Es wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

	PS C:>Add-AzureAccount

Nach der erfolgreichen Anmeldung sollten einige Informationen auf dem Bildschirm angezeigt werden, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zur Auswahl des Abonnements benötigen Sie Ihre Abonnement-ID oder den Anmeldenamen für das Abonnement (**-SubscriptionName**). Sie können diese Informationen aus dem vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren. Wenn Sie Ihre Abonnementinformationen haben, führen Sie das folgende Cmdlet aus:

	PS C:>Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Erstellen von Ressourcengruppe, Server und Firewallregel

Nachdem Sie Zugriff auf Cmdlets für Ihr Azure-Abonnement haben, können Sie im nächsten Schritt die Ressourcengruppe einrichten, die den Server für den elastischen Pool enthält. Sie können den nächsten Befehl für einen beliebigen gültigen Speicherort anpassen. Führen Sie **(Get-AzureLocation | where-object {$_.Name -eq "Microsoft.Sql/servers" }).Locations** aus, um eine Liste der gültigen Speicherorte abzurufen.

Wenn Sie bereits über eine Ressourcengruppe verfügen, können Sie mit dem nächsten Schritt fortfahren, oder Sie führen den folgenden Befehl zum Erstellen einer neuen Ressourcengruppe aus:

	PS C:>New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"

### Erstellen eines Servers 

Elastische Pools werden innerhalb von Azure SQL-Datenbankservern erstellt. Wenn Sie bereits über einen Server verfügen, können Sie mit dem nächsten Schritt fortfahren, oder Sie führen den folgenden Befehl zum Erstellen eines neuen V12-Servers aus: Ersetzen Sie "ServerName" durch den Namen des Servers. Es muss ein eindeutiger SQL Azure-Servername sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Die Serverdetails und PowerShell-Eingabeaufforderung werden angezeigt, nachdem der Server erfolgreich erstellt wurde. Sie können den Befehl für einen beliebigen gültigen Speicherort anpassen.

	PS C:>New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Beim Ausführen dieses Befehls wird ein Fenster zur Eingabe von **Benutzername** und **Kennwort** geöffnet. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.


### Konfigurieren einer Serverfirewallregel für den Zugriff auf den Server

Richten Sie eine Firewallregel für den Zugriff auf den Server ein. Führen Sie den folgenden Befehl aus, der die Start- und End-IP-Adressen durch gültige Werte für Ihren Computer ersetzt.

Wenn Ihr Server Zugriff auf andere Azure-Dienste ermöglichen muss, fügen Sie den Schalter **-AllowAllAzureIPs** hinzu, durch den eine spezielle Firewallregel hinzugefügt und alle Zugriffe für Azure-Datenverkehr auf den Server ermöglicht werden.

	PS C:>New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Erstellen eines elastischen Pools und elastischer Datenbanken

Sie haben jetzt eine Ressourcengruppe, einen Server und eine Firewallregel konfiguriert, sodass Sie auf den Server zugreifen können. Durch den folgenden Befehl wird der elastische Pool erstellt. Dieser Befehl erstellt einen Pool mit insgesamt 400 DTUs. Jede Datenbank im Pool bietet garantiert immer 10 verfügbare DTUs ("DatabaseDtuMin"). Einzelne Datenbanken im Pool können maximal 100 DTUs ("DatabaseDtuMax") nutzen. Ausführliche Erläuterungen der Parameter finden Sie unter [Elastische Azure SQL-Datenbankpools](sql-database-elastic-pool.md).


	PS C:>New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Erstellen oder Hinzufügen von elastischen Datenbanken in einem Pool

Der im vorherigen Schritt erstellte elastische Pool ist leer und enthält noch keine Datenbanken. Die folgenden Abschnitte zeigen, wie neue Datenbanken innerhalb des elastischen Pools erstellt und vorhandene Datenbanken zum Pool hinzugefügt werden.


### Erstellen einer neuen elastischen Datenbank in einem elastischen Pool

Verwenden Sie zum direkten Erstellen einer neuen Datenbank in einem elastischen Pool das Cmdlet **New-AzureSqlDatabase**, und legen Sie den **ElasticPoolName**-Parameter fest.


	PS C:>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Verschieben einer vorhandenen Datenbank in einen elastischen Pool

Verwenden Sie zum Verschieben einer vorhandenen Datenbank in einen elastischen Pool das Cmdlet **Set-AzurSqlDatabase**, und legen Sie den **ElasticPoolName**-Parameter fest.


Erstellen Sie zu Demonstrationszwecken eine Datenbank, die sich nicht in einem elastischen Pool befindet.

	PS C:>New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Verschieben Sie die vorhandene Datenbank in den elastischen Pool.

	PS C:>Set-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Überwachen von elastischen Datenbanken und elastischen Pools

### Abrufen des Status der elastischen Poolvorgänge

Sie können den Status der elastischen Poolvorgänge, einschließlich Erstellung und Updates, verfolgen.

	PS C:> Get-AzureSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Abrufen des Status beim Verschieben einer elastischen Datenbank in und aus einem elastischen Pool

	PS C:>Get-AzureSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Abrufen der Ressourcenverbrauchsmetriken für einen elastischen Pool

Metriken, die als Prozentsatz des Ressourcenpool-Grenzwerts abgerufen werden können:

* Durchschnittliche CPU-Auslastung – cpu_percent 
* Durchschnittliche E/A-Auslastung – data_io_percent 
* Durchschnittliche Protokollnutzung – log_write_percent 
* Durchschnittliche Speicherauslastung – memory_percent 
* Durchschnittliche DTU-Auslastung (als Maximalwert der CPU-, E/A- und Protokoll-Auslastung) – DTU_percent 
* Maximale Anzahl gleichzeitiger Benutzeranforderungen (Worker) – max_concurrent_requests 
* Maximale Anzahl gleichzeitiger Benutzersitzungen – max_concurrent_sessions 
* Gesamtspeichergröße für den elastischen Pool – storage_in_megabytes 


Granularität/Beibehaltungsdauern für die Metriken:

* Daten werden mit 5-Minuten-Granularität zurückgegeben.  
* Die Datenaufbewahrung beträgt 14 Tage.  


Dieses Cmdlet und die API beschränken die Anzahl der Zeilen, die in einem Aufruf abgerufen werden können, auf 1000 Zeilen (ca. drei Tage Daten bei 5-Minuten-Granularität). Dieser Befehl kann jedoch mehrmals mit verschiedenen Anfangs- und Endzeitintervallen aufgerufen werden, um mehr Daten abzurufen.


Abrufen der Metriken:

	PS C:> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Abrufen zusätzlicher Tage durch eine Wiederholung des Aufrufs und Anfügen der Daten:

	PS C:> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formatieren der Tabelle:

    PS C:> $table = Format-MetricsAsTable $metrics 

Exportieren in eine CSV-Datei:

    PS C:> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Abrufen der Ressourcenverbrauchsmetriken für eine elastische Datenbank

Diese APIs sind identisch mit den aktuellen APIs (V12), die für die Überwachung der Ressourcenverwendung einer eigenständigen Datenbank verwendet werden, mit Ausnahme der folgenden semantischen Unterschiede:

* Die für diese API abgerufenen Metriken werden als Prozentsatz des "databaseDtuMax"-Werts (oder der entsprechenden Obergrenze für die zugrunde liegende Metrik wie CPU, E/A usw.) ausgedrückt, der für diesen elastischen Pool festgelegt wurde. Beispielsweise zeigen 50 % Auslastung bei einer dieser Metriken an, dass der spezifische Ressourcenverbrauch der Obergrenze pro Datenbank für diese Ressource im übergeordneten elastischen Pool bei 50 % liegt. 

Abrufen der Metriken: PS C:> $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Abrufen zusätzlicher Tage nach Bedarf durch eine Wiederholung des Aufrufs und Anfügen der Daten:

    PS C:> $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formatieren der Tabelle:

    PS C:> $table = Format-MetricsAsTable $metrics 

Exportieren in eine CSV-Datei:

    PS C:> foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Gesamtbild


    Switch-AzureMode -Name AzureResourceManager
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Nächste Schritte

Nach dem Erstellen eines elastischen Pools können Sie elastische Datenbanken im Pool mit elastischen Aufträgen verwalten. Elastische Aufträge erleichtern die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool.

Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).


## Referenz für elastische Datenbanken

Weitere Informationen über elastische Datenbanken und elastische Datenbankpools, einschließlich API- und Fehlerinformationen, finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).

<!---HONumber=July15_HO3-->