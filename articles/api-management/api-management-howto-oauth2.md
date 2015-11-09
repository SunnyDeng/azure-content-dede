<properties 
	pageTitle="So autorisieren Sie Entwicklerkonten mithilfe von OAuth 2.0 in Azure API Management" 
	description="Erfahren Sie, wie Sie Benutzer mit OAuth 2.0 in API Management autorisieren." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2015" 
	ms.author="sdanie"/>

# So autorisieren Sie Entwicklerkonten mithilfe von OAuth 2.0 in Azure API Management

Viele APIs unterstützen [OAuth 2.0](http://oauth.net/2/) zum Schützen der API und um sicherzustellen, dass nur autorisierte Benutzer Zugriff erhalten und nur auf die Ressourcen zugreifen können, für die sie berechtigt sind. Um die interaktive Entwicklerkonsole von Azure API Management mit solchen APIs zu verwenden, ermöglicht der Dienst Ihnen das Konfigurieren Ihrer Dienstinstanz für die Zusammenarbeit mit einer OAuth 2.0-aktivierten API.

## <a name="prerequisites"> </a>Voraussetzungen

Diese Anleitung beschreibt, wie Sie eine Instanz des API Management-Diensts zur Verwendung der OAuth 2.0-Autorisierung für Entwicklerkonten konfigurieren. Es wird jedoch nicht behandelt, wie Sie einen OAuth 2.0-Anbieter konfigurieren. Die Konfiguration ist je nach OAuth 2.0-Anbieter unterschiedlich, die Schritte sind jedoch ähnlich, und die beim Konfigurieren von OAuth 2.0 in Ihrer Instanz des API Management-Diensts erforderlichen Informationen sind gleich. In diesem Thema werden Beispiele zur Verwendung von Azure Active Directory als OAuth 2.0-Anbieter gezeigt.

>[AZURE.NOTE]Weitere Informationen zum Konfigurieren von OAuth 2.0 mithilfe von Azure Active Directory finden Sie im Beispiel [WebApp-GraphAPI-DotNet][].

## <a name="step1"> </a>Konfigurieren eines OAuth 2.0-Autorisierungsservers in API Management

Klicken Sie zunächst im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**. Daraufhin gelangen Sie zum API Management-Herausgeberportal.

![Herausgeberportal][api-management-management-console]

>[AZURE.NOTE]Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][].

Klicken Sie auf **Sicherheit** im Menü **API Management** auf der linken Seite, klicken Sie auf **OAuth 2.0** und dann auf **Autorisierungsserver hinzufügen**.

![OAuth 2.0][api-management-oauth2]

Nachdem Sie auf **Autorisierungsserver hinzufügen** geklickt haben, wird das Formular für neue Autorisierungsserver angezeigt.

![Neuer Server][api-management-oauth2-server-1]

Geben Sie in die Felder **Name** und **Beschreibung** einen Namen bzw. eine optionale Beschreibung ein.

>[AZURE.NOTE]Anhand dieser Felder wird der OAuth 2.0-Autorisierungsserver innerhalb der aktuellen Instanz des API Management-Diensts identifiziert. Die Werte der Felder stammen nicht vom OAuth 2.0-Server.

Geben Sie die **Seiten-URL für Clientregistrierung** ein. Auf dieser Seite können Benutzer ihre Konten erstellen und verwalten. Die Seite variiert je nach OAuth 2.0-Anbieter. **Seiten-URL für Clientregistrierung** verweist auf die Seite, die Benutzer zum Erstellen und Konfigurieren eigener Konten für OAuth 2.0-Anbieter nutzen können, die eine Benutzerverwaltung für Konten unterstützen. Einige Organisationen nutzen diese Funktionalität nicht, selbst wenn der OAuth 2.0-Anbieter sie unterstützt. Wenn für Ihren OAuth 2.0-Anbieter keine Benutzerverwaltung für Konten konfiguriert ist, geben Sie hier eine Platzhalter-URL ein, z. B. die URL Ihres Unternehmens oder eine URL wie `https://placeholder.contoso.com`.

Der nächste Abschnitt des Formulars enthält die Einstellungen **Autorisierungscode-Berechtigungstypen**, **Autorisierungsendpunkt-URL** und **Autorisierungsanforderungsmethode**.

![Neuer Server][api-management-oauth2-server-2]

Geben Sie die **Autorisierungscode-Berechtigungstypen** an, indem Sie die gewünschten Typen aktivieren. **Autorisierungscode** wird standardmäßig festgelegt.

Geben Sie die **Autorisierungsendpunkt-URL** ein. Für Azure Active Directory lautet diese URL ähnlich wie die folgende, wobei `<client_id>` durch die Client-ID ersetzt wird, die Ihre Anwendung gegenüber dem OAuth 2.0-Server authentifiziert.

    https://login.windows.net/<client_id>/oauth2/authorize

Über **Methode für Autorisierungsanforderung** wird festgelegt, wie die Autorisierungsanforderung an den OAuth 2.0-Server gesendet wird. Per Voreinstellung ist **GET** ausgewählt.

