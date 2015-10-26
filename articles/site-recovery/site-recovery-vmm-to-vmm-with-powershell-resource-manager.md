<properties
	pageTitle="Schützen von virtuellen Computern zwischen zwei VMM-Standorten mit PowerShell und dem Microsoft Azure-Ressourcen-Manager"
	description="Automatisieren Sie den Schutz zwischen zwei lokalen VMM-Standorten mit PowerShell und dem Azure-Ressourcen-Manager."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  VMM-Standort zu VMM-Standort mit PowerShell und Azure-Ressourcen-Manager


## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Eine vollständige Liste mit Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

Azure PowerShell ist ein Modul, das Cmdlets zum Verwalten von Azure durch Windows PowerShell enthält. Sie können zwei Arten von Modulen verwenden: das Azure-Profil oder den Azure-Ressourcen-Manager (ARM).

Dieser Artikel beschreibt, wie Sie Azure Site Recovery mithilfe von Windows PowerShell® und ARM bereitstellen und den Schutz virtueller Computer zwischen zwei VMM-Standorten konfigurieren und koordinieren. Virtuelle Computer auf Hyper-V-Hostservern, die sich in privaten VMM-Clouds an einem primären Standort befinden, werden per Hyper-V-Replikat repliziert, und es wird ein Failover auf einen sekundären VMM-Standort durchgeführt.

Sie müssen kein PowerShell-Experte sein, um diesen Artikel verwenden zu können. Es wird jedoch vorausgesetzt, dass Sie grundlegend mit Konzepten wie Modulen, Cmdlets und Sitzungen vertraut sind. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx) sowie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](powershell-azure-resource-manager.md).

Der Artikel enthält Informationen zu den Voraussetzungen für das Szenario und zeigt, wie Sie Azure PowerShell für Site Recovery einrichten, einen Site Recovery-Tresor erstellen, den Azure Site Recovery-Anbieter auf VMM-Servern installieren und die Server im Tresor registrieren, Schutzeinstellungen für VMM-Clouds konfigurieren, die für alle geschützten virtuellen Computer gelten, und den Schutz für diese virtuellen Computer aktivieren. Abschließend wird das Failover getestet, um sicherzustellen, dass alles wie erwartet funktioniert.


## Vorbereitung

Stellen Sie sicher, dass diese Voraussetzungen erfüllt werden:

- Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht ein [kostenloses Testkonto](pricing/free-trial/) zur Verfügung. Darüber hinaus können Sie sich über die [Preise für Azure Site Recovery Manager](http://azure.microsoft.com/pricing/details/site-recovery/) informieren.
- Am primären und sekundären Standort muss jeweils ein VMM-Server mit System Center 2012 R2 vorhanden sein.
- Jeder VMM-Server muss über mindestens eine Cloud verfügen, die Folgendes enthält:
	- Eine oder mehrere VMM-Hostgruppen.
	- Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.
	- Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver.
	- Erfahren Sie mehr über das Einrichten von VMM-Clouds:

		- [What’s New in Private Cloud with System Center 2012 R2 VMM](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) (Neues in der Private Cloud mit System Center 2012 R2 VMM; in englischer Sprache) und [VMM 2012 and the Clouds](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) (VMM 2012 und die Clouds; in englischer Sprache).
		- [Konfigurieren der VMM-Cloud-Struktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [Erstellen einer privaten Cloud in VMM](https://technet.microsoft.com/library/jj860425.aspx) und [Exemplarische Vorgehensweise: Erstellen von privaten Clouds mit System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)
- Mindestens ein Hyper-V-Hostserver, auf dem mindestens Windows Server 2012 mit Hyper-V-Rolle ausgeführt wird und die neuesten Updates installiert sind. Der Server oder Cluster muss sich in einer VMM-Cloud befinden.
- Wenn Sie Hyper-V in einem Cluster ausführen, wird der Clusterbroker nicht automatisch erstellt, wenn Sie einen Cluster mit statischen IP-Adressen verwenden. Sie müssen den Clusterbroker manuell konfigurieren. Anweisungen finden Sie unter [Konfigurieren des Hyper-V-Replikatbrokers](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).

	- Sie benötigen Azure PowerShell. Verwenden Sie mindestens die Azure PowerShell-Version 0.9.6. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md). 
	- Bei der Installation von Azure PowerShell wird auch eine benutzerdefinierte Konsole installiert. Die PowerShell-Befehle können über diese Konsole oder über ein anderes Hostprogramm (etwa Windows PowerShell ISE) ausgeführt werden.

## Schritt 1: Einrichten von PowerShell


1. Öffnen Sie eine PowerShell-Konsole, und führen Sie den folgenden Befehl aus, um zum ARM-Modul zu wechseln:

    `Switch-AzureMode AzureResourceManager`

3. Führen Sie anschließend den folgenden Befehl aus, um Ihr Azure-Konto der PowerShell-Sitzung hinzuzufügen. Sie werden zur Eingabe der Anmeldeinformationen für Ihr Konto aufgefordert.

    `Add-AzureAccount`

	Hinweis: Wenn Sie als CSP-Partner für einen Mandanten tätig sind, müssen Sie den Kunden beim Hinzufügen des Azure-Kontos als Mandanten angeben:

    `Add-AzureAccount-Tenant "customer"`

5. Da ein Konto mehrere Abonnements enthalten kann, müssen Sie das Abonnement zuordnen, das Sie mit dem Konto verwenden möchten.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. Wenn Sie Site Recovery-Cmdlets erstmals im Rahmen des Abonnements verwenden, müssen Sie den Azure-Anbieter für Site Recovery registrieren:

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## Schritt 2: Einrichten des Site Recovery-Tresors

1. Wenn Sie noch nicht über einen Site Recovery-Tresor verfügen, führen Sie das Cmdlet [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx) aus:

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## Schritt 3: Generieren eines Tresorregistrierungsschlüssels

1. Führen Sie das Cmdlet [Get-AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) aus, um einen Tresorregistrierungsschlüssel zu erhalten. Mit diesem Schlüssel können Sie VMM-Server im Tresor registrieren:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## Schritt 4: Installieren des Azure Site Recovery-Anbieters

1. Laden Sie im Site Recovery-Tresor über die Seite „Schnellstart“ die Anbieter-Installationsdatei herunter.
2. Erstellen Sie mithilfe des folgenden Befehls auf jedem VMM-Server einen Ordner:

    `pushd C:\ASR`

3. Führen Sie den folgenden Befehl aus, um die Dateien des heruntergeladenen Anbieters zu extrahieren:

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. Führen Sie den folgenden Befehl aus, um den Anbieter zu installieren:

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. Warten Sie, bis die Installation abgeschlossen ist, und registrieren Sie den Server im Tresor:

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## Schritt 5: Einrichten des Tresorkontexts

1. Führen Sie das Get-AzureSiteRecoveryVault-Cmdlet aus, um sicherzustellen, dass alle Befehle für den angegebenen Tresor ausgeführt werden:

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. Laden Sie die Tresoreinstellungen herunter:

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass die Cmdlets für den Tresor ausgeführt werden:

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## Schritt 3: Konfigurieren der Cloudschutzeinstellungen

Nachdem der VMM-Server im Tresor registriert ist, können Sie Cloudschutzeinstellungen konfigurieren, die für alle virtuellen Computer auf Hyper-V-Hosts gelten, die sich in Clouds auf dem registrierten VMM-Server befinden.

1. Erstellen Sie für die primäre und sekundäre Cloud einen Container im Tresor:

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. Konfigurieren Sie Schutzeinstellungen für die Clouds:

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. Starten Sie einen Auftrag, um die Cloudcontainer mit den Cloudschutzeinstellungen zu verknüpfen:

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## Schritt 4: Aktivieren des VM-Schutzes

Aktivieren Sie den Schutz für virtuelle Computer in den VMM-Clouds:

1. Rufen Sie den virtuellen Computer ab, die Sie schützen möchten:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. Aktivieren Sie den Schutz für den virtuellen Computer:

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## Schritt 5: Durchführen eines Testfailovers

1.	Wählen Sie den virtuellen Computer für das Failover aus:

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. Führen Sie einen Testfailoverauftrag aus:

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. Vergewissern Sie sich, dass der virtuelle Computer, für den das Failover durchgeführt wurde, am sekundären Standort vorhanden ist, und schließen Sie das Failover ab:

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## Nächste Schritte

Für Fragen oder Kommentare zu diesem Szenario steht das [Site Recovery-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/) zur Verfügung.

<!---HONumber=Oct15_HO3-->