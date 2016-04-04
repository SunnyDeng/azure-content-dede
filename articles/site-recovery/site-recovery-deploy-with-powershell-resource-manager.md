<properties
	pageTitle="Schützen von Servern in Azure mithilfe von Azure PowerShell mit Azure-Ressourcen-Manager | Microsoft Azure"
	description="Automatisieren Sie den Schutz von Servern in Azure mit Azure Site Recovery mithilfe von PowerShell und Azure-Ressourcen-Manager."
	services="site-recovery"
	documentationCenter=""
	authors="bsiva"
	manager="abhiag"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="03/16/2016"
	ms.author="bsiva"/>

# Replizieren zwischen lokalen virtuellen Hyper-V-Computern und Azure mithilfe von PowerShell und Azure Resource Manager

> [AZURE.SELECTOR]
- [Klassisches Azure-Portal](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)



## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Eine vollständige Liste mit Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure durch Windows PowerShell enthält. Sie können zwei Arten von Modulen verwenden: das Azure-Profil oder den Azure-Ressourcen-Manager (ARM).

Mit PowerShell-Cmdlets für Azure Site Recovery (ASR), die mit Azure PowerShell für ARM verfügbar sind, können Sie Ihre Server in Azure schützen und wiederherstellen.

Dieser Artikel beschreibt anhand eines Beispiels, wie Sie Azure Site Recovery mithilfe von Windows PowerShell® und ARM bereitstellen, um Serverschutz in Azure zu konfigurieren und koordinieren. Das Beispiel in diesem Artikel zeigt Ihnen, wie Sie virtuelle Computer auf einem Hyper-V-Host mit Azure PowerShell mit ARM in Azure schützen, für sie ein Failover ausführen und sie wiederherstellen.

> [AZURE.NOTE] Die PowerShell-Cmdlets für Azure Site Recovery ermöglichen Ihnen derzeit, die Szenarien VMM-Standort-zu-VMM-Standort, VMM-Standort-zu-Azure und Hyper-V-Standort-zu-Azure zu konfigurieren. Unterstützung für andere ASR-Szenarien ist bald verfügbar.

Sie müssen kein PowerShell-Experte sein, um diesen Artikel verwenden zu können. Es wird jedoch vorausgesetzt, dass Sie grundlegend mit Konzepten wie Modulen, Cmdlets und Sitzungen vertraut sind. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).
- Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure Resource Manager](../powershell-azure-resource-manager.md).


## Wichtige Features

- Schützen Ihrer Server in Azure, Failoverausführung und Wiederherstellung in Azure IaaS (ARM) oder Azure IaaS (klassisch)
- Microsoft-Partner, die Teil des Cloud-Lösungsanbieterprogramms (Cloud Solution Provider, CSP) sind, können den Schutz der Server ihrer Kunden im CSP-Abonnement (Mandantenabonnement) des Kunden konfigurieren und verwalten.

## Vorbereitung

Stellen Sie sicher, dass diese Voraussetzungen erfüllt werden:

