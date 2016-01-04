<properties
    pageTitle="Was ist mit dem Clouddienstprojekt passiert? | Microsoft Azure | Verbundene Visual Studio-Dienste"
	description="Erfahren Sie, was nach dem Herstellen einer Verbindung mit einem Azure-Speicherkonto mithilfe von verbundenen Visual Studio-Diensten in einem Clouddienstprojekt passiert."
    services="storage"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-what-happened"
	ms.devlang="na"
	ms.topic="article"
  ms.date="12/16/2015"
	ms.author="tarcher"/>

# Was ist mit dem Clouddienstprojekt passiert (verbundene Visual Studio-Dienste für Azure Storage)?

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

<!---HONumber=AcomDC_1217_2015-->