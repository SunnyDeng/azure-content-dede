<properties
	pageTitle="Anfügen eines Datenträgers | Microsoft Azure"
	description="Hier erhalten Sie Informationen zum Anfügen von neuen oder vorhandenen Datenträgern an einen virtuellen Computer im Azure-Vorschauportal mit dem Ressourcen-Manager-Bereitstellungsmodell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="cynthn"/>

# Anfügen eines Datenträgers für Daten im Azure-Vorschauportal

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen einer Ressource mit dem Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, eine Ressource mit dem [klassischen Bereitstellungsmodell](storage-windows-attach-disk.md) zu erstellen.

In diesem Artikel wird beschrieben, wie Sie über das Azure-Vorschauportal neue und vorhandene Datenträger an einen virtuellen Computer anfügen können. Lesen Sie die folgenden Tipps, bevor Sie mit diesem Vorgang fortfahren:

- Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](virtual-machines-size-specs.md).
- Für die Verwendung von Premium-Speicher benötigen Sie einen virtuellen Computer der DS- oder GS-Serie. Sie können Datenträger aus Premium- und aus Standard-Speicherkonten für diese virtuellen Computer verwenden. Premium-Speicher ist in bestimmten Regionen verfügbar. Nähere Informationen finden Sie unter [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](../storage/storage-premium-storage-preview-portal.md).
- Bei Datenträgern, die an virtuelle Computer angefügt werden, handelt es sich um VHD-Dateien in einem Azure-Speicherkonto. Weitere Informationen finden Sie unter [Informationen zu Datenträgern und VHDs für virtuelle Computer](virtual-machines-disks-vhds.md).
- Neue Datenträger werden von Azure beim Anfügen automatisch erstellt. Sie müssen den Datenträger also nicht manuell erstellen.
- Bei einem bereits vorhandenen Datenträger muss die VHD-Datei in einem Azure-Speicherkonto verfügbar sein. Sie können eine bereits vorhandene Datei verwenden, die noch nicht an einen anderen virtuellen Computer angefügt wurde, oder eine eigene VHD-Datei in das Speicherkonto hochladen.

## Suchen des virtuellen Computers

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.

2. Klicken Sie im Menü "Hub" auf **Durchsuchen**.

3. Klicken Sie auf das Blatt "Suchen", führen Sie einen Bildlauf nach unten durch, und klicken Sie auf **Virtuelle Computer**.

	![Suchen nach virtuellen Computern](./media/virtual-machines-attach-disk-preview/search-blade-preview-portal.png)

4.	Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.

5. Klicken Sie rechts unter **Zusammenfassung** auf **Alle Einstellungen**, und klicken Sie anschließend auf **Datenträger**.

	![Öffnen der Datenträgereinstellungen](./media/virtual-machines-attach-disk-preview/find-disk-settings.png)

Folgen Sie den Anweisungen zum Anfügen eines neuen oder eines vorhandenen Datenträgers.

## Option 1: Anfügen eines neuen Datenträgers

1.	Klicken Sie auf dem Blatt **Datenträger** auf **Neuen anfügen**.

2.	Überprüfen und aktualisieren Sie nach Bedarf die Standardeinstellungen, und klicken Sie anschließend auf **OK**.

 	![Überprüfen der Datenträgereinstellungen](./media/virtual-machines-attach-disk-preview/attach-new.png)

3.	Nachdem der Datenträger von Azure erstellt und an den virtuellen Computer angefügt wurde, wird der neue Datenträger in den Datenträgereinstellungen des virtuellen Computers unter **Datenträger** angezeigt.

## Option 2: Anfügen eines vorhandenen Datenträgers

1.	Klicken Sie auf dem Blatt **Datenträger** auf **Vorhandenen anfügen**.

2.	Klicken Sie unter **Vorhandenen Datenträger anfügen** auf **VHD-Datei**.

	![Anfügen eines vorhandenen Datenträgers](./media/virtual-machines-attach-disk-preview/attach-existing.png)

3.	Wählen Sie unter **Speicherkonten** das Konto und den Container aus, der die VHD-Datei enthält.

	![Suchen nach VHD-Dateien](./media/virtual-machines-attach-disk-preview/find-storage-container.png)

4.	Wählen Sie die VHD-Datei aus.

5.	Die ausgewählte Datei wird unter **Vorhandenen Datenträger anfügen** und dort unter **VHD-Datei** angezeigt. Klicken Sie auf **OK**.

6.	Nachdem der Datenträger von Azure an den virtuellen Computer angefügt wurde, wird er in den Datenträgereinstellungen des virtuellen Computers unter **Datenträger** angezeigt.

## Nächste Schritte

Nach dem Hinzufügen des Datenträgers müssen Sie ihn für die Verwendung im Betriebssystem des virtuellen Computers vorbereiten:

- Informationen für Linux finden Sie unter "Gewusst wie: Initialisieren eines neuen Datenträgers unter Linux" in diesem [Artikel](virtual-machines-linux-how-to-attach-disk.md).
- Informationen für Windows finden Sie unter "Gewusst wie: Initialisieren eines neuen Datenträgers unter Windows Server" in diesem [Artikel](storage-windows-attach-disk.md).

## Zusätzliche Ressourcen

[Informationen zu Azure-Speicherkonten]

<!--Link references-->

[Informationen zu Azure-Speicherkonten]: ../storage-whatis-account/

<!---HONumber=Sept15_HO4-->