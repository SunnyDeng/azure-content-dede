<properties
	pageTitle="Gewusst wie: Erstellen, Verwalten oder Löschen eines Speicherkontos im Azure-Portal | Microsoft Azure"
	description="Erstellen Sie ein neues Speicherkonto, verwalten Sie die Zugriffsschlüssel für Ihr Konto, oder löschen Sie ein Speicherkonto im Azure-Portal. Erfahren Sie mehr über Standard- und Premium-Speicherkonten."
	services="storage"
	documentationCenter=""
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/14/2016"
	ms.author="robinsh"/>


# Informationen zu Azure-Speicherkonten

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## Übersicht

Ein Azure-Speicherkonto bietet Ihnen Zugriff auf die Azure Blob-, Warteschlangen-, Tabellen- und Dateidienste in Azure Storage. Ihr Speicherkonto stellt den eindeutigen Namespace für Ihre Azure Storage-Datenobjekte bereit. Standardmäßig sind die Daten in Ihrem Konto nur für Sie als Kontobesitzer verfügbar.

Zwei Typen von Speicherkonten stehen zur Verfügung:

- Ein Standardspeicherkonto umfasst Blob-, Tabellen- und Warteschlangenspeicher.
- Ein Storage Premium-Konto unterstützt aktuell ausschließlich Festplatten virtueller Azure-Computer. Eine ausführliche Übersicht über Azure Premium Storage finden Sie unter [Premium Storage: Hochleistungsspeicher für Workloads auf virtuellen Azure-Maschinen](storage-premium-storage.md).

## Speicherkontoabrechnung

Die Rechnungsstellung für Azure-Speicher basiert auf Ihrem Speicherkonto. Speicherkosten basieren auf vier Faktoren: Speicherkapazität, Replikationsschema, Speichertransaktionen und Datenausgang.

- Speicherkapazität bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten wird dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden.
- Die Replikation bestimmt, wie viele Kopien Ihrer Daten gleichzeitig unterhalten werden und an welchen Standorten.
- Transaktionen beziehen sich auf alle Lese- und Schreibvorgänge im Azure-Speicher.
- Datenausgang bezieht sich auf Daten, die aus einer Azure-Region übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen durchführen, um Daten und Dienste in den gleichen Rechenzentren zu gruppieren und so Datenausgangsgebühren zu reduzieren oder zu eliminieren.)  

Die Seite [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage) bietet detaillierte Preisinformationen für Speicherkapazität, Replikation und Transaktionen. Auf der Seite [Datenübertragungen – Preisübersicht](https://azure.microsoft.com/pricing/details/data-transfers/) finden Sie detaillierte Preisinformationen für den Datenausgang.

Weitere Informationen zu Kapazität und Leistungszielen von Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](storage-scalability-targets.md).

> [AZURE.NOTE] Wenn Sie einen virtuellen Azure-Computer erstellen, wird für Sie automatisch ein Speicherkonto in der Bereitstellungsregion erstellt, falls Sie noch kein Speicherkonto in der entsprechenden Region haben. Es ist nicht erforderlich, den unten aufgeführten Schritten zum Anlegen eines Speicherkontos für die Festplatten Ihres virtuelles Computers zu folgen. Der Name des Speicherkontos basiert auf dem Namen des virtuellen Computers. In der [Dokumentation zu Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) finden Sie weitere Einzelheiten.

## Speicherkontoendpunkte

Jedes Objekt, das Sie in Azure Storage speichern, verfügt über eine eindeutige URL-Adresse. Der Name des Speicherkontos bildet die Unterdomäne dieser Adresse. Die Kombination aus Unterdomäne und Domänenname, die für jeden Dienst spezifisch ist, bildet einen *Endpunkt* für Ihr Speicherkonto.

Wenn Ihr Speicherkonto beispielsweise *meinspeicherkonto* heißt, dann sind die Standardendpunkte für Ihr Speicherkonto wie folgt:

- Blob-Dienst: http://*mystorageaccount*.blob.core.windows.net

- Tabellendienst: http://*mystorageaccount*.table.core.windows.net

- Warteschlangendienst: http://*mystorageaccount*.queue.core.windows.net

- Dateidienst: http://*mystorageaccount*.file.core.windows.net

Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt generiert. Eine Blob-Adresse kann beispielsweise folgendes Format haben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*

