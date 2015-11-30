<properties 
	pageTitle="Konfigurieren der Azure Active Directory-Authentifizierung für die App Services-Anwendung" 
	description="Erfahren Sie, wie Sie die Azure Active Directory-Authentifizierung für die App Services-Anwendung konfigurieren." 
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
	ms.date="10/29/2015" 
	ms.author="mahender"/>

# So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure App Services zur Verwendung von Azure Active Directory als Authentifizierungsanbieter konfigurieren.


	> [AZURE.NOTE] This topic demonstrates use of the App Service Authentication / Authorization feature. This replaces the App Service gateway for most applications. If using the gateway, please see the [alternative method]. Differences that apply to using the gateway are called out in notes throughout that section.


## <a name="express"> </a>Konfigurieren von Azure Active Directory mit Express-Einstellungen

13. Navigieren Sie im [Azure-Verwaltungsportal] zu Ihrer Anwendung. Klicken Sie auf **Einstellungen** und anschließend auf **Authentifizierung/Autorisierung**.

14. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, aktivieren Sie es über die Option **Ein**.

15. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Express** unter **Verwaltungsmodus**.

16. Klicken Sie auf **OK**, um die Anwendung in Azure Active Directory zu registrieren. Dadurch wird eine neue Registrierung erstellt. Wenn Sie stattdessen eine vorhandene Registrierung auswählen möchten, klicken Sie auf **Select an existing app**, und suchen Sie nach dem Namen einer zuvor erstellten Registrierung des Mandanten. Klicken Sie auf die Registrierung, um sie auszuwählen, und klicken Sie auf **OK**. Klicken Sie dann auf dem Blatt „Azure Active Directory-Einstellungen“ auf **OK**.

    ![][0]
	
16. Standardmäßig erfolgt die Anmeldung über App Service, dabei wird jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht eingeschränkt. Diese Aufgabe erfüllt der App-Code. Wenn die Website vollständig durch die Azure Active Directory-Anmeldung geschützt werden soll, wählen Sie in der Dropdownliste **Action to take when request is not authenticated** die Option **Azure Active Directory** aus. Dadurch müssen alle Anforderungen authentifiziert werden. Nicht authentifizierte Anforderungen werden zur Azure Active Directory-Anmeldung umgeleitet.

17. Klicken Sie auf **Speichern**.

Sie können nun Azure Active Directory für die Authentifizierung in Ihrer App verwenden.

## <a name="advanced"> </a>(Alternative Methode:) Manuelles Konfigurieren von Azure Active Directory mit erweiterten Einstellungen
Sie können Konfigurationseinstellungen auch manuell bereitstellen Dies ist die bevorzugte Lösung, falls der zu verwendende AAD-Mandant sich von dem Mandanten unterscheidet, mit dem die Anmeldung in Azure erfolgt. Um die Konfiguration abzuschließen, müssen Sie zunächst eine Registrierung in Azure Active Directory erstellen, und App Service dann einige Registrierungsdetails liefern.

### <a name="register"> </a>Registrieren Ihrer Anwendung bei Azure Active Directory

1. Melden Sie sich bei der [Vorschau des Azure-Verwaltungsportals] an, und navigieren Sie zur gewünschten Anwendung. Kopieren Sie die **URL**. Diese dient zum Konfigurieren Ihrer Azure Active Directory-App.

3. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu **Active Directory**.

    ![][2]

4. Wählen Sie Ihr Verzeichnis und anschließend die Registerkarte **Anwendungen** im oberen Bereich aus. Klicken Sie im unteren Bereich auf **HINZUFÜGEN**, um eine neue App-Registrierung zu erstellen.

5. Klicken Sie auf **Add an application my organization is developing**.

6. Geben Sie im Add Application-Assistenten einen **Namen** für Ihre Anwendung ein und klicken Sie auf den Typ **Web Application And/Or Web API**. Klicken Sie dann auf Continue.

