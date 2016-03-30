<properties 
   pageTitle="Informationen zu VPN-Gateways für standortübergreifende Verbindungen in Virtual Network | Microsoft Azure"
   description="Erfahren Sie, wie Sie VPN Gateways einsetzen, die für standortübergreifende Site-to-Site-Verbindungen für Hybridkonfigurationen, VNet-to-VNet-Verbindungen und Point-to-Site-Verbindungen verwendet werden können."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Informationen zu VPN-Gateways

VPN Gateways, die auch als Azure Virtual Network Gateways bezeichnet werden, werden zum Senden von Netzwerkdatenverkehr zwischen virtuellen Netzwerken und lokalen Standorten verwendet. Außerdem dienen sie zum Senden von Datenverkehr zwischen mehreren virtuellen Netzwerken in Azure (VNET-zu-VNET). In den folgenden Abschnitten werden die Elemente beschrieben, die zu einem VPN Gateway gehören.

Die Anleitung, die Sie zum Erstellen des VPN Gateways verwenden, richtet sich nach dem Bereitstellungsmodell, das Sie bei der Erstellung Ihres virtuellen Netzwerks genutzt haben. Wenn Sie Ihr VNET beispielsweise mit dem klassischen Bereitstellungsmodell erstellt haben, verwenden Sie die Richtlinien und Anleitungen für das klassische Bereitstellungsmodell, um das VPN Gateway zu erstellen und zu konfigurieren. Es ist nicht möglich, ein Resource Manager VPN Gateway für ein virtuelles Netzwerk zu erstellen, das auf dem klassischen Bereitstellungsmodell basiert.

Weitere Informationen zu Bereitstellungsmodellen finden Sie unter [Grundlagen des Resource Manager- und klassischen Bereitstellungsmodells](../resource-manager-deployment-model.md).


## <a name="gwsub"></a>Gatewaysubnetz

Sie müssen zuerst ein Gatewaysubnetz für Ihr VNET erstellen, um ein VPN Gateway zu konfigurieren. Alle Gatewaysubnetze müssen den Namen „GatewaySubnet“ haben, damit sie einwandfrei funktionieren.

Die Mindestgröße des Gatewaysubnetzes hängt gänzlich von der Konfiguration ab, die Sie erstellen möchten. Obwohl es bei manchen Konfigurationen möglich ist, ein Gatewaysubnetz mit einer Größe von nur /29 zu erstellen, empfehlen wir, ein Gatewaysubnetz von mindestens /28 (/28, /27, /26 usw.) zu erstellen.

Wenn Sie eine höhere Gatewaygröße wählen, wird verhindert, dass Sie die Größenbeschränkung für Gateways erreichen. Wenn Sie beispielsweise ein Gateway mit einem Gatewaysubnetz der Größe /29 erstellt haben und eine Konfiguration zur Koexistenz von Site-to-Site und ExpressRoute durchführen möchten, müssen Sie folgende Schritte ausführen: Sie müssen das Gateway löschen, das Gatewaysubnetz löschen, das Gatewaysubnetz der Größe /28 oder größer erstellen und dann das Gateway neu erstellen.

Indem Sie gleich ein größeres Gatewaysubnetz erstellen, können Sie später Zeit sparen, wenn Sie der Netzwerkumgebung neue Konfigurationsfeatures hinzufügen.

Im Beispiel unten ist ein Gatewaysubnetz mit dem Namen GatewaySubnet zu sehen. Sie erkennen, das mit der CIDR-Notation die Größe /27 angegeben wird. Dies ist für die meisten Konfigurationen groß genug für eine ausreichende Zahl von IP-Adressen, die derzeit üblich sind.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27


## <a name="gwtype"></a>Gatewaytypen

Mit dem Gatewaytyp wird angegeben, wie die Verbindung für das Gateway selbst hergestellt wird. Es ist eine erforderliche Konfigurationseinstellung für das Resource Manager-Bereitstellungsmodell. Verwechseln Sie den Gatewaytyp nicht mit dem VPN-Typ, mit dem der Typ der Weiterleitung für Ihr VPN angegeben wird. Die verfügbaren Werte für den Gatewaytyp sind *Vpn* und *ExpressRoute*.


Im Beispiel für das Resource Manager-Bereitstellungsmodell wird -GatewayType als *Vpn* angegeben. Beim Erstellen eines Gateways müssen Sie sicherstellen, dass der Gatewaytyp für Ihre Konfiguration korrekt ist.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>Gateway-SKUs

