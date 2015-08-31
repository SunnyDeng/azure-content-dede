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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Google-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

In diesem Thema wird veranschaulicht, wie Sie Mobile Apps zur Verwendung von Google als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

## <a name="register"> </a>Registrieren Ihrer Anwendung für Google


1. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Mobile App. Kopieren Sie die **URL**. Sie werden sie später mit der Google-App verwenden.
 
2. Klicken Sie auf **Einstellungen**, **Benutzerauthentifizierung**, und klicken Sie dann auf **Google**. Kopieren Sie den **Umleitungs-URI**. Diese verwenden Sie zur Konfiguration der Google-App.

3. Navigieren Sie zur <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google APIs</a>-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project**, geben Sie einen **Project name** an, und klicken Sie auf **Create**.

4. Klicken Sie in der links angezeigten Navigationsleiste auf **API und Authentifizierung**. Klicken Sie dann auf **Zustimmungsbildschirm**. Geben Sie Ihre **E-Mail-Adresse** und einen Wert für **Produktname** ein. Klicken Sie anschließend auf **Save**.

5. Wählen Sie ebenfalls unter **API und Authentifizierung** die Einstellung **APIs**, und aktivieren Sie **Google+ API**. Sie befindet sich unter **Social-APIs**. Sie können auch einfach nach der **Google+-API** suchen.

6. Wählen Sie erneut unter **API und Authentifizierung** die Einstellung **Zugangsdaten**, und klicken Sie dann auf **Neue Client-ID erstellen**.

7. Wählen Sie **Webanwendung**. Fügen Sie die zuvor kopierte **URL** in **Authorized JavaScript Origins** ein, und ersetzen Sie dann die generierte URL in **Autorisierte Umleitungs-URI** durch den zuvor kopierten **Umleitungs-URI** der Mobile App. Der URI ist Ihr Mobile App-Gateway mit dem Pfad _/signin-google_ am Ende. Beispiel: `https://contosogateway.azurewebsites.net/signin-google`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden. Klicken Sie dann auf **Client-ID erstellen**.

     ![][0]

8. Notieren Sie im nächsten Bildschirm unterhalb von **Client-ID für Webanwendungen** die Werte für **Client-ID** und **Clientschlüssel**.

    > [AZURE.IMPORTANT]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.


## <a name="secrets"> </a>Hinzufügen von Google-Informationen zu Ihrer mobilen App

7. Fügen Sie zurück im [Azure-Verwaltungsportal] im Blatt mit den Google-Einstellungen für Ihre Mobile App die Werte für die Client-ID und den geheimen Client-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

     ![][1]


Sie können nun Google für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-google-authentication-preview/mobile-app-google-settings.png

<!-- URLs. -->

[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-Verwaltungsportal]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->