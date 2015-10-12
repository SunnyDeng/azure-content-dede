<properties 
   pageTitle="Was ist eine Netzwerksicherheitsgruppe (NSG)?"
   description="Informationen zu Netzwerksicherheitsgruppen (NSGs)"
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
   ms.date="09/22/2015"
   ms.author="telmos" />

# Was ist eine Netzwerksicherheitsgruppe (NSG)?

Mit einer NSG können Sie eingehenden Datenverkehr für Instanzen virtueller Computer in Ihrem virtuellen Netzwerk steuern. Eine NSG enthält Regeln zur Zugriffssteuerung, die den Datenverkehr auf Grundlage der Richtung des Datenverkehrs, des Protokolls, der Quelladresse und des Quellports, und der Zieladresse und des Zielports zulässt oder verweigert. Die Regeln für eine NSG können jederzeit geändert werden, und die Änderungen werden auf alle zugeordneten Instanzen angewendet.

>[AZURE.WARNING]NSGs können nur in regionalen VNets verwendet werden. Wenn Sie versuchen, Endpunkte in einer Bereitstellung zu schützen, die kein VNet hat oder ein VNet verwendet, das einer Affinitätsgruppe zugeordnet ist, lesen Sie [Was ist eine Endpunkt-Zugriffssteuerungsliste (Access Control List, ACL)?](./virtual-networks-acl.md). Sie können auch [Ihr VNet in ein regionales VNet migrieren](./virtual-networks-migrate-to-regional-vnet.md).

![NSGs](./media/virtual-network-nsg-overview/figure1.png)

Diese Abbildung zeigt ein virtuelles Netzwerk mit zwei Subnetzen, wobei jedem Subnetz eine NSG zugeordnet ist, um den Datenverkehr zu steuern.

>[AZURE.NOTE]Endpunktbasierte ACLs und Netzwerksicherheitsgruppen können nicht für die gleiche VM-Instanz verwendet werden. Wenn Sie eine NSG verwenden möchten und bereits eine Endpunkt-ACL eingerichtet ist, entfernen Sie zuerst die Endpunkt-ACL. Informationen zur Vorgehensweise finden Sie unter [Verwalten von Zugriffssteuerungslisten (ACLs) für Endpunkte mithilfe von PowerShell](virtual-networks-acl-powershell.md).

## Funktionsweise einer Netzwerksicherheitsgruppe (NSG)

Netzwerksicherheitsgruppen unterscheiden sich von Endpunkt-basierten ACLs. Endpunkt-ACLs betreffen nur den öffentlichen Port, der über den Eingabeendpunkt verfügbar gemacht wird. Eine NSG kann für mehrere VM-Instanzen gelten und steuert den gesamten ein- und ausgehenden Datenverkehr des virtuellen Computers.

Eine Netzwerksicherheitsgruppe besitzt einen Namen, ist einer Region zugeordnet und verfügt über eine aussagekräftige Beschriftung. Sie enthält zwei Arten von Regeln: **eingehende Regeln** und **ausgehende Regeln**. Eingehende Regeln werden auf die eingehenden Pakete eines virtuellen Computers angewendet, ausgehende Regeln gelten für ausgehende Pakete. Die Regeln werden auf dem Host angewendet, auf dem sich der virtuelle Computer befindet. Eingehende und ausgehende Pakete müssen einer Regel vom Typ **Zulassen** entsprechen. Andernfalls wird das Paket verworfen.

Regeln werden gemäß ihrer Priorität verarbeitet. So wird beispielsweise eine Regel mit einer niedrigeren Prioritätsnummer (z. B. 100) vor einer Regel mit höherer Prioritätsnummer (z. B. 200) verarbeitet. Sobald eine Übereinstimmung gefunden wurde, werden keine weiteren Regeln mehr verarbeitet.

Eine Regel umfasst Folgendes:

- **Name**: Ein eindeutiger Bezeichner für die Regel.

- **Typ**: eingehend/ausgehend

- **Priorität**: <You can specify an integer between 100 and 4096>

- **Quell-IP-Adresse**: CIDR des Quell-IP-Adressbereichs

- **Quellportbereich**: <integer or range between 0 and 65536>

- **Ziel-IP-Adressbereich**: CIDR des Ziel-IP-Adressbereichs

- **Zielportbereich**: <integer or range between 0 and 65536>

- **Protokoll**: <zulässig: TCP, UDP oder „*“>

- **Zugriff**: zulassen/verweigern

### Standardregeln

Eine NSG enthält Standardregeln. Die Standardregeln können zwar nicht gelöscht werden, haben aber niedrigste Priorität und können somit durch selbst erstellte Regeln außer Kraft gesetzt werden. Die Standardregeln beschreiben die empfohlenen Standardeinstellungen der Plattform. Wie in den folgenden Standardregeln zu sehen, wird Datenverkehr aus einem bzw. an ein VNet in ein- und ausgehender Richtung zugelassen.

