<properties
	pageTitle="SharePoint-Intranetfarm-Arbeitsauslastung Phase 4: Konfigurieren der SharePoint-Server"
	description="In dieser vierten Phase der Intranet-Bereitstellung einer SharePoint 2013-Farm erstellen Sie die virtuellen Computer für die SharePoint-Server sowie eine neue SharePoint-Farm."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/05/2015"
	ms.author="josephd"/>

# SharePoint-Intranetfarm-Workload Phase 4: Konfigurieren der SharePoint-Server

In dieser Phase der Intranet-Bereitstellung einer SharePoint 2013-Farm mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in den Azure-Infrastrukturdiensten richten Sie die Anwendungs- und Webebenen der SharePoint-Farm ein und erstellen die Farm unter Verwendung des SharePoint-Konfigurations-Assistenten.

Diese Phase muss vor Beginn von [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md).

## Erstellen der virtuellen Computer für die SharePoint-Server in Azure

Sie benötigen für die SharePoint-Server vier virtuelle Computer. Zwei virtuelle SharePoint-Servercomputer sind als Front-End-Webserver vorgesehen und zwei für die Verwaltung und Bereitstellung der SharePoint-Anwendungen. Zwei SharePoint-Server auf jeder Ebene gewährleisten eine hohe Verfügbarkeit.

Mit dem folgenden Azure PowerShell-Befehlsblock erstellen Sie die virtuellen Computer für diese vier SharePoint-Server. Geben Sie die Werte für die Variablen ein, lassen Sie dabei aber die < and >-Zeichen weg. Die Werte für diesen Azure PowerShell-Befehlsblock entnehmen Sie den folgenden Tabellen:

- Tabelle M für Ihre virtuellen Computer
- Tabelle V für die Einstellungen Ihres virtuellen Netzwerks
- Tabelle S für Ihr Subnetz
- Tabelle A für Ihre Verfügbarkeitsgruppen
- Tabelle C für Ihre Clouddienste

