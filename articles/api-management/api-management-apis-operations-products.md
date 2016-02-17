<properties 
	pageTitle="Erstellen von APIs, Operationen und Produkten in Azure API Management" 
	description="Erfahren Sie, wie Sie APIs, Vorgänge und Produkte in API Management erstellen." 
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
	ms.date="02/01/2016" 
	ms.author="sdanie"/>

# Erstellen von APIs, Operationen und Produkten in Azure API Management

In Azure API Management werden APIs und deren Operationen zu Produkten hinzugefügt und können dort von Entwicklern verwendet werden, um Anwendungen unter Verwendung der APIs zu erstellen. Die Anleitungen in diesem Bereich beschreiben, wie Sie eine API erstellen, Operationen zur API hinzufügen, die API zu einem Produkt zuordnen und das Produkt für Entwickler veröffentlichen können.

## <a name="create-apis"> </a>Erstellen von APIs

Eine API in API Management besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. Neue APIs werden im Herausgeberportal erstellt.

Diese Anleitung beschreibt die Erstellung und Konfiguration einer neuen API in API Management.

-   [Erstellen von APIs][]

## <a name="add-operations"> </a>Hinzufügen von Operationen zu einer API

Bevor eine API in API Management verwendet werden kann, müssen Operationen hinzugefügt werden. Diese Anleitung beschreibt, wie Sie unterschiedliche Typen von Operationen zu einer API in API Management hinzufügen können.

-   [Hinzufügen von Operationen zu einer API][]

Eine API und deren Operationen können auch in einem Schritt importiert werden, entweder im WADL- oder im Swagger-Format.

-	[Importieren einer API-Definition mit Operationen][]

## <a name="add-product"> </a>Erstellen und Veröffentlichen eines Produkts

Produkte in API Management enthalten eine oder mehrere APIs sowie ein Nutzungskontingent und Nutzungsbedingungen. Nachdem ein Produkt veröffentlicht wurde, können Entwickler das Produkt abonnieren und die APIs des Produkts verwenden. Diese Themen enthalten eine Anleitung zum Erstellen von Produkten, Hinzufügen von APIs und zum Veröffentlichen des Produkts für Entwickler.

-   [Hinzufügen und Veröffentlichen eines Produkts][]
-	[Erstellen und Konfigurieren erweiterter Produkteinstellungen][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[Erstellen von APIs]: api-management-howto-create-apis.md
[Hinzufügen von Operationen zu einer API]: api-management-howto-add-operations.md
[Hinzufügen und Veröffentlichen eines Produkts]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Importieren einer API-Definition mit Operationen]: api-management-howto-import-api.md
[Erstellen und Konfigurieren erweiterter Produkteinstellungen]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_0204_2016-->