7. Fügen Sie in das Feld **Anmelde-URL** die zuvor kopierte URL der Anwendung ein. Geben Sie dieselbe URL in das Feld **App-ID-URI** ein. Klicken Sie dann auf Continue.

8. Nachdem die Anwendung hinzugefügt wurde, klicken Sie auf die Registerkarte **Konfigurieren**. Ändern Sie die **Antwort-URL** unter **Einmaliges Anmelden** in die URL Ihrer Anwendung mit angehängtem Pfad _/.auth/login/aad/callback_. Beispiel: `https://contoso.azurewebsites.net/.auth/login/aad/callback`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

    ![][3]
	
	
	> [AZURE.NOTE]Wenn anstelle des Authentifizierungs-/Autorisierungsfeatures von App Service das App Service-Gateway verwendet wird, greift Ihre Umleitungs-URL stattdessen auf die Gateway-URL mit dem Pfad _/signin-aad_ zurück.


9. Klicken Sie auf **Speichern**. Kopieren Sie dann die **Client-ID** für die App. Ihre Anwendung wird später anhand dieser Angabe konfiguriert.

10. Klicken Sie in der Befehlsleiste unten auf **Endpunkte anzeigen**, kopieren Sie dann die URL des **Verbundmetadatendokuments**, und laden Sie dieses herunter, oder öffnen Sie es im Browser.

11. Innerhalb des **EntityDescriptor**-Stammelements sollte ein **EntityID**-Attribut gemäß `https://sts.windows.net/` gefolgt von einer GUID (Mandanten-ID) vorhanden sein, die speziell für Ihren Mandanten gültig ist. Kopieren Sie diese ID – sie dient als **Aussteller-URL**. Ihre Anwendung wird später anhand dieser Angabe konfiguriert.

### <a name="secrets"> </a>Hinzufügen von Azure Active Directory-Informationen zu Ihrer Anwendung


	> [AZURE.NOTE]
	If using the App Service Gateway, ignore this section and instead navigate to your gateway in the portal. Select **Settings**, **Identity**, and then **Azure Active Directory**. Paste in the ClientID and add the tenant ID to the **Allowed Tenants** list. Click **Save**.


13. Navigieren Sie in der [Vorschau des Azure-Verwaltungsportals] zu Ihrer Anwendung. Klicken Sie auf **Einstellungen** und anschließend auf **Authentifizierung/Autorisierung**.

14. Falls das Authentifizierungs-/Autorisierungsfeature nicht aktiviert ist, aktivieren Sie es über die Option **Ein**.

15. Klicken Sie auf **Azure Active Directory**, und klicken Sie dann auf **Erweitert** unter **Verwaltungsmodus**. Fügen Sie die Werte für Client-ID und Aussteller-URL ein, die Sie zuvor abgerufen haben. Klicken Sie dann auf **OK**.

    ![][1]
	
16. Standardmäßig erfolgt die Anmeldung über App Service, dabei wird jedoch der Zugriff auf die Inhalte Ihrer Website und APIs nicht eingeschränkt. Diese Aufgabe erfüllt der App-Code. Wenn die Website vollständig durch die Azure Active Directory-Anmeldung geschützt werden soll, wählen Sie in der Dropdownliste **Action to take when request is not authenticated** die Option **Azure Active Directory** aus. Dadurch müssen alle Anforderungen authentifiziert werden. Nicht authentifizierte Anforderungen werden zur Azure Active Directory-Anmeldung umgeleitet.

17. Klicken Sie auf **Speichern**.

Sie können nun Azure Active Directory für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-express-settings.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/mobile-app-aad-advanced-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-configure.png

<!-- URLs. -->

[Vorschau des Azure-Verwaltungsportals]: https://portal.azure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-xamarin-ios-aad-sso.md
[alternative method]: #advanced

<!---HONumber=Nov15_HO4-->