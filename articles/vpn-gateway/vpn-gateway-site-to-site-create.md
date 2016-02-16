<properties
   pageTitle="Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Gateway-Verbindung mit dem klassischen Azure-Portal | Microsoft Azure"
   description="Erstellen eines VNet mit einer S2S-VPN-Gateway-Verbindung für standortübergreifende und Hybridkonfigurationen mithilfe des klassischen Bereitstellungsmodells."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/04/2016"
   ms.author="cherylmc"/>

# Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung mit dem klassischen Azure-Portal

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Dieser Artikel führt Sie durch den Erstellungsvorgang eines virtuellen Netzwerks und einer Standort-zu-Standort-VPN-Verbindung mit Ihrem lokalen Netzwerk. Standort-zu-Standort-Verbindungen können für standortübergreifende Konfigurationen und Hybridkonfigurationen verwendet werden. Dieser Artikel gilt für das **klassische** Bereitstellungsmodell. Wenn Sie eine Standort-zu-Standort-Verbindung für das **Ressourcen-Manager**-Bereitstellungsmodell erstellen möchten, finden Sie Informationen dazu unter [Konfigurieren einer Standort-zu-Standort-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md). Wenn Sie VNets miteinander verbinden, aber keine Verbindung mit einem lokalen Standort erstellen möchten, finden Sie unter [Konfigurieren einer VNet-zu-VNet-Verbindung im klassischen Azure-Portal](virtual-networks-configure-vnet-to-vnet-connection.md) oder [Konfigurieren einer VNet-zu-VNet-Verbindung für das Ressourcen-Manager-Bereitstellungsmodell](vpn-gateway-vnet-vnet-rm-ps.md) entsprechende Informationen.

**Informationen zu Azure-Bereitstellungsmodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 
## Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

- Ein kompatibles VPN-Gerät (und eine Person, die es konfigurieren kann). Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md). Wenn Sie sich mit dem Konfigurieren des VPN-Geräts oder mit den IP-Adressbereichen Ihrer lokalen Netzwerkkonfiguration nicht auskennen, müssen Sie sich an eine Person wenden, die Ihnen diese Details liefern kann.

-  Eine externe öffentliche IP-Adresse für Ihr VPN-Gerät. Diese IP-Adresse darf sich nicht hinter einer NAT befinden.

- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.


## Erstellen des virtuellen Netzwerks

1. Melden Sie sich beim **klassischen Azure-Portal** an.

2. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

3. Geben Sie die Informationen auf den folgenden Seiten an, um Ihr VNet zu erstellen.

## Seite "Details zu Virtual Network"

Geben Sie Folgendes ein:

- **Name**: Name des virtuellen Netzwerks. Beispiel: *EastUSVNet*. Sie verwenden den hier festgelegten Namen bei der Bereitstellung Ihrer VMs und PaaS-Instanzen. Deshalb sollte der Name nicht zu kompliziert sein.
- **Speicherort**: Der Speicherort steht in direkter Beziehung zu dem physischen Standort (Region), an dem sich Ihre Ressourcen (VMs) befinden soll. Wenn Sie z. B. möchten, dass sich die virtuellen Computer, die Sie für Ihr virtuelles Netzwerk bereitstellen, physisch in *East US* (USA, Osten) befinden, wählen Sie diesen Speicherort aus. Sie können die Ihrem virtuellen Netzwerk zugeordnete Region nach dem Erstellen nicht mehr ändern.

## Seite "DNS-Server und VPN-Konnektivität"

Geben Sie die folgenden Informationen ein, und klicken Sie dann unten rechts auf den Pfeil „Weiter“.

- **DNS-Server**: Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, oder wählen Sie einen zuvor registrierten DNS-Server im Kontextmenü aus. Durch diese Einstellung wird kein DNS-Server erstellt. Sie bietet die Möglichkeit, die DNS-Server anzugeben, die Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten.
- **Standort-zu-Standort-VPNs konfigurieren:** Aktivieren Sie das Kontrollkästchen **Ein Site-to-Site-VPN konfigurieren**.
- **Lokales Netzwerk:** Ein lokales Netzwerk stellt Ihren lokalen physischen Standort dar. Sie können ein zuvor erstelltes lokales Netzwerk auswählen oder ein neues lokales Netzwerk erstellen. Wenn Sie ein zuvor erstelltes lokales Netzwerk auswählen, sollten Sie aber auf der Konfigurationsseite **Lokale Netzwerke** sicherstellen, dass die IP-Adresse des VPN-Geräts (öffentliche IPv4-Adresse), das Sie für diese Verbindung nutzen, korrekt ist.

## Seite "Site-to-Site-Konnektivität"

