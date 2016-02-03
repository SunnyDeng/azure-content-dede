<properties
	pageTitle="Erstellen einer benutzerdefinierten virtuellen Windows-Maschine| Microsoft Azure"
	description="Hier erfahren Sie, wie Sie eine benutzerdefinierte virtuelle Maschine über das klassische Azure-Portal unter Verwendung des klassischen Bereitstellungsmodells erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/15/2016"
	ms.author="cynthn"/>

	
# Erstellen eines benutzerdefinierten virtuellen Computers mit Windows


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.
 


Ein *benutzerdefinierter* virtueller Computer ist einfach ein virtueller Computer, den Sie mit der Option **Aus Katalog** erstellen, da Sie durch diese Option mehr Konfigurationsmöglichkeiten erhalten als durch die Option **Schnellerfassung**. Zu diesen Möglichkeiten gehören:

- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk.
- Installieren des virtuellen Azure-Computer-Agents und der -Erweiterungen, z. B. für Antischadsoftware.
- Hinzufügen des virtuellen Computers zu vorhandenen Clouddiensten.
- Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto.
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe.

> [AZURE.IMPORTANT]Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Übersicht über Azure Virtual Network](virtual-networks-overview.md).


## So erstellen Sie den virtuellen Computer


[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=AcomDC_0121_2016-->