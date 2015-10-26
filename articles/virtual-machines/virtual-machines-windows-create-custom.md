<properties
	pageTitle="Erstellen eines benutzerdefinierten virtuellen Windows-Computers | Microsoft Azure"
	description="Erfahren Sie, wie Sie in Azure einen benutzerdefinierten virtuellen Computer erstellen, auf dem Windows ausgeführt wird."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>


<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="cynthn"/>

#Erstellen eines benutzerdefinierten virtuellen Computers mit Windows in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Ein *benutzerdefinierter* virtueller Computer ist lediglich ein virtueller Computer, den Sie mit der Option **Aus Katalog** erstellen, da Sie durch diese Option mehr Konfigurationsmöglichkeiten erhalten als durch die Option **Schnellerfassung**. Zu diesen Möglichkeiten gehören:

- Verbinden des virtuellen Computers mit einem virtuellen Netzwerk.
- Installieren von VM-Agent und -Erweiterungen z. B. für Antischadsoftware
- Hinzufügen des virtuellen Computers zu einem vorhandenen Clouddienst.
- Hinzufügen des virtuellen Computers zu einem vorhandenen Speicherkonto.
- Hinzufügen des virtuellen Computers zu einer Verfügbarkeitsgruppe.

> [AZURE.IMPORTANT]Wenn Ihr virtueller Computer ein virtuelles Netzwerk verwenden soll, damit Sie sich direkt über den Hostnamen oder über eine lokal eingerichtete Verbindung mit ihm verbinden können, stellen Sie sicher, dass Sie das virtuelle Netzwerk schon dann angeben, wenn Sie den virtuellen Computer erstellen. Ein virtueller Computer kann so konfiguriert werden, dass er nur zu dem Zeitpunkt Teil eines virtuellen Netzwerks werden kann, zu dem er erstellt wird. Ausführliche Informationen über virtuelle Netzwerke erhalten Sie unter [Übersicht über Azure Virtual Network](virtual-networks-overview.md).

##So erstellen Sie den virtuellen Computer

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

<!---HONumber=Oct15_HO3-->