Beim Erstellen eines VPN Gateways müssen Sie die gewünschte Gateway-SKU angeben. Es gibt drei VPN-Gateway-SKUs:

- Basic
- Standard
- HighPerformance

Im Beispiel unten wird GatewaySku als *Standard* angegeben.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

### Geschätzter aggregierter Durchsatz nach SKU und Gatewaytyp


In der folgenden Tabelle sind die Gatewaytypen und der geschätzte zusammengefasste Durchsatz angegeben. Für die einzelnen Gateway-SKUs gelten unterschiedliche Preise. Informationen zu den Preisen finden Sie unter [VPN-Gateway – Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/). Diese Tabelle betrifft sowohl das Resource Manager-Bereitstellungsmodell als auch das klassische Bereitstellungsmodell.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>VPN-Typen

Für jede Konfiguration ist ein bestimmter VPN-Typ erforderlich, damit sie funktioniert. Wenn Sie zwei Konfigurationen kombinieren, z.B. das Erstellen einer Site-to-Site-Verbindung und einer Point-to-Site-Verbindung mit demselben VNET, müssen Sie einen VPN-Typ verwenden, mit dem beide Verbindungsanforderungen erfüllt werden. Bei einer Koexistenz von Point-to-Site- und Site-to-Site-Verbindungen müssen Sie einen routenbasierten VPN-Typ nutzen, wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, bzw. ein dynamisches Gateway, wenn Sie das klassische Bereitstellungsmodell verwenden.

Beim Erstellen der Konfiguration wählen Sie den VPN-Typ aus, der für Ihre Verbindung erforderlich ist.

Es gibt zwei VPN-Typen:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Im Beispiel für das Resource Manager-Bereitstellungsmodell wird -VpnType als *RouteBased* angegeben. Beim Erstellen eines Gateways müssen Sie sicherstellen, dass -VpnType für Ihre Konfiguration korrekt ist.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## <a name="lng"></a>Gateways des lokalen Netzwerks

Mit dem Gateway des lokalen Netzwerks ist normalerweise Ihr lokaler Standort gemeint. Beim klassischen Bereitstellungsmodell wurde das Gateway des lokalen Netzwerks als „Lokaler Standort“ bezeichnet. Sie geben dem Gateway des lokalen Netzwerks einen Namen, stellen die öffentliche IP-Adresse des lokalen VPN-Geräts bereit und geben die Adresspräfixe an, die sich am lokalen Standort befinden. Azure sucht unter den Zieladresspräfixen nach Netzwerkdatenverkehr, sieht in der Konfiguration nach, die Sie für das Gateway des lokalen Netzwerks angegeben haben, und leitet die Pakete entsprechend weiter. Sie können diese Adresspräfixe bei Bedarf ändern.



### Ändern von Adresspräfixen – Resource Manager

Beim Ändern von Adresspräfixen unterscheidet sich das Verfahren in Abhängigkeit davon, ob Sie Ihr VPN Gateway bereits erstellt haben. Weitere Informationen finden Sie im Artikelabschnitt [Ändern von Adresspräfixen für ein lokales Netzwerkgateway](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

Im Beispiel unten sehen Sie, dass ein Gateway des lokalen Netzwerks mit dem Namen MyOnPremiseWest angegeben ist und zwei IP-Adresspräfixe enthält.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Ändern von Adresspräfixen – Klassische Bereitstellung

Wenn Sie Ihre lokalen Standorte bei Verwendung des klassischen Bereitstellungsmodells ändern, können Sie hier die Konfigurationsseite „Lokale Netzwerke“ im klassischen Portal verwenden oder die Datei für die Netzwerkkonfiguration (NETCFG.XML) direkt ändern.


## VPN-Geräte

Sie müssen sicherstellen, dass das VPN-Gerät, dessen Einsatz Sie planen, den für die Konfiguration erforderlichen VPN-Typ unterstützt. Weitere Informationen zu kompatiblen VPN-Geräten finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).

## Gatewayanforderungen


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## Nächste Schritte

Lesen Sie sich die weiteren Informationen im Artikel [Häufig gestellte Fragen zum VPN Gateway](vpn-gateway-vpn-faq.md) durch, bevor Sie mit der Planung und dem Entwurf Ihrer Konfiguration fortfahren.





 

<!---HONumber=AcomDC_0323_2016-->