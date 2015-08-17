<properties 
   pageTitle="Herstellen einer Verbindung zwischen klassischen VNets und ARM-VNets in Azure"
   description="Weitere Informationen zum Erstellen einer VPN-Verbindung zwischen klassischen VNets und neuen VNets"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/14/2015"
   ms.author="telmos" />

# Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets

Azure verfügt zurzeit über zwei Verwaltungsmodi: Azure Service Manager (als "klassisch" bezeichnet) und Azure Resource Manager (ARM). Wenn Sie Azure seit einiger Zeit verwenden, verfügen Sie wahrscheinlich über virtuelle Azure-Computer und Instanzrollen, die in einem klassischen VNet ausgeführt werden. Und Ihre neueren virtuellen Computer und Rolleninstanzen werden ggf. in einem in ARM erstellten VNet ausgeführt.

Unter diesen Umständen sollten Sie sicherstellen, dass die neue Infrastruktur mit Ihren klassischen Ressourcen kommunizieren kann. Sie können zu diesem Zweck eine VPN-Verbindung zwischen den beiden VNets erstellen.

In diesem Artikel erfahren Sie, wie eine Standort-zu-Standort-VPN-Verbindung (S2S) zwischen einem klassischen VNet und einem ARM-VNet erstellt wird.

>[AZURE.NOTE]Dieser Artikel geht davon aus, dass Sie bereits über klassische VNets und ARM-VNets verfügen und mit dem Konfigurieren einer S2S-VPN-Verbindung für klassische VNets vertraut sind. Eine detaillierte End-to-End-Lösung für S2S-VPN-Konnektivität zwischen klassischen und ARM-VNets finden Sie im [Lösungshandbuch - Herstellen einer Verbindung zwischen einem klassischen VNet und einem ARM-VNet mithilfe einer S2S-VPN-Verbindung](../virtual-networks-arm-asm-s2s.md).

Unten finden Sie eine Übersicht über die Aufgaben, die ausgeführt werden müssen, um eine S2S-VPN-Verbindung zwischen einem klassischen VNet und einem ARM-VNet mithilfe von Azure-Gateways zu erstellen.

