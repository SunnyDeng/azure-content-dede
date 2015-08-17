<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="Beschreibt, was beim Erstellen eines Azure-Speichers in einem Visual Studio ASP.NET 5-Projekts passiert ist." 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea"/>

# Was ist mit meinem Projekt passiert?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

###Was ist mit meinem Projekt passiert?

##### Verweise wurden hinzugefügt

Das Azure Storage NuGet-Paket wurde zu Ihrem Visual Studio-Projekt hinzugefügt. Dieses Paket fügt die folgenden .NET-Verweise hinzu:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.Configuration`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

Außerdem wurde das NuGet-Paket **Microsoft.Framework.Configuration.Json** hinzugefügt.

#####Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt 
In der Datei config.json Ihres Projekts wurde ein Element mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos erstellt.

Weitere Informationen finden Sie unter [ASP.NET 5](http://www.asp.net/vnext).
 

<!---HONumber=August15_HO6-->