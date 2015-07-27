<properties 
	pageTitle="Erstellen einer Momentaufnahme eines Blobs" 
	description="Anleitung zum Erstellen von Momentaufnahmen von Azure Storage-Blobs" 
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
	ms.date="05/27/2015" 
	ms.author="tamram"/>

# Erstellen einer Momentaufnahme eines Blobs

## Übersicht

Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird. Momentaufnahmen sind nützlich, um Blobs zu sichern. Sobald eine Momentaufnahme erstellt wurde, kann sie gelesen, kopiert oder gelöscht, aber nicht mehr geändert werden.

Für den Namen der Momentaufnahme eines Blobs wird der Name des Basisblobs verwendet, aus dem die Momentaufnahme erstellt wurde, und zusätzlich ein **DateTime**-Wert angefügt, der den Zeitpunkt angibt, an dem die Momentaufnahme erstellt wurde. Wenn der Seitenblob-URI `http://storagesample.core.blob.windows.net/mydrives/myvhd` ist, lautet der Momentaufnahmen-URI z. B. `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`. Die Momentaufnahmen eines Blobs verwenden denselben URI und unterscheiden sich nur durch den angefügten **DateTime**-Wert.

Für ein Blob kann eine beliebige Anzahl von Momentaufnahmen vorhanden sein. Momentaufnahmen bleiben bestehen, bis sie explizit gelöscht werden, wobei eine Momentaufnahme jedoch nicht ohne das zugehörige Quellblob existieren kann. Sie können alle einem Blob zugeordneten Momentaufnahmen auflisten, um die aktuell vorhandenen Momentaufnahmen nachzuverfolgen.

Wenn Sie eine Momentaufnahme eines Blobs erstellen, werden seine Systemeigenschaften mit denselben Werten in die Momentaufnahme kopiert.

Dem Basis-Blob zugeordnete Leases wirken sich nicht auf die Momentaufnahme aus. Sie können für eine Momentaufnahme keine Lease abrufen.

## Kopieren von Momentaufnahmen 

Für Kopiervorgänge, die Blobs und Momentaufnahmen betreffen, gelten folgende Regeln:

- Sie können eine Momentaufnahme über das zugehörige Basis-Blob kopieren. Indem Sie eine Momentaufnahme zu einem Basis-Blob heraufstufen, können Sie eine frühere Version eines Blobs wiederherstellen. Die Momentaufnahme bleibt erhalten, ihre Quelle wird jedoch durch eine Kopie überschrieben, die gelesen und geschrieben werden kann.

- Sie können eine Momentaufnahme in ein Ziel-Blob mit einem anderen Namen kopieren. Das resultierende Ziel-Blob ist ein beschreibbares Blob und keine Momentaufnahme.

- Wenn ein Quell-Blob kopiert wird, werden ggf. vorhandene Momentaufnahmen des Quell-Blobs nicht mit in das Ziel kopiert. Wenn ein Ziel-Blob durch eine Kopie überschrieben wird, bleiben alle dem Ziel-Blob zugeordneten Momentaufnahmen unter dem Namen des Blobs erhalten.

- Wenn Sie eine Momentaufnahme eines Block-Blobs erstellen, wird die Liste der Blöcke mit ausgeführtem Commit für das Blob ebenfalls in die Momentaufnahme kopiert. Blöcke ohne ausgeführten Commit werden nicht kopiert.

## Angeben einer Zugriffsbedingung 

Sie können eine Zugriffsbedingung angeben, sodass die Momentaufnahme nur erstellt wird, wenn eine bestimmte Bedingung erfüllt ist. Um eine Zugriffsbedingung anzugeben, verwenden Sie die AccessCondition-Eigenschaft. Wenn die angegebene Bedingung nicht erfüllt ist, wird die Momentaufnahme nicht erstellt, und der Blob-Dienst gibt den Statuscode "HTTPStatusCode.PreconditionFailed" zurück.

## Löschen von Momentaufnahmen 

