<properties pageTitle="Erste Schritte mit der Azure API-Verwaltung" metaKeywords="" description="Erfahren Sie mehr &uuml;ber das Erstellen von APIs und Vorg&auml;ngen sowie &uuml;ber die ersten Schritte mit der API-Verwaltung." metaCanonical="" services="api-management" documentationCenter="API Management" title="Erste Schritte mit der Azure API-Verwaltung" authors="sdanie" solutions="" manager="dwrede" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Erste Schritte mit der Azure API-Verwaltung

Diese Anleitung beschreibt die ersten Schritte mit der API-Verwaltung und Ihren ersten API-Aufruf.

> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

Der erste Schritt bei der Arbeit mit der API-Verwaltung ist die Erstellung einer Dienstinstanz. Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an und klicken Sie auf **Neu**, **App-Dienste**, **API-Verwaltung**, **Erstellen**.

![Neue Instanz der API-Verwaltung][Neue Instanz der API-Verwaltung]

Geben Sie unter **URL** einen eindeutigen Unterdomänennamen für die Dienst-URL ein.

Wählen Sie die gewünschten Werte für **Preisebene**, **Abonnement** und **Region** für Ihre Dienstinstanz aus. Für dieses Lernprogramm können Sie eine beliebige Preisebene verwenden. Treffen Sie Ihre Auswahl und klicken Sie auf Weiter.

![Neuer API-Verwaltungs-Dienst][Neuer API-Verwaltungs-Dienst]

Geben Sie **Contoso Ltd.** unter **Organisationsname** ein und geben Sie Ihre E-Mail-Adresse in das Feld Administrator-E-Mail ein.

> Diese E-Mail-Adresse wird für Benachrichtigungen vom API-Verwaltungssystem verwendet. Weitere Informationen finden Sie unter [Benachrichtigungen konfigurieren][Benachrichtigungen konfigurieren].

Aktivieren Sie das Kontrollkästchen, um Ihre Dienstinstanz zu erstellen.

![Neuer API-Verwaltungs-Dienst][1]

![Neuer API-Verwaltungs-Dienst][2]

Nach der Erstellung der Dienstinstanz können Sie Ihre API erstellen.

## <a name="create-api"> </a>Erstellen einer API

Eine API besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. API-Operationen funktionieren als Proxies für existierende Webdienste.

Jede API-Verwaltungs-Dienstinstanz wird mit einer Beispiel-Echo-API eingerichtet, die Sie mit einem beliebigen HTTP-Verb aufrufen können. Der Rückgabewert enthält die Header und den Text, die Sie übermittelt haben. Dieses Lernprogramm verwendet den Back-End-Webdienst für die Echo-API, um eine neue API mit dem Namen **My Echo Service** in der API-Verwaltung zu erstellen.

APIs werden in der API-Verwaltungskonsole erstellt und konfiguriert, die über die Azure-Verwaltungskonsole erreichbar ist. Um die API-Verwaltungskonsole zu erreichen, klicken Sie im Azure-Portal auf **Verwaltungskonsole** für Ihren API-Verwaltungsdienst.

![Neue API-Verwaltungskonsole][Neue API-Verwaltungskonsole]

Um die **My Echo API** zu erstellen, klicken Sie auf **APIs** im Menü **API-Verwaltung** auf der linken Seite und anschließend auf **API hinzufügen**.

![API erstellen][API erstellen]

![Neue API hinzufügen][Neue API hinzufügen]

Die folgenden drei Felder werden zum Konfigurieren der neuen API verwendet.

-   Geben Sie **My Echo API** in das Textfeld **Titel der Web-API** ein. **Titel der Web-API** enthält einen eindeutigen und beschreibenden Namen für die API. Dieser Name wird in den Entwickler- und Verwaltungsportalen angezeigt.
-   Geben Sie **<http://echoapi.cloudapp.net/api>** in das Feld **Webdienst-URL** ein. **Webdienst-URL** verweist auf den HTTP-Dienst, der die API implementiert. Die API-Verwaltung leitet Anfragen an diese Adresse weiter.
-   Geben Sie **myecho** in das Feld **URL-Suffix für Web-API** ein. **URL-Suffix für Web-API** wird an die Basis-URL für den API-Verwaltungsdienst angehängt. Ihre APIs teilen eine gemeinsame Basis-URL und unterscheiden sich durch ein eindeutiges Suffix, das an die Basis angehängt wird.

