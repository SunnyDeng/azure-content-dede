<properties 
	pageTitle="Erstellen und Veröffentlichen eines Produkts in Azure API Management" 
	description="Erfahren Sie, wie Sie Produkte in Azure API Management erstellen und veröffentlichen." 
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
	ms.date="06/10/2015" 
	ms.author="sdanie"/>

# Erstellen und Veröffentlichen eines Produkts in Azure API Management

In der Azure-API-Verwaltung enthält ein Produkt eine oder mehrere APIs sowie ein nutzungskontingent und die Nutzungsbedingungen. Nachdem ein Produkt veröffentlicht wurde, können Entwickler das Produkt abonnieren und die APIs des Produkts verwenden. Dieses Thema beschreibt, wie Sie ein Produkt erstellen, eine API hinzufügen und das Produkt für Entwickler veröffentlichen können.

Weitere Informationen zum Arbeiten mit Produkten finden Sie im folgende Video.

> [AZURE.VIDEO using-products]

## <a name="create-product"> </a>Erstellen eines Produkts

Operationen werden einer API im Herausgeberportal hinzugefügt und in diesem konfiguriert. Um auf das Herausgeberportal zuzugreifen, klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**.

![Herausgeberportal][api-management-management-console]

>Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][].

Klicken Sie auf **Produkte** im Menü auf der linken Seite, um die Seite **Produkte** anzuzeigen, und klicken Sie auf **Produkt hinzufügen**.

![Produkte][api-management-products]

![Neues Produkt][api-management-add-new-product]

Geben Sie einen beschreibenden Namen für das Produkt in das Feld **Name** und eine Beschreibung in das Feld **Beschreibung** ein.

Produkte in der API-Verwaltung kann **Öffnen** oder **Protected**. Geschützte Produkte müssen abonniert werden, bevor Sie verwendet werden können, während offene Produkte ohne Abonnement genutzt werden können. Überprüfen Sie **erfordern Abonnement** einer geschützten Produkt zu erstellen, ein Abonnement erforderlich ist. Dies ist die Standardeinstellung.

Wenn Sie möchten, dass ein Administrator Abonnements für dieses Produkt prüfen und ablehnen oder akzeptieren muss, markieren Sie die Option **Abonnementgenehmigung erforderlich**. Wenn dieses Feld nicht markiert ist, werden Abonnements automatisch genehmigt. Weitere Informationen zu Abonnements finden Sie unter [Anzeigen von Abonnenten eines Produkts][].

Damit entwicklerkonten mehrmals das Produkt abonnieren können, überprüfen Sie die **mehrere Abonnements zulassen** Kontrollkästchen. Wenn dieses Kontrollkästchen nicht aktiviert ist, kann jede Entwicklerkonto nur ein einziges Mal für das Produkt abonnieren.

![Unbegrenzte mehrere Abonnements][api-management-unlimited-multiple-subscriptions]

Um die Anzahl der mehrere gleichzeitige Abonnements zu beschränken, überprüfen Sie die **Beschränken der Anzahl von gleichzeitigen Abonnements für** das Kontrollkästchen, und geben Sie die zulässige Anzahl an. Im folgenden Beispiel sind die gleichzeitige Abonnements auf vier pro Entwicklerkonto beschränkt.

![Vier mehrere Abonnements][api-management-four-multiple-subscriptions]

Sobald alle neuen Produktoptionen konfiguriert sind, klicken Sie auf **Speichern** zum Erstellen des neuen Produkts.

![Produkte][api-management-products-page]

>Standardmäßig sind neue Produkte nicht veröffentlicht und nur für die Gruppe **Administratoren** sichtbar.

Klicken Sie auf den entsprechenden Produktnamen auf der Registerkarte **Produkte**, um ein Produkt zu konfigurieren.

## <a name="add-apis"> </a>APIs zu einem Produkt hinzufügen

Die **Produkte** Seite enthält vier Links für die Konfiguration: **Zusammenfassung**, **Einstellungen**, **Sichtbarkeit**, und **Abonnenten**. Die **Zusammenfassung** Registerkarte ist, können APIs hinzufügen und veröffentlichen oder Aufheben der Veröffentlichung eines Produkts.

![Zusammenfassung][api-management-new-product-summary]

