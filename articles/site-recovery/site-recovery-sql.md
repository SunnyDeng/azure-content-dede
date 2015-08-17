<properties 
	pageTitle="Notfallwiederherstellung mit SQL Server und Azure Site Recovery" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung von SQL Server zu einen lokalen Standort oder zu Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# Notfallwiederherstellung mit SQL Server und Azure Site Recovery 

Site Recovery (ein Azure-Dienst) bereichert Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung durch die Orchestrierung von Replikation, Failover und Wiederherstellung Ihrer virtuellen Computer und physischen Server. Site Recovery unterstützt eine Reihe von Replikationsmechanismen für konsistenten Schutz und einheitliche Replikations- und Failovervorgänge zu Azure oder zu einem sekundären Datencenter. Eine Übersicht über alle Bereitstellungsszenarien für Azure Site Recovery finden Sie [hier](site-recovery-overview.md).

 In diesem Artikel erfahren Sie, wie Sie das SQL Server-Back-End einer Anwendung mit einer Kombination aus SQL Server-Technologien für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) und Site Recovery schützen. Die in diesem Artikel beschriebenen Szenarien setzen fundierte Kenntnisse über die BCDR-Features von SQL Server (Failoverclustering, AlwaysOn-Verfügbarkeitsgruppen, Datenbankspiegelung, Protokollversand) und Site Recovery voraus.



## Übersicht

SQL Server bildet die Grundlage für viele Workloads. Anwendungen wie SharePoint, Dynamics und SAP verwenden SQL Server zur Implementierung von Datendiensten. SQL Server bietet Features für Hochverfügbarkeit und Notfallwiederherstellung. Hierzu zählen etwa SQL Server-Verfügbarkeitsgruppen für den Schutz und die Wiederherstellung von SQL Server-Datenbanken.

Site Recovery kann zum Schutz von SQL Server verwendet werden, wenn SQL Server als virtueller Hyper-V-Computer, als virtueller VMware-Computer oder als physischer Server ausgeführt wird.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>Physischer Server</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Lokal zu lokal</td>
		<td>Lokal zu Azure</td>
		<td>Lokal zu lokal</td>
		<td>Lokal zu Azure</td>
		<td>Lokal zu lokal</td>
		<td>Lokal zu Azure</td>
    </tr>
	<tr align="left" valign="top">
		<td>Ja</td>
		<td>Ja</td>
		<td>Ja</td>
		<td>In Kürze verfügbar</td>
		<td>Ja</td>
		<td>In Kürze verfügbar</td>
    </tr>
    </tbody>
    </table>

## Unterstützung und Integration

Site Recovery kann zur Bereitstellung einer Notfallwiederherstellungslösung mit systemeigenen BCDR-Technologien von SQL Server kombiniert werden:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Feature</b></td>
		<td><b>Details</b></td>
		<td><b>SQL&#160;Server-Version</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>AlwaysOn-Verfügbarkeitsgruppen</b></td>
		<td><p>Mehrere eigenständige Instanzen von SQL&#160;Server werden jeweils in einem Failovercluster mit mehreren Knoten ausgeführt.</p> <p>Datenbanken können in Failovergruppen zusammengefasst werden, die in SQL&#160;Server-Instanzen kopiert (gespiegelt) werden können, sodass kein freigegebener Speicher erforderlich ist.</p> <p>Ermöglicht die Notfallwiederherstellung zwischen einem primären und einem oder mehreren sekundären Standorten. Zwei Knoten können in einem Shared-Nothing-Cluster mit SQL&#160;Server-Datenbanken in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover eingerichtet werden.</p></td>
		<td>SQL Server 2014/2012 Enterprise</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>Failoverclustering (AlwaysOn-FCI)</b></td>
		<td><p>SQL&#160;Server nutzt Windows-Failoverclustering, um eine hohe Verfügbarkeit der lokalen SQL&#160;Server-Workloads zu erreichen.</p><p>Knoten, auf denen SQL&#160;Server-Instanzen mit freigegebenen Datenträgern ausgeführt werden, werden in einem Failovercluster konfiguriert. Fällt eine Instanz aus, wird für den Cluster ein Failover zu einer anderen Instanz durchgeführt.</p> <p>Der Cluster schützt nicht vor Fehlern oder Ausfällen im freigegebenen Speicher. Der freigegebene Datenträger kann mit iSCSI, Fibre Channel oder freigegebenen VHDX-Dateien implementiert werden.</p></td>
		<td><p>SQL Server Enterprise</p> <p>SQL Server Standard (auf zwei Knoten beschränkt)</p></td>
	<tr align="left" valign="top">
		<td><b>Datenbankspiegelung im Modus für hohe Sicherheit</b></td>
		<td>Schützt eine einzelne Datenbank mittels einer sekundären Kopie. Replikationsmodi mit hoher Sicherheit (synchron) oder mit hoher Leistung (asynchron) verfügbar. Kein Failovercluster erforderlich.</td>
		<td><p>SQL Server 2008 R2</p><p>SQL Server Enterprise (alle Editionen)</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>Eigenständige SQL&#160;Server-Instanz</b></td>
		<td>SQL&#160;Server und die Datenbank werden auf einem einzelnen Server (physisch oder virtuell) gehostet. Bei einem virtuellen Server wird Hostclustering verwendet, um eine hohe Verfügbarkeit zu gewährleisten. Keine hohe Verfügbarkeit auf Gastebene.</td>
		<td>Enterprise oder Standard</td>
 
    </tbody>
    </table>

