<properties pageTitle="How to create and configure advanced product settings in Azure API Management" metaKeywords="" description="Learn how to configure a product with quota and rate limit policies." metaCanonical="" services="" documentationCenter="API Management" title="How to create and configure advanced product settings in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Erstellen und Konfigurieren erweiterter Produkteinstellungen in der Azure API-Verwaltung

Die Produkte in der Azure API-Verwaltung (Vorschau) sind hochkonfigurierbar, um die Anforderungen von API-Anbietern zu erfüllen. Dieses Thema beschreibt einige der erweiterten Produkteinstellungen, inklusive Durchsatzgrenzen und Kontingentrichtlinien.

In diesem Lernprogramm erstellen Sie ein kostenloses Testprodukt, das bis zu 10 Aufrufe pro Minute und maximal 200 Aufrufe pro Woche erlaubt, veröffentlichen das Produkt und testen die Richtlinie für die Durchsatzgrenze.

## In diesem Thema

-   [Erstellen eines Produkts][]
-   [Hinzufügen einer API zum Produkt][]
-   [Konfigurieren von Durchsatzgrenzen und Kontingenten][]
-   [Veröffentlichen des Produkts][]
-   [Abonnieren eines Entwicklerkontos für das Produkt][]
-   [Aufrufen einer Operation und Testen der Durchsatzgrenze][]
-   [Nächste Schritte][]

## <a name="create-product"> </a>Erstellen eines Produkts

In diesem Schritt erstellen Sie ein kostenloses Testprodukt, für das keine Abonnementgenehmigung erforderlich ist.

Klicken Sie zunächst in der **Verwaltungskonsole** im Azure-Portal auf Ihren API-Verwaltungsdienst. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

>Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][].

![API-Verwaltungskonsole][]

Klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite, um Seite **Produkte** anzuzeigen.

![Produkt hinzufügen][]

Klicken Sie auf **Produkt hinzufügen**, um das Popupfenster **Neues Produkt hinzufügen** anzuzeigen.

![Neues Produkt hinzufügen][]

Geben Sie **Kostenloser Test** in das Textfeld **Titel** ein.

Geben Sie **Abonnenten können bis zu 10 Aufrufe pro Minute und bis zu 200 Aufrufe pro Woche ausführen, danach wird der Zugriff verweigert.** in das Textfeld **Beschreibung** ein.

Wenn Sie möchten, dass ein Administrator Abonnements für dieses Produkt prüfen und ablehnen oder akzeptieren muss, markieren Sie die Option **Abonnementgenehmigung erforderlich**. Wenn dieses Feld nicht markiert ist, werden Abonnements automatisch genehmigt. In diesem Beispiel werden Abonnements automatisch genehmigt, markieren Sie dieses Kontrollkästchen also nicht.

Geben Sie alle Werte ein und klicken Sie auf **Speichern**, um das Produkt zu erstellen.

![Hinzugefügtes Produkt][]

Standardmäßig sind neue Produkte für Benutzer in der Gruppe **Administratoren** sichtbar. Wir werden die Gruppe **Entwickler** hinzufügen. Klicken Sie auf **Kostenloser Test** und wählen Sie die Registerkarte **Sichtbarkeit** aus.

> Die API-Verwaltung verwendet Gruppen, um die Sichtbarkeit von Produkten für Entwickler zu verwalten. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Gruppen in der Azure API-Verwaltung][].

![Hinzufügen der Gruppe "Entwickler"][]

Markieren Sie die Gruppe **Entwickler** und klicken Sie auf **Speichern**.

## <a name="add-api"> </a>Hinzufügen einer API zum Produkt

In diesem Schritt des Lernprogramms werden Sie die Echo API zu dem neu erstellten Produkt "Kostenloser Test" hinzufügen.

> Jede API-Verwaltungs-Dienstinstanz enthält eine vorkonfigurierte Echo-API, die Sie für Tests und erste Schritte mit der API-Verwaltung nutzen können. Weitere Informationen finden Sie unter [Erste Schritte mit der Azure API-Verwaltung][].

Klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite und klicken Sie auf **Kostenloser Test**, um das Produkt zu konfigurieren.

![Produkt konfigurieren][]

Klicken Sie auf **API zu Produkt hinzufügen**.

![API zum Produkt hinzufügen][]

Markieren Sie das Kontrollkästchen neben **Echo API** und klicken Sie auf **Speichern**.

![Echo API hinzufügen][]

## <a name="policies"> </a>Konfigurieren von Durchsatzgrenzen und Kontingenten

