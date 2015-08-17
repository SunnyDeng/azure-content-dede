<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="Beschreibt, was beim Erstellen eines Azure-Speichers in einem Visual Studio Azure WebJob-Projekts passiert ist." 
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
	ms.date="07/13/2015" 
	ms.author="patshea123"/>

# Was ist mit meinem Projekt passiert?

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-webjobs-getting-started-blobs.md)
> - [What Happened](vs-storage-webjobs-what-happened.md)

###<span id="whathappened">Was ist mit meinem Projekt passiert?</span>

##### Verweise wurden hinzugefügt

Das Azure Storage NuGet-Paket wurde zu Ihrem Visual Studio-Projekt hinzugefügt oder aktualisiert. Dieses Paket fügt die folgenden .NET-Verweise hinzu:

- `Microsoft.Data.Edm`
- `Microsoft.Data.OData`
- `Microsoft.Data.Services.Client`
- `Microsoft.WindowsAzure.ConfigurationManager`
- `Microsoft.WindowsAzure.Storage`
- `Newtonsoft.Json`
- `System.Data`
- `System.Spatial`

#####Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt 
In der Datei „App.config“ Ihres Projekts wurden `AzureWebJobsStorage` und `AzureWebJobsDashboard` mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos erstellt.

Weitere Informationen finden Sie unter [Empfohlene Ressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!---HONumber=August15_HO6-->