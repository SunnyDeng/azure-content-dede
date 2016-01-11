<properties
   pageTitle="Öffentliche und private IP-Adressen (klassisch) in Azure | Microsoft Azure"
   description="Hier erfahren Sie mehr über öffentliche und private IP-Adressen in Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="telmos" />

# IP-Adressen (klassisch) in Azure
Sie können Azure-Ressourcen IP-Adressen zuweisen, um die Kommunikation mit anderen Azure-Ressourcen, Ihrem lokalen Netzwerk und dem Internet zu ermöglichen. In Azure können zwei Arten von IP-Adressen verwendet werden: öffentliche und private.

Öffentliche IP-Adressen werden für die Kommunikation mit dem Internet verwendet. Hierzu zählen auch öffentliche Azure-Dienste.

Private IP-Adressen werden für die Kommunikation innerhalb eines virtuellen Azure-Netzwerks (VNet), Clouddiensts und Ihres lokalen Netzwerks verwendet, wenn Sie Ihr Netzwerk mithilfe eines VPN-Gateways oder einer ExpressRoute-Verbindung auf Azure ausdehnen.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [resource manager deployment model](virtual-network-ip-addresses-overview-arm.md).

## Öffentliche IP-Adressen
Öffentliche IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit dem Internet und öffentlichen Azure-Diensten wie [Azure Redis Cache](https://azure.microsoft.com/services/cache), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs), [SQL-Datenbanken](sql-database-technical-overview.md) und [Azure Storage](storage-introduction.md).

Eine öffentliche IP-Adresse wird folgenden Ressourcentypen zugeordnet:

- Clouddienste
- Virtuelle IaaS-Maschinen (IaaS-VMs)
- PaaS-Rolleninstanzen
- VPN-Gateways
- Anwendungsgateways

### Zuordnungsmethode
Die öffentliche IP-Adresse einer Azure-Ressource wird *dynamisch* aus einem Pool mit verfügbaren öffentlichen IP-Adressen des Ressourcenerstellungsorts zugewiesen. Bei Beendigung der Ressource wird die IP-Adresse wieder freigegeben. Bei einem Clouddienst tritt dieser Fall ein, wenn alle Rolleninstanzen beendet werden. Dies lässt sich durch Verwendung einer *statischen* (reservierten) IP-Adresse verhindern. (Weitere Informationen finden Sie weiter unten unter „Clouddienste“.)

>[AZURE.NOTE]Die Liste mit den IP-Adressbereichen, aus denen Azure-Ressourcen öffentliche IP-Adressen zugewiesen werden, finden Sie unter [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653).

### DNS-Hostnamenauflösung
Beim Erstellen eines Clouddiensts oder einer IaaS-VM müssen Sie einen für alle Ressourcen in Azure eindeutigen DNS-Clouddienstnamen angeben. Dadurch wird „*DNS-Name*.cloudapp.net“ in den von Azure verwalteten DNS-Servern der öffentlichen IP-Adresse der Ressource zugeordnet. Wenn Sie also beispielsweise einen Clouddienst mit dem DNS-Clouddienstnamen **contoso** erstellen, wird der vollqualifizierte Domänenname (Fully-Qualified Domain Name, FQDN) **contoso.cloudapp.net** zu einer öffentlichen IP-Adresse (VIP) des Clouddiensts aufgelöst. Mit diesem FQDN können Sie einen benutzerdefinierten CNAME-Domäneneintrag mit Verweis auf die öffentliche IP-Adresse in Azure erstellen.

### Clouddienste
Ein Clouddienst besitzt immer eine öffentliche IP-Adresse. Diese wird als virtuelle IP-Adresse (VIP) bezeichnet. In einem Clouddienst können Sie Endpunkte erstellen, um verschiedene VIP-Ports den internen Ports von VMs und Rolleninstanzen innerhalb des Clouddiensts zuzuordnen.

Sie können [einem Clouddienst mehrere VIPs zuweisen](load-balancer-multivip.md), um entsprechende Szenarien (etwa eine mehrinstanzenfähige Umgebung mit SSL-basierten Websites) zu realisieren.

Durch Verwendung einer *statischen* öffentlichen IP-Adresse (einer so genannten [reservierten IP-Adresse](virtual-networks-reserved-public-ip.md)) können Sie sicherstellen, dass die öffentliche IP-Adresse eines Clouddiensts auch bei Beendigung aller Rolleninstanzen unverändert bleibt. Eine statische (reservierte) IP-Ressource kann an einem bestimmten Ort erstellt und einem beliebigen Clouddienst an diesem Ort zugewiesen werden. Die reservierte IP-Adresse stammt aus einem Pool verfügbarer IP-Adressen für den Erstellungsort und kann nicht direkt angegeben werden. Diese IP-Adresse wird erst freigegeben, wenn Sie sie explizit löschen.

Statische (reservierte) öffentliche IP-Adressen werden häufig verwendet, wenn für den Clouddienst Folgendes gilt:

- Der Clouddienst erfordert die Einrichtung von Firewallregeln durch Endbenutzer.
- Der Clouddienst ist abhängig von einer externen DNS-Namensauflösung, und eine dynamische IP-Adresse würde eine Aktualisierung der A-Einträge erfordern.
- Der Clouddienst nutzt externe Webdienste mit einem IP-basierten Sicherheitsmodell.
- Der Clouddienst verwendet mit einer IP-Adresse verknüpfte SSL-Zertifikate.

### IaaS-VMs und PaaS-Rolleninstanzen
Sie können einer IaaS-[VM](virtual-machines-about.md) oder einer PaaS-Rolleninstanz innerhalb eines Clouddiensts eine öffentliche IP-Adresse zuweisen. Diese wird als öffentliche IP auf Instanzebene (Instance-Level Public IP, [ILPIP](virtual-networks-instance-level-public-ip.md)) bezeichnet. Diese öffentliche IP-Adresse kann nur dynamisch sein.

### VPN-Gateways
Mit einem [VPN-Gateway](vpn-gateway-about-vpngateways.md) lässt sich eine Verbindung zwischen einem Azure-VNet und anderen Azure-VNets oder lokalen Netzwerken herstellen. Einem VPN-Gateway wird *dynamisch* eine öffentliche IP-Adresse zugewiesen, was die Kommunikation mit dem Remotenetzwerk ermöglicht.

### Anwendungsgateways
Ein Azure [Anwendungsgateway](application-gateway-introduction.md) kann im Rahmen eines Layer7-Lastenausgleichs zur Weiterleitung von HTTP-basiertem Netzwerkdatenverkehr verwendet werden. Dem Anwendungsgateway wird *dynamisch* eine öffentliche IP-Adresse zugewiesen, die dann als Lastenausgleich-VIP fungiert.

### Auf einen Blick
Die folgende Tabelle gibt Aufschluss über die einzelnen Ressourcentypen, die möglichen Zuordnungsmethoden (dynamisch/statisch) sowie über die Möglichkeit, mehrere öffentliche IP-Adressen zuzuweisen:

|Ressource|Dynamisch|Statisch|Mehrere IP-Adressen|
|---|---|---|---|
|Cloud-Dienst|Ja|Ja|Ja|
|IaaS-VM oder PaaS-Rolleninstanz|Ja|Nein|Nein|
|VPN Gateway|Ja|Nein|Nein|
|Anwendungsgateway|Ja|Nein|Nein|

## Private IP-Adressen
Private IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit anderen Ressourcen in einem Clouddienst oder [virtuellen Netzwerk](virtual-networks-overview.md) (VNet) oder mit einem lokalen Netzwerk (über ein VPN-Gateway oder eine ExpressRoute-Verbindung) ohne Verwendung einer über das Internet erreichbaren IP-Adresse.

Im klassischen Bereitstellungsmodell von Azure wird eine private IP-Adresse verschiedenen Azure-Ressourcen zugewiesen.

- IaaS-VMs und PaaS-Rolleninstanzen
- Interner Lastenausgleich
- Anwendungsgateway

### IaaS-VMs und PaaS-Rolleninstanzen
Virtuelle Maschinen (VMs), die mit dem klassischen Bereitstellungsmodell erstellt werden, werden (ähnlich wie PaaS-Rolleninstanzen) immer in einem Clouddienst platziert. Das Verhalten privater IP-Adressen ist bei diesen Ressourcen daher ähnlich.

Ein Clouddienst kann auf zwei Arten bereitgestellt werden:

- Als *eigenständiger* Clouddienst (also nicht innerhalb eines virtuellen Netzwerks).
- Als Teil eines virtuellen Netzwerks.

#### Zuordnungsmethode
Bei einem *eigenständigen* Clouddienst wird den Ressourcen *dynamisch* eine private IP-Adresse aus dem Adressbereich privater IP-Adressen des Azure-Datencenters zugewiesen. Die IP-Adresse kann nur für die Kommunikation mit anderen VMs verwendet werden, die sich innerhalb des gleichen Clouddiensts befinden. Wenn die Ressource beendet und wieder gestartet wird, kann sich diese IP-Adresse ändern.

Bei Bereitstellung eines Clouddiensts innerhalb eines virtuellen Netzwerks erhalten die Ressourcen private IP-Adressen aus dem Adressbereich der zugeordneten Subnetze (gemäß Angabe in der Netzwerkkonfiguration). Diese privaten IP-Adressen können für die Kommunikation zwischen allen VMs des VNets verwendet werden.

Darüber hinaus wird bei Clouddiensten in einem VNet standardmäßig eine private IP-Adresse *dynamisch* (per DHCP) zugewiesen. Diese kann sich ändern, wenn die Ressource beendet und wieder gestartet wird. Wenn die IP-Adresse unverändert bleiben soll, müssen Sie die *statische* Zuordnungsmethode verwenden und eine gültige IP-Adresse innerhalb des entsprechenden Adressbereichs angeben.

 Statische private IP-Adressen werden häufig für Folgendes verwendet:

 - VMs, die als Domänencontroller oder DNS-Server fungieren.
 - VMs, die Firewallregeln mit IP-Adressen erfordern.
 - VMs, die Dienste ausführen, auf die von anderen Apps über eine IP-Adresse zugegriffen wird.

#### Interne DNS-Hostnamenauflösung
Alle Azure-VMs und PaaS-Rolleninstanzen werden standardmäßig mit [von Azure verwalteten DNS-Servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) konfiguriert, sofern nicht explizit benutzerdefinierte DNS-Server konfiguriert werden. Diese DNS-Server stellen die interne Namensauflösung für VMs und Rolleninstanzen im gleichen VNet oder Clouddienst bereit.

Beim Erstellen einer VM wird den von Azure verwalteten DNS-Servern eine Hostnamenzuordnung für die private IP-Adresse hinzugefügt. Bei einer VM mit mehreren NICs wird der Hostname der privaten IP-Adresse der primären NIC zugeordnet. Diese Zuordnungsinformationen sind allerdings auf Ressourcen innerhalb des gleichen Clouddiensts oder VNets beschränkt.

Bei einem *eigenständigen* Clouddienst können nur die Hostnamen der VMs/Rolleninstanzen innerhalb des gleichen Clouddiensts aufgelöst werden. Bei einem Clouddienst innerhalb eines VNets können die Hostnamen aller VMs/Rolleninstanzen innerhalb des VNets aufgelöst werden.

### Interner Lastenausgleich (Internal Load Balancer, ILB) und Anwendungsgateways
Sie können der **Front-End**-Konfiguration eines [Azure-ILBs](load-balancer-internal-overview.md) oder [Azure-Anwendungsgateways](application-gateway-introduction.md) eine private IP-Adresse zuweisen. Diese private IP-Adresse fungiert als interner Endpunkt und steht nur den Ressourcen innerhalb des entsprechenden virtuellen Netzwerks (VNet) und der damit verbundenen Remotenetzwerke zur Verfügung. Der Front-End-Konfiguration kann eine dynamische oder eine statische private IP-Adresse zugewiesen werden. Für Multi-VIP-Szenarien können auch mehrere private IP-Adressen zugewiesen werden.

### Auf einen Blick
Die folgende Tabelle gibt Aufschluss über die einzelnen Ressourcentypen, die möglichen Zuordnungsmethoden (dynamisch/statisch) sowie über die Möglichkeit, mehrere private IP-Adressen zuzuweisen:

|Ressource|Dynamisch|Statisch|Mehrere IP-Adressen|
|---|---|---|---|
|VMs (in einem *eigenständigen* Clouddienst)|Ja|Ja|Ja|
|PaaS-Rolleninstanz (in einem *eigenständigen* Clouddienst)|Ja|Nein|Ja|
|VM oder PaaS-Rolleninstanz (in einem VNet)|Ja|Ja|Ja|
|Front-End für internen Lastenausgleich|Ja|Ja|Ja|
|Application Gateway-Front-End|Ja|Ja|Ja|

## Nächste Schritte
- [Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse](virtual-network-deploy-static-pip-classic-ps.md)
- [Bereitstellen einer VM mit einer statischen privaten IP-Adresse](virtual-networks-static-private-ip-classic-pportal.md)
- [Erstellen eines Lastenausgleichs mit PowerShell](load-balancer-get-started-internet-classic-cli.md)
- [Erstellen eines internen Lastenausgleichs mit PowerShell](load-balancer-get-started-ilb-classic-ps.md)
- [Erstellen eines Anwendungsgateways mit PowerShell](application-gateway-create-gateway.md)
- [Erstellen eines internen Anwendungsgateways mit PowerShell](application-gateway-ilb.md)

<!---HONumber=AcomDC_1223_2015-->