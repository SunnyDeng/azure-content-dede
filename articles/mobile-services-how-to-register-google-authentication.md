<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Register your apps for Google login with Mobile Services" authors="" solutions="" manager="" editor="" />

Registrieren Ihrer App für die Google-Anmeldung mit Mobile Services
===================================================================

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Google zur Authentifizierung mit Azure Mobile Services zu verwenden.

**Hinweis**

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302), um ein neues Google-Konto zu erstellen.

1.  Navigieren Sie zur [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303)-Website, melden Sie sich mit Ihren Google-Anmeldeinformationen an, und klicken Sie auf **Create project**.

    ![][1]   

2.  Klicken Sie auf **API-Zugriff** und anschließend auf **ID für OAuth 2.0-Client erstellen...**.

    ![][2]

3.  Geben Sie unter **Markeninformationen** Ihren **Produktnamen** ein und klicken Sie auf **Weiter**.

    ![][3]

4.  Wählen Sie unter **Client ID-Einstellungen** den Wert **Webanwendung** aus, geben Sie die URL Ihres mobilen Dienstes unter **Ihre Website bzw. Hostname** ein, klicken Sie auf **weitere Optionen**, ersetzen Sie die generierte URL unter **Autorisierte Weiterleitungs-URIs** durch die URL Ihres mobilen Dienstes mit dem Präfix */login/google* und klicken Sie auf **Client-ID erstellen**.

    ![][4]

    > [WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Dienstes mit dem Präfix *signin-google* für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-google`.

5.  Notieren Sie unter **Client-ID für Webanwendungen** die Werte für **Client-ID** und **Geheimer Clientschlüssel**.

    ![][5]

    **Sicherheitshinweis**

    Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei.

Sie können nun ein Google-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die Werte für Client-ID und geheimen Clientschlüssel für Mobile Services bereitstellen.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-developers.png
[2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
[3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
[4]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
[5]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-app-details.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/
