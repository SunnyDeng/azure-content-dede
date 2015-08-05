<properties 
	pageTitle="API Management-Schlüsselkonzepte" 
	description="Erfahren Sie mehr über APIs, Produkte, Rollen, Gruppen und andere API Management-Schlüsselkonzepte." 
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
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Importieren einer API-Definition mit Operationen in Azure API Management

In API Management können Sie neue APIs entweder manuell erstellen und Operationen manuell hinzufügen, oder Sie können die API zusammen mit den Operationen in einem Schritt importieren.

APIs und deren Operationen können in den folgenden Formaten importiert werden.

-	WADL
-	Swagger

Diese Anleitung beschreibt die Erstellung einer neuen API und den Import von Operationen in einem Schritt. Weitere Informationen zur manuellen Erstellung von APIs und zum Hinzufügen von Operationen finden Sie unter [Erstellen von APIs][] und [Hinzufügen von Operationen zu einer API][].

## <a name="import-api"> </a>Importieren einer API

APIs werden im Herausgeberportal erstellt und konfiguriert. Um auf das Herausgeberportal zuzugreifen, klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Verwalten**. Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][] im Lernprogramm [Erste Schritte mit Azure API Management][].

![Herausgeberportal][api-management-management-console]

Klicken Sie auf **APIs** im Menü **API Management** auf der linken Seite, und klicken Sie anschließend auf **API importieren**.

![Importieren einer API][api-management-import-apis]

Die drei Registerkarten im Fenster **API importieren** entsprechen den drei Formaten, in denen Sie die API-Spezifikation angeben können.

-	Mit der Option **Aus Zwischenablage** können Sie die API-Spezifikation in das entsprechende Textfeld einfügen.
-	Mit der Option **Aus Datei** können Sie eine Datei mit der API-Spezifikation auswählen.
-	Mit der Option **Aus URL** können Sie die URL der API-Spezifikation angeben.

![API-Importformat][api-management-import-api-clipboard]

Geben Sie die API-Spezifikation an und verwenden Sie die Optionsfelder auf der rechten Seite, um das Spezifikationsformat auszuwählen. Die folgenden Formate werden unterstützt.

-	WADL
-	Swagger

Geben Sie anschließend ein **URL-Suffix für die Web-API** ein. Dieses Suffix wird an die Basis-URL für Ihren API Management-Dienst angehängt. Alle APIs auf einer Instanz eines API Management-Diensts teilen sich dieselbe Basis-URL. API Management unterscheidet APIs durch deren Suffix. Daher muss jede API in einer bestimmten API Management-Dienstinstanz ein eindeutiges Suffix aufweisen.

Geben Sie alle Werte ein, und klicken Sie auf **Speichern**, um die API und die entsprechenden Operationen zu erstellen.

>[AZURE.NOTE]Ein Lernprogramm zum Importieren einer einfachen Taschenrechner-API im Swagger-Format finden Sie unter [Verwalten Ihrer ersten API in Azure API Management](api-management-get-started.md).

## <a name="export-api"> </a>Exportieren einer API

Sie können nicht nur neue APIs importieren, sondern auch die Definitionen Ihrer APIs aus dem Herausgeberportal exportieren. Klicken Sie dazu auf **API exportieren** auf der Registerkarte **Zusammenfassung** für Ihre **API**.

![Exportieren einer API][api-management-export-api]

APIs können im WADL- oder Swagger-Format exportiert werden. Wählen Sie das gewünschte Format aus, klicken Sie auf **Speichern**, und wählen Sie einen Speicherort für die Datei aus.

![API-Exportformat][api-management-export-api-format]

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie die API erstellt und die Operationen importiert haben, können Sie zusätzliche Einstellungen prüfen und konfigurieren, die API zu einem Produkt hinzufügen und anschließend veröffentlichen, um die API für Entwickler zugänglich zu machen. Weitere Informationen finden Sie in den folgenden Anleitungen.

-	[Konfigurieren von API-Einstellungen][]
-	[Erstellen und Veröffentlichen eines Produkts][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Erste Schritte mit Azure API Management]: api-management-get-started.md
[Erstellen einer API Management-Dienstinstanz]: api-management-get-started.md#create-service-instance

[Hinzufügen von Operationen zu einer API]: api-management-howto-add-operations.md
[Erstellen und Veröffentlichen eines Produkts]: api-management-howto-add-products.md
[Erstellen von APIs]: api-management-howto-create-apis.md
[Konfigurieren von API-Einstellungen]: api-management-howto-create-apis.md#configure-api-settings

<!---HONumber=July15_HO4-->