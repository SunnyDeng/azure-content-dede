<properties
   pageTitle="Konfigurieren einer VNet-zu-VNet-Verbindung | Microsoft Azure"
	description="Herstellen von Verbindungen zwischen virtuellen Azure-Netzwerken im gleichen Abonnement oder in der gleichen Region oder in verschiedenen Abonnements oder Regionen"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>


# Konfigurieren einer VNet-zu-VNet-Verbindung im Azure-Portal

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

In diesem Artikel werden Sie durch den Vorgang zum Verbinden virtueller Netzwerke im klassischen Bereitstellungsmodus unter Verwendung einer Kombination aus Azure-Portal und PowerShell geleitet.


Das Verbinden eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) ähnelt dem Verbinden eines virtuellen Netzwerks mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPSec/IKE bereitzustellen. Die VNets, die Sie verbinden, können sich in verschiedenen Abonnements und Regionen befinden. Sie können sogar VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren, wie in der folgenden Abbildung dargestellt:

![VNet-zu-VNet-Konnektivitätsdiagramm](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)


>[AZURE.NOTE]Azure verfügt derzeit über zwei Bereitstellungsmodelle: das klassische Bereitstellungsmodell und das Azure-Ressourcen-Manager-Bereitstellungsmodell. Die Konfigurations-Cmdlets und die Schritte der beiden Bereitstellungsmethoden unterscheiden sich. Dieses Thema führt Sie durch die Erstellung der virtuellen Netzwerke mit dem klassischen Bereitstellungsmodus. Wenn Sie virtuelle Netzwerke, die im Azure-Ressourcen-Manager-Modus erstellt wurden, miteinander verbinden möchten, lesen Sie [Konfigurieren einer VNet-zu-VNet-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Wenn Sie ein im klassischen Bereitstellungsmodus erstelltes virtuelles Netzwerk mit einem virtuellen Netzwerk verbinden möchten, das im Azure-Ressourcen-Manager erstellt wurde, finden Sie weitere Informationen unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Gründe für Verbindungen zwischen virtuellen Netzwerken

Aus den folgenden Gründen empfiehlt sich das Herstellen von Verbindungen zwischen virtuellen Netzwerken:

- **Regionsübergreifende Georedundanz und Geopräsenz**
	- Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endpunkte mit Internetzugriff verwenden zu müssen.
	- Mit dem Azure-Load Balancer und Clustertechnologie von Microsoft oder Drittanbietern können Sie Workloads mit hoher Verfügbarkeit mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Ein wichtiges Beispiel ist die Einrichtung von SQL Always On mit Verfügbarkeitsgruppen, die sich über mehrere Azure-Regionen erstrecken.

- **Regionale Anwendungen mit mehreren Ebenen mit starker Isolierungsgrenze**
	- Innerhalb der gleichen Region können Sie Anwendungen mit mehreren Ebenen mit mehreren verbundenen virtuellen Netzwerken einrichten, die starke Isolierung und sichere Kommunikation zwischen den Ebenen aufweisen.

- **Abonnementübergreifende Kommunikation zwischen Organisationen in Azure**
	- Wenn Sie über mehrere Azure-Abonnements verfügen, können Sie Workloads aus verschiedenen Abonnements sicher zwischen virtuellen Netzwerken verbinden.
	- Für Unternehmen und Dienstanbieter können Sie die organisationsübergreifende Kommunikation mit sicherer VPN-Technologie in Azure aktivieren.

## Häufig gestellte Fragen zu Verbindungen von VNet zu VNet

- Die virtuellen Netzwerke können sich im gleichen Abonnement oder in verschiedenen Abonnements befinden.

- Die virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen (Standorte) befinden.

- Ein Clouddienst oder Endpunkt mit Lastenausgleich darf auch dann NICHT mehrere virtuelle Netzwerke umfassen, wenn diese verbunden sind.

- Für das Verbinden virtueller Netzwerke sind keine lokalen VPN-Gateways erforderlich. Diese werden nur benötigt, wenn standortübergreifende Konnektivität erforderlich ist.

- VNet-zu-VNet unterstützt das Verbinden virtueller Azure-Netzwerke. Nicht unterstützt hingegen werden Verbindungen virtueller Computer oder Clouddienste, die sich NICHT in einem virtuellen Netzwerk befinden.

- VNet-zu-VNet erfordert Azure-VPN-Gateways mit VPNs mit dynamischem Routing. Azure-VPN-Gateways mit statischem Routing werden nicht unterstützt.

- Virtuelle Netzwerkverbindungen können gleichzeitig mit VPNs mit mehreren Standorten und maximal 10 VPN-Tunneln für ein VPN-Gateway des virtuellen Netzwerks verwendet werden, das Verbindungen mit anderen virtuellen Netzwerken oder lokalen Standorten herstellt.

- Die Adressräume der virtuellen Netzwerke und der lokalen Netzwerkstandorte dürfen sich nicht überschneiden. Wenn sich Adressbereiche überschneiden, tritt beim Erstellen eines virtuellen Netzwerks oder beim Hochladen von NETCFG-Konfigurationsdateien ein Fehler auf.

- Redundante Tunnel zwischen einem Paar virtueller Netzwerke werden nicht unterstützt.

- Alle VPN-Tunnel des virtuellen Netzwerks (einschließlich P2S-VPNs) verwenden die verfügbare Bandbreite auf dem Azure-VPN-Gateway und die gleiche SLA für die Verfügbarkeit des VPN-Gateways in Azure gemeinsam.

- VNet-zu-VNet-Datenverkehr wird über den Azure-Backbone übertragen.

## Konfigurieren von VNet-zu-VNet-Verbindungen

In diesem Verfahren erstellen Sie schrittweise eine Verbindung zwischen zwei virtuellen Netzwerken: VNet1 und VNet2. Sie müssen mit Netzwerken vertraut sein, um die IP-Adressbereiche zu ersetzen, die mit Ihren Anforderungen an den Netzwerkentwurf kompatibel sind. Das Verbinden aus einem virtuellen Azure-Netzwerk mit einem anderen virtuellen Azure-Netzwerk ist mit dem Herstellen einer Verbindung mit einem lokalen Netzwerk über ein Standort-zu-Standort-VPN (Site-to-Site, S2S) identisch.

Dieses Verfahren verwendet in erster Linie das Azure-Portal. Sie müssen jedoch Microsoft Azure PowerShell-Cmdlets verwenden, um Verbindungen mit den VPN-Gateways herzustellen.

![Verbinden von VNet zu VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

Es gibt fünf Abschnitte, die geplant und konfiguriert werden müssen. Konfigurieren Sie jeden Abschnitt in der unten aufgeführten Reihenfolge:

1. [Planen der IP-Adressbereiche](#plan-your-ip-address-ranges)
2. [Erstellen der virtuellen Netzwerke](#create-your-virtual-networks)
3. [Hinzufügen lokaler Netzwerke](#add-local-networks)
4. [Erstellen der dynamischen Routinggateways für jedes VNet](#create-the-dynamic-routing-gateways-for-each-vnet)
5. [Verbinden der VPN-Gateways](#connect-the-vpn-gateways)


## Planen der IP-Adressbereiche

Eine wichtige Entscheidung betrifft die Bereiche, die Sie zum Konfigurieren Ihrer Netzwerkkonfigurationsdatei (NETCFG-Datei) verwenden. Aus der Perspektive von VNet1 ist VNet2 nur eine weitere VPN-Verbindung, die in der Azure-Plattform definiert ist. Aus der Sicht von VNet2 ist VNet1 nur eine weitere VPN-Verbindung. Beide Netzwerke identifizieren sich gegenseitig als lokaler Netzwerkstandort. Denken Sie daran, dass Sie sicherstellen müssen, dass keiner der VNet-Bereiche oder der Bereiche des lokalen Netzwerks Überschneidungen aufweist.

Tabelle 1 zeigt ein Beispiel, wie VNets definiert werden. Verwenden Sie die unten aufgeführten Bereiche nur als Richtlinie. Notieren Sie sich die Bereiche, die Sie für Ihre virtuellen Netzwerke verwenden. Sie benötigen diese Informationen in den späteren Schritten.

**Tabelle 1**

|Virtuelles Netzwerk |Definition des virtuellen Netzwerkstandorts |Definition des lokalen Netzwerkstandorts|
|:----------------|:-------------------------------|:----------------------------|
|VNet1 |VNet1 (10.1.0.0/16) |VNet2 (10.2.0.0/16) |
|VNet2 |VNet2 (10.2.0.0/16) |VNet1 (10.1.0.0/16) |

## Erstellen der virtuellen Netzwerke

Für dieses Lernprogramm erstellen Sie zwei virtuelle Netzwerke: VNet1 und VNet2. Ersetzen Sie die Beispielwerte durch Ihre eigenen Werte, wenn Sie Ihre VNets erstellen. Für die Zwecke dieses Lernprogramms werden die folgenden Werte für die VNets verwendet:

VNet1: Adressraum = 10.1.0.0/16, Region=USA West

VNet2: Adressraum = 10.2.0.0/16, Region=Japan Ost

1. Melden Sie sich beim **Verwaltungsportal** an.

2. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Custom Create**, um den Konfigurationsassistenten zu starten.

Geben Sie auf der Seite **Details zum virtuellen Netzwerk** die folgenden Informationen ein:

  ![Details zum virtuellen Netzwerk](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736055.png)

  - **Name** – Der Name des virtuellen Netzwerks. Beispiel: VNet1
  - **Standort** – Wenn Sie ein virtuelles Netzwerk erstellen, ordnen Sie dieses einem Azure-Standort (einer Region) zu. Wenn Sie z. B. wünschen, dass Ihre virtuellen Computer, die Sie für Ihr virtuelles Netzwerk bereitgestellt haben, physisch in "USA West" vorhanden sind, wählen Sie diesen Standort aus. Sie können den Ihrem virtuellen Netzwerk zugeordnete Standort nach dem Erstellen nicht mehr ändern.



Geben Sie auf der Seite **DNS Server und VPN-Konnektivität** die folgenden Informationen ein, und klicken Sie dann in der unteren rechten Ecke auf den Pfeil "Weiter".

  ![DNS-Server und VPN-Konnektivität](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736056.jpg)


- **DNS-Server** – Geben Sie den Namen und die IP-Adresse des DNS-Servers ein, oder wählen Sie einen zuvor registrierten DNS-Server in der Dropdown-Liste aus. Durch diese Einstellung wird kein DNS-Server erstellt. Sie bietet die Möglichkeit, die DNS-Server anzugeben, die Sie zur Namensauflösung für dieses virtuelle Netzwerk verwenden möchten. Wenn Sie Namensauflösung zwischen Ihren virtuellen Netzwerken wünschen, müssen Sie Ihren eigenen DNS-Server konfigurieren, anstatt die Namensauflösung zu verwenden, die von Azure bereitgestellt wird.

  - Aktivieren Sie keines der Kontrollkästchen. Klicken Sie nur unten rechts auf den Pfeil, um zum nächsten Bildschirm zu gelangen.

Geben Sie auf der Seite **Adressräume des virtuellen Netzwerks** die Adressräume an, die für das virtuelle Netzwerk verwendet werden sollen. Dies sind die dynamischen IP-Adressen (DIPS), die den virtuellen Computern und anderen Rolleninstanzen zugewiesen werden, die Sie für dieses virtuelle Netzwerk bereitstellen. Es ist besonders wichtig, einen Bereich auszuwählen, der sich nicht mit den anderen Bereichen überschneidet, die für Ihr lokales Netzwerk verwendet werden. Sie müssen sich mit Ihrem Netzwerkadministrator abstimmen, der ggf. einen Bereich von IP-Adressen aus dem Adressraum Ihres lokalen Netzwerks reservieren muss, den Sie für Ihr virtuelles Netzwerk verwenden können.


  ![Seite "Adressräume des virtuellen Netzwerks"](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736057.jpg)

  **Geben Sie die folgenden Informationen ein**, und klicken Sie dann auf das Häkchen in der unteren rechten Ecke, um Ihr Netzwerk zu konfigurieren.

  - **Adressraum** – Umfasst Start-IP und die Anzahl Adressen. Stellen Sie sicher, dass sich die angegebenen Adressräume und die Adressräume im lokalen Netzwerk nicht überschneiden. In diesem Beispiel verwenden Sie 10.1.0.0/16 für VNet1.
  - **Subnetz hinzufügen** – Umfasst Start-IP und Anzahl Adressen. Zusätzliche Subnetze sind nicht erforderlich, aber Sie können eine getrenntes Subnetz für virtuelle Computer erstellen, die über statische DIPs verfügen sollen. Vielleicht möchten Sie jedoch auch Ihre virtuellen Computer in einem Subnetz zusammenfassen, das von Ihren anderen Rolleninstanzen getrennt ist.

**Klicken Sie auf das Häkchen** rechts unten auf der Seite, damit das virtuelle Netzwerk erstellt wird. Sobald der Vorgang abgeschlossen ist, wird im Azure-Portal auf der Seite *Netzwerke* unter *Status* der Eintrag *Erstellt* angezeigt.

## Erstellen eines weiteren virtuellen Netzwerks

Wiederholen Sie anschließend die oben aufgeführten Schritte, um ein weiteres virtuelles Netzwerk zu erstellen. Im weiteren Verlauf dieser Übung stellen Sie eine Verbindung zwischen diesen beiden virtuellen Netzwerken her. Beachten Sie, dass keine doppelten oder sich überlappenden Adressräume vorhanden sein dürfen. Verwenden Sie für dieses Lernprogramm die folgenden Werte:

- **VNet2**
- **Adressraum**: 10.2.0.0/16
- **Region** = Japan Ost

## Hinzufügen lokaler Netzwerke

Wenn Sie eine VNet-zu-VNet-Konfiguration erstellen, müssen Sie jedes VNet so konfigurieren, dass die gegenseitige Identifizierung als lokaler Netzwerkstandort erfolgt. In diesem Verfahren konfigurieren Sie jedes VNet als ein lokales Netzwerk. Wenn Sie zuvor bereits VNets konfiguriert haben, fügen Sie diese auf die folgende Weise als lokale Netzwerke im Azure-Portal hinzu.

1. Klicken Sie unten links auf dem Bildschirm auf **Neu**. Klicken Sie im Navigationsbereich auf **Netzwerkdienste** und dann auf **Virtuelles Netzwerk**. Klicken Sie auf **Lokales Netzwerk hinzufügen**.

2. Geben Sie auf der Seite **Details zum lokalen Netzwerk angeben** unter **Name** den Namen eines virtuellen Netzwerks ein, das Sie in der VNet-zu-VNet-Konfiguration verwenden möchten. In diesem Beispiel verwenden Sie VNet 1, weil Sie VNet2 auf dieses virtuelle Netzwerk für diese Konfiguration verweisen lassen.

  Verwenden Sie für IP-Adresse des VPN-Geräts eine beliebige IP-Adresse. Normalerweise verwenden Sie die tatsächliche externe IP-Adresse für ein VPN-Gerät. Für VNet-zu-VNet-Konfigurationen verwenden Sie die IP-Adresse des Gateways. Da Sie das Gateway jedoch noch nicht erstellt haben, verwenden Sie die hier angegebene IP-Adresse als Platzhalter. Anschließend kehren Sie zu diesen Einstellungen zurück und konfigurieren sie dann mit den entsprechenden Gateway-IP-Adressen, nachdem Azure diese generiert hat.

3. Auf der Seite **Adresse angeben** fügen Sie den tatsächlichen IP-Adressbereich und die Adressenanzahl für VNet1 ein. Diese Angaben müssen genau dem Bereich entsprechen, den Sie in einem früheren Schritt für VNet1 angegeben haben.

4. Nachdem Sie VNet1 als lokales Netzwerk konfiguriert haben, kehren Sie zurück und konfigurieren VNet2 mithilfe der Werte, die diesem VNet entsprechen.

5. Nun verweisen Sie beide VNets als ein lokales Netzwerk aufeinander. Navigieren Sie im Verwaltungsportal zur Seite **Konfigurieren** für VNet1. Wählen Sie unter **Standort-zu-Standort-Konnektivität** die Option **Eine Verbindung mit dem lokalen Netzwerk herstellen** aus, und wählen Sie dann **VNET2** als lokales Netzwerk aus.

  ![Herstellen einer Verbindung mit dem lokalen Netzwerk](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736058.jpg)

6. Klicken Sie im Abschnitt **Virtuelle Netzwerkadressräume** auf der gleichen Seite auf **Gatewaysubnetz hinzufügen**, und klicken Sie dann unten auf der Seite auf das Symbol **Speichern**, um Ihre Konfiguration zu speichern.

7. Wiederholen Sie diesen Schritt für VNet2, um VNet1 als ein lokales Netzwerk anzugeben.

## Erstellen der dynamischen Routinggateways für jedes VNet

Nachdem Sie nun alle VNets konfiguriert haben, konfigurieren Sie die VNet-Gateways.

1. Stellen Sie auf der Seite **Netzwerke** sicher, dass in der Statusspalte für Ihr virtuelles Netzwerk **Erstellt** angezeigt wird.

2. Klicken Sie in der Spalte **Name** auf den Namen Ihres virtuellen Netzwerks.

3. Vergewissern Sie sich auf der Seite **Dashboard**, dass dieses VNet noch kein konfiguriertes Gateway besitzt. Diese Statusänderung wird angezeigt, wenn Sie die Konfigurationsschritte für Ihr Gateway absolvieren.

4. Klicken Sie unten auf der Seite auf **Gateway erstellen**.

  Sie müssen **Dynamisches Routing** auswählen. Wenn Sie aufgefordert werden zu bestätigen, dass das Gateway erstellt werden soll, klicken Sie auf "Ja".

  ![Gatewaytyp](./media/virtual-networks-configure-vnet-to-vnet-connection/IC717026.png)

5. Während der Erstellung Ihres Gateways wechselt die Gatewaygrafik auf der Seite zu Gelb und zeigt "Gateway wird erstellt" an. Die Erstellung des Gateways nimmt normalerweise etwa 15 Minuten in Anspruch.

6. Wiederholen Sie die gleichen Schritte für das andere VNet. Stellen Sie dabei sicher, dass Sie **Dynamisches Gateway** auswählen. Das erste VNet-Gateway muss nicht fertiggestellt sein, wenn Sie mit dem Erstellen des Gateways für das andere VNet beginnen.

7. Wenn sich der Gatewaystatus in "Connecting" ändert, wird die IP-Adresse für jedes Gateway im Dashboard angezeigt. Notieren Sie sich die IP-Adressen, die für jedes VNet gelten. Gehen Sie dabei sorgfältig vor, um sie nicht zu verwechseln. Dies sind die IP-Adressen, die verwendet werden, wenn Sie die IP-Platzhalteradressen für das VPN-Gerät unter **Lokale Netzwerke** bearbeiten.

## Bearbeiten des lokalen Netzwerks

1. Klicken Sie auf der Seite **Lokale Netzwerke** auf den Namen des lokalen Netzwerks, den Sie bearbeiten möchten, und klicken Sie dann unten auf der Seite auf **Bearbeiten**. Geben Sie als **IP-Adresse des VPN-Geräts** die IP-Adresse des Gateways ein, das dem VNet entspricht. Geben Sie z. B. für VNet1 die Gateway-IP-Adresse ein, die VNet1 zugewiesen ist. Klicken Sie unten auf der Seite auf den Pfeil.

2. Klicken Sie auf der Seite **Den Adressraum angeben** unten rechts auf das Häkchen, ohne Änderungen vorzunehmen.

## Verbinden der VPN-Gateways

Nachdem alle zuvor beschriebenen Schritte abgeschlossen wurden, legen Sie die vorinstallierten IPSec-/IKE-Schlüssel auf identische Werte fest. Dies kann mithilfe einer REST-API oder eines PowerShell-Cmdlets erfolgen. Wenn Sie PowerShell verwenden, vergewissern Sie sich, dass Sie die aktuellste Version der Microsoft Azure PowerShell-Cmdlets verwenden. Die Beispiele unten verwenden PowerShell-Cmdlets zum Festlegen des Schlüsselwerts auf "A1b2C3D4". Beachten Sie, dass beide Gateways den gleichen Schlüsselwert verwenden. Bearbeiten Sie die Beispiele unten entsprechend Ihren eigenen Werten.

Für VNet1

	PS C:\> Set-AzureVNetGatewayKey -VNetName VNet1 -LocalNetworkSiteName VNet2 -SharedKey A1b2C3D4

Für VNet2

	PS C:\> Set-AzureVNetGatewayKey -VNetName VNet2 -LocalNetworkSiteName VNet1 -SharedKey A1b2C3D4

Warten Sie, bis die Verbindung initialisiert wird. Nachdem das Gateway initialisiert wurde, sieht es wie in der Abbildung unten gezeigt aus, und Ihre virtuellen Netzwerke sind verbunden.

![Gatewaystatus – verbunden](./media/virtual-networks-configure-vnet-to-vnet-connection/IC736059.jpg)

## Nächste Schritte


Informationen zum Hinzufügen virtueller Computer zu Ihrem virtuellen Netzwerk finden Sie unter [Erstellen eines benutzerdefinierten virtuellen Computers](../virtual-machines/virtual-machines-create-custom.md).

Informationen zum Konfigurieren einer VNet-Verbindung mit RRAS finden Sie unter [Standort-zu-Standort-VPN in Azure Virtual Network mithilfe von Windows Server 2012 RRAS (Routing- und RAS-Dienst)](https://msdn.microsoft.com/library/dn636917.aspx).

Informationen zum Konfigurationsschema finden Sie unter [Konfigurationsschema für virtuelle Azure-Netzwerke](https://msdn.microsoft.com/library/azure/jj157100.aspx).


[1]: ../hdinsight-hbase-geo-replication-configure-vnets.md
[2]: http://channel9.msdn.com/Series/Getting-started-with-Windows-Azure-HDInsight-Service/Configure-the-VPN-connectivity-between-two-Azure-virtual-networks
 

<!---HONumber=August15_HO9-->