In ausgehender Richtung wird die Verbindung mit dem Internet zugelassen, in eingehender Richtung wird sie dagegen standardmäßig blockiert. Eine der Standardregeln ermöglicht dem Load Balancer von Azure die Überprüfung der Integrität des virtuellen Computers. Sie können diese Regel außer Kraft setzen, wenn der virtuelle Computer oder die Gruppe von virtuellen Computern, für den bzw. für die die NSG gilt, nicht am Lastenausgleich beteiligt ist.

Standardregeln:

**Eingehend**

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|-----------------------------------|----------|--------------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET INBOUND | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | ZULASSEN |
| ALLOW AZURE LOAD BALANCER INBOUND | 65001 | AZURE\_LOADBALANCER | * | * | * | * | ZULASSEN |
| DENY ALL INBOUND | 65500 | * | * | * | * | * | VERWEIGERN |

**Ausgehend**

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|-------------------------|----------|-----------------|-------------|-----------------|------------------|----------|--------|
| ALLOW VNET OUTBOUND | 65000 | VIRTUAL\_NETWORK | * | VIRTUAL\_NETWORK | * | * | ZULASSEN |
| ALLOW INTERNET OUTBOUND | 65001 | * | * | INTERNET | * | * | ZULASSEN |
| DENY ALL OUTBOUND | 65500 | * | * | * | * | * | VERWEIGERN |

### Standardtags

Standardtags sind vom System bereitgestellte Bezeichner für eine Kategorie von IP-Adressen. Sie können in benutzerdefinierten Regeln angegeben werden. Folgende Standardtags stehen zur Verfügung:

- **VIRTUAL\_NETWORK**: Standardtag für Ihren gesamten Netzwerkadressraum. Dieser umfasst den Adressraum des virtuellen Netzwerks (IP-CIDR in Azure) sowie den gesamten verbundenen lokalen Adressraum (lokale Netzwerke). Dazu gehören auch VNet-zu-VNet-Adressräume.

- **AZURE\_LOADBALANCER**: Standardtag für den Infrastruktur-Load Balancer von Azure. Wird in eine Azure-Datencenter-IP umgewandelt, die als Ausgangspunkt für die Integritätstests von Azure fungiert. Nur erforderlich, wenn der virtuelle Computer oder die Gruppe virtueller Computer, der bzw. die der NSG zugeordnet ist, Teil eines Satzes mit Lastenausgleich ist.

- **INTERNET**: Standardtag für den IP-Adressraum, der außerhalb des virtuellen Netzwerks liegt und über das öffentliche Internet erreichbar ist. Dieser Bereich schließt auch den Azure-eigenen öffentlichen IP-Adressraum mit ein.

### ICMP-Datenverkehr

In NSG-Regeln kann derzeit als Protokoll nur *TCP* oder *UDP* angegeben werden. Ein spezielles Tag für *ICMP* ist nicht verfügbar. ICMP-Datenverkehr ist in einem virtuellen Netzwerk jedoch standardmäßig über die eingehenden VNet-Regeln zulässig, die ein-/ausgehenden Datenverkehr für sämtliche Ports und Protokolle innerhalb des VNets zulassen.

## Zuordnen von NSGs

Sie können eine NSG virtuellen Computern, Netzwerkkarten und Subnetzen zuordnen.

- **Zuordnen einer NSG zu einem virtuellen Computer.** Wenn Sie eine NSG einem virtuellen Computer zuordnen, werden die Netzwerkzugriffsregeln in der NSG auf jeglichen Datenverkehr angewendet, der für den virtuellen Computer bestimmt ist oder diesen verlässt. 

- **Zuordnen einer NSG zu einer Netzwerkkarte.** Wenn Sie eine NSG zu einer Netzwerkkarte zuordnen, werden die Netzwerkzugriffsregeln in der NSG nur auf diese Netzwerkkarte angewendet. Für einen virtuellen Computer mit mehreren Netzwerkkarten bedeutet dies, dass sich eine NSG, die auf eine einzelne Netzwerkkarte angewendet wird, nicht auf die anderen Netzwerkkarten auswirkt.

- **Zuordnen einer NSG zu einem Subnetz** Wenn Sie eine NSG einem Subnetz zuordnen, werden die Netzwerkzugriffsregeln in der NSG auf alle virtuellen Computer im Subnetz angewendet.

Sie können einem virtuellen Computer, einer Netzwerkkarte, die von einem virtuellen Computer verwendet wird, und dem Subnetz, an das die Netzwerkkarte gebunden ist, verschiedene NSGs zuordnen. In diesem Fall werden alle Netzwerkzugriffsregeln in dieser Reihenfolge auf den Datenverkehr angewendet:

- **Eingehender Datenverkehr**
	1. NSG für Subnetz
	2. NSG für Netzwerkkarte
	3. NSG für virtuellen Computer
- **Ausgehender Datenverkehr**
	1. NSG für virtuellen Computer
	2. NSG für Netzwerkkarte
	3. NSG für Subnetz

![NSG-ACLs](./media/virtual-network-nsg-overview/figure2.png)

>[AZURE.NOTE]Sie können einem Subnetz, einem virtuellen Computer oder einer Netzwerkkarte zwar nur eine einzelne NSG zuordnen, Sie können eine solche NSG aber beliebig vielen Ressourcen zuordnen.

