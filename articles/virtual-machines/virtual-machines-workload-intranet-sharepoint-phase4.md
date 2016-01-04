<properties
	pageTitle="SharePoint Server 2013-Farm, Phase 4 | Microsoft Azure"
	description="In Phase 4 der SharePoint Server 2013-Farm erstellen Sie in Azure die virtuellen Computer für den SharePoint-Server und eine neue SharePoint-Farm."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# SharePoint-Intranetfarm-Workload Phase 4: Konfigurieren der SharePoint-Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

In dieser Phase der Intranet-Bereitstellung einer SharePoint 2013-Farm mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in den Azure-Infrastrukturdiensten richten Sie die Anwendungs- und Webebenen der SharePoint-Farm ein und erstellen die Farm unter Verwendung des SharePoint-Konfigurations-Assistenten.

Diese Phase muss vor Beginn von [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Erstellen der virtuellen Computer für die SharePoint-Server in Azure

Sie benötigen für die SharePoint-Server vier virtuelle Computer. Zwei virtuelle SharePoint-Servercomputer sind als Front-End-Webserver vorgesehen und zwei für die Verwaltung und Bereitstellung der SharePoint-Anwendungen. Zwei SharePoint-Server auf jeder Ebene gewährleisten eine hohe Verfügbarkeit.

Zunächst konfigurieren Sie den internen Lastenausgleich, sodass Azure den Clientdatenverkehr gleichmäßig auf die zwei Front-End-Webserver verteilt. Dazu müssen Sie eine interne Lastenausgleichsinstanz konfigurieren, die aus einem Namen und einer eigenen IP-Adresse, die aus dem Adressraum des Subnetzes abgerufen wurde, das Sie Ihrem virtuellen Azure-Netzwerk zugewiesen haben.

> [AZURE.NOTE]Die folgenden Befehlssätze verwenden Azure PowerShell 1.0 und höher. Weitere Informationen finden Sie unter [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) (in englischer Sprache).

Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg. Die Werte für die folgenden Azure PowerShell-Befehlssätze entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks
- Tabelle S für Ihr Subnetz
- Tabelle ST für Ihre Speicherkonten
- Tabelle A für Ihre Verfügbarkeitsgruppen

Tabelle M haben Sie in [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md) ausgefüllt und die Tabellen V, S, ST und A in [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure-PowerShell-Eingabeaufforderung aus.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Fügen Sie danach der internen DNS-Infrastruktur Ihres Unternehmens einen DNS-Adresseintrag hinzu, der den vollständig qualifizierten Domänennamen der SharePoint-Farm (z. B. „spfarm.corp.contoso.com“) in die dem internen Load Balancer zugewiesene IP-Adresse auflöst (der Wert von $privIP im vorangegangenen Azure PowerShell-Befehlsblock).

Mit dem folgenden Azure PowerShell-Befehlsblock erstellen Sie die virtuellen Computer für diese vier SharePoint-Server. Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure-PowerShell-Eingabeaufforderung aus.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Da diese virtuellen Computer für eine Intranetanwendung gedacht sind, wird ihnen keine öffentliche IP-Adresse oder ein DNS-Domänenname zugewiesen, und sie sind nicht über das Internet erreichbar. Dies bedeutet jedoch auch, dass Sie damit über das Azure-Portal keine Verbindung herstellen können. Die Schaltfläche **Verbinden** ist nicht verfügbar, wenn Sie die Eigenschaften des virtuellen Computers anzeigen.

Verwenden Sie einen Remotedesktopclient Ihrer Wahl, und erstellen Sie eine Remotedesktopverbindung mit jedem virtuellen Computer. Verwenden Sie den zugehörigen Intranet-DNS- oder Computernamen und die Anmeldeinformationen des lokalen Administratorkontos.

Treten Sie dann für jeden virtuellen Computer der geeigneten Active Directory-Domäne bei, indem Sie an der Windows PowerShell-Eingabeaufforderung diese Befehle ausführen.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Beachten Sie, dass Sie Anmeldeinformationen für ein Domänenkonto angeben müssen, nachdem Sie den **Add-Computer**-Befehl eingegeben haben.

Nach dem Neustart gehen Sie für jeden der vier SharePoint-Server nach den Anweisungen der Prozedur [Anmelden bei einem virtuellen Computer über eine Remotedesktopverbindung](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) vor, um sich mit den Anmeldeinformationen für das Konto „[Domäne]\\sp\_farm\_db“ anzumelden. Diese Anmeldeinformationen haben Sie in [Phase 2: Konfigurieren von Domänencontrollern](virtual-machines-workload-intranet-sharepoint-phase2.md) erstellt.

Gehen Sie für jeden der vier SharePoint-Server nach den Anweisungen der Prozedur [Testen der Konnektivität](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) vor, um die Konnektivität der Standorte Ihres Unternehmensnetzwerks zu testen.

> [AZURE.NOTE]Die SharePoint-Server werden aus dem SharePoint Server 2013-Testversion-Image erstellt. Sie müssen die Installation so abändern, dass sie einen Lizenzschlüssel einer Verkaufsversion oder einer Volumenlizenz für die Standard oder Enterprise Edition von SharePoint Server 2013 verwendet.

## Konfigurieren der SharePoint-Farm

Führen Sie zum Konfigurieren des ersten SharePoint-Servers der Farm folgende Schritte aus:

1.	Doppelklicken Sie auf dem Desktop des ersten SharePoint-Anwendungsservers auf **Konfigurations-Assistent für SharePoint 2013-Produkte**. Wenn Sie gefragt werden, ob das Programm Änderungen am Computer vornehmen darf, klicken Sie auf **Ja**.
2.	Klicken Sie auf der Seite **Willkommen bei den SharePoint-Produkten** auf **Weiter**.
3.	Ein Dialogfeld des **Konfigurations-Assistenten für SharePoint-Produkte** weist Sie darauf hin, dass Dienste (z. B. IIS) neu gestartet oder zurückgesetzt werden. Klicken Sie auf **Ja**.
4.	Wählen Sie auf der Seite **Verbindung mit einer Serverfarm herstellen** die Option **Eine neue Serverfarm erstellen** aus, und klicken Sie auf **Weiter**.
5.	Auf der Seite **Einstellungen für die Konfigurationsdatenbank angeben**:
 - Geben Sie unter **Datenbankserver** den Namen des primären Datenbankservers ein.
 - Geben Sie unter **Benutzername** den in [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md) erstellten Namen "[Domäne]**\\sp\_farm\_db**" ein. Das Konto "sp\_farm\_db" verfügt, wie Sie sich erinnern, über "sysadmin"-Berechtigungen für den Datenbankserver.
 - Geben Sie unter **Kennwort** das Kennwort für das Konto „sp\_farm\_db“ ein.
6.	Klicken Sie auf **Weiter**.
7.	Geben Sie auf der Seite **Farmsicherheitseinstellungen angeben** eine Passphrase ein und bestätigen Sie diese. Notieren Sie die Passphrase, und bewahren Sie sie für den Bedarfsfall an einem sicheren Ort auf. Klicken Sie auf **Weiter**.
8.	Klicken Sie auf der Seite **Webanwendung für die SharePoint-Zentraladministration konfigurieren** auf **Weiter**.
9.	Die Seite **Der Konfigurations-Assistent für SharePoint-Produkte wird abgeschlossen** wird angezeigt. Klicken Sie auf **Weiter**.
10.	Die Seite **SharePoint-Produkte konfigurieren** wird angezeigt. Warten Sie, bis der Konfigurationsprozess abgeschlossen ist (dies dauert etwa 8 Minuten).
11.	Klicken Sie nach der erfolgreichen Konfiguration der Farm auf **Fertig stellen**. Die neue Verwaltungswebsite wird gestartet.
12.	Klicken Sie zum Starten der Konfiguration der SharePoint-Farm auf **Assistenten starten**.

Gehen Sie auf dem zweiten SharePoint-Anwendungsserver und den beiden Front-End-Webservern nach folgenden Anweisungen vor:

1.	Doppelklicken Sie auf dem Desktop auf **Konfigurations-Assistent für SharePoint 2013-Produkte**. Wenn Sie gefragt werden, ob das Programm Änderungen am Computer vornehmen darf, klicken Sie auf **Ja**.
2.	Klicken Sie auf der Seite **Willkommen bei den SharePoint-Produkten** auf **Weiter**.
3.	Ein Dialogfeld des **Konfigurations-Assistenten für SharePoint-Produkte** weist Sie darauf hin, dass Dienste (z. B. IIS) neu gestartet oder zurückgesetzt werden. Klicken Sie auf **Ja**.
4.	Klicken Sie auf der Seite **Verbindung mit einer Serverfarm herstellen** auf **Verbindung mit einer vorhandenen Serverfarm herstellen**, und klicken Sie dann auf **Weiter**.
5.	Geben Sie auf der Seite **Einstellungen für die Konfigurationsdatenbank angeben** unter **Datenbankserver** den Namen des primären Datenbankservers ein, und klicken Sie dann auf **Datenbanknamen abrufen**.
6.	Klicken Sie in der Liste mit den Datenbanknamen auf **SharePoint\_Config**, und klicken Sie dann auf **Weiter**.
7.	Geben Sie auf der Seite **Farmsicherheitseinstellungen angeben** die im vorherigen Verfahren festgelegte Passphrase ein. Klicken Sie auf **Weiter**.
8.	Die Seite **Der Konfigurations-Assistent für SharePoint-Produkte wird abgeschlossen** wird angezeigt. Klicken Sie auf **Weiter**.
9.	Klicken Sie auf der Seite **Konfiguration erfolgreich** auf **Fertig stellen**.

Bei der Erstellung der Farm konfiguriert SharePoint auf dem virtuellen Computer des primären SQL-Servers eine Reihe von Serveranmeldungen. In SQL Server 2012 wurde das Konzept von Benutzern mit Kennwörtern für eigenständige Datenbanken eingeführt. Die Datenbank selbst speichert alle Metadaten und Benutzerinformationen der Datenbank, so dass sich ein Benutzer, der in dieser Datenbank definiert ist, nicht gesondert anmelden muss. Die Informationen dieser Datenbank werden durch die Verfügbarkeitsgruppe repliziert und stehen auch nach einem Failover noch zur Verfügung. Weitere Informationen finden Sie unter [Eigenständige Datenbanken](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Standardmäßig sind SharePoint-Datenbanken jedoch keine eigenständigen Datenbanken. Daher müssen Sie den sekundären Datenbankserver manuell so konfigurieren, dass er über den gleichen Anmeldungssatz für die SharePoint-Farmkonten verfügt wie der primäre Datenbankserver. Diese Synchronisierung können Sie in SQL Server Management Studio ausführen, indem Sie zur selben Zeit eine Verbindung zu beiden Servern herstellen.

Nach Abschluss dieser anfänglichen Konfiguration stehen weitere Konfigurationsoptionen für die Funktionen der SharePoint-Farm zur Verfügung. Weitere Informationen finden Sie unter [Planen von SharePoint 2013 für Azure-Infrastrukturdienste](http://msdn.microsoft.com/library/dn275958.aspx).

Hier sehen Sie die nach erfolgreichem Abschluss dieser Phase erstellte Konfiguration.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Nächster Schritt

- Zum Fortsetzen der Konfiguration dieser Workload wechseln Sie zu [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md).

<!---HONumber=AcomDC_1217_2015-->