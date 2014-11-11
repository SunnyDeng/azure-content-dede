<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Twitter zur Authentifizierung mit Azure Mobile Services zu verwenden.

<div class="dev-callout"><b>Hinweis</b>
<p>Sie ben&ouml;tigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschlie&szlig;en. Rufen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a> auf, um ein neues Twitter-Konto zu erstellen.</p>
</div>

1.  Navigieren Sie zur Website für [Twitter-Entwickler][Twitter-Entwickler], melden Sie sich mit den Anmeldeinformationen für Ihr Twitter-Konto an, und klicken Sie auf **Create a new application**.

    ![][0]

2.  Geben Sie unter **Name**, **Description** und **Website** Werte für Ihre App und dann unter **Callback URL** die URL Ihres mobilen Diensts gefolgt vom Pfad */login/twitter* ein.

    > [WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix *signin-twitter* für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-twitter`.

    ![][1]

3.  Lesen und bestätigen Sie die Nutzungsbedingungen am Ende der Seite, lösen Sie das CAPTCHA und klicken Sie auf **Twitter-Anwendung erstellen**.

    ![][2]

    Daraufhin wird die Anwendung mit den angegebenen Details registriert.

4.  Notieren Sie die Werte für **Consumer key** und **Consumer secret**.

    ![][3]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schl&uuml;ssel mit niemandem, und geben Sie ihn nicht &uuml;ber Ihre App frei.</p>
</div>

5.  Klicken Sie auf die Registerkarte **Einstellungen**, blättern Sie nach unten und markieren Sie die Option **Anwendung für die Anmeldung bei Twitter freigeben** und klicken Sie auf **Twitter-Einstellungen für diese Anwendung aktualisieren**.

    ![][4]

Sie können nun ein Twitter-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die consumer key und consumer secret für Mobile Services bereitstellen.



  [Twitter-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268300
  [0]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
  [1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
  [2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
  [3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
  [4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png
