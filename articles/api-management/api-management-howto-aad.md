<properties 
	pageTitle="Autorisieren von Entwicklerkonten mithilfe von Active Directory in Azure API Management" 
	description="Erfahren Sie, wie Benutzer mithilfe von Active Directory in Azure API Management autorisiert werden." 
	services="api-management" 
	documentationCenter="API Management" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="sdanie"/>

# Autorisieren von Entwicklerkonten mithilfe von Active Directory in Azure API Management


## Übersicht
Dieser Leitfaden zeigt, wie Sie den Zugriff auf das Entwicklerportal für alle Benutzer in einem oder mehreren Azure Active Directory-Verzeichnissen aktivieren. Außerdem erfahren Sie, wie Sie Azure Active Directory-Benutzergruppen verwalten, indem Sie externe Gruppen hinzufügen, die Benutzer eines anderen Azure Active Directory-Verzeichnisses enthalten.

>Zum Ausführen der hier genannten Schritte müssen Sie über ein Azure Active Directory-Verzeichnis verfügen, in dem eine Anwendung erstellt wird.

## Autorisieren von Entwicklerkonten mithilfe von Active Directory

Klicken Sie zunächst im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**. Daraufhin gelangen Sie zum API Management-Herausgeberportal.

![Herausgeberportal][api-management-management-console]

>Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][].

Klicken Sie im Menü **API Management** auf der linken Seite auf **Sicherheit**, und klicken Sie dann auf **Externe Identitäten**.

![Externe Identitäten][api-management-security-external-identities]

Klicken Sie auf **Azure Active Directory**. Notieren Sie sich den Wert für **Umleitungs-URL**, und wechseln Sie zu Azure Active Directory im Azure-Portal.

![Externe Identitäten][api-management-security-aad-new]

Klicken Sie auf die Schaltfläche **Hinzufügen**, um eine neue Azure Active Directory-Anwendung zu erstellen, und wählen Sie **Von meiner Organisation entwickelte Anwendung hinzufügen**.

![Neue Azure Active Directory-Anwendung hinzufügen][api-management-new-aad-application-menu]

Geben Sie einen Namen für die Anwendung ein, wählen Sie** Webanwendung und/oder Web-API** aus, und klicken Sie auf "Weiter".

![Neue Azure Active Directory-Anwendung][api-management-new-aad-application-1]

Kopieren Sie für den **Anmelde-URL** den Wert für **Umleitungs-URL** aus dem Abschnitt **Azure Active Directory** der Registerkarte **Externe Identitäten** im Herausgeberportal, und entfernen Sie das Suffix **-aad** am Ende der URL. In diesem Beispiel erhält **Anmelde-URL** den Wert `https://aad03.portal.current.int-azure-api.net/signin`.

Geben Sie für die **App-ID-URL** entweder die Standarddomäne oder eine benutzerdefinierte Domäne für das Azure Active Directory-Verzeichnis ein, und hängen Sie eine eindeutige Zeichenfolge an die Domäne an. In diesem Beispiel wird die Standarddomäne ****https://contoso5api.onmicrosoft.com** mit dem Suffix **/api** angegeben.

![Neue Azure Active Directory-Anwendung – Eigenschaften][api-management-new-aad-application-2]

Klicken Sie auf die Schaltfläche mit dem Häkchen, um die neue Anwendung zu speichern und zu erstellen, und wechseln Sie zur Registerkarte **Konfigurieren**, um die neue Anwendung zu konfigurieren.

![Neue Azure Active Directory-Anwendung wurde erstellt][api-management-new-aad-app-created]

Wenn mehrere Azure Active Directory-Verzeichnisse für diese Anwendung verwendet werden sollen, klicken Sie bei der Einstellung **Die Anwendung ist mehrinstanzenfähig** auf **Ja**. Der Standardwert lautet **Nein**.

![Anwendung ist mehrinstanzenfähig][api-management-aad-app-multi-tenant]

Kopieren Sie die **Umleitungs-URL** aus dem Abschnitt **Azure Active Directory** der Registerkarte **Externe Identitäten** im Herausgeberportal, und fügen Sie sie in das Textfeld Antwort-URL ein.

![Antwort-URL][api-management-aad-reply-url]

