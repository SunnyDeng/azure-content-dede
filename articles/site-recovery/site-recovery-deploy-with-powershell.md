<properties
	pageTitle="Automatisieren des Schutzes zwischen einem lokalen VMM-Standort und Azure mithilfe von PowerShell"
	description="Automatisieren der Bereitstellung von Azure Site Recovery mithilfe von PowerShell"
	services="site-recovery"
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/08/2015"
	ms.author="lauraa"/>

#  Bereitstellen von Azure Site Recovery mit PowerShell
Windows PowerShell® ist eine aufgabenbasierte Befehlszeilenshell und Skriptsprache, die insbesondere für die Systemverwaltung konzipiert ist. Die Verwendung von PowerShell-Cmdlets für Azure Site Recovery wird zwischen einem Hyper-V-Website, die von VMM verwaltet wird, und Azure unterstützt.

## Übersicht

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer in einer Vielzahl von Bereitstellungsszenarien aufeinander abgestimmt werden. Eine vollständige Liste der Bereitstellungsszenarien finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

Dieser Artikel zeigt die Verwendung von PowerShell zur Automatisierung allgemeiner Aufgaben zum Bereitstellen von Azure Site Recovery, einschließlich Orchestrieren und Automatisieren des Schutzes für Workloads auf virtuellen Computern auf Hyper-V-Hostservern, die sich in privaten VMM-Clouds befinden. In diesem Szenario werden virtuelle Computer von einem primären VMM-Standort mithilfe von Hyper-V-Replikaten in Azure repliziert.

Der Artikel Handbuch enthält Informationen zu Voraussetzungen für das Szenario und zeigt, wie Sie einen Site Recovery-Tresor einrichten, den Azure Site Recovery-Anbieter auf dem Quell-VMM-Server installieren, den Server im Tresor registrieren, Azure-Speicherkonten hinzufügen, den Azure Recovery Services Agent auf Hyper-V-Hostservern installieren, Schutzeinstellungen für VMM-Clouds konfigurieren, die auf alle geschützten virtuellen Computer angewendet werden, und den Schutz für diese virtuellen Computer aktivieren. Zum Schluss testen Sie das Failover, um sicherzustellen, dass alles wie erwartet funktioniert.

