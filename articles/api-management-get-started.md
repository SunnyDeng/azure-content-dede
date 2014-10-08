<properties pageTitle="Get started with Azure API Management" metaKeywords="" description="Learn how to create APIs, operations, and get started with API Management." metaCanonical="" services="" documentationCenter="API Management" title="Get started with Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Erste Schritte mit der Azure API-Verwaltung

Diese Anleitung beschreibt die ersten Schritte mit der API-Verwaltung und Ihren ersten API-Aufruf.

## In diesem Thema

-   [Erstellen einer API-Verwaltungsinstanz][]
-   [Erstellen einer API][]
-   [Hinzufügen einer Operation][]
-   [Hinzufügen der API zu einem Produkt][]
-   [Abonnieren des Produkts, das die API enthält][]
-   [Aufrufen einer Operation aus dem Entwicklerportal][]
-   [Anzeigen von Analysen][]
-   [Nächste Schritte][]

## <a name="create-service-instance"> </a>Erstellen einer API-Verwaltungsinstanz

> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][].

Der erste Schritt bei der Arbeit mit der API-Verwaltung ist die Erstellung einer Dienstinstanz. Melden Sie sich beim [Azure-Verwaltungsportal][] an und klicken Sie auf **Neu**, **App-Dienste**, **API-Verwaltung**, **Erstellen**.

![Neue Instanz der API-Verwaltung][]

Geben Sie unter **URL** einen eindeutigen Unterdomänennamen für die Dienst-URL ein.

Wählen Sie die gewünschten Werte für **Preisebene**, **Abonnement** und **Region** für Ihre Dienstinstanz aus. Für dieses Lernprogramm können Sie eine beliebige Preisebene verwenden. Treffen Sie Ihre Auswahl und klicken Sie auf Weiter.

![Neuer API-Verwaltungs-Dienst][]

Geben Sie **Contoso Ltd.** unter **Organisationsname** ein und geben Sie Ihre E-Mail-Adresse in das Feld Administrator-E-Mail ein.

> Diese E-Mail-Adresse wird für Benachrichtigungen vom API-Verwaltungssystem verwendet. Weitere Informationen finden Sie unter [Benachrichtigungen konfigurieren][].

Aktivieren Sie das Kontrollkästchen, um Ihre Dienstinstanz zu erstellen.

![Neuer API-Verwaltungs-Dienst][1]

![Neuer API-Verwaltungs-Dienst][2]

Nach der Erstellung der Dienstinstanz können Sie Ihre API erstellen.

## <a name="create-api"> </a>Erstellen einer API

Eine API besteht aus einem Satz von Operationen, die von Clientanwendungen aufgerufen werden können. API-Operationen funktionieren als Proxies für existierende Webdienste.

Jede API-Verwaltungs-Dienstinstanz enthält eine vorkonfigurierte Echo-API, die übermittelte Eingaben an den Aufrufer zurückgibt. Um diese API zu verwenden, können Sie ein beliebiges HTTP-Verb aufrufen. Der Rückgabewert enthält die Header und den Text, die Sie übermittelt haben.

Dieses Lernprogramm verwendet den <http://echoapi.cloudapp.net/api>-Webdienst, um eine neue API mit dem Namen **My Echo Service** in der API-Verwaltung zu erstellen.

APIs werden in der API-Verwaltungskonsole erstellt und konfiguriert, die über die Azure-Verwaltungskonsole erreichbar ist. Um die API-Verwaltungskonsole zu erreichen, klicken Sie im Azure-Portal auf **Verwaltungskonsole** für Ihren API-Verwaltungsdienst.

![Neue API-Verwaltungskonsole][]

Um die **My Echo API** zu erstellen, klicken Sie auf **APIs** im Menü **API-Verwaltung** auf der linken Seite und anschließend auf **API hinzufügen**.

![API erstellen][]

![Neue API hinzufügen][]

Die folgenden drei Felder werden zum Konfigurieren der neuen API verwendet.

