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

# Konfigurieren Ihrer Anwendung zur Verwendung der Google-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

In diesem Thema wird veranschaulicht, wie Sie Mobile Apps zur Verwendung von Google als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

1. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Mobile App. Kopieren Sie die **URL**. Sie werden sie später mit der Google-App verwenden.
 
2. Klicken Sie auf **Einstellungen**, **Benutzerauthentifizierung**, und klicken Sie dann auf **Google**. Kopieren Sie den **Umleitungs-URI**. Diese verwenden Sie zur Konfiguration der Google-App.

3. Navigieren Sie zur [Google APIs](http://go.microsoft.com/fwlink/p/?LinkId=268303)-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project**, geben Sie einen **Project name** an, und klicken Sie auf **Create**.

4. Klicken Sie in der linken Navigationsleiste auf **API & Auth** und anschließend unter **Social APIs** auf **Google+ API** > **Enable API**.

5. Klicken Sie auf **API & Auth** > **Credentials** > **OAuth consent screen**, wählen Sie dann Ihre **Email address** aus, geben Sie einen Wert für **Product Name** ein, und klicken Sie auf **Save**.

6. Klicken Sie auf der Registerkarte **Credentials** auf **Add credentials** > **OAuth 2.0 client ID**, und wählen Sie dann **Web application**.

7. Fügen Sie die zuvor kopierte Mobile Apps-**URL** in **Authorized JavaScript Origins** ein, und fügen Sie dann den zuvor kopierten **Umleitungs-URI** in **Authorized Redirect-URI** ein. Der Umleitungs-URI ist Ihr Mobile App-Gateway mit angehängtem Pfad _/signin-google_. Beispiel: `https://contosogateway.azurewebsites.net/signin-google`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden. Klicken Sie dann auf **Erstellen**.

8. Notieren Sie sich auf dem nächsten Bildschirm die Werte für die Client-ID und den geheimen Clientschlüssel.

    > [AZURE.IMPORTANT]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

9. Fügen Sie im [Azure-Verwaltungsportal] auf dem Blatt mit den Google-Einstellungen für Ihre Mobile App die Werte für die Client-ID und den geheimen Client-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

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
 

<!---HONumber=Oct15_HO3-->