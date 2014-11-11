<properties pageTitle="How create and publish a product in Azure API Management" metaKeywords="" description="Learn how to create and publish products in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How create and publish a product in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Erstellen und Veröffentlichen eines Produkts in der Azure API-Verwaltung

Produkte in der Azure API-Verwaltung (Vorschau) enthalten eine oder mehrere APIs sowie ein Nutzungskontingent und Nutzungsbedingungen. Nachdem ein Produkt veröffentlicht wurde, können Entwickler das Produkt abonnieren und die APIs des Produkts verwenden. Dieses Thema beschreibt, wie Sie ein Produkt erstellen, eine API hinzufügen und das Produkt für Entwickler veröffentlichen können.

## In diesem Thema

-   [Erstellen eines Produkts][Erstellen eines Produkts]
-   [Hinzufügen von APIs zu einem Produkt][Hinzufügen von APIs zu einem Produkt]
-   [Hinzufügen von beschreibenden Informationen zu einem Produkt][Hinzufügen von beschreibenden Informationen zu einem Produkt]
-   [Veröffentlichen des Produkts][Veröffentlichen des Produkts]
-   [Sichtbarmachen des Produkts für Entwickler][Sichtbarmachen des Produkts für Entwickler]
-   [Anzeigen von Abonnenten eines Produkts][Anzeigen von Abonnenten eines Produkts]
-   [Nächste Schritte][Nächste Schritte]

## <a name="create-product"> </a>Erstellen eines Produkts

Operationen werden in der Verwaltungskonsole zu einer API hinzugefügt und konfiguriert. Um die Verwaltungskonsole zu erreichen, klicken Sie im Azure-Portal auf **Verwaltungskonsole** für Ihren API-Verwaltungsdienst.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![API-Verwaltungskonsole][API-Verwaltungskonsole]

Klicken Sie auf **Produkte** im Menü auf der linken Seite, um die Seite **Produkte** anzuzeigen, und klicken Sie auf **Produkt hinzufügen**.

![Produkte][Produkte]

![Neues Produkt][Neues Produkt]

Geben Sie einen beschreibenden Namen für das Produkt in das Feld **Name** und eine Beschreibung in das Feld **Beschreibung** ein.

Wenn Sie möchten, dass ein Administrator Abonnements für dieses Produkt prüfen und ablehnen oder akzeptieren muss, markieren Sie die Option **Abonnementgenehmigung erforderlich**. Wenn dieses Feld nicht markiert ist, werden Abonnements automatisch genehmigt. Weitere Informationen zu Abonnements finden Sie unter [Anzeigen von Abonnenten eines Produkts][Anzeigen von Abonnenten eines Produkts].

![Produkte][1]

> Standardmäßig sind neue Produkte nicht veröffentlicht und nur für die Gruppe **Administratoren** sichtbar.

Klicken Sie auf den entsprechenden Produktnamen auf der Registerkarte **Produkte**, um ein Produkt zu konfigurieren.

## <a name="add-apis"> </a>Hinzufügen von APIs zu einem Produkt

Die Seite **Produkte** enthält vier Links für die Konfiguration: **Zusammenfassung**, **Einstellungen**, **Sichtbarkeit** und **Entwickler**. Auf der Registerkarte **Zusammenfassung** können Sie APIs hinzufügen und die Veröffentlichung von Produkten steuern.

![Zusammenfassung][Zusammenfassung]

Bevor Sie Ihr Produkt veröffentlichen, müssen Sie mindestens eine API hinzufügen. Klicken Sie dazu auf **API zu Produkt hinzufügen**.

![APIs hinzufügen][APIs hinzufügen]

Wählen Sie die gewünschten APIs aus und klicken Sie auf **Speichern**.

## <a name="add-description"> </a>Hinzufügen von beschreibenden Informationen zu einem Produkt

Die Registerkarte **Einstellungen** enthält detaillierte Daten über das Produkt wie z. B. dessen Zweck, die bereitgestellten APIs und sonstige nützliche Informationen. Dieser Inhalt richtet sich an die Entwickler, die die API aufrufen, und kann im Nur-Text-Format oder mit HTML-Markup erstellt werden.

