<properties 
	pageTitle="Was ist mit meinem Cordova-Projekt passiert (Visual Studio Verbundene Dienste) | Microsoft Azure" 
	description="Beschreibt, was mit Ihrem Azure Cordova-Projekt passiert ist, nachdem Azure Mobile Services mithilfe von Visual Studio Verbundene Dienste hinzugefügt wurde." 
	services="mobile-services" 
	documentationCenter="na" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/17/2015" 
	ms.author="patshea"/>

# Was ist mit meinem Azure Cordova-Projekt passiert, nachdem Azure Mobile Services mithilfe von Visual Studio Verbundene Dienste hinzugefügt wurde?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

##Verweise wurden hinzugefügt

Das Azure Mobile Service Client-Plug-In, das in allen Hybrid-Apps für mehrerer Geräte enthalten ist, wurde aktiviert.
  
##Werte für die Verbindungszeichenfolge für Mobile Services

Unter `services\mobileServices\settings` wurde eine neue JavaScript-Datei (JS-Datei) mit einem **MobileServiceClient** generiert. Sie enthält die Anwendungs-URL des ausgewählten mobilen Diensts sowie seinen Anwendungsschlüssel. Die Datei enthält die Initialisierung eines Clientobjekts des mobilen Diensts, die dem folgenden Code ähnelt.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=Sept15_HO4-->