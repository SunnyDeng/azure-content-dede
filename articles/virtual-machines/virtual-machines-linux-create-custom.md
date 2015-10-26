<properties
	pageTitle="Erstellen eines virtuellen Linux-Computers | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie einen benutzerdefinierten virtuellen Computer mit dem Linux-Betriebssystem unter Verwendung des klassischen Bereitstellungsmodells erstellen."
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

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] []Ressourcen-Manager-Modell](virtual-machines-linux-tutorial.md).

Dieses Thema enthält Informationen zum Erstellen eines *benutzerdefinierten* virtuellen Computers mithilfe der Azure-Befehlszeilenschnittstelle und des klassischen Bereitstellungsmodells. Wir verwenden ein Linux-Image aus den verfügbaren **IMAGES**in Azure. Die Azure-Befehlszeilenschnittstelle enthält u. a. folgende Konfigurationsoptionen:

- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk
- Hinzufügen des virtuellen Computers zu einem vorhandenen Clouddienst
- Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe oder einem Speicherort

> [AZURE.IMPORTANT]Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Übersicht über Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).

<p/>


- [Erstellen eines virtuellen Linux-Computers](virtual-machines-linux-tutorial.md)


## Erstellen eines virtuellen Linux-Computers mithilfe des klassischen Bereitstellungsmodells

[AZURE.INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]

<!---HONumber=Oct15_HO3-->