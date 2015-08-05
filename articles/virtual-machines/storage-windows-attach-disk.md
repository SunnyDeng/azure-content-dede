<properties
	pageTitle="Anfügen eines Datenträgers an einen virtuellen Computer | Azure"
	description="Erfahren Sie, wie Sie einen Datenträger an einen virtuellen Azure-Computer anfügen und initialisieren können, damit er betriebsbereit ist."
	services="virtual-machines, storage"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="kathydav"/>

# Anfügen eines Datenträgers an einen virtuellen Windows-Computer

Sie können leere Datenträger und Datenträger mit Daten anfügen. In beiden Fällen sind die Datenträger eigentlich VHD-Dateien, die sich in einem Azure-Speicherkonto befinden. Außerdem müssen Sie in beiden Fällen den Datenträger nach dem Anfügen initialisieren, damit er verwendet werden kann.

> [AZURE.NOTE]Es empfiehlt sich, einen oder mehrere separate Datenträger zu verwenden, um die Daten eines virtuellen Computers zu speichern. Wenn Sie einen virtuellen Azure-Computer erstellen, hat dieser einen Datenträger für das Betriebssystem, der Laufwerk C zugeordnet ist, und einen temporären Datenträger, der Laufwerk D zugeordnet ist. **Verwenden Sie Laufwerk D nicht zum Speichern von Daten.** Wie der Name schon sagt, bietet dieser Datenträger nur temporäre Speicherung. Er ermöglicht keine Redundanz oder Sicherung, da er sich nicht im Azure-Speicher befindet.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>Gewusst wie: Initialisieren eines neues Datenträgers unter Windows Server

1. Stellen Sie eine Verbindung mit dem virtuellen Computer her. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird][logon].

2. Öffnen Sie den **Server-Manager**, nachdem Sie sich auf dem virtuellen Computer angemeldet haben. Klicken Sie im linken Bereich auf **Datei- und Speicherdienste**.

	![Server-Manager öffnen](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. Erweitern Sie das Menü und wählen Sie **Datenträger**.

4. Der Abschnitt **Datenträger** listet Datenträger 0, Datenträger 1 und Datenträger 2 auf. Datenträger 0 ist der Betriebssystem-Datenträger. Bei Datenträger 1 handelt es sich um einen temporären Datenträger (der nicht für die Datenspeicherung verwendet werden sollte), und Datenträger 2 ist der Datenträger, den Sie an den virtuellen Computer angefügt haben. Der Datenträger hat eine Kapazität von 5 GB, je nachdem, was Sie beim Anfügen des Datenträgers angegeben haben. Klicken Sie mit der rechten Maustaste auf Datenträger 2, und wählen Sie **Initialisieren** aus.

5.	Sie werden darüber informiert, dass alle Daten gelöscht werden, wenn der Datenträger initialisiert wird. Klicken Sie auf **Ja**, um die Warnung zu bestätigen und den Datenträger zu initialisieren. Klicken Sie dann mit der rechten Maustaste auf Disk 2, und wählen Sie **Neues Volume** aus.

6.	Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Sobald der Assistent fertig ist, wird ein neues Volume im Bereich **Volumes** aufgelistet. Der Datenträger ist jetzt online und zum Speichern von Daten bereit.

	![Volume erfolgreich initialisiert](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE]Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).

## Zusätzliche Ressourcen

[Trennen eines Datenträgers von einem virtuellen Computer unter Windows](storage-windows-detach-disk.md)

[Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

<!---HONumber=July15_HO4-->