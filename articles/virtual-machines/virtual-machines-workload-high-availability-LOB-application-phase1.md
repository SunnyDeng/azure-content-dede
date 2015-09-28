<properties 
	pageTitle="Branchenanwendung, Phase 1 | Microsoft Azure" 
	description="In Phase 1 der Branchenanwendung erstellen Sie in Azure das virtuelle Netzwerk und weitere Elemente der Azure-Infrastruktur." 
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
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Branchenanwendungs-Workload, Phase 1: Konfigurieren von Azure
 
[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen von Ressourcen mit dem Ressourcen-Manager-Bereitstellungsmodell.
 
In dieser Phase der Bereitstellung einer nur im Intranet verfügbaren Branchenanwendung mit hoher Verfügbarkeit in den Azure-Infrastrukturdiensten erstellen Sie die Azure-Netzwerk- und Speicherinfrastruktur. Diese Phase muss vor Beginn von [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) ausgeführt worden sein. Eine Übersicht über alle Phasen finden Sie unter [Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

Azure muss mit den folgenden grundlegenden Netzwerkkomponenten bereitgestellt werden:

- Ein standortübergreifendes virtuelles Netzwerk mit einem Subnetz zum Hosten der virtuellen Azure-Computer
- Zwei Azure-Speicherkonten zum Speichern von VHD-Datenträgerimages und weiteren Datenträgern
- Drei Verfügbarkeitsgruppen

## Voraussetzungen

Füllen Sie vor der Konfiguration der Azure-Komponenten die folgenden Tabellen aus. Dieser Abschnitt soll Ihnen bei der Konfiguration von Azure helfen. Am besten drucken Sie die Seite aus und notieren Sie sich in den Lücken die erforderlichen Informationen oder kopieren Sie den Abschnitt in ein Dokument und füllen Sie es in der elektronischen Version aus.

Die Einstellungen für das virtuelle Netzwerk (VNet) tragen Sie in Tabelle V ein.

Element | Konfigurationselement | Beschreibung | Wert 
--- | --- | --- | --- 
1\. | VNet-Name | Der Name, den Sie dem virtuellen Azure-Netzwerk zuweisen (z. B. SPFarmNet) . | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | VNet-Standort | Das Azure-Rechenzentrum, in dem sich das virtuelle Netzwerk befindet. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Name des lokalen Netzwerks | Der Name, den Sie Ihrem Unternehmensnetzwerk zuweisen. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | IP-Adresse des VPN-Geräts | Die öffentliche IPv4-Adresse der Schnittstelle Ihres VPN-Geräts im Internet. Fragen Sie Ihre IT-Abteilung nach dieser Adresse. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | VNet-Adressraum | Der Adressraum des virtuellen Netzwerks (definiert in einem einzigen privaten Adresspräfix). Fragen Sie Ihre IT-Abteilung nach diesem Adressraum. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Der erste DNS-Server für das virtuelle Netzwerk | Die vierte mögliche IP-Adresse für den Adressraum des zweiten Subnetzes des virtuellen Netzwerks (siehe Tabelle S). Fragen Sie Ihre IT-Abteilung nach dieser Adresse. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Der zweite DNS-Server für das virtuelle Netzwerk | Die fünfte mögliche IP-Adresse für den Adressraum des zweiten Subnetzes des virtuellen Netzwerks (siehe Tabelle S). Fragen Sie Ihre IT-Abteilung nach dieser Adresse. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | Gemeinsam verwendeter IPsec-Schlüssel | Eine aus 128 zufällig ausgewählten alphanumerischen Zeichen bestehende Zeichenfolge, die zur Authentifizierung beider Seiten der Site-to-Site-VPN-Verbindung verwendet wird. Fragen Sie Ihre IT- oder Sicherheitsabteilung nach diesem Schlüsselwert. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_


**Tabelle V: Konfiguration eines standortübergreifenden virtuellen Netzwerks**

Füllen Sie für das Subnetz dieser Lösung Tabelle S aus.

- Legen Sie für das erste Subnetz einen 29-Bit-Adressraum (mit einer /29-Präfixlänge) für das Azure-Gatewaysubnetz fest.
- Geben Sie für das zweite Subnetz einen Anzeigenamen, einen auf dem Adressraum des virtuellen Netzwerks basierenden einzelnen IP-Adressraum und einen beschreibenden Zweck an. 

Fragen Sie Ihre IT-Abteilung nach diesen Adressräumen, die aus dem Adressraum des virtuellen Netzwerks bestimmt werden. Beide Adressräume muss im CIDR-Format (Classless Interdomain Routing) eingegeben werden, das auch als Netzwerkpräfixformat bezeichnet wird. Beispiel: 10.24.64.0/20.

Item | Subnetzname | Subnetzadressraum | Zweck 
--- | --- | --- | --- 
1\. | Gatewaysubnetz | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Das von den virtuellen Azure-Gatewaycomputern verwendete Subnetz.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle S: Subnetze im virtuellen Netzwerk**

> [AZURE.NOTE]Diese vordefinierte Architektur verwendet aus Gründen der Einfachheit nur ein Subnetz. Wenn Sie zur Emulierung einer Subnetzisolierung verschiedene Datenverkehrsfilter überlagern möchten, können Sie Azure-[Netzwerksicherheitsgruppen](virtual-networks-nsg.md) verwenden.

Füllen Sie für die zwei lokalen DNS-Server, die Sie bei der anfänglichen Einrichtung der Domänencontroller in Ihrem virtuellen Netzwerk verwenden möchten, Tabelle D aus. Geben Sie jedem DNS-Server einen Anzeigenamen und eine einzelne IP-Adresse. Der Anzeigename muss nicht mit dem Hostnamen oder dem Computernamen des DNS-Servers übereinstimmen. Auch wenn hierfür nur zwei Einträge vorgesehen sind, können Sie noch weitere hinzufügen. Erarbeiten Sie diese Liste gemeinsam mit Ihrer IT-Abteilung.

Element | Anzeigename des DNS-Servers | IP-Adresse des DNS-Servers 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Tabelle D: Lokale DNS-Server**

Zur Weiterleitung von Paketen aus dem virtuellen Azure-Netzwerk an Ihr Unternehmensnetzwerk über die Site-to-Site-VPN-Verbindung müssen Sie das virtuelle Netzwerk mit einem lokalen Netzwerk konfigurieren, das eine Liste der Adressräume (in CIDR-Notation) für alle erreichbaren Standorte im lokalen Netzwerk Ihres Unternehmens enthält. Die Liste der Adressräume, die Ihr lokales Netzwerk definieren, muss eindeutig sein und darf sich nicht mit den Adressräumen anderer virtueller oder lokaler Netzwerke überschneiden.

Füllen Sie zur Angabe der Adressräume Ihres lokalen Netzwerks Tabelle L aus. Hierfür sind zwar nur drei Einträge vorgesehen, vermutlich werden Sie aber mehr benötigen. Erarbeiten Sie die Liste dieser Adressräume gemeinsam mit Ihrer IT-Abteilung.

Element | Adressraum des lokalen Netzwerks 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle L: Adresspräfixe für das lokale Netzwerk**

Sie benötigen Azure PowerShell, Version 0.9.5 oder höher. Führen Sie diesen Befehl aus, um Ihre Azure PowerShell-Version zu überprüfen.

	Get-Module azure | format-table version

Wenn Sie die aktuelle Version von Azure PowerShell installieren müssen, entfernen Sie die vorhandene Version über **Systemsteuerung – Programme und Funktionen**. Befolgen Sie anschließend die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md), um Azure PowerShell auf dem lokalen Computer zu installieren. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung.

Wählen Sie zunächst das richtige Azure-Abonnement für diese Befehle aus. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Sie erhalten den korrekten Abonnementnamen aus der Eigenschaft **SubscriptionName** der Ausgabe des Befehls **Get-AzureSubscription**.

Wechseln Sie in Azure PowerShell mit diesem Befehl in den Ressourcen-Manager-Modus.

	Switch-AzureMode AzureResourceManager 

Erstellen Sie eine neue Ressourcengruppe für Ihre Branchenanwendung.

Um einen eindeutigen Namen für die Ressourcengruppe zu finden, verwenden Sie diesen Befehl zum Auflisten der vorhandenen Ressourcengruppen.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Verwenden Sie diese Befehle, um die Azure-Speicherorte aufzulisten, in denen Sie virtuelle Computer auf Ressourcen-Manager-Basis erstellen können.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Erstellen Sie die neue Ressourcengruppe mit diesen Befehlen.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Virtuelle Computer auf Ressourcen-Manager-Basis benötigen ein Speicherkonto auf Ressourcen-Manager-Basis.

Item | Speicherkontoname | Zweck 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Das Storage Premium-Konto, das von den virtuellen SQL Server-Computern verwendet wird.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Das Storage Standard-Konto, das von allen anderen virtuellen Computern in der Workload verwendet wird. 

**Tabelle ST: Speicherkonten**

Sie benötigen diesen Namen beim Erstellen der virtuellen Computer in den Phasen 2, 3 und 4.

Sie müssen für jedes Speicherkonto einen global eindeutigen Namen angeben, der nur aus Kleinbuchstaben und Zahlen besteht. Mit diesem Befehl können Sie die vorhandenen Speicherkonten auflisten.

	Get-AzureStorageAccount | Sort Name | Select Name

Um zu testen, ob ein gewählter Speicherkontenname global eindeutig ist, müssen Sie den Befehl **Test-AzureName** im Azure-Dienstverwaltungsmodus von PowerShell ausführen. Verwenden Sie diese Befehle.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Wenn der Befehl "Test-AzureName" als Ergebnis **False** zurückgibt, ist der vorgeschlagene Name eindeutig. Wenn Sie einen eindeutigen Namen für jedes der beiden Speicherkonten festgelegt haben, aktualisieren Sie Tabelle ST, und wechseln Sie mit dem folgenden Befehl wieder in den Ressourcen-Manager-Modus von Azure PowerShell.

	Switch-AzureMode AzureResourceManager 

Führen Sie folgende Befehle aus, um das erste Speicherkonto zu erstellen.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Führen Sie folgende Befehle aus, um das zweite Speicherkonto zu erstellen.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Erstellen Sie das virtuelle Azure-Netzwerk, in dem Ihre Branchenanwendung gehostet werden soll.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

Verwenden Sie diese Befehle, um die Gateways für die Site-to-Site-VPN-Verbindung zu erstellen.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzurePublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Konfigurieren Sie das lokale VPN-Gerät für die Verbindung zum Azure-VPN-Gateway. Weitere Informationen finden Sie unter [Konfigurieren des VPN-Geräts](../virtual-networks/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Zum Konfigurieren des lokalen VPN-Geräts benötigen Sie Folgendes:

- Die öffentliche IPv4-Adresse des Azure-VPN-Gateways für Ihr virtuelles Netzwerk (aus der Ergebnisanzeige des Befehls **Get-AzurePublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**)
- Den vorinstallierten IPsec-Schlüssel für die Site-to-Site-VPN-Verbindung (Tabelle V – Element 8 – Spalte "Wert")

Stellen Sie anschließend sicher, dass der Adressraum des virtuellen Netzwerks aus Ihrem lokalen Netzwerk erreichbar ist. Dies erfolgt in der Regel durch Hinzufügen einer Route zwischen dem Adressraum des virtuellen Netzwerks und Ihrem VPN-Gerät und der Bekanntgabe dieser Route für den Rest der Routinginfrastruktur Ihres Unternehmensnetzwerks. Erarbeiten Sie diese Lösung gemeinsam mit Ihrer IT-Abteilung.

Definieren Sie als Nächstes die Namen von drei Verfügbarkeitsgruppen. Füllen Sie Tabelle A aus.

Element | Zweck | Name der Verfügbarkeitsgruppe 
--- | --- | --- 
1\. | Domänencontroller | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | SQL-Server | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Webserver | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabelle A: Namen der Verfügbarkeitsgruppen**

Sie benötigen diesen Namen beim Erstellen der virtuellen Computer in den Phasen 2, 3 und 4.

Erstellen Sie diese neuen Verfügbarkeitsgruppen mit folgenden Azure PowerShell-Befehlen.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Hier sehen Sie die nach erfolgreichem Abschluss dieser Phase erstellte Konfiguration.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase1/workload-lobapp-phase1.png)

## Nächster Schritt

Zum Fortsetzen der Konfiguration dieser Arbeitsauslastung gehen Sie zu [Phase 2: Konfigurieren der Domänencontroller](virtual-machines-workload-high-availability-LOB-application-phase2.md).

## Zusätzliche Ressourcen

[Bereitstellen einer hochverfügbaren Branchenanwendung in Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Architekturblaupause für Branchenanwendungen](http://msdn.microsoft.com/dn630664)

[Einrichten einer webbasierten Branchenanwendung in einer Hybrid Cloud zu Testzwecken](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Implementierungsrichtlinien für Azure-Infrastrukturdienste](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Azure-Infrastrukturdienste-Workload: SharePoint Server 2013-Farm](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Sept15_HO3-->