Führen Sie einen Bildlauf zum Ende der Registerkarte "Konfigurieren" durch, wählen Sie die Dropdownliste **Anwendungsberechtigungen**, und aktivieren Sie **Verzeichnisdaten lesen**.

![Anwendungsberechtigungen][api-management-aad-app-permissions]

Aktivieren Sie die Dropdownliste **Berechtigungen der Stellvertretung**, und aktivieren Sie **Anmeldung aktivieren und Benutzerprofile lesen**.

![Berechtigungen der Stellvertretung][api-management-aad-delegated-permissions]

>Weitere Informationen zu Anwendungen und delegierten Berechtigungen finden Sie unter [Zugreifen auf die Graph-API][].

Kopieren Sie die **Client-ID** in die Zwischenablage.

![Client-ID][api-management-aad-app-client-id]

Wechseln Sie zurück zum Herausgeberportal, und fügen Sie die aus der Azure Active Directory-Anwendungskonfiguration kopierte **Client-ID** ein.

![Client-ID][api-management-client-id]

Wechseln Sie zurück zur Azure Active Directory-Konfiguration, klicken Sie im Abschnitt **Schlüssel** auf die Dropdownliste **Dauer auswählen**, und geben Sie ein Intervall an. In diesem Beispiel wird der Wert **1 Jahr** verwendet.

![Schlüssel][api-management-aad-key-before-save]

Klicken Sie auf **Speichern**, um die Konfiguration zu speichern und den Schlüssel anzuzeigen. Kopieren Sie den Schlüssel in die Zwischenablage.

>Notieren Sie sich diesen Schlüssel. Nachdem Sie das Fenster für die Azure Active Directory-Konfiguration geschlossen haben, ist eine Anzeige des Schlüssels nicht mehr möglich.

![Schlüssel][api-management-aad-key-after-save]

Wechseln Sie zurück zum Herausgeberportal, und fügen Sie den Schlüssel in das Textfeld **Geheimer Clientschlüssel** ein.

![Geheimer Clientschlüssel][api-management-client-secret]

**Zulässige Mandanten** gibt an, welche Verzeichnisse Zugriff auf die APIs der API Management-Dienstinstanz haben. Geben Sie die Domänen der Azure Active Directory-Instanzen an, denen Sie Zugriff erteilen möchten. Sie können mehrere Domänen durch neue Zeilen, Leerzeichen oder Kommas trennen.

![Zulässige Mandanten][api-management-client-allowed-tenants]

Im Abschnitt **Zulässige Mandanten** können mehrere Domänen angegeben werden. Bevor sich Benutzer aus einer anderen Domäne als der ursprünglichen Domäne (der Domäne, in der die Anwendung registriert wurde) anmelden können, muss ein globaler Administrator dieser anderen Domäne der Anwendung Berechtigungen für den Verzeichnisdatenzugriff erteilen. Hierzu muss sich der globale Administrator bei der Anwendung anmelden und auf **Akzeptieren** klicken. Im folgenden Beispiel wurde `miaoaad.onmicrosoft.com` im Abschnitt **Zulässige Mandanten** hinzugefügt, und ein globaler Administrator dieser Domäne meldet sich zum ersten Mal an.

![Berechtigungen][api-management-permissions-form]

>Wenn ein nicht globaler Administrator versucht, sich anzumelden, bevor Berechtigungen durch einen globalen Administrator erteilt wurden, tritt ein Anmeldefehler auf, und ein Fehlerbildschirm wird angezeigt.

Nachdem die gewünschte Konfiguration angegeben wurde, klicken Sie auf **Speichern**.

![Speichern][api-management-client-allowed-tenants-save]

Nachdem die Änderungen gespeichert wurden, können sich Benutzer aus dem angegebenen Azure Active Directory-Verzeichnis beim Entwicklerportal anmelden, indem sie die unter [Anmelden beim Entwicklerportal mit einem Azure Active Directory-Konto][] beschriebenen Schritte ausführen.

## Hinzufügen einer externen Azure Active Directory-Gruppe

Nachdem der Zugriff für Benutzer in einem Azure Active Directory-Verzeichnis aktiviert wurde, können Sie Azure Active Directory-Gruppen in API Management hinzufügen, um die Zuweisung der Entwickler in der Gruppe zu den gewünschten Produkten zu vereinfachen.

