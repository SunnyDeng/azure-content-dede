<properties title="Erste Schritte mit Azure Storage" pageTitle="Erste Schritte mit Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

## Erste Schritte mit Azure Storage (ASP.NET vNext-Projekte)

> [AZURE.SELECTOR]
>
> -   [Blobs][Blobs]
> -   [Warteschlangen][Erste Schritte]
> -   [Tabellen][Tabellen]

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeichers aus .NET][Verwenden des Warteschlangenspeichers aus .NET].

Sie müssen die folgenden Aufgaben ausführen, um programmgesteuert auf Warteschlangen in ASP.NET vNext-Projekten zuzugreifen.

1.  Fügen Sie die folgende Codenamespace-Deklaration am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

        using Microsoft.Framework.ConfigurationModel;

2.  Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Abrufen der Verbindungszeichenfolge des Speichers

Bevor Sie eine Warteschlange verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Warteschlangen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET vNext-Projekt verwenden, können Sie die Methode **get** des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-queues-include](../includes/vs-storage-getting-started-queues-include.md)]

Weitere Informationen finden Sie unter [ASP.NET vNext][ASP.NET vNext].

  [Erste Schritte]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Was ist passiert?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Blobs]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Tabellen]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Verwenden des Warteschlangenspeichers aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-queues/ "Verwenden des Warteschlangenspeichers aus .NET"
  [ASP.NET vNext]: http://www.asp.net/vnext
