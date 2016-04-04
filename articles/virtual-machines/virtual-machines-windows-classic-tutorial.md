<properties
	pageTitle="Erstellen einer virtuellen Maschine unter Windows im klassischen Portal | Microsoft Azure"
	description="Erstellen Sie einen virtuellen Windows-Computer im klassischen Azure-Portal."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="cynthn"/>

# Erstellen eines virtuellen Computers unter Windows im klassischen Azure-Portal

> [AZURE.SELECTOR]
- [Azure-Portal](virtual-machines-windows-hero-tutorial.md)
- [Klassisches Azure-Portal](virtual-machines-windows-classic-tutorial.md)
- [PowerShell: Resource Manager-Bereitstellung](virtual-machines-windows-ps-manage.md)
- [PowerShell: Klassische Bereitstellung](virtual-machines-windows-classic-create-powershell.md)

<!-- HHTML comment in to break between the selector and the note in the include below-->

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-windows-hero-tutorial.md).

In diesem Tutorial erfahren Sie, wie einfach es ist, eine virtuelle Azure-Maschine (VM) mit Windows im klassischen Azure-Portal zu erstellen. Als Beispiel wird ein Windows Server-Image verwendet. Dies ist jedoch nur eines von vielen Images, die Azure bietet. Beachten Sie, dass Ihre Imageauswahl von Ihrem Abonnement abhängt. Beispielsweise können Windows-Desktop-Images für MSDN-Abonnenten verfügbar sein.


Sie können auch mithilfe [eigener Images](virtual-machines-windows-classic-createupload-vhd.md) virtuelle Computer erstellen. Informationen zu dieser und zu anderen Methoden finden Sie unter [Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-creation-choices.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Exemplarische Vorgehensweise per Video

Hier finden Sie eine exemplarische Vorgehensweise für dieses Lernprogramm.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im klassischen Azure-Portal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [AZURE.NOTE] Sie können auch das funktionsreichere, anpassbare Azure-Portal verwenden, um einen virtuellen Computer zu erstellen, erweiterte Überwachungs- und Diagnosefeatures zu verwenden, Premium-Speicher einzusetzen und vieles mehr. Die verfügbaren Optionen zum Konfigurieren eines virtuellen Computers sind in den beiden Portalen im Wesentlichen ähnlich, aber nicht identisch. Verwenden Sie z. B. das Azure-Portal, um einen virtuellen Computer mit Premium-Speicher zu konfigurieren.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Nächste Schritte

- Melden Sie sich beim virtuellen Computer an. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-windows-classic-connect-logon.md).

- Fügen Sie einen Datenträger zum Speichern von Daten hinzu. Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. Anweisungen finden Sie unter [Anfügen eines Datenträgers an einen virtuellen Windows-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde](virtual-machines-windows-classic-attach-disk.md).

<!---HONumber=AcomDC_0323_2016-->