![Produkteinstellungen][Produkteinstellungen]

Markieren Sie die Option **Abonnementgenehmigung erforderlich**, wenn Sie alle Abonnements für dieses Produkt manuell genehmigen möchten. Standardmäßig werden alle Produktabonnements automatisch genehmigt.

Füllen Sie Optional das Feld **Nutzungsbedingungen** aus und beschreiben Sie die Bedingungen, denen die Abonnenten zustimmen müssen, um das Produkt verwenden zu können.

## <a name="publish-product"> </a>Veröffentlichen des Produkts

Bevor die APIs eines Produkts aufgerufen werden können, müssen Sie das Produkt veröffentlichen. Klicken Sie auf der Registerkarte **Zusammenfassung** für das Produkt auf **Veröffentlichen** und anschließend auf **Ja, veröffentlichen**, um Ihre Entscheidung zu bestätigen. Klicken Sie auf **Veröffentlichung aufheben**, um ein bereits veröffentlichtes Produkt privat zu setzen.

![Veröffentlichen des Produkts][2]

## <a name="make-visible"> </a>Sichtbarmachen des Produkts für Entwickler

Auf der Registerkarte **Sichtbarkeit** können Sie die Rollen auswählen, die das Produkt im Entwicklerportal sehen und das Produkt abonnieren können.

![Produktsichtbarkeit][Produktsichtbarkeit]

Konfigurieren Sie die Sichtbarkeit eines Produkts für die Entwickler in einer Gruppe über das Kontrollkästchen neben der Gruppe und klicken Sie auf **Speichern**.

> Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung][Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung].

## <a name="view-subscribers"> </a>Anzeigen von Abonnenten eines Produkts

Die Registerkarte **Entwickler** enthält eine Liste der Entwickler, die das Produkt abonniert haben. Klicken Sie auf den Namen des Entwicklers, um dessen Details und Einstellungen anzuzeigen. In diesem Beispiel wurde das Produkt noch von keinem Entwickler abonniert.

![Entwickler][Entwickler]

## <a name="next-steps"> </a>Nächste Schritte

Sobald Sie die gewünschten APIs hinzugefügt und das Produkt veröffentlicht haben, können Entwickler das Produkt abonnieren und die APIs aufrufen. Ein Lernprogramm mit einer Demonstration dieser Punkte und erweiterten Produktkonfigurationen finden Sie unter [Erstellen und Konfigurieren erweiterter Produkteinstellungen in der Azure API-Verwaltung][Erstellen und Konfigurieren erweiterter Produkteinstellungen in der Azure API-Verwaltung].

  [Erstellen eines Produkts]: #create-product
  [Hinzufügen von APIs zu einem Produkt]: #add-apis
  [Hinzufügen von beschreibenden Informationen zu einem Produkt]: #add-description
  [Veröffentlichen des Produkts]: #publish-product
  [Sichtbarmachen des Produkts für Entwickler]: #make-visible
  [Anzeigen von Abonnenten eines Produkts]: #view-subscribers
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [API-Verwaltungskonsole]: ./media/api-management-howto-add-products/api-management-management-console.png
  [Produkte]: ./media/api-management-howto-add-products/api-management-products.png
  [Neues Produkt]: ./media/api-management-howto-add-products/api-management-add-new-product.png
  [1]: ./media/api-management-howto-add-products/api-management-products-page.png
  [Zusammenfassung]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
  [APIs hinzufügen]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
  [Produkteinstellungen]: ./media/api-management-howto-add-products/api-management-product-settings.png
  [2]: ./media/api-management-howto-add-products/api-management-publish-product.png
  [Produktsichtbarkeit]: ./media/api-management-howto-add-products/api-management-product-visibility.png
  [Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung]: ../api-management-howto-create-groups
  [Entwickler]: ./media/api-management-howto-add-products/api-management-developer-list.png
  [Erstellen und Konfigurieren erweiterter Produkteinstellungen in der Azure API-Verwaltung]: ../api-management-howto-product-with-rules
