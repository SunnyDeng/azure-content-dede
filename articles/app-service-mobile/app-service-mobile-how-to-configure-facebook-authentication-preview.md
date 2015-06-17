<properties
	pageTitle="Konfigurieren der Facebook-Authentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Facebook-Authentifizierung für Ihre App Services-Anwendung konfigurieren."
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
	ms.date="05/26/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Facebook-Anmeldung

In diesem Thema wird veranschaulicht, wie Sie Azure App Services zur Verwendung von Facebook als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Facebook-Konto mit verifizierter E-Mail-Adresse und einer Mobiltelefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite [facebook.com] auf, um ein neues Facebook-Konto zu erstellen.

## <a name="register"> </a>Registrieren Ihrer Anwendung für Facebook

1. Navigieren Sie zur Website für [Facebook-Entwickler], und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.

2. (Optional) Falls Sie sich noch nicht registriert haben, klicken Sie auf **Apps** und dann auf **Register as a Developer**, akzeptieren Sie die Richtlinie, und führen Sie die Registrierungsschritte durch.

3. Klicken Sie auf **Apps** und dann auf **Create a New App**.

4. Wählen Sie **Website** als Ihre Plattform aus. Wählen Sie einen eindeutigen Namen für Ihre App aus, und klicken Sie dann auf **Create New Facebook App ID**.

5. Wählen Sie aus der Dropdownliste eine Kategorie für Ihre Anwendung aus. Klicken Sie dann auf **Create App ID**.

6. Wählen Sie auf der nächsten Seite die Option **Skip Quick Start** in der rechten oberen Ecke. Dadurch gelangen Sie zum Entwickler-Dashboard für Ihre Anwendung.

7. Klicken Sie im Feld **App Secret** auf **Show**, geben Sie bei Aufforderung Ihr Kennwort ein, und notieren Sie die Werte für **App ID** und **App Secret**.

	> [AZURE.NOTE]**Sicherheitshinweis** Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

8. Klicken Sie in der links angezeigten Navigationsleiste auf **Settings**. Geben Sie in **App Domains** die URL des Gateways ein, und geben Sie in **Contact Email** eine E-Mail-Adresse an.

    ![][0]

9. Wenn Sie keinen Websiteabschnitt sehen, klicken Sie auf **Add Platform**, und wählen Sie **Website**. Geben Sie in **Site URL** die URL Ihres App Service-Gateways ein, und klicken Sie dann auf **Save Changes**.

10. Klicken Sie auf die Registerkarte **Advanced**, und fügen Sie Ihren Umleitungs-URI zu **Valid OAuth redirect URIs** hinzu. Klicken Sie dann auf **Save Changes**. Der Umleitungs-URI entspricht der URL des Gateways mit angehängtem Pfad _/signin-facebook_. Beispiel: `https://contosogateway.azurewebsites.net/signin-facebook`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

11. Das zum Registrieren der Anwendung verwendete Facebook-Konto fungiert als Administrator der App. Zu diesem Zeitpunkt können sich nur Administratoren bei der Anwendung anmelden. Klicken Sie im links angezeigten Navigationsbereich auf **Status & Review**, um andere Facebook-Konten zu authentifizieren. Klicken Sie dann auf **Ja**, um den allgemeinen öffentlichen Zugriff zu aktivieren.


## <a name="secrets"> </a>Hinzufügen von Facebook-Informationen zu Ihrer mobilen App


12. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrem App Services-Gateway.

13. Wählen Sie unter **Einstellungen** die Option **Identität**, und wählen Sie dann **Facebook**. Fügen Sie die Werte für App-ID und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

    ![][1]

Sie können nun Facebook für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-settings.png

<!-- URLs. -->
[Facebook-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-Verwaltungsportal]: https://portal.azure.com/
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->