Ein Blob mit Momentaufnahmen kann nur dann gelöscht werden, wenn die Momentaufnahmen ebenfalls gelöscht werden. Sie können eine einzelne Momentaufnahme löschen oder dem Speicherdienst mitteilen, dass alle Momentaufnahmen beim Löschen des Quell-Blobs gelöscht werden sollen. Wenn Sie versuchen, ein Blob zu löschen, für das noch Momentaufnahmen vorhanden sind, gibt der Aufruf einen Fehler zurück.

## Momentaufnahmen mit Premium-Speicher
Die Verwendung von Momentaufnahmen mit Premium-Speicher unterliegt den folgenden Regeln:

- Die Anzahl der Momentaufnahmen pro Seitenblob in einem Premium-Speicherkonto ist auf 100 beschränkt. Wenn diese Grenze überschritten wird, gibt der Momentaufnahmenblob-Vorgang Fehlercode 409 ("SnapshotCountExceeded") zurück.

- Eine Momentaufnahme eines Seitenblobs in einem Premium-Speicherkonto kann alle zehn Minuten erstellt werden. Wenn diese Grenze überschritten wird, gibt der Momentaufnahmenblob-Vorgang Fehlercode 409 ("SnaphotOperationRateExceeded") zurück.

- Das Lesen von Momentaufnahmen eines Seitenblobs in einem Premium-Speicherkonto über "Get Blob" wird nicht unterstützt. Der Aufruf von "Get Blob" für eine Momentaufnahme in einem Storage Premium-Konto gibt Fehlercode 400 ("InvalidOperation") zurück. Der Aufruf von "Get Blob Properties" und "Get Blob Metadata" für eine Momentaufnahme wird hingegen unterstützt.

- Zum Lesen einer Momentaufnahme können Sie eine Momentaufnahme mithilfe des Copy Blob-Vorgangs in ein anderes Seitenblob im Konto kopieren. Das Ziel-Blob für den Kopiervorgang darf keine vorhandenen Momentaufnahmen besitzen. Wenn das Zielblob Momentaufnahmen aufweist, gibt "Copy Blob" Fehlercode 409 ("SnapshotsPresent") zurück.

## Zurückgeben des absoluten URI zu einer Momentaufnahme 

Dieses C#-Codebeispiel erstellt eine neue Momentaufnahme und schreibt den absoluten URI für den primären Standort.

    //Create the blob service client object.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();

    //Get a reference to a blob.
    CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
    blob.UploadText("This is a blob.");

    //Create a snapshot of the blob and write out its primary URI.
    CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
    Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);

## Grundlegendes zur Ermittlung der Gebühren für Momentaufnahmen

Durch das Erstellen einer Momentaufnahme, die eine schreibgeschützte Kopie eines BLOBs darstellt, können auf Ihrem Konto zusätzliche Gebühren für die Datenspeicherung anfallen. Wenn Sie beim Entwurf Ihrer Anwendung die Art und Weise der Ermittlung dieser Gebühren berücksichtigen, können Sie unnötige Kosten minimieren.

### Wichtige Überlegungen zur Abrechnung

Die folgende Liste enthält wichtige Punkte, die beim Erstellen einer Momentaufnahme zu berücksichtigen sind.

- Gebühren werden für eindeutige Blöcke oder Seiten erhoben, die im BLOB oder in der Momentaufnahme enthalten sind. Ihrem Konto werden erst dann zusätzliche Gebühren für Momentaufnahmen angerechnet, die einem BLOB zugeordnet sind, wenn Sie das BLOB aktualisieren, auf dem sie basieren. Sobald Sie das Basis-BLOB aktualisieren, entstehen Abweichungen von den zugeordneten Momentaufnahmen, und es werden für alle eindeutigen Blöcke oder Seiten in jedem BLOB und in jeder Momentaufnahme Gebühren berechnet.

- Wenn Sie einen Block innerhalb eines Block-BLOBs ersetzen, wird dieser Block anschließend als eindeutiger Block berechnet. Dies gilt auch, wenn der Block dieselbe Block-ID und dieselben Daten enthält wie in der Momentaufnahme. Sobald ein erneuter Commit für den Block ausgeführt wird, weicht er von seinem Pendant in den Momentaufnahmen ab, und Ihnen werden die Daten des Blocks berechnet. Das gleiche gilt für eine Seite in einem Seiten-BLOB, die mit identischen Daten aktualisiert wird.

