<properties linkid="develop-mobile-how-to-guides-register-for-twitter-authentication" urlDisplayName="Register for Twitter Authentication" pageTitle="Register for Twitter authentication - Mobile Services" metaKeywords="Azure registering application, Azure Twitter authentication, application authenticate, authenticate mobile services, Mobile Services Twitter" description="Learn how to use Twitter authentication with your Azure Mobile Services application." metaCanonical="" services="" documentationCenter="" title="Register your apps for Twitter login with Mobile Services" authors="" solutions="" manager="" editor="" />

Registrieren Ihrer App für die Twitter-Anmeldung mit Mobile Services
====================================================================

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Twitter zur Authentifizierung mit Azure Mobile Services zu verwenden.

**Hinweis**

Sie benötigen ein Twitter-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite [twitter.com](http://go.microsoft.com/fwlink/p/?LinkID=268287) auf, um ein neues Twitter-Konto zu erstellen.

1.  Navigieren Sie zur [Twitter-Entwickler](http://go.microsoft.com/fwlink/p/?LinkId=268300)-Webseite, melden Sie sich mit Ihren Twitter-Anmeldeinformationen an und klicken Sie auf **Neue Anwendung erstellen**.

    ![][1]

2.  Geben Sie **Name**, **Beschreibung** und **Website** für Ihre App ein und tragen Sie die URL Ihres mobilen Dienstes unter **Rückruf-URL** ein.

    ![][2]

    > [WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Dienstes mit dem Präfix *signin-google* für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-twitter`.
    > Der Wert **Website** muss ausgefüllt werden, wird jedoch nicht verwendet.

3.  Lesen und bestätigen Sie die Nutzungsbedingungen am Ende der Seite, lösen Sie das CAPTCHA und klicken Sie auf **Twitter-Anwendung erstellen**.

	![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png)
	
	Daraufhin wird die Anwendung mit den angegebenen Details registriert.

4.  Notieren Sie die Werte für **Consumer key** und **Consumer secret**.

    ![][4]

    **Sicherheitshinweis**

    Consumer secret ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

5.  Klicken Sie auf die Registerkarte **Einstellungen**, blättern Sie nach unten und markieren Sie die Option **Anwendung für die Anmeldung bei Twitter freigeben** und klicken Sie auf **Twitter-Einstellungen für diese Anwendung aktualisieren**.

    ![](./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png)

Sie können nun ein Twitter-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die consumer key und consumer secret für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-developers.png
[2]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app1.png
[3]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-app2.png
[4]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-app-details.png
[5]: ./media/mobile-services-how-to-register-twitter-authentication/mobile-services-twitter-register-settings.png

<!-- URLs. -->

[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
