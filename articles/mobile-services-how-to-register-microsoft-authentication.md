<properties pageTitle="Registrieren für die Microsoft Authentifizierung - Mobile Services" description="Erfahren Sie mehr über die Registrierung für die Microsoft-Authentifizierung in Ihrer Azure Mobile Services-Anwendung." authors="ggailey777" services="mobile-services" documentationCenter="" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga"/>

# Registrieren Ihrer Apps für die Verwendung einer Microsoft Account-Anmeldung

In diesem Thema erfahren Sie, wie Sie Ihre App registrieren, um Live Connect als Authentifizierungsanbieter für Azure Mobile Services verwenden zu können. 

>[AZURE.NOTE]Informationen zum Konfigurieren der Microsoft-Kontoauthentifizierung für eine universelle Windows-App oder zum Bereitstellen der einmaligen Anmeldung für eine Windows Store-App finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft-Authentifizierung](/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Navigieren Sie im Live Connect Developer Center zur Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Eigene Apps</a>, und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an. 

2. Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

   	![][1] 

   	Damit wird die Anwendung bei Live Connect registriert.

3. Klicken Sie auf **API-Einstellungen**, geben Sie in **Umleitungs-URL** den Wert  `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` an, und klicken Sie auf **Speichern**.

	>[AZURE.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix _signin-microsoft_ für Ihren mobilen Dienst als .NET-Dienst, z. B. <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Dadurch wird die Microsoft-Kontoauthentifizierung für Ihre App aktiviert.

	>[AZURE.NOTE]Zur Registrierung einer vorhandenen Live Connect-App müssen Sie u. U. zuerst **Erweiterte Umleitungssicherheit** aktivieren.

4. Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**. 

   	![][2]

    > [AZURE.NOTE] Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

Sie können nun eine Microsoft Account-Anmeldung für die Authentifizierung in Ihrer App verwenden, indem Sie die Client-ID und den geheimen Clientschlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
