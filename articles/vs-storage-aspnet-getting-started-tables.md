<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-getting-started-intro](../includes/vs-storage-aspnet-getting-started-intro.md)]

### Erste Schritte mit Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/vs-storage-aspnet-getting-started-blobs" title="BLOBs" class="current">BLOBs</a><a href="/de-de/documentation/articles/vs-storage-aspnet-getting-started-queues" title="Warteschlangen">Warteschlangen</a><a href="/de-de/documentation/articles/vs-storage-aspnet-getting-started-tables" title="Tabellen">Tabellen</a></div>

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Weitere Informationen finden Sie unter [Verwenden von Tabellenspeicher aus .NET][Verwenden von Tabellenspeicher aus .NET].

Sie müssen die folgenden Aufgaben ausführen, um programmgesteuert auf Tabellen in ASP.NET-Projekten zuzugreifen.

1.  Abrufen der Assembly Microsoft.WindowsAzure.Storage.dll. Sie können zu diesem Zweck NuGet verwenden. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann NuGet-Pakete verwalten aus. Suchen Sie online nach WindowsAzure.Storage, und klicken Sie dann auf Installieren, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren. Hinzufügen eines Verweises auf diese Assembly zu Ihrem Projekt.
2.  Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C# Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

###### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Tabelle verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Tabellen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Für ASP.NET-Projekte können Sie den Typ **ConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

[BLOBs]: /de-de/documentation/articles/vs-storage-aspnet-getting-started-blobs "BLOBs"
[Warteschlangen]: /de-de/documentation/articles/vs-storage-aspnet-getting-started-queues "Warteschlangen"
[Tabellen]: /de-de/documentation/articles/vs-storage-aspnet-getting-started-tables "Tabellen"
[Verwenden von Tabellenspeicher aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Verwenden von Tabellenspeicher aus .NET"