Im nächsten Abschnitt werden **Token-Endpunkt-URL**, **Clientauthentifizierungsmethoden**, **Sendemethode für Zugriffstoken** und **Standardmäßiger Geltungsbereich** angegeben.

![Neuer Server][api-management-oauth2-server-3]

Bei einem Azure Active Directory OAuth 2.0-Server weist **Token-Endpunkt-URL** das folgende Format auf. Hierbei verwendet `<APPID>` das Format `yourapp.onmicrosoft.com`.

    https://login.windows.net/<APPID>/oauth2/token

Die Standardeinstellung für **Clientauthentifizierungsmethoden** lautet **Basis**, und die **Sendemethode für Zugriffstoken** ist **Autorisierungsheader**. Diese Werte werden zusammen mit **Standardmäßiger Geltungsbereich** in diesem Abschnitt des Formulars festgelegt.

Der Abschnitt **Clientanmeldeinformationen** enthält die Werte für **Client-ID** und **Geheimer Clientschlüssel**, die beim Erstellen und Konfigurieren Ihres OAuth 2.0-Servers abgerufen werden. Nachdem die Werte für **Client-ID** und **Geheimer Clientschlüssel** angegeben wurden, wird die **Umleitungs-URI** für den **Autorisierungscode** generiert. Die URI wird zum Konfigurieren der Antwort-URL in Ihrer OAuth 2.0-Serverkonfiguration verwendet.

![Neuer Server][api-management-oauth2-server-4]

Falls für **Autorisierungscode-Berechtigungstypen** die Einstellung **Ressourcenbesitzer-Kennwort** festgelegt ist, werden diese Berechtigungen im Abschnitt **Ressourcenbesitzer-Kennwortberechtigungen** festgelegt. Andernfalls lassen Sie den Abschnitt leer.

![Neuer Server][api-management-oauth2-server-5]

Nachdem Sie das Formular ausgefüllt haben, klicken Sie auf **Speichern**, um die OAuth 2.0-Autorisierungsserverkonfiguration für API Management zu speichern. Nach dem Speichern der Serverkonfiguration können Sie APIs wie im nächsten Abschnitt beschrieben zur Nutzung konfigurieren.

## <a name="step2"> </a>Konfigurieren einer API zum Verwenden der OAuth 2.0-Benutzerauthentifizierung

Klicken Sie auf **APIs** im Menü **API Management** auf der linken Seite, klicken Sie auf die gewünschte API und danach auf **Sicherheit**. Aktivieren Sie anschließend das Kontrollkästchen für **OAuth 2.0**.

![Benutzerautorisierung][api-management-user-authorization]

Wählen Sie in der Dropdownliste den gewünschten **Autorisierungsserver**, und klicken Sie dann auf **Speichern**.

![Benutzerautorisierung][api-management-user-authorization-save]

## <a name="step3"> </a>Testen der OAuth 2.0-Benutzerauthentifizierung im Entwicklerportal

Nachdem Sie Ihren OAuth 2.0-Autorisierungsserver und Ihre API zu dessen Nutzung konfiguriert haben, können Sie ihn testen, indem Sie zum Entwicklerportal wechseln und eine API aufrufen. Klicken Sie im Menü oben rechts auf **Entwicklerportal**.

![Entwicklerportal][api-management-developer-portal-menu]

Klicken Sie auf **APIs** im Hauptmenü, und wählen Sie **Echo API** aus.

![Echo API][api-management-apis-echo-api]

>[AZURE.NOTE]Falls nur eine API konfiguriert oder für Ihr Konto sichtbar ist, können Sie auf APIs klicken, um direkt zu den Operationen für diese API zu gelangen.

Wählen Sie die Operation **GET Resource** aus, klicken Sie auf **Konsole öffnen**, und wählen Sie dann aus der Dropdownliste **Autorisierungscode** aus.

![Konsole öffnen][api-management-open-console]

Wenn der **Autorisierungscode** ausgewählt wurde, wird ein Popup-Fenster mit dem Anmeldeformular des OAuth 2.0-Anbieters angezeigt. In diesem Beispiel wird das Anmeldeformular von Azure Active Directory bereitgestellt.

>[AZURE.NOTE]Falls Sie Popup-Fenster deaktiviert haben, werden Sie dazu aufgefordert, sie im Browser zu aktivieren. Wählen Sie danach erneut **Autorisierungscode** aus. Daraufhin wird das Anmeldeformular angezeigt.

![Anmelden][api-management-oauth2-signin]

Nachdem Sie sich angemeldet haben, werden die **Anforderungsheader** mit einem `Authorization : Bearer`-Header ausgefüllt, der die Anforderung autorisiert.

![Token des Anforderungsheaders][api-management-request-header-token]

Nun können Sie die gewünschten Werte für die restlichen Parameter konfigurieren und die Anforderung absenden.

## Nächste Schritte

Weitere Informationen zur Verwendung von OAuth 2.0 und API Management finden Sie im folgenden Video und dem zugehörigen [Artikel](api-management-howto-protect-backend-with-aad.md).

> [AZURE.VIDEO protecting-web-api-backend-with-azure-active-directory-and-api-management]

[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

<!---HONumber=Nov15_HO1-->