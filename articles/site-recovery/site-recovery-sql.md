<properties 
	pageTitle="Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery | Microsoft Azure" 
	description="In diesem Artikel wird beschrieben, wie Sie SQL Server mit Azure Site Recovery bzw. SQL Server-Funktionen für die Notfallwiederherstellung replizieren." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/14/2016" 
	ms.author="raynew"/>


# Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery 


Der Dienst Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem sekundären lokalen Datencenter repliziert werden. Eine kurze Übersicht Eine kurze Übersicht über das Gesamtthema finden Sie unter [Was ist Azure Site Recovery?](site-recovery-overview.md).

 In diesem Artikel erfahren Sie, wie Sie das SQL Server-Back-End einer Anwendung mit einer Kombination aus SQL Server-Technologien für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) und Azure Site Recovery schützen. Die in diesem Artikel beschriebenen Szenarien setzen fundierte Kenntnisse zu den Funktionen für die Notfallwiederherstellung von SQL Server (Failoverclustering, AlwaysOn-Verfügbarkeitsgruppen, Datenbankspiegelung, Protokollversand) und Azure Site Recovery voraus.



## Übersicht

SQL Server bildet die Grundlage für viele Workloads. Anwendungen wie SharePoint, Dynamics und SAP verwenden SQL Server zur Implementierung von Datendiensten. Anwendungen stellen SQL Server auf verschiedene Arten bereit:

- **Eigenständiger SQL-Server:** SQL Server und alle Datenbanken werden auf einem einzelnen Computer gehostet (physisch oder virtuell). Bei einer Virtualisierung wird Hostclustering verwendet, um die lokale hohe Verfügbarkeit sicherzustellen. Es wird keine hohe Verfügbarkeit für die Gastebene implementiert.
- **SQL Server-Failoverclustering-Instanzen (Always ON FCI):** Zwei oder mehr Knoten mit SQL-Serverinstanzen und freigegebenen Datenträgern werden in einem Windows-Failovercluster konfiguriert. Wenn eine der Clusterinstanzen nicht betriebsbereit ist, kann der Cluster das Failover für SQL Server zu einer anderen Instanz durchführen. Dieses Setup wird normalerweise zur Sicherstellung der hohen Verfügbarkeit am primären Standort verwendet. Es stellt keinen Schutz vor Fehlern oder einem Ausfall auf der Ebene des freigegebenen Speichers dar. Ein freigegebener Datenträger kann per ISCSI, Fibre Channel oder Shared VHDx implementiert werden.
- **SQL AlwaysOn-Verfügbarkeitsgruppen:** Bei diesem Setup werden zwei Knoten in einem Cluster ohne Freigabe mit SQL Server-Datenbanken eingerichtet, die in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover konfiguriert sind.

In Enterprise-Editionen stellt SQL Server systemeigene Technologie zur Notfallwiederherstellung bereit, um Datenbanken an einem Remotestandort wiederherzustellen. In diesem Artikel nutzen wir diese native SQL-Technologie für die Notfallwiederherstellung und führen die Integration durch:

- SQL AlwaysOn-Verfügbarkeitsgruppen zur Notfallwiederherstellung für SQL Server 2012 oder 2014 Enterprise-Editionen
- SQL-Datenbankspiegelung im Hochsicherheitsmodus für SQL Server Standard Edition (beliebige Version) oder SQL Server 2008 R2


Site Recovery kann zum Schützen von SQL Server verwendet werden. Dies ist in der Tabelle unten zusammengefasst.

 |**Lokal zu lokal** | **Lokal zu Azure** 
---|---|---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja 
**Physischer Server** | Ja | Ja


## Unterstützung und Integration

Diese SQL Server-Versionen werden von den Szenarien in diesem Artikel unterstützt:


- SQL Server 2014 Enterprise und Standard
- SQL Server 2012 Enterprise und Standard
- SQL Server 2008 R2 Enterprise und Standard