Durchsatzgrenzen und Kontingente werden im Richtlinien-Editor konfiguriert. Klicken Sie auf **Richtlinien** im Menü **API-Verwaltung** auf der linken Seite und klicken Sie auf **Kostenloser Test** in der Dropdownliste **Richtlinien-Produktbereich**.

![Produktrichtlinie][]

Klicken Sie auf **Richtlinie hinzufügen**, um die Richtlinienvorlage zu importieren und die Richtlinie für Durchsatzgrenzen und Kontingente zu erstellen.

![Richtlinie hinzufügen][]

Um Richtlinien hinzuzufügen, platzieren Sie den Cursor entweder im **Eingehend**- oder **Ausgehend**-Bereich der Richtlinienvorlage. Richtlinien für Durchsatzgrenzen und Kontingente sind eingehende Richtlinien. Platzieren Sie den Cursor also im Element für eingehende Richtlinien.

![Richtlinieneditor][]

In diesem Lernprogramm werden wir die Richtlinien **Aufrufbeschränkung** und **Nutzungskontingent** erstellen.

![Richtlinienanweisungen][]

Platzieren Sie den Cursor in das **Eingehend**-Richtlinienelement und klicken Sie auf den Pfeil neben **Aufrufbeschränkung**, um die Richtlinienvorlage einzufügen.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Aufrufbeschränkung** kann auf der Produktebene sowie auf der API- und auf der Namensebene für einzelne Operationen verwendet werden. Dieses Lernprogramm verwendet Richtlinien auf Produktebene. Löschen Sie also die Elemente **api** und **operation** aus dem Element **rate-limit**, wie im folgenden Beispiel gezeigt.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Das Produkt **Kostenloser Test** soll bis zu 10 Aufrufe pro Minute erlauben. Geben Sie also **10** als Wert für das calls-Attribut ein und **60** für das **renewal-period**-Attribut.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Um die **Nutzungskontingent**-Richtlinie zu konfigurieren, platzieren Sie Ihren Cursor direkt unterhalb des neu erstellten **rate-limit**-Elements innerhalb des **inbound**-Elements und klicken Sie auf den Pfeil links neben **Nutzungskontingent einstellen**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Da diese Richtlinie auf der Produktebene arbeiten soll, löschen Sie die Elemente mit den Namen **api** und **operation**, wie im folgenden Beispiel gezeigt.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Kontingente können basierend auf der Anzahl von Aufrufen pro Intervall, der Bandbreite oder beiden Werten konfiguriert werden. In diesem Lernprogramm drosseln wir nicht nach Bandbreite. Löschen Sie also das **bandwidth**-Attribut.

    <quota calls="number" renewal-period="seconds">
    </quota>

Für das Produkt **Kostenloser Test** gilt ein Kontingent von 200 Aufrufen pro Woche. Geben Sie **200** als Wert für das calls-Attribut ein, und **604800** als Wert für renewal-period.

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> Die Richtlinienintervalle werden in Sekunden angegeben. Um die Intervalle für eine Woche zu berechnen, multiplizieren Sie die Anzahl der Tage (7) mit der Anzahl der Stunden pro Tag (24), der Anzahl der Minuten pro Stunde (60) und der Anzahl von Sekunden pro Minute (60). 7 \* 24 \* 60 \* 60 = 604800.

Konfigurieren Sie die Richtlinie, sodass diese dem folgenden Beispiel entspricht.

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
        
    </inbound>
    <outbound>
        
        <base />
        
        </outbound>
    </policies>

Wenn Sie die gewünschten Richtlinien konfiguriert haben, klicken Sie auf **Speichern**.

![Richtlinie speichern][]

## <a name="publish-product"> </a>Veröffentlichen des Produkts

Sie haben die APIs hinzugefügt und die Richtlinien konfiguriert, und das Produkt kann jetzt von Entwicklern aufgerufen werden. Bevor die Entwickler das Produkt nutzen können, müssen Sie es veröffentlichen. Klicken Sie auf **Produkte** im Menü **API-Verwaltung** auf der linken Seite und klicken Sie auf **Kostenloser Test**, um das Produkt zu konfigurieren.

![Produkt konfigurieren][]

Klicken Sie auf **Veröffentlichen** und anschließend zur Bestätigung auf **Ja, veröffentlichen**.

![Veröffentlichen des Produkts][1]

## <a name="subscribe-account"> </a>Abonnieren eines Entwicklerkontos für das Produkt

Ihr Produkt ist nun veröffentlicht und kann von Entwicklern abonniert und verwendet werden.

