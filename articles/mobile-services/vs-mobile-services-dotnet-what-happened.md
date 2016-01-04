<properties
	pageTitle="Was ist mit meinem .NET-Projekt passiert, nachdem Mobile Services mithilfe von Visual Studio Verbundene Dienste hinzugefügt wurde? | Microsoft Azure"
	description="Beschreibt, was in Ihrem Visual Studio .NET-Projekt passiert ist, nachdem Azure Mobile Services mithilfe von Verbundene Dienste hinzugefügt wurde."
	services="mobile-services"
	documentationCenter=""
	authors="TomArcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/17/2015"
	ms.author="tarcher"/>

# Was ist mit meinem Visual Studio .NET-Projekt passiert, nachdem Azure Mobile Services mithilfe von Verbundene Dienste hinzugefügt wurde?

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-dotnet-getting-started.md)
> - [What Happened](vs-mobile-services-dotnet-what-happened.md)

## Verweise wurden hinzugefügt

Das NuGet-Paket für Azure Mobile Services wurde Ihrem Projekt hinzugefügt. Die folgenden .NET-Verweise wurde Ihrem Projekt hinzugefügt:

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives**

## Werte für die Verbindungszeichenfolge für Mobile Services

In Ihrer Datei **App.xaml.cs** wurde ein MobileServiceClient-Objekt mit der Anwendungs-URL des ausgewählten mobilen Diensts und seinem Anwendungsschlüssel erstellt.

## Mobile Services-Projekt hinzugefügt

Wenn ein .NET Mobile Service im Anbieter für verbundene Dienste erstellt wird, wird ein Mobile Services-Projekt erstellt und der Projektmappe hinzugefügt.


[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_1203_2015-->