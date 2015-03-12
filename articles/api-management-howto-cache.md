<properties 
	pageTitle="Caching von Operationsergebnissen in der Azure API-Verwaltung" 
	description="Erfahren Sie, wie Sie Latenz, Bandbreitennutzung und Webdienstauslastung für API Management-Dienstaufrufe verbessern." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

# Caching von Operationsergebnissen in der Azure API-Verwaltung

Operationen in der API-Verwaltung (Vorschau) können für Antwort-Caching konfiguriert werden. Das Caching von Antworten kann API-Latenz, Bandbreitennutzung und Webdienst-Last für Daten, die sich eher selten ändern, drastisch senken.

In diesem Lernprogramm werden Sie die Cachingeinstellungen und -Richtlinien für die Operationen der Echo-Beispiel-API prüfen und die Operation im Entwicklerportal aufrufen, um das Caching in Aktion zu erleben.

## In diesem Thema

-   [Konfigurieren einer Operation für Caching][Konfigurieren einer Operation für Caching]
-   [Prüfen der Caching-Richtlinien][Prüfen der Caching-Richtlinien]
-   [Aufrufen einer Operation und Testen der Cachingfunktion][Aufrufen einer Operation und Testen der Cachingfunktion]
-   [Nächste Schritte][Nächste Schritte]

## <a name="configure-caching"> </a>Konfigurieren einer Operation für Caching

In diesem Schritt prüfen Sie die Cachingeinstellungen für die **GET Resource (cached)**-Operation der Echo-Beispiel-API.