Wenn Sie ein neues lokales Netzwerk erstellen, wird die Seite **Site-to-Site-Konnektivität** angezeigt. Wenn Sie ein zuvor erstelltes lokales Netzwerk verwenden möchten, wird diese Seite nicht im Assistenten angezeigt, und Sie können mit dem nächsten Abschnitt fortfahren.

Geben Sie die folgenden Informationen ein, und klicken Sie dann auf den Pfeil „Weiter“.

- 	**Name:** Der Name, den Sie Ihrem lokalen Netzwerkstandort geben möchten.
- 	**IP-Adresse des VPN-Geräts:** Dies ist die öffentliche IPv4-Adresse des lokalen VPN-Geräts, mit dem Sie die Verbindung mit Azure herstellen. Das VPN-Gerät kann sich nicht hinter einer NAT befinden.
- 	**Adressraum:** Umfasst die Start-IP und CIDR (Adressenanzahl). Hier geben Sie die Adressbereiche an, die Sie über das Gateway für das virtuelle Netzwerk an Ihren lokalen Standort senden möchten. Wenn eine IP-Zieladresse in die hier angegebenen Bereiche fällt, wird sie über das Gateway für das virtuelle Netzwerk geleitet.
- 	**Adressraum hinzufügen:** Wenn Sie mehrere Adressbereiche über das Gateway für das virtuelle Netzwerk senden möchten, geben Sie hier alle zusätzlichen Adressbereiche an. Sie können die Bereiche später auf der Seite **Lokales Netzwerk** hinzufügen oder entfernen.

## Seite "Adressräume von Virtual Network"

Geben Sie den Adressbereich an, den Sie für Ihr virtuelles Netzwerk verwenden möchten. Dies sind die dynamischen IP-Adressen (DIPS), die den virtuellen Computern und anderen Rolleninstanzen zugewiesen werden, die Sie für dieses virtuelle Netzwerk bereitstellen.

Es ist besonders wichtig, einen Bereich auszuwählen, der sich nicht mit den anderen Bereichen überschneidet, die für Ihr lokales Netzwerk verwendet werden. Sprechen Sie sich mit Ihrem Netzwerkadministrator ab. Der Netzwerkadministrator muss ggf. einen Bereich von IP-Adressen aus dem Adressraum Ihres lokalen Netzwerks reservieren, den Sie für Ihr virtuelles Netzwerk verwenden können.

Geben Sie die folgenden Informationen ein, und klicken Sie dann auf das Häkchen in der unteren rechten Ecke, um Ihr Netzwerk zu konfigurieren.

- **Adressraum:** Umfasst die Start-IP und die Anzahl von Adressen. Stellen Sie sicher, dass sich die angegebenen Adressräume und die Adressräume im lokalen Netzwerk nicht überschneiden.
- **Subnetz hinzufügen:** Umfasst die Start-IP und die Anzahl von Adressen. Zusätzliche Subnetze sind nicht erforderlich, aber Sie können eine getrenntes Subnetz für virtuelle Computer erstellen, die über statische DIPs verfügen sollen. Vielleicht möchten Sie jedoch auch Ihre virtuellen Computer in einem Subnetz zusammenfassen, das von Ihren anderen Rolleninstanzen getrennt ist.
- **Gatewaysubnetz hinzufügen:** Klicken Sie auf diese Option, um das Gatewaysubnetz hinzuzufügen. Das Gatewaysubnetz wird nur für das Gateway des virtuellen Netzwerks verwendet und ist für diese Konfiguration erforderlich.

Klicken Sie auf das Häkchen am rechten unteren Rand der Seite, damit das virtuelle Netzwerk erstellt wird. Sobald der Vorgang abgeschlossen ist, wird im klassischen Azure-Portal auf der Seite **Netzwerke** unter **Status** der Eintrag **Erstellt** angezeigt. Nachdem das VNet erstellt wurde, können Sie Ihr Gateway für das virtuelle Netzwerk konfigurieren.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## Konfigurieren des Gateways für das virtuelle Netzwerk

Als Nächstes konfigurieren Sie das Gateway für das virtuelle Netzwerk, um eine sichere Standort-zu-Standort-Verbindung herzustellen. Weitere Informationen finden Sie unter [Konfigurieren eines Gateways für ein virtuelles Netzwerk im klassischen Azure-Portal](vpn-gateway-configure-vpn-gateway-mp.md).

## Nächste Schritte

Sie können dem virtuellen Netzwerk virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines/virtual-machines-create-custom.md).

Wenn Sie eine Verbindung zwischen dem klassischen virtuellen Netzwerk und einem virtuellen Netzwerk, das mit dem Azure-Ressourcen-Manager-Modus erstellt wurde, konfigurieren möchten, finden Sie weitere Informationen unter [Herstellen einer Verbindung zwischen klassischen VNets und VNets des Azure-Ressourcen-Managers](../virtual-network/virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=AcomDC_0211_2016-->