<properties
	pageTitle="Festlegen der Azure Backup-Speicherredundanzoptionen | Microsoft Azure"
	description="Lernen Sie den Unterschied zwischen georedundantem Speicher und lokal redundantem Speicher kennen, um eine für Sie geeignete Azure Backup-Speicherredundanzoptionen auszuwählen."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="jimpark; markgal"/>


# Speicherredundanzoptionen für Azure Backup

Ihre Geschäftsanforderungen legen die entsprechenden Speicherredundanzoption für Azure Backup-Speicher fest. Wenn Sie Azure als primären Speicherendpunkt für die Sicherung verwenden (wenn Sie beispielsweise von einem Windows-Server in Azure sichern), sollten Sie die Option „Georedundanter Speicher“ in Betracht ziehen (Standardeinstellung). Bei Verwendung von Azure als tertiärem Speicherendpunkt (Sie verwenden beispielsweise SCDPM, um eine lokale Sicherungskopie vor Ort zu haben, und Azure für Ihre langfristigen Aufbewahrungsanforderungen), sollten Sie einen lokal redundanten Speicher auswählen.

## Georedundanter Speicher (GRS)

Georedundanter Speicher verwaltet sechs Kopien Ihrer Daten. Mit GRS werden Ihre Daten dreimal innerhalb der primären Region und dreimal in einer sekundären Region hunderte von Kilometern von der primären Region entfernt repliziert, wodurch höchste Stabilität erreicht wird. Im Fall eines Fehlers in der primären Region stellt Azure Backup durch das Speichern von Daten in GRS sicher, dass Ihre Daten dauerhaft in zwei verschiedenen Regionen aufbewahrt werden.

## Lokal redundanter Speicher (LRS)

Lokal redundanter Speicher verwaltet drei Kopien Ihrer Daten. LRS wird innerhalb eines einzelnen Standorts dreimal in einer einzelnen Region repliziert. LRS schützt Ihre Daten vor normalen Hardwareausfällen, jedoch nicht vor dem Ausfall einer ganzen Azure-Einrichtung. Dadurch werden die Kosten zum Speichern von Daten in Azure gesenkt, und es wird eine geringere Dauerhaftigkeit für Ihre Daten bereitgestellt, die möglicherweise für tertiäre Kopien ausreicht.

Sie können die entsprechende Option für Ihre Anforderungen über die Option **Konfigurieren** des Sicherungstresors auswählen.

## Nächste Schritte

- Bereiten Sie die Umgebung für die [Sicherung von Windows-Servern oder -Clients](backup-configure-vault.md) vor.
- Falls noch einige Fragen unbeantwortet sein sollten, hilft Ihnen das Thema [Azure Backup – Häufig gestellte Fragen](backup-azure-backup-faq.md) weiter.
- Besuchen Sie das [Azure Backup-Forum](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0211_2016-->