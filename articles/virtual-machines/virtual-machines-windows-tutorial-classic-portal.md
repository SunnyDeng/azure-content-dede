<properties
	pageTitle="Erstellen eines virtuellen Computers unter Windows in Azure"
	description="Erfahren Sie, wie Sie im klassischen Azure-Portal virtuelle Windows-Computer erstellen können."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-classic-portal"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="kathydav"/>



# Erstellen eines virtuellen Computers unter Windows

> [AZURE.SELECTOR]
- [Azure portal](virtual-machines-windows-tutorial.md)
- [Azure classic portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-vms.md)

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer (Azure-VM) zu erstellen. In diesem Lernprogramm wird ein Windows Server-Image verwendet, das jedoch nur eines von vielen in Azure verfügbaren Images ist. Hierzu zählen Windows- und Linux-basierte Betriebssysteme sowie Images mit vorinstallierten Anwendungen. Welche Images Sie auswählen können, hängt jeweils von der Art Ihres Abonnements ab. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.

Sie können auch mithilfe [eigener Images](../virtual-machines-create-upload-vhd-windows-server-classic-portal.md) virtuelle Windows-Computer erstellen. Weitere Informationen zu virtuellen Azure-Computern finden Sie unter [Übersicht über virtuelle Computer in Azure](http://msdn.microsoft.com/library/azure/jj156143.aspx).

[AZURE.INCLUDE [Hinweis zur kostenlosen Testversion](../../includes/free-trial-note.md)]

## <a id="createvirtualmachine"> </a>Erstellen von virtuellen Computern

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** ￼￼￼￼im klassischen Azure-Portal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [AZURE.NOTE]Sie können auch das umfangreichere, anpassbare [Azure-Portal](https://portal.azure.com) testen, um einen virtuellen Computer zu erstellen, das Bereitstellen von Anwendungsvorlagen für mehrere virtuelle Computer zu automatisieren, erweiterte Überwachungs- und Diagnosefunktionen für virtuelle Computer auszuführen und vieles mehr. Die verfügbaren VM-Konfigurationsoptionen in den beiden Portalen sind ähnlich, aber nicht identisch.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Nächste Schritte

- Melden Sie sich beim virtuellen Computer an. Ausführliche Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md).

- Fügen Sie einen Datenträger zum Speichern von Daten hinzu. Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. Anweisungen finden Sie im [Lernprogramm zum Hinzufügen von Datenträgern](storage-windows-attach-disk.md).

## Zusätzliche Ressourcen

Weitere Informationen zur Konfiguration eines virtuellen Computers und den geeigneten Zeitpunkten finden Sie unter [Informationen zu Azure-VM-Konfigurationseinstellungen](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!--HONumber=52-->
 