Tabelle M haben Sie in [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md) ausgefüllt und die Tabellen V, S, A und C in [Phase 1: Konfigurieren von Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Führen Sie nach der Bereitstellung der richtigen Werte den daraus resultierenden Befehlsblock an der Azure-PowerShell-Eingabeaufforderung aus.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

Gehen Sie für jeden der vier SharePoint-Server nach den Anweisungen der Prozedur [Anmelden bei einem virtuellen Computer über eine Remotedesktopverbindung](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) vor, um sich mit den Anmeldeinformationen für das Konto "[Domäne]\sp_farm_db" anzumelden. Diese Anmeldeinformationen haben Sie in [Phase 2: Konfigurieren von Domänencontrollern](virtual-machines-workload-intranet-sharepoint-phase2.md) erstellt.

Gehen Sie für jeden der vier SharePoint-Server nach den Anweisungen der Prozedur [Testen der Konnektivität](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) vor, um die Konnektivität der Standorte Ihres Unternehmensnetzwerks zu testen.

## Konfigurieren der SharePoint-Farm

Führen Sie zum Konfigurieren des ersten SharePoint-Servers der Farm folgende Schritte aus:

1.	Doppelklicken Sie auf dem Desktop des ersten SharePoint-Anwendungsservers auf **Konfigurations-Assistent für SharePoint 2013-Produkte**. Wenn Sie gefragt werden, ob das Programm Änderungen am Computer vornehmen darf, klicken Sie auf **Ja**.
2.	Klicken Sie auf der Seite **Willkommen bei den SharePoint-Produkten** auf **Weiter**.
3.	Ein Dialogfeld des **Konfigurations-Assistenten für SharePoint-Produkte** weist Sie darauf hin, dass Dienste (z. B. IIS) neu gestartet oder zurückgesetzt werden. Klicken Sie auf **Ja**.
4.	Wählen Sie auf der Seite **Verbindung mit einer Serverfarm herstellen** die Option **Eine neue Serverfarm erstellen** aus, und klicken Sie auf **Weiter**.
5.	Auf der Seite **Einstellungen für die Konfigurationsdatenbank angeben**:
 - Geben Sie unter **Datenbankserver** den Namen des primären Datenbankservers ein.
 - Geben Sie unter **Benutzername** den in [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-intranet-sharepoint-phase2.md) erstellten Namen "[Domäne]**\sp_farm_db**" ein. Das Konto "sp_farm_db" verfügt, wie Sie sich erinnern, über "sysadmin"-Berechtigungen für den Datenbankserver.
 - Geben Sie unter **Kennwort** das Kennwort für das Konto „sp_farm_db“ ein.
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
6.	Klicken Sie in der Liste mit den Datenbanknamen auf **SharePoint_Config**, und klicken Sie dann auf **Weiter**.
7.	Geben Sie auf der Seite **Farmsicherheitseinstellungen angeben** die im vorherigen Verfahren festgelegte Passphrase ein. Klicken Sie auf **Weiter**.
8.	Die Seite **Der Konfigurations-Assistent für SharePoint-Produkte wird abgeschlossen** wird angezeigt. Klicken Sie auf **Weiter**.
9.	Klicken Sie auf der Seite **Konfiguration erfolgreich** auf **Fertig stellen**.

Bei der Erstellung der Farm konfiguriert SharePoint auf dem virtuellen Computer des primären SQL-Servers eine Reihe von Serveranmeldungen. In SQL Server 2012 wurde das Konzept von Benutzern mit Kennwörtern für eigenständige Datenbanken eingeführt. Die Datenbank selbst speichert alle Metadaten und Benutzerinformationen der Datenbank, so dass sich ein Benutzer, der in dieser Datenbank definiert ist, nicht gesondert anmelden muss. Die Informationen dieser Datenbank werden durch die Verfügbarkeitsgruppe repliziert und stehen auch nach einem Failover noch zur Verfügung. Weitere Informationen finden Sie unter [Eigenständige Datenbanken](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Standardmäßig sind SharePoint-Datenbanken jedoch keine eigenständigen Datenbanken. Daher müssen Sie den sekundären Datenbankserver manuell so konfigurieren, dass er über den gleichen Anmeldungssatz für die SharePoint-Farmkonten verfügt wie der primäre Datenbankserver. Diese Synchronisierung können Sie in SQL Server Management Studio ausführen, indem Sie zur selben Zeit eine Verbindung zu beiden Servern herstellen.

Nach Abschluss dieser anfänglichen Konfiguration stehen weitere Konfigurationsoptionen für die Funktionen der SharePoint-Farm zur Verfügung. Weitere Informationen finden Sie unter [Planen von SharePoint 2013 für Azure-Infrastrukturdienste](http://msdn.microsoft.com/library/dn275958.aspx).

## Konfigurieren des internen Lastenausgleichs

Durch internen Lastenausgleich stellen Sie sicher, dass der Datenverkehr innerhalb der SharePoint-Farm gleichmäßig auf die beiden Front-End-Webserver verteilt wird. Dazu müssen Sie eine interne Lastenausgleichsinstanz konfigurieren, die aus einem Namen und einer eigenen IP-Adresse aus dem Adressraum des Subnetzes besteht. Mit den folgenden Azure PowerShell-Befehlen finden Sie heraus, ob eine IP-Adresse, die Sie für den internen Lastenausgleich ausgewählt haben, verfügbar ist:

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Wenn das Feld **IsAvailable** in der Ausgabe des Befehls **Test-AzureStaticVNetIP** **True** lautet, können Sie die IP-Adresse verwenden.

Führen Sie auf dem lokalen Computer an der Eingabeaufforderung von Azure PowerShell den folgenden Befehlssatz aus.

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.

	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Fügen Sie danach der DNS-Infrastruktur Ihres Unternehmens einen DNS-Adresseintrag hinzu, der den vollständig qualifizierten Domänennamen der SharePoint-Farm (z. B. sp.corp.contoso.com) auf die der internen Lastenausgleichsinstanz zugewiesene IP-Adresse auflöst (der Wert von **$IP** im vorangegangenen Azure PowerShell-Befehlsblock).

Hier sehen Sie die nach erfolgreichem Abschluss dieser Phase erstellte Konfiguration.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieser Workload gehen Sie zu [Phase 5: Erstellen der Verfügbarkeitsgruppe und Hinzufügen der SharePoint-Datenbanken](virtual-machines-workload-intranet-sharepoint-phase5.md).

## Zusätzliche Ressourcen

[Bereitstellen von SharePoint mit SQL Server AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[In Azure-Infrastrukturdiensten gehostete SharePoint-Farmen](virtual-machines-sharepoint-infrastructure-services.md)

[Infografik zu SharePoint mit SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Microsoft Azure-Architekturen für SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=July15_HO4-->