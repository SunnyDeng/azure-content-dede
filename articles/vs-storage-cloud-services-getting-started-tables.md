<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)
> - [Was ist passiert?](/documentation/articles/vs-storage-cloud-services-what-happened/)

##Erste Schritte mit Azure Storage (Cloud Service-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-cloud-services-getting-started-blobs/)
> - [Warteschlangen](/documentation/articles/vs-storage-cloud-services-getting-started-queues/)
> - [Tabellen](/documentation/articles/vs-storage-cloud-services-getting-started-tables/)

Der Azure-Tabellenspeicherdienst ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.  Weitere Informationen finden Sie unter[Verwenden von Tabellenspeicher aus .NET](http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-tables/#create-table "How to use Table Storage from .NET").

Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Table;

##### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Tabelle verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Tabellen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Für Cloud-Dienstprojekte können Sie den Typ **CloudConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      CloudConfigurationManager.GetSetting("<storageAccountName>_AzureStorageConnectionString"));

[AZURE.INCLUDE [vs-storage-getting-started-tables-include](../includes/vs-storage-getting-started-tables-include.md)]
<!--HONumber=42-->
