<properties
	pageTitle="Anfügen eines Datenträgers an einen virtuellen Computer | Microsoft Azure"
	description="Fügen Sie einen Datenträger an einen virtuellen Windows-Computer an, der mit dem klassischen Bereitstellungsmodell erstellt wurde, und initialisieren Sie ihn."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="cynthn"/>

# Anfügen eines Datenträgers an einen virtuellen Windows-Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-attach-disk-preview.md).

Wenn Sie einen zusätzlichen Datenträger benötigen, können Sie einen leeren Datenträger oder einen vorhandenen Datenträger mit Daten an einen virtuellen Computer anfügen. In beiden Fällen sind die Datenträger eigentlich VHD-Dateien, die sich in einem Azure-Speicherkonto befinden müssen. Einen neuen Datenträger müssen Sie nach dem Anfügen außerdem noch initialisieren, damit er durch einen virtuellen Windows-Computer genutzt werden kann.

Es empfiehlt sich, einen oder mehrere separate Datenträger zu verwenden, um die Daten eines virtuellen Computers zu speichern. Wenn Sie einen virtuellen Azure-Computer erstellen, verfügt dieser über einen Datenträger für das Betriebssystem, der Laufwerk C zugeordnet ist, und einen temporären Datenträger, der Laufwerk D zugeordnet ist. **Verwenden Sie den temporären Datenträger nicht zum Speichern von Daten.** Wie der Name schon sagt, bietet dieser temporäre Datenträger nur eine temporäre Speicherung. Es ermöglicht keine Redundanz oder Sicherung, da es sich nicht im Azure-Speicher befindet.

## Video zur exemplarischen Vorgehensweise

Hier finden Sie eine exemplarische Vorgehensweise für die Schritte dieses Tutorials.

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Gewusst wie: Initialisieren eines neuen Datenträgers unter Windows Server

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][logon].

2. Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

	![Server-Manager öffnen](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Erweitern Sie das Menü, und wählen Sie **Datenträger**.

4. Im Abschnitt **Datenträger** werden die Datenträger aufgelistet. In den meisten Fällen sind darin Datenträger 0, Datenträger 1 und Datenträger 2 enthalten. Datenträger 0 ist der Betriebssystemdatenträger. Bei Datenträger 1 handelt es sich um den temporären Datenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Datenträger 2 ist der Datenträger, den Sie gerade an den virtuellen Computer angefügt haben. Für den neuen Datenträger wird die Partition als **Unbekannt** aufgelistet. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen Sie **Initialisieren** aus.

5.	Sie werden darüber informiert, dass alle Daten gelöscht werden, wenn der Datenträger initialisiert wird. Klicken Sie auf **Ja**, um die Warnung zu bestätigen und den Datenträger zu initialisieren. Wenn dies abgeschlossen ist, wird die Partition als **GPT** aufgelistet. Klicken Sie erneut mit der rechten Maustaste auf den Datenträger, und wählen Sie **Neues Volume** aus.

6.	Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet. Der Datenträger ist jetzt online und zum Speichern von Daten bereit.

	![Volume erfolgreich initialisiert](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).

## Zusätzliche Ressourcen

[Trennen eines Datenträgers von einem virtuellen Computer unter Windows](storage-windows-detach-disk.md)

[Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=Nov15_HO2-->