Die folgende Tabelle enthält unsere Empfehlungen für die Integration von SQL Server-BCDR-Technologien in die Site Recovery-Bereitstellung:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Version</b></td>
		<td><b>Edition</b></td>
		<td><b>Bereitstellung</b></td>
		<td><b>Lokal zu lokal</b></td>
		<td><b>Lokal zu Azure</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 oder 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>Failoverclusterinstanz</td>
		<td>AlwaysOn-Verfügbarkeitsgruppen</td>
		<td>AlwaysOn-Verfügbarkeitsgruppen</td>
    </tr>
	<tr align="left" valign="top">
		<td>AlwaysOn-Verfügbarkeitsgruppen für hohe Verfügbarkeit</td>
		<td>AlwaysOn-Verfügbarkeitsgruppe</td>
		<td>AlwaysOn-Verfügbarkeitsgruppe</td>
    </tr>
	<tr align="left" valign="top">
		<td>Standard</td>
		<td>Failoverclusterinstanz</td>
		<td>Site&#160;Recovery-Replikation mit lokaler Spiegelung</td>
		<td>Site&#160;Recovery-Replikation mit lokaler Spiegelung</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise oder Standard</td>
		<td>Eigenständig</td>
		<td>Site&#160;Recovery-Replikation mit lokaler Spiegelung</td>
		<td>Site&#160;Recovery-Replikation mit lokaler Spiegelung</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise oder Standard</td>
		<td>Eigenständig</td>
		<td>Site&#160;Recovery-Replikation mit lokaler Spiegelung</td>
		<td>Site&#160;Recovery-Replikation mit lokaler Spiegelung</td>
    </tr>
    </tbody>
    </table>


## Voraussetzungen für die Bereitstellung

Sie benötigen Folgendes:


- Eine lokale SQL Server-Bereitstellung mit einer unterstützten SQL Server-Version. In der Regel benötigen Sie auch ein Active Directory für SQL Server.
- Die erforderlichen Komponenten für das gewünschte Bereitstellungsszenario. Informationen zu den erforderlichen Komponenten finden Sie im jeweiligen Bereitstellungsartikel. Die Links dazu finden Sie in der [Übersicht über Site Recovery](site-recovery-overview.md).
- Wenn Sie die Wiederherstellung in Azure einrichten möchten, müssen Sie auf Ihren virtuellen SQL Server-Computern das [Tool zur Ermittlung der Bereitschaft virtueller Azure-Computer](http://www.microsoft.com/download/details.aspx?id=40898) ausführen, um sicherzustellen, dass diese mit Azure und Site Recovery kompatibel sind.

## Einrichten des Schutzes

Führen Sie die folgenden Schritte durch:

- Einrichten von Active Directory
- Einrichten des Schutzes für einen SQL Server-Cluster oder eigenständigen Server

### Einrichten von Active Directory

Für den ordnungsgemäßen Betrieb von SQL Server muss Active Directory am sekundären Wiederherstellungsstandort vorhanden sein. Verfügbare Optionen:

- **Kleine Unternehmen**: Wenn nur eine geringe Anzahl von Anwendungen und ein einzelner Domänencontroller für den lokalen Standort vorhanden sind und Sie ein Failover für den gesamten Standort durchführen möchten, empfiehlt es sich, den Domänencontroller mithilfe der Site Recovery-Replikation zum sekundären Datencenter oder zu Azure zu replizieren.

- **Mittlere bis große Unternehmen**: Wenn Sie über eine hohe Anzahl von Anwendung verfügen, eine Active Directory-Gesamtstruktur verwenden und ein anwendungs- oder workloadspezifisches Failover durchführen möchten, empfiehlt sich die Einrichtung eines zusätzlichen Domänencontrollers im sekundären Datencenter oder in Azure. Hinweis: Wenn Sie für die Wiederherstellung an einem Remotestandort AlwaysOn-Verfügbarkeitsgruppen verwenden, empfiehlt es sich, einen weiteren zusätzlichen Domänencontroller für den sekundären Standort oder für Azure einzurichten, der für die wiederhergestellte SQL Server-Instanz verwendet werden kann.

Bei den Anweisungen in diesem Dokument wird davon ausgegangen, dass am sekundären Standort ein Domänencontroller verfügbar ist.

### Einrichten des Schutzes für eine eigenständige SQL Server-Instanz


In dieser Konfiguration empfiehlt es sich, den SQL Server-Computer mithilfe der Site Recovery-Replikation zu schützen. Welche Schritte dafür konkret erforderlich sind, hängt davon ab, ob SQL Server als virtueller Computer oder als physischer Server eingerichtet ist und ob Sie Azure oder einen sekundären lokalen Standort als Replikationsziel verwenden möchten. Informationen zu allen Bereitstellungsszenarien finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).


