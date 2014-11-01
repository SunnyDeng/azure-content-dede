<properties title="Erste Schritte mit Azure Storage" pageTitle="Erste Schritte mit Azure Storage" metaKeywords="Azure, Getting Started, Storage" description="" services="storage" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="storage" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

### <span id="whathappened">Was ist mit meinem Projekt passiert?</span>

###### Verweise wurden hinzugefügt

Das NuGet-Paket Azure Storage wurde Ihrem Visual Studio-Projekt hinzugefügt.
Dieses Paket fügt die folgenden .NET-Verweise hinzu:

-   `Microsoft.Data.Edm`
-   `Microsoft.Data.OData`
-   `Microsoft.Data.Services.Client`
-   `Microsoft.WindowsAzure.Configuration`
-   `Microsoft.WindowsAzure.Storage`
-   `Newtonsoft.Json`
-   `System.Data`
-   `System.Spatial`

###### Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt

Elemente mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos wurden erstellt. An den folgenden Dateien wurden Änderungen vorgenommen:

-   `ServiceDefinition.csdef`
-   `ServiceConfiguration.Cloud.cscfg`
-   `ServiceConfiguration.Local.cscfg`

  [Erste Schritte]: /documentation/articles/vs-storage-cloud-services-getting-started-blobs/
  [Was ist passiert?]: /documentation/articles/vs-storage-cloud-services-what-happened/