Bevor Sie Ihr Produkt veröffentlichen, müssen Sie mindestens eine API hinzufügen. Klicken Sie dazu auf **API zu Produkt hinzufügen**.

![APIs hinzufügen][api-management-add-apis-to-product]

Wählen Sie die gewünschten APIs aus und klicken Sie auf **Speichern**.

## <a name="add-description"> </a>Beschreibende Informationen zu einem Produkt hinzufügen

Die Registerkarte **Einstellungen** enthält detaillierte Daten über das Produkt wie z. B. dessen Zweck, die bereitgestellten APIs und sonstige nützliche Informationen. Dieser Inhalt richtet sich an die Entwickler, die die API aufrufen, und kann im Nur-Text-Format oder mit HTML-Markup erstellt werden.

![Produkteinstellungen][api-management-product-settings]

Überprüfen Sie **erfordern Abonnement** einer geschützten Produkt zu erstellen, erfordert ein Abonnement verwendet werden, oder deaktivieren das Kontrollkästchen, um ein open-Produkt zu erstellen, ohne ein Abonnement aufgerufen werden können.

Markieren Sie die Option **Abonnementgenehmigung erforderlich**, wenn Sie alle Abonnements für dieses Produkt manuell genehmigen möchten. Standardmäßig werden alle Produktabonnements automatisch genehmigt.

Damit entwicklerkonten mehrmals das Produkt abonnieren können, überprüfen Sie die **mehrere Abonnements zulassen** Kontrollkästchen und geben Sie optional einen Grenzwert. Wenn dieses Kontrollkästchen nicht aktiviert ist, kann jede Entwicklerkonto nur ein einziges Mal für das Produkt abonnieren.

Füllen Sie Optional das Feld **Nutzungsbedingungen** aus und beschreiben Sie die Bedingungen, denen die Abonnenten zustimmen müssen, um das Produkt verwenden zu können.

## <a name="publish-product"> </a>Veröffentlichen ein Produkts

Bevor die APIs eines Produkts aufgerufen werden können, müssen Sie das Produkt veröffentlichen. Klicken Sie auf der Registerkarte **Zusammenfassung** für das Produkt auf **Veröffentlichen** und anschließend auf **Ja, veröffentlichen**, um Ihre Entscheidung zu bestätigen. Klicken Sie auf **Veröffentlichung aufheben**, um ein bereits veröffentlichtes Produkt privat zu setzen.

![Veröffentlichen des Produkts][api-management-publish-product]

## <a name="make-visible"> </a>Ein Produkts für Entwickler sichtbar zu machen

Auf der Registerkarte **Sichtbarkeit** können Sie die Rollen auswählen, die das Produkt im Entwicklerportal sehen und das Produkt abonnieren können.

![Produktsichtbarkeit][api-management-product-visiblity]

Konfigurieren Sie die Sichtbarkeit eines Produkts für die Entwickler in einer Gruppe über das Kontrollkästchen neben der Gruppe und klicken Sie auf **Speichern**.

>Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung][].

## <a name="view-subscribers"> </a>Abonnenten zu einem Produkt anzeigen

Die **Abonnenten** Registerkarte aufgelistet, die Entwickler, die das Produkt abonniert haben. Klicken Sie auf den Namen des Entwicklers, um dessen Details und Einstellungen anzuzeigen. In diesem Beispiel wurde das Produkt noch von keinem Entwickler abonniert.

![Entwickler][api-management-developer-list]

## <a name="next-steps"> </a>Nächste Schritte

Sobald Sie die gewünschten APIs hinzugefügt und das Produkt veröffentlicht haben, können Entwickler das Produkt abonnieren und die APIs aufrufen. Ein Lernprogramm mit einer Demonstration dieser Punkte und erweiterten Produktkonfigurationen finden Sie unter [Erstellen und Konfigurieren erweiterter Produkteinstellungen in der Azure API-Verwaltung][].


[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Anzeigen von Abonnenten eines Produkts]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Erstellen und Verwenden von Gruppen für Entwicklerkonten in der Azure API-Verwaltung]: api-management-howto-create-groups.md
[Erstellen und Konfigurieren erweiterter Produkteinstellungen in der Azure API-Verwaltung]: api-management-howto-product-with-rules.md

<!---HONumber=GIT-SubDir_Tue_AM_dede-->