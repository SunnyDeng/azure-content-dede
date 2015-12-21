<properties 
   pageTitle="Öffentliche und private IP-Adressen im Azure-Netzwerkressourcenanbieter | Microsoft Azure"
   description="Informieren Sie sich über öffentliche und private IP-Adressen für Netzwerkressourcenanbieter im Azure-Ressourcen-Manager."
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# IP-Adressen in Azure Virtual Network
In diesem Artikel geht es um die IP-Adressierung für Virtual Machines, Load Balancer, VPN Gateways und APP-Gateways.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)].

## Öffentliche IP-Adressen
Mit öffentlichen IP-Adressen können Azure-Ressourcen mit dem Internet und anderen öffentlichen Azure-Diensten wie [Azure Redis Cache](https://azure.microsoft.com/services/cache) und [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs) kommunizieren. Eine öffentliche IP-Adresse ist eine unabhängige Ressource und kann unterschiedlichen Arten von Azure-Ressourcen zugeordnet werden, z. B. [Virtual Machines](virtual-machines-about.md) (VM) und [Load Balancers](load-balancer-overview.md) (LB).

### Verfahren zum Zuweisen öffentlicher IP-Adressen
Öffentliche IP-Adressen werden aus einem Pool mit IP-Adressen zugewiesen, die an einem bestimmten Standort verfügbar sind. Eine vollständige Liste mit den IP-Adressbereichen, die von Microsoft Azure-Rechenzentren verwendet werden, finden Sie unter [IP-Bereiche des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653).

Es gibt zwei Verfahren zum Zuweisen einer IP-Adresse zu einer öffentlichen IP-Ressource: *dynamisch* oder *statisch*. Das Standardverfahren ist die *dynamische* Zuweisung. Beim *dynamischen* Verfahren wird eine IP-Adresse bei der Erstellung **nicht** der *öffentlichen IP-Adressressource* zugewiesen. Wenn eine Ressource (z. B. eine VM oder ein Load Balancer), die der *öffentlichen IP-Adressressource* zugeordnet ist, erstellt oder gestartet wird, wird aus dem Pool mit IP-Adressen eine IP-Adresse zugewiesen. Die Zuweisung dieser IP-Adresse wird aufgehoben, und die IP-Adresse wird für den verfügbaren Pool freigegeben, wenn die zugeordnete Ressource gelöscht oder beendet wird.

Wenn Sie das *statische* Zuweisungsverfahren verwenden, wird der *öffentlichen IP-Adressressource* bei der Erstellung eine IP-Adresse zugewiesen. In diesem Fall bleibt die IP-Adresse unabhängig vom Zustand der zugeordneten Ressource zugewiesen. Sie wird für den verfügbaren Pool nur freigegeben, wenn die *öffentliche IP-Adressressource* gelöscht wird oder das Zuweisungsverfahren in *dynamisch* geändert wird.

### DNS-Auflösung
Eine Bezeichnung für einen DNS-Domänennamen kann für eine öffentliche IP-Ressource angegeben werden. Hierbei wird ein entsprechender DNS-Eintrag auf den Azure-DNS-Servern erstellt. Der entsprechende FQDN „*domainnamelabel*.*location*.cloudapp.azure.com“ kann global in die IP-Adresse aufgelöst werden, die der öffentlichen IP-Ressource zugewiesen ist.

Unten gehen wir die unterschiedlichen Ressourcentypen durch, die einer öffentlichen IP-Adresse zugeordnet werden können.

### Virtual Machine
Eine öffentliche IP-Adresse ist einer [Virtual Machine](virtual-machines-about.md) (VM) über eine Netzwerkschnittstellenkarte (NIC) zugeordnet. Jede VM kann nur über eine öffentliche IP-Adresse verfügen. Dies gilt unabhängig davon, wie viele Netzwerkschnittstellenkarten der VM zugeordnet sind. Nur eine *dynamisch* zugewiesene *öffentliche IP-Adressressource* kann einer VM-Netzwerkschnittstellenkarte zugeordnet werden. Bei einer VM mit mehreren Netzwerkschnittstellenkarten kann die *öffentliche IP-Adressressource* nur der primären Netzwerkschnittstellenkarte zugeordnet werden.

### Azure-Lastenausgleich
Eine öffentliche IP-Adresse kann der Front-End-Konfiguration eines [Azure Load Balancers](load-balancer-overview.md) (LB) zugeordnet werden, die als virtuelle IP-Adresse (VIP) mit Lastenausgleich und Erreichbarkeit über das Internet dient. Einem LB können sowohl *dynamische* als auch *statische* öffentliche IP-Ressourcen zugeordnet werden. Es ist möglich, mehrere *öffentliche IP-Adressressourcen* einer LB-Front-End-Konfiguration zuzuordnen, um Szenarien wie mehrinstanzenfähige Umgebungen mit mehreren SSL-basierten Websites zu realisieren.

### Application Gateway
Eine öffentliche IP-Adresse kann der Front-End-Konfiguration eines [Azure Application Gateway](application-gateway-introduction.md) zugeordnet werden, um dafür die Konfiguration mit einer per Internet zugänglichen virtuellen IP (VIP) mit Lastenausgleich durchzuführen. Derzeit kann einem Application Gateway nur eine *dynamisch* zugewiesene öffentliche IP-Ressource zugeordnet werden. Es ist aber möglich, mehrere öffentliche IP-Adressen zuzuordnen.

### VPN Gateway
Eine öffentliche IP-Adresse muss der IP-Konfiguration eines [Azure VPN Gateway](vpn-gateway-about-vpngateways.md) zugeordnet werden, und zwar bei der Erstellung einer VPN-Verbindung zwischen einem Azure Virtual Network und einem lokalen Netzwerk oder einem anderen Azure Virtual Network. Derzeit kann einem VPN Gateway nur eine *dynamisch* zugewiesene öffentliche IP-Ressource zugeordnet werden.

### Auf einen Blick

|Ressource|Statische Zuweisung|Dynamische Zuweisung|Mehrere IP-Adressen|
|---|---|---|---|
|Virtual Machine (VM)/Netzwerkschnittstellenkarte (NIC)|Ja|Nein|Nein|
|Load Balancer-Front-End|Ja|Ja|Ja|
|Application Gateway-Front-End|Ja|Nein|Ja|
|VPN Gateway|Ja|Nein|Nein|

## Private IP-Adressen
Private IP-Adressen ermöglichen die Kommunikation zwischen Ressourcen in einem virtuellen Netzwerk ohne Verwendung von per Internet zugänglichen IP-Adressen. Diese IP-Adresse wird über den Adressbereich des Subnetzes im virtuellen Netzwerk zugewiesen.

### Verfahren zum Zuweisen privater IP-Adressen
Es gibt zwei Verfahren zum Zuweisen von IP-Adressen: *dynamisch* oder *statisch*. Das Standardverfahren ist die *dynamische* Zuweisung, bei der die IP-Adresse per DHCP zugewiesen wird. Alternativ dazu können Sie auch explizit das *statische* Zuweisungsverfahren festlegen und eine IP-Adresse angeben. In diesem Fall wird die Ressource der angegebenen IP-Adresse zugewiesen, solange sie sich im Adressbereich des Subnetzes befindet und frei ist (also keiner anderen Ressource zugewiesen ist).

Es gibt unterschiedliche Ressourcentypen, denen eine private IP-Adresse zugewiesen werden kann. Beachten Sie, dass beide Zuweisungsverfahren (*statisch* und *dynamisch*) für alle diese Ressourcentypen funktionieren.

### Virtual Machine
Eine private IP-Adresse wird einer Netzwerkschnittstellenkarte (NIC) einer [virtuellen Maschine](virtual-machines-about.md) (VM) zugewiesen. Die maximale Anzahl an privaten IP-Adressen richtet sich für jede VM nach der Anzahl der zugeordneten Netzwerkschnittstellenkarten (eine pro NIC).

#### Interne DNS-Hostnamenauflösung
Alle Azure-VMs sind mit [von Azure verwalteten DNS-Servern](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) konfiguriert, es sei denn, bei der Erstellung wird explizit etwas anderes angegeben. Bei Verwendung von unter Azure verwalteten DNS-Servern wird automatisch ein DNS-Datensatz erstellt, über den der Hostname der VM in die private IP-Adresse der VM aufgelöst wird. Bei einer VM mit mehreren Netzwerkschnittstellenkarten wird der Hostname in die private IP-Adresse der primären Netzwerkschnittstellenkarte aufgelöst.

### Interner Load Balancer
Eine private IP-Adresse kann dem Front-End eines [internen Azure Load Balancers](load-balancer-internal-overview.md) (ILB) zugewiesen werden, das als virtuelle IP-Adresse (VIP) für die Ressourcen im virtuellen Netzwerk mit Lastenausgleich dient. So wird der Lastenausgleich ermöglicht, ohne dass die IP-Adresse im Internet verfügbar gemacht wird.

### App-Gateway
Eine private IP-Adresse kann dem Front-End eines [Azure Application Gateway](application-gateway-introduction.md) zugewiesen werden, um es mit einem internen Endpunkt zu konfigurieren, der nicht im Internet verfügbar gemacht wird. Dies wird auch als Endpunkt mit internem Load Balancer (ILB) bezeichnet. Das Verhalten und die Zuweisungsverfahren ähneln der ILB-Beschreibung weiter oben.

### Auf einen Blick
|Ressource|Statische Zuweisung|Dynamische Zuweisung|Mehrere IP-Adressen|
|---|---|---|---|
|Virtual Machine (VM)/Netzwerkschnittstellenkarte (NIC)|Ja|Ja|Ja|
|Load Balancer-Front-End|Ja|Ja|Ja|
|Application Gateway-Front-End|Ja|Ja|Ja|

## Nächste Schritte


[Azure PowerShell-Verweis für Netzwerke](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure Resource Manager-Vorlagensprache](../resource-group-authoring-templates.md)

[Azure-Netzwerk – Häufig verwendete Vorlagen](https://github.com/Azure/azure-quickstart-templates)

[Anbieter von Computerressourcen](../virtual-machines-azurerm-versus-azuresm)

[Azure Resource Manager-Übersicht](../resource-group-overview)

[Rollenbasierte Zugriffssteuerung im Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Verwenden von Tags im Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Bereitstellungen von Vorlagen](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=AcomDC_1210_2015-->