<properties 
	pageTitle="Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen" 
	description="Eine Anleitung für die Abrechnung von Azure Storage-BLOB-Momentaufnahmen" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen

Durch das Erstellen einer Momentaufnahme, die eine schreibgeschützte Kopie eines BLOBs darstellt, können auf Ihrem Konto zusätzliche Gebühren für die Datenspeicherung anfallen. Wenn Sie beim Entwurf Ihrer Anwendung die Art und Weise der Ermittlung dieser Gebühren berücksichtigen, können Sie unnötige Kosten minimieren.

##Wichtige Überlegungen zur Abrechnung

Die folgende Liste enthält wichtige Punkte, die beim Erstellen einer Momentaufnahme zu berücksichtigen sind.

- Gebühren werden für eindeutige Blöcke oder Seiten erhoben, die im BLOB oder in der Momentaufnahme enthalten sind. Ihrem Konto werden erst dann zusätzliche Gebühren für Momentaufnahmen angerechnet, die einem BLOB zugeordnet sind, wenn Sie das BLOB aktualisieren, auf dem sie basieren. Sobald Sie das Basis-BLOB aktualisieren, entstehen Abweichungen von den zugeordneten Momentaufnahmen, und es werden für alle eindeutigen Blöcke oder Seiten in jedem BLOB und in jeder Momentaufnahme Gebühren berechnet.

- Wenn Sie einen Block innerhalb eines Block-BLOBs ersetzen, wird dieser Block anschließend als eindeutiger Block berechnet. Dies gilt auch, wenn der Block dieselbe Block-ID und dieselben Daten enthält wie in der Momentaufnahme. Sobald ein erneuter Commit für den Block ausgeführt wird, weicht er von seinem Pendant in den Momentaufnahmen ab, und Ihnen werden die Daten des Blocks berechnet. Das gleiche gilt für eine Seite in einem Seiten-BLOB, die mit identischen Daten aktualisiert wird.

- Wenn Sie einen Blockblob durch einen Aufruf der Methode UploadFile, UploadText, UploadStream oder UploadByteArray ersetzen, werden alle Blöcke im betreffenden BLOB ersetzt. Wenn dem BLOB eine Momentaufnahme zugeordnet ist, weisen anschließend alle Blöcke im Basis-BLOB und in der Momentaufnahme Abweichungen auf, und Ihnen werden Gebühren für alle Blöcke in beiden BLOBs berechnet. Dies gilt auch, wenn die Daten im Basis-BLOB und in der Momentaufnahme identisch sind.

- Der Azure-Blob-Dienst kann nicht feststellen, ob zwei Blöcke identische Daten enthalten. Jeder hochgeladene Block, für den ein Commit ausgeführt wird, wird als eindeutig behandelt, selbst wenn die enthaltenen Daten und die Block-ID identisch sind. Da Gebühren jeweils für eindeutige Blöcke berechnet werden, ist zu berücksichtigen, dass beim Aktualisieren eines BLOBs mit einer zugeordneten Momentaufnahme zusätzliche eindeutige Blöcke generiert werden, für die zusätzliche Gebühren entstehen.

> [AZURE.NOTE] Planen und verwalten Sie Momentaufnahmen sorgfältig, um unnötige Gebühren zu vermeiden. Es wird empfohlen, Momentaufnahmen folgendermaßen zu verwalten:

> - Löschen und erstellen Sie zugehörige Momentaufnahmen für ein BLOB neu, wenn Sie das BLOB aktualisieren, selbst wenn Sie mit identischen Daten aktualisieren, es sei denn, der Anwendungsentwurf erfordert, dass die Momentaufnahmen beibehalten werden. Durch das Löschen und Neuerstellen der Momentaufnahmen für ein BLOB können Sie sicherstellen, dass das BLOB und die Momentaufnahmen nicht voneinander abweichen.

> - Wenn Sie Momentaufnahmen für ein BLOB behalten, vermeiden Sie Aufrufe von UploadFile, UploadText, UploadStream und UploadByteArray zum Aktualisieren von BLOBs, da diese Methoden alle Blöcke im BLOB ersetzen. Aktualisieren Sie stattdessen so wenig Blöcke wie möglich, indem Sie die PutBlock-Methode oder die PutBlockList-Methode aufrufen.


##Abrechnungsszenarien für Momentaufnahmen


Die folgenden Szenarien veranschaulichen, wie Gebühren für ein Block-BLOB und zugehörige Momentaufnahmen berechnet werden. 

In Szenario 1 wurde das Basis-BLOB nicht aktualisiert, seit die Momentaufnahme erstellt wurde, sodass Gebühren nur für die eindeutigen Blöcke 1, 2 und 3 berechnet werden:

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

In Szenario 2 wurde das Basis-BLOB aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde aktualisiert. Obwohl er die gleichen Daten und dieselbe ID enthält, ist er nicht identisch mit dem Block 3 der Momentaufnahme. Daher wird das Konto mit Gebühren für vier Blöcke belastet:

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

In Szenario 3 wurde das Basis-BLOB aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde im Basis-BLOB durch Block 4 ersetzt, die Momentaufnahme enthält aber immer noch den Block 3. Daher wird das Konto mit Gebühren für vier Blöcke belastet:
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

In Szenario 4 wurde das Basis-BLOB vollständig aktualisiert und enthält keinen der ursprünglichen Blöcke. Daher wird das Konto für alle acht eindeutigen Blöcke belastet. Dieses Szenario kann auftreten, wenn Sie eine der Updatemethoden UploadFile, UploadText, UploadFromStream oder UploadByteArray verwenden, da diese Methoden alle Inhalte eines BLOBs ersetzen.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
