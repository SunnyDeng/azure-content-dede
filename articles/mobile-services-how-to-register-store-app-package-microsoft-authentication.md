<properties 
	pageTitle="Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung" 
	description="Erfahren Sie mehr über die Registrierung Ihrer Windows Store-App für die Microsoft-Authentifizierung in Ihrer Azure Mobile Services-Anwendung." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="glenga"/>

# Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung

Azure Mobile Services unterstützt sowohl client- als auch servergesteuerte Authentifizierungsmethoden. Mit der servergesteuerten Authentifizierung können Sie Identitätsanbieter wie z. B. Microsoft-Konten verwenden. Wenn Sie ein Microsoft-Konto mit servergesteuerter Authentifizierung verwenden, ohne Ihre App bei Mobile Services registriert zu haben, müssen Ihre Benutzer die Anmeldeinformationen für jede Authentifizierung erneut eingeben. Wenn Sie Ihre App registrieren, werden die Microsoft-Konto-Anmeldeinformationen zwischengespeichert und können zur Authentifizierung verwendet werden, ohne dass der Benutzer die Daten erneut eingeben muss. Dieses Thema beschreibt, wie Sie Ihr Windows Store-App-Pakets für ein verbessertes Anmeldeerlebnis mit Microsoft-Konten registrieren können, wenn Sie Azure Mobile Services zur Authentifizierung verwenden.

>[AZURE.NOTE]Visual Studio 2013 ermöglicht die einfache Registrierung Ihres Windows Store-App-Pakets bei Mobile Services. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Schnellstart: Hinzufügen von Pushbenachrichtigungen für einen mobilen Dienst</a> im Windows Dev Center.

Die clientverwaltete Authentifizierung kann verwendet werden, um einmaliges Anmelden auf Windows-Geräten mit Live Connect zu ermöglichen. Falls Sie die Live Connect-APIs verwenden, müssen Sie die Schritte in diesem Thema nicht ausführen. Weitere Informationen finden Sie unter [Authentifizieren einer Windows Store-App mit "Einmaliges Anmelden über Live Connect"].

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Falls Sie Ihr App-Paket bereits registriert haben, müssen Sie den Wert <strong>true</strong> für <em>useSingleSignOn</em> übergeben, wenn Sie die <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a>-Methode aufrufen. Auf diese Weise bieten Sie Ihren Benutzern ein verbessertes Anmeldeerlebnis für Microsoft-Konten.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authentifizieren einer Windows Store-App mit "Einmaliges Anmelden über Live Connect"]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Get started with users C#]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Get started with users JavaScript]: /develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=54-->