-   Geben Sie **My Echo API** in das Textfeld **Titel der Web-API** ein. **Titel der Web-API** enthält einen eindeutigen und beschreibenden Namen für die API. Dieser Name wird in den Entwickler- und Verwaltungsportalen angezeigt.
-   Geben Sie **<http://echoapi.cloudapp.net/api>** in das Feld **Webdienst-URL** ein. **Webdienst-URL** verweist auf den HTTP-Dienst, der die API implementiert. Die API-Verwaltung leitet Anfragen an diese Adresse weiter.
-   Geben Sie **myecho** in das Feld **URL-Suffix für Web-API** ein. **URL-Suffix für Web-API** wird an die Basis-URL für den API-Verwaltungsdienst angehängt. Ihre APIs teilen eine gemeinsame Basis-URL und unterscheiden sich durch ein eindeutiges Suffix, das an die Basis angehängt wird.

Klicken Sie auf **Speichern**, um die API zu erstellen. Sobald die neue API erstellt wurde, wird die Zusammenfassungsseite für die API im Verwaltungsportal angezeigt.

![API-Zusammenfassung][]

Der API-Bereich enthält vier Registerkarten. Die Registerkarte **Zusammenfassung** enthält verschiedene Metriken und Informationen über die API. In der Registerkarte **Einstellungen** können Sie die Konfiguration der API anzeigen und bearbeiten, inklusive der Authentifizierungs-Anmeldeinformationen für den Back-End-Dienst. In der Registerkarte **Operationen** können Sie die Operationen der API verwalten, wie im folgenden Schritt in diesem Lernprogramm beschrieben. In der Registerkarte **Probleme** werden Probleme angezeigt, die Entwickler für Ihre APIs gemeldet haben.

> Die Echo-API verwendet keine Authentifizierung. Weitere Informationen zum Konfigurieren der Authentifizierung finden Sie unter [Konfigurieren der API-Einstellungen][].

Nachdem Sie die API erstellt und die Einstellungen konfiguriert haben, können Sie Operationen zur API hinzufügen. Die Operationsdefinitionen dienen zur Validierung eingehender Anfragen und zur automatischen Generierung der Dokumentation.

## <a name="add-operation"> </a>Hinzufügen einer Operation

Klicken Sie auf **Operationen**, um den Operationsbereich für die API anzuzeigen. Da Sie noch keine Operationen erstellt haben, ist dieser Bereich leer.

![Operationen][]

Klicken Sie auf **Operation hinzufügen**, um eine neue Operation zu erstellen. Das Fenster **Neue Operation** wird angezeigt, und die Registerkarte **Signatur** ist standardmäßig ausgewählt.

![Signatur der Operation][]

In diesem Beispiel definieren wir eine GET-Operation für den Echo-Dienst. Geben Sie die folgenden Werte in die Felder auf der Registerkarte **Signatur** ein.

-   Geben Sie **GET** in das Textfeld **HTTP-Verb** ein. Wenn Sie mit der Texteingabe beginnen, können Sie **GET** aus der angezeigten Liste mit HTTP-Verben auswählen.
-   Geben Sie **/resource** in das Textfeld **URL-Vorlage** ein.
-   Geben Sie **GET resource** in das Textfeld **Anzeigename** ein.
-   Geben Sie **Demonstration eines GET-Aufrufs auf eine Beispielressource. Der Aufruf wird von einem "echo"-Back-End verarbeitet, das die Anfrage als Antwort zurückgibt (Header und Text werden unverändert zurückgegeben).** in das Textfeld **Beschreibung** ein. Mit dieser Beschreibung wird die Dokumentation für diese Operation generiert, wenn Entwickler die API verwenden.

Klicken Sie auf **Parameter**, um die Abfrageparameter für diese Operation zu konfigurieren. Dieses Beispiel verwendet zwei Abfrageparameter. Klicken Sie auf **Abfrageparameter hinzufügen** und geben Sie die folgenden Werte ein, um einen Abfrageparameter zu erstellen.

