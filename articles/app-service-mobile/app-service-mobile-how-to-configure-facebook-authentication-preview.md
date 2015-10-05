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
	ms.date="07/27/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Facebook-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure Mobile Apps zur Verwendung von Facebook als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Facebook-Konto mit verifizierter E-Mail-Adresse und einer Mobiltelefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite [facebook.com] auf, um ein neues Facebook-Konto zu erstellen.

## <a name="register"> </a>Registrieren Ihrer Anwendung für Facebook

1. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Mobile App. Kopieren Sie die **URL**. Diese verwenden Sie zur Konfiguration der Facebook-App.
 
2. Klicken Sie auf **Einstellungen**, **Benutzerauthentifizierung**, und dann auf **Facebook**. Kopieren Sie den **Umleitungs-URI** aus dem Facebook-Blatt. Diesen verwenden Sie mit der Facebook-App.
 
3. Navigieren Sie in einem anderen Browserfenster zur Website für [Facebook-Entwickler], und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.

4. (Optional) Falls Sie sich noch nicht registriert haben, klicken Sie auf **Apps** und dann auf **Register as a Developer**, akzeptieren Sie die Richtlinie, und führen Sie die Registrierungsschritte durch.

5. Klicken Sie auf **My Apps** und dann auf **Create a New App**.

6. Wählen Sie **Website** als Ihre Plattform aus. Wählen Sie einen eindeutigen Namen für Ihre App aus, und klicken Sie dann auf **Create New Facebook App ID**.

7. Wählen Sie aus der Dropdownliste eine Kategorie für Ihre Anwendung aus. Klicken Sie dann auf **Create App ID**.

8. Wählen Sie auf der nächsten Seite die Option **Skip Quick Start** in der rechten oberen Ecke. Dadurch gelangen Sie zum Entwickler-Dashboard für Ihre Anwendung.

9. Klicken Sie im Feld **App Secret** auf **Show**, geben Sie bei Aufforderung Ihr Kennwort ein, und notieren Sie die Werte für **App ID** und **App Secret**. Legen Sie diese im Blatt „Facebook-Authentifizierungseinstellungen“ Ihrer mobilen App fest.

	> [AZURE.NOTE]**Sicherheitshinweis** Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

10. Klicken Sie in der links angezeigten Navigationsleiste auf **Settings**. Geben Sie in **App Domains** die **URL** der Mobile App ein, und geben Sie in **Contact Email** eine E-Mail-Adresse an.

    ![][0]

11. Wenn Sie keinen Websiteabschnitt sehen, klicken Sie auf **Add Platform**, und wählen Sie **Website**. Geben Sie im Feld **Website-URL** die **URL** Ihrer mobilen App ein, und klicken Sie auf **Änderungen speichern**.

12. Klicken Sie auf die Registerkarte **Advanced**, und fügen Sie den **Umleitungs-URI** Ihrer mobilen App hinzu, den Sie zuvor nach **Valid OAuth redirect URIs** kopiert haben. Klicken Sie dann auf **Save Changes**. Der Umleitungs-URI entspricht der URL des Mobile App-Gateways mit angehängtem Pfad _/signin-facebook_. Beispiel: `https://contosogateway.azurewebsites.net/signin-facebook`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

13. Das zum Registrieren der Anwendung verwendete Facebook-Konto fungiert als Administrator der App. Zu diesem Zeitpunkt können sich nur Administratoren bei der Anwendung anmelden. Klicken Sie im links angezeigten Navigationsbereich auf **Status & Review**, um andere Facebook-Konten zu authentifizieren. Klicken Sie dann auf **Ja**, um den allgemeinen öffentlichen Zugriff zu aktivieren.


## <a name="secrets"> </a>Hinzufügen von Facebook-Informationen zu Ihrer mobilen App


12. Navigieren Sie zurück im [Azure-Verwaltungsportal] wieder auf das Blatt „Facebook-Einstellungen“ für Ihre mobile App. Fügen Sie die Werte für App-ID und den geheimen App-Schlüssel ein, die Sie zuvor abgerufen haben. Klicken Sie anschließend auf **Save**.

    ![][1]

Sie können nun Facebook für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication-preview/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-Verwaltungsportal]: https://portal.azure.com/
 

<!---HONumber=August15_HO8-->