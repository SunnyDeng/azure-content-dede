<properties 
   pageTitle="Konfigurieren eines VPN-Gateways im Verwaltungsportal | Microsoft Azure"
   description="In diesem Artikel erfahren Sie, wie Sie das VPN-Gateway Ihres virtuellen Netzwerks konfigurieren und zwischen dem statischen und dynamischen Routingtyp wechseln."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/12/2015"
   ms.author="cherylmc" />

# Konfigurieren eines VPN-Gateways im Verwaltungsportal

Wenn Sie eine sichere standortübergreifende Verbindung zwischen Azure und Ihrem lokalen Standort erstellen möchten, benötigen Sie ein konfiguriertes VPN-Gateway. Es gibt verschiedene Arten von Gateways, und die Art des zu erstellenden Gateways hängt sowohl von Ihrem Netzwerkentwurf als auch vom gewünschten lokalen VPN-Gerät ab. Einige Konnektivitätsoptionen \(wie etwa Punkt-zu-Standort-Verbindungen\) setzen ein Gateway mit dynamischem Routing voraus. Wenn Sie Ihr Gateway so konfigurieren möchten, dass es gleichzeitig Punkt-zu-Standort-Verbindungen \(P2S\) und eine Standort-zu-Standort-Verbindung \(S2S\) unterstützt, müssen Sie ein Gateway mit dynamischem Routing konfigurieren, auch wenn für die Standort-zu-Standort-Verbindung eigentlich ein beliebiger Gateway-Routingtyp konfiguriert werden kann. Darüber hinaus müssen Sie sicherstellen, dass das Gerät, das Sie für Ihre Standort-zu-Standort-Verbindung verwenden möchten, den gewünschten Gatewaytyp unterstützt. Siehe [Zu VPN-Gateways](vpn-gateway-about-vpngateways.md).

## Konfigurationsübersicht

Vor dem Konfigurieren Ihres Gateways müssen Sie zuerst Ihr virtuelles Netzwerk erstellen. Die Schritte zum Erstellen eines virtuellen Netzwerks für standortübergreifende Verbindungen finden Sie unter [Konfigurieren eines virtuellen Netzwerks mit Standort-zu-Standort-VPN](vpn-gateway-site-to-site-create.md) bzw. unter [Konfigurieren eines virtuellen Netzwerks mit Punkt-zu-Standort-VPN](vpn-gateway-point-to-site-create.md). Führen Sie anschließend die folgenden Schritte durch, um das VPN-Gateway zu konfigurieren und die Informationen zu sammeln, die Sie zum Konfigurieren des VPN-Geräts benötigen.