## Überlegungen zum Entwurf

Sie müssen wissen, wie virtuelle Computer mit Infrastrukturdiensten und PaaS-Diensten, die in Azure gehostet werden, kommunizieren, wenn Sie Ihre NSGs entwerfen. Auf die meisten Azure-PaaS-Dienste, wie z. B. SQL-Datenbanken und Speicher, kann nur über eine öffentliche Internetadresse zugegriffen werden. Dies gilt auch für Lastenausgleichstests.

Ein häufiges Szenario in Azure ist die Abtrennung von virtuellen Computern und PaaS-Rollen in Subnetze abhängig davon, ob für diese Objekte ein Internetzugriff erforderlich ist oder nicht. In solchen Szenarios kann ein Subnetz mit virtuellen Computern oder Rolleninstanzen vorhanden sein, die Zugriff auf Azure-PaaS-Dienste wie SQL-Datenbanken und Speicher benötigen, für die jedoch keine eingehende oder ausgehende Kommunikation mit dem öffentlichen Internet erforderlich ist.

Betrachten Sie z. B. die folgende NSG-Regel für ein solches Szenario:

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|KEIN INTERNET|100| VIRTUAL\_NETWORK|&#42;|INTERNET|&#42;|TCP|VERWEIGERN| 

Da die Regel den gesamten Zugriff aus dem virtuellen Netzwerk auf das Internet verweigert, können virtuelle Computer nicht auf Azure-PaaS-Dienste zugreifen, für die ein öffentlicher Internet-Endpunkt erforderlich ist, z. B. SQL-Datenbanken.

Erwägen Sie, anstatt einer Verweigerungsregel eine Regel zu verwenden, mit der der Zugriff aus dem virtuellen Netzwerk auf das Internet zugelassen, der Zugriff aus dem Internet auf das virtuelle Netzwerk jedoch verweigert wird, wie unten dargestellt:

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
|INS INTERNET|100| VIRTUAL\_NETWORK|&#42;|INTERNET|&#42;|TCP|ZULASSEN|
|AUS DEM INTERNET|110| INTERNET|&#42;|VIRTUAL\_NETWORK|&#42;|TCP|VERWEIGERN| 

>[AZURE.WARNING]Azure verwendet ein spezielles Subnetz, das als **Gatewaysubnetz** bezeichnet wird, um VPN-Gateways mit anderen VNets und lokalen Netzwerken zu verwalten. Das Zuordnen einer NSG zu diesem Subnetz bewirkt, dass das VPN-Gateway nicht mehr wie erwartet funktioniert. Ordnen Sie NSGs NICHT Gatewaysubnetzen zu!

Außerdem müssen Sie die unten aufgeführten besonderen Regeln berücksichtigen. Vergewissern Sie sich, dass Sie keinen Datenverkehr blockieren, der durch diese Regeln zugelassen wird. Andernfalls hat Ihre Infrastruktur keine Möglichkeit, mit wesentlichen Azure-Diensten zu kommunizieren.

- **Virtuelle IP des Hostknotens**: Grundlegende Infrastrukturdienste wie DHCP, DNS und die Systemüberwachung werden über die virtualisierte Host-IP-Adresse 168.63.129.16 bereitgestellt. Diese öffentliche IP-Adresse gehört Microsoft und ist die einzige virtuelle IP-Adresse, die in allen Regionen zu diesem Zweck verwendet wird. Die IP-Adresse wird der physischen IP-Adresse des Servercomputers (Hostknoten) zugeordnet, der den virtuellen Computer hostet. Der Hostknoten fungiert als DHCP-Relay, als rekursiver DNS-Resolver und als Integritätstestquelle für den Load Balancer und den Computer. Eingehende Kommunikation für diese IP-Adresse darf nicht als Angriff betrachtet werden.

- **Lizenzierung (Schlüsselverwaltungsdienst)**: Die auf den virtuellen Computern verwendeten Windows-Images müssen lizenziert werden. Zu diesem Zweck wird eine Lizenzierungsanforderung an die Hostserver des Schlüsselverwaltungsdiensts gesendet, die solche Abfragen verarbeiten. Dafür wird immer der ausgehende Port 1688 verwendet.

## Grenzen

Sie müssen die folgenden Begrenzungen berücksichtigen, wenn Sie Ihre NSGs entwerfen.

|**Beschreibung**|**Begrenzung**|
|---|---|
|Anzahl von NSGs, die Sie einem Subnetz, einem virtuellen Computer oder einer Netzwerkkarte zuordnen können|1|
|NSGs pro Region und Abonnement|100|
|NSG-Regeln pro NSG|200|

Sehen Sie sich alle [Einschränkungen an, die es hinsichtlich der Netzwerkdienste in Azure gibt](../azure-subscription-service-limits/#networking-limits), bevor Sie Ihre Lösung entwerfen.

## Nächste Schritte

- [Bereitstellen von NSGs im klassischen Bereitstellungsmodell](virtual-networks-create-nsg-classic-ps.md)
- [Bereitstellen von NSGs im Ressourcen-Manager](virtual-networks-create-nsg-arm-pportal.md)

<!---HONumber=Oct15_HO1-->