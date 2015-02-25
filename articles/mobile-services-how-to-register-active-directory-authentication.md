<properties pageTitle="Registrieren für die Authentifizierung in Azure Active Directory - Mobile Services" description="Erfahren Sie mehr über die Registrierung für die Azure Active Directory-Authentifizierung in Ihrer Mobile Services-Anwendung." authors="wesmc7777" services="mobile-services" documentationCenter="" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"/>

# Registrieren Ihrer Apps für die Verwendung einer Azure Active Directory-Anmeldung

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Azure Active Directory als Authentifizierungsanbieter für Azure Mobile Services zu verwenden. 


>[AZURE.NOTE] Die in diesem Thema beschriebenen Schritte sollten mit dem Lernprogramm [Hinzufügen von Authentifizierung zur Mobile Services-App](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/) verwendet werden, wenn Sie [Vom Dienst ausgehende Anmeldevorgänge](http://msdn.microsoft.com/de-de/library/azure/dn283952.aspx) mit Ihrer App verwenden möchten. Auch wenn Ihre App [vom Client ausgehende Anmeldevorgänge](http://msdn.microsoft.com/de-de/library/azure/jj710106.aspx) für Azure Active Directory und einen mobilen .NET Back-End-Dienst erfordert, sollten Sie mit dem Lernprogramm [Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden](/de-de/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication/) beginnen.


1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihren mobilen Dienst.

    ![][1]

2. Klicken Sie auf die Registerkarte **Identität** für Ihren mobilen Dienst. 

    ![][2]

3. Blättern Sie nach unten bis zum Bereich für den **Azure Active Directory**-Identitätsanbieter, und kopieren Sie die dort aufgelistete **APP-URL**.

    ![][3]

4. Navigieren Sie zu **Active Directory** im Verwaltungsportal, und klicken Sie auf Ihr Verzeichnis.

    ![][4] 	

5. Klicken Sie oben auf die Registerkarte **Anwendungen** und anschließend auf **HINZUFÜGEN**, um eine App hinzuzufügen. 

    ![][10]

6. Klicken Sie auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.

7. Geben Sie im Assistenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Webanwendung und/oder Web-API**. Klicken Sie dann auf Continue.

    ![][5]

8. Fügen Sie im Feld **URL für Anmeldung** die App-ID ein, die Sie aus den Einstellungen für den Active Directory-Identitätsanbieter Ihres mobilen Dienstes kopiert haben. Geben Sie denselben eindeutigen Ressourcenbezeichner in das Feld **App-ID-URI** ein. Klicken Sie dann auf Continue.
 
    ![][6]


9. Nachdem die Anwendung hinzugefügt wurde, klicken Sie auf die Registerkarte **Konfigurieren**. Kopieren Sie nun die **Client-ID** für die App.

    Wenn Sie den mobilen Dienst so konfiguriert haben, dass er das .NET-Back-End verwendet, bearbeiten Sie zusätzlich **Antwort-URL** unter **Einmaliges Anmelden** und legen diese URL gefolgt vom Pfad _signin-aad_ als URL Ihres mobilen Diensts fest. Beispiel:  `https://todolist.azure-mobile.net/signin-aad`

    ![][8]


10. Kehren Sie zur Registerkarte **Identität** für Ihren mobilen Dienst zurück. Fügen Sie am unteren Ende die Einstellung **Client-ID** für den Azure Active Directory-Identitätsanbieter ein.

  
11. In der Liste **Zulässige Mandanten** müssen Sie die Domäne des Verzeichnisses hinzufügen, in dem Sie die Anwendung registriert haben (z. B. "contoso.onmicrosoft.com"). Den Namen der Standarddomäne finden Sie, indem Sie in Active Directory auf die Registerkarte **Domänen** klicken.

    ![][11]
 
    Fügen Sie den Domänennamen der Liste **Zulässige Mandanten** hinzu, und klicken Sie auf **Speichern**.    


    ![][9]



Sie können nun Azure Active Directory für die Authentifizierung in Ihrer App verwenden. 



<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-selection.png
[2]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-identity-tab.png
[3]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-copy-app-url-waad-auth.png
[4]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-select-ad-waad-auth.png
[5]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-1-waad-auth.png
[6]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-2-waad-auth.png
[7]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-add-app-wizard-3-waad-auth.png
[8]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-waad-auth.png
[9]: ./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-clientid-pasted-waad-auth.png
[10]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-waad-idenity-tab-selection.png
[11]:./media/mobile-services-how-to-register-active-directory-authentication/mobile-services-default-domain.png

<!-- URLs. -->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/



<!--HONumber=42-->
