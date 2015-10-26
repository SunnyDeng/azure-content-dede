<properties
	pageTitle="Erstellen eines virtuellen Computers unter Windows in Azure"
	description="Erstellen Sie einen virtuellen Windows-Computer im Azure-Portal."
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

# Erstellen Sie einen virtuellen Windows-Computer im Azure-Portal

> [AZURE.SELECTOR]
- [Azure preview portal](virtual-machines-windows-tutorial.md)
- [Azure portal](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: Resource Manager deployment](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: Classic deployment](virtual-machines-ps-create-preconfigure-windows-vms.md)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-windows-tutorial.md).

In diesem Lernprogramm erfahren Sie, wie einfach es ist, einen virtuellen Azure-Computer (VM) im Azure-Portal zu erstellen. Als Beispiel wird ein Windows Server-Image verwendet. Dies ist jedoch nur eines von vielen Images, die Azure bietet. Beachten Sie, dass Ihre Imageauswahl von Ihrem Abonnement abhängt. Beispielsweise können Desktop-Images für MSDN-Abonnenten verfügbar sein.

Sie können auch mithilfe [eigener Images](virtual-machines-create-upload-vhd-windows-server.md) virtuelle Computer erstellen. Informationen zu dieser und zu anderen Methoden finden Sie unter [Verschiedene Möglichkeiten zum Erstellen eines virtuellen Windows-Computers](virtual-machines-windows-choices-create-vm.md).

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## Exemplarische Vorgehensweise per Video

Hier finden Sie eine exemplarische Vorgehensweise für dieses Lernprogramm.

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>Erstellen eines virtuellen Computers

In diesem Abschnitt erfahren Sie, wie Sie die Option **Aus Galerie** im Azure-Portal verwenden, um einen virtuellen Computer zu erstellen. Mit dieser Option erhalten Sie mehr Konfigurationsoptionen als mit **Schnellerfassung**. Wenn Sie beispielsweise einen virtuellen Computer mit einem virtuellen Netzwerk verbinden möchten, sollten Sie die Option **Aus Galerie** verwenden.

> [AZURE.NOTE]Sie können auch das funktionsreichere, anpassbare [Azure-Vorschauportal](https://portal.azure.com) verwenden, um einen virtuellen Computer zu erstellen, erweiterte Überwachungs- und Diagnosefeatures zu verwenden, Storage Premium einzusetzen und vieles mehr. Die verfügbaren Optionen zum Konfigurieren eines virtuellen Computers sind in den beiden Portalen im Wesentlichen ähnlich, aber nicht identisch. Verwenden Sie z. B. das Vorschauportal, um einen virtuellen Computer mit Premium-Speicher zu konfigurieren.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## Nächste Schritte

- Melden Sie sich beim virtuellen Computer an. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird](virtual-machines-log-on-windows-server.md).

- Fügen Sie einen Datenträger zum Speichern von Daten hinzu. Sie können sowohl leere Datenträger als auch Datenträger mit Daten anfügen. Anweisungen finden Sie im [Lernprogramm zum Hinzufügen von Datenträgern](storage-windows-attach-disk.md).

<!---HONumber=Oct15_HO3-->