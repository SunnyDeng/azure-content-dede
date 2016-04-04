<properties
	pageTitle="Replizieren virtueller Hyper-V-Computer in VMM-Clouds mithilfe von Azure Site Recovery und PowerShell (Resource Manager) | Microsoft Azure"
	description="Replizieren von virtuellen Hyper-V-Computern in VMM-Clouds mithilfe von Azure Site Recovery und PowerShell"
	services="site-recovery"
	documentationCenter=""
	authors="Rajani-Janaki-Ram"
	manager="rochakm"
	editor="raynew"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="rajanaki"/>

# Replizieren virtueller Hyper-V-Computer in VMM-Clouds nach Azure mithilfe von PowerShell und Azure Resource Manager

> [AZURE.SELECTOR]
- [Klassisches Azure-Portal](site-recovery-vmm-to-azure.md)
- [PowerShell – klassisch](site-recovery-deploy-with-powershell.md)
- [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md) 


## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Eine vollständige Liste mit Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

In diesem Artikel erfahren Sie, wie Sie PowerShell zur Automatisierung häufiger Aufgaben verwenden, die Sie ausführen müssen, wenn Sie Azure Site Recovery zum Replizieren virtueller Hyper-V-Computer in System Center VMM-Clouds in Azure Storage einrichten.

Der Artikel enthält Voraussetzungen für das Szenario und zeigt Ihnen folgende Vorgänge:

- Einrichten eines Recovery Services-Tresors
- installieren des Azure Site Recovery-Anbieters auf dem VMM-Quellserver 
- Registrieren des Servers im Tresor, Hinzufügen eines Azure-Speicherkontos
- Installieren des Azure Recovery Services-Agents auf Hyper-V-Hostservern
- Konfigurieren von Schutzeinstellungen für VMM-Clouds, die auf alle geschützten virtuellen Computer angewendet werden 
- Aktivieren des Schutzes für diese virtuellen Computer 
- Testen des Failovers, um sicherzustellen, dass alles wie erwartet funktioniert