Konfigurieren Sie die folgenden Werte für den ersten Abfrageparameter.

-   Geben Sie **param1** in das Textfeld **Name** ein.
-   Geben Sie **Ein benötigter Beispielparameter.** in das Textfeld **Beschreibung** ein.
-   Klicken Sie auf das Feld **Type** und wählen Sie **Zeichenfolge** aus der Liste aus. Die unterstützten Typen sind **Zeichenfolge**, **Zahl**, **Boolescher Wert** und **DateTime**.
-   Klicken Sie auf das Feld **Werte**, geben Sie **Beispiel** in das Textfeld ein und klicken Sie auf das Pluszeichen, um den Text für den Standardwert des Parameters einzurichten. Nachdem Sie den Standardtext eingerichtet haben, klicken Sie außerhalb des Felds **Werte**, um das Fenster Werte zu schließen.
-   Aktivieren Sie das Kontrollkästchen **Erforderlich**.

Konfigurieren Sie die folgenden Werte für den zweiten Abfrageparameter.

-   **Name**: **param2**
-   **Beschreibung**: **Ein weiterer Beispielparameter, nicht als erforderlich konfiguriert.**
-   **Geben Sie Folgendes ein**: **Zahl**

Sie sollten nach Möglichkeit Beispielantworten für alle Statuscodes angeben, die die Operation zurückgeben kann. Jeder Statuscode kann mehr als einen Antworttext existieren, nämlich einen pro unterstütztem Inhaltstyp. In diesem Lernprogramm fügen wir einen Antwortcode **200 OK** hinzu.

Klicken Sie auf **Hinzufügen** im Bereich Antworten, geben Sie **200** in das Textfeld ein und wählen Sie **200 OK** in der Dropdownliste aus.

![Antwort hinzufügen][]

Wenn Sie **200 OK** auswählen, wird ein neuer Antwortcode zur Operation hinzugefügt, und das Antwortfenster wird angezeigt. Geben Sie **Antwort in jedem Fall.** in das Textfeld **Beschreibung** ein.

![Antwort hinzufügen][3]

> **Darstellung hinzufügen** wird verwendet, um Antworten in unterschiedlichen Darstellungen hinzuzufügen. Weitere Informationen hierzu finden Sie unter [Antworten][].

Klicken Sie auf **Speichern**, um die neu konfigurierte Operation zur API hinzuzufügen.

## <a name="add-api-to-product"> </a>Hinzufügen der API zu einem Produkt

Entwickler müssen ein Produkt zunächst abonnieren, um API-Aufrufe ausführen zu können. Ein Produkt bietet Zugriff auf eine oder mehrere APIs und kann Zugriffseinschränkungen wie z. B. Nutzungskontingente und Durchsatzgrenzen enthalten. In diesem Schritt des Lernprogramms werden Sie die My Echo API zu einem existierenden Produkt hinzufügen.

Klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite, um die verfügbaren Produkte in dieser API-Instanz anzuzeigen und zu konfigurieren.

![Produkte][]

Standardmäßig enthält jede API-Verwaltungsinstanz zwei Beispielprodukte:

-   **Starter**
-   **Unbegrenzt**

In diesem Lernprogramm verwenden wir das Produkt **Starter**. Klicken Sie auf **Starter**, um die Einstellungen anzuzeigen, inklusive der APIs, die diesem Produkt zugeordnet sind.

![API hinzufügen][]

Klicken Sie auf **API zu Produkt hinzufügen**.

![API hinzufügen][4]

Markieren Sie das Kontrollkästchen für **My Echo API** und klicken Sie auf **Speichern**.

![Hinzugefügte API][]

Die **My Echo API** ist nun zu einem Produkt zugeordnet, und Entwickler können das Produkt abonnieren und die API verwenden.

> Dieses Lernprogramm verwendet das Starter-Produkt, das vorkonfiguriert und einsatzbereit ist. Eine schrittweise Anleitung zur Erstellung und Veröffentlichung neuer Produkte finden Sie unter [Erstellen und Veröffentlichen von Produkten][].