Site Recovery kann zur Bereitstellung einer Notfallwiederherstellungslösung mit systemeigener BCDR-Technologie von SQL Server kombiniert werden. Dies ist in der folgenden Tabelle zusammengefasst:

**Feature** |**Details** | **SQL Server-Version** 
---|---|---
**AlwaysOn-Verfügbarkeitsgruppe** | Mehrere eigenständige Instanzen von SQL Server werden jeweils in einem Failovercluster mit mehreren Knoten ausgeführt.<br/><br/>Datenbanken können in Failovergruppen zusammengefasst werden, die in SQL Server-Instanzen kopiert (gespiegelt) werden können, sodass kein freigegebener Speicher erforderlich ist.<br/><br/>Ermöglicht die Notfallwiederherstellung zwischen einem primären und einem oder mehreren sekundären Standorten. Zwei Knoten können in einem Shared-Nothing-Cluster mit SQL Server-Datenbanken in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover eingerichtet werden. | SQL Server 2014 und 2012 Enterprise Edition
**Failoverclustering (AlwaysOn-FCI)** | SQL Server nutzt das Windows-Failoverclustering für hohe Verfügbarkeit der lokalen SQL Server-Workloads.<br/><br/>Knoten, auf denen Instanzen von SQL Server mit freigegebenen Datenträgern ausgeführt werden, werden in einem Failovercluster konfiguriert. Fällt eine Instanz aus, wird für den Cluster ein Failover zu einer anderen Instanz durchgeführt.<br/><br/>Der Cluster schützt nicht vor Fehlern oder Ausfällen im freigegebenen Speicher. Der freigegebene Datenträger kann mit iSCSI, Fibre Channel oder freigegebenen VHDX-Dateien implementiert werden. | SQL Server Enterprise Editions<br/><br/>SQL Server Standard Edition (auf zwei Knoten beschränkt)
**Datenbankspiegelung (Modus für hohe Sicherheit)** | Schützt eine einzelne Datenbank mittels einer sekundären Kopie. Replikationsmodi mit hoher Sicherheit (synchron) oder mit hoher Leistung (asynchron) verfügbar. Kein Failovercluster erforderlich. | SQL Server 2008 R2<br/><br/>SQL Server Enterprise (alle Editionen)
**Eigenständige SQL Server-Instanz** | SQL Server und die Datenbank werden auf einem einzelnen Server (physisch oder virtuell) gehostet. Bei einem virtuellen Server wird Hostclustering verwendet, um eine hohe Verfügbarkeit zu gewährleisten. Keine hohe Verfügbarkeit auf Gastebene. | Enterprise oder Standard

## Bereitstellungsempfehlungen


Diese Tabelle enthält unsere Empfehlungen für die Integration von SQL Server-BCDR-Technologien in Site Recovery:

**Version** |**Edition** | **Bereitstellung** | **Lokal zu lokal** | **Lokal zu Azure** 
---|---|---|---|---
SQL Server 2014 oder 2012 | Enterprise | Failoverclusterinstanz | AlwaysOn-Verfügbarkeitsgruppen | AlwaysOn-Verfügbarkeitsgruppen
| Enterprise | AlwaysOn-Verfügbarkeitsgruppen für hohe Verfügbarkeit | AlwaysOn-Verfügbarkeitsgruppen | AlwaysOn-Verfügbarkeitsgruppen
 | Standard | Failoverclusterinstanz (FCI) | Site Recovery-Replikation mit lokaler Spiegelung | Site Recovery-Replikation mit lokaler Spiegelung
 | Enterprise oder Standard | Eigenständig | Site Recovery-Replikation | Site Recovery-Replikation
SQL Server 2008 R2 | Enterprise oder Standard | Failoverclusterinstanz (FCI) | Site Recovery-Replikation mit lokaler Spiegelung | Site Recovery-Replikation mit lokaler Spiegelung
 | Enterprise oder Standard | Eigenständig | Site Recovery-Replikation | Site Recovery-Replikation
SQL Server (beliebige Version) | Enterprise oder Standard | Failoverclusterinstanz – DTC-Anwendung | Site Recovery-Replikation | Nicht unterstützt


