<properties 
   pageTitle="Erstellen und Verwalten eines Pool für elastische SQL-Datenbanken mit PowerShell" 
   description="Erstellen und Verwalten eines elastischen Pool für elastische Azure SQL-Datenbanken mit PowerShell" 
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
   ms.date="10/08/2015"
   ms.author="adamkr; sstein"/>

# Erstellen und Verwalten eines Pool für elastische SQL-Datenbanken mit PowerShell

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Übersicht

In diesem Artikel wird das Erstellen und Verwalten eines Pools für elastische Datenbanken in einer SQL-Datenbank mithilfe von PowerShell veranschaulicht.

> [AZURE.IMPORTANT]Ab Veröffentlichung von Azure PowerShell 1.0 Preview ist das Cmdlet "Switch-AzureMode" nicht mehr verfügbar. Außerdem wurden die Cmdlets im Modul "Azure-Ressourcen-Manager" umbenannt. In den Beispielen in diesem Artikel wird die neue Benennungskonvention von PowerShell 1.0 Preview verwendet. Ausführliche Informationen finden Sie unter ["Switch-AzureMode" in Azure PowerShell veraltet](https://github.com/Azure/azure-powershell/wiki/Deprecation-of-Switch-AzureMode-in-Azure-PowerShell).

Zur Ausführung von PowerShell-Cmdlets muss Azure PowerShell installiert sein und ausgeführt werden. Aufgrund des Entfernens von "Switch-AzureMode" müssen Sie die neueste Azure PowerShell herunterladen und durch Ausführen des [Microsoft-Webplattform-Installers](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) installieren. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

Die Schritte zum Erstellen eines Pools für elastische Datenbanken mit Azure PowerShell sind unterteilt und werden zur Übersichtlichkeit einzeln erläutert. Wenn Sie lediglich eine zusammengefasste Liste der Befehle suchen, finden Sie diese im Abschnitt **Gesamtbild** am Ende dieses Artikels.

In diesem Artikel erfahren Sie, wie Sie alle erforderlichen Elemente mit Ausnahme des Azure-Abonnements erstellen, die Sie zum Anlegen und Konfigurieren eines Pools für elastische Datenbanken benötigen. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.

> [AZURE.NOTE]Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit Azure SQL-Datenbank V12 verfügbar.


## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Da jetzt das Azure-Ressourcen-Manager-Modul ausgeführt wird, haben Sie Zugriff auf alle erforderlichen Cmdlets zum Erstellen und Konfigurieren eines Pools für elastische Datenbanken. Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Führen Sie Folgendes aus. Es wird eine Anmeldeseite angezeigt, auf der Sie Ihre Anmeldeinformationen eingeben müssen. Verwenden Sie die E-Mail-Adresse und das Kennwort für die Anmeldung beim Azure-Portal.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung sollten einige Informationen auf dem Bildschirm angezeigt werden, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zur Auswahl des Abonnements benötigen Sie Ihre Abonnement-ID oder den Anmeldenamen für das Abonnement (**-SubscriptionName**). Sie können diese Informationen aus dem vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen, können Sie sie über das Cmdlet **Get-AzureSubscription** abrufen und die gewünschten Abonnementinformationen aus dem ResultSet kopieren. Wenn Sie Ihre Abonnementinformationen haben, führen Sie das folgende Cmdlet aus:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000


## Erstellen von Ressourcengruppe, Server und Firewallregel

Nachdem Sie Zugriff auf Cmdlets für Ihr Azure-Abonnement haben, können Sie im nächsten Schritt die Ressourcengruppe einrichten, die den Server für den Pool für elastische Datenbanken enthält. Sie können den nächsten Befehl für einen beliebigen gültigen Speicherort anpassen. Führen Sie **(Get-AzureRMLocation | where-object {$\_.Name -eq "Microsoft.Sql/servers" }).Locations** aus, um eine Liste der gültigen Speicherorte abzurufen.

Wenn Sie bereits über eine Ressourcengruppe verfügen, können Sie mit dem nächsten Schritt fortfahren, oder Sie führen den folgenden Befehl zum Erstellen einer neuen Ressourcengruppe aus:

	New-AzureRMResourceGroup -Name "resourcegroup1" -Location "West US"

### Erstellen eines Servers 

Pools für elastische Datenbanken werden innerhalb von Azure SQL-Datenbankservern erstellt. Wenn Sie bereits über einen Server verfügen, können Sie mit dem nächsten Schritt fortfahren, oder Sie führen den folgenden Befehl zum Erstellen eines neuen V12-Servers aus: Ersetzen Sie "ServerName" durch den Namen des Servers. Es muss ein eindeutiger SQL Azure-Servername sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Die Serverdetails und PowerShell-Eingabeaufforderung werden angezeigt, nachdem der Server erfolgreich erstellt wurde. Sie können den Befehl für einen beliebigen gültigen Speicherort anpassen.

	New-AzureRMSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"

Beim Ausführen dieses Befehls wird ein Fenster zur Eingabe von **Benutzername** und **Kennwort** geöffnet. Dabei handelt es sich nicht um Ihre Azure-Anmeldeinformationen. Geben Sie den Benutzernamen und das Kennwort ein, die Sie als Anmeldeinformationen für das Administratorkonto des neuen Servers erstellen möchten.


### Konfigurieren einer Serverfirewallregel für den Zugriff auf den Server

Richten Sie eine Firewallregel für den Zugriff auf den Server ein. Führen Sie den folgenden Befehl aus, der die Start- und End-IP-Adressen durch gültige Werte für Ihren Computer ersetzt.

Wenn Ihr Server Zugriff auf andere Azure-Dienste ermöglichen muss, fügen Sie den Schalter **-AllowAllAzureIPs** hinzu, durch den eine spezielle Firewallregel hinzugefügt und alle Zugriffe für Azure-Datenverkehr auf den Server ermöglicht werden.

	New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

Weitere Informationen finden Sie unter [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx).


## Erstellen eines Pools für elastische Datenbanken und elastischer Datenbanken

Sie haben jetzt eine Ressourcengruppe, einen Server und eine Firewallregel konfiguriert, sodass Sie auf den Server zugreifen können. Durch den folgenden Befehl wird der Pool für elastische Datenbanken erstellt. Dieser Befehl erstellt einen Pool mit insgesamt 400 eDTUs. Jede Datenbank im Pool bietet garantiert immer 10 verfügbare eDTUs ("DatabaseDtuMin"). Einzeldatenbanken im Pool können maximal 100 eDTUs ("DatabaseDtuMax") nutzen. Ausführliche Erläuterungen der Parameter finden Sie unter [Elastische Azure SQL-Datenbankpools](sql-database-elastic-pool.md).


	New-AzureRMSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


### Erstellen oder Hinzufügen elastischer Datenbanken in einem Pool für elastische Datenbanken

Der im vorherigen Schritt erstellte Pool ist leer und enthält noch keine elastischen Datenbanken. Die folgenden Abschnitte zeigen, wie neue elastische Datenbanken innerhalb des Pools erstellt und vorhandene Datenbanken zum Pool hinzugefügt werden.

*Nach dem Erstellen eines Pools können Sie Transact-SQL auch zum Erstellen von neuen elastischen Datenbanken im Pool verwenden und vorhandene Datenbanken in und aus Pools verschieben. Weitere Informationen finden Sie unter [Referenz für Pools für elastische Datenbanken – Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

### Erstellen einer neuen elastischen Datenbank in einem Pool für elastische Datenbanken

Verwenden Sie zum direkten Erstellen einer neuen Datenbank in einem Pool das Cmdlet **New-AzureRMSqlDatabase**, und legen Sie den **ElasticPoolName**-Parameter fest.


	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



### Verschieben einer vorhandenen Datenbank in einen Pool für elastische Datenbanken

Verwenden Sie zum Verschieben einer vorhandenen Datenbank in einen Pool das Cmdlet **Set-AzureRMSqlDatabase**, und legen Sie den **ElasticPoolName**-Parameter fest.


Erstellen Sie zu Demonstrationszwecken eine Datenbank, die sich nicht in einem Pool für elastische Datenbanken befindet.

	New-AzureRMSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -Edition "Standard"

Verschieben Sie die vorhandene Datenbank in den Pool für elastische Datenbanken.

	Set-AzureRMSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Ändern der Leistungseinstellungen eines Pools für elastische Datenbanken


    Set-AzureRMSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Überwachen von elastischen Datenbanken und Pools für elastische Datenbanken

### Abrufen des Status der Vorgänge für Pools für elastische Datenbanken

Sie können den Status der Vorgänge im Pool für elastische Datenbanken, einschließlich Erstellung und Updates, verfolgen.

	Get-AzureRMSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


### Abrufen des Status beim Verschieben einer elastischen Datenbank in und aus einem Pool für elastische Datenbanken

	Get-AzureRMSqlElasticPoolDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

### Abrufen der Ressourcenverbrauchsmetriken für einen Pool für elastische Datenbanken

Metriken, die als Prozentsatz des Ressourcenpool-Grenzwerts abgerufen werden können:

* Durchschnittliche CPU-Auslastung – cpu\_percent 
* Durchschnittliche E/A-Auslastung – data\_io\_percent 
* Durchschnittliche Protokollnutzung – log\_write\_percent 
* Durchschnittliche Speicherauslastung – memory\_percent 
* Durchschnittliche eDTU-Auslastung (als Maximalwert der CPU-, E/A- und Protokoll-Auslastung) – DTU\_percent 
* Maximale Anzahl gleichzeitiger Benutzeranforderungen (Worker) – max\_concurrent\_requests 
* Maximale Anzahl gleichzeitiger Benutzersitzungen – max\_concurrent\_sessions 
* Gesamtspeichergröße für den elastischen Pool – storage\_in\_megabytes 


Granularität/Beibehaltungsdauern für die Metriken:

* Daten werden mit 5-Minuten-Granularität zurückgegeben.  
* Die Datenaufbewahrung beträgt 14 Tage.  


Dieses Cmdlet und die API beschränken die Anzahl der Zeilen, die in einem Aufruf abgerufen werden können, auf 1000 Zeilen (ca. drei Tage Daten bei 5-Minuten-Granularität). Dieser Befehl kann jedoch mehrmals mit verschiedenen Anfangs- und Endzeitintervallen aufgerufen werden, um mehr Daten abzurufen.


Abrufen der Metriken:

	$metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Abrufen zusätzlicher Tage durch eine Wiederholung des Aufrufs und Anfügen der Daten:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formatieren der Tabelle:

    $table = Format-MetricsAsTable $metrics 

Exportieren in eine CSV-Datei:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

### Abrufen der Ressourcenverbrauchsmetriken für eine elastische Datenbank

Diese APIs sind identisch mit den aktuellen APIs (V12), die für die Überwachung der Ressourcenverwendung einer eigenständigen Datenbank verwendet werden, mit Ausnahme der folgenden semantischen Unterschiede:

* Die für diese API abgerufenen Metriken werden als Prozentsatz des "databaseDtuMax"-Werts (oder der entsprechenden Obergrenze für die zugrunde liegende Metrik wie CPU, E/A usw.) ausgedrückt, der für diesen Pool für elastische Datenbanken festgelegt wurde. Beispielsweise zeigen 50 % Auslastung bei einer dieser Metriken an, dass der spezifische Ressourcenverbrauch der Obergrenze pro Datenbank für diese Ressource im übergeordneten Pool für elastische Datenbanken bei 50 % liegt. 

Abrufen der Metriken: $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")

Abrufen zusätzlicher Tage nach Bedarf durch eine Wiederholung des Aufrufs und Anfügen der Daten:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formatieren der Tabelle:

    $table = Format-MetricsAsTable $metrics 

Exportieren in eine CSV-Datei:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Gesamtbild


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000
    New-AzureRMResourceGroup -Name "resourcegroup1" -Location "West US"
    New-AzureRMSqlServer -ResourceGroupName "resourcegroup1" -ServerName "server1" -Location "West US" -ServerVersion "12.0"
    New-AzureRMSqlServerFirewallRule -ResourceGroupName "resourcegroup1" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"
    New-AzureRMSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
    New-AzureRMSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1" -MaxSizeBytes 10GB
    Set-AzureRMSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

    $metrics = (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 
    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/d7c1d29a-ad13-4033-877e-8cc11d27ebfd/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015")
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Nächste Schritte

Nach dem Erstellen eines Pools für elastische Datenbanken können Sie elastische Datenbanken im Pool mit elastischen Aufträgen verwalten. Elastische Aufträge erleichtern die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool. Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).


## Referenz für elastische Datenbanken

Weitere Informationen über elastische Datenbanken und elastische Datenbankpools, einschließlich API- und Fehlerinformationen, finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).

<!---HONumber=Oct15_HO3-->