### Einrichten des Schutzes für SQL Server-Cluster (2012 oder 2014 Enterprise)

Wenn die SQL Server-Instanz Verfügbarkeitsgruppen für hohe Verfügbarkeit verwendet (oder eine Failoverclusterinstanz), empfiehlt es sich, auch am Wiederherstellungsstandort Verfügbarkeitsgruppen zu verwenden. Diese Empfehlung gilt für Anwendungen, die keine verteilten Transaktionen verwenden.

##### Lokal zu lokal

1. [Konfigurieren Sie Datenbanken](https://msdn.microsoft.com/library/hh213078.aspx) in Verfügbarkeitsgruppen.
2. Erstellen Sie ein neues virtuelles Netzwerk am sekundären Standort.
3. Richten Sie ein Standort-zu-Standort-VPN zwischen dem neuen virtuellen Netzwerk und dem primären Standort ein.
4. Erstellen Sie einen virtuellen Computer am Wiederherstellungsstandort, und installieren Sie SQL Server.
5. Erweitern Sie die vorhandenen AlwaysOn-Verfügbarkeitsgruppen auf den neuen virtuellen SQL Server-Computer. Konfigurieren Sie diese SQL Server-Instanz als asynchrone Replikatkopie.
6. Erstellen Sie einen Verfügbarkeitsgruppenlistener, oder aktualisieren Sie den vorhandenen Listener, sodass er den asynchronen virtuellen Replikatcomputer enthält.
7. Stellen Sie sicher, dass die Anwendungsfarm mit dem Listener konfiguriert ist. Sollte die Farm mit dem Namen des Datenbankservers konfiguriert sein, aktualisieren Sie sie so, dass sie den Listener verwendet. Andernfalls muss dieser Schritt nach dem Failover durchgeführt werden.

#### Lokal zu Azure

Bei einer Replikation zu Azure kann das Konfigurieren mehrerer Verfügbarkeitsgruppen zur Herausforderung werden, da jede Verfügbarkeitsgruppe einen dedizierten Listener benötigt und für die Konfiguration der einzelnen Listener jeweils ein separater Clouddienst erforderlich ist. Es empfiehlt sich daher, eine einzelne Verfügbarkeitsgruppe zu konfigurieren, die alle Datenbanken enthält.

1. Erstellen Sie ein virtuelles Azure-Netzwerk.
2. Richten Sie ein Standort-zu-Standort-VPN zwischen dem lokalen Standort und diesem Netzwerk ein.
3. Erstellen Sie im Netzwerk einen neuen virtuellen Azure-Computer mit SQL Server, und konfigurieren Sie ihn als asynchrones Verfügbarkeitsgruppenreplikat. Wenn die SQL Server-Ebene nach dem Failover zu Azure hochverfügbar sein muss, konfigurieren Sie in Azure zwei asynchrone Replikatkopien.
4. Richten Sie im virtuellen Netzwerk ein Replikat des Domänencontrollers ein.
5. Stellen Sie sicher, dass auf dem virtuellen Computer VM-Erweiterungen aktiviert sind. Diese werden für die Push-Übertragung SQL Server-spezifischer Skripts in einem Wiederherstellungsplan benötigt.
6. Konfigurieren Sie einen SQL Server-Listener für die Verfügbarkeitsgruppe mit dem internen Lastenausgleich von Azure.
7. Konfigurieren Sie die Anwendungsebene so, dass der Listener für den Zugriff auf die Datenbankebene verwendet wird. Für Anwendungen, die verteilte Transaktionen verwenden, empfiehlt sich die Verwendung von Site Recovery mit SAN-Replikation oder VMWare-Standort-zu-Standort-Replikation.

### Einrichten des Schutzes für SQL Server-Cluster (Standard oder 2008 R2)

Bei einem Cluster mit SQL Server Standard Edition oder SQL Server 2008 R2 empfiehlt es sich, SQL Server mit der Site Recovery-Replikation zu schützen.

#### Lokal zu lokal

- In einer Hyper-V-Umgebung mit einer Anwendung, die verteilte Transaktionen verwendet, empfiehlt sich die Bereitstellung von [Site Recovery mit SAN-Replikation](site-recovery-vmm-san.md).

- In einer VMware-Umgebung empfiehlt sich die Bereitstellung des Schutzes vom Typ [VMware zu VMware](site-recovery-vmware-to-vmware.md).

#### Lokal zu Azure

Bei der Replikation zu Azure unterstützt die Standortwiederherstellung keine Gastcluster. Für die Standard-Edition von SQL Server steht zudem keine kostengünstige Notfallwiederherstellungslösung zur Verfügung. Es empfiehlt sich, den lokalen SQL Server-Cluster mit einer eigenständigen SQL Server-Instanz zu schützen und die Wiederherstellung in Azure vorzunehmen.


1. Konfigurieren Sie eine zusätzliche eigenständige SQL Server-Instanz am lokalen Standort.
2. Konfigurieren Sie diese Instanz so, dass sie als Spiegelung für die zu schützenden Datenbanken fungiert. Konfigurieren Sie die Spiegelung im Modus für hohe Sicherheit.
3.	Konfigurieren Sie Site Recovery am lokalen Standort auf der Grundlage der Umgebung ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) oder [VMware](site-recovery-vmware-to-azure.md)).
4.	Verwenden Sie die Site Recovery-Replikation, um die neue SQL Server-Instanz zu Azure zu replizieren. Da es sich hierbei um eine Spiegelkopie mit hoher Sicherheit handelt, wird sie mit dem primären Cluster synchronisiert. Bei der Replikation zu Azure wird allerdings die Site Recovery-Replikation verwendet.

