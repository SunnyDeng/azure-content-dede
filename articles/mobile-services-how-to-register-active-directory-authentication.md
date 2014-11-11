<properties linkid="develop-mobile-how-to-guides-register-for-active-directory-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Mobile Services application." title="Register your account to use an Azure Active Directory account login" authors="wesmc" services="mobile-services" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Registrieren Ihrer Apps für die Verwendung einer Azure Active Directory-Anmeldung

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Azure Active Directory als Authentifizierungsanbieter für Azure Mobile Services zu verwenden.

> [WACOM.NOTE] Wenn Sie eine clientgesteuerte Authentifizierung für einmaliges Anmelden (SSO, Single Sign-On) mit Azure Active Directory bereitstellen möchten, informieren Sie sich im Lernprogramm [Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden][Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden].

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Identity** für Ihren mobilen Dienst.

    ![][1]

3.  Blättern Sie nach unten bis zum Bereich für den **Azure active directory**-Identitätsanbieter und kopieren Sie die dort aufgelistete **APP URL**.

    ![][2]

4.  Navigieren Sie zu **Active Directory** im Verwaltungsportal und klicken Sie auf Ihr Directory.

    ![][3]

5.  Klicken Sie oben auf die Registerkarte **Applications** und anschließend auf **ADD**, um eine App hinzuzufügen.

    ![][4]

6.  Klicken Sie auf **Add an application my organization is developing**.

7.  Geben Sie im Add Application-Assistenten einen **Namen** für Ihre Anwendung ein und klicken Sie auf den Typ **Web Application And/Or Web API**. Klicken Sie dann auf Continue.

    ![][5]

8.  Fügen Sie im Feld **SIGN-ON URL** die App-ID ein, die Sie aus den Einstellungen für den Active Directory-Identitätsanbieter Ihres mobilen Dienstes kopiert haben. Geben Sie denselben eindeutigen Ressourcenbezeichner in das Feld **App-ID-URI** ein. Klicken Sie dann auf Continue.

    ![][6]

9.  Nachdem die Anwendung hinzugefügt wurde, klicken Sie auf die Registerkarte **Konfigurieren**. Kopieren Sie nun die **Client ID** für die App.

    Wenn Sie den mobilen Dienst so konfiguriert haben, dass er das .NET-Back-End verwendet, bearbeiten Sie zusätzlich **Antwort-URL** unter **Einmaliges Anmelden** und legen diese URL gefolgt vom Pfad *signin-aad* als URL Ihres mobilen Diensts fest. Beispiel: `https://todolist.azure-mobile.net/signin-aad`

    ![][7]

10. Kehren Sie zur Registerkarte **Identity** für Ihren mobilen Dienst zurück. Fügen Sie am unteren Ende die **Client ID**-Einstellung für den Azure Active Directory-Identitätsanbieter ein.

11. In der Liste **Zulässige Mandanten** müssen Sie die Domäne des Verzeichnisses hinzufügen, in dem Sie die Anwendung registriert haben (z. B. "contoso.onmicrosoft.com"). Den Namen der Standarddomäne finden Sie, indem Sie in Active Directory auf die Registerkarte **Domänen** klicken.

    ![][8]

    Fügen Sie den Domänennamen der Liste **Zulässige Mandanten** hinzu, und klicken Sie auf **Speichern**.

    ![][9]

Sie können nun Azure Active Directory für die Authentifizierung in Ihrer App verwenden.



  [Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
  [1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
  [2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
  [3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
  [4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
  [5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
  [6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
  [7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
  [8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png
  [9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