Sie können auch einen benutzerdefinierten Domänennamen konfigurieren, den Sie mit Ihrem Speicherkonto verwenden. Details für klassische Speicherkonten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihren Blob Storage-Endpunkt](storage-custom-domain-name.md). Für ARM-Speicherkonten wurde diese Funktion im [Azure-Portal](https://portal.azure.com) noch nicht hinzugefügt, Sie können jedoch PowerShell für die Konfiguration verwenden. Weitere Informationen finden Sie unter dem Cmdlet [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx).

## Erstellen Sie ein Speicherkonto.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im Menü „Hub“ die Option **Neu** -> **Daten und Speicher** -> **Speicherkonto** aus.

3. Wählen Sie ein Bereitstellungsmodell aus: **Ressourcen-Manager** oder **Klassisch**. **Ressourcen-Manager** ist das empfohlene Bereitstellungsmodell. Weitere Informationen finden Sie unter [Grundlegendes zur Bereitstellung über den Ressourcen-Manager im Vergleich zur klassischen Bereitstellung](../resource-manager-deployment-model.md).

4. Geben Sie einen Namen für Ihr Speicherkonto ein.

	> [AZURE.NOTE] Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten.
	>  
	> Der Name Ihres Speicherkontos muss innerhalb von Azure eindeutig sein. Im Azure-Portal wird angezeigt, ob der von Ihnen ausgewählte Speicherkontoname bereits vergeben ist.

	Unter [Speicherkontoendpunkte](#storage-account-endpoints) (siehe unten) finden Sie Details dazu, wie der Name des Speicherkontos zum Adressieren Ihrer Objekte in Azure Storage verwendet wird.

5. Geben Sie den Typ des zu erstellenden Speicherkontos an. Der Speicherkontotyp bestimmt, wie das Speicherkonto repliziert wird, und gibt an, ob es sich um ein Standardspeicherkonto oder ein Premium-Speicherkonto handelt.

	Der Standardtyp für ein Speicherkonto ist **Standard-RAGRS**. Dies ist ein Standardspeicherkonto mit schreibgeschützter georedundanter Replikation. Dieser Speicherkontotyp wird in eine sekundäre Region repliziert, die weit von der primären Region entfernt ist, und bietet Lesezugriff auf den sekundären Standort.

	Weitere Details zu den Replikationsoptionen für Azure Storage finden Sie unter [Azure Storage-Replikation](storage-redundancy.md). Weitere Informationen zu Standard- und Premium-Speicherkonten finden Sie unter [Einführung in Microsoft Azure Storage](storage-introduction.md) und [Premium-Speicher: Hochleistungsspeicher für Arbeitslasten auf virtuellen Azure-Computern](storage-premium-storage.md)

6. Geben Sie an, ob Sie für die Diagnose Ihr Speicherkonto aktivieren möchten. Zur Diagnose gehören Protokollierung und Metriken der Speicheranalyse.

7. Wenn Sie mehr als ein Azure-Abonnement besitzen, wird das Feld **Abonnement** angezeigt. Wählen Sie das Abonnement aus, in dem Sie das neue Speicherkonto erstellen möchten.

8. Geben Sie eine neue Ressourcengruppe an, oder wählen Sie eine vorhandene Ressourcengruppe aus. Weitere Informationen zu Ressourcengruppen finden Sie unter [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen](../azure-portal/resource-group-portal.md).

9. Wählen Sie den geografischen Standort für das Speicherkonto aus.

10. Klicken Sie auf **Erstellen**, um das Speicherkonto zu erstellen.

## Verwalten von Speicherzugriffsschlüsseln

Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

> [AZURE.NOTE] Sie sollten das Weitergeben von Speicherzugriffsschlüsseln an andere vermeiden. Um den Zugriff auf Speicherressourcen zu gewähren, ohne den Zugriffsschlüssel weiterzugeben, verwenden Sie eine *Shared Access Signature*. Eine Shared Access Signature (SAS) bietet Zugriff auf eine Ressource in Ihrem Konto für ein von Ihnen definiertes Zeitintervall und mit den von Ihnen festgelegten Berechtigungen. Weitere Informationen finden Sie unter [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md).

### Anzeigen und Kopieren von Speicherzugriffsschlüssel

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto, und klicken Sie auf das Symbol **Schlüssel**, um Kontozugriffsschlüssel anzuzeigen, zu kopieren und erneut zu generieren. Das Blatt **Zugriffsschlüssel** enthält auch vorkonfigurierte Verbindungszeichenfolgen, in denen Ihre primären und sekundären Schlüssel verwendet werden, die Sie zur Nutzung in der Anwendung kopieren können.

### Erneutes Generieren von Speicherzugriffsschlüsseln

Sie sollten die Zugriffsschlüssel für Ihr Speicherkonto regelmäßig ändern, um dafür zu sorgen, dass Ihre Speicherverbindungen sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, um es Ihnen zu ermöglichen, Verbindungen zum Speicherkonto mit einem Zugriffsschlüssel aufrecht zu erhalten, während Sie den anderen Zugriffsschlüssel neu generieren.

> [AZURE.WARNING] Das erneute Generieren der Zugriffsschlüssel wirkt sich auf virtuelle Computer, Mediendienste und alle Anwendungen aus, die vom Speicherkonto abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das Speicherkonto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.

**Virtuelle Computer**: Falls Ihr Speicherkonto virtuelle Computer enthält, die ausgeführt werden, müssen Sie alle virtuellen Computer nach dem erneuten Generieren der Zugriffsschlüssel neu bereitstellen. Um die erneute Bereitstellung zu vermeiden, beenden Sie die virtuellen Computer, bevor Sie die Zugriffsschlüssel neu generieren.

**Mediendienste**: Falls Sie Mediendienste haben, die auf Ihr Speicherkonto angewiesen sind, müssen Sie die Zugriffsschlüssel nach dem erneuten Generieren der Schlüssel mit Ihrem Mediendienst erneut synchronisieren.

**Anwendungen**: Falls Sie Webanwendungen oder Clouddienste haben, die das Speicherkonto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln – es sei denn, Sie führen einen Rollup für die Schlüssel aus.

Mit dem folgenden Prozess wechseln Sie Ihre Speicherzugriffsschlüssel:

1. Aktualisieren Sie die Verbindungszeichenfolgen im Anwendungscode, damit sie auf den sekundären Zugriffsschlüssel des Speicherkontos verweisen.

2. Generieren Sie den primären Zugriffsschlüssel für das Speicherkonto neu. Klicken Sie auf dem Blatt **Zugriffsschlüssel** auf **Schlüssel 1 erneut generieren** und dann auf **Ja**, um das Generieren eines neuen Schlüssels zu bestätigen.

3. Aktualisieren Sie die Verbindungszeichenfolgen in Ihrem Code, um auf den neuen primären Zugriffsschlüssel zu verweisen.

4. Generieren Sie den sekundären Zugriffsschlüssel auf die gleiche Weise neu.

## Löschen eines Speicherkontos

Um ein Speicherkonto zu entfernen, das Sie nicht mehr verwenden, wechseln Sie im [Azure-Portal](https://portal.azure.com) zu dem Speicherkonto , und klicken Sie auf **Löschen**. Durch das Löschen eines Speicherkontos wird das gesamte Konto gelöscht, einschließlich aller Daten im Konto.

> [AZURE.WARNING] Es ist nicht möglich, ein gelöschtes Speicherkonto wiederherzustellen oder Inhalte abzurufen, die das Konto vor dem Löschen enthielt. Sichern Sie alle Inhalte, die Sie speichern möchten, bevor Sie das Konto löschen. Dies gilt auch für alle Ressourcen im Konto – gelöschte Blobs, Tabellen, Warteschlangen oder Dateien können nicht wiederhergestellt werden.

Um ein Speicherkonto löschen zu können, das einer virtuellen Azure-Maschine zugeordnet ist, müssen Sie zuerst sicherstellen, dass alle Datenträger der virtuellen Maschine gelöscht wurden. Wenn Sie die Datenträger der virtuellen Maschine vorher nicht löschen, wird beim Versuch, das Speicherkonto zu löschen, eine Fehlermeldung der folgenden Art angezeigt:

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Wenn das Speicherkonto das klassische Bereitstellungsmodell verwendet, können Sie den Datenträger der virtuellen Maschine entfernen, indem Sie folgende Schritte im [klassischen Azure-Portal](https://manage.windowsazure.com) ausführen:

1. Navigieren Sie zum [klassischen Azure-Portal](https://manage.windowsazure.com).
2. Navigieren Sie zur Registerkarte „Virtuelle Computer“.
3. Klicken Sie auf die Registerkarte „Datenträger“.
4. Wählen Sie Ihren Datenträger aus, und klicken Sie dann auf „Datenträger löschen“.
5. Um Datenträgerimages zu löschen, navigieren Sie zur Registerkarte „Images“. Löschen Sie alle unter dem Konto gespeicherten Images.

Weitere Informationen finden Sie unter [Dokumentation zu virtuellen Maschinen](http://azure.microsoft.com/documentation/services/virtual-machines/).

## Nächste Schritte

- [Azure Storage-Replikation](storage-redundancy.md)
- [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](storage-configure-connection-string.md)
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)
- Besuchen Sie den [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/).

<!---HONumber=AcomDC_0218_2016-->