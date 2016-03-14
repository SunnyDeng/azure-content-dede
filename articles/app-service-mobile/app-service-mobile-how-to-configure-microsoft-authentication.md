<properties
	pageTitle="Konfigurieren der Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung"
	description="Erfahren Sie, wie Sie die Microsoft-Kontoauthentifizierung für Ihre App Services-Anwendung konfigurieren."
	authors="mattchenderson"
	services="app-service"
	documentationCenter=""
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/24/2016"
	ms.author="mahender"/>

# Konfigurieren Ihrer App Service-Anwendung zur Verwendung der Microsoft-Kontoanmeldung

[AZURE.INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Thema wird demonstriert, wie Sie Azure App Services zur Verwendung eines Microsoft-Kontos als Authentifizierungsanbieter konfigurieren.

## <a name="register-windows-dev-center"> </a>Registrieren Ihrer Windows-App beim Windows Dev Center

Universelle Windows-Apps und Windows Store-Apps müssen über das Windows Dev Center registriert werden. So können Sie künftig auf einfachere Weise Pushbenachrichtigungen für Ihre App konfigurieren.

>[AZURE.NOTE]Für Windows Phone 8-, Windows Phone 8.1 Silverlight- und alle nicht auf Windows-basierenden Apps können Sie diesen Abschnitt überspringen. Wenn Sie bereits Pushbenachrichtigungen für Ihre Windows-App konfiguriert haben, können Sie diesen Abschnitt ebenfalls überspringen.

1. Melden Sie sich beim [Azure-Portal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Sie verwenden diese URL, um Ihre App mit einem Microsoft-Konto zu konfigurieren.

2. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie zum [Windows Dev Center](https://dev.windows.com/dashboard/Application/New), und melden Sie sich mit Ihrem Microsoft-Konto an. Geben Sie einen Namen für Ihre App ein, überprüfen Sie dessen Verfügbarkeit, und klicken Sie anschließend auf **App-Name reservieren**.

3. Öffnen Sie Ihr Windows-App-Projekt in Visual Studio. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie anschließend auf **Store** > **App mit Store verknüpfen...**.

  	![](./media/app-service-mobile-how-to-configure-microsoft-authentication/mobile-app-windows-store-association.png)

4. Klicken Sie im Assistenten auf **Anmelden**, melden Sie sich mit Ihrem Microsoft-Konto an, wählen Sie den reservierten App-Namen aus, und klicken Sie auf **Weiter** > **Zuordnen**. Für eine universelle Windows 8.1-App müssen Sie die Schritte 4 und 5 für das Windows Phone Store-Projekt wiederholen.

6. Klicken Sie auf der Windows Dev Center-Seite für Ihre neue App auf **Dienste** > **Pushbenachrichtigungen**.

7. Klicken Sie auf der Seite **Pushbenachrichtigungen** unter **Windows-Pushbenachrichtigungsdienste (WNS) und Microsoft Azure Mobile Services** auf **Live Services-Website**.

Als Nächstes konfigurieren Sie die Microsoft-Kontoauthentifizierung für Ihre Windows-App.


## <a name="register-microsoft-account"> </a>Registrieren Ihrer App mit einem Microsoft-Konto

Wenn Sie Ihre Windows-App bereits im vorherigen Abschnitt registriert haben, können Sie zu Schritt 4 wechseln.

1. Melden Sie sich beim [Azure-Portal] an, und navigieren Sie zu Ihrer Anwendung. Kopieren Sie die **URL**. Sie verwenden diese URL, um Ihre App mit einem Microsoft-Konto zu konfigurieren.

2. Navigieren Sie im Microsoft Account Developer Center zur Seite [Eigene Anwendungen], und melden Sie sich, falls erforderlich, mit Ihrem Microsoft-Konto an.

3. Klicken Sie auf **Anwendung erstellen**, geben Sie dann einen **Anwendungsnamen** ein, und klicken Sie auf **Ich stimme zu**.

4. Klicken Sie auf **API-Einstellungen**, wählen Sie für **Mobile oder Desktopclient-App** die Option **Ja**, geben Sie die **Umleitungs-URL** für Ihre Anwendung an, und klicken Sie auf **Speichern**.
 
	![][0]

	>[AZURE.NOTE]Der Umleitungs-URI ist die URL Ihrer Anwendung mit angefügtem Pfad _/.auth/login/microsoftaccount/callback_. Beispiel: `https://contoso.azurewebsites.net/.auth/login/microsoftaccount/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

6. Klicken Sie auf **App-Einstellungen**, und notieren Sie sich die Werte unter **Client-ID** und **Geheimer Clientschlüssel**.

    > [AZURE.IMPORTANT] Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.

## <a name="secrets"> </a>Hinzufügen von Microsoft-Kontoinformationen zu Ihrer App Service-Anwendung

1. Navigieren Sie zurück im [Azure-Portal] zu Ihrer Anwendung, und klicken Sie auf **Einstellungen** > **Authentifizierung/Autorisierung**.

2. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, aktivieren Sie es über die Option **Ein**.

3. Klicken Sie auf **Microsoft-Konto** Fügen Sie die ID und den geheimen Schlüssel ein, die Sie zuvor erhalten haben, und aktivieren Sie optional alle Bereiche, die bei Ihrer Anwendung erforderlich sind. Klicken Sie dann auf **OK**.

    ![][1]

	Standardmäßig erfolgt die Authentifizierung über App Service, wobei jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht autorisiert wird. Sie müssen die Benutzer in Ihrem App-Code autorisieren.

4. (Optional:) Um den Zugriff auf Ihre Website ausschließlich auf Benutzer zu beschränken, die von Microsoft authentifiziert wurden, legen Sie **Action to take when request is not authenticated** auf **Microsoft Account** fest. Dadurch müssen alle Anforderungen authentifiziert werden. Alle nicht authentifizierten Anforderungen werden zur Authentifizierung an Microsoft umgeleitet.

5. Klicken Sie auf **Speichern**.

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

<!---HONumber=AcomDC_0302_2016-->