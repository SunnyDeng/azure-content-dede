<properties 
	pageTitle="Notfallwiederherstellung mit SQL Server und Azure Site Recovery | Microsoft Azure" 
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
	ms.date="11/18/2015" 
	ms.author="raynew"/>


# Notfallwiederherstellung mit SQL Server und Azure Site Recovery 

Site Recovery (ein Azure-Dienst) bereichert Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung durch die Orchestrierung von Replikation, Failover und Wiederherstellung Ihrer virtuellen Computer und physischen Server. Site Recovery unterstützt eine Reihe von Replikationsmechanismen für konsistenten Schutz und einheitliche Replikations- und Failovervorgänge zu Azure oder zu einem sekundären Datencenter. Eine Übersicht über alle Bereitstellungsszenarien für Azure Site Recovery finden Sie [hier](site-recovery-overview.md).

 In diesem Artikel erfahren Sie, wie Sie das SQL Server-Back-End einer Anwendung mit einer Kombination aus SQL Server-Technologien für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) und Site Recovery schützen. Die in diesem Artikel beschriebenen Szenarien setzen fundierte Kenntnisse über die BCDR-Features von SQL Server (Failoverclustering, AlwaysOn-Verfügbarkeitsgruppen, Datenbankspiegelung, Protokollversand) und Site Recovery voraus.



## Übersicht

SQL Server bildet die Grundlage für viele Workloads. Anwendungen wie SharePoint, Dynamics und SAP verwenden SQL Server zur Implementierung von Datendiensten. Features für Hochverfügbarkeit und Notfallwiederherstellung von SQL Server, z. B. SQL Server-Verfügbarkeitsgruppen, werden verwendet, um SQL Server-Datenbanken zu schützen und wiederherzustellen. Anwendungen stellen SQL Server in den folgenden Konfigurationen bereit:


1. Eigenständiger SQL-Server: Der SQL-Server und alle Datenbanken werden auf einem einzelnen Computer gehostet (physischer oder virtueller Computer). Bei einer Virtualisierung wird das Hostclustering für die lokale hohe Verfügbarkeit verwendet. Es wird keine hohe Verfügbarkeit für die Gastebene implementiert. 
2.	SQL Server-Failoverclustering-Instanzen (auch Always ON FCI): Bei diesem Setup werden zwei oder mehr Knoten mit SQL-Serverinstanzen mit freigegebenen Datenträgern in einem Windows-Failovercluster konfiguriert. Wenn eine SQL-Failovercluster-Instanz ausfällt, kann der Cluster für den SQL-Server ein Failover zu einer anderen Instanz durchführen. Dieses Setup wird normalerweise für die hohe Verfügbarkeit an einem primären Standort verwendet. Dies stellt keinen Schutz vor Fehlern oder einem Ausfall auf der Ebene des freigegebenen Speichers dar. Ein freigegebener Datenträger kann per ISCSI, Fibre Channel oder Shared VHDx implementiert werden.
3.	SQL Always ON-Verfügbarkeitsgruppen: Bei diesem Setup werden zwei Knoten in einem Cluster ohne Freigabe mit SQL-Datenbanken eingerichtet, die in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover konfiguriert sind.

Außerdem stellt SQL Server systemeigene Technologie zur Notfallwiederherstellung in Enterprise-Editionen bereit, um Datenbanken an einem Remotestandort wiederherzustellen. Wo dies erforderlich ist, werden die folgenden systemeigenen SQL-Technologien zur Notfallwiederherstellung genutzt und integriert, um einen auf Azure Site Recovery basierenden Plan für die Notfallwiederherstellung zu erstellen.


1. SQL Always On-Verfügbarkeitsgruppen zur Notfallwiederherstellung für SQL 2012 oder 2014 Enterprise-Editionen 
2.	SQL-Datenbankspiegelung im Hochsicherheitsmodus für SQL Server Standard (beliebige Version) oder SQL Server 2008 R2



Site Recovery kann zum Schutz von SQL Server verwendet werden, wenn SQL Server auf einem virtuellen Hyper-V-Computer, einem virtuellen VMware-Computer oder einem physischen Server ausgeführt wird.

 |**Lokal zu lokal** | **Lokal zu Azure** 
---|---|---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja 
**Physischer Server** | Ja | Ja


## Unterstützung und Integration

Unten sind die Versionen und Editionen von SQL Server angegeben, die für diesen Artikel gelten:


- SQL Server 2014 Enterprise und Standard
- SQL Server 2012 Enterprise und Standard
- SQL Server 2008 R2 Enterprise und Standard


Site Recovery kann zur Bereitstellung einer Notfallwiederherstellungslösung mit systemeigener BCDR-Technologie von SQL Server kombiniert werden. Dies ist in der folgenden Tabelle zusammengefasst:

**Feature** |**Details** | **SQL Server-Version** 
---|---|---
**AlwaysOn-Verfügbarkeitsgruppe** | <p>Mehrere eigenständige Instanzen von SQL Server werden jeweils in einem Failovercluster mit mehreren Knoten ausgeführt.</p> <p>Datenbanken können in Failovergruppen zusammengefasst werden, die in SQL Server-Instanzen kopiert (gespiegelt) werden können, sodass kein freigegebener Speicher erforderlich ist.</p> <p>Ermöglicht die Notfallwiederherstellung zwischen einem primären und einem oder mehreren sekundären Standorten. Zwei Knoten können in einem Shared-Nothing-Cluster mit SQL Server-Datenbanken in einer Verfügbarkeitsgruppe mit synchroner Replikation und automatischem Failover eingerichtet werden.</p> | SQL Server 2014/2012 Enterprise
**Failoverclustering (AlwaysOn-FCI)** | <p>SQL Server nutzt das Windows-Failoverclustering für hohe Verfügbarkeit der lokalen SQL Server-Workloads.</p><p>Knoten, auf denen Instanzen von SQL Server mit freigegebenen Datenträgern ausgeführt werden, werden in einem Failovercluster konfiguriert. Fällt eine Instanz aus, wird für den Cluster ein Failover zu einer anderen Instanz durchgeführt.</p> <p>Der Cluster schützt nicht vor Fehlern oder Ausfällen im freigegebenen Speicher. Der freigegebene Datenträger kann mit iSCSI, Fibre Channel oder freigegebenen VHDX-Dateien implementiert werden.</p> | SQL Server Enterprise-Editionen</p> <p>SQL Server Standard (auf zwei Knoten beschränkt)
**Datenbankspiegelung (Modus für hohe Sicherheit)** | Schützt eine einzelne Datenbank mittels einer sekundären Kopie. Replikationsmodi mit hoher Sicherheit (synchron) oder mit hoher Leistung (asynchron) verfügbar. Kein Failovercluster erforderlich. | <p>SQL Server 2008 R2</p><p>SQL Server Enterprise (alle Editionen)</p>
**Eigenständige SQL Server-Instanz** | SQL Server und die Datenbank werden auf einem einzelnen Server (physisch oder virtuell) gehostet. Bei einem virtuellen Server wird Hostclustering verwendet, um eine hohe Verfügbarkeit zu gewährleisten. Keine hohe Verfügbarkeit auf Gastebene. | Enterprise oder Standard




