<properties
	pageTitle="Konfigurieren der Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung konfigurieren."
	authors="mattchenderson" 
	services="app-service\mobile"
	documentationCenter=""
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Microsoft-Kontoanmeldung

In diesem Thema wird veranschaulicht, wie Sie Azure App Services zur Verwendung eines Microsoft-Kontos als Authentifizierungsanbieter konfigurieren.

## <a name="register"> </a>Registrieren Ihrer Anwendung für ein Microsoft-Konto

1. Navigieren Sie im Microsoft Account Developer Center zur Seite [Eigene Anwendungen], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

2. Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

3. Klicken Sie auf **API-Einstellungen**. Wählen Sie für **Mobile oder Desktopclient-App** die Einstellung **Ja** aus. Geben Sie im Feld **Umleitungs-URL** die URL Ihres Gateways mit angehängtem Pfad _/signin-microsoft_ ein. Beispiel: `https://contosogateway.azurewebsites.net/signin-microsoft`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden. Klicken Sie nach der Eingabe der Umleitungs-URL auf **Speichern**.

	![][0]

	>[AZURE.NOTE]Zur Registrierung einer vorhandenen Microsoft-Konto-App müssen Sie u. U. zuerst **Erweiterte Umleitungssicherheit** aktivieren.

4. Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**.

    > [AZURE.NOTE]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.


## <a name="secrets"> </a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer mobilen App

5. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrem App Services-Gateway.

6. Klicken Sie unter **Einstellungen** auf **Identität**, und wählen Sie anschließend **Microsoft-Konto**. Fügen Sie die Werte für App-ID und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

    ![][1]

Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication-preview/app-service-microsoftaccount-settings.png

<!-- URLs. -->

[Eigene Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-Verwaltungsportal]: https://portal.azure.com/
 

<!---HONumber=July15_HO3-->