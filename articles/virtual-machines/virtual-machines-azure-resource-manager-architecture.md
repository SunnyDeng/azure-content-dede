<properties
   pageTitle="Architektur des Azure-Ressourcen-Managers | Microsoft Azure"
   description="Lernen Sie die Architektur des Ressourcen-Managers und die Beziehungen zwischen den Anbietern für Compute-, Netzwerk- und Speicherressourcen kennen."
   services="virtual-machines"
   documentationCenter=""
   authors="davidmu1"
   manager="timlt"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="davidmu"/>

# Architektur des Azure-Ressourcen-Managers

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.



Dieser Artikel bietet einen Überblick über die Architektur der Dienstverwaltung und des Ressourcen-Managers zum Erstellen infrastrukturbasierter Anwendungen und Workloads.

## Architektur für die Dienstverwaltung

Bevor die Architektur des Azure-Ressourcen-Managers und die verschiedenen Ressourcenanbieter erläutert werden, betrachten wir die Architektur, die derzeit für die Azure-Dienstverwaltung vorhanden ist. In der Azure-Dienstverwaltung werden die Compute-, Speicher- oder Netzwerkressourcen für das Hosten von virtuellen Computern wie folgt bereitgestellt:

- Ein erforderlicher Clouddienst, der als Container für das Hosten virtueller Computer (Compute) fungiert. Virtuelle Computer werden automatisch mit einer Netzwerkschnittstellenkarte (Network Interface Card, NIC) bereitgestellt, und ihnen wird von Azure eine IP-Adresse zugewiesen. Darüber hinaus enthält der Clouddienst eine externe Lastenausgleichsinstanz, eine öffentliche IP-Adresse und Standardendpunkte, um Remotedesktop- und Remote-PowerShell-Datenverkehr für die Windows-basierten virtuellen Computer und Secure Shell-Datenverkehr (SSH) für Linux-basierte virtuelle Computer zu ermöglichen.
- Ein erforderliches Speicherkonto, in dem die VHDs für einen virtuellen Computer gespeichert werden, einschließlich des Betriebssystems sowie temporärer und zusätzlicher Datenträger (Speicher).
- Ein optionales virtuelles Netzwerk, das als zusätzlicher Container fungiert, in dem Sie eine Subnetzstruktur erstellen und das Subnetz benennen können, in dem sich der virtuelle Computer befindet (Netzwerk).

Im Folgenden sind die Komponenten und deren Beziehungen für die Azure-Dienstverwaltung dargestellt.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

## Architektur des Ressourcen-Managers

Für den Azure-Ressourcen-Manager unterstützen Ressourcenanbieter die einzelnen Ressourcen zum Erstellen von funktionsfähigen virtuellen Computern in der von Ihnen benötigten Konfiguration. Für virtuelle Computer gibt es drei wichtige Ressourcenanbieter:

- Anbieter von Computeressourcen (Compute Resource Provider, CRP): Unterstützen Instanzen von virtuellen Computern und optionale Verfügbarkeitsgruppen.
- Anbieter von Speicherressourcen (Storage Resource Provider, SRP): Unterstützen erforderliche Speicherkonten, die VHDs für virtuelle Computer speichern, einschließlich der Datenträger für Betriebssysteme und zusätzlicher Datenträger.
- Anbieter von Netzwerkressourcen (Network Resource Provider, NRP): Unterstützen erforderliche NICs, IP-Adressen virtueller Computer und Subnetze innerhalb von virtuellen Netzwerken sowie optionale Lastenausgleichsinstanzen, deren IP-Adressen und Netzwerksicherheitsgruppen.

Außerdem gibt es Beziehungen zwischen den Ressourcen innerhalb der Ressourcenanbieter:

