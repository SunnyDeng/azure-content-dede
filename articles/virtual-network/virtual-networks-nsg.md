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
   ms.date="08/13/2015"
   ms.author="telmos" />

# Was ist eine Netzwerksicherheitsgruppe (NSG)?

Mit einer NSG können Sie eingehenden Datenverkehr für Instanzen virtueller Computer in Ihrem virtuellen Netzwerk steuern. Eine NSG enthält Regeln zur Zugriffssteuerung, die den Datenverkehr auf Grundlage der Richtung des Datenverkehrs, des Protokolls, der Quelladresse und des Quellports, und der Zieladresse und des Zielports zulässt oder verweigert. Die Regeln für eine NSG können jederzeit geändert werden, und die Änderungen werden auf alle zugeordneten Instanzen angewendet. Um eine NSG verwenden zu können, müssen Sie über ein regionales VNet verfügen.

>[AZURE.WARNING]NSGs sind nicht mit VNets kompatibel, die einer Affinitätsgruppe zugeordnet sind. Wenn Sie über kein regionales VNet verfügen und trotzdem den eingehenden Datenverkehr für Ihre Endpunkte steuern möchten, lesen Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste (Access Control List, ACL)?](./virtual-networks-acl.md) weiter. Sie können auch [Ihr VNet in ein regionales VNet migrieren](./virtual-networks-migrate-to-regional-vnet.md).

Sie können eine NSG einem virtuellen Computer oder einem Subnetz innerhalb eines VNets zuordnen. Bei der Zuordnung zu einem virtuellen Computer gilt die NSG für den gesamten ein- und ausgehenden Datenverkehr der VM-Instanz. Bei der Zuordnung zu einem Subnetz im VNet gilt sie für den gesamten ein- und ausgehenden Datenverkehr aller VM-Instanzen im Subnetz. Ein virtueller Computer bzw. ein Subnetz kann jeweils nur einer einzelnen NSG zugeordnet werden, und jede NSG kann bis zu 200 Regeln enthalten. Pro Abonnement können 100 NSGs verwendet werden.

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

### Besondere Infrastrukturregeln

NSG-Regeln sind explizit. Es wird nur der Datenverkehr zugelassen oder verweigert, der in der NSG angegeben ist. Es gibt jedoch zwei Arten von Datenverkehr, die unabhängig von der Netzwerksicherheitsgruppe immer zulässig sind. Diese Vorkehrung wurde zur Unterstützung der Infrastruktur getroffen.

- **Virtuelle IP des Hostknotens**: Grundlegende Infrastrukturdienste wie DHCP, DNS und die Systemüberwachung werden über die virtualisierte Host-IP-Adresse 168.63.129.16 bereitgestellt. Diese öffentliche IP-Adresse gehört Microsoft und ist die einzige virtuelle IP-Adresse, die in allen Regionen zu diesem Zweck verwendet wird. Die IP-Adresse wird der physischen IP-Adresse des Servercomputers (Hostknoten) zugeordnet, der den virtuellen Computer hostet. Der Hostknoten fungiert als DHCP-Relay, als rekursiver DNS-Resolver und als Integritätstestquelle für den Load Balancer und den Computer. Eingehende Kommunikation für diese IP-Adresse darf nicht als Angriff betrachtet werden.

- **Lizenzierung (Schlüsselverwaltungsdienst)**: Die auf den virtuellen Computern verwendeten Windows-Images müssen lizenziert werden. Zu diesem Zweck wird eine Lizenzierungsanforderung an die Hostserver des Schlüsselverwaltungsdiensts gesendet, die solche Abfragen verarbeiten. Dafür wird immer der ausgehende Port 1688 verwendet.

### Standardtags

Standardtags sind vom System bereitgestellte Bezeichner für eine Kategorie von IP-Adressen. Sie können in benutzerdefinierten Regeln angegeben werden. Folgende Standardtags stehen zur Verfügung:

- **VIRTUAL\_NETWORK**: Standardtag für Ihren gesamten Netzwerkadressraum. Dieser umfasst den Adressraum des virtuellen Netzwerks (IP-CIDR in Azure) sowie den gesamten verbundenen lokalen Adressraum (lokale Netzwerke). Dazu gehören auch VNet-zu-VNet-Adressräume.

- **AZURE\_LOADBALANCER**: Standardtag für den Infrastruktur-Load Balancer von Azure. Wird in eine Azure-Datencenter-IP umgewandelt, die als Ausgangspunkt für die Integritätstests von Azure fungiert. Nur erforderlich, wenn der virtuelle Computer oder die Gruppe virtueller Computer, der bzw. die der NSG zugeordnet ist, Teil eines Satzes mit Lastenausgleich ist.

