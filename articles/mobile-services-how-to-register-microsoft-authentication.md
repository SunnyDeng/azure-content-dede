<properties pageTitle="Register for Microsoft authentication - Mobile Services" metaKeywords="Azure registering application, Azure Microsoft authentication, application authenticate, authenticate mobile services" description="Learn how to register for Microsoft authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use a Microsoft Account login" authors="glenga" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrieren Ihrer Apps für die Verwendung einer Microsoft Account-Anmeldung

In diesem Thema erfahren Sie, wie Sie Ihre App registrieren, um Live Connect als Authentifizierungsanbieter für Azure Mobile Services verwenden zu können.

> [WACOM.NOTE]Informationen zum Konfigurieren der Microsoft-Kontoauthentifizierung für eine Windows 8.1- oder Windows Phone 8.1-App finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung][Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung].

1.  Navigieren Sie im Live Connect Developer Center zur Seite [Eigene Apps][Eigene Apps], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

2.  Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

    ![][0]

    Damit wird die Anwendung bei Live Connect registriert.

3.  Klicken Sie auf **API-Einstellungen**, geben Sie in **Umleitungs-URL** den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` an, und klicken Sie auf **Speichern**.

    > [WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix *signin-microsoft* für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-microsoft`.

    ![][1]

    Dadurch wird die Microsoft-Kontoauthentifizierung für Ihre App aktiviert.

    > [WACOM.NOTE]Zur Registrierung einer vorhandenen Live Connect-App müssen Sie u. U. zuerst **Erweiterte Umleitungssicherheit** aktivieren.

4.  Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**.

    ![][2]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der geheime Clientschl&uuml;ssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschl&uuml;ssel mit niemandem, und geben Sie ihn nicht &uuml;ber Ihre App frei.</p>
</div>

Sie können nun eine Microsoft Account-Anmeldung für die Authentifizierung in Ihrer App verwenden, indem Sie die Client-ID und den geheimen Clientschlüssel für Mobile Services bereitstellen.



  [Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
  [Eigene Apps]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [0]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-live-connect-add-app.png
  [1]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png
  [2]: ./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png
