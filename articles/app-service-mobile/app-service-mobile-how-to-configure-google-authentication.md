<properties
	pageTitle="Konfigurieren der Google-Authentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Google-Authentifizierung für Ihre App Services-Anwendung konfigurieren."
    services="app-service\mobile"
	documentationCenter=""
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/28/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Nutzung der Google-Anmeldung

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure App Service zur Verwendung von Google als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrieren Ihrer Anwendung für Google

1. Melden Sie sich im [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Diese verwenden Sie zur Konfiguration der Google-App.
 
2. Navigieren Sie zur [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303)-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project**, geben Sie einen **Project name** an, und klicken Sie auf **Create**.

3. Klicken Sie in der linken Navigationsleiste auf **API & Auth** und anschließend unter **Social APIs** auf **Google+ API** > **Enable API**.

4. Klicken Sie auf **API & Auth** > **Credentials** > **OAuth consent screen**, wählen Sie dann Ihre **Email address** aus, geben Sie einen Wert für **Product Name** ein, und klicken Sie auf **Save**.

5. Klicken Sie auf der Registerkarte **Credentials** auf **Add credentials** > **OAuth 2.0 client ID**, und wählen Sie dann **Web application**.

6. Fügen Sie die zuvor kopierte App Service-**URL** in **Authorized JavaScript Origins** ein, und fügen Sie dann den zuvor kopierten **Umleitungs-URI** in **Authorized Redirect-URI** ein. Der Umleitungs-URI ist die URL Ihrer Anwendung, an die der Pfad _/.auth/login/google/callback_ angefügt wurde. Beispiel: `https://contoso.azurewebsites.net/.auth/login/google/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden. Klicken Sie dann auf **Erstellen**.


	> [AZURE.NOTE]Wenn anstelle der App Service-Authentifizierungs-/-Autorisierungsfunktion das App Service-Gateway verwendet wird, greift Ihre Umleitungs-URL stattdessen auf die Gateway-URL mit dem Pfad _/signin-google_ zurück.


7. Notieren Sie sich auf dem nächsten Bildschirm die Werte für die Client-ID und den geheimen Clientschlüssel.


    > [AZURE.IMPORTANT]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.


## <a name="secrets"> </a>Hinzufügen von Google-Informationen zu Ihrer Anwendung


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Google**. Paste in the values you obtained earlier and click **Save**.


8. Wenn Sie sich wieder im [Azure-Verwaltungsportal] befinden, navigieren Sie zu Ihrer Anwendung. Klicken Sie auf **Einstellungen**, und dann auf **Authentifizierung/Autorisierung**.

9. Wenn die Authentifizierungs-/Autorisierungsfunktion nicht aktiviert ist, stellen Sie den Schalter auf **Ein**.

10. Klicken Sie auf **Google**. Fügen Sie die ID und den geheimen Schlüssel ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.

    ![][1]
	
11. Standardmäßig erfolgt die Anmeldung über App Service, der jedoch den Zugriff auf die Inhalte Ihrer Website und APIs nicht einschränkt. Diese Aufgabe erfüllt der App Code. Wenn die Website vollständig von Google-Anmeldung geschützt werden soll, stellen Sie die **Auszuführende Aktion bei nicht authentifizierter Anforderung** in der Dropdownliste auf die Option **Google** ein. Dadurch müssen alle Anforderungen authentifiziert werden und nicht authentifizierte Anforderungen werden zur Google-Anmeldung umgeleitet.

12. Klicken Sie auf **Speichern**.

Sie können nun Google für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-Verwaltungsportal]: https://portal.azure.com/
 

<!---HONumber=Nov15_HO4-->