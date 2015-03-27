<properties 
	pageTitle="Erstellen einer Momentaufnahme eines Blobs" 
	description="Eine Anleitung zum Erstellen von Momentaufnahmen von Azure Storage-Blobs" 
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

#Erstellen einer Momentaufnahme eines Blobs

Sie können eine Momentaufnahme eines Blobs erstellen. Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird. Nachdem eine Momentaufnahme erstellt wurde, kann sie zwar gelesen, kopiert oder gelöscht, aber nicht geändert werden. Momentaufnahmen bieten eine Möglichkeit, um ein Blob so zu sichern, wie es zu einem bestimmten Zeitpunkt dargestellt wird.

Für den Namen der Momentaufnahme eines Blobs wird der Name des Basis-Blobs verwendet, aus dem die Momentaufnahme erstellt wurde, und zusätzlich ein DateTime-Wert angefügt, der den Zeitpunkt angibt, an dem die Momentaufnahme erstellt wurde. Für den Seitenblob-URI http://storagesample.core.blob.windows.net/mydrives/myvhd kann der URI der Momentaufnahme beispielsweise http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z lauten. Über diesen Wert kann bei anschließenden Vorgängen auf die Momentaufnahme verwiesen werden. Die Momentaufnahmen eines Blobs verwenden denselben URI und unterscheiden sich nur durch diesen DateTime-Wert. Im Clientbibliothekscode gibt die Snapshot-Eigenschaft des Blobs einen DateTime-Wert zurück, der die Momentaufnahme in Bezug auf das Basis-Blob eindeutig identifiziert. Sie können diesen Wert verwenden, um weitere Vorgänge für die Momentaufnahme durchzuführen.

Für ein Blob kann eine beliebige Anzahl von Momentaufnahmen vorhanden sein. Momentaufnahmen bleiben bestehen, bis sie explizit gelöscht werden, wobei eine Momentaufnahme jedoch nicht ohne das zugehörige Quell-Blob existieren kann. Sie können alle einem Blob zugeordneten Momentaufnahmen auflisten, um die aktuell vorhandenen Momentaufnahmen nachzuverfolgen.

##Erben von Eigenschaften

Wenn Sie eine Momentaufnahme eines Blobs erstellen, werden die Systemeigenschaften mit denselben Werten in die Momentaufnahme kopiert. Diese Liste führt kopierte Systemeigenschaften für die .NET-Speicherclientbibliothek auf:

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


Eine dem Basis-Blob zugeordnete Lease wird nicht in die Momentaufnahme kopiert. Momentaufnahmen können nicht geleast werden.

##Kopieren von Momentaufnahmen 

Für Kopiervorgänge, die Blobs und Momentaufnahmen betreffen, gelten folgende Regeln:

- Sie können eine Momentaufnahme über das zugehörige Basis-Blob kopieren. Indem Sie eine Momentaufnahme zu einem Basis-Blob heraufstufen, können Sie eine frühere Version eines Blobs wiederherstellen. Die Momentaufnahme bleibt erhalten, ihre Quelle wird jedoch durch eine Kopie überschrieben, die gelesen und geschrieben werden kann.

- Sie können eine Momentaufnahme in ein Ziel-Blob mit einem anderen Namen kopieren. Das resultierende Ziel-Blob ist ein schreibbares Blob und keine Momentaufnahme.

- Wenn ein Quell-Blob kopiert wird, werden ggf. vorhandene Momentaufnahmen des Quell-Blobs nicht mit in das Ziel kopiert. Wenn ein Ziel-Blob durch eine Kopie überschrieben wird, bleiben alle dem Ziel-Blob zugeordneten Momentaufnahmen unter dem Namen des Blobs erhalten. 

- Wenn Sie eine Momentaufnahme eines Block-Blobs erstellen, wird die Liste der Blöcke mit ausgeführtem Commit für das Blob ebenfalls in die Momentaufnahme kopiert. Blöcke ohne ausgeführten Commit werden nicht kopiert.

##Angeben einer Zugriffsbedingung 

Sie können eine Zugriffsbedingung angeben, sodass die Momentaufnahme nur erstellt wird, wenn eine bestimmte Bedingung erfüllt ist. Um eine Zugriffsbedingung anzugeben, verwenden Sie die AccessCondition-Eigenschaft. Wenn die angegebene Bedingung nicht erfüllt ist, wird die Momentaufnahme nicht erstellt, und der Blob-Dienst gibt den Statuscode "HTTPStatusCode.PreconditionFailed" zurück.

##Löschen von Momentaufnahmen 

Ein Blob mit Momentaufnahmen kann nur dann gelöscht werden, wenn die Momentaufnahmen ebenfalls gelöscht werden. Sie können eine einzelne Momentaufnahme löschen oder dem Speicherdienst mitteilen, dass alle Momentaufnahmen beim Löschen des Quell-Blobs gelöscht werden sollen. Wenn Sie versuchen, ein Blob zu löschen, für das noch Momentaufnahmen vorhanden sind, gibt der Aufruf einen Fehler zurück.

##Momentaufnahmen mit Premium-Speicher
Die Verwendung von Momentaufnahmen mit Premium-Speicher unterliegt den folgenden Regeln:

- Die Anzahl der Momentaufnahmen pro Seitenblob in einem Premium-Speicherkonto ist auf 100 beschränkt. Wenn diese Grenze überschritten wird, gibt der Momentaufnahmen-Blob-Vorgang Fehlercode 409 (SnapshotCountExceeded) zurück.

- Eine Momentaufnahme eines Seitenblobs in einem Premium-Speicherkonto kann alle zehn Minuten erstellt werden. Wenn diese Rate überschritten wird, gibt der Momentaufnahmen-Blob-Vorgang Fehlercode 409 (SnapshotOperationRateExceeded) zurück.

- Das Lesen von Momentaufnahmen eines Seitenblobs in einem Premium-Speicherkonto über "Get Blob" wird nicht unterstützt. Der Aufruf von "Get Blob" für eine Momentaufnahme in einem Premium-Speicherkonto gibt Fehlercode 400 (ungültiger Vorgang) zurück. Der Aufruf von "Get Blob Properties" und "Get Blob Metadata" für eine Momentaufnahme wird hingegen unterstützt.

- Zum Lesen einer Momentaufnahme können Sie eine Momentaufnahme mithilfe des Copy Blob-Vorgangs in ein anderes Seitenblob im Konto kopieren. Das Ziel-Blob für den Kopiervorgang darf keine vorhandenen Momentaufnahmen besitzen. Wenn das Ziel-Blob Momentaufnahmen aufweist, gibt "Copy Blob" Fehlercode 409 (SnapshotsPresent) zurück.

##Erstellen des absoluten URI zu einer Momentaufnahme 

Dieses Codebeispiel erstellt den absoluten URI einer Momentaufnahme aus dem Basis-Blob.

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
