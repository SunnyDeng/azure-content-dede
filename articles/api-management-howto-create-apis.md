<properties pageTitle="How to create APIs in Azure API Management" metaKeywords="" description="Learn how to create and configure APIs in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to create APIs in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Erstellen von APIs in der Azure API-Verwaltung

Eine API in der API-Verwaltung (Vorschau) besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. Neue APIs werden in der Verwaltungskonsole erstellt, und anschließend werden die gewünschten Operationen hinzugefügt. Nachdem Sie die Operationen hinzugefügt haben, wird die API zu einem Produkt hinzugefügt und kann veröffentlicht werden. Nach der Veröffentlichung der API können Entwickler diese abonnieren und verwenden.

Diese Anleitung beschreibt den ersten Schritt im Prozess: Erstellung und Konfiguration einer neuen API in der API-Verwaltung. Weitere Informationen zum Hinzufügen von Operationen und zum Veröffentlichen von Produkten finden Sie unter [Hinzufügen von Operationen zu einer API][Hinzufügen von Operationen zu einer API] und [Erstellen und Veröffentlichen von Produkten][Erstellen und Veröffentlichen von Produkten].

## In diesem Thema

-   [Erstellen einer neuen API][Erstellen einer neuen API]
-   [Konfigurieren der API-Einstellungen][Konfigurieren der API-Einstellungen]
-   [Nächste Schritte][Nächste Schritte]

## <a name="create-new-api"> </a>Erstellen einer neuen API

Klicken Sie im Azure-Portal für Ihren API-Verwaltungs-Dienstinstanz auf **Verwaltungskonsole**, um APIs zu erstellen und zu konfigurieren. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![Verwaltungskonsole][Verwaltungskonsole]

Klicken Sie auf **APIs** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **API hinzufügen**.

![API erstellen][API erstellen]

Konfigurieren Sie die neue API im Fenster **Neue API hinzufügen**.

![Neue API hinzufügen][Neue API hinzufügen]

Die folgenden drei Felder werden zum Konfigurieren der neuen API verwendet.

-   **Titel der Web-API** enthält einen eindeutigen und beschreibenden Namen für die API. Dieser Name wird in den Entwickler- und Verwaltungsportalen angezeigt.
-   **Webdienst-URL** verweist auf den HTTP-Dienst, der die API implementiert. Die API-Verwaltung leitet Anfragen an diese Adresse weiter.
-   **URL-Suffix für Web-API** wird an die Basis-URL für den API-Verwaltungsdienst angehängt. Alle in einer API-Verwaltungs-Dienstinstanz gehosteten APIs teilen sich dieselbe Basis-URL. Die API-Verwaltung unterscheidet APIs durch deren Suffix. Daher muss jede API eines bestimmten Herausgebers ein eindeutiges Suffix haben.

Konfigurieren Sie die drei Werte und klicken Sie auf **Speichern**. Sobald die neue API erstellt wurde, wird die Zusammenfassungsseite für die API im Verwaltungsportal angezeigt.

![API-Zusammenfassung][API-Zusammenfassung]

## <a name="configure-api-settings"> </a>Konfigurieren der API-Einstellungen

Auf der Registerkarte **Einstellungen** können Sie die Konfiguration einer API anzeigen und bearbeiten. **Titel der Web-API**, **Webdienst-URL** und **URL-Suffix für die Web-API** werden bei der Erstellung der API konfiguriert und können hier geändert werden. **Beschreibung** enthält eine optionale Beschreibung und unter **Mit Anmeldeinformationen** können Sie die HTTP-Standardauthentifizierung konfigurieren.

![API-Einstellungen][API-Einstellungen]

Um die HTTP-Standardauthentifizierung für den Webdienst zu konfigurieren, der die API implementiert, wählen Sie **Einfach** in der Dropdownliste **Mit Anmeldeinformationen** aus und geben Sie die gewünschten Anmeldeinformationen ein.

![Einstellungen für die Standardauthentifizierung][Einstellungen für die Standardauthentifizierung]

Klicken Sie auf **Speichern**, um die Änderungen an Ihren API-Einstellungen zu speichern.

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie die API erstellt und die Einstellungen konfiguriert haben, können Sie Operationen zur API hinzufügen, die API zu einem Produkt hinzufügen und anschließend veröffentlichen, um die API für Entwickler zugänglich zu machen. Weitere Informationen finden Sie in den folgenden beiden Anleitungen.

-   [Hinzufügen von Operationen zu einer API][Hinzufügen von Operationen zu einer API]
-   [Erstellen und Veröffentlichen eines Produkts][Erstellen und Veröffentlichen von Produkten]

  [Hinzufügen von Operationen zu einer API]: ../api-management-howto-add-operations
  [Erstellen und Veröffentlichen von Produkten]: ../api-management-howto-add-products
  [Erstellen einer neuen API]: #create-new-api
  [Konfigurieren der API-Einstellungen]: #configure-api-settings
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [Verwaltungskonsole]: ./media/api-management-howto-create-apis/api-management-management-console.png
  [API erstellen]: ./media/api-management-howto-create-apis/api-management-create-api.png
  [Neue API hinzufügen]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
  [API-Zusammenfassung]: ./media/api-management-howto-create-apis/api-management-api-summary.png
  [API-Einstellungen]: ./media/api-management-howto-create-apis/api-management-api-settings.png
  [Einstellungen für die Standardauthentifizierung]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