Wenn Sie bereits über ein VPN-Gateway verfügen und den Routingtyp ändern möchten, lesen Sie unter [Ändern des VPN-Gatewaytyps](#how-to-change-your-vpn-gateway-type) weiter.

1. [Erstellen eines VPN-Gateways](#create-a-vpn-gateway)

1. [Sammeln von Konfigurationsinformationen für Ihr VPN-Gerät](#gather-information-for-your-vpn-device-configuration)

1. [Konfigurieren des VPN-Geräts](#configure-your-vpn-device)

1. [Überprüfen der lokalen Netzwerkbereiche und der IP-Adresse des VPN-Gateways](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

## Erstellen eines VPN-Gateways

1. Stellen Sie auf der Seite **Netzwerke** sicher, dass in der Statusspalte für Ihr virtuelles Netzwerk **Erstellt** angezeigt wird.

1. Klicken Sie in der Spalte **Name** auf den Namen Ihres virtuellen Netzwerks.

1. Beachten Sie auf der Seite **Dashboard**, dass für dieses VNet noch kein Gateway konfiguriert wurde. Dieser Status wird angezeigt, wenn Sie die Konfigurationsschritte für Ihr Gateway durchführen.

![Gateway nicht erstellt](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


Klicken Sie anschließend am unteren Seitenrand auf **Gateway erstellen**. Sie haben die Wahl zwischen *Statisches Routing* und *Dynamisches Routing*. Bei der Wahl des Routingtyps müssen einige Faktoren berücksichtigt werden. Dazu zählt beispielsweise die Frage, was das VPN-Gerät unterstützt und ob Punkt-zu-Standort-Verbindungen unterstützt werden müssen. Informieren Sie sich unter [Informationen zu VPN-Geräten und Gateways für virtuelle Netzwerkverbindungen](http://go.microsoft.com/fwlink/p/?LinkId=615934) darüber, welchen Routingtyp Sie benötigen. Wenn Sie den Gatewaytyp nach der Erstellung ändern möchten, müssen Sie das Gateway löschen und neu erstellen. Wenn Sie zum Bestätigen der Gatewayerstellung aufgefordert werden, klicken Sie auf **Ja**.

![Gatewaytyp](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

Während der Gatewayerstellung wechselt die Gatewaygrafik auf der Seite zu Gelb und zeigt *Gateway wird erstellt* an. Die Gatewayerstellung kann bis zu 25 Minuten dauern. Warten Sie, bis die Gatewayerstellung abgeschlossen ist, bevor Sie mit anderen Konfigurationseinstellungen fortfahren.

![Gatewayerstellung](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

Wenn der Gatewaystatus zu *Verbindung wird hergestellt* wechselt, können Sie die für das VPN-Gerät benötigten Informationen sammeln.

![Gatewayverbindungsherstellung](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## Sammeln von Konfigurationsinformationen für Ihr VPN-Gerät

Sammeln Sie nach der Gatewayerstellung die Konfigurationsinformationen für Ihr VPN-Gerät. Diese Informationen finden Sie auf der Dashboardseite für Ihr virtuelles Netzwerk:

1. **Gateway-IP-Adresse**: Die IP-Adresse befindet sich auf der Dashboardseite. Sie wird erst nach Abschluss der Gatewayerstellung angezeigt.

1. **Gemeinsam verwendeter Schlüssel**: Klicken Sie am unteren Bildschirmrand auf **Schlüssel verwalten**. Klicken Sie auf das Symbol neben dem Schlüssel, um ihn in die Zwischenablage zu kopieren. Fügen Sie den Schlüssel anschließend ein, und speichern Sie ihn. Beachten Sie, dass diese Schaltfläche nur funktioniert, wenn ein einzelner S2S-VPN-Tunnel vorhanden ist. Sind mehrere S2S-VPN-Tunnel vorhanden, muss die API oder das PowerShell-Cmdlet „Get Virtual Network Gateway Shared Key“ verwendet werden.

![Schlüssel verwalten](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## Konfigurieren des VPN-Geräts

Nach Abschluss der vorherigen Schritte müssen Sie oder Ihr Netzwerkadministrator das VPN-Gerät konfigurieren, um die Verbindung zu erstellen. Weitere Informationen zu VPN-Geräten finden Sie unter [Informationen zu VPN-Geräten und Gateways für virtuelle Netzwerkverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Wenn das VPN-Gerät konfiguriert wurde, können Sie Ihre aktualisierten Verbindungsinformationen auf der Dashboardseite für Ihr VNet anzeigen.

Mithilfe eines der folgenden Befehle können Sie Ihre Verbindung testen:

| | Cisco ASA | Cisco ISR/ASR | Juniper SSG/ISG | Juniper SRX/J |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **SAs im Hauptmodus prüfen** | show crypto isakmp sa | show crypto isakmp sa | get ike cookie | show security ike security-association |
| **SAs im schnellen Modus prüfen** | show crypto ipsec sa | show crypto ipsec sa | get sa | show security ipsec security-association |


## Überprüfen der lokalen Netzwerkbereiche und der IP-Adresse des VPN-Gateways

### Überprüfen der IP-Adresse Ihres VPN-Gateways

Damit ein Gateway ordnungsgemäß eine Verbindung herstellen kann, muss die IP-Adresse für Ihr VPN-Gerät korrekt für das lokale Netzwerk konfiguriert sein, das Sie für Ihre standortübergreifende Konfiguration angegeben haben. In der Regel wird dies beim Festlegen der Standort-zu-Standort-Konfiguration erledigt. Wenn Sie dieses lokale Netzwerk allerdings bereits mit einem anderen Gerät verwendet haben oder sich die IP-Adresse für dieses lokale Netzwerk geändert hat, empfiehlt es sich, die Einstellungen mit der korrekten Gateway-IP-Adresse zu aktualisieren.

1. Klicken Sie zum Überprüfen der Gateway-IP-Adresse im linken Bereich des Portals auf **Netzwerke**, und wählen Sie dann am oberen Seitenrand die Option **Lokale Netzwerke** aus. Die VPN-Gateway-Adressen für die einzelnen lokalen Netzwerke, die Sie erstellt haben, werden angezeigt. Wählen Sie zum Bearbeiten der IP-Adresse das entsprechende VNet aus, und klicken Sie am unteren Seitenrand auf **Bearbeiten**.

1. Bearbeiten Sie auf der Seite **Details zum lokalen Netzwerk angeben** die IP-Adresse, und klicken Sie anschließend am unteren Seitenrand auf den Weiter-Pfeil.

1. Klicken Sie rechts unten auf der Seite **Den Adressraum angeben** auf das Häkchen, um die Einstellungen zu speichern.

### Überprüfen der Adressbereiche für Ihre lokalen Netzwerke

Damit der richtige Datenverkehr über das Gateway an Ihren lokalen Standort gelangt, müssen Sie sicherstellen, dass Sie jeden IP-Adressbereich aufgeführt haben, der in der Konfiguration des lokalen Netzwerks enthalten sein soll. Abhängig von der Netzwerkkonfiguration Ihres lokalen Standorts kann dies eine recht umfangreiche Angelegenheit sein, da jeder Bereich in der Azure-Konfiguration **Lokale Netzwerke** aufgeführt sein muss. Datenverkehr für eine IP-Adresse, die in den aufgeführten Bereichen enthalten ist, wird über das VPN-Gateway des virtuellen Netzwerks gesendet. Bei den aufgeführten IP-Adressbereichen muss es sich nicht um private Bereiche handeln. Es empfiehlt sich jedoch zu überprüfen, ob die lokale Konfiguration den eingehenden Datenverkehr empfangen kann.

Gehen Sie zum Hinzufügen oder Bearbeiten der Bereiche für ein lokales Netzwerk wie folgt vor:

1. Klicken Sie zum Bearbeiten der IP-Bereiche für ein lokales Netzwerk im linken Bereich des Portals auf **Netzwerke**, und wählen Sie dann am oberen Seitenrand die Option **Lokale Netzwerke** aus. Im Portal lassen sich die aufgeführten Bereiche am einfachsten auf der Seite **Bearbeiten** anzeigen. Wählen Sie zum Anzeigen der Bereiche das entsprechende VNet aus, und klicken Sie am unteren Seitenrand auf **Bearbeiten**.

1. Nehmen Sie auf der Seite **Details zum lokalen Netzwerk angeben** keine Änderungen vor. Klicken Sie am unteren Seitenrand auf den Weiter-Pfeil.

1. Nehmen Sie auf der Seite **Den Adressraum angeben** die gewünschten Änderungen vor. Klicken Sie anschließend auf das Häkchen, um die Konfiguration zu speichern.

## Anzeigen des Gatewaydatenverkehrs

Das Gateway und den Gatewaydatenverkehr können Sie auf der Dashboardseite von Virtual Network anzeigen.

Auf der Dashboardseite wird Folgendes angezeigt:

- Die ein- und ausgehende Datenmenge, die über das Gateway abgewickelt wird.

- Die Namen der DNS-Server, die für Ihr virtuelles Netzwerk angegeben sind.

- Die Verbindung zwischen Gateway und VPN-Gerät.

- Der gemeinsam verwendete Schlüssel, mit dem die Gatewayverbindung mit Ihrem VPN-Gerät konfiguriert wurde.


## Ändern des VPN-Gatewaytyps

Da einige Konnektivitätskonfigurationen nur für bestimmte Gatewaytypen verfügbar sind, kann es vorkommen, dass Sie den Gatewaytyp eines vorhandenes VPN-Gateways ändern müssen. Ein Beispiel: Sie möchten eine bereits vorhandene Standort-zu-Standort-Verbindung mit statischem Gateway um Punkt-zu-Standort-Verbindungen erweitern. Für Punkt-zu-Standort-Verbindungen wird jedoch ein dynamisches Gateway benötigt. Daher müssen Sie den Gatewaytyp von „statisch“ in „dynamisch“ ändern, um die Konfiguration zu ermöglichen.

Zum Ändern des Routingtyps eines VPN-Gateways müssen Sie das vorhandene Gateway löschen und es anschließend mit dem neuen Routingtyp erneut erstellen. Das virtuelle Netzwerk muss für diese Änderung nicht gelöscht werden.

Vergewissern Sie sich vor dem Ändern des Gatewaytyps, dass Ihr VPN-Gerät den gewünschten Gatewaytyp unterstützt. Unter [Informationen zu VPN-Geräten und Gateways für virtuelle Netzwerkverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=615934) können Sie neue Routingkonfigurationsbeispiele herunterladen und sich über die Anforderungen von VPN-Geräten informieren.

>[AZURE.IMPORTANT]Wenn Sie ein VPN-Gateway für das virtuelle Netzwerk löschen, wird die dem Gateway zugewiesene VIP freigegeben. Wenn Sie das Gateway neu erstellen, wird ihm eine neue VIP zugewiesen.

1. **Löschen Sie das vorhandene VPN-Gateway.**

	Klicken Sie am unteren Rand der Dashboardseite für Ihr virtuelles Netzwerk auf **Gateway löschen**. Warten Sie, bis eine Benachrichtigung mit dem Hinweis erscheint, dass das Gateway gelöscht wurde. Anschließend können Sie ein neues Gateway erstellen.

1. **Erstellen Sie ein neues VPN-Gateway.**

	Führen Sie das unter [Erstellen eines VPN-Gateways](#create-a-vpn-gateway) beschriebene Verfahren aus.


## Nächste Schritte

Weitere Informationen zu den standortübergreifenden Verbindungen von Virtual Network finden Sie unter [Informationen zu sicheren, standortübergreifenden virtuellen Netzwerkverbindungen](http://go.microsoft.com/fwlink/p/?LinkID=532884).

Sie können dem virtuellen Netzwerk virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines/virtual-machines-create-custom.md).

Wenn Sie eine Punkt-zu-Standort-VPN-Verbindung konfigurieren möchten, helfen Ihnen die Informationen unter [Konfigurieren einer Punkt-zu-Standort-VPN-Verbindung](vpn-gateway-point-to-site-create.md) weiter.

 

<!---HONumber=July15_HO5-->