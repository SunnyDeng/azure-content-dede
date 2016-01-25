<properties
   pageTitle="Öffentliche und private IP-Adressen in Azure-Ressourcen-Manager | Microsoft Azure"
   description="Hier erfahren Sie mehr über die Verwendung öffentlicher und privater IP-Adressen in Azure-Ressourcen-Manager."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/12/2015"
   ms.author="telmos" />

# IP-Adressen in Azure
Sie können Azure-Ressourcen IP-Adressen zuweisen, um die Kommunikation mit anderen Azure-Ressourcen, Ihrem lokalen Netzwerk und dem Internet zu ermöglichen. In Azure können zwei Arten von IP-Adressen verwendet werden: öffentliche und private.

Öffentliche IP-Adressen werden für die Kommunikation mit dem Internet verwendet. Hierzu zählen auch öffentliche Azure-Dienste.

Private IP-Adressen werden für die Kommunikation innerhalb eines Azure Virtual Networks (VNet) und innerhalb Ihres lokalen Netzwerks verwendet, wenn Sie Ihr Netzwerk mithilfe eines VPN-Gateways oder einer ExpressRoute-Verbindung auf Azure ausdehnen.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [Klassisches Bereitstellungsmodell](virtual-network-ip-addresses-overview-classic.md).

Wenn Sie mit dem klassischen Bereitstellungsmodell vertraut sind, sehen Sie sich die [Unterschiede bei IP-Adressen zwischen dem klassischen und dem Ressourcen-Manager](virtual-network-ip-addresses-overview-classic.md#Differences-between-Resource-Manager-and-classic-deployments)-Bereitstellungsmodell an.

## Öffentliche IP-Adressen
Öffentliche IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit dem Internet und öffentlichen Azure-Diensten wie [Azure Redis Cache](https://azure.microsoft.com/services/cache), [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs), [SQL-Datenbanken](sql-database-technical-overview.md) und [Azure Storage](storage-introduction.md).

In Azure-Ressourcen-Manager ist eine [öffentliche IP-Adresse](resource-groups-networking.md#public-ip-address) eine Ressource, die über eigene Eigenschaften verfügt. Sie können eine öffentliche IP-Adressressource einer der folgenden Ressourcen zuordnen:

- VMs
- Load Balancer mit Internetzugriff
- VPN-Gateways
- Anwendungsgateways

### Zuordnungsmethode
Es gibt zwei Methoden zum Zuordnen einer IP-Adresse zu einer *öffentlichen IP-Ressource*: *dynamisch* oder *statisch*. Die Standardmethode für die Zuordnung ist *dynamisch*. Bei diesem Verfahren wird eine IP-Adresse **nicht** zum Zeitpunkt ihrer Erstellung zugewiesen. Stattdessen wird die öffentliche IP-Adresse zugeordnet, wenn Sie die zugeordnete Ressource (z. B. einen virtuellen Computer oder einen Load Balancer) starten oder erstellen. Die IP-Adresse wird freigegeben, wenn Sie die Ressource beenden oder löschen. Dies hat zur Folge, dass die IP-Adresse geändert wird, wenn Sie eine Ressource beenden und starten.

Damit die IP-Adresse für die zugeordnete Ressource unverändert bleibt, können Sie die Zuordnungsmethode explizit auf *statisch* festlegen. In diesem Fall wird sofort eine IP-Adresse zugewiesen. Sie wird nur freigegeben, wenn Sie die Ressource löschen oder deren Zuordnungsmethode in *dynamisch* ändern.

>[AZURE.NOTE]Auch wenn Sie die Zuordnungsmethode auf *statisch* festlegen, können Sie nicht die tatsächliche IP-Adresse festlegen, die der *öffentlichen IP-Ressource* zugewiesen wird. Sie wird stattdessen aus einem Pool der verfügbaren IP-Adressen an dem Azure-Standort zugeordnet, an dem die Ressource erstellt wird.

Statische öffentliche IP-Adressen werden häufig in den folgenden Szenarien verwendet:

- Endbenutzer müssen Firewallregeln für die Kommunikation mit Ihren Azure-Ressourcen aktualisieren.
- Bei der DNS-Namensauflösung erfordert eine Änderung einer IP-Adresse eine Aktualisierung von A-Datensätzen.
- Ihre Azure-Ressourcen kommunizieren mit anderen Apps oder Diensten, die ein IP-basiertes Sicherheitsmodell verwenden.
- Sie verwenden SSL-Zertifikate, die mit einer IP-Adresse verknüpft sind.

>[AZURE.NOTE]Die Liste mit den IP-Adressbereichen, aus denen den Azure-Ressourcen öffentliche IP-Adressen (dynamisch/statisch) zugewiesen werden, finden Sie unter [IP-Bereiche des Azure-Datencenters](https://www.microsoft.com/download/details.aspx?id=41653).

### DNS-Hostnamensauflösung
Sie können eine DNS-Domänennamensbezeichnung für eine öffentliche IP-Ressource angeben. Dadurch erstellen Sie für *Domänennamensbezeichnung*.*Standort*.cloudapp.azure.com eine Zuordnung zur öffentlichen IP-Adresse in den von Azure verwalteten DNS-Servern. Wenn Sie z. B. eine öffentliche IP-Ressource mit **contoso** als *Domänennamensbezeichung* am Azure-*Standort* **USA, Westen** erstellen, wird der vollqualifizierte Domänenname (FQDN) **contoso.westus.cloudapp.azure.com** in die öffentliche IP-Adresse der Ressource aufgelöst. Mit diesem FQDN können Sie einen benutzerdefinierten CNAME-Domäneneintrag mit Verweis auf die öffentliche IP-Adresse in Azure erstellen.

>[AZURE.IMPORTANT]Jede erstellte Domänennamensbezeichnung muss innerhalb des Azure-Standorts eindeutig sein.

### VMs
Sie können eine öffentliche IP-Adresse einem [virtuellen Computer](virtual-machines-about.md) (VM, Virtual Machine) zuordnen, indem Sie sie dessen **Netzwerkschnittstellenkarte** (NIC, Network Interface Card) zuordnen. Bei einem virtuellen Computer mit mehreren Netzwerkschnittstellenkarten (NICs) können Sie die IP-Adresse nur der *primären* NIC zuweisen. Sie können einem virtuellen Computer (VM) eine dynamische oder eine statische öffentliche IP-Adresse zuweisen.

### Load Balancer mit Internetzugriff
Sie können eine öffentliche IP-Adresse einem [Azure Load Balancer](load-balancer-overview.md) zuordnen, indem Sie sie der **Front-End**-Konfiguration des Load Balancers zuweisen. Diese öffentliche IP-Adresse fungiert als virtuelle IP-Adresse (VIP) mit Lastenausgleich. Sie können einem Load Balancer-Front-End eine dynamische oder eine statische öffentliche IP-Adresse zuweisen. Sie können einem Load Balancer-Front-End auch mehrere öffentliche IP-Adressen zuweisen. Dadurch werden Szenarien mit [mehreren VIPs](load-balancer-multivip.md) ermöglicht, wie sie z. B. in mehrinstanzenfähigen Umgebungen mit SSL-basierten Websites benötigt werden.

### VPN-Gateways
Über [Azure VPN Gateway](vpn-gateway-about-vpngateways.md) wird eine Verbindung zwischen einem Azure Virtual Network (VNet) und anderen Azure-VNets oder lokalen Netzwerken hergestellt. Sie müssen der **IP-Konfiguration** dieses Gateways eine öffentliche IP-Adresse zuweisen, um eine Kommunikation mit dem Remotenetzwerk zu ermöglichen. Derzeit können Sie einem VPN-Gateway nur eine dynamische öffentliche IP-Adresse zuweisen.

### Anwendungsgateways
Sie können eine öffentliche IP-Adresse einem Azure [Application Gateway](application-gateway-introduction.md) zuordnen, indem Sie sie der **Front-End**-Konfiguration des Gateways zuweisen. Diese öffentliche IP-Adresse fungiert als VIP (virtuelle IP-Adresse) mit Lastenausgleich. Derzeit können Sie der Front-End-Konfiguration eines Anwendungsgateways nur eine *dynamische* öffentliche IP-Adresse zuweisen. Für Szenarien mit mehreren VIPs können Sie auch mehrere öffentliche IP-Adressen zuweisen.

### Auf einen Blick
Die folgende Tabelle gibt Aufschluss über die einzelnen Ressourcentypen, die möglichen Zuordnungsmethoden (dynamisch/statisch) sowie über die Möglichkeit, mehrere öffentliche IP-Adressen zuzuweisen.

|Ressource|Dynamisch|Statisch|Mehrere IP-Adressen|
|---|---|---|---|
|Netzwerkschnittstellenkarte (NIC) (einer VM)|Ja|Ja|Nein|
|Load Balancer-Front-End|Ja|Ja|Ja|
|VPN Gateway|Ja|Nein|Nein|
|Application Gateway-Front-End|Ja|Nein|Nein|

## Private IP-Adressen
Private IP-Adressen ermöglichen Azure-Ressourcen die Kommunikation mit anderen Ressourcen in einem [Azure Virtual Network](virtual-networks-overview.md) (VNet) oder in einem lokalen Netzwerk über ein VPN-Gateway oder eine ExpressRoute-Verbindung, ohne dass dabei eine über das Internet erreichbare IP-Adresse verwendet wird.

Im Azure-Ressourcen-Manager-Bereitstellungsmodell wird verschiedenen Azure-Ressourcen eine private IP-Adresse zugeordnet.

- VMs
- Interne·Load Balancer (ILBs)
- Anwendungsgateways

### Zuordnungsmethode
Eine private IP-Adresse wird aus dem Adressbereich des Subnetzes zugeordnet, dem die Ressource hinzugefügt wurde. Der Adressbereich des Subnetzes ist ein Teil des VNet-Adressbereichs.

Es gibt zwei Methoden zum Zuordnen von privaten IP-Adressen: *dynamisch* oder *statisch*. Die Standardmethode ist die *dynamische* Zuordnung. Dabei wird die IP-Adresse aus dem Subnetz der Ressource per DHCP automatisch zugewiesen. Wenn Sie die Ressource beenden und wieder starten, kann sich diese IP-Adresse ändern.

Sie können die Zuordnungsmethode auf *statisch* festlegen, damit die IP-Adresse unverändert bleibt. In diesem Fall müssen Sie eine gültige IP-Adresse angeben, die zum Subnetz der Ressource gehört.

Statische private IP-Adressen werden häufig für Folgendes verwendet:

- VMs, die als Domänencontroller oder DNS-Server fungieren
- Ressourcen, die Firewallregeln mit IP-Adressen erfordern
- Ressourcen, auf die von anderen Apps oder Ressourcen über eine IP-Adresse zugegriffen wird.

### VMs
Eine private IP-Adresse wird der **Netzwerkschnittstellenkarte** (NIC) eines [virtuellen Computers](virtual-machines-about.md) (VM, Virtual Machine) zugewiesen. Bei einem virtuellen Computer (VM) mit mehreren Netzwerkschnittstellenkarten (NICs) wird jeder NIC eine private IP-Adresse zugewiesen. Für eine NIC können Sie als Zuordnungsmethode entweder „dynamisch“ oder „statisch“ angeben.

#### Interne DNS-Hostnamensauflösung (für VMs)
Alle Azure-VMs werden standardmäßig mit [von Azure verwalteten DNS-Servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) konfiguriert, sofern nicht explizit benutzerdefinierte DNS-Server konfiguriert werden. Diese DNS-Server stellen die interne Namensauflösung für VMs im gleichen VNet bereit.

Beim Erstellen einer VM wird den von Azure verwalteten DNS-Servern eine Zuordnung des Hostnamens zur zugehörigen privaten IP-Adresse hinzugefügt. Bei einer VM mit mehreren Netzwerkschnittstellenkarten (NICs) wird der Hostname der privaten IP-Adresse der primären NIC zugeordnet.

VMs, die mit von Azure verwalteten DNS-Servern konfiguriert wurden, können die Hostnamen aller VMs innerhalb ihres VNets in deren private IP-Adressen auflösen.

### Interne Load Balancer (ILB) und Anwendungsgateways
Sie können der **Front-End**-Konfiguration eines [Azure-ILBs](load-balancer-internal-overview.md) oder eines [Azure Application Gateways](application-gateway-introduction.md) eine private IP-Adresse zuweisen. Diese private IP-Adresse fungiert als interner Endpunkt und steht nur den Ressourcen innerhalb des entsprechenden virtuellen Netzwerks (VNet) und der damit verbundenen Remotenetzwerke zur Verfügung. Der Front-End-Konfiguration kann eine dynamische oder eine statische private IP-Adresse zugewiesen werden. Für Szenarien mit mehreren virtuellen IP-Adressen (VIPs) können Sie auch mehrere private IP-Adressen zuweisen.

### Auf einen Blick
Die folgende Tabelle gibt Aufschluss über die einzelnen Ressourcentypen, die möglichen Zuordnungsmethoden (dynamisch/statisch) sowie über die Möglichkeit, mehrere private IP-Adressen zuzuweisen:

|Ressource|Statisch|Dynamisch|Mehrere IP-Adressen|
|---|---|---|---|
|Virtual Machine (VM)/Netzwerkschnittstellenkarte (NIC)|Ja|Ja|Ja|
|Front-End für interne Load Balancer|Ja|Ja|Ja|
|Application Gateway-Front-End|Ja|Ja|Ja|

## Grenzen

Folgende Tabelle zeigt die Einschränkungen für die IP-Adressierung in Azure pro Region pro Abonnement. Sie können sich [an den Support wenden](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um die Standardlimits Ihren Unternehmensanforderungen entsprechend auf die maximalen Grenzwerte zu erhöhen.

||Standardlimit|Maximaler Grenzwert| |---|---|---| |Öffentliche IP-Adressen (dynamisch)|60|Wenden Sie sich an den Support.| |Öffentliche IP-Adressen (statisch)|20|Wenden Sie sich an den Support.| |Öffentliche Front-End-IP pro Lastenausgleich|5|Wenden Sie sich an den Support.| |Private Front-End-IP pro Lastenausgleich|1|Wenden Sie sich an den Support.|

Lesen Sie unbedingt die vollständigen Informationen zu [Netzwerkeinschränkungen](azure-subscription-service-limits.md#networking-limits) in Azure.

## Preise

In den meisten Fällen sind öffentliche IP-Adressen kostenlos. Es wird eine Schutzgebühr für die Verwendung zusätzlicher und/oder statischer öffentlicher IP-Adressen erhoben. Informieren Sie sich unbedingt über die [Preisstruktur für öffentliche IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses/).

Zusammenfassend gilt für öffentliche IP-Ressourcen die folgenden Preisstruktur:

- VPN-Gateways und Anwendungsgateways verwenden nur eine dynamische öffentliche IP-Adresse, die kostenlos ist.
- Virtuelle Computer verwenden nur eine öffentliche IP-Adresse, die kostenlos ist, solange es sich um eine dynamische IP-Adresse handelt. Wenn ein virtueller Computer eine statische öffentliche IP-Adresse verwendet, fällt dies unter „Nutzung statischer (reservierter) öffentlicher IP-Adressen“.
- Jeder Lastenausgleich kann mehrere öffentliche IP-Adressen verwenden. Die erste öffentliche IP-Adresse ist kostenlos. Zusätzliche dynamische IP-Adressen werden mit €0,0034 pro Stunde berechnet. Statische öffentliche IP-Adressen fallen unter „Nutzung statischer (reservierter) öffentlicher IP-Adressen“.
- Nutzung statischer (reservierter) öffentlicher IP-Adressen: 
	- Die ersten fünf (in Gebrauch) sind kostenlos. Zusätzliche statische öffentliche IP-Adressen werden mit €0,0034 pro Stunde berechnet. 
	- Statische öffentliche IP-Adressen, die keiner Ressource zugewiesen sind, werden mit €0,0034 pro Stunde in Rechnung gestellt.
	- Die Nutzung wird basierend auf der Gesamtanzahl von statischen öffentlichen IP-Adressen im Abonnement berechnet.

## Nächste Schritte
- [Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse](virtual-network-deploy-static-pip-arm-portal.md) mithilfe des Azure-Portals
- Erfahren Sie, wie Sie [eine VM mit einer statischen öffentlichen IP-Adresse mithilfe einer Vorlage bereitstellen](virtual-network-deploy-static-pip-arm-template.md).
- [Bereitstellen einer VM mit einer statischen privaten IP-Adresse](virtual-networks-static-private-ip-arm-pportal.md) mithilfe des Azure-Portals

<!---HONumber=AcomDC_0114_2016-->