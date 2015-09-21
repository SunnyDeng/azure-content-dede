<properties
    pageTitle="Was ist mit dem Clouddienstprojekt passiert? | Microsoft Azure | Verbundene Visual Studio-Dienste"
	description="Erfahren Sie, was nach dem Herstellen einer Verbindung mit einem Azure-Speicherkonto mithilfe von verbundenen Visual Studio-Diensten in einem Clouddienstprojekt passiert."
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
	ms.date="09/03/2015"
	ms.author="patshea123"/>

# Was ist mit dem Clouddienstprojekt passiert (verbundene Visual Studio-Dienste für Azure Storage)?

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-cloud-services-getting-started-blobs.md)
> - [What happened](vs-storage-cloud-services-what-happened.md)


## Verweise wurden hinzugefügt

Das Azure Storage NuGet-Paket wurde zu Ihrem Visual Studio-Projekt hinzugefügt. Dieses Paket fügt die folgenden .NET-Verweise hinzu:

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.Configuration**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## Die Verbindungszeichenfolge für Azure Storage wurde hinzugefügt
Elemente mit der Verbindungszeichenfolge und dem Schlüssel des ausgewählten Speicherkontos wurden erstellt. An den folgenden Dateien wurden Änderungen vorgenommen:

- **ServiceDefinition.csdef**
- **ServiceConfiguration.Cloud.cscfg**
- **ServiceConfiguration.Local.cscfg**

<!---HONumber=Sept15_HO2-->