## Voraussetzungen für die Bereitstellung

Sie benötigen Folgendes:

- Eine lokale SQL Server-Bereitstellung mit einer unterstützten SQL Server-Version. In der Regel benötigen Sie auch ein Active Directory für SQL Server.
- Die erforderlichen Komponenten für das gewünschte Bereitstellungsszenario. Informationen zu den erforderlichen Komponenten finden Sie im jeweiligen Bereitstellungsartikel. Entsprechende Links finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).
- Wenn Sie die Wiederherstellung in Azure einrichten möchten, müssen Sie auf Ihren virtuellen SQL Server-Computern das [Tool zur Ermittlung der Bereitschaft virtueller Azure-Computer](http://www.microsoft.com/download/details.aspx?id=40898) ausführen, um sicherzustellen, dass diese mit Azure und Site Recovery kompatibel sind.


## Einrichten von Active Directory

Für den ordnungsgemäßen Betrieb von SQL Server muss Active Directory am sekundären Wiederherstellungsstandort vorhanden sein. Verfügbare Optionen:

- **Kleine Unternehmen**: Wenn nur eine geringe Anzahl von Anwendungen und ein einzelner Domänencontroller für den lokalen Standort vorhanden sind und Sie ein Failover für den gesamten Standort durchführen möchten, empfiehlt es sich, den Domänencontroller mithilfe der Site Recovery-Replikation zum sekundären Datencenter oder zu Azure zu replizieren.

- **Mittlere bis große Unternehmen**: Wenn Sie über eine hohe Anzahl von Anwendung verfügen, eine Active Directory-Gesamtstruktur verwenden und ein anwendungs- oder workloadspezifisches Failover durchführen möchten, empfiehlt sich die Einrichtung eines zusätzlichen Domänencontrollers im sekundären Datencenter oder in Azure. Hinweis: Wenn Sie für die Wiederherstellung an einem Remotestandort AlwaysOn-Verfügbarkeitsgruppen verwenden, empfiehlt es sich, einen weiteren zusätzlichen Domänencontroller für den sekundären Standort oder für Azure einzurichten, der für die wiederhergestellte SQL Server-Instanz verwendet werden kann.

Bei den Anweisungen in diesem Dokument wird davon ausgegangen, dass am sekundären Standort ein Domänencontroller verfügbar ist. Lesen Sie die [weiteren Informationen](site-recovery-active-directory.md) zum Schützen von Active Directory mit Site Recovery.

## Integrieren des Schutzes in SQL Server AlwaysOn (lokal unter Azure)

### Schützen von Hyper-V-VMs in VMM-Clouds

Site Recovery bietet native Unterstützung von SQL AlwaysOn. Wenn Sie eine SQL-Verfügbarkeitsgruppe mit einer virtuellen Azure-Maschine erstellt haben, die als „Sekundär“ eingerichtet ist, können Sie anschließend mithilfe von Site Recovery das Failover der Verfügbarkeitsgruppen verwalten.

>[AZURE.NOTE] Diese Funktion befindet sich derzeit in der Vorschauphase und ist verfügbar, wenn Hyper-V-Hostserver im primären Rechenzentrum in VMM-Clouds verwaltet werden.

#### Voraussetzungen

Hier sind die Voraussetzungen angegeben, die Sie zum Integrieren von SQL AlwaysOn in Site Recovery benötigen, wenn Sie die Replikation von VMM durchführen:

- Lokale SQL Server-Instanz (eigenständiger Server oder Failovercluster).
- Mindestens ein virtueller Azure-Computer, auf dem SQL Server installiert ist.
- Eine SQL-Verfügbarkeitsgruppe, die zwischen der lokalen SQL Server-Instanz und der unter Azure ausgeführten SQL Server-Instanz eingerichtet ist.
- PowerShell-Remoting muss für den lokalen SQL Server-Computer aktiviert sein. Der VMM-Server muss PowerShell-Remoteaufrufe an die SQL Server-Instanz richten können.
- Für die lokale SQL Server-Instanz muss ein Benutzerkonto in SQL-Benutzergruppen hinzugefügt werden, und zwar mindestens mit den folgenden Berechtigungen:
	- ALTER AVAILABILITY GROUP: Berechtigungen [hier](https://msdn.microsoft.com/library/hh231018.aspx) und [hier](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: Berechtigungen [hier](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Ein RunAs-Konto muss auf dem VMM-Server für das Konto aus dem vorherigen Schritt erstellt werden.
- Das SQL PS-Modul muss für SQL Server-Instanzen installiert werden, die lokal oder in virtuellen Azure-Maschinen ausgeführt werden.
- Der VM-Agent muss auf virtuellen Maschinen installiert werden, die in Azure ausgeführt werden.
- NTAUTHORITY\\System benötigt folgende Berechtigungen für eine in virtuellen Maschinen unter Azure ausgeführte SQL Server-Instanz:
	- ALTER AVAILABILITY GROUP: Berechtigungen [hier](https://msdn.microsoft.com/library/hh231018.aspx) und [hier](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE: Berechtigungen [hier](https://msdn.microsoft.com/library/ff877956.aspx#Security)

####  Schritt 1: Hinzufügen einer SQL Server-Instanz


1. Klicken Sie auf **SQL hinzufügen**, um eine neue SQL Server-Instanz hinzuzufügen. 

	![SQL hinzufügen](./media/site-recovery-sql/add-sql.png)

2. Geben Sie unter **SQL Server-Einstellungen konfigurieren** > **Name** einen Anzeigenamen für die SQL Server-Instanz ein.
3. Geben Sie unter **SQL Server (FQDN)** den FQDN der SQL Server-Quellinstanz an, die Sie hinzufügen möchten. Für den Fall, dass die SQL Server-Instanz in einem Failovercluster installiert ist, geben Sie den FQDN des Clusters und nicht den eines der Clusterknoten an.  
4. Wählen Sie unter **SQL Server-Instanz** die Standardinstanz aus, oder geben Sie den Namen der benutzerdefinierten Instanz an.
5. Wählen Sie unter **VMM-Server** einen VMM-Server aus, der im Site Recovery-Tresor registriert ist. Site Recovery nutzt diesen VMM-Server für die Kommunikation mit der SQL Server-Instanz.
6. Geben Sie unter **Als Konto ausführen** den Namen eines RunAs-Kontos an, das auf dem angegebenen VMM-Server erstellt wurde. Dieses Konto wird verwendet, um auf die SQL Server-Instanz zuzugreifen, und muss über die Berechtigungen „Lesen“ und „Failover“ für Verfügbarkeitsgruppen auf diesem SQL Server-Computer verfügen.

	![Dialogfeld "SQL hinzufügen"](./media/site-recovery-sql/add-sql-dialog.png)

Nachdem Sie die SQL Server-Instanz hinzugefügt haben, wird sie auf der Registerkarte **Server mit SQL Server** angezeigt.

![SQL Server-Liste](./media/site-recovery-sql/sql-server-list.png)


#### Schritt 2: Hinzufügen einer SQL-Verfügbarkeitsgruppe

1. Nach dem Hinzufügen des SQL Server-Computers ist der nächste Schritt das Hinzufügen der Verfügbarkeitsgruppen zu Site Recovery. Führen Sie hierfür eine Detailsuche innerhalb der im vorherigen Schritt hinzugefügten SQL Server-Instanz durch, und klicken Sie dann auf "SQL-Verfügbarkeitsgruppe hinzufügen". 

	![SQL-Verfügbarkeitsgruppe hinzufügen](./media/site-recovery-sql/add-sqlag.png)

2. Eine SQL-Verfügbarkeitsgruppe kann in einen oder mehrere virtuelle Computer in Azure repliziert werden. Beim Hinzufügen der SQL-Verfügbarkeitsgruppe müssen Sie den Namen und das Abonnement des virtuellen Azure-Computers angeben, auf den durch Site Recovery das Failover der Verfügbarkeitsgruppe erfolgen soll.

	![Dialogfeld "SQL-Verfügbarkeitsgruppe hinzufügen"](./media/site-recovery-sql/add-sqlag-dialog.png)

3. Bei einem Failover würde im obigen Beispiel die Verfügbarkeitsgruppe DB1-AG zur primären Verfügbarkeitsgruppe auf dem virtuellen Computer SQLAGVM2 im Abonnement DevTesting2.

>[AZURE.NOTE] Nur die Verfügbarkeitsgruppen, die in der im vorigen Schritt hinzugefügten SQL Server-Instanz primär sind, stehen für das Hinzufügen zu Site Recovery zur Verfügung. Wenn Sie in der SQL Server-Instanz eine Verfügbarkeitsgruppe als primär eingestuft haben oder der SQL Server-Instanz nach deren Hinzufügen mehrere Verfügbarkeitsgruppen hinzugefügt haben, aktualisieren Sie sie über die SQL Server-Option "Aktualisieren".

#### Schritt 3: Erstellen eines Wiederherstellungsplans

Der nächste Schritt ist die Erstellung eines Wiederherstellungsplans mit virtuellen Computern und Verfügbarkeitsgruppen. Wählen Sie denselben in Schritt 1 verwendeten VMM-Server als Quelle und Microsoft Azure als Ziel aus.

![Wiederherstellungsplan erstellen](./media/site-recovery-sql/create-rp1.png)

![Wiederherstellungsplan erstellen](./media/site-recovery-sql/create-rp2.png)

In diesem Beispiel besteht die SharePoint-Anwendung aus drei virtuellen Computern, die eine SQL-Verfügbarkeitsgruppe als Back-End verwenden. Bei diesem Wiederherstellungsplan können Sie sowohl die Verfügbarkeitsgruppe als auch den virtuellen Computer auswählen, die die Anwendung bilden.

Sie können den Wiederherstellungsplan weiter anpassen, indem Sie virtuelle Computer in verschiedene Failovergruppen verschieben, um die Reihenfolge des Failovers festzulegen. Für die Verfügbarkeitsgruppe erfolgt das Failover immer zuerst, da sie als Back-End einer beliebigen Anwendung verwendet wird.

![Wiederherstellungsplan anpassen](./media/site-recovery-sql/customize-rp.png)

### Schritt 4: Failover

Andere Failoveroptionen sind verfügbar, nachdem eine Verfügbarkeitsgruppe einem Wiederherstellungsplan hinzugefügt wurde.

Failover | Details
--- | ---
**Geplantes Failover** | Ein geplantes Failover impliziert ein Failover ohne Datenverlust. Hierfür wird der Verfügbarkeitsmodus der SQL-Verfügbarkeitsgruppe zunächst auf „Synchron“ festgelegt. Dann wird ein Failover ausgelöst, um die Verfügbarkeitsgruppe auf dem bereitgestellten virtuellen Computer als „Primär“ zu markieren, während die Verfügbarkeitsgruppe Site Recovery hinzugefügt wird. Sobald das Failover abgeschlossen ist, wird der Verfügbarkeitsmodus auf den gleichen Wert wie vor dem Auslösen des geplanten Failovers festgelegt.
**Nicht geplantes Failover** | Ein ungeplantes Failover kann zum Verlust von Daten führen. Beim Auslösen eines ungeplanten Failovers wird der Verfügbarkeitsmodus der Verfügbarkeitsgruppe nicht geändert, und diese wird als „Primär“ auf der bereitgestellten virtuellen Maschine markiert, während die Verfügbarkeitsgruppe Site Recovery hinzugefügt wird. Sobald ein ungeplantes Failover abgeschlossen ist und der lokale Server mit SQL Server wieder zur Verfügung steht, muss für die Verfügbarkeitsgruppe "Replikation rückgängig machen" ausgelöst werden. Beachten Sie, dass diese Aktion für den Wiederherstellungsplan nicht verfügbar ist und für die SQL-Verfügbarkeitsgruppe auf der Registerkarte "SQL Server" ausgeführt werden kann.
**Test-Failover** | Ein Testfailover für SQL-Verfügbarkeitsgruppen wird nicht unterstützt. Wenn Sie ein Testfailover für einen Wiederherstellungsplan mit einer SQL-Verfügbarkeitsgruppe auslösen, wird das Failover für die Verfügbarkeitsgruppe übersprungen.


Sehen Sie sich diese Failoveroptionen an.

Option | Details
--- | ---
**Option 1:** | 1\. Führen Sie ein Testfailover für die Anwendungs- und die Front-End-Ebene durch.<br/><br/>2. Aktualisieren Sie die Anwendungsebene für den Zugriff auf die Replikatkopie im schreibgeschützten Modus, und führen Sie einen schreibgeschützten Test der Anwendung durch.
**Option 2** | 1\. Erstellen Sie eine Kopie der virtuellen SQL Server-Replikatcomputerinstanz (mit VMM-Klon bei Standort zu Standort oder mit Azure Backup), und machen Sie sie in einem Testnetzwerk verfügbar.<br/><br/>2. Führen Sie das Test-Failover mit dem Wiederherstellungsplan durch.

Schritt 5: Failback

Wenn Sie die Verfügbarkeitsgruppe für die lokale SQL Server-Instanz wieder als „Primär“ festlegen möchten, müssen Sie dazu ein geplantes Failover für den Wiederherstellungsplan auslösen und die Richtung von Microsoft Azure zum lokalen VMM-Server wählen.

>[AZURE.NOTE] Nach einem ungeplanten Failover muss die umgekehrte Replikation für die Verfügbarkeitsgruppe ausgelöst werden, um die Replikation fortzusetzen. Bis dahin bleibt die Replikation unterbrochen.



### Schützen von Computern ohne VMM

Für Umgebungen, die nicht von einem VMM-Server verwaltet werden, können Azure Automation-Runbooks verwendet werden, um ein skriptgesteuertes Failover von SQL-Verfügbarkeitsgruppen zu konfigurieren. Es folgen die entsprechenden Schritte:

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

## Integrieren von Schutz mit SQL AlwaysOn (lokal zu lokal)

Wenn die SQL Server-Instanz Verfügbarkeitsgruppen für hohe Verfügbarkeit verwendet (oder eine Failoverclusterinstanz), empfiehlt es sich, auch am Wiederherstellungsstandort Verfügbarkeitsgruppen zu verwenden. Diese Empfehlung gilt für Anwendungen, die keine verteilten Transaktionen verwenden.

1. [Konfigurieren Sie Datenbanken](https://msdn.microsoft.com/library/hh213078.aspx) in Verfügbarkeitsgruppen.
2. Erstellen Sie ein neues virtuelles Netzwerk am sekundären Standort.
3. Richten Sie ein Standort-zu-Standort-VPN zwischen dem neuen virtuellen Netzwerk und dem primären Standort ein.
4. Erstellen Sie einen virtuellen Computer am Wiederherstellungsstandort, und installieren Sie SQL Server.
5. Erweitern Sie die vorhandenen AlwaysOn-Verfügbarkeitsgruppen auf den neuen virtuellen SQL Server-Computer. Konfigurieren Sie diese SQL Server-Instanz als asynchrone Replikatkopie.
6. Erstellen Sie einen Verfügbarkeitsgruppenlistener, oder aktualisieren Sie den vorhandenen Listener, sodass er den asynchronen virtuellen Replikatcomputer enthält.
7. Stellen Sie sicher, dass die Anwendungsfarm mit dem Listener konfiguriert ist. Sollte die Farm mit dem Namen des Datenbankservers konfiguriert sein, aktualisieren Sie sie so, dass sie den Listener verwendet. Andernfalls muss dieser Schritt nach dem Failover durchgeführt werden.

Für Anwendungen, die verteilte Transaktionen verwenden, empfiehlt sich die Verwendung von [Site Recovery mit SAN-Replikation](site-recovery-vmm-san.md) oder [Standort-zu-Standort-Replikation für VMware- oder physische Server](site-recovery-vmware-to-vmware.md).

### Überlegungen zum Wiederherstellungsplan

1. Fügen Sie dieses Beispielskript der VMM-Bibliothek am primären und sekundären Standort hinzu.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Fügen Sie beim Erstellen eines Wiederherstellungsplans für die Anwendung einen geskripteten Startschritt vor der ersten Gruppe ein, der das Skript für das Failover von Verfügbarkeitsgruppen aufruft.



## Schützen einer eigenständigen SQL Server-Instanz

In dieser Konfiguration empfiehlt es sich, den SQL Server-Computer mithilfe der Site Recovery-Replikation zu schützen. Welche Schritte dafür konkret erforderlich sind, hängt davon ab, ob SQL Server als virtueller Computer oder als physischer Server eingerichtet ist und ob Sie Azure oder einen sekundären lokalen Standort als Replikationsziel verwenden möchten. Informationen zu allen Bereitstellungsszenarien finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).


## Schützen eines SQL Server-Clusters (Standard oder 2008 R2)

Bei einem Cluster mit SQL Server Standard Edition oder SQL Server 2008 R2 empfiehlt es sich, SQL Server mit der Site Recovery-Replikation zu schützen.

### Lokal zu lokal

- Für eine Anwendung, die verteilte Transaktionen verwendet, empfiehlt sich die Bereitstellung von [Site Recovery mit SAN-Replikation](site-recovery-vmm-san.md) für eine Hyper-V-Umgebung und von [VMware-/physischem Server zu VMware](site-recovery-vmware-to-vmware.md) für eine VMware-Umgebung.

- Nutzen Sie für Nicht-DTC-Anwendungen den obigen Ansatz, um den Cluster als eigenständigen Server wiederherzustellen, indem Sie eine lokale DB-Hochsicherheitsspiegelung verwenden.

### Lokal zu Azure

Bei der Replikation zu Azure unterstützt die Standortwiederherstellung keine Gastcluster. Für die Standard-Edition von SQL Server steht zudem keine kostengünstige Notfallwiederherstellungslösung zur Verfügung. Es empfiehlt sich, den lokalen SQL Server-Cluster mit einer eigenständigen SQL Server-Instanz zu schützen und die Wiederherstellung in Azure vorzunehmen.


1. Konfigurieren Sie eine zusätzliche eigenständige SQL Server-Instanz am lokalen Standort.
2. Konfigurieren Sie diese Instanz so, dass sie als Spiegelung für die zu schützenden Datenbanken fungiert. Konfigurieren Sie die Spiegelung im Modus für hohe Sicherheit.
3.	Konfigurieren Sie Site Recovery am lokalen Standort auf Grundlage der Umgebung ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) oder [VMware-/physischer Server](site-recovery-vmware-to-azure-classic.md)).
4.	Verwenden Sie die Site Recovery-Replikation, um die neue SQL Server-Instanz zu Azure zu replizieren. Da es sich hierbei um eine Spiegelkopie mit hoher Sicherheit handelt, wird sie mit dem primären Cluster synchronisiert. Bei der Replikation zu Azure wird allerdings die Site Recovery-Replikation verwendet.

Die folgende Abbildung veranschaulicht dieses Setup:

![Standardcluster](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Überlegungen zum Failback

Bei SQL-Standardclustern ist für das Failback nach einem nicht geplanten Failover eine SQL-Sicherung und -Wiederherstellung der Spiegelinstanz im ursprünglichen Cluster und eine Wiederherstellung der Spiegelung erforderlich.

## Nächste Schritte
Lesen Sie die [weiteren Informationen](site-recovery-best-practices.md) zur Vorbereitung auf die Bereitstellung von Site Recovery.










 

<!---HONumber=AcomDC_0218_2016-->