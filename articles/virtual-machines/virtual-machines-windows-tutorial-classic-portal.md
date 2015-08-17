<properties
	pageTitle="Erstellen eines virtuellen Computers unter Windows in Azure"
	description="Erstellen Sie einen virtuellen Windows-Computer im Azure-Portal."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/05/2015"
	ms.author="kathydav"/>

# Erstellen Sie einen virtuellen Windows-Computer im Azure-Portal

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell - Resource management](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell - Service management](virtual-machines-ps-create-preconfigure-windows-vms.md)


In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer (VM) im Azure-Portal zu erstellen. Als Beispiel wird ein Windows Server-Image verwendet. Dies ist jedoch nur eines von vielen Images, die Azure bietet. Beachten Sie, dass Ihre Imageauswahl von Ihrem Abonnement abhängt. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.

Sie können auch mithilfe [eigener Images](virtual-machines-create-upload-vhd-windows-server.md) virtuelle Computer erstellen. Informationen zu dieser und zu anderen Methoden finden Sie unter [Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Exemplarische Vorgehensweise per Video

Hier finden Sie eine exemplarische Vorgehensweise für dieses Lernprogramm.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** ￼￼￼￼im Azure-Portal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [AZURE.NOTE]Sie können auch das funktionsreichere, anpassbare [Azure-Vorschauportal](https://portal.azure.com) verwenden, um einen virtuellen Computer zu erstellen, die Bereitstellung von Anwendungsvorlagen für mehrere virtuelle Computer zu automatisieren, erweiterte VM-Überwachungs- und Diagnosefeatures zu verwenden und mehr. Die verfügbaren VM-Konfigurationsoptionen in den beiden Portalen sind ähnlich, aber nicht identisch.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## Nächste Schritte

- Melden Sie sich beim virtuellen Computer an. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md).

- Fügen Sie einen Datenträger zum Speichern von Daten hinzu. Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. Anweisungen finden Sie im [Lernprogramm zum Hinzufügen von Datenträgern](storage-windows-attach-disk.md).

## Zusätzliche Ressourcen

Weitere Informationen zur Konfiguration eines virtuellen Computers und den geeigneten Zeitpunkten finden Sie unter [Informationen zu Azure-VM-Konfigurationseinstellungen](http://msdn.microsoft.com/library/azure/dn763935.aspx).

<!---HONumber=August15_HO6-->