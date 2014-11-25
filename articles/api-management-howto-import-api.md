<properties pageTitle="API Management key concepts" metaKeywords="" description="Learn about APIs, products, roles, groups, and other API Management key concepts." metaCanonical="" services="" documentationCenter="API Management" title="API Management key concepts" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Importieren einer API-Definition mit Operationen in der Azure API-Verwaltung

In der API-Verwaltung (Vorschau) können Sie neue APIs entweder manuell erstellen und Operationen manuell hinzufügen, oder Sie können die API zusammen mit den Operationen in einem Schritt importieren.

APIs und deren Operationen können in den folgenden Formaten importiert werden.

-   WADL
-   Swagger

Diese Anleitung beschreibt die Erstellung einer neuen API und den Import von Operationen in einem Schritt.

> Weitere Informationen zur manuellen Erstellung von APIs und zum Hinzufügen von Operationen finden Sie unter [Erstellen von APIs][Erstellen von APIs] und [Hinzufügen von Operationen zu einer API][Hinzufügen von Operationen zu einer API].

## In diesem Thema

-   [Importieren einer API][Importieren einer API]
-   [Exportieren einer API][Exportieren einer API]
-   [Nächste Schritte][Nächste Schritte]

## <a name="import-api"> </a>Importieren einer API

Klicken Sie im Azure-Portal für Ihren API-Verwaltungsdienst auf **Verwaltungskonsole**, um APIs zu erstellen und zu konfigurieren. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![Verwaltungskonsole][Verwaltungskonsole]

Klicken Sie auf **APIs** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **API importieren**.

![Importieren einer API][1]

Die drei Registerkarten im Fenster **API importieren** entsprechen den drei Formaten, in denen Sie die API-Spezifikation angeben können.

-   Mit der Option **Aus Zwischenablage** können Sie die API-Spezifikation in das entsprechende Textfeld einfügen.
-   Mit der Option **Aus Datei** können Sie eine Datei mit der API-Spezifikation auswählen.
-   Mit der Option **Aus URL** können Sie die URL der API-Spezifikation angeben.

![API-Importformat][API-Importformat]

Geben Sie die API-Spezifikation an und verwenden Sie die Optionsfelder auf der rechten Seite, um das Spezifikationsformat auszuwählen. Die folgenden Formate werden unterstützt.

-   WADL
-   Swagger

Geben Sie anschließend ein **URL-Suffix für die Web-API** ein. Dieses Suffix wird an die Basis-URL für Ihren API-Verwaltungsdienst angehängt. Alle APIs auf einer Instanz eines API-Verwaltungsdiensts teilen sich dieselbe Basis-URL. Die API-Verwaltung unterscheidet APIs durch deren Suffix. Daher muss jede API in einer bestimmten API-Verwaltungs-Dienstinstanz ein eindeutiges Suffix haben.

Geben Sie alle Werte ein und klicken Sie auf **Speichern**, um die API und die entsprechenden Operationen zu erstellen.

## <a name="export-api"> </a>Exportieren einer API

Sie können nicht nur neue APIs importieren, sondern auch die Definitionen Ihrer APIs aus der Verwaltungskonsole exportieren. Klicken Sie dazu auf **API exportieren** auf der Registerkarte **Zusammenfassung** für Ihre **API**.

![Exportieren einer API][2]

APIs können im WADL- oder Swagger-Format exportiert werden. Wählen Sie das gewünschte Format aus, klicken Sie auf **Speichern** und wählen Sie einen Speicherort für die Datei aus.

![API-Exportformat][API-Exportformat]

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie die API erstellt und die Operationen importiert haben, können Sie zusätzliche Einstellungen prüfen und konfigurieren, die API zu einem Produkt hinzufügen und anschließend veröffentlichen, um die API für Entwickler zugänglich zu machen. Weitere Informationen finden Sie in den folgenden Anleitungen.

-   [Konfigurieren der API-Einstellungen][Konfigurieren der API-Einstellungen]
-   [Erstellen und Veröffentlichen eines Produkts][Erstellen und Veröffentlichen eines Produkts]

  [Erstellen von APIs]: ../api-management-howto-create-apis
  [Hinzufügen von Operationen zu einer API]: ../api-management-howto-add-operations
  [Importieren einer API]: #import-api
  [Exportieren einer API]: #export-api
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [Verwaltungskonsole]: ./media/api-management-howto-import-api/api-management-management-console.png
  [1]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
  [API-Importformat]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
  [2]: ./media/api-management-howto-import-api/api-management-export-api.png
  [API-Exportformat]: ./media/api-management-howto-import-api/api-management-export-api-format.png
  [Konfigurieren der API-Einstellungen]: ../api-management-howto-create-apis/#configure-api-settings
  [Erstellen und Veröffentlichen eines Produkts]: ../api-management-howto-add-products