- Sie benötigen ein [Microsoft Azure](https://azure.microsoft.com/)-Konto. Für den Einstieg steht eine [kostenlose Testversion](pricing/free-trial/) zur Verfügung. Darüber hinaus können Sie sich über die [Preisgestaltung für Azure Site Recovery-Manager](https://azure.microsoft.com/pricing/details/site-recovery/) informieren.
- Sie benötigen Azure PowerShell 1.0. Informationen zu dieser Version, und wie Sie diese installieren, finden Sie unter [Azure PowerShell 1.0](https://azure.microsoft.com/).
- Sie müssen die Module [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) und [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) installieren. Sie erhalten die neuesten Versionen dieser Module im [PowerShell-Katalog](https://www.powershellgallery.com/).

In diesem Artikel wird das Verwenden von Azure Powershell mit ARM zum Konfigurieren und Verwalten des Schutzes Ihrer Server an einem Beispiel veranschaulicht. Das Beispiel in diesem Artikel zeigt Ihnen, wie Sie einen virtuellen Computer auf einem Hyper-V-Host in Azure schützen, und die folgenden Voraussetzungen gelten für dieses Beispiel. Eine umfassendere Aufstellung von Anforderungen für die verschiedenen ASR-Szenarien finden Sie in der Dokumentation zum jeweiligen Szenario.

- Sie benötigen einen Hyper-V-Host unter Windows Server 2012 R2 mit einem oder mehreren virtuellen Computern.
- Hyper-V-Server müssen entweder direkt oder über einen Proxy mit dem Internet verbunden sein.
- Die zu schützenden virtuellen Computer müssen die [Voraussetzungen für virtuelle Computer](site-recovery-best-practices.md#virtual-machines) erfüllen.
	

## Schritt 1: Melden Sie sich bei Ihrem Azure-Konto an.


1. Öffnen Sie eine PowerShell-Konsole, und führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Konto anzumelden. Das Cmdlet ruft eine Webseite auf, die Sie zur Eingabe Ihrer Kontoanmeldeinformationen auffordert.

    	Login-AzureRmAccount

	Alternativ können Sie Ihre Kontoanmeldeinformationen auch als Parameter des `Login-AzureRmAccount`-Cmdlets mit dem `-Credential`-Parameter einbeziehen.
	
	Wenn Sie als CSP-Partner für einen Mandanten tätig sind, müssen Sie den Kunden mit Mandanten-ID oder primärem Mandantendomänennamen als Mandanten angeben.

		Login-AzureRmAccount -Tenant "fabrikam.com"

2. Da ein Konto mehrere Abonnements enthalten kann, müssen Sie das Abonnement zuordnen, das Sie mit dem Konto verwenden möchten.

    	Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  Stellen Sie mit den folgenden Befehlen sicher, dass Ihr Abonnement zur Verwendung der Azure-Anbieter für Recovery Services und Site Recovery registriert ist:

	- `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
	-  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

	Wenn „RegistrationState“ in der Ausgabe der beiden obigen Befehle auf „Registered“ gesetzt ist, können Sie mit Schritt 2 fortfahren. Andernfalls müssen Sie den fehlenden Anbieter in Ihrem Abonnement registrieren.


	So registrieren Sie den Azure-Anbieter für Site Recovery:

    	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
	
	Auch wenn Sie die Recovery Services-Cmdlets zum ersten Mal in Ihrem Abonnement verwenden, müssen Sie den Azure-Anbieter für Recovery Services registrieren. Bevor Sie dies tun können, müssen Sie den Zugriff auf den Recovery Services-Anbieter in Ihrem Abonnement mit folgendem Befehl aktivieren:

		Register-AzureRmProviderFeature -FeatureName betaAccess -ProviderNamespace Microsoft.RecoveryServices

	>[AZURE.TIP] Es kann nach erfolgreicher Ausführung des obigen Befehls bis zu einer Stunde dauern, den Zugriff auf den Recovery Services-Anbieter in Ihrem Abonnement zu aktivieren. In der Zwischenzeit kann bei Versuchen, den Recovery Services-Anbieter in Ihrem Abonnement mit dem `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`-Befehl zu registrieren, ein Fehler auftreten. Warten Sie in diesem Fall eine Stunde, und wiederholen Sie den Versuch.

	Sobald Sie den Zugriff auf den Recovery Services-Anbieter in Ihrem Abonnement aktiviert haben, registrieren Sie den Anbieter in Ihrem Abonnement mit folgendem Befehl:

		Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

	Überprüfen Sie mit den Befehlen `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` und `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`, ob die Anbieter erfolgreich registriert wurden.

	

## Schritt 2: Einrichten des Recovery Services-Tresors

1. Erstellen Sie eine ARM-Ressourcengruppe, in der Sie den Tresor erstellen, oder verwenden Sie eine vorhandene Ressourcengruppe. Der folgende Befehl erstellt beispielsweise eine neue ARM-Ressourcengruppe:

		New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

	Dabei enthält die $ResourceGroupName-Variable den Namen der zu erstellenden ARM-Ressourcengruppe und die $Geo-Variable die Azure-Region, in der die Ressourcengruppe erstellt wird (z. B. „Brasilien Süd“).

	Sie können eine Liste der ARM-Ressourcengruppen in Ihrem Abonnement mit dem `Get-AzureRmResourceGroup`-Cmdlet abrufen.

2. Erstellen Sie einen neuen Azure Recovery Services-Tresor wie folgt:

		$vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

	Sie können eine Liste der vorhandenen Tresore mithilfe des `Get-AzureRmRecoveryServicesVault`-Cmdlets abrufen.

> [AZURE.NOTE] Wenn Sie Vorgänge in ASR-Tresoren ausführen möchten, die mit dem klassischen Portal oder dem PowerShell-Modul für die Azure-Service-Verwaltung erstellt wurden, können Sie eine Liste mit solchen Tresoren mit dem `Get-AzureRmSiteRecoveryVault`-Cmdlet abrufen. Sie sollten für alle neuen Vorgänge einen neuen Recovery Services-Tresor erstellen. Die Site Recovery-Tresore, die Sie zuvor erstellt haben, werden weiterhin unterstützt, bieten aber nicht die neuesten Features.

## Schritt 3: Generieren eines Tresorregistrierungsschlüssels

1. Generieren Sie einen Tresorregistrierungsschlüssel für Ihren Tresor, und laden Sie ihn herunter.

    	Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -Path $path
2. Importieren Sie die heruntergeladene Tresoreinstellungsdatei, um den Tresorkontext festzulegen.
 
		Import-AzureRmSiteRecoveryVaultSettingsFile -Path $path 

## Schritt 4: Erstellen eines Hyper-V-Standorts und Generieren eines neuen Tresorregistrierungsschlüssels für den Standort

1. Erstellen Sie einen neuen Hyper-V-Standort wie folgt:
		
		$sitename = "MySite"                #Specify site friendly name
		New-AzureRmSiteRecoverySite -Name $sitename

	Dieses Cmdlet startet einen ASR-Auftrag, um den Standort zu erstellen, und gibt ein ASR-Auftragsobjekt zurück. Warten Sie, bis der Auftrag abgeschlossen ist, und überprüfen Sie, ob der Auftrag erfolgreich abgeschlossen wurde.

	Sie können das ASR-Auftragsobjekt abrufen und damit den aktuellen Status des Auftrags mit dem Get-AzureRmSiteRecoveryJob-Cmdlet überprüfen. 
2. Generieren Sie einen Registrierungsschlüssel für den Standort, und laden Sie ihn herunter:

		$SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
		Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
	
	Kopieren Sie den heruntergeladenen Schlüssel in den Hyper-V-Host. Sie benötigen den Schlüssel, um den Hyper-V-Host am Standort zu registrieren.

	
## Schritt 5: Installieren des Azure Site Recovery-Anbieters und Azure Recovery Services-Agents auf dem Hyper-V-Host

1. Laden Sie das Installationsprogramm für die neueste Version des Anbieters [hier](https://aka.ms/downloaddra) herunter.
2. Führen Sie das Installationsprogramm auf dem Hyper-V-Host aus, und fahren Sie am Ende der Installation mit der Registrierung fort.
3. Geben Sie den heruntergeladenen Standortregistrierungsschlüssel ein, wenn Sie aufgefordert werden, und schließen Sie die Registrierung des Hyper-V-Hosts am Standort ab.
4. Überprüfen Sie folgendermaßen, ob der Hyper-V-Host am Standort registriert wurde:

		$server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname 

## Schritt 6: Erstellen einer Replikationsrichtlinie und deren Zuordnung zum Schutzcontainer

1. Erstellen Sie eine Replikationsrichtlinie:

		$ReplicationFrequencyInSeconds = "300";    	#options are 30,300,900
		$PolicyName = “replicapolicy”
		$Recoverypoints = 6            		#specify the number of recovery points
		$storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id 

		$PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

	Überprüfen Sie den zurückgegebenen Auftrag, um sicherzustellen, dass die Replikationsrichtlinie erfolgreich erstellt wurde.

	>[AZURE.IMPORTANT] Das angegebene Speicherkonto sollte sich in der gleichen Azure-Region wie Ihr Recovery Services-Tresor befinden, und die Georeplikation sollte hierfür aktiviert sein.
	>
	> - Wenn das angegebene Speicherkonto für die Wiederherstellung vom Typ Azure Storage(klassisch) ist, werden die geschützten Computer bei einem Failover auf Azure IaaS (klassisch) wiederhergestellt.
	> - Wenn das angegebene Speicherkonto für die Wiederherstellung vom Typ Azure Storage(ARM) ist, werden die geschützten Computer bei einem Failover auf Azure IaaS (ARM) wiederhergestellt.
	
 
2. Rufen Sie den entsprechenden Schutzcontainer des Standorts ab:
		
		$protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.	Starten Sie die Zuordnung des Schutzcontainers mit der Replikationsrichtlinie:

		$Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
		$associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

	Warten Sie, bis der Zuordnungsauftrag abgeschlossen ist, und stellen Sie sicher, dass er erfolgreich abgeschlossen wurde.

##Schritt 7: Aktivieren des Schutzes für virtuelle Computer

1. Rufen Sie die Schutzentität ab, die der VM entspricht, die Sie schützen möchten:
		
		$VMFriendlyName = "Fabrikam-app"                    #Name of the VM 
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. Starten Sie den Schutz des virtuellen Computers:
		
		$Ostype = "Windows"                                 # "Windows" or "Linux"
		$DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

	>[AZURE.IMPORTANT] Das angegebene Speicherkonto sollte sich in der gleichen Azure-Region wie Ihr Recovery Services-Tresor befinden, und die Georeplikation sollte hierfür aktiviert sein.
	>
	> - Wenn das angegebene Speicherkonto für die Wiederherstellung vom Typ Azure Storage(klassisch) ist, werden die geschützten Computer bei einem Failover auf Azure IaaS (klassisch) wiederhergestellt.
	> - Wenn das angegebene Speicherkonto für die Wiederherstellung vom Typ Azure Storage(ARM) ist, werden die geschützten Computer bei einem Failover auf Azure IaaS (ARM) wiederhergestellt.

	> Wenn dem virtuellen Computer, den Sie schützen, mehrere Datenträger angefügt sind, müssen Sie den Betriebssystemdatenträger mit dem OSDiskName-Parameter angeben.

3. Warten Sie, bis die virtuellen Computer nach der ersten Replikation einen geschützten Zustand erreicht haben. Dies dauert eine Weile und ist von Faktoren wie der zu replizierenden Datenmenge und der zum Hochladen zu Azure verfügbaren Bandbreite abhängig. Die Parameter „State“ und „StateDescription“ des Auftrags werden wird folgt aktualisiert, wenn der virtuelle Computer einen geschützten Status erreicht hat.

		
		PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

		PS C:\> $DRjob | Select-Object -ExpandProperty State
		Succeeded

		PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
		Completed

4. Aktualisieren Sie die Wiederherstellungseigenschaften, z. B. die Größe der VM-Rolle und das Azure-Netzwerk, dem die NICs der VMs bei einem Failover angefügt werden.

		PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

		PS C:\> $VMFriendlyName = "Fabrikam-App"

		PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

		PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

		PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

		PS C:\> $UpdateJob


		Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
		                   s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
		Type             : Microsoft.RecoveryServices/vaults/replicationJobs
		JobType          : UpdateVmProperties
		DisplayName      : Update the virtual machine
		ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
		State            : Succeeded
		StateDescription : Completed
		StartTime        : 10-12-2015 17:55:53 +00:00
		EndTime          : 10-12-2015 17:55:54 +00:00
		TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
		TargetObjectType : ProtectionEntity
		TargetObjectName : Fabrikam-App
		AllowedActions   : {Restart}
		Tasks            : {UpdateVmPropertiesTask}
		Errors           : {}



## Schritt 8: Ausführen eines Testfailovers

1. Führen Sie einen Testfailoverauftrag aus:
		
		$nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group
		
		$protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

		$TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. Stellen Sie sicher, dass die Test-VM in Azure erstellt wird. (Der Testfailoverauftrag wird nach dem Erstellen der Test-VM in Azure angehalten. Der Auftrag wird durch Bereinigen der erstellten Artefakte beim Fortsetzen des Auftrags abgeschlossen, wie im nächsten Schritt dargestellt.)

3. Schließen Sie das Testfailover ab.

    	$TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

<!---HONumber=AcomDC_0323_2016-->