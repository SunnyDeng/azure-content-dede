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
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Was ist passiert?](/documentation/articles/vs-storage-aspnet5-what-happened/)

### <span id="whathappened">Was ist mit meinem Projekt passiert?</span>

##### Verweise wurden hinzugefügt

Das Azure Storage NuGet-Paket wurde zu Ihrem Visual Studio-Projekt hinzugefügt.  
Dieses Paket fügt die folgenden .NET-Verweise hinzu:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

Außerdem wurde das NuGet-Paket **Microsoft.Framework.ConfigurationModel.Json** hinzugefügt.

##### Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt 
In der Datei config.json Ihres Projekts wurde ein Element mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos erstellt.

Weitere Informationen finden Sie unter [ASP.NET 5](http://www.asp.net/vnext).
\<!--HONumber=42-->
