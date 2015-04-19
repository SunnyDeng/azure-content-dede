<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="Erste Schritte mit Azure-Warteschlangenspeicher in einem ASP.NET-Projekt in Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](vs-storage-aspnet-getting-started-queues.md)
> - [Was ist passiert?](vs-storage-aspnet-what-happened.md)

## Erste Schritte mit Azure Storage (ASP.NET-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Warteschlangen](vs-storage-aspnet-getting-started-queues.md)
> - [Tabellen](vs-storage-aspnet-getting-started-tables.md)

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeichers aus .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/).

Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

##### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Warteschlange verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Warteschlangen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Für ASP.NET-Projekte können Sie den Typ **ConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      ConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Weitere Informationen finden Sie unter [ASP.NET.](http://www.asp.net)
<!--HONumber=47-->