> Um eine externe Azure Active Directory-Gruppe zu konfigurieren, muss Azure Active Directory zunächst – wie in der Vorgehensweise im vorherigen Abschnitt beschrieben – über die Registerkarte "Identitäten" konfiguriert werden.

Externe Azure Active Directory-Gruppen werden über die Registerkarte **Sichtbarkeit** des Produkts hinzugefügt, für das Sie der Gruppe Zugriff gewähren möchten. Klicken Sie auf **Produkte** und anschließend auf den Namen des gewünschten Produkts.

![Produkt konfigurieren][api-management-configure-product]

Wechseln Sie zur Registerkarte **Sichtbarkeit**, und klicken Sie auf **Gruppen aus Azure Active Directory hinzufügen**.

![Gruppen hinzufügen][api-management-add-groups]

Wählen Sie in der Dropdownliste **Azure Active Directory-Mandant**, und geben Sie den Namen der gewünschten Gruppe in das Textfeld **Hinzuzufügende Gruppen** ein.

![Gruppe auswählen][api-management-select-group]

Diesen Gruppennamen finden Sie in der Liste **Gruppen** für Ihr Azure Active Directory-Verzeichnis, wie im folgenden Beispiel gezeigt.

![Azure Active Directory-Gruppenliste][api-management-aad-groups-list]

Klicken Sie auf **Hinzufügen**, um den Gruppennamen zu validieren und die Gruppe hinzuzufügen. In diesem Beispiel wird die externe Gruppe **Contoso 5 Developers** hinzugefügt.

![Gruppe hinzugefügt][api-management-aad-group-added]

Klicken Sie auf **Speichern**, um die neue Gruppenauswahl zu speichern.

Sobald eine Azure Active Directory-Gruppe für ein Produkt konfiguriert wurde, steht es zur Aktivierung auf der Registerkarte **Sichtbarkeit** für die weiteren Produkte in der API Management-Dienstinstanz zur Verfügung.

Um die Eigenschaften für externe Gruppen nach dem Hinzufügen zu prüfen und zu konfigurieren, klicken Sie auf der Registerkarte **Gruppen** auf den Namen der Gruppe.

![Gruppen verwalten][api-management-groups]

Auf dieser Registerkarte können Sie **Name** und **Beschreibung** der Gruppe bearbeiten.

![Gruppe bearbeiten][api-management-edit-group]

Benutzer aus dem konfigurierten Azure Active Directory-Verzeichnis können sich beim Entwicklerportal anmelden und beliebige Gruppen abonnieren, die sie anzeigen können. Hierbei müssen die Anweisungen im folgenden Abschnitt befolgt werden.

## So melden Sie sich mit einem Azure Active Directory-Konto beim Entwicklerportal an

Um sich mit einem im vorherigen Abschnitt konfigurierten Azure Active Directory-Konto beim Entwicklerportal anzumelden, öffnen Sie ein neues Browserfenster mit der **Anmelde-URL** aus der Active Directory-Anwendungskonfiguration, und klicken Sie auf **Azure Active Directory**.

![Entwicklerportal][api-management-dev-portal-signin]

Geben Sie die Anmeldeinformationen eines Benutzer in Ihrem Azure Active Directory-Verzeichnis ein, und klicken Sie auf **Anmelden**.

![Anmelden][api-management-aad-signin]

Es wird möglicherweise ein Registrierungsformular angezeigt, wenn weitere Informationen erforderlich sind. Vervollständigen Sie das Registrierungsformular, und klicken Sie auf **Registrieren**.

![Registrierung][api-management-complete-registration]

Der Benutzer ist jetzt beim Entwicklerportal für Ihre API Management-Dienstinstanz angemeldet.

![Registrierung abgeschlossen][api-management-registration-complete]



[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Erste Schritte mit der Azure API-Verwaltung]: api-management-get-started.md
[Get started with advanced API configuration]: api-management-get-started-advanced.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Erstellen einer API-Verwaltungsinstanz]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Zugreifen auf die Graph-API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Anmelden beim Entwicklerportal mit einem Azure Active Directory-Konto]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

<!---HONumber=Oct15_HO3-->