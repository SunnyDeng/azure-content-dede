<properties 
	pageTitle="Erstellen von APIs in Azure API Management" 
	description="Erfahren Sie, wie Sie APIs in Azure API Management erstellen und konfigurieren." 
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
	ms.date="12/07/2015" 
	ms.author="sdanie"/>

# Erstellen von APIs in Azure API Management

Eine API in API Management besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. Neue APIs werden im Herausgeberportal erstellt, anschließend werden die gewünschten Operationen hinzugefügt. Nachdem Sie die Operationen hinzugefügt haben, wird die API zu einem Produkt hinzugefügt und kann veröffentlicht werden. Nach der Veröffentlichung der API können Entwickler diese abonnieren und verwenden.

Diese Anleitung beschreibt die ersten Schritte im Prozess: das Erstellen und Konfigurieren einer neuen API in API Management. Weitere Informationen zum Hinzufügen von Operationen und zum Veröffentlichen von Produkten finden Sie unter [Hinzufügen von Operationen zu einer API][] und [Erstellen und Veröffentlichen von Produkten][].

## <a name="create-new-api"> </a>Erstellen einer neuen API

APIs werden im Herausgeberportal erstellt und konfiguriert. Um auf das Herausgeberportal zuzugreifen, klicken Sie im klassischen Azure-Portal für Ihren API Management-Dienst auf **Verwalten**.

![Herausgeberportal][api-management-management-console]

>Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][].

Klicken Sie auf **APIs** im Menü **API Management** auf der linken Seite, und klicken Sie anschließend auf **API hinzufügen**.

![API erstellen][api-management-create-api]

Konfigurieren Sie die neue API im Fenster **Neue API hinzufügen**.

![Neue API hinzufügen][api-management-add-new-api]

Die folgenden Felder werden zum Konfigurieren der neuen API verwendet.

-	**Name der Web-API** enthält einen eindeutigen und beschreibenden Namen für die API. Dieser Name wird in den Entwickler- und Herausgeberportalen angezeigt.
-	**Webdienst-URL** verweist auf den HTTP-Dienst, der die API implementiert. API Management leitet Anfragen an diese Adresse weiter.
-	**URL-Suffix für Web-API** wird an die Basis-URL für den API Management-Dienst angehängt. Alle in einer API Management-Dienstinstanz gehosteten APIs teilen sich dieselbe Basis-URL. API Management unterscheidet APIs durch deren Suffix. Daher muss jede API eines bestimmten Herausgebers ein eindeutiges Suffix haben.
-	Mit **URL-Schema für Web-API** wird festgelegt, welche Protokolle für den Zugriff auf die API verwendet werden können. Standardmäßig wird HTTPS angegeben.
-	Um diese neue API optional einem Produkt hinzuzufügen, klicken Sie in das Dropdown-Listenfeld **Produkte (optional)** und wählen ein Produkt aus. Dieser Schritt kann mehrfach wiederholt werden, um die API mehreren Produkten hinzuzufügen.

Wenn Sie die gewünschten Werte konfiguriert haben, klicken Sie auf **Speichern**. Sobald die neue API erstellt wurde, wird die Zusammenfassungsseite für die API im Herausgeberportal angezeigt.

![API-Zusammenfassung][api-management-api-summary]

## <a name="configure-api-settings"> </a>Konfigurieren der API-Einstellungen

Auf der Registerkarte **Einstellungen** können Sie die Konfiguration einer API anzeigen und bearbeiten. **Name der Web-API**, **Webdienst-URL** und **URL-Suffix für die Web-API** werden bei der Erstellung der API konfiguriert und können hier geändert werden. **Beschreibung** liefert eine optionale Beschreibung, mit **URL-Schema für Web-API** wird festgelegt, welche Protokolle für den Zugriff auf die API verwendet werden können.

![API-Einstellungen][api-management-api-settings]

Um die Gatewayauthentifizierung für die Back-End-Dienst-Implementierung der API zu konfigurieren, wählen Sie die Registerkarte **Sicherheit**. Über die Dropdownliste **Mit Anmeldeinformationen** können Sie die **HTTP-Standardauthentifizierung** oder die **Clientzertifikatauthentifizierung** konfigurieren. Zum Verwenden der HTTP-Standardauthentifizierung geben Sie einfach die gewünschten Anmeldeinformationen ein. Informationen zum Verwenden der Clientzertifikatauthentifizierung finden Sie unter [Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung][] in Azure API Management.

Auf der Registerkarte **Sicherheit** können Sie außerdem die **Benutzerautorisierung** mithilfe von OAuth 2.0 konfigurieren. Weitere Informationen finden Sie unter [Autorisieren von Entwicklerkonten mithilfe von OAuth 2.0 in Azure API Management][].

![Einstellungen für die Standardauthentifizierung][api-management-api-settings-credentials]

Klicken Sie auf **Speichern**, um die Änderungen an Ihren API-Einstellungen zu speichern.

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie die API erstellt und die Einstellungen konfiguriert haben, können Sie Operationen zur API hinzufügen, die API zu einem Produkt hinzufügen und anschließend veröffentlichen, um die API für Entwickler zugänglich zu machen. Weitere Informationen finden Sie in den folgenden Artikeln.

-	[Hinzufügen von Operationen zu einer API][]
-	[Erstellen und Veröffentlichen eines Produkts][]





[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[Hinzufügen von Operationen zu einer API]: api-management-howto-add-operations.md
[Erstellen und Veröffentlichen eines Produkts]: api-management-howto-add-products.md
[Erstellen und Veröffentlichen von Produkten]: api-management-howto-add-products.md

[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance
[Sichern von Back-End-Diensten über eine Clientzertifikatauthentifizierung]: api-management-howto-mutual-certificates.md
[Autorisieren von Entwicklerkonten mithilfe von OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md

<!---HONumber=AcomDC_1210_2015-->