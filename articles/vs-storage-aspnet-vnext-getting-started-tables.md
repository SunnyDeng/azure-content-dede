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
> -   [Warteschlangen][Warteschlangen]
> -   [Tabellen][Erste Schritte]

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet. Weitere Informationen finden Sie unter [Verwenden von Tabellenspeicher aus .NET][Verwenden von Tabellenspeicher aus .NET].

Sie müssen die folgenden Aufgaben ausführen, um programmgesteuert auf Tabellen in ASP.NET vNext-Projekten zuzugreifen.

1.  Fügen Sie die folgende Codenamespace-Deklaration am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

        using Microsoft.Framework.ConfigurationModel;

2.  Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Abrufen der Verbindungszeichenfolge des Speichers

Bevor Sie eine Tabelle verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Tabellen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET vNext-Projekt verwenden, können Sie die Methode **get** des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]

Weitere Informationen finden Sie unter [ASP.NET vNext][ASP.NET vNext].

  [Erste Schritte]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Was ist passiert?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Blobs]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Warteschlangen]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Verwenden von Tabellenspeicher aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "Verwenden von Tabellenspeicher aus .NET"
  [ASP.NET vNext]: http://www.asp.net/vnext
