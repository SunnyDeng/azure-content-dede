<properties title="Erste Schritte mit Azure Storage" pageTitle="Erste Schritte mit Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/10/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

## Erste Schritte mit Azure Storage (ASP.NET vNext-Projekte)

> [AZURE.SELECTOR]
>
> -   [Blobs][Erste Schritte]
> -   [Warteschlangen][Warteschlangen]
> -   [Tabellen][Tabellen]

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten.

Im ersten Schritt müssen Sie ein Azure-Speicherkonto und dann mindestens einen Container im Speicher erstellen. Sie können z. B. einen Speicher namens "Notizbuch" und dann Container im Speicher namens "Bilder" zum Speichern von Bildern und "Audio" zum Speichern von Audiodateien erstellen. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen. Weitere Informationen zum programmgesteuerten Manipulieren von BLOBs finden Sie unter [Verwenden von BLOB-Speicher aus .NET][Verwenden von BLOB-Speicher aus .NET].

Sie müssen die folgenden Aufgaben ausführen, um programmgesteuert auf BLOBs in ASP.NET vNext-Projekten zuzugreifen.

1.  Fügen Sie die folgende Codenamespace-Deklaration am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

        using Microsoft.Framework.ConfigurationModel;

2.  Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

        var config = new Configuration();
        config.AddJsonFile("config.json");

##### Abrufen der Verbindungszeichenfolge des Speichers

Bevor Sie einen BLOB verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die BLOBs gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET vNext-Projekt verwenden, können Sie die Methode **get** des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

[WACOM.INCLUDE [vs-storage-getting-started-blobs-include](../includes/vs-storage-getting-started-blobs-include.md)]

Weitere Informationen finden Sie unter [ASP.NET vNext][ASP.NET vNext].

  [Erste Schritte]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-blobs/
  [Was ist passiert?]: /documentation/articles/vs-storage-aspnet-vnext-what-happened/
  [Warteschlangen]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-queues/
  [Tabellen]: /documentation/articles/vs-storage-aspnet-vnext-getting-started-tables/
  [Verwenden von BLOB-Speicher aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/ "Verwenden von BLOB-Speicher aus .NET"
  [vs-storage-getting-started-blobs-include]: ../includes/vs-storage-getting-started-blobs-include.md
  [ASP.NET vNext]: http://www.asp.net/vnext
