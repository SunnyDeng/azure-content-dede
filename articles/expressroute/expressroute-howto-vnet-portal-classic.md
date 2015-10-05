<properties
   pageTitle="Konfigurieren eines virtuellen Netzwerks und Gateways für ExpressRoute | Microsoft Azure"
   description="Dieser Artikel beschreibt die Einrichtung eines virtuellen Netzwerks (VNet) für ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/22/2015"
   ms.author="cherylmc"/>

# Konfigurieren eines virtuellen Netzwerks für ExpressRoute

Diese Schritte führen Sie durch die Konfiguration eines virtuellen Netzwerks und eines Gateways zur Verwendung mit ExpressRoute. Dazu wird das klassische Bereitstellungsmodell verwendet. Diese Konfiguration nicht derzeit nicht für virtuelle Netzwerke und für Gateways unterstützt, die mit dem Ressourcen-Manager-Modell erstellt wurden. Sobald sie verfügbar ist, werden wir auf dieser Seite einen Link zu der Dokumentation bereitstellen.
 
>[AZURE.IMPORTANT]Sie sollten wissen, dass Azure derzeit mit zwei Bereitstellungsmodellen arbeitet: der Bereitstellung mit dem Ressourcen-Manager und der klassischen Bereitstellung. Bevor Sie Ihre Konfiguration beginnen, sollten Sie sicherstellen, dass Sie die Bereitstellungsmodelle und -tools verstehen. Informationen zu den Bereitstellungsmodellen finden Sie unter [Azure-Bereitstellungsmodelle](../azure-classic-rm.md).

1. Melden Sie sich beim **Azure-Portal** an.

2. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

3. Geben Sie auf der Seite **Details zum virtuellen Netzwerk** die folgenden Informationen ein.

	- **Name** – Der Name Ihres virtuellen Netzwerks. Sie verwenden den hier festgelegten Namen bei der Bereitstellung Ihrer VMs und PaaS-Instanzen, deshalb sollte der Name nicht zu kompliziert sein.
	- **Speicherort** – Der Speicherort steht in direkter Beziehung zu dem physischen Standort (Region), an dem sich Ihre Ressourcen (VMs) befinden sollen. Wenn Sie z. B. möchten, dass sich die virtuellen Computer, die Sie für Ihr virtuelles Netzwerk bereitstellen, physisch in "USA West" befinden, wählen Sie diesen Speicherort aus. Sie können die Ihrem virtuellen Netzwerk zugeordnete Region nach dem Erstellen nicht mehr ändern.

4. Geben Sie auf der Seite **DNS Server und VPN-Konnektivität** die folgenden Informationen ein, und klicken Sie dann in der unteren rechten Ecke auf den Weiter-Pfeil.

	- **DNS-Server** – Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, oder wählen Sie einen zuvor registrierten DNS-Server in der Dropdown-Liste aus. Durch diese Einstellung wird kein DNS-Server erstellt. Sie bietet die Möglichkeit, die DNS-Server anzugeben, die Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten.
	- **Standort-zu-Standort-VPNs konfigurieren** – Aktivieren Sie das Kontrollkästchen **Ein Site-to-Site-VPN konfigurieren**.
	- **ExpressRoute auswählen** – Aktivieren Sie das Kontrollkästchen **ExpressRoute verwenden**. Diese Option wird nur angezeigt, wenn Sie im vorherigen Schritt ***Ein Site-to-Site-VPN konfigurieren*** ausgewählt haben.
	- **Lokales Netzwerk** – Ein lokales Netzwerk stellt Ihren lokalen physischen Standort dar. Sie können ein zuvor erstelltes lokales Netzwerk auswählen oder ein neues lokales Netzwerk erstellen.

	Wenn Sie ein vorhandenes lokales Netzwerk auswählen, fahren Sie mit Schritt 5 fort.

