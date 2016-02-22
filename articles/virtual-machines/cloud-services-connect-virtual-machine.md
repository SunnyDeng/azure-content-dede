<properties
	pageTitle="Verbinden virtueller Computer in einem Clouddienst | Microsoft Azure"
	description="Verbinden virtueller Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden, mit einem Azure-Clouddienst oder einem virtuellen Netzwerk."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cynthn"/>


# Verbinden virtueller Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden, mit einem virtuellen Netzwerk oder einem Clouddienst

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt werden, befinden sich immer in einem Clouddienst. Der Clouddienst fungiert als Container und stellt einen eindeutigen öffentlichen DNS-Namen, eine öffentliche IP-Adresse und einen Satz von Endpunkten bereit, damit auf den virtuellen Computer über das Internet zugegriffen werden kann. Der Clouddienst kann sich in einem virtuellen Netzwerk befinden, was jedoch nicht zwingend erforderlich ist.

Befindet sich ein Clouddienst nicht in einem virtuellen Netzwerk, handelt es sich um einen *eigenständigen* Clouddienst. Die virtuellen Computer in einem eigenständigen Clouddienst können mit anderen virtuellen Computern nur über deren öffentliche DNS-Namen kommunizieren. Dieser Datenverkehr läuft über das Internet. Wenn ein Clouddienst mit einem virtuellen Netzwerk verbunden ist, können die virtuellen Computer in diesem Clouddienst mit allen anderen virtuellen Computern in dem virtuellen Netzwerk kommunizieren, ohne dass Daten über das Internet gesendet werden.

Wenn Sie Ihre virtuellen Computer im gleichen eigenständigen Clouddienst platzieren, können Sie Lastenausgleich und Verfügbarkeitsgruppen immer noch nutzen. Weitere Informationen finden Sie unter [Lastenausgleich virtueller Computer](load-balance-virtual-machines.md) und [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-manage-availability.md). Sie können die virtuellen Computer jedoch nicht in Subnetzen organisieren oder einen eigenständigen Clouddienst mit Ihrem lokalen Netzwerk verbinden. Hier sehen Sie ein Beispiel:

![Virtuelle Computer in einem eigenständigen Clouddienst](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

Wenn Sie Ihre virtuellen Computer in einem virtuellen Netzwerk platzieren, können Sie entscheiden, wie viele Clouddienste Sie verwenden möchten, um Lastenausgleich und Verfügbarkeitsgruppen zu nutzen. Darüber hinaus können Sie die virtuellen Computer in Subnetzen auf die gleiche Weise organisieren wie Ihr lokales Netzwerk und das virtuelle Netzwerk mit Ihrem lokalen Netzwerk verbinden. Hier sehen Sie ein Beispiel:

![Virtuelle Computer in einem virtuellen Netzwerk](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

Virtuelle Netzwerke sind das empfohlene Verfahren zum Verbinden virtueller Computern in Azure. Die bewährte Methode ist es, jede Ebene der Anwendung in einem separaten Clouddienst zu konfigurieren. Möglicherweise müssen Sie jedoch einige virtuelle Computer aus verschiedenen Anwendungsebenen im gleichen Clouddienst kombinieren, um die Höchstzahl von 200 Clouddiensten pro Abonnement nicht zu überschreiten. Diese und andere Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-subscription-service-limits.md).

## Virtuelle Computer in einem virtuellen Netzwerk verbinden

So verbinden Sie virtuelle Computer in einem virtuellen Netzwerk:

1.	Erstellen Sie das virtuelle Netzwerk im [Azure-Portal](virtual-networks-create-vnet-classic-pportal.md).
2.	Erstellen Sie die Gruppe von Clouddiensten für Ihre Bereitstellung entsprechend Ihrem Entwurf für Verfügbarkeitsgruppen und Lastenausgleich. Klicken Sie im klassischen Azur-Portal für jeden Clouddienst auf **Neu > Compute > Cloud-Dienst > Benutzerdefiniert erstellen**.
3.	Klicken Sie zum Erstellen der einzelnen neuen virtuellen Computer auf **Neu > Compute > Virtueller Computer > Aus Katalog**. Wählen Sie den richtigen Clouddienst und das richtige virtuelle Netzwerk für den virtuellen Computer aus. Wurde der Clouddienst bereits mit einem virtuellen Netzwerk verbunden, ist der Name dieses Diensts bereits für Sie ausgewählt.

![Auswählen eines Clouddiensts für einen virtuellen Computer](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## Virtuelle Computer in einem eigenständigen Clouddienst verbinden

So verbinden Sie virtuelle Computer in einem eigenständigen Clouddienst:

1.	Erstellen Sie den Clouddienst im [klassischen Azure-Portal](http://manage.windowsazure.com). Klicken Sie auf **Neu > Compute > Clouddienst > Benutzerdefiniert erstellen**. Alternativ können Sie beim Erstellen des ersten virtuellen Computers den Clouddienst für Ihre Bereitstellung erstellen.

2.	Wählen Sie beim Erstellen der virtuellen Computer den Namen des im vorherigen Schritts erstellten Clouddiensts aus.

	![Hinzufügen eines virtuellen Computers zu einem vorhandenen Clouddienst](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##Ressourcen
[Lastenausgleich virtueller Computer](load-balance-virtual-machines.md)

[Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-manage-availability.md)

Nachdem Sie einen virtuellen Computer erstellt haben, empfiehlt es sich, einen Datenträger hinzuzufügen, damit ein Speicherort für die Daten der Dienste und Arbeitsauslastungen verfügbar ist. Weitere Informationen finden Sie unter einer der folgenden Optionen:

[Vorgehensweise: Anfügen eines Datenträgers an einen virtuellen Linux-Computer](virtual-machines-linux-how-to-attach-disk.md)

[Anfügen eines Datenträgers an einen virtuellen Windows-Computer](storage-windows-attach-disk.md)

<!---HONumber=AcomDC_0211_2016-->