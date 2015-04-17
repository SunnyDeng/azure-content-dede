<properties 
	pageTitle="Konfigurieren der Google-Authentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Google-Authentifizierung für Ihre App Services-Anwendung konfigurieren." 
    services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Google-Anmeldung

In diesem Thema wird veranschaulicht, wie Sie Azure App Services zur Verwendung von Google als Authentifizierungsanbieter konfigurieren. 

Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschließen. Um ein neues Google-Konto zu erstellen, wechseln Sie zu <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

## <a name="register"> </a>Registrieren Ihrer Anwendung für Google

1. Navigieren Sie zur <a href="http://go.microsoft.com/fwlink/p/?LinkId=268303" target="_blank">Google-APIs</a>-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Projekt erstellen**, geben Sie einen Wert für **Projektname** an, und klicken Sie auf **Erstellen**.

2. Klicken Sie in der links angezeigten Navigationsleiste auf **APIs und Authentifizierung**. Klicken Sie dann auf **Zustimmungsbildschirm**. Geben Sie Ihre **E-Mail-Adresse** und einen Wert für **Produktname** ein. Klicken Sie anschließend auf **Speichern**. 

3. Wählen Sie ebenfalls unter **APIs und Authentifizierung** die Einstellung **APIs**, und aktivieren Sie **Google+ API**.

4. Wählen Sie erneut unter **APIs und Authentifizierung** die Einstellung **Zugangsdaten**, und klicken Sie dann auf **Neue Client-ID erstellen**.

5. Wählen Sie **Webanwendung**. Geben Sie in **Autorisierte JavaScript-Quellen** Ihren App Services-Gateway-URL ein, und ersetzen Sie dann den generierten URL in **Autorisierte Weiterleitungs-URIs** durch die URL Ihres Gateways mit angehängtem Pfad _/signin-google_. Beispiel: `https://contoso.azurewebsites.net/signin-google`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden. Klicken Sie dann auf **Client-ID erstellen**.

     ![][0]

6. Notieren Sie im nächsten Bildschirm unterhalb von **Client-ID für Webanwendungen** die Werte für **Client-ID** und **Clientschlüssel**. 

    > [AZURE.IMPORTANT] Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.


## <a name="secrets"> </a>Hinzufügen von Google-Informationen zu Ihrer mobilen App

7. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrem App Services-Gateway.

8. Wählen Sie unter **Einstellungen** die Option **Identität**, und wählen Sie dann **Google**. Fügen Sie die Werte für Client-ID und den geheimen Clientschlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie dann auf **Speichern**.

     ![][1]


Sie können nun Google für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../includes/app-service-mobile-related-content-get-started-users.md)]


<!-- Anchors. -->

<!-- Images. -->

[0]: ./media/app-service-how-to-configure-google-authentication/app-service-google-redirect.png
[1]: ./media/app-service-how-to-configure-google-authentication/app-service-google-settings.png

<!-- URLs. -->

[Google-APIs]: http://go.microsoft.com/fwlink/p/?LinkId=268303

[Azure-Verwaltungsportal]: https://portal.azure.com/

<!--HONumber=49-->