- Wenn Sie einen Blockblob durch einen Aufruf der Methode UploadFile, UploadText, UploadStream oder UploadByteArray ersetzen, werden alle Blöcke im betreffenden BLOB ersetzt. Wenn dem BLOB eine Momentaufnahme zugeordnet ist, weisen anschließend alle Blöcke im Basis-BLOB und in der Momentaufnahme Abweichungen auf, und Ihnen werden Gebühren für alle Blöcke in beiden BLOBs berechnet. Dies gilt auch, wenn die Daten im Basis-BLOB und in der Momentaufnahme identisch sind.

- Der Azure-Blob-Dienst kann nicht feststellen, ob zwei Blöcke identische Daten enthalten. Jeder hochgeladene Block, für den ein Commit ausgeführt wird, wird als eindeutig behandelt, selbst wenn die enthaltenen Daten und die Block-ID identisch sind. Da Gebühren jeweils für eindeutige Blöcke berechnet werden, ist zu berücksichtigen, dass beim Aktualisieren eines BLOBs mit einer zugeordneten Momentaufnahme zusätzliche eindeutige Blöcke generiert werden, für die zusätzliche Gebühren entstehen.

> [AZURE.NOTE]Planen und verwalten Sie Momentaufnahmen sorgfältig, um unnötige Gebühren zu vermeiden. Es wird empfohlen, Momentaufnahmen folgendermaßen zu verwalten:

> - Löschen und erstellen Sie zugehörige Momentaufnahmen für ein BLOB neu, wenn Sie das BLOB aktualisieren, selbst wenn Sie mit identischen Daten aktualisieren, es sei denn, der Anwendungsentwurf erfordert, dass die Momentaufnahmen beibehalten werden. Durch das Löschen und Neuerstellen der Momentaufnahmen für ein BLOB können Sie sicherstellen, dass das BLOB und die Momentaufnahmen nicht voneinander abweichen.

> - Wenn Sie Momentaufnahmen für ein BLOB behalten, vermeiden Sie Aufrufe von UploadFile, UploadText, UploadStream und UploadByteArray zum Aktualisieren von BLOBs, da diese Methoden alle Blöcke im BLOB ersetzen. Aktualisieren Sie stattdessen so wenig Blöcke wie möglich, indem Sie die PutBlock-Methode oder die PutBlockList-Methode aufrufen.


### Abrechnungsszenarien für Momentaufnahmen


Die folgenden Szenarien veranschaulichen, wie Gebühren für ein Block-BLOB und zugehörige Momentaufnahmen berechnet werden.

In Szenario 1 wurde das Basis-BLOB nicht aktualisiert, seit die Momentaufnahme erstellt wurde, sodass Gebühren nur für die eindeutigen Blöcke 1, 2 und 3 berechnet werden:

![Azure-Speicherressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

In Szenario 2 wurde das Basis-BLOB aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde aktualisiert. Obwohl er die gleichen Daten und dieselbe ID enthält, ist er nicht identisch mit dem Block 3 der Momentaufnahme. Daher wird das Konto mit Gebühren für vier Blöcke belastet:

![Azure-Speicherressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

In Szenario 3 wurde das Basisblob aktualisiert, die Momentaufnahme jedoch nicht. Block 3 wurde im Basis-BLOB durch Block 4 ersetzt, die Momentaufnahme enthält aber immer noch den Block 3. Daher wird das Konto mit Gebühren für vier Blöcke belastet:
 
![Azure-Speicherressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

In Szenario 4 wurde das Basis-BLOB vollständig aktualisiert und enthält keinen der ursprünglichen Blöcke. Daher wird das Konto für alle acht eindeutigen Blöcke belastet. Dieses Szenario kann auftreten, wenn Sie eine der Updatemethoden UploadFile, UploadText, UploadFromStream oder UploadByteArray verwenden, da diese Methoden alle Inhalte eines BLOBs ersetzen.

![Azure-Speicherressourcen](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

<!---HONumber=July15_HO3-->