Sollten beim Einrichten dieses Szenarios Probleme auftreten, besuchen Sie das [Azure Recovery Services-Forum](http://go.microsoft.com/fwlink/?LinkId=313628).


## Vorbereitung

Stellen Sie sicher, dass diese Voraussetzungen erfüllt werden:
### Voraussetzungen für Azure

- Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Beginnen Sie mit einer [kostenlosen Testversion](http://aka.ms/try-azure), falls Sie kein Konto haben. Darüber hinaus können Sie sich über die [Preisgestaltung für Azure Site Recovery-Manager](http://go.microsoft.com/fwlink/?LinkId=378268) informieren.
- Sie benötigen ein Azure-Speicherkonto, um in Azure replizierte Daten zu speichern. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen zum Einrichten von Azure-Speicher finden Sie unter [Einführung in Microsoft Azure Storage](http://go.microsoft.com/fwlink/?LinkId=398704).
- Sie müssen sicherstellen, dass die virtuellen Computer, die Sie schützen möchten, den Azure-Anforderungen entsprechen. Informationen hierzu finden Sie unter [Unterstützung virtueller Computer](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A).

### VMM-Voraussetzungen
- Sie benötigen einen VMM-Server, der unter System Center 2012 R2 ausgeführt wird.
- Auf allen VMM-Servern mit virtuellen Computern, die Sie schützen möchten, muss der Azure Site Recovery-Anbieter ausgeführt werden. Dieser wird bei der Bereitstellung von Azure Site Recovery installiert.
- Sie benötigen mindestens eine Cloud auf dem VMM-Server, den Sie schützen möchten. Die Cloud sollte Folgendes enthalten:
	- Eine oder mehrere VMM-Hostgruppen.
	- Einen oder mehrere Hyper-V-Hostserver oder Cluster in jeder Hostgruppe.
	- Einen oder mehrere virtuelle Computer auf dem Hyper-V-Quellserver. Es muss sich um virtuelle Computer der 1. Generation handeln.
- Erfahren Sie mehr über das Einrichten von VMM-Clouds:
	- Weitere Informationen über private VMM-Clouds erhalten Sie unter [Private Cloud-Neuerungen mit System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) und unter [VMM 2012 und die Clouds](http://go.microsoft.com/fwlink/?LinkId=324956).
	- Informationen zum [Konfigurieren der VMM-Cloudstruktur](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- Sobald Ihre Cloud-Strukturelemente eingerichtet sind, finden Sie Informationen zum Erstellen privater Clouds unter [Erstellen einer privaten Cloud in VMM](http://go.microsoft.com/fwlink/?LinkId=324953) und [Exemplarische Vorgehensweise: Erstellen von privaten Clouds mit System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/?LinkId=324954)

### Hyper-V-Voraussetzungen

- Auf den Hyper-V-Hostservern muss mindestens Windows Server 2012 mit Hyper-V-Rolle ausgeführt werden, und die neuesten Updates müssen installiert sein.
- Wenn Sie Hyper-V in einem Cluster ausführen, wird der Clusterbroker nicht automatisch erstellt, wenn Sie einen Cluster mit statischen IP-Adressen verwenden. Sie müssen den Clusterbroker manuell konfigurieren. Anweisungen finden Sie unter [Hyper-V-Replikatbroker konfigurieren](http://go.microsoft.com/fwlink/?LinkId=403937).
- Alle Hyper-V-Hostserver oder Cluster, deren Schutz Sie verwalten möchten, müssen in einer VMM-Cloud enthalten sein.

### Voraussetzungen für die Netzwerkzuordnung
Wenn Sie virtuelle Computer in Azure schützen, wird eine Netzwerkzuordnung zwischen VM-Netzwerken auf dem Quell-VMM-Server und den Azure-Zielnetzwerken erstellt, um Folgendes zu ermöglichen:

- Alle Computer, die ein Failover in demselben Netzwerk durchführen, können sich unabhängig vom jeweiligen Wiederherstellungsplan miteinander verbinden.
- Wenn ein Netzwerkgateway im Azure-Zielnetzwerk eingerichtet ist, können die virtuellen Computer eine Verbindung zu anderen lokalen virtuellen Computern herstellen.
- Wenn Sie keine Netzwerkzuordnung konfigurieren, können nur virtuelle Computer, für die ein Failover in demselben Wiederherstellungsplan erfolgt, nach einem Failover auf Azure eine Verbindung miteinander herstellen.

Wenn Sie eine Netzwerkzuordnung bereitstellen möchten, benötigen Sie Folgendes:

- Die virtuellen Computer, die Sie auf dem VMM-Quellserver schützen möchten, sollten mit einem VM-Netzwerk verbunden sein. Dieses Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist.
- Ein Azure-Netzwerk, mit dem replizierte virtuelle Computer nach einem Failover eine Verbindung herstellen können. Dieses Netzwerk wählen Sie zum Zeitpunkt des Failovers aus. Das Netzwerk sollte sich in derselben Region wie Ihr Azure Site Recovery-Abonnement befinden.
- Informationen zur Netzwerkzuordnung finden Sie unter:
	- [Konfigurieren von logischen Netzwerken in VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
	- [Konfiguration von VM-Netzwerken und Gateways in VMM](http://go.microsoft.com/fwlink/?LinkId=386308)
	- [Konfigurieren und Überwachen virtueller Netzwerke in Azure](http://go.microsoft.com/fwlink/?LinkId=402555)

###PowerShell-Voraussetzungen
Stellen Sie sicher, dass Azure PowerShell einsatzbereit ist. Wenn Sie PowerShell bereits verwenden, müssen Sie auf Version 0.8.10 oder höher aktualisieren. Ausführliche Informationen zum Einrichten von PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md). Nach dem Einrichten und Konfigurieren von PowerShell können Sie alle verfügbaren Cmdlets für den Dienst [hier](https://msdn.microsoft.com/library/dn850420.aspx) anzeigen.

Tipps für die Verwendung von Cmdlets, z. B. wie Parameterwerte, Eingaben und Ausgaben in der Regel in Azure-PowerShell behandelt werden, finden Sie unter [Erste Schritte mit Azure-Cmdlets](https://msdn.microsoft.com/library/azure/jj554332.aspx).

## Schritt 1: Festlegen des Abonnements 

Führen Sie diese Cmdlets in PowerShell aus:



			$UserName = "<user@live.com>"
	$Password = "<password>"
	$AzureSubscriptionName = "prod_sub1"

	$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
	$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
	Add-AzureAccount -Credential $Cred;
	$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName


Ersetzen Sie die Elemente in "< >" durch Ihre jeweiligen Informationen.

## Schritt 2: Erstellen eines Site Recovery-Tresors

Ersetzen Sie in PowerShell die Elemente in "< >" durch Ihre jeweiligen Informationen. Führen Sie anschließend die folgenden Befehle aus:

```

	$VaultName = "<testvault123>"
	$VaultGeo  = "<Southeast Asia>"
	$OutputPathForSettingsFile = "<c:>"

```


```
	New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
	$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## Schritt 3: Generieren eines Tresorregistrierungsschlüssels

Generieren Sie einen Registrierungsschlüssel im Tresor. Nachdem Sie den Azure Site Recovery-Anbieter heruntergeladen und auf dem VMM-Server installiert haben, verwenden Sie diesen Schlüssel, um den VMM-Server im Tresor zu registrieren.

1.	Rufen Sie die Tresoreinstellungsdatei ab, und legen Sie den Kontext fest:
	
	```
	
		$VaultName = "<testvault123>"
		$VaultGeo  = "<Southeast Asia>"
		$OutputPathForSettingsFile = "<c:>"
	
		$VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;
	
	```
	
2.	Legen Sie den Tresorkontext durch Ausführen der folgenden Befehle fest:
	
	```	
		$VaultSettingFilePath = $vaultSetingsFile.FilePath 
		$VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop
```

## Schritt 4: Installieren des Azure Site Recovery-Anbieters

1.	Erstellen Sie ein Verzeichnis auf dem VMM-Computer durch Ausführen des folgenden Befehls:
	
	```
	
		pushd C:\ASR\
	
	```
	
2. Extrahieren Sie die Dateien mithilfe des heruntergeladenen Anbieters durch Ausführen des folgenden Befehls.
	
	```
	
		AzureSiteRecoveryProvider.exe /x:. /q
	
	```
	
3. Installieren Sie den Anbieter mithilfe der folgenden Befehls:
	
	```
	
	.\SetupDr.exe /i
	
	```
	
	```
	
	$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
	                $isNotInstalled = $true;
	                if(Test-Path $installationRegPath)
	                {
	                                $isNotInstalled = $false;
	                }
	}While($isNotInstalled)
	
	```
	
	Warten Sie, bis die Installation abgeschlossen ist.
	
4. Registrieren Sie den Server beim Tresor mithilfe des folgenden Befehls:
	
	```
	
	$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
	
	```
	
## Schritt 5: Erstellen eines Azure-Speicherkontos

Wenn Sie kein Azure-Speicherkonto haben, erstellen Sie ein Konto für die Georeplikation durch Ausführen des folgenden Befehls:

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

Beachten Sie, dass sich das Speicherkonto in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein muss.


## Schritt 6: Installieren des Azure Recovery Services-Agent

Installieren Sie im Azure-Portal den Azure Recovery Services-Agent auf jedem Hyper-V-Hostserver in den VMM-Clouds, die Sie schützen möchten.

Führen Sie den folgenden Befehl auf allen VMM-Hosts aus.

```

	marsagentinstaller.exe /q /nu

```


## Schritt 7: Konfigurieren der Cloudschutzeinstellungen

1.	Erstellen Sie ein Cloudschutzprofil für Azure durch Ausführen des folgenden Befehls:
	
	```
	
	$ReplicationFrequencyInSeconds = "300";
	$ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider 	HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName `
	-RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds 	$ReplicationFrequencyInSeconds;
	
	```
	
2.	Rufen Sie einen Schutzcontainer durch Ausführen der folgenden Befehle ab:
	
	```
	
		$PrimaryCloud = "testcloud"
		$protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;	
	
	```
	
3.	Starten Sie die Zuordnung des Schutzcontainers zur Cloud:
	
	```
	
		$associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -	ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;		
	
	```
	
4.	Nachdem der Auftrag abgeschlossen ist, führen Sie den folgenden Befehl aus:

			$job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
			if($job -eq $null -or $job.StateDescription -ne "Completed")
			{
				$isJobLeftForProcessing = $true;
			}
5. Nachdem die Verarbeitung des Auftrags abgeschlossen ist, führen Sie den folgenden Befehl aus:

		if($isJobLeftForProcessing)
			{
			Start-Sleep -Seconds 60
			}
				}While($isJobLeftForProcessing)
	
	
Um den Abschluss des Vorgangs zu überprüfen, führen Sie die Schritte in [Überwachen der Aktivität](#monitor) aus.

## Schritt 8: Konfigurieren der Netzwerkzuordnung
Bevor Sie mit der Netzwerkzuordnung beginnen, stellen Sie sicher, dass virtuelle Computer auf dem VMM-Quellserver mit einem VM-Netzwerk verbunden sind. Erstellen Sie außerdem ein oder mehrere virtuelle Azure-Netzwerke. Beachten Sie, dass einem einzelnen Azure-Netzwerk mehrere VM-Netzwerke zugeordnet werden können.

Wenn das Zielnetzwerk mehrere Subnetze enthält und eines dieser Subnetze denselben Namen besitzt wie das Subnetz des virtuellen Quellcomputers, dann wird der virtuelle Replikatcomputer nach einem Failover mit diesem Zielsubnetz verbunden. Gibt es kein Zielsubnetz mit einem übereinstimmenden Namen, wird der virtuelle Computer mit dem ersten Subnetz im Netzwerk verbunden.

Der erste Befehl ruft Server für den aktuellen Azure Site Recovery-Tresor ab. Der Befehl speichert die Microsoft Azure Site Recovery-Server in der "$Servers"-Arrayvariablen.



	$Servers = Get-AzureSiteRecoveryServer


Der zweite Befehl ruft das Site Recovery-Netzwerk für den ersten Server im "$Servers"-Array ab. Der Befehl speichert die Netzwerke in der "$Networks"-Variablen.

```

	$Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

```

Der dritte Befehl ruft die Azure-Abonnements mithilfe des "Get-AzureSubscription"-Cmdlets ab und speichert dann diesen Wert in der "$Subscriptions"-Variablen.

```

	$Subscriptions = Get-AzureSubscription

```

Der vierte Befehl ruft virtuelle Azure-Netzwerke mithilfe des "Get-AzureVNetSite"-Cmdlets ab und speichert dann diesen Wert in der "$AzureVmNetworks"-Variablen.

```

	$AzureVmNetworks = Get-AzureVNetSite

```

Das letzte Cmdlet erstellt eine Zuordnung zwischen dem primären Netzwerk und dem virtuellen Azure-Computernetzwerk. Das Cmdlet gibt das primäre Netzwerk als erstes Element von "$Networks" an. Das Cmdlet gibt ein Netzwerk für virtuelle Computer als erstes Element von "$AzureVmNetworks" mithilfe seiner ID an. Der Befehl enthält Ihre Azure-Abonnement-ID.

```

PS C:> New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id

```

## Schritt 9: Aktivieren des Schutzes für virtuelle Computer

Nach der korrekten Konfiguration von Servern, Clouds und Netzwerken können Sie den Schutz für die virtuellen Computer in der Cloud aktivieren. Beachten Sie Folgendes:

Die virtuellen Computer müssen die Azure-Anforderungen erfüllen. Unter <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Voraussetzungen und Support</a> im Planungshandbuch können Sie diese prüfen.

Um den Schutz zu aktivieren, müssen die Eigenschaften "Betriebssystem" und "Betriebssystem-Datenträger" für den virtuellen Computer gesetzt sein. Sie können diese Eigenschaften setzen, wenn Sie den virtuellen Computer in VMM mithilfe einer Vorlage für virtuelle Maschinen erstellen. Außerdem können Sie diese Eigenschaften für vorhandene virtuelle Computer auf den Registerkarten **Allgemein** und **Hardwarekonfiguration** in den Eigenschaften der virtuellen Computer festlegen. Falls diese Eigenschaften in VMM nicht angezeigt werden, sollten Sie sie dennoch im Azure Site Recovery-Portal konfigurieren können.


	
1.	Führen Sie den folgenden Befehl aus, um den Schutzcontainer abzurufen und den Schutz zu aktivieren:
		
	```
	
	$ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
	
	```
	
2. Rufen Sie die Schutzentität (VM) durch Ausführen des folgenden Befehls ab:
		
	```
	
	$protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer
		
		```
			
3. Aktivieren Sie den DR für den virtuellen Computer, indem Sie den folgenden Befehl ausführen:

	
	$jobResult = $protectionEntity Set-AzureSiteRecoveryProtectionEntity - ProtectionEntity-Schutz aktivieren - Force
	

## Testen der Bereitstellung

Um Ihre Bereitstellung zu testen, können Sie ein Testfailover für einen einzelnen virtuellen Computer durchführen oder einen Wiederherstellungsplan erstellen, der mehrere virtuelle Computer umfasst, und ein Testfailover für diesen Plan durchführen. Das Test-Failover simuliert Ihre Failover- und Wiederherstellungsmechanismen in einem isolierten Netzwerk. Beachten Sie Folgendes:

- Wenn Sie nach dem Failover eine Verbindung mit dem virtuellen Computer in Azure über Remote Desktop herstellen möchten, aktivieren Sie die Remote Desktop-Verbindung auf dem virtuellen Computer, bevor Sie das Test-Failover ausführen.
- Nach dem Failover verwenden Sie eine öffentliche IP-Adresse, um eine Verbindung zum virtuellen Computer in Azure über Remote Desktop herzustellen. Wenn Sie dies möchten, stellen Sie sicher, dass keine Domänenrichtlinien vorhanden sind, die das Verbinden zu einem virtuellen Computer über eine öffentliche Adresse verhindern.

Um den Abschluss des Vorgangs zu überprüfen, führen Sie die Schritte in [Überwachen der Aktivität](#monitor) aus.

### Erstellen eines Wiederherstellungsplans

1. Erstellen Sie eine XML-Datei als Vorlage für Ihren Wiederherstellungsplan mithilfe der unten aufgeführten Daten, und speichern Sie sie dann unter "C:\\RPTemplatePath.xml".
2. Ändern Sie die RecoveryPlan-Knoten "Id", "Name", "PrimaryServerId" und "SecondaryServerId".
3. Ändern Sie den "ProtectionEntity"-Knoten "PrimaryProtectionEntityId" (vmid aus VMM).
4. Sie können weitere virtuelle Computer hinzufügen, indem Sie weitere "ProtectionEntity"-Knoten hinzufügen.
	
	```
	
	<#
	<?xml version="1.0" encoding="utf-16"?>
	<RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test" 	PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5" 	SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description="" 	Version="V2014_07">
	  <Actions />
	  <ActionGroups>
	    <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </ShutdownAllActionGroup>
	    <FailoverAllActionGroup Id="FailoverAllActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	    </FailoverAllActionGroup>
	    <BootActionGroup Id="DefaultActionGroup">
	      <PreActionSequence />
	      <PostActionSequence />
	      <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-	cf163cc36ef8" />
	    </BootActionGroup>
	  </ActionGroups>
	  <ActionGroupSequence>
	    <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup" 	Before="FailoverAllActionGroup" />
	    <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup" 	After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
	    <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
	  </ActionGroupSequence>
	</RecoveryPlan>
	#>
	
	```
	
4. Geben Sie die Daten in die Vorlage ein:
	
	```
	
	$TemplatePath = "C:\RPTemplatePath.xml";
	
	```
	
5. Erstellen Sie das "RecoveryPlan"-Objekt:
	
	```
	
		$RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;
	
	```
	
### Durchführen eines Test-Failovers

1. Rufen Sie das "RecoveryPlan"-Objekt durch Ausführen des folgenden Befehls ab:
	
	```
	
		$RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
	
	```
	
2. Starten Sie dann den Test-Failover, indem Sie den folgenden Befehl ausführen:
	
	```
	
	$jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;
	
	```
	
## <a name=monitor></a> Überwachen der Aktivität

Verwenden Sie die folgenden Befehle zum Überwachen der Aktivität. Beachten Sie, dass Sie zwischen den Aufträgen auf den Abschluss der Verarbeitung warten müssen.

```

Do
{
                $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
                Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
                if($job -eq $null -or $job.StateDescription -ne "Completed")
                {
                                $isJobLeftForProcessing = $true;
                }

```

```

if($isJobLeftForProcessing)
                {
                                Start-Sleep -Seconds 60
                }
}While($isJobLeftForProcessing)

```


##<a id="next" name="next" href="#next"></a>Nächste Schritte
<UL>

<LI>Weitere Informationen zu Azure Site Recovery-PowerShell-Cmdlets finden Sie in <a href="https://msdn.microsoft.com/library/dn850420.aspx">diesem</a> Artikel.

<LI>Informationen zur Planung und Bereitstellung von Azure Site Recovery in einer realen Produktionsumgebung finden Sie im <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planungshandbuch für Azure Site Recovery</a> und im <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Bereitstellungshandbuch für Azure Site Recovery</a>.</LI>

<LI>Wenn Sie Fragen haben, besuchen Sie das<a href="http://go.microsoft.com/fwlink/?LinkId=313628"> Azure Recovery Services-Forum</a>.</LI></UL>

<!---HONumber=July15_HO2-->