<properties 
    pageTitle="Verwalten eines Pools für elastische Datenbanken (PowerShell) | Microsoft Azure" 
    description="Erfahren Sie, wie Sie PowerShell zum Verwalten eines Pools für elastische Datenbanken nutzen"  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Überwachen und Verwalten eines Pools für elastische Datenbanken (PowerShell) 

> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


Erstellen und Verwalten eines [Pools für elastische Datenbanken](sql-database-elastic-pool.md) mit PowerShell-Cmdlets

Häufige Fehlercodes finden Sie unter [SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen: Datenbankverbindungsfehler und andere Probleme](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar. Wenn Sie über einen SQL-Datenbank V11-Server verfügen, können Sie in einem Schritt [mithilfe von PowerShell auf V12 aktualisieren und einen Pool erstellen](sql-database-upgrade-server-portal.md).

Sie müssen Azure PowerShell 1.0 oder höher installiert haben. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).




## Erstellen einer neuen elastischen Datenbank in einem Pool für elastische Datenbanken

Verwenden Sie zum direkten Erstellen einer neuen Datenbank in einem Pool das Cmdlet [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx), und legen Sie den **ElasticPoolName**-Parameter fest.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Verschieben einer eigenständigen Datenbank in einen Pool für elastische Datenbanken

Verwenden Sie zum Verschieben einer vorhandenen Datenbank in einen Pool das Cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx), und legen Sie den **ElasticPoolName**-Parameter fest.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Ändern der Leistungseinstellungen eines Pools für elastische Datenbanken

Verwenden Sie das Cmdlet [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx), um die Leistungseinstellungen eines Pools für elastische Datenbanken zu ändern.

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Abrufen des Status der Vorgänge für Pools für elastische Datenbanken

Sie können den Status der Vorgänge im Pool für elastische Datenbanken, einschließlich Erstellung und Updates, mithilfe des Cmdlets [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) nachverfolgen.

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Abrufen des Status beim Verschieben einer elastischen Datenbank in und aus einem Pool für elastische Datenbanken

Sie können den Status der Vorgänge der elastischen Datenbanken, einschließlich Erstellung und Updates, mithilfe des Cmdlets [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) nachverfolgen.

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Abrufen von Auslastungsdaten für einen Pool für elastische Datenbanken

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
* Die Datenaufbewahrung beträgt 14 Tage.  


Dieses Cmdlet und die API beschränken die Anzahl der Zeilen, die in einem Aufruf abgerufen werden können, auf 1000 Zeilen (ca. drei Tage Daten bei 5-Minuten-Granularität). Dieser Befehl kann jedoch mehrmals mit verschiedenen Anfangs- und Endzeitintervallen aufgerufen werden, um mehr Daten abzurufen.


Abrufen der Metriken:

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Abrufen zusätzlicher Tage durch eine Wiederholung des Aufrufs und Anfügen der Daten:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formatieren der Tabelle:

    $table = Format-MetricsAsTable $metrics 

Exportieren in eine CSV-Datei:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## Abrufen der Ressourcenverbrauchsmetriken für eine elastische Datenbank

Diese APIs sind identisch mit den aktuellen APIs (V12), die für die Überwachung der Ressourcenverwendung einer eigenständigen Datenbank verwendet werden, mit Ausnahme der folgenden semantischen Unterschiede:

* Die für diese API abgerufenen Metriken werden als Prozentsatz des "databaseDtuMax"-Werts (oder der entsprechenden Obergrenze für die zugrunde liegende Metrik wie CPU, E/A usw.) ausgedrückt, der für diesen Pool für elastische Datenbanken festgelegt wurde. Beispielsweise zeigen 50 % Auslastung bei einer dieser Metriken an, dass der spezifische Ressourcenverbrauch der Obergrenze pro Datenbank für diese Ressource im übergeordneten Pool für elastische Datenbanken bei 50 % liegt. 

Abrufen der Metriken:

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Abrufen zusätzlicher Tage nach Bedarf durch eine Wiederholung des Aufrufs und Anfügen der Daten:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formatieren der Tabelle:

    $table = Format-MetricsAsTable $metrics 

Exportieren in eine CSV-Datei:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Überwachen und Verwalten eines Pools für elastische Datenbanken am Beispiel von PowerShell


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Nächste Schritte

- [Erstellen elastischer Aufträge:](sql-database-elastic-jobs-overview.md) Elastische Aufträge erleichtern die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool.


## Referenz für elastische Datenbanken

Weitere Informationen über elastische Datenbanken und elastische Datenbankpools, einschließlich API- und Fehlerinformationen, finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0323_2016-->