## <a name="subscribe"> </a>Abonnieren des Produkts, das die API enthält

Um API-Aufrufe ausführen zu können, müssen Entwickler zunächst das Produkt abonnieren, das Zugriff auf die API bietet. Entwickler können die Produkte im Entwicklerportal abonnieren. Administratoren können Entwickler in der Verwaltungskonsole für Produkte abonnieren. Sie sind standardmäßig als Administrator konfiguriert. da Sie die API-Verwaltungsinstanz in den vorherigen Schritten des Lernprogramms erstellt haben. Daher werden Sie ein Konto für das **Starter**-Produkt abonnieren.

Klicken Sie auf **Entwickler** im Menü **API-Verwaltung** auf der linken Seite und konfigurieren Sie die Entwickler für diese Dienstinstanz.

![Entwickler][]

Klicken Sie auf den Namen des Entwicklers, um die Einstellungen für den jeweiligen Benutzer zu konfigurieren, inklusive Abonnements.

> In diesem Beispiel abonnieren wir einen Entwickler mit dem Namen Clayton Gragg. Falls Sie kein Entwicklerkonto erstellt haben, können Sie das Administratorkonto abonnieren. Weitere Informationen zum Erstellen von Entwicklerkonten finden Sie unter [Verwalten von Entwicklerkonten in der Azure API-Verwaltung][].

![Abonnement hinzufügen][]

Klicken Sie auf **Abonnement hinzufügen**.

![Abonnement hinzufügen][5]

Markieren Sie das Kontrollkästchen für **Starter** und klicken Sie auf **Abonnieren**.

![Abonnement hinzugefügt][]

Wenn Sie Ihr Entwicklerkonto abonniert haben, können Sie die APIs des Produkts aufrufen.

## <a name="call-operation"> </a>Aufrufen einer Operation aus dem Entwicklerportal

Operationen können direkt aus dem Entwicklerportal aufgerufen werden. Dies ist ein einfacher Weg, um die Operationen einer API anzuzeigen und zu testen. In diesem Schritt des Lernprogramms werden Sie die Get-Methode aufrufen, die Sie zur **My Echo API** hinzugefügt haben. Klicken Sie auf **Entwicklerportal** im Menü oben rechts im Verwaltungsportal.

![Entwicklerportal][]

Klicken Sie im obersten Menü auf **APIs** und anschließend auf **My Echo API**, um die verfügbaren Operationen anzuzeigen.

![Entwicklerportal][6]

Die Beschreibung und Parameter, die Sie bei der Erstellung der Operation eingegeben haben, werden hier angezeigt und bieten Entwicklern eine Dokumentation für die Ausführung dieser Operation.

Klicken Sie auf **GET Resource** und anschließend auf **Konsole öffnen**.

![Operationskonsole][]

Geben Sie beliebige Werte für die Parameter sowie Ihren Entwicklerschlüssel ein und klicken Sie auf **HTTP Get**.

![HTTP Get][]

Nach dem Aufruf der Operation zeigt das Entwicklerportal die **Angeforderte URL** vom Back-End-Dienst, den **Antwortstatus**, die **Antwortheader** sowie den **Antwortinhalt** an.

![Antwort][]

## <a name="view-analytics"> </a>Anzeigen von Analysen

Um Analysen für die **My Echo API** anzuzeigen, wechseln Sie zurück zum Verwaltungsportal, indem Sie im Benutzermenü oben rechts im Entwicklerportal auf **Verwalten** klicken.

![Verwalten][]

Die Standardansicht für das Verwaltungsportal ist das Dashboard, das eine Übersicht für Ihre API-Verwaltungsinstanz enthält.

![Dashboard][]

Bewegen Sie den Mauszeige über die Grafik für die My Echo API, um spezielle Metriken für die Nutzung der API über einen bestimmten Zeitraum anzuzeigen.

