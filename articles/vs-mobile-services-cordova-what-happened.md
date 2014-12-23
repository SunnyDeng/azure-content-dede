<properties title="Getting Started with Mobile Services" pageTitle="" metaKeywords="Azure, Getting Started, Mobile Services" description="" services="mobile-services" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="mobile-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb" />

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-mobile-services-cordova-getting-started/)
> - [Was ist passiert?](/documentation/articles/vs-mobile-services-cordova-what-happened/)

###<span id="whathappened">Was ist mit meinem Projekt passiert?</span>

#####Verweise wurden hinzugefügt

Das Azure Mobile Service Client-Plug-In, das in allen Hybrid-Apps für mehrerer Geräte enthalten ist, wurde aktiviert.
  
#####Werte für die Verbindungszeichenfolge für Mobile Services

Unter "services\mobileServices\settings" wurde eine neue JavaScript-Datei (JS-Datei) mit einem **MobileServiceClient** generiert. Sie enthält die Anwendungs-URL des ausgewählten mobilen Diensts sowie seinen Anwendungsschlüssel. Die Datei enthält die Initialisierung eines Clientobjekts des mobilen Diensts, die dem folgenden Code ähnelt.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[Weitere Informationen zu mobilen Diensten](http://azure.microsoft.com/documentation/services/mobile-services/)