Sollten beim Einrichten dieses Szenarios Probleme auftreten, besuchen Sie das [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [AZURE.NOTE] Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager und klassische Bereitstellungen](../resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.

## Vorbereitung

Stellen Sie sicher, dass diese Voraussetzungen erfüllt werden:

### Voraussetzungen für Azure

- Sie benötigen ein [Microsoft Azure](https://azure.microsoft.com/)-Konto. Wenn Sie keins besitzen, beginnen Sie mit einem [kostenloses Konto](https://azure.microsoft.com/free). Darüber hinaus können Sie sich über die [Preisgestaltung für Azure Site Recovery-Manager](https://azure.microsoft.com/pricing/details/site-recovery/) informieren.
- Sie benötigen ein CSP-Abonnement, wenn Sie die Replikation zu einem CSP-Abonnementszenario ausführen möchten. Erfahren Sie mehr über das CSP-Programm unter [Registrieren für das CSP-Programm](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
- Sie benötigen ein Azure-Speicherkonto der Version 2 (ARM), um nach Azure replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement oder dem CSP-Abonnement zugeordnet sein. Weitere Informationen zum Einrichten von Azure-Speicher finden Sie unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md).
- Sie müssen sicherstellen, dass die virtuellen Computer, die Sie schützen möchten, den [Anforderungen an virtuelle Azure-Computer](site-recovery-best-practices.md#azure-virtual-machine-requirements) entsprechen.

> [AZURE.NOTE] Derzeit sind nur Vorgänge auf VM-Ebene über Powershell möglich. Die Unterstützung für Vorgänge auf Wiederherstellungsplanebene ist bald erhältlich. Derzeit können Sie Failover nur auf der Ebene von geschützten VMs und nicht auf der Ebene von Wiederherstellungsplänen durchführen.

### VMM-Voraussetzungen
- Sie benötigen einen VMM-Server, der unter System Center 2012 R2 ausgeführt wird.
- Auf allen VMM-Servern mit virtuellen Computern, die Sie schützen möchten, muss der Azure Site Recovery-Anbieter ausgeführt werden. Dieser wird bei der Bereitstellung von Azure Site Recovery installiert.
- Sie benötigen mindestens eine Cloud auf dem VMM-Server, den Sie schützen möchten. Die Cloud sollte Folgendes enthalten:
	- Eine oder mehrere VMM-Hostgruppen.
	- Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.
	- Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver.
- Erfahren Sie mehr über das Einrichten von VMM-Clouds:
	- Weitere Informationen über private VMM-Clouds erhalten Sie unter [Private Cloud-Neuerungen mit System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) und unter [VMM 2012 und die Clouds](http://go.microsoft.com/fwlink/?LinkId=324956).
	- Informationen zum [Planen der VMM-Infrastruktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Sobald Ihre Cloudfabricelemente eingerichtet sind, finden Sie Informationen zum Erstellen privater Clouds unter [Erstellen einer privaten Cloud in VMM](http://go.microsoft.com/fwlink/p/?LinkId=324953) und [Exemplarische Vorgehensweise: Erstellen von privaten Clouds mit System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).


### Hyper-V-Voraussetzungen

- Auf den Hyper-V-Hostservern muss mindestens Windows Server 2012 mit Hyper-V-Rolle ausgeführt werden, und die neuesten Updates müssen installiert sein.
- Wenn Sie Hyper-V in einem Cluster ausführen, wird der Clusterbroker nicht automatisch erstellt, wenn Sie einen Cluster mit statischen IP-Adressen verwenden. Sie müssen den Clusterbroker manuell konfigurieren. Weitere 
- Anweisungen finden Sie unter [How to Configure Hyper-V Replica Broker](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx) (Konfigurieren des Hyper-V-Replikatbrokers).
- Alle Hyper-V-Hostserver oder Cluster, deren Schutz Sie verwalten möchten, müssen in einer VMM-Cloud enthalten sein.

### Voraussetzungen für die Netzwerkzuordnung
Wenn Sie virtuelle Computer in Azure schützen, wird eine Netzwerkzuordnung zwischen VM-Netzwerken auf dem Quell-VMM-Server und den Azure-Zielnetzwerken erstellt, um Folgendes zu ermöglichen:

- Alle Computer, die ein Failover in demselben Netzwerk durchführen, können sich unabhängig vom jeweiligen Wiederherstellungsplan miteinander verbinden.
- Wenn ein Netzwerkgateway im Azure-Zielnetzwerk eingerichtet ist, können die virtuellen Computer eine Verbindung zu anderen lokalen virtuellen Computern herstellen.
- Wenn Sie keine Netzwerkzuordnung konfigurieren, können nur die virtuellen Computer, für die ein Failover in demselben Wiederherstellungsplan erfolgt, nach einem Failover auf Azure eine Verbindung miteinander herstellen.

Wenn Sie eine Netzwerkzuordnung bereitstellen möchten, benötigen Sie Folgendes:

- Die virtuellen Computer, die Sie auf dem VMM-Quellserver schützen möchten, sollten mit einem VM-Netzwerk verbunden sein. Dieses Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
- Ein Azure-Netzwerk, mit dem replizierte virtuelle Computer nach einem Failover eine Verbindung herstellen können. Dieses Netzwerk wählen Sie zum Zeitpunkt des Failovers aus. Das Netzwerk sollte sich in derselben Region wie Ihr Azure Site Recovery-Abonnement befinden.

Informationen zur Netzwerkzuordnung finden Sie unter:

- [Konfigurieren von logischen Netzwerken in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386307)
- [Konfiguration von VM-Netzwerken und Gateways in VMM](http://go.microsoft.com/fwlink/p/?LinkId=386308)
- [Konfigurieren und Überwachen virtueller Netzwerke in Azure](https://azure.microsoft.com/documentation/services/virtual-network/)


###PowerShell-Voraussetzungen
Stellen Sie sicher, dass Azure PowerShell einsatzbereit ist. Wenn Sie PowerShell bereits verwenden, müssen Sie auf Version 0.8.10 oder höher aktualisieren. Informationen zum Einrichten von PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Nach dem Einrichten und Konfigurieren von PowerShell können Sie alle verfügbaren Cmdlets für den Dienst [hier](https://msdn.microsoft.com/library/dn850420.aspx) anzeigen.

Tipps für die Verwendung von Cmdlets, z. B. wie Parameterwerte, Eingaben und Ausgaben in der Regel in Azure PowerShell behandelt werden, finden Sie unter [Erste Schritte mit Azure-Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Schritt 1: Festlegen des Abonnements 

1. Melden Sie sich über Azure PowerShell mithilfe der folgenden Cmdlets beim Azure-Konto an.
 
		$UserName = "<user@live.com>"
		$Password = "<password>"
		$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
		$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
		Login-AzureRmAccount #-Credential $Cred 
	

2. Rufen Sie eine Liste Ihrer Abonnements ab. Dadurch werden auch die subscriptionIDs für die einzelnen Abonnements aufgeführt. Notieren Sie sich die subscriptionID des Abonnements, in dem Sie den Recovery Services-Tresor erstellen möchten.

		Get-AzureRmSubscription 

3. Legen Sie das Abonnement fest, in dem der Recovery Services-Tresor erstellt werden soll, indem Sie die Abonnement-ID angeben.

		Set-AzureRmContext –SubscriptionID <subscriptionId>


## Schritt 2: Erstellen eines Recovery Services-Tresors

1. Falls noch keine vorhanden ist, erstellen Sie eine ARM-Ressourcengruppe.

		New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location

2. Erstellen Sie einen neuen Recovery Services-Tresor, und speichern Sie das erstellte ASR-Tresorobjekt in einer Variablen (die später verwendet wird). Sie können das ASR-Tresorobjekt auch nach der Erstellung abrufen, indem Sie das Cmdlet „Get-AzureRMRecoveryServicesVault“ verwenden:

		$vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location 

## Schritt 3: Generieren eines Tresorregistrierungsschlüssels

Generieren Sie einen Registrierungsschlüssel im Tresor. Nachdem Sie den Azure Site Recovery-Anbieter heruntergeladen und auf dem VMM-Server installiert haben, verwenden Sie diesen Schlüssel, um den VMM-Server im Tresor zu registrieren.

1.	Rufen Sie die Tresoreinstellungsdatei ab, und legen Sie den Kontext fest:
	

		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault vaultname -Path #VaultSettingFilePath
	
	
2.	Legen Sie den Tresorkontext durch Ausführen der folgenden Befehle fest:
	
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath

## Schritt 4: Installieren des Azure Site Recovery-Anbieters

1.	Erstellen Sie ein Verzeichnis auf dem VMM-Computer durch Ausführen des folgenden Befehls:
	
		New-Item c:\ASR -type directory
		
2.	Extrahieren Sie die Dateien mithilfe des heruntergeladenen Anbieters durch Ausführen des folgenden Befehls.
	
		pushd C:\ASR\
		.\AzureSiteRecoveryProvider.exe /x:. /q

	
3.	Installieren Sie den Anbieter mithilfe der folgenden Befehls:
	
		.\SetupDr.exe /i
		$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
		do
		{
		                $isNotInstalled = $true;
		                if(Test-Path $installationRegPath)
		                {
		                                $isNotInstalled = $false;
		                }
		}While($isNotInstalled)

    Warten Sie, bis die Installation abgeschlossen ist.
	
4.	Registrieren Sie den Server beim Tresor mithilfe des folgenden Befehls:
	
		$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
		pushd $BinPath
		$encryptionFilePath = "C:\temp".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

	
## Schritt 5: Erstellen eines Azure-Speicherkontos

1. Wenn Sie kein Azure-Speicherkonto haben, erstellen Sie durch Ausführen des folgenden Befehls ein Konto für die Georeplikation in derselben geografischen Region, in der sich der Tresor befindet:

		$StorageAccountName = "teststorageacc1"	#StorageAccountname
		$StorageAccountGeo  = "Southeast Asia" 	
		$ResourceGroupName =  “myRG” 			#ResourceGroupName 
		$RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Beachten Sie, dass sich das Speicherkonto in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein muss.

## Schritt 6: Installieren des Azure Recovery Services-Agent

1. Laden Sie den Azure Recovery Services-Agent unter [http:/aka.ms/latestmarsagent](http:/aka.ms/latestmarsagent "http:/aka.ms/latestmarsagent") herunter, und installieren Sie ihn auf jedem Hyper-V-Hostserver in den VMM-Clouds, die Sie schützen möchten.

2. Führen Sie den folgenden Befehl auf allen VMM-Hosts aus.

	marsagentinstaller.exe /q /nu

## Schritt 7: Konfigurieren der Cloudschutzeinstellungen

1.	Erstellen Sie eine Replikationsrichtlinie für Azure durch Ausführen des folgenden Befehls:

	
		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points 

		$policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

2.	Rufen Sie einen Schutzcontainer durch Ausführen der folgenden Befehle ab:
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
  
3.	Rufen Sie mithilfe des erstellten Auftrags die Richtliniendetails in eine Variable ab, indem Sie den Anzeigenamen der Richtlinie angeben:

		$policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname

4.	Starten Sie die Zuordnung des Schutzcontainers mit der Replikationsrichtlinie:

		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  

5.	Nachdem der Auftrag abgeschlossen ist, führen Sie den folgenden Befehl aus:
   
		$job = Get-AzureRmSiteRecoveryJob -Job $associationJob
   		if($job -eq $null -or $job.StateDescription -ne "Completed")
   		 {
        $isJobLeftForProcessing = $true;
    	}

6.	Nachdem die Verarbeitung des Auftrags abgeschlossen ist, führen Sie den folgenden Befehl aus:

		if($isJobLeftForProcessing)
    	{
    	Start-Sleep -Seconds 60
    	}
        }While($isJobLeftForProcessing)

Um den Abschluss des Vorgangs zu überprüfen, führen Sie die Schritte in [Überwachen der Aktivität](#monitor) aus.

## Schritt 8: Konfigurieren der Netzwerkzuordnung

Bevor Sie mit der Netzwerkzuordnung beginnen, stellen Sie sicher, dass virtuelle Computer auf dem VMM-Quellserver mit einem VM-Netzwerk verbunden sind. Erstellen Sie außerdem ein oder mehrere virtuelle Azure-Netzwerke.

Weitere Informationen zum Erstellen eines virtuellen Netzwerks über Azure Resource Manager und PowerShell finden Sie unter [Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung mit Azure Resource Manager und PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell/).

Beachten Sie, dass einem einzelnen Azure-Netzwerk mehrere VM-Netzwerke zugeordnet werden können. Wenn das Zielnetzwerk mehrere Subnetze enthält und eines dieser Subnetze den gleichen Namen besitzt wie das Subnetz des virtuellen Quellcomputers, wird der virtuelle Replikatcomputer nach dem Failover mit diesem Zielsubnetz verbunden. Gibt es kein Zielsubnetz mit einem übereinstimmenden Namen, wird der virtuelle Computer mit dem ersten Subnetz im Netzwerk verbunden.

1. Der erste Befehl ruft Server für den aktuellen Azure Site Recovery-Tresor ab. Der Befehl speichert die Microsoft Azure Site Recovery-Server in der "$Servers"-Arrayvariablen.

		$Servers = Get-AzureRmSiteRecoveryServer

2. Der zweite Befehl ruft das Site Recovery-Netzwerk für den ersten Server im "$Servers"-Array ab. Der Befehl speichert die Netzwerke in der "$Networks"-Variablen.


    	$Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

3. Der dritte Befehl ruft virtuelle Azure-Netzwerke ab und speichert dann diesen Wert in der Variablen „$AzureVmNetworks“.
   
		$AzureVmNetworks =  Get-AzureRmVirtualNetwork

4. Das letzte Cmdlet erstellt eine Zuordnung zwischen dem primären Netzwerk und dem virtuellen Azure-Computernetzwerk. Das Cmdlet gibt das primäre Netzwerk als erstes Element von "$Networks" an. Das Cmdlet gibt ein Netzwerk für virtuelle Computer als erstes Element von $AzureVmNetworks an.

		New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]


## Schritt 9: Aktivieren des Schutzes für virtuelle Computer

Nach der korrekten Konfiguration der Server, Clouds und Netzwerke können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren.

 Beachten Sie Folgendes:

 - Die virtuellen Computer müssen die Azure-Anforderungen erfüllen. Unter [Voraussetzungen und Support](../site-recovery-best-practices) im Planungshandbuch können Sie diese prüfen.

 - Um den Schutz zu aktivieren, müssen die Eigenschaften „Betriebssystem“ und „Betriebssystem-Datenträger“ für den virtuellen Computer festgelegt sein. Sie können diese Eigenschaften setzen, wenn Sie den virtuellen Computer in VMM mithilfe einer Vorlage für virtuelle Computer erstellen. Außerdem können Sie diese Eigenschaften für vorhandene virtuelle Computer auf den Registerkarten **Allgemein** und **Hardwarekonfiguration** in den Eigenschaften der virtuellen Computer festlegen. Falls diese Eigenschaften in VMM nicht angezeigt werden, sollten Sie sie dennoch im Azure Site Recovery-Portal konfigurieren können.


  1. Führen Sie den folgenden Befehl aus, um den Schutzcontainer abzurufen und den Schutz zu aktivieren:
	
			$ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
	
  2. Rufen Sie die Schutzentität (VM) durch Ausführen des folgenden Befehls ab:
	
	 		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
		
  3. Aktivieren Sie den DR für den virtuellen Computer, indem Sie den folgenden Befehl ausführen:

			$jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1



## Testen der Bereitstellung

Um Ihre Bereitstellung zu testen, können Sie ein Testfailover für eine einzelne virtuellen Maschine durchführen oder einen Wiederherstellungsplan erstellen, der mehrere virtuelle Maschinen umfasst, und ein Testfailover für diesen Plan durchführen. Das Testfailover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk. Beachten Sie Folgendes:

- Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remotedesktop herstellen möchten, aktivieren Sie die Remotedesktopverbindung auf dem virtuellen Computer, bevor Sie das Testfailover ausführen.
- Nach dem Failover verwenden Sie eine öffentliche IP-Adresse, um eine Verbindung mit dem virtuellen Computer in Azure über Remotedesktop herzustellen. Wenn Sie dies möchten, stellen Sie sicher, dass keine Domänenrichtlinien vorhanden sind, die das Verbinden zu einem virtuellen Computer über eine öffentliche Adresse verhindern.

Um den Abschluss des Vorgangs zu überprüfen, führen Sie die Schritte in [Überwachen der Aktivität](#monitor) aus.


### Ausführen eines Testfailovers

1.	Starten Sie dann das Testfailover, indem Sie den folgenden Befehl ausführen:
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### Ausführen eines geplanten Failovers

1. Starten Sie dann das geplante Failover, indem Sie den folgenden Befehl ausführen:
	
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### Ausführen eines ungeplanten Failovers

1. Starten Sie dann das geplante Failover, indem Sie den folgenden Befehl ausführen:
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

		$jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

	
## <a name=monitor></a> Überwachen der Aktivität

Verwenden Sie die folgenden Befehle zum Überwachen der Aktivität. Beachten Sie, dass Sie zwischen den Aufträgen auf den Abschluss der Verarbeitung warten müssen.

	Do
	{
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        	$isJobLeftForProcessing = $true;
        }

	if($isJobLeftForProcessing)
        {
        	Start-Sleep -Seconds 60
        }
	}While($isJobLeftForProcessing)



## Nächste Schritte

[Erfahren Sie mehr](https://msdn.microsoft.com/library/dn850420.aspx) über PowerShell-Cmdlets für Azure Site Recovery</a>.

<!---HONumber=AcomDC_0323_2016-->