1 -[Erstellen eines VPN-Gateways für ein klassisches VNet](#Step-1:-Create-a-VPN-gateway-for-the-classic-VNet)

2 -[Erstellen eines VPN-Gateways für ein ARM-VNet](#Step-2:-Create-a-VPN-gateway-for-the-ARM-VNet)

3 -[Erstellen einer Verbindung zwischen den Gateways](#Step-3:-Create-a-connection-between-the-gateways)

## Schritt 1: Erstellen eines VPN-Gateways für ein klassisches VNet

Befolgen Sie die Anweisungen unten, um das VPN-Gateway für das klassische VNet zu erstellen.

1. Öffnen Sie das klassische Portal über https://manage.windowsazure.com, und geben Sie Ihre Anmeldeinformationen ein, falls erforderlich.
2. Klicken Sie in der unteren linken Ecke des Bildschirms auf die Schaltfläche **NEU**, und klicken Sie dann auf **NETZWERKDIENSTE**, auf**VIRTUELLE NETZWERKE** und anschließend auf **LOKALES NETZWERK HINZUFÜGEN**.
3. Geben Sie im Fenster **Details zum lokalen Netzwerk angeben** einen Namen für das ARM-VNet an, mit dem Sie eine Verbindung herstellen möchten, und klicken Sie dann in der unteren rechten Ecke des Fensters auf die Pfeilschaltfläche.
3. Geben Sie im Adressraumtextfeld **Start-IP** das Netzwerkpräfix für das ARM-VNet ein, mit dem Sie eine Verbindung herstellen möchten. 
4. Wählen Sie in der Dropdownliste **CIDR (ANZAHL ADRESSEN)** die Anzahl der Bits aus, die für den Netzwerkteil des CIDR-Blocks verwendet wird, der vom ARM-VNet verwendet wird, mit dem Sie eine Verbindung herstellen möchten.
5. Geben Sie in **IP-ADRESSE DES VPN-GERÄTS (OPTIONAL)** eine beliebige gültige öffentliche IP-Adresse ein. Wir werden diese IP-Adresse später ändern. Klicken Sie dann unten rechts auf dem Bildschirm auf die Schaltfläche mit dem Häkchen. Die folgende Abbildung zeigt Beispieleinstellungen für diese Seite.

	![Einstellungen des lokalen Netzwerks](..\virtual-network\media\virtual-networks-arm-asm-s2s-howto\figurex1.png)

5. Klicken Sie auf der Seite **Netzwerke** auf **VIRTUELLE NETZWERKE**, klicken Sie dann auf Ihr klassisches VNet, und klicken Sie anschließend auf **KONFIGURIEREN**.
6. Aktivieren Sie unter **Site-to-Site-Konnektivität** das Kontrollkästchen **Eine Verbindung mit dem lokalen Netzwerk herstellen**.
7. Wählen Sie das lokale Netzwerk, das Sie in Schritt 4 erstellt haben, aus der Liste der verfügbaren Netzwerke aus der Dropdownliste **LOKALES NETZWERK** aus, und klicken Sie dann auf**SPEICHERN**.
8. Nachdem Sie die Einstellungen gespeichert haben, klicken Sie auf **DASHBOARD**, klicken Sie dann unten auf der Seite auf **GATEWAY ERSTELLEN**, klicken Sie auf **DYNAMISCHES ROUTING** und dann auf **JA**.
9. Warten Sie, bis das Gateway erstellt wurde, und kopieren Sie dann seine öffentliche IP-Adresse. Sie benötigen diese, um das Gateway im ARM-VNet einzurichten.

## Schritt 2: Erstellen eines VPN-Gateways für das ARM-VNet

Befolgen Sie die Anweisungen unten, um ein VPN-Gateway für das ARM-VNet zu erstellen.

1. Wechseln Sie in einer PowerShell-Konsole in den ARM-Modus, indem Sie den folgenden Befehl ausführen.

		Switch-AzureMode AzureResourceManager

2. Erstellen Sie ein lokales Netzwerk, indem Sie den folgenden Befehl ausführen. Das lokale Netzwerk muss CIDR-Block des klassischen VNet verwenden, mit dem Sie eine Verbindung herstellen möchten, sowie die öffentliche IP-Adresse des Gateways, die Sie in Schritt 1 oben erstellt haben.

		New-AzureLocalNetworkGateway -Name VNetClassicNetwork `
			-Location "East US" -AddressPrefix "10.0.0.0/20" `
			-GatewayIpAddress "168.62.190.190" -ResourceGroupName RG1

3. Erstellen Sie eine öffentliche IP-Adresse für das Gateway, indem Sie den folgenden Befehl ausführen.

		$ipaddress = New-AzurePublicIpAddress -Name gatewaypubIP`
			-ResourceGroupName RG1 -Location "East US" `
			-AllocationMethod Dynamic

4. Rufen Sie das für das Gateway verwendete Subnetz ab, indem Sie den folgenden Befehl ausführen.

		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureVirtualNetwork -Name VNetARM -ResourceGroupName RG1) 

	>[AZURE.IMPORTANT]Das Gatewaysubnetz muss bereits vorhanden sein, und es muss den Namen "GatewaySubnet" tragen.

5. Erstellen Sie ein IP-Konfigurationsobjekt für das Gateway, indem Sie den folgenden Befehl ausführen. Beachten Sie die ID für das Gatewaysubnetz. Dieses Subnetz muss im VNet vorhanden sein.

		$ipconfig = New-AzureVirtualNetworkGatewayIpConfig `
			-Name ipconfig -PrivateIpAddress 10.1.2.4 `
			-SubnetId $subnet.id -PublicIpAddressId $ipaddress.id

	>[AZURE.IMPORTANT]An die Parameter *SubnetzId* und *PublicIpAddressId* müssen die Eigenschaft "Id" aus dem Subnetz und die IP-Adressobjekte übergeben werden. Sie können keine einfache Zeichenfolge verwenden.
	
5. Erstellen Sie das ARM-VNet-Gateway, indem Sie den folgenden Befehl ausführen.

		New-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1 `
			-Location "East US" -GatewayType Vpn -IpConfigurations $ipconfig `
			-EnableBgp $false -VpnType RouteBased

6. Nachdem das VPN-Gateway erstellt wurde, rufen Sie seine öffentliche IP-Adresse an, indem Sie den folgenden Befehl ausführen. Kopieren Sie die IP-Adresse. Sie benötigen diese, um das klassische VNet im lokalen Netzwerk zu konfigurieren.

		Get-AzurePublicIpAddress -Name gatewaypubIP -ResourceGroupName RG1

## Schritt 3: Erstellen einer Verbindung zwischen den Gateways

1. Öffnen Sie das klassische Portal über https://manage.windowsazure.com, und geben Sie Ihre Anmeldeinformationen ein, falls erforderlich.
2. Führen Sie im klassischen Portal einen Bildlauf nach unten aus, und klicken Sie auf **NETZWERKE**. Klicken Sie dann auf**LOKALE NETZWERKE**, auf das ARM-VNet, mit dem eine Verbindung hergestellt werden soll, und dann auf die Schaltfläche **BEARBEITEN**.
3. Geben Sie unter **IP-ADRESSE DES VPN-GERÄTS (OPTIONAL)** die IP-Adresse für das ARM-VNet-Gateway ein, die Sie in Schritt 2 weiter oben abgerufen haben, und klicken Sie dann in der unteren rechten Ecke auf die NACH-RECHTS-TASTE. Klicken Sie anschließend auf die Schaltfläche mit dem Häkchen.
4. Wechseln Sie in einer PowerShell-Konsole in den Azure Service Manager-Modus, indem Sie den folgenden Befehl ausführen.

		Switch-AzureMode AzureServiceManager

5. Richten Sie einen gemeinsam verwendeten Schlüssel ein, indem Sie den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie die Namen der VNets in die Namen Ihrer eigenen VNets ändern.

		Set-AzureVNetGatewayKey -VNetName VNetClassic `
			-LocalNetworkSiteName VNetARM -SharedKey abc123

6. Wechseln Sie in einer PowerShell-Konsole in den Azure Resource Manager-Modus, indem Sie den folgenden Befehl ausführen.

		Switch-AzureMode AzureResourceManager

7. Erstellen Sie die VPN-Verbindung, indem Sie die folgenden Befehle ausführen.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name VNetClassic -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name v1v2Gateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "East US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

## Nächste Schritte

- Weitere Informationen zum [Netzwerkressourcenanbieter (Network Resource Provider, NRP) für ARM](../resource-groups-networking.md).
- Erstellen einer [End-to-End-Lösung, die ein klassisches VNet mit einem ARM-VNet mithilfe einer S2S-VPN-Verbindung verbindet](../virtual-networks-arm-asm-s2s.md).

<!---HONumber=August15_HO6-->