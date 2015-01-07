<properties urlDisplayName="Register for Twitter Authentication" pageTitle="Registrieren für die Twitter Authentifizierung - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Twitter login with Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />

# Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Twitter zur Authentifizierung mit Azure Mobile Services zu verwenden.

>[WACOM.NOTE] Dieses Lernprogramm befasst sich mit [Azure Mobile Services](http://azure.microsoft.com/de-de/services/mobile-services/), eine Lösung, die Sie dabei unterstützt, skalierbare mobile Anwendungen für beliebige Plattformen zu erstellen. Mit Mobile Services ist es einfach, Daten zu synchronisieren, Benutzer zu authentifizieren und Pushbenachrichtigungen zu senden. Diese Seite unterstützt das Lernprogramm <a href="http://azure.microsoft.com/de-de/documentation/articles/mobile-services-ios-get-started-users/">Erste Schritte mit der Authentifizierung</a> . In diesem Lernprogramm wird veranschaulicht, wie sich Benutzer bei Ihrer App anmelden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm <a href="http://azure.microsoft.com/de-de/documentation/articles/mobile-services-ios-get-started/">Erste Schritte mit Mobile Services</a>abschließen.

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie zum Erstellen eines neuen Twitter-Kontos die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkID=268287" target="_blank">twitter.com</a>auf.

1. Navigieren Sie zur Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268300" target="_blank">Twitter-Entwickler</a> , melden Sie sich mit Ihren Anmeldeinformationen beim Twitter-Konto an, und klicken Sie dann auf **Neue Anwendung erstellen**.

   	![][1]

2. Geben Sie die Werte für **Name**, **Beschreibung** und **Website** für Ihre App ein. Anschließend geben Sie die URL Ihres mobilen Diensts ein, an den Sie den Pfad _/login/twitter_ in der **Rückruf-URL** anfügen.

	>[WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix "signin-twitter" für Ihren mobilen Dienst als .NET-Dienst, z. B. <code>https://todolist.azure-mobile.net/signin-twitter</code>.

   	![][2]

3.  Lesen und bestätigen Sie die Nutzungsbedingungen am Ende der Seite, lösen Sie das CAPTCHA und klicken Sie auf **Twitter-Anwendung erstellen**. 

   	![][3]

   	Daraufhin wird die Anwendung mit den angegebenen Details registriert.

6. Notieren Sie die Werte für **Consumer key** und **Consumer secret**. 

   	![][4]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
	<p>Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.</p>
    </div>

7. Klicken Sie auf die Registerkarte **Einstellungen**, blättern Sie nach unten und markieren Sie die Option **Anwendung für die Anmeldung bei Twitter freigeben** und klicken Sie auf **Twitter-Einstellungen für diese Anwendung aktualisieren**.

	![][5]

Sie können nun ein Twitter-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die consumer key und consumer secret für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
