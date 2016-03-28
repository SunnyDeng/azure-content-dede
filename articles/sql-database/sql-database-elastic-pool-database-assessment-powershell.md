<properties
	pageTitle="PowerShell-Skript zum Identifizieren einzelner für einen Pool geeignete Datenbanken"
	description="Ein elastischer Datenbankpool stellt eine Sammlung der verfügbaren Ressourcen dar, die von einer Gruppe von elastischen Datenbanken gemeinsam verwendet werden. Dieses Dokument enthält ein PowerShell-Skript, das Ihnen helfen soll, die Eignung eines Pools für elastische Datenbanken für eine Gruppe von Datenbanken einzuschätzen."
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# PowerShell-Skript zum Ermitteln der für einen Pool für elastische Datenbanken geeigneten Datenbanken

Mit dem PowerShell-Skript in diesem Artikel werden die zusammengefassten eDTU-Werte für Benutzerdatenbanken auf einem SQL-Datenbankserver geschätzt. Mit dem Skript werden Daten erfasst, während es ausgeführt wird. Für eine typische Produktionsworkload sollten Sie das Skript für mindestens einen Tag ausführen. Idealerweise sollten Sie das Skript für eine Dauer ausführen, die die typische Workload der Datenbanken darstellt – also ausreichend lange, um die Daten zu erfassen, die einer normalen und einer maximalen Auslastung der Datenbanken entsprechen. Wenn Sie das Skript eine Woche oder auch länger ausführen, erhalten Sie wahrscheinlich eine genauere Schätzung.

Dieses Skript ist besonders für die Evaluierung von Datenbanken auf v11-Servern, bei denen Pools nicht unterstützt werden, für die Migration zu v12-Servern, bei denen Pools unterstützt werden, nützlich. Auf v12-Servern verfügt die SQL-Datenbank über eine integrierte Logik, die den Verlauf der Nutzungstelemetrie analysiert und einen Pool empfiehlt, wenn dies die kostengünstigere Lösung ist. Informationen dazu, wie Sie diese Funktion verwenden, finden Sie unter [Überwachen, Verwalten und Skalieren eines Pools für elastische Datenbanken](sql-database-elastic-pool-manage-portal.md).

> [AZURE.IMPORTANT] Beim Ausführen des Skripts muss das PowerShell-Fenster geöffnet sein. Schließen Sie das PowerShell-Fenster erst, wenn Sie das Skript für die erforderliche Zeit ausgeführt haben.

## Voraussetzungen 

Installieren Sie Folgendes, bevor Sie das Skript ausführen:

- Die neuesten [Powershell-Befehlszeilentools](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
- Das [SQL Server 2014-Featurepack](https://www.microsoft.com/download/details.aspx?id=42295).

### Details zum Skript

Sie können das Skript auf Ihrem lokalen Computer oder in einem virtuellen Computer in der Cloud ausführen. Wenn Sie es auf Ihrem lokalen Computer ausführen, kann dies Gebühren für die Datenübergabe verursachen, da das Skript Daten aus den Zieldatenbanken herunterladen muss. Nachstehend sind die Schätzungen für das Datenvolumen basierend auf der Anzahl von Zieldatenbanken und der Ausführungsdauer des Skripts angegeben. Informationen zu Azure-Datenübertragungskosten finden Sie unter [Details zu den Datenübertragungskosten](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 Datenbank pro Stunde = 38 KB
 -     1 Datenbank pro Tag = 900 KB
 -     1 Datenbank pro Woche = 6 MB
 -     100 Datenbanken pro Tag = 90 MB
 -     500 Datenbanken pro Woche = 3 GB

Das Skript schließt Datenbanken aus, die keine guten Kandidaten für die aktuelle öffentliche Vorschau im Standard-Tarif sind. Wenn Sie auf dem Zielserver weitere Datenbanken ausschließen müssen, ändern Sie das Skript Ihren Anforderungen entsprechend. Standardmäßig kompiliert das Skript keine Informationen für Folgendes:

* Elastische Datenbanken (Datenbanken, die sich bereits in einem elastischen Pool befinden)
* Die Masterdatenbank auf dem Server

Für das Skript ist eine Ausgabedatenbank zum Speichern von Zwischendaten zwecks Analyse erforderlich. Sie können eine neue Datenbank anlegen oder eine vorhandene verwenden. Obwohl es für die Ausführung des Tools technisch nicht erforderlich ist, empfiehlt es sich, die Ausgabedatenbank auf einem anderen Server zu speichern, um Auswirkungen auf das Analyseergebnis zu vermeiden. Die Leistungsstufe der Ausgabedatenbank sollte mindestens S0 oder höher sein. Beim Sammeln von Daten für eine große Anzahl von Datenbanken über einen längeren Zeitraum sollten Sie überlegen, ob die Ausgabedatenbank auf eine höhere Leistungsstufe aktualisiert werden sollte.

Für das Skript müssen Sie Anmeldeinformationen angeben, um die Verbindung mit dem Zielserver herzustellen (dem Kandidaten für den Pool für elastische Datenbanken), und zwar mit einem vollständigen Servernamen wie <*dbname*>**.database.windows.net**. Das Skript unterstützt die gleichzeitige Analyse mehrerer Server nicht.

Nach der Übermittlung von Werten für den anfänglichen Parametersatz werden Sie aufgefordert, sich bei Ihrem Azure-Konto anzumelden. Hiermit melden Sie sich beim Zielserver und nicht beim Datenbankausgabeserver an.
	
Wenn beim Ausführen des Skripts die folgenden Warnungen ausgegeben werden, können Sie diese ignorieren:

- WARNUNG: Das Cmdlet „Switch-AzureMode“ ist veraltet.
- WARNUNG: Es konnten keine SQL Server-Dienstinformationen abgerufen werden. Fehler bei einem Versuch, eine Verbindung zu WMI auf 'Microsoft.Azure.Commands.Sql.dll' herzustellen: Der RPC-Server ist nicht verfügbar.

Nach Abschluss des Skripts wird die geschätzte Anzahl eDTUs ausgegeben, die für einen Pool erforderlich sind, der alle Datenbankkandidaten auf dem Zielserver enthalten soll. Diese geschätzten eDTUs können zum Erstellen und Konfigurieren des Pools verwendet werden. Nachdem der Pool erstellt wurde und Datenbanken in diesen Pool verschoben wurden, überwachen Sie ihn für einige Tage genauestens, und nehmen Sie im Bedarfsfall Anpassungen an der Konfiguration der Pool-eDTUs vor. Informationen finden Sie unter [Überwachen, Verwalten und Skalieren eines Pools für elastische Datenbanken](sql-database-elastic-pool-manage-portal.md)


   [AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)
    
    param (
    [Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
    [Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
    [Parameter(Mandatory=$true)][string]$username, # user name
    [Parameter(Mandatory=$true)][string]$serverPassword, # password
    [Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
    [Parameter(Mandatory=$true)][string]$outputdatabaseName,
    [Parameter(Mandatory=$true)][string]$outputDBUsername,
    [Parameter(Mandatory=$true)][string]$outputDBpassword,
    [Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
    )
    
    Add-AzureAccount 
    Select-AzureSubscription $AzureSubscriptionName
    Switch-AzureMode AzureResourceManager
    
    $server = Get-AzureSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    
    # Check version/upgrade status of the server
    $upgradestatus = Get-AzureSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
    $version = ""
    if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
    {
    $version = $upgradestatus.Status
    }
    else
    {
    $version = $server.ServerVersion
    }
    
    # For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
    $ListOfDBs = Get-AzureSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
    
    $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
    $destinationTableName = "resource_stats_output"
    
    # Create a table in output database for metrics collection
    $sql = "
    IF  NOT EXISTS (SELECT * FROM sys.objects 
    WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
    
    BEGIN
    Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
    Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
    END
    TRUNCATE TABLE $($destinationTableName);
    "
    Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
    
    # waittime (minutes) is interval between data collection queries in the loop below.
    $Waittime = 10
    $end_Time = [DateTime]::UtcNow
    $start_time = $end_time.AddMinutes(-$Waittime)
    $finish_time = $end_Time.AddMinutes($duration_minutes)
    
    While ($end_time -lt $finish_time)
    {
    Write-Host "Collecting metrics..." 
    foreach ($db in $ListOfDBs)
    {
    if ($version -in ("12.0", "Completed")) # for V12 databases 
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'S3' THEN 100
    WHEN 'P1' THEN 125
    WHEN 'P2' THEN 250
    WHEN 'P3' THEN 1000
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    else
    {
    $sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
    $sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceLevelObjectiveName)';"
    $sql+= "
    Declare @DTU_cap int, @db_size float;
    Select @DTU_cap = CASE @SLO 
    WHEN 'Basic' THEN 5
    WHEN 'S0' THEN 10
    WHEN 'S1' THEN 20
    WHEN 'S2' THEN 50
    WHEN 'P1' THEN 100
    WHEN 'P2' THEN 200
    WHEN 'P3' THEN 800
    ELSE 50 -- assume Web/Business DBs
    END
    SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
    SELECT @dbname as database_name, @SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
    as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
    WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
    " 
    }
    
    $result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
    #bulk copy the metrics to output database
    $bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
    $bulkCopy.BulkCopyTimeout = 600
    $bulkCopy.DestinationTableName = "$destinationTableName";
    $bulkCopy.WriteToServer($result);
    
    }
    
    $start_time = $start_time.AddMinutes($Waittime)
    $end_time = $end_time.AddMinutes($Waittime)
    Write-Host $start_time
    Write-Host $end_time
    do {
    Start-Sleep 1
       }
    until (([DateTime]::UtcNow) -ge $end_time)
    }
    
    Write-Host "Analyzing the collected metrics...."
    # Analysis query that does aggregation of the resource metrics to calculate pool size.
    $sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
    WITH group_stats AS
    (
    SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
    FROM resource_stats_output 
    WHERE slo LIKE '
    
    $sql2 = '
    GROUP BY end_time
    )
    -- calculate aggregate storage and DTUs for all DBs in the group
    , group_DTU AS
    (
    SELECT end_time, avg_group_Storage, 
    (SELECT Max(v)
       FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
    FROM group_stats
    )
    -- Get top 1 percent of the storage and DTU utilization samples.
    , top1_percent AS (
    SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
    )
    
    -- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
    --SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
    SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
    IF @DTU_Storage > @DTU_Perf_99 
    SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
    ELSE 
    SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
    
    #check if there are any web/biz edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Shared*")
    {
    write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any basic edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "Basic*")
    {
    write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'Basic%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]} 
    }
    
    #check if there are any standard edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "S*")
    {
    write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
    
    #check if there are any premium edition dbs in the collected metrics
    $checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
    $output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
    if ($output -like "P*")
    {
    write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
    $sql = $sql1 + "'P%'"  + $sql2
    $data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
    $data | %{'{0}' -f $_[0]}
    }
        

<!---HONumber=AcomDC_0316_2016-->