Die folgende Abbildung veranschaulicht dieses Setup:

![Standardcluster](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##Erstellen von Wiederherstellungsplänen

Wiederherstellungspläne dienen zum Gruppieren von Computern, für die ein gemeinsames Failover durchgeführt werden soll. Machen Sie sich zunächst mit [Wiederherstellungsplänen](site-recovery-create-recovery-plans.md) und [Failover](site-recovery-failover.md) vertraut, bevor Sie fortfahren.


### Erstellen eines Wiederherstellungsplans für SQL Server-Cluster (SQL Server 2012/2014 Enterprise)

#### Konfigurieren von SQL Server-Skripts für das Failover zu Azure

In diesem Szenario nutzen wir benutzerdefinierte Skripts und Azure Automation für Wiederherstellungspläne, um ein geskriptetes Failover von SQL Server-Verfügbarkeitsgruppen zu konfigurieren.

1.	Erstellen Sie eine lokale Datei für das Failoverskript einer Verfügbarkeitsgruppe. Dieses Beispielskript gibt einen Pfad zur Verfügbarkeitsgruppe im Azure-Replikat an und führt ein Failover der Gruppe zu dieser Replikatinstanz durch. Dieses Skript wird auf dem virtuellen SQL Server-Replikatcomputer ausgeführt, indem es zusammen mit der Erweiterung des benutzerdefinierten Skripts übergeben wird.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Laden Sie das Skript an ein Blob im Azure-Speicherkonto hoch. Zu verwendendes Beispiel:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Erstellen Sie ein Azure Automation-Runbook, um die Skripts auf dem virtuellen SQL Server-Replikatcomputer in Azure aufzurufen. Verwenden Sie dazu dieses Beispielskript. Weitere Informationen zur Verwendung von Automation-Runbooks in Wiederherstellungsplänen finden Sie [hier](site-recovery-runbook-automation.md).

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	Fügen Sie beim Erstellen eines Wiederherstellungsplans für die Anwendung einen geskripteten Startschritt vor der ersten Gruppe ein, der das Automation-Runbook für das Failover von Verfügbarkeitsgruppen aufruft.

#### Konfigurieren von SQL Server-Skripts für das Failover zu einem sekundären Standort

1. Fügen Sie dieses Beispielskript der VMM-Bibliothek am primären und sekundären Standort hinzu.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Fügen Sie beim Erstellen eines Wiederherstellungsplans für die Anwendung einen geskripteten Startschritt vor der ersten Gruppe ein, der das Skript für das Failover von Verfügbarkeitsgruppen aufruft.

### Erstellen eines Wiederherstellungsplans für SQL Server-Cluster (Standard)

#### Konfigurieren von SQL Server-Skripts für das Failover zu Azure

1.	Erstellen Sie eine lokale Datei für das Failoverskript für die SQL Server-Datenbankspiegelung. Verwenden Sie das folgende Beispielskript:

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Laden Sie das Skript an ein Blob im Azure-Speicherkonto hoch. Verwenden Sie das folgende Beispielskript:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Erstellen Sie ein Azure Automation-Runbook, um das Skript auf dem virtuellen SQL Server-Replikatcomputer in Azure aufzurufen. Verwenden Sie dazu dieses Beispielskript. Weitere Informationen zur Verwendung von Automation-Runbooks in Wiederherstellungsplänen finden Sie [hier](site-recovery-runbook-automation.md). Vergewissern Sie sich zuvor, dass der VM-Agent nach dem Failover auf dem virtuellen SQL Server-Computer ausgeführt wird.

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. Fügen Sie dem Wiederherstellungsplan die folgenden Schritte hinzu, um ein Failover der SQL Server-Ebene durchzuführen:

	- Fügen Sie für ein geplantes Failover ein primärseitiges Skript hinzu, um den primären Cluster nach dem Herunterfahren der Gruppe herunterzufahren.
	- Fügen Sie dem Wiederherstellungsplan den virtuellen, für die Datenbankspiegelung verwendeten SQL Server-Computer hinzu (vorzugsweise in der ersten Startgruppe).
3.	Fügen Sie ein Skript hinzu, das nach dem Failover ausgeführt wird und innerhalb dieses virtuellen Computers mithilfe des obigen Automation-Skripts ein Failover der Spiegelkopie durchführt. Hinweis: Da sich der Name der Datenbankinstanz geändert hat, muss die Anwendungsebene für die Verwendung der neuen Datenbank konfiguriert werden.


#### Konfigurieren von SQL Server-Skripts für das Failover zu einem sekundären Standort

1.	Fügen Sie dieses Beispielskript der VMM-Bibliothek am primären und sekundären Standort hinzu.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Fügen Sie dem Wiederherstellungsplan den virtuellen, für die Datenbankspiegelung verwendeten SQL Server-Computer hinzu (vorzugsweise in der ersten Startgruppe).
3.	Fügen Sie ein Skript hinzu, das nach dem Failover ausgeführt wird und innerhalb dieses virtuellen Computers mithilfe des obigen VMM-Skripts ein Failover der Spiegelkopie durchführt. Hinweis: Da sich der Name der Datenbankinstanz geändert hat, muss die Anwendungsebene für die Verwendung der neuen Datenbank konfiguriert werden.





## Überlegungen zum Test-Failover

Bei Verwendung von AlwaysOn-Verfügbarkeitsgruppen ist kein Test-Failover der SQL Server-Ebene möglich. Alternativen:

- Option 1:

	1. Führen Sie ein Test-Failover für die Anwendungs- und die Front-End-Ebene durch.
	2. Aktualisieren Sie die Anwendungsebene für den Zugriff auf die Replikatkopie im schreibgeschützten Modus, und führen Sie einen schreibgeschützten Test der Anwendung durch.

- Option 2:
1.	Erstellen Sie eine Kopie der virtuellen SQL Server-Replikatcomputerinstanz (mit VMM-Klon bei Standort zu Standort oder mit Azure Backup), und machen Sie sie in einem Testnetzwerk verfügbar.
2.	Führen Sie das Test-Failover mit dem Wiederherstellungsplan durch.

## Überlegungen zum Failback

Bei SQL-Standardclustern ist für das Failback nach einem nicht geplanten Failover eine SQL Server-Sicherung und -Wiederherstellung der Spiegelinstanz im ursprünglichen Cluster und eine anschließende Wiederherstellung der Spiegelung erforderlich.





 

<!---HONumber=August15_HO6-->