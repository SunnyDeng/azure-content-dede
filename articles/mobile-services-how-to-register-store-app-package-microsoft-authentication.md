<properties linkid="develop-mobile-how-to-guides-register-windows-store-app-server-auth" urlDisplayName="Shared Access Signature Part 1" pageTitle="Register your Windows Store app package for Microsoft authentication" metaKeywords="" description="Learn how to register your Windows Store app for Microsoft authentication in your Azure Mobile Services application" metaCanonical="" services="" documentationCenter="Mobile" title="Register your Windows Store app package for Microsoft authentication" authors="glenga" solutions="" manager="" editor="" />

Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung
===============================================================================

Azure Mobile Services unterstützt sowohl client- als auch servergesteuerte Authentifizierungsmethoden. Mit der servergesteuerten Authentifizierung können Sie Identitätsanbieter wie z. B. Microsoft-Konten verwenden. Wenn Sie ein Microsoft-Konto mit servergesteuerter Authentifizierung verwenden, ohne Ihre App bei Mobile Services registriert zu haben, müssen Ihre Benutzer die Anmeldeinformationen für jede Authentifizierung erneut eingeben. Wenn Sie Ihre App registrieren, werden die Microsoft-Konto-Anmeldeinformationen zwischengespeichert und können zur Authentifizierung verwendet werden, ohne dass der Benutzer die Daten erneut eingeben muss. Dieses Thema beschreibt, wie Sie Ihr Windows Store-App-Pakets für ein verbessertes Anmeldeerlebnis mit Microsoft-Konten registrieren können, wenn Sie Azure Mobile Services zur Authentifizierung verwenden.

Clientgesteuerte Authentifizierung kann verwendet werden, um einmaliges Anmelden auf Windows-Geräten mit Live Connect anzubieten. Falls Sie die Live Connect-APIs verwenden, müssen Sie die Schritte in diesem Thema nicht ausführen. Weitere Informationen finden Sie unter [Authentifizieren einer Windows Store-App mit "Einmaliges Anmelden über Live Connect"](/de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet).

Durch die Registrierung erhalten Sie außerdem die Möglichkeit, Pushbenachrichtigungen an Ihre App zu senden. Falls Sie das Lernprogramm [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/) für Ihre App bereits abgeschlossen haben, dann haben Sie die Schritte in diesem Artikel bereits ausgeführt.

**Hinweis**

*Visual Studio 2013-Vorschau* enthält neue Features, die eine einfache Registrierung Ihres Windows Store-App-Pakets mit Mobile Services ermöglichen. Weitere Informationen finden Sie unter [Schnellstart: Hinzufügen von Pushbenachrichtigungen für einen mobilen Dienst](http://go.microsoft.com/fwlink/p/?LinkId=309101) im Windows Dev Center.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Falls Sie Ihr App-Paket bereits registriert haben, müssen Sie den Wert **true** für *useSingleSignOn* übergeben, wenn Sie die [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594)-Methode aufrufen. Auf diese Weise bieten Sie Ihren Benutzern ein verbessertes Anmeldeerlebnis für Microsoft-Konten.

