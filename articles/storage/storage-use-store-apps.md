<properties
	pageTitle="Verwenden von Azure-Speicher in Windows Store-Apps | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine Windows Store-App erstellen, die Blob, Queue, Table oder File Storage von Azure verwendet."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm" />
<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="tamram"/>
	
# Verwenden von Azure Storage in Windows Store-Apps

## Übersicht

Dieser Leitfaden zeigt die ersten Schritte der Entwicklung einer Windows Store-App, die Azure Storage nutzt.

## Herunterladen erforderlicher Tools

- [Visual Studio 2012](http://msdn.microsoft.com/library/windows/apps/br211384) vereinfacht das Erstellen, Debuggen, Lokalisieren, Packen und Bereitstellen von Windows Store-Apps.
- Die [Azure Storage-Clientbibliothek für Windows-Runtime](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/11/05/windows-azure-storage-client-library-for-windows-runtime.aspx) stellt eine Klassenbibliothek für die Arbeit mit Azure Storage bereit.
- Die [WCF Data Services-Tools für Windows Store-Apps](http://www.microsoft.com/download/details.aspx?id=30714) erweitern die Nutzung von „Dienstverweis hinzufügen“ durch clientseitige OData-Unterstützung für Windows Store-Apps in Visual Studio 2012 und höher.

## Entwickeln von Apps

### Vorbereitung

Erstellen Sie ein neues Windows Store-App-Projekt in Visual Studio 2012 oder neuer:

![store-apps-storage-vs-project][store-apps-storage-vs-project]

Fügen Sie dann einen Verweis zur Azure Storage-Clientbibliothek hinzu, indem Sie mit der rechten Maustaste auf **Verweise** klicken und dann **Verweis hinzufügen** auswählen. Wechseln Sie anschließend zur Speicherclientbibliothek für Windows-Runtime, die Sie heruntergeladen haben:

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### Verwenden der Bibliothek mit den Blob- und Warteschlangendiensten

An diesem Punkt ist Ihre App bereit zum Aufruf der Blob- und Warteschlangendienste von Azure. Fügen Sie die folgenden **using**-Anweisungen hinzu, sodass direkt auf Azure Storage-Typen verwiesen werden kann:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Fügen der Seite dann eine Schaltfläche hinzu. Fügen Sie dem **Click**-Ereignis den folgenden Code hinzu, und ändern Sie die Ereignishandlermethode mit dem [async-Schlüsselwort](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Dieser Code setzt voraus, dass Sie die zwei Zeichenfolgenvariablen *accountName* und *accountKey* besitzen. Sie stehen für den Namen Ihres Speicherkontos und den Kontoschlüssel, der dem Konto zugeordnet ist.

Erstellen Sie die Anwendung, und führen Sie sie aus. Beim Klicken auf die Schaltfläche wird überprüft, ob ein Container namens *container1* in Ihrem Konto vorhanden ist. Andernfalls wird er erstellt.

### Verwenden der Bibliothek mit dem Tabellenspeicherdienst

Die Typen, die für die Kommunikation mit dem Azure-Tabellenspeicherdienst verwendet werden, hängen von WCF Data Services für die Windows Store-Apps-Bibliothek ab. Fügen Sie dann mithilfe der Paket-Manager-Konsole einen Verweis zu den erforderlichen WCF-Bibliotheken hinzu:

![store-apps-storage-package-manager][store-apps-storage-package-manager]

Verweisen Sie Package Manager mithilfe des folgenden Befehls auf den Speicherort auf dem Computer:

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Dieser Befehl fügt dem Projekt automatisch alle erforderlichen Verweise hinzu. Falls Sie die Paket-Manager-Konsole nicht verwenden möchten, können Sie auch den NuGet-Ordner von WCF Data Services auf dem lokalen Computer zur Liste der Paketquellen hinzufügen. Fügen Sie dann den Verweis über die im Thema zum [Verwalten von NuGet-Paketen mithilfe des Dialogs](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog) beschriebene Oberfläche hinzu.

Wenn Sie auf das NuGet-Paket von WCF Data Services verwiesen haben, ändern Sie den Code im **Click**-Ereignis der Schaltfläche:

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Dieser Code überprüft, ob eine Tabelle namens *table1* in Ihrem Konto vorhanden ist. Andernfalls wird sie erstellt.

Sie können auch einen Verweis zu „Microsoft.WindowsAzure.Storage.Table.dll“ hinzufügen. Diese Datei ist im gleichen heruntergeladenen Paket verfügbar. Diese Bibliothek enthält zusätzliche Funktionen, zum Beispiel Serialisierung und allgemeine Abfragen basierend auf Reflexion. Beachten Sie, dass diese Bibliothek JavaScript nicht unterstützt.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png

<!---HONumber=AcomDC_0114_2016-->