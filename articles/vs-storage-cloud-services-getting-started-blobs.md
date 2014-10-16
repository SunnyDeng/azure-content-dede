<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-cloud-services-getting-started-intro][vs-storage-cloud-services-getting-started-intro]]

# Erste Schritte mit Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/vs-storage-cloud-services-getting-started-blobs" title="BLOBs" class="current">BLOBs</a><a href="/de-de/documentation/articles/vs-storage-cloud-services-getting-started-queues" title="Warteschlangen">Warteschlangen</a><a href="/de-de/documentation/articles/vs-storage-cloud-services-getting-started-tables" title="Tabellen">Tabellen</a></div>

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten.

Im ersten Schritt müssen Sie ein Azure-Speicherkonto und dann mindestens einen Container im Speicher erstellen. Sie können z. B. einen Speicher namens "Notizbuch" und dann Container im Speicher namens "Bilder" zum Speichern von Bildern und "Audio" zum Speichern von Audiodateien erstellen. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen. Weitere Informationen zum programmgesteuerten Manipulieren von BLOBs finden Sie unter [Verwenden von BLOB-Speicher aus .NET][Verwenden von BLOB-Speicher aus .NET].

Sie müssen die folgenden Aufgaben ausführen, um programmgesteuert auf BLOBs in einem Cloud-Dienstprojekt zuzugreifen.

1.  Abrufen der Assembly Microsoft.WindowsAzure.Storage.dll. Sie können zu diesem Zweck NuGet verwenden. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann NuGet-Pakete verwalten aus. Suchen Sie online nach WindowsAzure.Storage, und klicken Sie dann auf Installieren, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren. Hinzufügen eines Verweises auf diese Assembly zu Ihrem Projekt.
2.  Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie einen BLOB verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die BLOBs gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Für Cloud-Dienstprojekte können Sie den Typ **CloudConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include][vs-storage-getting-started-blobs-include]]

[vs-storage-cloud-services-getting-started-intro]: ../includes/vs-storage-cloud-services-getting-started-intro.md
[BLOBs]: /de-de/documentation/articles/vs-storage-cloud-services-getting-started-blobs "BLOBs"
[Warteschlangen]: /de-de/documentation/articles/vs-storage-cloud-services-getting-started-queues "Warteschlangen"
[Tabellen]: /de-de/documentation/articles/vs-storage-cloud-services-getting-started-tables "Tabellen"
[Verwenden von BLOB-Speicher aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/ "Verwenden von BLOB-Speicher aus .NET"
[vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