> Jede API-Verwaltungs-Dienstinstanz enthält eine vorkonfigurierte Echo-API, die Sie für Tests und erste Schritte mit der API-Verwaltung nutzen können. Weitere Informationen finden Sie unter [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

Klicken Sie zunächst in der **Verwaltungskonsole** im Azure-Portal auf Ihren API-Verwaltungsdienst. Daraufhin gelangen Sie zum Verwaltungsportal für die API-Verwaltung.

![API-Verwaltungskonsole][API-Verwaltungskonsole]

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

Klicken Sie auf **APIs** im Menü **API-Verwaltung** auf der linken Seite, und klicken Sie anschließend auf **Echo API**.

![Echo API][Echo API]

Wählen Sie die Registerkarte **Operationen** aus und klicken Sie auf die Operation **GET Resource (cached)** in der Liste der **Operationen**.

![Operationen der Echo API][Operationen der Echo API]

Wählen Sie die Registerkarte **Caching** aus, um die Cachingeinstellungen für diese Operation anzuzeigen.

![Registerkarte Caching][Registerkarte Caching]

Markieren Sie das Kontrollkästchen **Aktivieren**, um Caching für eine Operation zu aktivieren. In diesem Beispiel ist die Cachingfunktion aktiviert.

Jedes Operationsergebnis erhält einen Schlüssel anhand der Felder, die in **Nach Abfrageparametern variieren** und **Nach Headern variieren** ausgewählt wurden. Falls Sie mehrere Antworten anhand von Abfrageparametern oder Headern zwischenspeichern möchten, können Sie dies in diesen beiden Feldern konfigurieren.

**Dauer** gibt das Ablaufintervall der gespeicherten Antworten an. In diesem Beispiel ist das Intervall **3600** Sekunden, was einer Stunde entspricht.

Mit der Cachingkonfiguration in diesem Beispiel liefert die erste Anforderung an die **GET Resource (cached)**-Operation eine Antwort vom Back-End-Dienst zurück. Diese Antwort wird zwischengespeichert und erhält einen Schlüssel anhand der angegebenen Header und Abfrageparameter. Bei nachfolgenden Aufrufen der Operation mit denselben Parametern wird die zwischengespeicherte Antwort zurückgegeben, bis das Ablaufintervall abgelaufen ist.

## <a name="caching-policies"> </a>Prüfen der Caching-Richtlinien

Wenn Sie die Cachingeinstellungen für eine Operation auf der Registerkarte **Caching** konfigurieren, werden Cachingrichtlinien für die Operation hinzugefügt. Sie können diese Richtlinien im Richtlinieneditor anzeigen und bearbeiten.

Klicken Sie auf **Richtlinien** im Menü **API-Verwaltung** auf der linken Seite und wählen Sie **Echo API / GET Resource (cached)** in der Dropdownliste **Operation** aus.

![Richtlinien-Geltungsbereich Operation][Richtlinien-Geltungsbereich Operation]

Daraufhin werden die Richtlinien für diese Operation im Richtlinieneditor angezeigt.

![Richtlinieneditor für die API-Verwaltung][Richtlinieneditor für die API-Verwaltung]

Die Richtliniendefinition für diese Operation enthält auch die Richtlinien für die Cachingkonfiguration, die Sie im vorigen Schritt auf der Registerkarte **Caching** geprüft haben.

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>
            <rewrite-uri template="/resource" />
        </inbound>
        <outbound>
            <base />
            <cache-store caching-mode="cache-on" duration="3600" />
        </outbound>
    </policies>

> Änderungen, die Sie im Richtlinieneditor vornehmen, werden auf der Registerkarte **Caching** der Operation übernommen, und umgekehrt.

## <a name="test-operation"> </a>Aufrufen einer Operation und Testen der Cachingfunktion

Um die Cachingfunktion in Aktion zu sehen, können Sie die Operation im Entwicklerportal aufrufen. Klicken Sie im Menü oben rechts auf **Entwicklerportal**.

![Entwicklerportal][Entwicklerportal]

Klicken Sie auf **APIs** im Hauptmenü und wählen Sie **Echo API** aus.

![Echo API][1]

> Falls nur eine API konfiguriert oder für Ihr Konto sichtbar ist, können Sie auf APIs klicken, um direkt zu den Operationen für diese API zu gelangen.

Wählen Sie die Operation **GET Resource (cached)** aus und klicken Sie auf **Konsole öffnen**.

![Konsole öffnen][Konsole öffnen]

Mit der Konsole können Sie Operationen direkt aus dem Entwicklerportal heraus aufrufen.

![Konsole][Konsole]

Übernehmen Sie die Standardwerte für **param1** und **param2**.

Wählen Sie den gewünschten Schlüssel in der Dropdownliste **Abonnement-Schlüssel** aus. Wenn das Konto nur über ein Abonnement verfügt, wird dieses automatisch ausgewählt.

Geben Sie **sampleheader:value1** in das Textfeld **Request headers** ein.

Klicken Sie auf **HTTP Get** und notieren Sie sich die Antwortheader.

Geben Sie **sampleheader:value2** in das Textfeld **Request headers** ein und klicken Sie auf **HTTP Get**.

**sampleheader** hat in der Antwort immer noch den Wert **value1**. Probieren Sie unterschiedliche Werte aus und beachten Sie, dass immer der zwischengespeicherte Wert aus dem ersten Aufruf zurückgegeben wird.

Geben Sie **25** in das Feld **param2** ein und klicken Sie auf **HTTP Get**.

**sampleheader** hat in der Antwort nun den Wert **value2**. Die zuvor zwischengespeicherte Antwort wurde nicht zurückgegeben, da die Operationsergebnisse einen Schlüssel anhand der Abfragezeichenfolge erhalten.

## <a name="next-steps"> </a>Nächste Schritte

-   Lesen Sie die anderen Themen im Lernprogramm [Erste Schritte bei der erweiterten API-Konfiguration][Erste Schritte bei der erweiterten API-Konfiguration].
-   Weitere Informationen zu Caching-Richtlinien finden Sie unter [Caching-Richtlinien][Caching-Richtlinien] in der [Richtlinienreferenz für die API-Verwaltung][Richtlinienreferenz für die API-Verwaltung].

  [Konfigurieren einer Operation für Caching]: #configure-caching
  [Prüfen der Caching-Richtlinien]: #caching-policies
  [Aufrufen einer Operation und Testen der Cachingfunktion]: #test-operation
  [Nächste Schritte]: #next-steps
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [API-Verwaltungskonsole]: ./media/api-management-howto-cache/api-management-management-console.png
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Echo API]: ./media/api-management-howto-cache/api-management-echo-api.png
  [Operationen der Echo API]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
  [Registerkarte Caching]: ./media/api-management-howto-cache/api-management-caching-tab.png
  [Richtlinien-Geltungsbereich Operation]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
  [Richtlinieneditor für die API-Verwaltung]: ./media/api-management-howto-cache/api-management-policy-editor.png
  [Entwicklerportal]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
  [1]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
  [Konsole öffnen]: ./media/api-management-howto-cache/api-management-open-console.png
  [Konsole]: ./media/api-management-howto-cache/api-management-console.png
  [Erste Schritte bei der erweiterten API-Konfiguration]: ../api-management-get-started-advanced
  [Caching-Richtlinien]: ../api-management-policy-reference/#caching-policies
  [Richtlinienreferenz für die API-Verwaltung]: ../api-management-policy-reference

<!--HONumber=46--> 