Klicken Sie auf **Speichern**, um die API zu erstellen. Sobald die neue API erstellt wurde, wird die Zusammenfassungsseite für die API im Verwaltungsportal angezeigt.

![API-Zusammenfassung][API-Zusammenfassung]

> Die Echo-API verwendet keine Authentifizierung. Weitere Informationen zum Konfigurieren der Authentifizierung finden Sie unter [Konfigurieren der API-Einstellungen][Konfigurieren der API-Einstellungen].

## <a name="add-operation"> </a>Hinzufügen einer Operation

Klicken Sie auf **Operationen**, um den Operationsbereich für die API anzuzeigen. Die Operationsdefinitionen dienen zur Validierung eingehender Anfragen und zur automatischen Generierung der Dokumentation. Da Sie noch keine Operationen erstellt haben, ist dieser Bereich leer.

![Operationen][Operationen]

Klicken Sie auf **Operation hinzufügen**, um eine neue Operation zu erstellen. Das Fenster **Neue Operation** wird angezeigt, und die Registerkarte **Signatur** ist standardmäßig ausgewählt.

![Signatur der Operation][Signatur der Operation]

In diesem Beispiel definieren wir eine GET-Operation für den Echo-Dienst. Geben Sie die folgenden Werte in die Felder auf der Registerkarte **Signatur** ein.

-   Geben Sie **GET** in das Textfeld **HTTP-Verb** ein. Wenn Sie mit der Texteingabe beginnen, können Sie **GET** aus der angezeigten Liste mit HTTP-Verben auswählen.
-   Geben Sie **/resource** in das Textfeld **URL-Vorlage** ein.
-   Geben Sie **GET resource** in das Textfeld **Anzeigename** ein.
-   Geben Sie **Demonstration eines GET-Aufrufs auf eine Beispielressource. Der Aufruf wird von einem "echo"-Back-End verarbeitet, das die Anfrage als Antwort zurückgibt (Header und Text werden unverändert zurückgegeben).** in das Textfeld **Beschreibung** ein. Mit dieser Beschreibung wird die Dokumentation für diese Operation generiert, wenn Entwickler die API verwenden.

Klicken Sie auf **Parameter**, um die Abfrageparameter für diese Operation zu konfigurieren. Klicken Sie auf **Abfrageparameter hinzufügen** und geben Sie die folgenden Werte ein, um einen Abfrageparameter zu erstellen.

-   Geben Sie **param1** in das Textfeld **Name** ein.
-   Geben Sie **Ein benötigter Beispielparameter.** in das Textfeld **Beschreibung** ein.
-   Klicken Sie auf das Feld **Type** und wählen Sie **Zeichenfolge** aus der Liste aus. Die unterstützten Typen sind **Zeichenfolge**, **Zahl**, **Boolescher Wert** und **DateTime**.
-   Klicken Sie auf das Feld **Werte**, geben Sie **Beispiel** in das Textfeld ein und klicken Sie auf das Pluszeichen, um den Text für den Standardwert des Parameters einzurichten. Nachdem Sie den Standardtext eingerichtet haben, klicken Sie außerhalb des Felds **Werte**, um das Fenster Werte zu schließen.
-   Aktivieren Sie das Kontrollkästchen **Erforderlich**.

Klicken Sie auf **Speichern**, um die neu konfigurierte Operation zur API hinzuzufügen.

## <a name="add-api-to-product"> </a>Hinzufügen der API zu einem Produkt

Entwickler müssen ein Produkt zunächst abonnieren, um API-Aufrufe ausführen zu können. Ein Produkt bietet Zugriff auf eine oder mehrere APIs und kann Zugriffseinschränkungen wie z. B. Nutzungskontingente und Durchsatzgrenzen enthalten. In diesem Schritt des Lernprogramms werden Sie die My Echo API zu einem existierenden Produkt hinzufügen.

Klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite, um die verfügbaren Produkte in dieser API-Instanz anzuzeigen und zu konfigurieren.

![Produkte][Produkte]

Standardmäßig enthält jede API-Verwaltungsinstanz zwei Beispielprodukte:

-   **Starter**
-   **Unbegrenzt**

