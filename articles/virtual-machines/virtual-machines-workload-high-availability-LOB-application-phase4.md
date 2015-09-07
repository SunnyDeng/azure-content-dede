<properties 
	pageTitle="Branchenanwendung, Phase 4 | Microsoft Azure"
	description="Phase 4 der Branchenanwendung in Azure erstellen Sie die Webserver, und laden Sie Ihre Branchenanwendung auf diese Server."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="josephd"/>

# Branchenanwendungs-Workload, Phase 4: Konfigurieren der Webserver

In dieser Phase der Bereitstellung einer hochverfügbaren Branchenanwendung in den Azure-Infrastrukturdiensten erstellen Sie die Webserver und laden Ihre Branchenanwendung auf die Server.

Diese Phase muss vor Beginn von [Phase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

## Erstellen der virtuellen Computer für die Webserver in Azure

Es gibt zwei virtuelle Computer, die als Webserver fungieren. Auf diesen können Sie ASP.NET-Anwendungen oder ältere Anwendungen bereitstellen, die von IIS 8 (Internetinformationsdienste) in Windows Server 2012 R2 gehostet werden können.

Zunächst konfigurieren Sie den internen Lastenausgleich, sodass Azure den Clientdatenverkehr an die Branchenanwendung gleichmäßig auf die zwei Webserver verteilt. Dazu müssen Sie eine interne Lastenausgleichsinstanz konfigurieren, die aus einem Namen und einer eigenen IP-Adresse aus dem Adressraum des Subnetzes besteht, das Sie Ihrem virtuellen Azure-Netzwerk zugewiesen haben. Führen Sie die folgenden Befehle an der Azure PowerShell-Eingabeaufforderung aus, um die Verfügbarkeit einer IP-Adresse zu überprüfen, die Sie für den internen Load Balancer ausgewählt haben: Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg.

	Switch-AzureMode AzureServiceManagement
	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 2 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Wenn das Feld **IsAvailable** in der Ausgabe des Test-AzureStaticVNetIP-Befehls den Wert **True** aufweist, können Sie die IP-Adresse verwenden.

Wechseln Sie mit diesem Befehl zurück zum Ressourcen-Manager-Modus von PowerShell.

	Switch-AzureMode AzureResourceManager

Als Nächstes füllen Sie die Variablen und führen die folgenden Befehle aus:

	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Fügen Sie danach der DNS-Infrastruktur Ihres Unternehmens einen DNS-Adresseintrag hinzu, der den vollständig qualifizierten Domänennamen der Branchenanwendung (z. B. "lobapp.corp.contoso.com") in die dem internen Load Balancer zugewiesene IP-Adresse auflöst (der Wert von "$privIP" im vorangegangenen Azure PowerShell-Befehlsblock).

Anschließend erstellen Sie mit dem folgenden PowerShell-Befehlsblock die virtuellen Computer für die zwei Webserver. Die Werte für diesen PowerShell-Befehlsblock entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks
- Tabelle S für Ihr Subnetz
- Tabelle ST für Ihre Speicherkonten
- Tabelle A für Ihre Verfügbarkeitsgruppen

Die Tabelle M haben Sie in [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md), die Tabellen V, S, ST und A haben Sie in [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md) ausgefüllt.

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure PowerShell-Eingabeaufforderung aus.

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second second SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Verwenden Sie einen Remotedesktopclient Ihrer Wahl, und erstellen Sie eine Remotedesktopverbindung mit jedem virtuellen Computer, der als Webserver fungiert. Verwenden Sie den zugehörigen Intranet-DNS- oder Computernamen und die Anmeldeinformationen des lokalen Administratorkontos.

Treten Sie dann für jeden virtuellen Computer, der als Webserver fungiert, der geeigneten Active Directory-Domäne bei, indem Sie an der Windows PowerShell-Eingabeaufforderung diese Befehle ausführen.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Beachten Sie, dass Sie Anmeldeinformationen für ein Domänenkonto angeben müssen, nachdem Sie den **Add-Computer**-Befehl eingegeben haben.

Stellen Sie nach dem Neustart unter Verwendung eines Kontos mit lokalen Administratorberechtigungen eine neue Verbindung her.

Installieren und konfigurieren Sie jetzt für jeden Webserver IIS.

1. Führen Sie Server-Manager aus, und klicken Sie dann auf **Rollen und Features hinzufügen**.
2. Klicken Sie auf der Seite "Vorbereitungen" auf **Weiter**.
3. Klicken Sie auf der Seite "Installationstyp auswählen" auf **Weiter**.
4. Klicken Sie auf der Seite "Zielserver auswählen" auf **Weiter**.
5. Klicken Sie auf der Seite "Serverrollen" in der Liste **Rollen** auf **Webserver (IIS)**.
6. Klicken Sie bei entsprechender Aufforderung auf **Features hinzufügen** und anschließend auf **Weiter**.
7. Klicken Sie auf der Seite "Features auswählen" auf **Weiter**.
8. Klicken Sie auf der Seite "Webserver (IIS)" auf **Weiter**.
9. Aktivieren oder deaktivieren Sie auf der Seite "Rollendienste auswählen" die Kontrollkästchen für die Dienste, die Sie für Ihre Branchenanwendung benötigen, und klicken Sie dann auf **Weiter**. 10. Klicken Sie auf der Seite "Installationsauswahl bestätigen" auf **Installieren**.

## Bereitstellen Ihrer Branchenanwendung auf den virtuellen Computern, die als Webserver fungieren

Von einem Computer in Ihrem lokalen Netzwerk:

1.	Fügen Sie die Dateien für Ihre Branchenanwendung zu den zwei Webservern hinzu.
2.	Erstellen Sie die Datenbanken für Ihre Branchenanwendung auf dem SQL Server-Cluster.
3.	Testen Sie den Zugriff auf Ihre Branchenanwendung und ihre Funktionalität.

In diesem Diagramm sehen Sie die nach erfolgreichem Abschluss dieser Phase erstellte Konfiguration.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieser Workload wechseln Sie zu [Phase 5: Erstellen der Verfügbarkeitsgruppe und Hinzufügen der Anwendungsdatenbanken](virtual-machines-workload-high-availability-LOB-application-phase5.md).

## Zusätzliche Ressourcen

[Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: SharePoint Server 2013-Farm](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=August15_HO9-->