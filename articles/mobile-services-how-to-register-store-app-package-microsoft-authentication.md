<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung

Azure Mobile Services unterstützt sowohl client- als auch servergesteuerte Authentifizierungsmethoden. Mit der servergesteuerten Authentifizierung können Sie Identitätsanbieter wie z. B. Microsoft-Konten verwenden. Wenn Sie ein Microsoft-Konto mit servergesteuerter Authentifizierung verwenden, ohne Ihre App bei Mobile Services registriert zu haben, müssen Ihre Benutzer die Anmeldeinformationen für jede Authentifizierung erneut eingeben. Wenn Sie Ihre App registrieren, werden die Microsoft-Konto-Anmeldeinformationen zwischengespeichert und können zur Authentifizierung verwendet werden, ohne dass der Benutzer die Daten erneut eingeben muss. Dieses Thema beschreibt, wie Sie Ihr Windows Store-App-Pakets für ein verbessertes Anmeldeerlebnis mit Microsoft-Konten registrieren können, wenn Sie Azure Mobile Services zur Authentifizierung verwenden.

> [WACOM.NOTE]Visual Studio 2013 ermöglicht die einfache Registrierung Ihres Windows Store-App-Pakets bei Mobile Services. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen von Pushbenachrichtigungen für einen mobilen Dienst][Schnellstart: Hinzufügen von Pushbenachrichtigungen für einen mobilen Dienst] im Windows Dev Center.

Die clientverwaltete Authentifizierung kann verwendet werden, um einmaliges Anmelden auf Windows-Geräten mit Live Connect zu ermöglichen. Falls Sie die Live Connect-APIs verwenden, müssen Sie die Schritte in diesem Thema nicht ausführen. Weitere Informationen finden Sie unter [Authentifizieren einer Windows Store-App mit "Einmaliges Anmelden über Live Connect"][Authentifizieren einer Windows Store-App mit "Einmaliges Anmelden über Live Connect"].

> [WACOM.NOTE]Die clientverwaltete Authentifizierung unter Verwendung von Live Connect wird in einem mobilen .NET-Back-End-Dienst noch nicht unterstützt.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Falls Sie Ihr App-Paket bereits registriert haben, müssen Sie den Wert **true** für *useSingleSignOn* übergeben, wenn Sie die [LoginAsync][LoginAsync]-Methode aufrufen. Auf diese Weise bieten Sie Ihren Benutzern ein verbessertes Anmeldeerlebnis für Microsoft-Konten.



  [Schnellstart: Hinzufügen von Pushbenachrichtigungen für einen mobilen Dienst]: http://go.microsoft.com/fwlink/p/?LinkId=309101
  [Authentifizieren einer Windows Store-App mit "Einmaliges Anmelden über Live Connect"]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [LoginAsync]: http://go.microsoft.com/fwlink/p/?LinkId=311594