In diesem Lernprogramm verwenden wir das Produkt **Starter**. Klicken Sie auf **Starter**, um die Einstellungen anzuzeigen, inklusive der APIs, die diesem Produkt zugeordnet sind.

![API hinzufügen][API hinzufügen]

Klicken Sie auf **API zu Produkt hinzufügen**.

![API hinzufügen][3]

Markieren Sie das Kontrollkästchen für **My Echo API** und klicken Sie auf **Speichern**.

![Hinzugefügte API][Hinzugefügte API]

Die **My Echo API** ist nun zu einem Produkt zugeordnet, und Entwickler können das Produkt abonnieren und die API verwenden.

> Dieser Schritt des Lernprogramms verwendet das **Starter**-Produkt, das vorkonfiguriert und einsatzbereit ist. Eine schrittweise Anleitung zur Erstellung und Veröffentlichung neuer Produkte finden Sie unter [Erstellen und Veröffentlichen von Produkten][Erstellen und Veröffentlichen von Produkten].

Der Administrator-Benutzer abonniert automatisch alle Produkte und hat Zugriff auf alle APIs, die diese Produkte bereitstellen. Daher ist es nicht notwendig, das soeben erstellte Produkt manuell zu abonnieren, um Aufrufe auszuführen.

## <a name="call-operation"> </a>Aufrufen einer Operation aus dem Entwicklerportal

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Schritt des Lernprogramms werden Sie die Get-Methode aufrufen, die Sie zur **My Echo API** hinzugefügt haben. Klicken Sie auf **Entwicklerportal** im Menü oben rechts im Verwaltungsportal.

![Entwicklerportal][Entwicklerportal]

Klicken Sie im obersten Menü auf **APIs** und anschließend auf **My Echo API**, um die verfügbaren Operationen anzuzeigen.

![Entwicklerportal][4]

Die Beschreibung und Parameter, die Sie bei der Erstellung der Operation eingegeben haben, werden hier angezeigt und bieten Entwicklern eine Dokumentation für die Ausführung dieser Operation.

Klicken Sie auf **GET Resource** und anschließend auf **Konsole öffnen**.

![Operationskonsole][Operationskonsole]

Geben Sie beliebige Werte für die Parameter sowie Ihren Entwicklerschlüssel ein und klicken Sie auf **HTTP Get**.

![HTTP Get][HTTP Get]

Nach dem Aufruf der Operation zeigt das Entwicklerportal die **Angeforderte URL** vom Back-End-Dienst, den **Antwortstatus**, die **Antwortheader** sowie den **Antwortinhalt** an.

![Antwort][Antwort]

## <a name="next-steps"> </a>Nächste Schritte

-   Konfigurieren von Richtlinien
-   Anpassen des Entwicklerportals
-   Verfolgung von Aufrufen mit dem API-Inspektor

  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Neue Instanz der API-Verwaltung]: ./media/api-management-get-started/api-management-create-instance-menu.png
  [Neuer API-Verwaltungs-Dienst]: ./media/api-management-get-started/api-management-create-instance-step1.png
  [Benachrichtigungen konfigurieren]: ../api-management-howto-configure-notifications
  [1]: ./media/api-management-get-started/api-management-create-instance-step2.png
  [2]: ./media/api-management-get-started/api-management-instance-created.png
  [Neue API-Verwaltungskonsole]: ./media/api-management-get-started/api-management-management-console.png
  [API erstellen]: ./media/api-management-get-started/api-management-create-api.png
  [Neue API hinzufügen]: ./media/api-management-get-started/api-management-add-new-api.png
  [API-Zusammenfassung]: ./media/api-management-get-started/api-management-new-api-summary.png
  [Konfigurieren der API-Einstellungen]: ../api-management-howto-create-apis/#configure-api-settings
  [Operationen]: ./media/api-management-get-started/api-management-myecho-operations.png
  [Signatur der Operation]: ./media/api-management-get-started/api-management-operation-signature.png
  [Produkte]: ./media/api-management-get-started/api-management-list-products.png
  [API hinzufügen]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [3]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [Hinzugefügte API]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Erstellen und Veröffentlichen von Produkten]: ../api-management-howto-add-products
  [Entwicklerportal]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [4]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operationskonsole]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Antwort]: ./media/api-management-get-started/api-management-invoke-get-response.png