> Falls Ihr Diagramm keine Linien enthält, wechseln Sie zurück zum Entwicklerportal und führen Sie einige Aufrufe an die API aus, warten Sie einen Moment und kehren Sie zum Dashboard zurück.

![Analyse][]

Klicken Sie auf **Details anzeigen**, um die Zusammenfassungsseite für die API anzuzeigen, inklusive einer größeren Version der angezeigten Metriken.

![Zusammenfassung][]

Um detaillierte Metriken und Berichte anzuzeigen, klicken Sie auf **Analyse** im Menü **API-Verwaltung** auf der linken Seite.

![Übersicht][]

Der **Analyse**-Bereich enthält die folgenden vier Registerkarten.

-   **Auf einen Blick** enthält Metriken für Nutzung und Integrität sowie eine Liste der aktivsten Entwickler, Produkte, APIs und Operationen.
-   **Nutzung** enthält detaillierte Ansichten für API-Aufrufe und Bandbreitennutzung inklusive einer geografischen Darstellung.
-   **Integrität** konzentriert sich auf Statuscodes, Cache-Treffer und Antwortzeiten für APIs und Dienste.
-   **Aktivität** enthält Berichte mit detaillierten Informationen zur Aktivität pro Entwickler, Produkt, API und Operation.

## <a name="next-steps"> </a>Nächste Schritte

-   Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][].

  [Erstellen einer API-Verwaltungsinstanz]: #create-service-instance
  [Erstellen einer API]: #create-api
  [Hinzufügen einer Operation]: #add-operation
  [Hinzufügen der API zu einem Produkt]: #add-api-to-product
  [Abonnieren des Produkts, das die API enthält]: #subscribe
  [Aufrufen einer Operation aus dem Entwicklerportal]: #call-operation
  [Anzeigen von Analysen]: #view-analytics
  [Nächste Schritte]: #next-steps
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=api_management_hero_a
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
  [Antwort hinzufügen]: ./media/api-management-get-started/api-management-add-response.png
  [3]: ./media/api-management-get-started/api-management-add-response-window.png
  [Antworten]: ../api-management-howto-add-operations/#responses
  [Produkte]: ./media/api-management-get-started/api-management-list-products.png
  [API hinzufügen]: ./media/api-management-get-started/api-management-add-api-to-product.png
  [4]: ./media/api-management-get-started/api-management-add-myechoapi-to-product.png
  [Hinzugefügte API]: ./media/api-management-get-started/api-management-api-added-to-product.png
  [Erstellen und Veröffentlichen von Produkten]: ../api-management-howto-add-products
  [Entwickler]: ./media/api-management-get-started/api-management-developers.png
  [Verwalten von Entwicklerkonten in der Azure API-Verwaltung]: ../api-management-howto-create-or-invite-developers/
  [Abonnement hinzufügen]: ./media/api-management-get-started/api-management-add-subscription.png
  [5]: ./media/api-management-get-started/api-management-add-subscription-window.png
  [Abonnement hinzugefügt]: ./media/api-management-get-started/api-management-subscription-added.png
  [Entwicklerportal]: ./media/api-management-get-started/api-management-developer-portal-menu.png
  [6]: ./media/api-management-get-started/api-management-developer-portal-myecho-api.png
  [Operationskonsole]: ./media/api-management-get-started/api-management-developer-portal-myecho-api-console.png
  [HTTP Get]: ./media/api-management-get-started/api-management-invoke-get.png
  [Antwort]: ./media/api-management-get-started/api-management-invoke-get-response.png
  [Verwalten]: ./media/api-management-get-started/api-management-manage-menu.png
  [Dashboard]: ./media/api-management-get-started/api-management-dashboard.png
  [Analyse]: ./media/api-management-get-started/api-management-mouse-over.png
  [Zusammenfassung]: ./media/api-management-get-started/api-management-api-summary-metrics.png
  [Übersicht]: ./media/api-management-get-started/api-management-analytics-overview.png
  [Erste Schritte bei der erweiterten API-Konfiguration]: ../api-management-get-started-advanced
