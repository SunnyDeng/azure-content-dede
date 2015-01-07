<properties pageTitle="Registrieren für die Microsoft Authentifizierung - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrieren Ihrer Apps für die Verwendung einer Microsoft Account-Anmeldung

In diesem Thema erfahren Sie, wie Sie Ihre App registrieren, um Live Connect als Authentifizierungsanbieter für Azure Mobile Services verwenden zu können. 

>[WACOM.NOTE]Informationen zum Konfigurieren der Microsoft-Kontoauthentifizierung für eine universelle Windows-App oder zum Bereitstellen der einmaligen Anmeldung für eine Windows Store-App finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft-Authentifizierung](/de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication).

1. Navigieren Sie zur Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Eigene Apps</a> im Live Connect Developer Center und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an. 

2. Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein und klicken Sie auf **Ich stimme zu**.

   	![][1] 

   	Damit wird die Anwendung bei Live Connect registriert.

3. Klicken Sie auf **API-Einstellungen**, stellen Sie den Wert "https://<mobile_service>.azure-mobile.net/login/microsoftaccount" in **Umleitungs-URL** und klicken Sie dann auf **Speichern**.

	>[WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix "signin-microsoft" für Ihren mobilen Dienst als .NET-Dienst, z. B. <code>https://todolist.azure-mobile.net/signin-microsoft</code>.  

	![][3]

	Dadurch wird die Microsoft-Kontoauthentifizierung für Ihre App aktiviert.

	>[WACOM.NOTE]Zur Registrierung einer vorhandenen Live Connect-App müssen Sie u. U. zuerst **Erweiterte Umleitungssicherheit** aktivieren.

4. Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**. 

   	![][2]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
	<p>Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.</p>
    </div>

Sie können nun eine Microsoft Account-Anmeldung für die Authentifizierung in Ihrer App verwenden, indem Sie die Client-ID und den geheimen Clientschlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
[2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
[3]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png

<!-- URLs. -->

[Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Eigene Apps]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
