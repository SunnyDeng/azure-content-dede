<properties
   pageTitle="Konfigurieren einer Site-to-Site-Verbindung für ein virtuelles Netzwerk | Microsoft Azure"
   description="Sie erhalten Informationen zur Erstellung eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung für standortübergreifende und Hybridkonfigurationen."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor=""/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="cherylmc"/>

# Konfigurieren eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung

Sie können Ihren lokalen Standort mit einem virtuellen Netzwerk verbinden, indem Sie eine Site-to-Site-VPN-Verbindung erstellen. Bei diesem Verfahren werden Sie durch die Erstellung eines virtuellen Netzwerks und die Erstellung einer Site-to-Site-VPN-Verbindung zwischen Ihrem neu erstellten VNet und Ihrem lokalen Standort geführt.


## Vorbereitungen

- Überprüfen Sie, ob das ausgewählte VPN-Gerät die Anforderungen zum Herstellen einer standortübergreifenden Virtual Network-Verbindung erfüllt. Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten und Gateways für virtuelle Netzwerkverbindungen](http://go.microsoft.com/fwlink/p/?LinkId=615099).

- Beschaffen Sie für Ihr VPN-Gerät eine IPv4-IP mit externer Ausrichtung. Diese IP-Adresse ist für eine Site-to-Site-Konfiguration erforderlich und wird für Ihr VPN-Gerät verwendet, das nicht hinter einem NAT-Gerät angeordnet sein darf.

>[AZURE.IMPORTANT]Wenn Sie sich mit dem Konfigurieren des VPN-Geräts oder mit den IP-Adressbereichen Ihrer lokalen Netzwerkkonfiguration nicht auskennen, müssen Sie sich an eine Person wenden, die Ihnen diese Details liefern kann.

## Erstellen des virtuellen Netzwerks

1. Melden Sie sich am **Verwaltungsportal** an.

2. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

3. Geben Sie die Informationen auf den folgenden Seiten an, um Ihr VNet zu erstellen.

## Seite "Details zu Virtual Network"

Geben Sie die unten angegebenen Informationen ein.

- **Name**: Name des virtuellen Netzwerks. Beispiel: *EastUSVNet*. Sie verwenden den hier festgelegten Namen bei der Bereitstellung Ihrer VMs und PaaS-Instanzen. Deshalb sollte der Name nicht zu kompliziert sein.
- **Speicherort**: Der Speicherort steht in direkter Beziehung zu dem physischen Standort (Region), an dem sich Ihre Ressourcen (VMs) befinden soll. Wenn Sie z. B. möchten, dass sich die virtuellen Computer, die Sie für Ihr virtuelles Netzwerk bereitstellen, physisch in *East US* (USA, Osten) befinden, wählen Sie diesen Speicherort aus. Sie können die Ihrem virtuellen Netzwerk zugeordnete Region nach dem Erstellen nicht mehr ändern.

## Seite "DNS-Server und VPN-Konnektivität"
Geben Sie die folgenden Informationen ein, und klicken Sie dann unten rechts auf den Pfeil „Weiter“.

- **DNS-Server**: Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, oder wählen Sie einen zuvor registrierten DNS-Server in der Dropdownliste aus. Durch diese Einstellung wird kein DNS-Server erstellt. Sie bietet die Möglichkeit, die DNS-Server anzugeben, die Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten.
- **Standort-zu-Standort-VPNs konfigurieren:** Aktivieren Sie das Kontrollkästchen **Ein Site-to-Site-VPN konfigurieren**.
- **Lokales Netzwerk:** Ein lokales Netzwerk stellt Ihren lokalen physischen Standort dar. Sie können ein zuvor erstelltes lokales Netzwerk auswählen oder ein neues lokales Netzwerk erstellen. Wenn Sie ein zuvor erstelltes lokales Netzwerk auswählen, sollten Sie aber auf der Konfigurationsseite **Lokale Netzwerke** sicherstellen, dass die IP-Adresse des VPN-Geräts (öffentliche IPv4-Adresse), das Sie für diese Verbindung nutzen, korrekt ist.

## Seite "Site-to-Site-Konnektivität"
Wenn Sie ein neues lokales Netzwerk erstellen, wird die Seite **Site-to-Site-Konnektivität** angezeigt. Wenn Sie ein zuvor erstelltes lokales Netzwerk verwenden möchten, wird diese Seite nicht im Assistenten angezeigt, und Sie können mit dem nächsten Abschnitt fortfahren.

Geben Sie die folgenden Informationen ein, und klicken Sie dann auf den Pfeil „Weiter“.

- 	**Name:** Der Name, den Sie Ihrem lokalen Netzwerkstandort geben möchten.
- 	**IP-Adresse des VPN-Geräts:** Dies ist die öffentliche IPv4-Adresse des lokalen VPN-Geräts, mit dem Sie die Verbindung mit Azure herstellen. Das VPN-Gerät kann sich nicht hinter einer NAT befinden.
- 	**Adressraum:** Umfasst die Start-IP und CIDR (Adressenanzahl). Hier geben Sie die Adressbereiche an, die Sie über das Gateway für das virtuelle Netzwerk an Ihren lokalen Standort senden möchten. Wenn eine IP-Zieladresse in die hier angegebenen Bereiche fällt, wird sie über das Gateway für das virtuelle Netzwerk geleitet.
- 	**Adressraum hinzufügen:** Wenn Sie mehrere Adressbereiche über das Gateway für das virtuelle Netzwerk senden möchten, geben Sie hier alle zusätzlichen Adressbereiche an. Sie können die Bereiche später auf der Seite „Lokales Netzwerk“ hinzufügen oder entfernen.

## Seite "Adressräume von Virtual Network"
Geben Sie den Adressbereich an, den Sie für Ihr virtuelles Netzwerk verwenden möchten. Dies sind die dynamischen IP-Adressen (DIPS), die den virtuellen Computern und anderen Rolleninstanzen zugewiesen werden, die Sie für dieses virtuelle Netzwerk bereitstellen.

Es ist besonders wichtig, einen Bereich auszuwählen, der sich nicht mit den anderen Bereichen überschneidet, die für Ihr lokales Netzwerk verwendet werden. Sprechen Sie sich mit Ihrem Netzwerkadministrator ab. Der Netzwerkadministrator muss ggf. einen Bereich von IP-Adressen aus dem Adressraum Ihres lokalen Netzwerks reservieren, den Sie für Ihr virtuelles Netzwerk verwenden können.

Geben Sie die folgenden Informationen ein, und klicken Sie dann auf das Häkchen in der unteren rechten Ecke, um Ihr Netzwerk zu konfigurieren.

- **Adressraum:** Umfasst die Start-IP und die Anzahl von Adressen. Stellen Sie sicher, dass sich die angegebenen Adressräume und die Adressräume im lokalen Netzwerk nicht überschneiden.
- **Subnetz hinzufügen:** Umfasst die Start-IP und die Anzahl von Adressen. Zusätzliche Subnetze sind nicht erforderlich, aber Sie können eine getrenntes Subnetz für virtuelle Computer erstellen, die über statische DIPs verfügen sollen. Vielleicht möchten Sie jedoch auch Ihre virtuellen Computer in einem Subnetz zusammenfassen, das von Ihren anderen Rolleninstanzen getrennt ist.
- **Gatewaysubnetz hinzufügen:** Klicken Sie auf diese Option, um das Gatewaysubnetz hinzuzufügen. Das Gatewaysubnetz wird nur für das Gateway des virtuellen Netzwerks verwendet und ist für diese Konfiguration erforderlich.

Klicken Sie auf das Häkchen am rechten unteren Rand der Seite, damit das virtuelle Netzwerk erstellt wird. Sobald der Vorgang abgeschlossen ist, wird im Verwaltungsportal auf der Seite **Netzwerke** unter **Status** der Eintrag **Erstellt** angezeigt. Nachdem das VNet erstellt wurde, können Sie Ihr Gateway für das virtuelle Netzwerk konfigurieren.

## Konfigurieren des Gateways für das virtuelle Netzwerk

Als Nächstes konfigurieren Sie das **Gateway für das virtuelle Netzwerk**, um eine sichere Site-to-Site-Verbindung zu erstellen. Weitere Informationen finden Sie unter [Konfigurieren eines Gateways für ein virtuelles Netzwerk im Verwaltungsportal](vpn-gateway-configure-vpn-gateway-mp.md).

## Nächste Schritte

Weitere Informationen zu den standortübergreifenden Verbindungen von Virtual Network finden Sie unter [Informationen zu sicheren, standortübergreifenden virtuellen Netzwerkverbindungen](https://msdn.microsoft.com/library/azure/dn133798.aspx).

Wenn Sie eine Punkt-zu-Standort-VPN-Verbindung konfigurieren möchten, helfen Ihnen die Informationen unter [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung](vpn-gateway-point-to-site-create.md) weiter.

Sie können dem virtuellen Netzwerk virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines/virtual-machines-create-custom.md).

Informationen zum Konfigurieren einer VNet-Verbindung mit RRAS finden Sie unter [Konfigurieren eines Site-to-Site-VPN mit Windows Server 2012 RRAS (Routing- und RAS-Dienst)](https://msdn.microsoft.com/library/dn636917.aspx).
 

<!---HONumber=July15_HO2-->