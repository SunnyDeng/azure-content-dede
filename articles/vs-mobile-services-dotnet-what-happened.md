<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-mobile-services-dotnet-getting-started/)
> - [Was ist passiert?](/documentation/articles/vs-mobile-services-dotnet-what-happened/)

###<span id="whathappened">Was ist mit meinem Projekt passiert?</span>

#####Verweise wurden hinzugefügt

Das NuGet-Paket für Azure Mobile Services wurde Ihrem Projekt hinzugefügt. Die folgenden .NET-Verweise wurde Ihrem Projekt hinzugefügt:

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Werte für die Verbindungszeichenfolge für Mobile Services

In Ihrer Datei "App.xaml.cs" wurde ein **MobileServiceClient**-Objekt mit der Anwendungs-URL des ausgewählten mobilen Diensts und dem zugehörigen Anwendungsschlüssel erstellt. 

[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)



<!--HONumber=42-->