- **INTERNET**: Standardtag für den IP-Adressraum, der außerhalb des virtuellen Netzwerks liegt und über das öffentliche Internet erreichbar ist. Dieser Bereich schließt auch den Azure-eigenen öffentlichen IP-Adressraum mit ein.

### Ports und Portbereiche

Regeln für Netzwerksicherheitsgruppen können für einen einzelnen Quell-/Zielport oder für einen Portbereich angegeben werden. Dies ist besonders dann hilfreich, wenn Sie eine Vielzahl von Ports für eine Anwendung (beispielsweise FTP) öffnen möchten. Der Bereich muss fortlaufend sein und kann nicht mit der Angabe einzelner Ports kombiniert werden.

Verwenden Sie zum Angeben eines Portbereichs das Minuszeichen (-), wie weiter unten für den Parameter *DestinationPortRange* dargestellt:

	Get-AzureNetworkSecurityGroup -Name ApptierSG `
	| Set-AzureNetworkSecurityRule -Name FTP -Type Inbound -Priority 600 -Action Allow `
		-SourceAddressPrefix INTERNET -SourcePortRange * `
		-DestinationAddressPrefix * -DestinationPortRange 100-500 -Protocol *

### ICMP-Datenverkehr

In NSG-Regeln kann derzeit als Protokoll nur „TCP“ oder „UDP“ angegeben werden. Ein spezielles ICMP-Tag ist nicht verfügbar. ICMP-Datenverkehr ist in einem virtuellen Netzwerk jedoch standardmäßig über die eingehenden VNet-Regeln zulässig, die ein-/ausgehenden Datenverkehr für sämtliche Ports und Protokolle (*) innerhalb des VNets zulassen.

## Zuordnen von NSGs

Zuordnen einer NSG zu einem virtuellen Computer: Bei direkter Zuordnung einer NSG zu einem virtuellen Computer werden die Netzwerkzugriffsregeln in der NSG direkt auf den gesamten Datenverkehr angewendet, der für den virtuellen Computer bestimmt ist. Jede Regeländerung in der NSG wird binnen weniger Minuten auf den Datenverkehr angewendet. Wenn die Zuordnung der NSG zum virtuellen Computer aufgehoben wird, wird der vorherige Zustand wiederhergestellt. Mit anderen Worten: Es werden wieder die Standardeinstellungen des Systems verwendet, die vor der Anwendung der NSG verwendet wurden.

Zuordnen einer NSG zu einem Subnetz: Bei der Zuordnung zu einem Subnetz werden die Netzwerkzugriffsregeln in der NSG auf alle virtuellen Computer im Subnetz angewendet. Jede Aktualisierung der Zugriffsregeln in der NSG wird binnen weniger Minuten auf alle virtuellen Computer im Subnetz angewendet.

Zuordnen einer NSG zu einem Subnetz und zu einem virtuellen Computer: Sie haben die Möglichkeit, eine NSG einem virtuellen Computer und eine andere NSG dem Subnetz zuzuordnen, in dem sich der virtuelle Computer befindet. In diesem unterstützten Fall ist der virtuelle Computer auf zwei Ebenen geschützt. Bei eingehendem Datenverkehr durchläuft das Paket die im Subnetz angegebenen Zugriffsregeln und anschließend die Regeln auf dem virtuellen Computer. Bei ausgehendem Datenverkehr durchläuft das Paket zuerst die Regeln auf dem virtuellen Computer und anschließend die im Subnetz angegebenen Regeln (siehe Diagramm weiter unten).

![NSG-ACLs](./media/virtual-networks-nsg/figure1.png)

Die Zuordnung einer NSG zu einem virtuellen Computer oder zu einem Subnetz macht die Netzwerkzugriffsregeln äußerst explizit. Die Plattform fügt keine implizite Regel ein, um eingehenden Datenverkehr für einen bestimmten Port zuzulassen. In diesem Fall gilt: Wenn Sie einen Endpunkt für den virtuellen Computer erstellen, müssen Sie auch eine Regel erstellen , die Datenverkehr aus dem Internet zulässt. Andernfalls kann von außen nicht auf die VIP-Adresse<Port> zugegriffen werden.

Ein Beispiel: Sie erstellen einen neuen virtuellen Computer und eine neue NSG. Anschließend ordnen Sie die NSG dem virtuellen Computer zu. Dank der Regel „ALLOW VNET INBOUND“ kann der virtuelle Computer mit anderen virtuellen Computern im virtuellen Netzwerk kommunizieren. Dank der Regel „ALLOW INTERNET OUTBOUND“ kann der virtuelle Computer zudem ausgehende Verbindungen mit dem Internet herstellen. Später erstellen Sie einen Endpunkt an Port 80, um Datenverkehr für Ihre auf dem virtuellen Computer ausgeführte Website empfangen zu können. Unter der VIP (öffentliche virtuelle IP-Adresse) an Port 80 gerichtete Pakete aus dem Internet erreichen den virtuellen Computer erst, wenn Sie der NSG eine Regel wie die folgende hinzufügen:

| Name | Priorität | Quell-IP | Quellport | Ziel-IP | Zielport | Protokoll | Access |
|------|----------|-----------|-------------|----------------|------------------|----------|--------|
| WEB | 100 | INTERNET | * | * | 80 | TCP | ZULASSEN |

## Überlegungen zum Entwurf

Sie müssen wissen, wie virtuelle Computer mit Infrastrukturdiensten und dem von Azure gehosteten PaaS-Dienst kommunizieren, wenn Sie Ihre NSGs entwerfen. Auf die meisten Azure-PaaS-Dienste, wie z. B. SQL-Datenbanken und Speicher, kann nur über eine öffentliche Internetadresse zugegriffen werden. Dies gilt auch für Lastenausgleichstests.

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

## Planung – Workflow für eine Netzwerksicherheitsgruppe

Im Anschluss finden Sie die grundlegenden Workflowschritte für die Verwendung von Netzwerksicherheitsgruppen.

### Workflow – Erstellen und Zuordnen einer NSG

1. Erstellen Sie eine Netzwerksicherheitsgruppe (NSG).

1. Fügen Sie Netzwerksicherheitsregeln hinzu, falls die Standardregeln nicht ausreichen.

1. Ordnen Sie die NSG einem virtuellen Computer zu.

1. Aktualisieren Sie den virtuellen Computer.

1. Die NSG-Regeln werden nach der Aktualisierung sofort wirksam.

### Workflow – Aktualisieren einer vorhandenen NSG

1. Fügen Sie einer vorhandenen NSG eine Regel hinzu, oder löschen/aktualisieren Sie eine Regel.

1. Die Aktualisierungen werden binnen weniger Minuten an alle virtuellen Computer verteilt, die der NSG zugeordnet sind. Ist die NSG-Regel bereits dem virtuellen Computer zugeordnet, muss dieser nicht aktualisiert werden.

### Workflow – Ändern einer NSG-Zuordnung

1. Ordnen Sie einem virtuellen Computer, der bereits mit einer anderen NSG verknüpft ist, eine neue NSG zu.

1. Aktualisieren Sie den virtuellen Computer.

1. Die Regeln der neuen NSG werden binnen weniger Minuten wirksam.

## Erstellen, Konfigurieren und Verwalten von Netzwerksicherheitsgruppen

Derzeit können NSGs ausschließlich über PowerShell-Cmdlets und REST-APIs konfiguriert und geändert werden. NSGs können nicht über das Verwaltungsportal konfiguriert werden. Zum Erstellen, Konfigurieren und Verwalten von NSGs stehen folgende PowerShell-Cmdlets zur Verfügung:

**Erstellen einer Netzwerksicherheitsgruppe**

	New-AzureNetworkSecurityGroup -Name "MyVNetSG" -Location uswest `
		-Label "Security group for my Vnet in West US"

