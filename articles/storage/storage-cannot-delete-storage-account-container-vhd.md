<properties
	pageTitle="Problembehandlung für das Löschen von Azure-Speicherkonten, -Containern oder -VHDs | Microsoft Azure"
	description="Problembehandlung für das Löschen von Azure-Speicherkonten, -Containern oder -VHDs"
	services="storage"
	documentationCenter=""
	authors="genlin"
	manager="felixwu"
	editor=""
	tags="storage"/>

<tags
	ms.service="storage"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/20/2016"
	ms.author="genli"/>

# Problembehandlung für das Löschen von Azure-Speicherkonten, -Containern oder -VHDs

## Zusammenfassung
Möglicherweise treten Fehler beim Löschen von Azure-Speicherkonten, -Containern oder -VHDs im [Azure-Portal](https://portal.azure.com/) oder im [klassischen Azure-Portal](https://manage.windowsazure.com/) auf. Die Probleme können folgende Ursachen haben:

-	Wenn Sie einen virtuellen Computer löschen, werden der Datenträger und die VHD nicht automatisch gelöscht. Dies ist möglicherweise die Ursache für Fehler beim Löschen von Speicherkonten. Der Datenträger wird nicht gelöscht, damit Sie den Datenträger verwenden können, um einen anderen virtuellen Computer bereitzustellen.

-	Es besteht noch eine Lease für einen Datenträger oder für das Blob, das dem Datenträger zugeordnet ist.

Suchen Sie in den Azure-Foren zu [MSDN und Stack Overflow](https://azure.microsoft.com/support/forums/), falls Sie Ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können Ihr Problem in diesen Foren veröffentlichen oder auf Twitter an den @AzureSupport senden. Darüber hinaus können Sie eine Azure-Supportanfrage stellen, indem Sie auf der Website des [Azure-Supports](https://azure.microsoft.com/support/options/) die Option **Support erhalten** auswählen.

## Lösung
Um die häufigsten Probleme zu beheben, verwenden Sie die folgende Methode:

1. Wechseln Sie zum [klassischen Azure-Portal](https://manage.windowsazure.com/).
2. Wählen Sie **VIRTUELLER COMPUTER** > **DATENTRÄGER** aus.

	![disk.png](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Suchen Sie die Datenträger, die dem zu löschenden Speicherkonto oder Container bzw. der zu löschenden VHD zugeordnet sind. Überprüfen Sie den Speicherort des Datenträgers, um das zugeordnete Speicherkonto, den Container und die VHD zu finden.

	![location](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Stellen Sie sicher, dass kein virtueller Computer im Feld **Angeschlossen an** der Datenträger aufgeführt ist, und löschen Sie dann die Datenträger.

 	> [AZURE.NOTE] Wenn ein Datenträger mit einem virtuellen Computer verbunden ist, können Sie ihn nicht löschen. Datenträger werden asynchron von einem gelöschten virtuellen Computer getrennt. Möglicherweise dauert es einige Minuten nach dem Löschen des virtuellen Computers, bis dieses Feld leer ist.

5. Wählen Sie **VIRTUELLER COMPUTER** > **IMAGES** aus, und löschen Sie die Images, die dem Speicherkonto, dem Container oder der VHD zugeordnet sind.

Versuchen Sie danach erneut, das Speicherkonto, den Container oder die VHD zu löschen.

> [AZURE.WARNING] Sichern Sie alle Inhalte, die Sie speichern möchten, bevor Sie das Konto löschen. Es ist nicht möglich, ein gelöschtes Speicherkonto wiederherzustellen oder Inhalte abzurufen, die das Konto vor dem Löschen enthielt. Dies gilt auch für alle Ressourcen im Konto – gelöschte VHDs, Blobs, Tabellen, Warteschlangen oder Dateien können nicht wiederhergestellt werden. Stellen Sie sicher, dass die Ressource nicht verwendet wird.

## Symptom

Im folgenden Abschnitt werden häufige Fehler aufgeführt, die möglicherweise beim Löschen von Azure-Speicherkonten, -Containern oder -VHDs auftreten können:

### Szenario 1: Speicherkonto kann nicht gelöscht werden

Wenn Sie versuchen, ein nicht mehr genutztes Speicherkonto zu löschen, indem Sie im [Azure-Portal](https://portal.azure.com/) oder im [klassischen Azure-Portal](https://manage.windowsazure.com/) zum Speicherkonto navigieren und „Löschen“ auswählen, kann die folgende Fehlermeldung angezeigt werden:

**Im Azure-Portal**:

*Speicherkonto <vm-storage-account-name> konnte nicht gelöscht werden. Speicherkonto <vm-storage-account-name> kann nicht gelöscht werden: „Das Speicherkonto <vm-storage-account-name> hat einige aktive Bilder und/oder Datenträger. Entfernen Sie diese Bilder und/oder Datenträger, bevor Sie dieses Speicherkonto löschen.“*

**Im klassischen Azure-Portal**:

*Das Speicherkonto <vm-storage-account-name> hat einige aktive Bilder und/oder Datenträger, z. B. „xxxxxxxxx- xxxxxxxxx-O-209490240936090599“. Entfernen Sie diese Bilder und/oder Datenträger, bevor Sie dieses Speicherkonto löschen.*

Es kann auch der folgende Fehler angezeigt werden:

**Im Azure-Portal**:

*Das Speicherkonto <vm-storage-account-name> verfügt über 1 Container, der/die über ein aktives Image und/oder Datenträgerartefakte verfügt/verfügen. Stellen Sie sicher, dass diese Artefakte aus dem Image-Repository entfernt werden, bevor Sie dieses Speicherkonto löschen.*

**Im klassischen Azure-Portal**:

*Übermitteln fehlgeschlagen. Das Speicherkonto <vm-storage-account-name> verfügt über 1 Container, der/die über ein aktives Image und/oder Datenträgerartefakte verfügt/verfügen. Stellen Sie sicher, dass diese Artefakte aus dem Image-Repository entfernt werden, bevor Sie dieses Speicherkonto löschen. Wenn Sie versuchen, ein Speicherkonto zu löschen, und dem Speicherkonto aktive Datenträger zugeordnet sind, werden Sie mit einer Meldung darüber informiert, dass aktive Datenträger vorhanden sind, die gelöscht werden müssen.*

### Szenario 2: Container kann nicht gelöscht werden

Wenn Sie versuchen, den Speichercontainer zu löschen, kann die folgende Fehlermeldung angezeigt werden:

*Der Speichercontainer <container name> konnte nicht gelöscht werden. Fehler: Derzeit ist eine Lease für den Container vorhanden, und in der Anforderung wurde keine Lease-ID angegeben.*

### Szenario 3: VHD kann nicht gelöscht werden

Sie versuchen, nach dem Löschen eines virtuellen Computers die Blobs für die zugeordneten VHDs zu löschen, und die folgende Meldung wird angezeigt:

*Fehler beim Löschen von Blob „Pfad/XXXXXX-XXXXXX-os-1447379084699.vhd“. Fehler: Derzeit ist eine Lease für das Blob vorhanden, und in der Anforderung wurde keine Lease-ID angegeben.*

## Weitere Informationen

Virtuelle V1-Computer, die beibehalten wurden, werden im [Azure-Portal](https://portal.azure.com/) oder im [klassischen Azure-Portal](https://manage.windowsazure.com/) mit dem Status „Beendet (Zuordnung aufgehoben)“ angezeigt.

**Klassisches Azure-Portal**:

![Screenshot 1](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

**Azure-Portal**:

![Screenshot 2](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

Der Status „Beendet (Zuordnung aufgehoben)“ gibt die Computeressourcen wie CPU, Speicher und Netzwerk frei, aber die Datenträger werden weiterhin für den Benutzer reserviert, damit er den virtuellen Computer bei Bedarf schnell neu erstellen kann. Diese Datenträger werden auf den VHDs erstellt, die vom Azure-Speicher unterstützt werden. Diese VHDs gehören zum Speicherkonto, und die Datenträger verfügen über Leases für diese VHDs.

## Referenzen

- [Löschen eines Speicherkontos](storage-create-storage-account.md#delete-a-storage-account)
- [How to break the locked lease of blob storage in Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492) (in englischer Sprache)

<!---HONumber=AcomDC_0323_2016-->