> Administratoren einer API-Verwaltungs-Instanz werden automatisch für alle Produkte abonniert. In diesem Schritt des Lernprogramms werden wir das Produkt "Kostenloser Test" mit einem der nicht-Administratorkonten abonnieren. Falls Ihr Entwicklerkonto Teil der Administratorrolle ist, können Sie diesen Schritt dennoch mitverfolgen, auch wenn Sie das Produkt bereits abonniert haben.

Klicken Sie auf **Entwickler** im Menü **API-Verwaltung** auf der linken Seite und klicken Sie den Namen Ihres Entwicklerkontos. In diesem Beispiel verwenden wir das Entwicklerkonto **Clayton Gragg**.

![Entwickler konfigurieren][]

Klicken Sie auf **Abonnement hinzufügen**.

![Abonnement hinzufügen][]

Markieren Sie das Kontrollkästchen neben **Kostenloser Test** und klicken Sie auf **Abonnieren**.

![Abonnement hinzufügen][2]

## <a name="test-rate-limit"> </a>Aufrufen einer Operation und Testen der Durchsatzgrenze

Sie haben das Produkt "Kostenloser Test" nun konfiguriert und veröffentlicht und können Operationen aufrufen, um die Richtlinie für die Durchsatzgrenze zu testen. Klicken Sie im Menü oben rechts auf **Entwicklerportal**, um zum Entwicklerportal zu gelangen.

![Entwicklerportal][]

Klicken Sie auf **APIs** im Hauptmenü und wählen Sie **Echo API** aus.

![Entwicklerportal][3]

> Falls nur eine API konfiguriert oder für Ihr Konto sichtbar ist, können Sie auf APIs klicken, um direkt zu den Operationen für diese API zu gelangen.

Wählen Sie die Operation **GET Resource** aus und klicken Sie auf **Konsole öffnen**.

![Konsole öffnen][]

Behalten Sie die Standard-Parameterwerte bei und wählen Sie Ihren Abonnementschlüssel für das Produkt **Kostenloser Test** aus.

![Abonnementschlüssel][]

> Falls Sie mehrere Abonnements haben, stellen Sie sicher, dass Sie den Schlüssel für **Kostenloser Test** auswählen. Andernfalls treten die in den vorherigen Schritten konfigurierten Richtlinien nicht in Kraft.

Klicken Sie auf **HTTP Get** und sehen Sie sich die Antwort an. Beachten Sie den **Antwortstatus** von **200 OK**.

![Operationsergebnis][]

Klicken Sie häufiger als das konfigurierte Limit von 10 mal pro Minute auf **HTTP Get**. Sobald die Durchsatzgrenze überschritten ist, wird der Antwortstatus **429 Too many Requests** zurückgegeben.

![Operationsergebnis][4]

Die **Antwortheader** und der **Antworttext** enthalten das verbleibende Intervall, bis neue Aufrufe möglich sind.

Wenn die Durchsatzgrenze von 10 Aufrufen pro Minute aktiv ist, werden nachfolgende Aufrufe fehlschlagen, bis 60 Sekunden nach dem ersten der 10 erfolgreichen Aufrufe an das Produkt vergangen sind, bevor die Durchsatzgrenze überschritten wurde. In diesem Beispiel beträgt das verbleibende Intervall 43 Sekunden.

## <a name="next-steps"> </a>Nächste Schritte

-   Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][].

  [Erstellen eines Produkts]: #create-product
  [Hinzufügen einer API zum Produkt]: #add-api
  [Konfigurieren von Durchsatzgrenzen und Kontingenten]: #policies
  [Veröffentlichen des Produkts]: #publish-product
  [Abonnieren eines Entwicklerkontos für das Produkt]: #subscribe-account
  [Aufrufen einer Operation und Testen der Durchsatzgrenze]: #test-rate-limit
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [API-Verwaltungskonsole]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Produkt hinzufügen]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Neues Produkt hinzufügen]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Hinzugefügtes Produkt]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [Erstellen und Verwenden von Gruppen in der Azure API-Verwaltung]: ../api-management-howto-create-groups
  [Hinzufügen der Gruppe "Entwickler"]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Produkt konfigurieren]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [API zum Produkt hinzufügen]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Echo API hinzufügen]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Produktrichtlinie]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Richtlinie hinzufügen]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Richtlinieneditor]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Richtlinienanweisungen]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Richtlinie speichern]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Entwickler konfigurieren]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Abonnement hinzufügen]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [Entwicklerportal]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Konsole öffnen]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Abonnementschlüssel]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Operationsergebnis]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [4]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [Erste Schritte bei der erweiterten API-Konfiguration]: ../api-management-get-started-advanced
