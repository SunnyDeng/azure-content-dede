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
	ms.date="02/04/2016"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird demonstriert, wie Sie Azure App Services zur Verwendung eines Microsoft-Kontos als Authentifizierungsanbieter konfigurieren.


> [AZURE.NOTE]
Dieses Thema veranschaulicht die Verwendung der Authentifizierungs- und Autorisierungsfeatures von App Service. Dadurch wird für die meisten Anwendungen das App Service-Gateway ersetzt. Unterschiede, die für die Verwendung des Gateways gelten, werden in diesem Thema in Hinweisen hervorgehoben.


## <a name="register"> </a>Registrieren Ihrer Anwendung für ein Microsoft-Konto

1. Melden Sie sich beim [Azure-Portal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Diese verwenden Sie, um die Microsoft-Konto-App zu konfigurieren.

2. Navigieren Sie im Microsoft Account Developer Center zur Seite [Eigene Anwendungen], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

4. Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

5. Klicken Sie auf **API-Einstellungen**. Wählen Sie für **Mobile oder Desktopclient-App** die Einstellung **Ja** aus. Geben sie im Feld **Umleitungs-URL** die **Umleitungs-URL** Ihrer Anwendung ein, und klicken Sie auf **Speichern**. Der Umleitungs-URI ist die URL Ihrer Anwendung mit angefügtem Pfad _/.auth/login/microsoftaccount/callback_. Beispiel: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

	![][0]


	> [AZURE.NOTE]
	Wenn anstelle der App Service-Authentifizierungs- oder -Autorisierungsfunktion das App Service-Gateway verwendet wird, greift Ihre Umleitungs-URL stattdessen auf die Gateway-URL mit dem Pfad _/signin-microsoft_ zurück.


6. Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**.


    > [AZURE.NOTE] Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.


## <a name="secrets"> </a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer Anwendung

> [AZURE.NOTE]
Wenn Sie das App Service-Gateway verwenden, ignorieren Sie diesen Abschnitt und navigieren stattdessen im Portal zu Ihrem Gateway. Wählen Sie unter **Einstellungen** die Option **Identität** und dann **Microsoft-Konto** aus. Fügen Sie die erhaltenen abgerufenen Werte ein, und klicken Sie auf **Speichern**.


7. Navigieren Sie im [Azure-Portal] wieder zu Ihrer Anwendung. Klicken Sie auf **Einstellungen** und anschließend auf **Authentifizierung/Autorisierung**.

8. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, aktivieren Sie es über die Option **Ein**.

9. Klicken Sie auf **Microsoft-Konto** Fügen Sie die ID und den geheimen Schlüssel ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.

    ![][1]

	Standardmäßig erfolgt die Authentifizierung über App Service, wobei jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht autorisiert wird. Sie müssen die Benutzer in Ihrem App-Code autorisieren.

17. (Optional:) Um den Zugriff auf Ihre Website ausschließlich auf Benutzer zu beschränken, die von Microsoft authentifiziert wurden, legen Sie **Action to take when request is not authenticated** auf **Microsoft Account** fest. Dadurch müssen alle Anforderungen authentifiziert werden. Alle nicht authentifizierten Anforderungen werden zur Authentifizierung an Microsoft umgeleitet.

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
[Azure-Portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0211_2016-->