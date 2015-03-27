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
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung

Azure Mobile Services unterstützt sowohl client- als auch servergesteuerte Authentifizierungsmethoden. Mit der servergesteuerten Authentifizierung können Sie Identitätsanbieter wie z. B. Microsoft-Konten verwenden. Wenn Sie ein Microsoft-Konto mit servergesteuerter Authentifizierung verwenden, ohne Ihre App bei Mobile Services registriert zu haben, müssen Ihre Benutzer die Anmeldeinformationen für jede Authentifizierung erneut eingeben. Wenn Sie Ihre App registrieren, werden die Microsoft-Konto-Anmeldeinformationen zwischengespeichert und können zur Authentifizierung verwendet werden, ohne dass der Benutzer die Daten erneut eingeben muss. Dieses Thema beschreibt, wie Sie Ihr Windows Store-App-Pakets für ein verbessertes Anmeldeerlebnis mit Microsoft-Konten registrieren können, wenn Sie Azure Mobile Services zur Authentifizierung verwenden. 

>[AZURE.NOTE]Visual Studio 2013 ermöglicht die einfache Registrierung Ihres Windows Store-App-Pakets bei Mobile Services. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/p/?LinkId=309101">Schnellstart: Hinzufügen von Pushbenachrichtigungen für einen Mobile Service</a> im Windows Dev Center.

Die clientverwaltete Authentifizierung kann verwendet werden, um einmaliges Anmelden auf Windows-Geräten mit Live Connect zu ermöglichen. Falls Sie die Live Connect-APIs verwenden, müssen Sie die Schritte in diesem Thema nicht ausführen. Weitere Informationen finden Sie unter [Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect].   

[AZURE.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Nachdem Sie Ihr App-Paket registriert haben, stellen Sie den Wert <strong>wahr</strong> für <em>useSingleSignOn</em> bereit, wenn Sie die Methode <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> aufrufen. Auf diese Weise bieten Sie Ihren Benutzern ein verbessertes Anmeldeerlebnis für Microsoft-Konten.

<!-- Anchors. -->
<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Authentifizieren Ihrer Windows Store-App mit einmaliger Anmeldung von Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Erste Schritte mit C# für Benutzer]: /develop/mobile/tutorials/get-started-with-users-dotnet/
[Erste Schritte mit JavaScript für Benutzer]: /develop/mobile/tutorials/get-started-with-users-js/

<!--HONumber=47-->
