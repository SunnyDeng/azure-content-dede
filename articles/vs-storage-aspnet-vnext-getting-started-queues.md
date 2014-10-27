<properties title="Getting Started with Azure Storage" pageTitle="Getting Started with Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

[WACOM.INCLUDE [vs-storage-aspnet-vnext-getting-started-intro](../includes/vs-storage-aspnet-vnext-getting-started-intro.md)]

# Erste Schritte mit Azure Storage

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs" title="BLOBs" class="current">BLOBs</a><a href="/de-de/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues" title="Warteschlangen">Warteschlangen</a><a href="/de-de/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables" title="Tabellen">Tabellen</a></div>

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeichers aus .NET][Verwenden des Warteschlangenspeichers aus .NET].

Sie müssen die folgenden Aufgaben ausführen, um programmgesteuert auf Warteschlangen in ASP.NET vNext-Projekten zuzugreifen.

1.  Abrufen der Assembly Microsoft.WindowsAzure.Storage.dll. Sie können zu diesem Zweck NuGet verwenden. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie dann NuGet-Pakete verwalten aus. Suchen Sie online nach WindowsAzure.Storage, und klicken Sie dann auf Installieren, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren. Hinzufügen eines Verweises auf diese Assembly zu Ihrem Projekt.
2.  Installieren des NuGet-Pakets Microsoft.Framework.ConfigurationModel.Json.
3.  Fügen Sie die folgende Codenamespace-Deklaration am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

		using Microsoft.Framework.ConfigurationModel;

4.  Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

		var config = new Configuration();
		config.AddJsonFile("config.json");

### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Warteschlange verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Warteschlangen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET vNext-Projekt verwenden, können Sie die Methode get des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

[BLOBs]: /de-de/documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs "BLOBs"
[Warteschlangen]: /de-de/documentation/articles/vs-storage-aspnet-vnext-getting-started-queues "Warteschlangen"
[Tabellen]: /de-de/documentation/articles/vs-storage-aspnet-vnext-getting-started-tables "Tabellen"
[Verwenden des Warteschlangenspeichers aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-queues/ "Verwenden des Warteschlangenspeichers aus .NET"