**Hinzufügen oder Aktualisieren von Regeln**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityRule -Name WEB -Type Inbound -Priority 100 `
		-Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' `
		-DestinationAddressPrefix '*' -DestinationPortRange '*' -Protocol TCP


**Löschen einer Regel aus einer NSG**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityRule -Name WEB

**Zuordnen einer NSG zu einem virtuellen Computer**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Set-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Anzeigen von einem virtuellen Computer zugeordneten NSGs**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Get-AzureNetworkSecurityGroupAssociation

**Entfernen einer NSG von einem virtuellen Computer**

	Get-AzureVM -ServiceName "MyWebsite" -Name "Instance1" `
	| Remove-AzureNetworkSecurityGroupConfig -NetworkSecurityGroupName "MyVNetSG" `
	| Update-AzureVM

**Zuordnen einer NSG zu einem Subnetz**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Anzeigen von einem Subnetz zugeordneten NSGs**

	Get-AzureNetworkSecurityGroupForSubnet -SubnetName 'FrontEndSubnet' `
		-VirtualNetworkName 'VNetUSWest' 

**Entfernen einer NSG aus dem Subnetz**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" `
	| Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'VNetUSWest' `
		-SubnetName 'FrontEndSubnet'

**Löschen einer NSG**

	Remove-AzureNetworkSecurityGroup -Name "MyVNetSG"

**Abrufen der Details und Regeln einer NSG**

	Get-AzureNetworkSecurityGroup -Name "MyVNetSG" -Detailed
 
**Anzeigen aller Azure PowerShell-Cmdlets für NSGs**

	Get-Command *azurenetworksecuritygroup*

<!---HONumber=Sept15_HO3-->