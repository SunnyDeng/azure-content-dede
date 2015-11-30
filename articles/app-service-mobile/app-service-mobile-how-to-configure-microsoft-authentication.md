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
	ms.date="10/30/2015"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird demonstriert, wie Sie Azure App Services zur Verwendung eines Microsoft-Kontos als Authentifizierungsanbieter konfigurieren.


	> [AZURE.NOTE]
	This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. Differences that apply to using the gateway are called out in notes throughout the topic.


## <a name="register"> </a>Registrieren Ihrer Anwendung für ein Microsoft-Konto

1. Melden Sie sich im [Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Diese verwenden Sie, um die Microsoft-Konto-App zu konfigurieren.

2. Navigieren Sie im Microsoft Account Developer Center zur Seite [Eigene Anwendungen], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

4. Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

5. Klicken Sie auf **API-Einstellungen**. Wählen Sie für **Mobile oder Desktopclient-App** die Einstellung **Ja** aus. Geben sie im Feld **Umleitungs-URL** die **Umleitungs-URL** Ihrer Anwendung ein und klicken Sie auf **Speichern**. Der Umleitungs-URI ist die URL Ihrer Anwendung, an die der Pfad _/.auth/login/microsoftaccount/callback_ angefügt wurde. Beispiel: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

	![][0]


	> [AZURE.NOTE]Wenn anstelle der App Service-Authentifizierungs-/-Autorisierungsfunktion das App Service-Gateway verwendet wird, greift Ihre Umleitungs-URL stattdessen auf die Gateway-URL mit dem Pfad _/signin-microsoft_ zurück.


6. Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**.


    > [AZURE.NOTE]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.
	

## <a name="secrets"> </a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer Anwendung


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Microsoft Account**. Paste in the values you obtained earlier and click **Save**.


7. Wenn Sie sich wieder im [Azure-Verwaltungsportal] befinden, navigieren Sie zu Ihrer Anwendung. Klicken Sie auf **Einstellungen**, und dann auf **Authentifizierung/Autorisierung**.

8. Wenn die Authentifizierungs-/Autorisierungsfunktion nicht aktiviert ist, stellen Sie den Schalter auf **Ein**.

9. Klicken Sie auf **Microsoft-Konto** Fügen Sie die ID und den geheimen Schlüssel ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.

    ![][1]
	
11. Standardmäßig erfolgt die Anmeldung über App Service, der jedoch den Zugriff auf die Inhalte Ihrer Website und APIs nicht einschränkt. Diese Aufgabe erfüllt der App Code. Wenn die Website vollständig von Microsoft Account-Anmeldung geschützt werden soll, stellen Sie die **Auszuführende Aktion bei nicht authentifizierter Anforderung** in der Dropdownliste auf die Option **Microsoft-Konto** ein. Dadurch müssen alle Anforderungen authentifiziert werden und nicht authentifizierte Anforderungen werden zur Microsoft-Kontoanmeldung umgeleitet.

11. Klicken Sie auf **Speichern**.


Sie können nun ein Microsoft-Konto für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Authenticate your app with Live Connect Single Sign-On: [Windows](windows-liveconnect) -->



<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/app-service-microsoftaccount-redirect.png
[1]: ./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-microsoftaccount-settings.png

<!-- URLs. -->

[Eigene Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Azure-Verwaltungsportal]: https://portal.azure.com/

<!---HONumber=Nov15_HO4-->