- Ein virtueller Computer hängt von einem bestimmten Speicherkonto ab, das im SRP definiert ist, um die Datenträger im Blobspeicher zu speichern (erforderlich).
- Ein virtueller Computer verweist auf eine bestimmte NIC, die im NRP definiert ist (erforderlich), und eine Verfügbarkeitsgruppe, die im CRP definiert ist (optional).
- Eine NIC verweist auf die dem virtuellen Computer zugewiesene IP-Adresse (erforderlich), das Subnetz des virtuellen Netzwerks für den virtuellen Computer (erforderlich) und eine Netzwerksicherheitsgruppe (optional).
- Ein Subnetz innerhalb eines virtuellen Netzwerks verweist auf eine Netzwerksicherheitsgruppe (optional).
- Eine Lastenausgleichsinstanz verweist auf den Back-End-Pool von IP-Adressen, die die NIC eines virtuellen Computers enthalten (optional) und auf eine öffentliche oder private IP-Adresse für den Lastenausgleich (optional).

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch2.png)

Die Komponentisierung von Ressourcen ermöglicht mehr Flexibilität beim Konfigurieren der Infrastruktur für eine IT-Workload, die in Azure gehostet wird. Mit Vorlagen für den Azure-Ressourcen-Manager wird diese Flexibilität genutzt, um einen Satz von abhängigen Ressourcen für eine bestimmte Konfiguration zu erstellen. Beim Ausführen einer Vorlage stellt der Ressourcen-Manager sicher, dass die Ressourcen für eine Konfiguration in der richtigen Reihenfolge erstellt werden, um die Abhängigkeiten und Verweise beizubehalten. Beispielsweise erstellt der Ressourcen-Manager die NIC für einen virtuellen Computer erst, nachdem er ein virtuelles Netzwerk mit einem Subnetz und einer IP-Adresse erstellt hat (eine Netzwerksicherheitsgruppe ist optional).

Eine Ressourcengruppe ist ein logischer Container, der ähnliche Ressourcen für eine Anwendung enthält. Dies können mehrere virtuelle Computer, NICs, IP-Adressen, Lastenausgleichsinstanzen, Subnetze und Netzwerksicherheitsgruppen sein. Sie können beispielsweise alle Ressourcen der Anwendung als eine einzelne Verwaltungseinheit verwalten. Sie können sie alle gleichzeitig erstellen, aktualisieren und löschen. Im Folgenden sehen Sie eine Beispielanwendung, die in einer einzelnen Ressourcengruppen bereitgestellt wurde.

![](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Diese Anwendung besteht aus Folgendem:

- Zwei virtuellen Computern, die das gleiche Speicherkonto verwenden und sich in der gleichen Verfügbarkeitsgruppe und im gleichen Subnetz eines virtuellen Netzwerks befinden.
- Einer einzelnen NIC- und VM-IP-Adresse für jeden virtuellen Computer.
- Einer externen Lastenausgleichsinstanz, die den Internetdatenverkehr auf die NICs der zwei virtuellen Computer verteilt.

Alle Ressourcen der Anwendung werden über eine Ressourcengruppe verwaltet, in der sie enthalten sind.

Sie können die Komponentisierung sowie die Abhängigkeitsbeziehungen zwischen Ressourcen auch sehen, wenn Sie einen Ressourcen-Manager-basierten virtuellen Computer mit Azure PowerShell oder der Azure-Befehlszeilenschnittstelle erstellen. Bevor Sie den Befehl ausführen können, der den virtuellen Computer erstellt, müssen Sie eine Ressourcengruppe, ein Speicherkonto, ein virtuelles Netzwerk mit einem Subnetz und eine NIC mit einer IP-Adresse erstellen. Weitere Informationen finden Sie unter [Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md).

## Nächste Schritte

[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Bereitstellen und Verwalten von virtuellen Azure-Computern mit Ressourcen-Manager-Vorlagen und PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Zusätzliche Ressourcen

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Azure Resource Manager-Übersicht](resource-group-overview.md)

<!---HONumber=AcomDC_1203_2015-->