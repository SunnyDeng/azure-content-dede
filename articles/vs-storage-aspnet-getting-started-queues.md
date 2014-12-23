<properties title="Getting Started with Azure Storage" pageTitle="Erste Schritte mit Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="vs-getting-started" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Was ist passiert?](/documentation/articles/vs-storage-aspnet-what-happened/)

## Erste Schritte mit Azure Storage (ASP.NET-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet-getting-started-blobs/)
> - [Warteschlangen](/documentation/articles/vs-storage-aspnet-getting-started-queues/)
> - [Tabellen](/documentation/articles/vs-storage-aspnet-getting-started-tables/)

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeichers aus .NET](http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-queues/).

Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

#####Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Warteschlange verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Warteschlangen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Für ASP.NET-Projekte können Sie den Typ **ConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Weitere Informationen finden Sie unter [ASP.NET](http://www.asp.net).
