<properties
	pageTitle="Erstellen eines benutzerdefinierten virtuellen Computers unter Linux in Azure"
	description="Erstellen eines benutzerdefinierten virtuellen Computers unter Linux in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="dkshir"/>

# Erstellen eines benutzerdefinierten virtuellen Computers unter Linux in Azure

Dieses Thema enthält Informationen zum Erstellen eines *benutzerdefinierten* virtuellen Computers mithilfe der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows im Azure-Dienstverwaltungsdodus. Wir verwenden ein Linux-Image von den verfügbaren **IMAGES**in Azure. Die Azure-Befehlszeilenschnittstelle enthält u. a. folgende Konfigurationsoptionen:

- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk
- Hinzufügen des virtuellen Computers zu einem vorhandenen Cloud-Dienst
- Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe oder einem Speicherort

> [AZURE.IMPORTANT]Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Überblick über Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>
[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md)


## So erstellen Sie einen virtuellen Linux-Computer mit der Azure-Dienstverwaltung

[AZURE.INCLUDE [Virtueller-Computer-erstellen-LinuxVM](../../includes/virtual-machines-create-LinuxVM.md)]

<!---HONumber=August15_HO6-->