5. Wenn Sie ein neues lokales Netzwerk erstellen, wird die Seite **Site-to-Site-Konnektivität** angezeigt. Wenn Sie ein zuvor erstelltes lokales Netzwerk ausgewählt haben, wird diese Seite nicht im Assistenten angezeigt und Sie können mit dem nächsten Abschnitt fortfahren. Geben Sie zur Konfiguration des lokalen Netzwerks die folgenden Informationen ein, und klicken Sie dann auf den Pfeil "Weiter".

	- **Name** – Der Name, den Sie Ihrem lokalen Netzwerkstandort geben möchten.
	- **Adressraum** – Umfasst Start-IP und CIDR (Anzahl der Adressen). Sie können einen beliebigen Adressbereich angeben, solange er sich nicht mit dem Adressbereich Ihres virtuellen Netzwerks überschneidet.
	- **Adressraum hinzufügen** – Diese Einstellung ist für ExpressRoute nicht relevant. **Hinweis:** Sie müssen für ExpressRoute eine lokale Netzwerk-Website erstellen. Die Adresspräfixe, die für die lokale Netzwerk-Website angegeben sind, werden ignoriert. Adresspräfixe, die Microsoft über die ExpressRoute-Verbindung angekündigt werden, werden zu Routingzwecken verwendet.

6. Geben Sie auf der Seite **Virtual Network Address Spaces** die folgenden Informationen ein, und klicken Sie dann in der unteren rechten Ecke auf das Häkchen, um das Netzwerk zu konfigurieren.

	- **Adressraum** – Umfasst Start-IP und die Anzahl der Adressen. Stellen Sie sicher, dass sich die angegebenen Adressräume und die Adressräume im lokalen Netzwerk nicht überschneiden.
	- **Subnetz hinzufügen** – Umfasst Start-IP und Anzahl Adressen. Zusätzliche Subnetze sind nicht erforderlich, aber Sie können ein getrenntes Subnetz für virtuelle Computer erstellen, die über dynamische IP-Adressen (DIPs) verfügen sollen. Vielleicht möchten Sie jedoch auch Ihre virtuellen Computer in einem Subnetz zusammenfassen, das von Ihren PaaS-Instanzen getrennt ist.
	- **Gatewaysubnetz hinzufügen** – Klicken Sie auf diese Option, um das Gatewaysubnetz hinzuzufügen. Das Gatewaysubnetz wird nur für das Gateway des virtuellen Netzwerks verwendet und ist für diese Konfiguration erforderlich. ***Wichtig:*** Das Gatewaysubnetzpräfix für ExpressRoute muss gleich /28 oder kleiner (/27, /26 usw.) sein.

7. Klicken Sie auf das Häkchen am rechten unteren Rand der Seite, damit das virtuelle Netzwerk erstellt wird. Sobald der Vorgang abgeschlossen ist, wird im Verwaltungsportal auf der Seite **Netzwerke** unter **Status** der Eintrag **Erstellt** angezeigt.

8. Klicken Sie auf der Seite **Netzwerke** auf das eben erstellte virtuelle Netzwerk, und klicken Sie dann auf **Dashboard**.
9. Klicken Sie unten auf der Seite "Dashboard" auf **GATEWAY ERSTELLEN**, und klicken Sie dann auf **Ja**.

10. Bei Beginn der Gatewayerstellung erhalten Sie eine entsprechende Nachricht. Es kann bis zu 15 Minuten dauern, bis das Gateway erstellt ist.

11. Verknüpfen Sie Ihr Netzwerk mit einer Verbindung. Gehen Sie gemäß den Anweisungen im Artikel [Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen](expressroute-howto-linkvnets-classic.md) vor.

## Nächste Schritte

- Informationen zum Hinzufügen virtueller Computer zu Ihrem virtuellen Netzwerk finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines-create-custom.md).
- Weitere Informationen zu ExpressRoute finden Sie unter [ExpressRoute – Technische Übersicht](expressroute-introduction.md).


 

<!---HONumber=Sept15_HO4-->