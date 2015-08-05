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
	ms.date="05/26/2015" 
	ms.author="mahender"/>

# Konfigurieren Ihrer Anwendung zur Verwendung der Azure Active Directory-Anmeldung

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

In diesem Thema wird veranschaulicht, wie Sie Azure App Services zur Verwendung von Azure Active Directory als Authentifizierungsanbieter konfigurieren.

## <a name="register"> </a>Registrieren Ihrer Anwendung bei Azure Active Directory

1. Melden Sie sich unter [Vorschau auf das Azure-Verwaltungsportal] an, und navigieren Sie zu Ihrem App Service-Gateway.

2. Wählen Sie unter **Einstellungen** die Option **Identität**, und wählen Sie dann **Azure Active Directory**. Kopieren Sie die **APP-URL**. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

    ![][1]

3. Melden Sie sich am [Azure-Verwaltungsportal] an, und navigieren Sie zu **Active Directory**.

    ![][2]

4. Wählen Sie Ihr Verzeichnis und anschließend die Registerkarte **Anwendungen** im oberen Bereich aus. Klicken Sie im unteren Bereich auf **HINZUFÜGEN**, um eine neue App-Registrierung zu erstellen.

5. Klicken Sie auf **Add an application my organization is developing**.

6. Geben Sie im Add Application-Assistenten einen **Namen** für Ihre Anwendung ein und klicken Sie auf den Typ **Web Application And/Or Web API**. Klicken Sie dann auf Continue.

7. Fügen Sie im Feld **ANMELDE-URL** die aus den Active Directory-Identitätsanbietereinstellungen kopierte App-ID Ihres Gateways ein. Geben Sie denselben eindeutigen Ressourcenbezeichner in das Feld **App-ID-URI** ein. Klicken Sie dann auf Continue.

8. Nachdem die Anwendung hinzugefügt wurde, klicken Sie auf die Registerkarte **Konfigurieren**. Ändern Sie die **Antwort-URL** unterhalb von **Einmaliges Anmelden** in die URL Ihres Gateways mit angehängtem Pfad _/signin-aad_. Beispiel: `https://contosogateway.azurewebsites.net/signin-aad`. Stellen Sie sicher, dass Sie das HTTPS-Schema verwenden.

    ![][3]

9. Klicken Sie auf **Speichern**. Kopieren Sie dann die **Client-ID** für die App.

## <a name="secrets"> </a>Hinzufügen von Azure Active Directory-Informationen zu Ihrer mobilen App

10. Kehren Sie zum Vorschauverwaltungsportal und zum Blatt **Benutzerauthentifizierung** für Ihr Gateway zurück. Fügen Sie die **Client-ID**-Einstellung für den Azure Active Directory-Identitätsanbieter ein.
  
11. In der Liste **Zulässige Mandanten** müssen Sie die Domäne des Verzeichnisses hinzufügen, in dem Sie die Anwendung registriert haben (z. B. "contoso.onmicrosoft.com"). Den Namen der Standarddomäne finden Sie, indem Sie in Ihrem Azure Active Directory-Mandanten auf die Registerkarte **Domänen** klicken. Fügen Sie den Domänennamen der Liste **Zulässige Mandanten** hinzu, und klicken Sie auf **Speichern**.

Sie können nun Azure Active Directory für die Authentifizierung in Ihrer App verwenden.

## <a name="related-content"> </a>Verwandte Inhalte

[AZURE.INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

Authentifizieren von Benutzern Ihrer mobilen App mit Azure Active Directory für einmaliges Anmelden: [iOS][ios-adal]

<!-- Images. -->

[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-settings.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-navigate-aad.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication-preview/app-service-aad-app-configure.png

<!-- URLs. -->

[Vorschau auf das Azure-Verwaltungsportal]: https://portal.azure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[ios-adal]: ../app-service-mobile-dotnet-backend-xamarin-ios-aad-sso-preview.md
 

<!---HONumber=July15_HO4-->