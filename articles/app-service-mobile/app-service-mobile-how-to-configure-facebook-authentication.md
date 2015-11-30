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
	ms.date="10/29/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Facebook-Anmeldung

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema erfahren Sie, wie Sie Azure App Services für die Verwendung von Facebook als Authentifizierungsanbieter konfigurieren.

Sie benötigen ein Facebook-Konto mit verifizierter E-Mail-Adresse und einer Mobiltelefonnummer, um den in diesem Thema beschriebenen Vorgang abzuschließen. Rufen Sie die Seite [facebook.com] auf, um ein neues Facebook-Konto zu erstellen.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrieren Ihrer Anwendung für Facebook

1. Melden Sie sich im [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Diese verwenden Sie zur Konfiguration der Facebook-App.
 
2. Navigieren Sie in einem anderen Browserfenster zur Website für [Facebook-Entwickler], und melden Sie sich mit den Anmeldeinformationen für Ihr Facebook-Konto an.

3. (Optional) Falls Sie sich noch nicht registriert haben, klicken Sie auf **Apps** und dann auf **Register as a Developer**, akzeptieren Sie die Richtlinie, und führen Sie die Registrierungsschritte durch.

4. Klicken Sie auf **My Apps** und dann auf **Create a New App**.

5. Wählen Sie **Website** als Ihre Plattform aus. Wählen Sie einen eindeutigen Namen für Ihre App aus, und klicken Sie dann auf **Create New Facebook App ID**.

6. Wählen Sie aus der Dropdownliste eine Kategorie für Ihre Anwendung aus. Klicken Sie dann auf **Create App ID**.

7. Wählen Sie auf der nächsten Seite die Option **Skip Quick Start** in der rechten oberen Ecke. Dadurch gelangen Sie zum Entwickler-Dashboard für Ihre Anwendung.

8. Klicken Sie im Feld **App Secret** auf **Show**, geben Sie bei Aufforderung Ihr Kennwort ein, und notieren Sie die Werte für **App ID** und **App Secret**. Ihre Anwendung wird später mit diesen Angaben konfiguriert.

	> [AZURE.NOTE]**Sicherheitshinweis** Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

9. Klicken Sie in der links angezeigten Navigationsleiste auf **Settings**. Geben Sie in **App Domains** die **URL** der Mobile App ein, und geben Sie in **Contact Email** eine E-Mail-Adresse an.

    ![][0]

10. Wenn Sie keinen Websiteabschnitt sehen, klicken Sie auf **Add Platform**, und wählen Sie **Website**. Geben Sie im Feld **Website-URL** die **URL** Ihrer mobilen App ein, und klicken Sie auf **Änderungen speichern**.

11. Klicken Sie auf die Registerkarte **Advanced**, und fügen Sie den Umleitungs-URI Ihrer Anwendung zu **Valid OAuth redirect URIs** hinzu. Klicken Sie dann auf **Save Changes**. Der Umleitungs-URI ist die URL Ihrer Anwendung, an die der Pfad _/.auth/login/facebook/callback_ angefügt wurde. Beispiel: `https://contoso.azurewebsites.net/.auth/login/facebook/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.


	> [AZURE.NOTE]Wenn anstelle der App Service-Authentifizierung/-Autorisierung das App Service-Gateway verwendet wird, greift Ihre Umleitungs-URL stattdessen auf die Gateway-URL mit dem Pfad _/signin-facebook_ zurück.


12. Das zum Registrieren der Anwendung verwendete Facebook-Konto fungiert als Administrator der App. Zu diesem Zeitpunkt können sich nur Administratoren bei der Anwendung anmelden. Klicken Sie im links angezeigten Navigationsbereich auf **Status & Review**, um andere Facebook-Konten zu authentifizieren. Klicken Sie dann auf **Ja**, um den allgemeinen öffentlichen Zugriff zu aktivieren.


## <a name="secrets"> </a>Hinzufügen von Facebook-Informationen zu Ihrer Anwendung


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Facebook**. Paste in the values you obtained earlier and click **Save**.


13. Navigieren Sie im [Azure-Verwaltungsportal] zu Ihrer Anwendung. Klicken Sie auf **Einstellungen** und anschließend auf **Authentifizierung/Autorisierung**.

14. Ist das Authentifizierungs-/Autorisierungsfeature nicht aktiviert, aktivieren Sie es.

15. Klicken Sie auf **Facebook**. Fügen Sie die ID und den geheimen Schlüssel ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die Ihre Anwendung benötigt. Klicken Sie dann auf **OK**.

    ![][1]
	
16. Standardmäßig erfolgt die Anmeldung über App Service, dabei wird jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht eingeschränkt. Diese Aufgabe erfüllt der App-Code. Wenn die Website vollständig durch die Facebook-Anmeldung geschützt werden soll, wählen Sie in der Dropdownliste **Auszuführende Aktion bei nicht authentifizierter Anforderung** die Option **Facebook** aus. Dadurch müssen alle Anforderungen authentifiziert werden. Nicht authentifizierte Anforderungen werden zur Facebook-Anmeldung umgeleitet.

17. Klicken Sie auf **Speichern**.


Sie können nun Facebook für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->
[0]: ./media/app-service-mobile-how-to-configure-facebook-authentication/app-service-facebook-dashboard.png
[1]: ./media/app-service-mobile-how-to-configure-facebook-authentication/mobile-app-facebook-settings.png

<!-- URLs. -->
[Facebook-Entwickler]: http://go.microsoft.com/fwlink/p/?LinkId=268286
[facebook.com]: http://go.microsoft.com/fwlink/p/?LinkId=268285
[Get started with authentication]: /de-DE/develop/mobile/tutorials/get-started-with-users-dotnet/
[Azure-Verwaltungsportal]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->