Die folgende Tabelle enthält unsere Empfehlungen für die Integration von SQL Server-BCDR-Technologien in die Site Recovery-Bereitstellung:

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
- Die erforderlichen Komponenten für das gewünschte Bereitstellungsszenario. Informationen zu den erforderlichen Komponenten finden Sie im jeweiligen Bereitstellungsartikel. Die Links dazu finden Sie in der [Übersicht über Site Recovery](site-recovery-overview.md).
- Wenn Sie die Wiederherstellung in Azure einrichten möchten, müssen Sie auf Ihren virtuellen SQL Server-Computern das [Tool zur Ermittlung der Bereitschaft virtueller Azure-Computer](http://www.microsoft.com/download/details.aspx?id=40898) ausführen, um sicherzustellen, dass diese mit Azure und Site Recovery kompatibel sind.


## Einrichten des Schutzes von AD

Für den ordnungsgemäßen Betrieb von SQL Server muss Active Directory am sekundären Wiederherstellungsstandort vorhanden sein. Verfügbare Optionen:

- **Kleine Unternehmen**: Wenn nur eine geringe Anzahl von Anwendungen und ein einzelner Domänencontroller für den lokalen Standort vorhanden sind und Sie ein Failover für den gesamten Standort durchführen möchten, empfiehlt es sich, den Domänencontroller mithilfe der Site Recovery-Replikation zum sekundären Datencenter oder zu Azure zu replizieren.

- **Mittlere bis große Unternehmen**: Wenn Sie über eine hohe Anzahl von Anwendung verfügen, eine Active Directory-Gesamtstruktur verwenden und ein anwendungs- oder workloadspezifisches Failover durchführen möchten, empfiehlt sich die Einrichtung eines zusätzlichen Domänencontrollers im sekundären Datencenter oder in Azure. Hinweis: Wenn Sie für die Wiederherstellung an einem Remotestandort AlwaysOn-Verfügbarkeitsgruppen verwenden, empfiehlt es sich, einen weiteren zusätzlichen Domänencontroller für den sekundären Standort oder für Azure einzurichten, der für die wiederhergestellte SQL Server-Instanz verwendet werden kann.

Bei den Anweisungen in diesem Dokument wird davon ausgegangen, dass am sekundären Standort ein Domänencontroller verfügbar ist. Sie können [hier](http://aka.ms/asr-ad) auf den Leitfaden zur AD DR-Lösung zugreifen.

## Integration mit SQL AlwaysOn in Azure

### Lokal zu Azure

Azure Site Recovery (ASR) bietet systemeigene Unterstützung von SQL AlwaysOn. Wenn Sie eine SQL-Verfügbarkeitsgruppe mit einem virtuellen Azure-Computer erstellt haben, der als "Sekundär" eingerichtet ist, können Sie anschließend mithilfe von ASR das Failover der Verfügbarkeitsgruppen verwalten.

Diese Funktionalität ist derzeit in der Vorschauphase und verfügbar, wenn das primäre Datencenter von System Center Virtual Machine Manager (VMM) verwaltet wird.

#### Von einem VMM-Server verwaltete Umgebungen
Im ASR-Tresor sollte unter der Registerkarte "Geschützte Elemente" die Registerkarte "SQL Server" angezeigt werden.

![Geschützte Elemente](./media/site-recovery-sql/protected-items.png)

Es folgen die Schritte zum Integrieren von SQL AlwaysOn in ASR.

##### Voraussetzungen
- Lokale SQL Server-Instanz auf einem eigenständigen Server oder in einem Failovercluster. 
- Mindestens ein virtueller Azure-Computer, auf dem SQL Server installiert ist.
- SQL-Verfügbarkeitsgruppe, die zwischen der lokalen und in Azure ausgeführten SQL Server-Instanz eingerichtet ist.
- PowerShell-Remoting muss für die lokale SQL Server-Instanz aktiviert sein. Der VMM-Server muss PowerShell-Remoteaufrufe an SQL Server richten können.
- Für die lokale SQL Server-Instanz muss ein Benutzerkonto in SQL-Benutzergruppen mit mindestens den folgenden Berechtigungen hinzugefügt werden:
	- ALTER AVAILABILITY GROUP – [Verweis 1](https://msdn.microsoft.com/library/hh231018.aspx), [Verweis 2](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE – [Verweis 1](https://msdn.microsoft.com/library/ff877956.aspx#Security)
- Ein ausführendes Konto muss auf dem VMM-Server für das Konto aus dem vorherigen Schritt erstellt werden.
- Das SQL PS-Modul muss für SQL Server-Instanzen installiert werden, die lokal oder in virtuellen Azure-Computern ausgeführt werden.
- Der VM-Agent muss in virtuellen Computern installiert werden, die in Azure ausgeführt werden.
- NTAUTHORITY\\System benötigt folgende Berechtigungen für eine in virtuellen Computern in Azure ausgeführte SQL Server-Instanz:
	- ALTER AVAILABILITY GROUP – [Verweis 1](https://msdn.microsoft.com/library/hh231018.aspx), [Verweis 2](https://msdn.microsoft.com/library/ff878601.aspx#Anchor_3)
	- ALTER DATABASE – [Verweis 1](https://msdn.microsoft.com/library/ff877956.aspx#Security)

##### 1\. Hinzufügen einer SQL Server-Instanz

Klicken Sie auf "SQL hinzufügen", um eine neue SQL Server-Instanz hinzufügen.

![SQL hinzufügen](./media/site-recovery-sql/add-sql.png)

Geben Sie die Details der SQL Server-Instanz, von VMM und Anmeldeinformationen zum Verwalten der SQL Server-Instanz ein.

![Dialogfeld "SQL hinzufügen"](./media/site-recovery-sql/add-sql-dialog.png)

###### Parameter
 - Name: Anzeigename, den Sie zum Verweisen auf diese SQL Server-Instanz verwenden möchten
 - SQL Server (FQDN): Vollqualifizierter Domänenname (FQDN) der SQL Server-Quellinstanz, die Sie hinzufügen möchten. Für den Fall, dass die SQL Server-Instanz in einem Failovercluster installiert ist, geben Sie den FQDN des Clusters und nicht den eines der Clusterknoten an. 
 - SQL Server-Instanz: Wählen Sie die Standard-SQL-Instanz, oder geben Sie den Namen der benutzerdefinierten SQL Server-Instanz an.
 - VMM-Server: Wählen Sie einen der VMM-Server, der bereits bei Azure Site Recovery (ASR) registriert wurde. ASR nutzt diesen VMM-Server für die Kommunikation mit der SQL Server-Instanz.
 - Ausführendes Konto: Geben Sie den Namen eines der ausführenden Konten an, die auf dem oben ausgewählten VMM-Server erstellt wurden. Dieses ausführende Konto wird verwendet, um auf die SQL Server-Instanz zuzugreifen und muss über die Berechtigungen "Lesen" und "Failover" für Verfügbarkeitsgruppen in dieser SQL Server-Instanz verfügen. 

Nach dem Hinzufügen der SQL Server-Instanz wird sie auf der Registerkarte "SQL Server" angezeigt.

![SQL Server-Liste](./media/site-recovery-sql/sql-server-list.png)

##### 2\. Hinzufügen einer SQL-Verfügbarkeitsgruppe

Nach dem Hinzufügen der SQL Server-Instanz ist der nächste Schritt das Hinzufügen der Verfügbarkeitsgruppen zu ASR. Führen Sie hierfür eine Detailsuche innerhalb der im vorherigen Schritt hinzugefügten SQL Server-Instanz durch, und klicken Sie dann auf "SQL-Verfügbarkeitsgruppe hinzufügen".

![SQL-Verfügbarkeitsgruppe hinzufügen](./media/site-recovery-sql/add-sqlag.png)

Eine SQL-Verfügbarkeitsgruppe kann in einen oder mehrere virtuelle Computer in Azure repliziert werden. Beim Hinzufügen der SQL-Verfügbarkeitsgruppe müssen Sie den Namen und das Abonnement des virtuellen Azure-Computers angeben, auf den durch ASR das Failover der Verfügbarkeitsgruppe erfolgen soll.

![Dialogfeld "SQL-Verfügbarkeitsgruppe hinzufügen"](./media/site-recovery-sql/add-sqlag-dialog.png)

Bei einem Failover würde im obigen Beispiel die Verfügbarkeitsgruppe DB1-AG zur primären Verfügbarkeitsgruppe auf dem virtuellen Computer SQLAGVM2 im Abonnement DevTesting2.

>[AZURE.NOTE]Nur die Verfügbarkeitsgruppen, die in der im vorigen Schritt hinzugefügten SQL Server-Instanz primär sind, stehen für das Hinzufügen zu ASR zur Verfügung. Wenn Sie in der SQL Server-Instanz eine Verfügbarkeitsgruppe als primär eingestuft haben oder der SQL Server-Instanz nach deren Hinzufügen mehrere Verfügbarkeitsgruppen hinzugefügt haben, aktualisieren Sie sie über die SQL Server-Option "Aktualisieren".

#### 3\. Erstellen eines Wiederherstellungsplans

Der nächste Schritt ist die Erstellung eines Wiederherstellungsplans mit virtuellen Computern und Verfügbarkeitsgruppen. Wählen Sie denselben in Schritt 1 verwendeten VMM-Server als Quelle und Microsoft Azure als Ziel aus.

![Wiederherstellungsplan erstellen](./media/site-recovery-sql/create-rp1.png)

![Wiederherstellungsplan erstellen](./media/site-recovery-sql/create-rp2.png)

In diesem Beispiel besteht die SharePoint-Anwendung aus drei virtuellen Computern, die eine SQL-Verfügbarkeitsgruppe als Back-End verwenden. Bei diesem Wiederherstellungsplan können Sie sowohl die Verfügbarkeitsgruppe als auch den virtuellen Computer auswählen, die die Anwendung bilden.

Sie können den Wiederherstellungsplan weiter anpassen, indem Sie virtuelle Computer in verschiedene Failovergruppen verschieben, um die Reihenfolge des Failovers festzulegen. Für die Verfügbarkeitsgruppe erfolgt das Failover immer zuerst, da sie als Back-End einer beliebigen Anwendung verwendet wird.

![Wiederherstellungsplan anpassen](./media/site-recovery-sql/customize-rp.png)

#### 4\. Failover

Andere Failoveroptionen sind verfügbar, nachdem eine Verfügbarkeitsgruppe einem Wiederherstellungsplan hinzugefügt wurde.

##### Geplantes Failover

Ein geplantes Failover impliziert ein Failover ohne Datenverlust. Hierfür wird der Verfügbarkeitsmodus der SQL-Verfügbarkeitsgruppe zunächst auf "Synchron" festgelegt. Dann wird ein Failover ausgelöst, um die Verfügbarkeitsgruppe auf dem bereitgestellten virtuellen Computer als "Primär" zu markieren, während die Verfügbarkeitsgruppe zu ASR hinzugefügt wird. Sobald das Failover abgeschlossen ist, wird der Verfügbarkeitsmodus auf den gleichen Wert wie vor dem Auslösen des geplanten Failovers festgelegt.

##### Ungeplantes Failover

Ein ungeplantes Failover kann zum Verlust von Daten führen. Beim Auslösen eines ungeplanten Failovers wird der Verfügbarkeitsmodus der Verfügbarkeitsgruppe nicht geändert, und diese wird als "Primär" auf dem bereitgestellten virtuellen Computer markiert, während die Verfügbarkeitsgruppe zu ASR hinzugefügt wird. Sobald ein ungeplantes Failover abgeschlossen ist und der lokale Server mit SQL Server wieder zur Verfügung steht, muss für die Verfügbarkeitsgruppe "Replikation rückgängig machen" ausgelöst werden. Beachten Sie, dass diese Aktion für den Wiederherstellungsplan nicht verfügbar ist und für die SQL-Verfügbarkeitsgruppe auf der Registerkarte "SQL Server" ausgeführt werden kann.

##### Testfailover
Ein Testfailover für SQL-Verfügbarkeitsgruppen wird nicht unterstützt. Wenn Sie ein Testfailover für einen Wiederherstellungsplan mit einer SQL-Verfügbarkeitsgruppe auslösen, wird das Failover für die Verfügbarkeitsgruppe übersprungen.

Alternativen:

###### Option 1:



1. Führen Sie ein Test-Failover für die Anwendungs- und die Front-End-Ebene durch.

2. Aktualisieren Sie die Anwendungsebene für den Zugriff auf die Replikatkopie im schreibgeschützten Modus, und führen Sie einen schreibgeschützten Test der Anwendung durch.

###### Option 2:

1.	Erstellen Sie eine Kopie der virtuellen SQL Server-Replikatcomputerinstanz (mit VMM-Klon bei Standort zu Standort oder mit Azure Backup), und machen Sie sie in einem Testnetzwerk verfügbar.
2.	Führen Sie das Test-Failover mit dem Wiederherstellungsplan durch.

#### Failback

Wenn Sie die Verfügbarkeitsgruppe für die lokale SQL Server-Instanz wieder als "Primär" festlegen möchten, müssen Sie dazu ein geplantes Failover für den Wiederherstellungsplan auslösen und die Richtung von Microsoft Azure zum lokalen VMM-Server wählen.

#### Umgekehrte Replikation

Nach einem ungeplanten Failover muss die umgekehrte Replikation für die Verfügbarkeitsgruppe ausgelöst werden, um die Replikation fortzusetzen. Bis dahin bleibt die Replikation unterbrochen.


### Nicht per VMM-Server verwaltete Umgebungen

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

###Lokal zu lokal
Wenn die SQL Server-Instanz Verfügbarkeitsgruppen für hohe Verfügbarkeit verwendet (oder eine Failoverclusterinstanz), empfiehlt es sich, auch am Wiederherstellungsstandort Verfügbarkeitsgruppen zu verwenden. Diese Empfehlung gilt für Anwendungen, die keine verteilten Transaktionen verwenden.


1. [Konfigurieren Sie Datenbanken](https://msdn.microsoft.com/library/hh213078.aspx) in Verfügbarkeitsgruppen.
2. Erstellen Sie ein neues virtuelles Netzwerk am sekundären Standort.
3. Richten Sie ein Standort-zu-Standort-VPN zwischen dem neuen virtuellen Netzwerk und dem primären Standort ein.
4. Erstellen Sie einen virtuellen Computer am Wiederherstellungsstandort, und installieren Sie SQL Server.
5. Erweitern Sie die vorhandenen AlwaysOn-Verfügbarkeitsgruppen auf den neuen virtuellen SQL Server-Computer. Konfigurieren Sie diese SQL Server-Instanz als asynchrone Replikatkopie.
6. Erstellen Sie einen Verfügbarkeitsgruppenlistener, oder aktualisieren Sie den vorhandenen Listener, sodass er den asynchronen virtuellen Replikatcomputer enthält.
7. Stellen Sie sicher, dass die Anwendungsfarm mit dem Listener konfiguriert ist. Sollte die Farm mit dem Namen des Datenbankservers konfiguriert sein, aktualisieren Sie sie so, dass sie den Listener verwendet. Andernfalls muss dieser Schritt nach dem Failover durchgeführt werden.

Für Anwendungen, die verteilte Transaktionen verwenden, empfiehlt sich die Verwendung von [Site Recovery mit SAN-Replikation](site-recovery-vmm-san.md) oder [VMWare-Standort-zu-Standort-Replikation](site-recovery-vmware-to-vmware.md).

#### Überlegungen zum Wiederherstellungsplan

1. Fügen Sie dieses Beispielskript der VMM-Bibliothek am primären und sekundären Standort hinzu.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. Fügen Sie beim Erstellen eines Wiederherstellungsplans für die Anwendung einen geskripteten Startschritt vor der ersten Gruppe ein, der das Skript für das Failover von Verfügbarkeitsgruppen aufruft.



## Einrichten des Schutzes für einen eigenständigen SQL Server


In dieser Konfiguration empfiehlt es sich, den SQL Server-Computer mithilfe der Site Recovery-Replikation zu schützen. Welche Schritte dafür konkret erforderlich sind, hängt davon ab, ob SQL Server als virtueller Computer oder als physischer Server eingerichtet ist und ob Sie Azure oder einen sekundären lokalen Standort als Replikationsziel verwenden möchten. Informationen zu allen Bereitstellungsszenarien finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).


## Einrichten des Schutzes für einen SQL Server-Cluster (Standard oder 2008 R2)

Bei einem Cluster mit SQL Server Standard Edition oder SQL Server 2008 R2 empfiehlt es sich, SQL Server mit der Site Recovery-Replikation zu schützen.

#### Lokal zu lokal

- Für eine Anwendung, die verteilte Transaktionen verwendet, empfiehlt sich die Bereitstellung von [Site Recovery mit SAN-Replikation](site-recovery-vmm-san.md) für eine Hyper-V-Umgebung und von [VMware zu VMware](site-recovery-vmware-to-vmware.md) für eine VMware-Umgebung.

- Nutzen Sie für Nicht-DTC-Anwendungen den obigen Ansatz, um den Cluster als eigenständigen Server wiederherzustellen, indem Sie eine lokale DB-Hochsicherheitsspiegelung verwenden.

#### Lokal zu Azure

Bei der Replikation zu Azure unterstützt die Standortwiederherstellung keine Gastcluster. Für die Standard-Edition von SQL Server steht zudem keine kostengünstige Notfallwiederherstellungslösung zur Verfügung. Es empfiehlt sich, den lokalen SQL Server-Cluster mit einer eigenständigen SQL Server-Instanz zu schützen und die Wiederherstellung in Azure vorzunehmen.


1. Konfigurieren Sie eine zusätzliche eigenständige SQL Server-Instanz am lokalen Standort.
2. Konfigurieren Sie diese Instanz so, dass sie als Spiegelung für die zu schützenden Datenbanken fungiert. Konfigurieren Sie die Spiegelung im Modus für hohe Sicherheit.
3.	Konfigurieren Sie Site Recovery am lokalen Standort auf der Grundlage der Umgebung ([Hyper-V](site-recovery-hyper-v-site-to-azure.md) oder [VMware](site-recovery-vmware-to-azure.md)).
4.	Verwenden Sie die Site Recovery-Replikation, um die neue SQL Server-Instanz zu Azure zu replizieren. Da es sich hierbei um eine Spiegelkopie mit hoher Sicherheit handelt, wird sie mit dem primären Cluster synchronisiert. Bei der Replikation zu Azure wird allerdings die Site Recovery-Replikation verwendet.

Die folgende Abbildung veranschaulicht dieses Setup:

![Standardcluster](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)


### Überlegungen zum Failback

Bei SQL-Standardclustern ist für das Failback nach einem nicht geplanten Failover eine SQL-Sicherung und -Wiederherstellung der Spiegelinstanz im ursprünglichen Cluster und eine Wiederherstellung der Spiegelung erforderlich.










 

<!---HONumber=AcomDC_1210_2015-->