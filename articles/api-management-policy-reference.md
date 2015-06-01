<properties 
	pageTitle="Richtlinienreferenz für die Azure API-Verwaltung" 
	description="Erfahren Sie mehr über die verfügbaren Richtlinien zum Konfigurieren von API Management." 
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
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Richtlinienreferenz für die Azure API-Verwaltung

Dieses Thema enthält die Referenz für die folgenden Richtlinien der API-Verwaltung (Vorschau). Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien für die API-Verwaltung][Richtlinien für die API-Verwaltung].

-   [Richtlinien für die Zugriffsbeschränkung][Richtlinien für die Zugriffsbeschränkung]

    -   [Nutzungskontingent][Nutzungskontingent] - Ermöglicht die Einrichtung eines erneuerbaren oder auf Lebenszeit gültigen Kontingents für Aufrufe und / oder Bandbreite.
    -   [Durchsatzgrenze][Durchsatzgrenze] - Begrenzt Aufrufe und/oder Bandbreitennutzung, um API-Lastspitzen zu verhindern.
    -   [Beschränkung für Aufrufer-IP][Beschränkung für Aufrufer-IP] - Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.
-   [Richtlinien für Inhaltstransformationen][Richtlinien für Inhaltstransformationen]

    -   [HTTP-Header setzen][HTTP-Header setzen] - Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.
    -   [XML zu JSON konvertieren][XML zu JSON konvertieren] - Konvertiert den Antwort- oder Anforderungstext von XML zu „JSON“ oder der „XML faithful“-Form von JSON.
    -   [Zeichenfolge in Text ersetzen][Zeichenfolge in Text ersetzen] - Sucht nach einer Zeichenfolge in Antwort oder Anforderung und ersetzt diese durch eine andere Zeichenfolge.
    -   [Abfrageparameter setzen][Abfrageparameter setzen] - Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.
-   [Cachingrichtlinien][Cachingrichtlinien]

    -   [In Cache ablegen][In Cache ablegen] - Cache-Antwort gemäß der angegebenen Cachekonfiguration.
    -   [Aus Cache abrufen][Aus Cache abrufen] - Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück.
-   [Sonstige Richtlinien][Sonstige Richtlinien]

    -   [URI umschreiben][URI umschreiben] - Konvertiert eine Anfrage-URL von deren öffentlichen Form in die vom Webdienst erwartete Form.
    -   [URLs in Inhalt maskieren][URLs in Inhalt maskieren] - Schreibt Links im Antworttext und im Standortheader um (maskiert), sodass diese über den Proxy auf den äquivalenten Link zeigen.
    -   [Domänenübergreifende Aufrufe zulassen][Domänenübergreifende Aufrufe zulassen] - Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.
    -   [JSONP][JSONP] - Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.
    -   [CORS][CORS] - Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.

## <a name="access-restriction-policies"> </a>Richtlinien für die Zugriffsbeschränkung

-   [Nutzungskontingent][Nutzungskontingent] - Ermöglicht die Einrichtung eines erneuerbaren oder auf Lebenszeit gültigen Kontingents für Aufrufe und / oder Bandbreite.
-   [Durchsatzgrenze][Durchsatzgrenze] - Begrenzt Aufrufe und/oder Bandbreitennutzung, um API-Lastspitzen zu verhindern.
-   [Beschränkung für Aufrufer-IP][Beschränkung für Aufrufer-IP] - Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.

### <a name="usage-quota"> </a>Nutzungskontingent

**Beschreibung:**
Einrichtung eines erneuerbaren oder auf Lebenszeit gültigen Kontingents für Aufrufe und / oder Bandbreite.

**Richtlinienanweisung:**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**Beispiel:**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Anwendungsfälle:**
Für eingehende Aufrufe im Produkt-Geltungsbereich.

**Anwendungsfälle:**
Wenn die Nutzung von Operationen und/oder APIs für ein Produkt eingeschränkt werden soll.

**Pros und Kontras:**
Zum Definieren von Nutzungskontingenten für ein Produkt basierend auf Zeit und Bandbreite. Wenn eine API das definierte Kontingent erreicht hat, werden alle nachfolgenden Aufrufe mit einer Fehlermeldung abgelehnt. Kontingente werden normalerweise über die Anzahl von Anforderungsnachrichten definiert, die über ein bestimmtes Zeitintervall und Bandbreitenlimit erlaubt sind.

| Attribut                  | Beschreibung                                                                                                                              |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| quota calls="Anzahl"      | Die maximale Anzahl erlaubter Abonnementaufrufe pro Erneuerungsperiode (z. B. 10000)                                                      |
| bandwidth="Kilobyte"      | Die maximale Anzahl von Kilobyte, die dieses Produkt bzw. diese API oder Operation pro Erneuerungsperiode verbrauchen darf (z. B. 40000). |
| renewal-period="Sekunden" | Die Erneuerungsperiode in Sekunden, während derer das Kontingent gilt (z. B. 3600).                                                       |
| api name="Name"           | Der Name des API-Aufrufs, für den das Kontingent gilt.                                                                                    |
| calls="Anzahl"            | Die maximale Anzahl erlaubter Aufrufe für diese API oder Operation pro Erneuerungsperiode (z. B. 5000).                                   |
| operation name="Name"     | Der Name der Operation, für die das Kontingent gilt.                                                                                      |

### <a name="rate-limit"> </a>Durchsatzgrenze

**Beschreibung:**
Verhindert API-Lastspitzen, indem die Rate der Anforderungen an eine API oder optional auch an eine bestimmte API-Operation begrenzt wird. Wenn diese Richtlinie ausgelöst wird, erhält der Client den Antwortcode `429 Too Many Requests`.

**Richtlinienanweisung:**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**Beispiel:**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**Anwendungsfälle:**
Für eingehende Aufrufe im Produkt-Geltungsbereich.

| Element/Attribut          | Beschreibung                                                          |
|---------------------------|-----------------------------------------------------------------------|
| calls="Anzahl"            | Erlaubte Anzahl Aufrufe pro Erneuerungsperiode                        |
| renewal-period="Sekunden" | Zeitintervall, nach dem das Durchsatzgrenzen-Kontingent erneuert wird |
| api name="Name"           | Name der API, für die die Durchsatzgrenze gilt                        |
| operation name="Name"     | Name der Operation, für die die Durchsatzgrenze gilt                  |

### <a name="caller-ip-restriction"> </a>Beschränkung für Aufrufer-IP

**Beschreibung:**
Filtert (erlaubt/blockiert) Aufrufe von bestimmten IP-Adressen und/oder Adressbereichen.

**Richtlinienanweisung:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Beispiel:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Anwendungsfälle:**
Für eingehende Aufrufe in beliebigen Geltungsbereichen.

**Anwendungsfälle:**
Verwenden Sie diese Richtlinie, wenn Sie genau steuern möchten, wer auf Ihren Dienst zugreifen darf.

**Pros und Kontras:**
Verwenden Sie diese Richtlinie nur, wenn Sie eine besonders strenge Zugriffskontrolle (z. B. für Dienste mit hohen Sicherheitsanforderungen) pro Host oder Hostbereich benötigen.

| Attribut                                  | Beschreibung                                                                               |
|-------------------------------------------|--------------------------------------------------------------------------------------------|
| ip-filter action="allow | forbid"         | Gibt an, ob Aufrufe für die angegebene(n) Adresse(n) erlaubt oder blockiert werden sollen. |
| address="Adresse"                         | Eine oder mehrere IP-Adressen, für die diese Richtlinie gelten soll.                       |
| address-range from="Adresse" to="Adresse" | Ein IP-Adressbereich, für den diese Richtlinie gelten soll.                                |

## <a name="content-transformation-policies"> </a>Richtlinien für Inhaltstransformationen

-   [HTTP-Header setzen][HTTP-Header setzen] - Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.
-   [XML zu JSON konvertieren][XML zu JSON konvertieren] - Konvertiert den Antwort- oder Anforderungstext von XML zu „JSON“ oder der „XML faithful“-Form von JSON.
-   [Zeichenfolge in Text ersetzen][Zeichenfolge in Text ersetzen] - Sucht nach einer Zeichenfolge in Antwort oder Anforderung und ersetzt diese durch eine andere Zeichenfolge.
-   [Abfrageparameter setzen][Abfrageparameter setzen] - Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.

### <a name="set-http-header"> </a>HTTP-Header setzen

**Beschreibung:**
Weist einem vorhandenen Antwort- und/oder Anforderungsheader einen Wert zu oder fügt einen neuen Antwort- und/oder Anforderungsheader hinzu.

Fügt eine Liste von HTTP-Headern in eine HTTP-Nachricht ein. In eingehenden Pipelines setzt diese Richtlinie die HTTP-Header für die Anforderung, die an den Zieldienst übergeben wird. In ausgehenden Pipelines setzt diese Richtlinie die HTTP-Header für die Antwort, die an den Client des Proxys gesendet wird.

**Richtlinienanweisung:**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**Beispiel:**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**Anwendungsfälle:**
Für eingehende und ausgehende Aufrufe in beliebigen Geltungsbereichen mit Ausnahme von *Herausgeber*.

**Anwendungsfälle:**
Mit dieser Richtlinie können Sie die Operationsparameter und/oder Rückgabewerte einer HTTP-Transaktion angeben.

**Pros und Kontras:**
Die meisten HTTP-Anforderungen und viele Antworten benötigen Header für die Angabe von Ein- und Ausgabeparametern. Daher lässt sich diese Richtlinie vermutlich für beinahe all Ihre Dienste einsetzen.

| Attribut                                 | Beschreibung                                                                                                                                                                                                                                                  |
|------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| reconcile-action="override|skip|append"  | Gibt die auszuführende Aktion an, wenn ein Header bereits existiert. Hinweis: Mit „override“ werden mehrere Einträge mit demselben Namen gemäß aller Einträge gesetzt (die mehrfach aufgeführt sind); nur die aufgelisteten Werte werden im Ergebnis gesetzt. |
| header name="Headername"                 | Der Name des zu setzenden Headers. Erforderlich.                                                                                                                                                                                                              |
| exists-action="override | skip | append" | Gibt die auszuführende Aktion an, wenn ein Header bereits existiert                                                                                                                                                                                           |
| value="Wert"                             | Der Wert für den zu setzenden Header.                                                                                                                                                                                                                         |

### <a name="convert-xml-to-json"> </a>XML zu JSON konvertieren

**Beschreibung:**
Konvertiert den Anforderungs- oder Antworttext von XML zu JSON.

**Richtlinienanweisung:**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**Beispiel:**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**Anwendungsfälle:**
Für eingehende oder ausgehende Aufrufe im API- oder Operations-Geltungsbereich.

**Pros und Kontras:**
Zur Modernisierung von APIs, deren Back-End-Webdienste ausschließlich XML verwenden.

| Attribut                              | Beschreibung                                                                                                                                         |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| kind="javascript-friendly | direct    | Das konvertierte JSON hat ein gut lesbares Format für JavaScript-Entwickler | Das konvertierte JSON bildet die Struktur des XML-Ausgangsdokuments ab |
| apply= always | content-type-xml      | Immer konvertieren | Nur konvertieren, wenn der `Content-Type`-Header auf vorhandenes XML verweist                                                   |
| consider-accept-header="true | false" | Konvertierung auf Basis des `Accept`-Headers | Immer konvertieren                                                                                    |

### <a name="replace-string-in-body"> </a>Zeichenfolge in Text ersetzen

**Beschreibung:**
Sucht nach einer Zeichenfolge in Antwort oder Anforderung und ersetzt diese durch eine andere Zeichenfolge.

**Richtlinienanweisung:**

    <find-and-replace from="what to replace" to="replacement" />

**Beispiel:**

    <find-and-replace from="notebook" to="laptop" />

**Anwendungsfälle:**
Für eingehende und ausgehende Aufrufe in beliebigen Geltungsbereichen.

| Attribut                          | Beschreibung                                           |
|-----------------------------------|--------------------------------------------------------|
| from="zu ersetzende Zeichenfolge" | Die zu suchende Zeichenfolge.                          |
| to="Ersatz"                       | Die Ersatzzeichenfolge für die gefundene Zeichenfolge. |

### <a name="set-query-string-parameter"> </a>Abfrageparameter setzen

**Beschreibung:**
Fügt Abfrageparameter hinzu, löscht diese oder ersetzt deren Werte.

**Richtlinienanweisung:**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**Beispiel:**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Anwendungsfälle:**
Für eingehende Aufrufe in beliebigen Geltungsbereichen.

**Pros und Kontras:**
Mit dieser Richtlinie können Sie Abfrageparameter an den Back-End-Dienst übergeben, die optional sind oder in der Anforderung nie vorkommen.

| Element/Attribut         | Beschreibung                                                              |
|--------------------------|---------------------------------------------------------------------------|
| name="Name"              | Der Name des Parameters                                                   |
| exists-action="override" | Ersetzt den Wert des Parameters, wenn dieser in der Anforderung existiert |
| exists-action="skip"     | Führt keine Aktion aus, wenn der Parameter in der Anforderung existiert   |
| exists-action="append"   | Hängt den Wert an den vorhandenen Anforderungsparameter an                |
| exists-action="delete"   | Löscht den Parameter aus der Anforderung                                  |
| value="Wert"             | Setzt den Wert des Parameters im umschließenden Element                   |

## <a name="caching-policies"> </a>Cachingrichtlinien

-   [In Cache ablegen][In Cache ablegen] - Cache-Antwort gemäß der angegebenen Cachekonfiguration.
-   [Aus Cache abrufen][Aus Cache abrufen] - Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück.

### <a name="store-to-cache"> </a>In Cache ablegen

**Beschreibung:**
Cache-Antworten gemäß der angegebenen Cachekonfiguration. Setzt das Vorhandensein einer entsprechenden [Aus Cache abrufen][Aus Cache abrufen]-Richtlinie voraus.

**Richtlinienanweisung:**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**Beispiel:**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**Anwendungsfälle:**
Für ausgehende Aufrufe im API- und/oder Operations-Geltungsbereich.

**Anwendungsfälle:**
Verwenden Sie diese Richtlinie, wenn sich der Inhalt von Antworten über längere Zeit nicht ändert.

**Pros und Kontras:**
Das Caching von Antworten senkt Bandbreitennutzung und Prozessoranforderungen auf dem Back-End-Webserver und verbessert die Latenz der API-Consumer.

| Element/Attribut        | Beschreibung                                                                                                                                                     |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sekunden                | Lebensdauer der gespeicherten Einträge (in Sekunden, Standard=3600)                                                                                              |
| cache-on | do-not-cache | Antworten werden zwischengespeichert | Antworten werden nicht zwischengespeichert, und Cache-relevante Header werden gesetzt, um Downstream-Caching zu verbieten |

### <a name="get-from-cache"> </a>Aus Cache abrufen

**Beschreibung:**
Führt eine Cachesuche aus und gibt ggf. eine gültige Antwort aus dem Cache zurück. Antwortet auf Cache-Prüfungsanfragen von API-Consumern. Setzt das Vorhandensein einer entsprechenden [In Cache ablegen][In Cache ablegen]-Richtlinie voraus.

**Richtlinienanweisung:**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**Beispiel:**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**Anwendungsfälle:**
Für eingehende Aufrufe im API- und/oder Operations-Geltungsbereich.

**Anwendungsfälle:**
Verwenden Sie diese Richtlinie, wenn sich der Inhalt von Antworten über längere Zeit nicht ändert.

**Pros und Kontras:**
Das Caching von Antworten senkt Bandbreitennutzung und Prozessoranforderungen auf dem Back-End-Webserver und verbessert die Latenz der API-Consumer.

| Element/Attribut                                  | Beschreibung                                                                                                                                                                                                                                         |
|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vary-by-developer="true | false"                  | Für *true* werden Antworten pro Entwickler-Schlüssel zwischengespeichert; der Standardwert ist *false*                                                                                                                                           |
| vary-by-developer-groups="true | false"           | Für *true* werden Antworten pro Benutzerrolle zwischengespeichert; der Standardwert ist *false*                                                                                                                                                  |
| downstream-caching-type="none | private | public" | *none* - kein Downstream-Caching erlaubt; standardmäßig eingestellt | *private* - privates Downstream-Caching ist erlaubt | *public* - privates und gemeinsam genutztes Downstream-Caching ist erlaubt.                                        |
| vary-by-header: "Accept"                          | Antworten werden je nach Wert des `Accept`-Headers zwischengespeichert                                                                                                                                                                               |
| vary-by-header: Accept-Charset"                   | Antworten werden je nach Wert des `Accept-Charset`-Headers zwischengespeichert                                                                                                                                                                       |
| vary-by-header: "Headername"                      | Antworten werden je nach Wert des angegebenen Headers zwischengespeichert, z. B. `Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match`                                                     |
| vary-by-query-parameter: "Parametername"          | Antworten werden je nach Wert der angegebenen Abfrageparameter zwischengespeichert. Geben Sie einen oder mehrere Parameter an. Verwenden Sie Semikolons als Trennzeichen. Wenn kein Parameter angegeben ist, werden alle Abfrageparameter verwendet. |

## <a name="other-policies"> </a>Sonstige Richtlinien

-   [URI umschreiben][URI umschreiben] - Konvertiert eine Anfrage-URL von deren öffentlichen Form in die vom Webdienst erwartete Form.
-   [URLs in Inhalt maskieren][URLs in Inhalt maskieren] - Schreibt Links im Antworttext und im Standortheader um (maskiert), sodass diese über den Proxy auf den äquivalenten Link zeigen.
-   [Domänenübergreifende Aufrufe zulassen][Domänenübergreifende Aufrufe zulassen] - Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.
-   [JSONP][JSONP] - Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen.
-   [CORS][CORS] - Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.

### <a name="rewrite-uri"> </a>URI umschreiben

**Beschreibung:**
Konvertiert eine Anfrage-URL von deren öffentlichen Form in die vom Webdienst erwartete Form.

**Öffentliche URL:** <http://api.example.com/storenumber/ordernumber>

**Anforderungs-URL:** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>.

Fügen Sie keine zusätzlichen Vorlagen-Pfadparameter in die umgeschriebene URL ein. Sie können nur Abfrageparameter hinzufügen.

**Richtlinienanweisung:**

    <rewrite-uri template="uri template" />

**Beispiel:**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Anwendungsfälle:**
Für eingehende Aufrufe im Operations-Geltungsbereich.

**Anwendungsfälle:**
Verwenden Sie diese Richtlinie, um menschen- oder browserfreundliche URLs in das vom Webdienst erwartete Format umzuwandeln.

**Pros und Kontras:**
Verwenden Sie diese Richtlinie nur, wenn Sie ein alternatives URL-Format anbieten. Bereinigte URLs, RESTful-URLs, benutzerfreundliche URLs oder SEO-freundliche URLs sind rein strukturelle URLs, die keine Abfragezeichenfolge enthalten, sondern nur den Pfad der Ressource (nach Schema und Berechtigung). Dies geschieht oft aus Gründen der Ästhetik, Benutzerfreundlichkeit oder zur Suchmaschinenoptimierung (SEO).

| Attribut               | Beschreibung                                              |
|------------------------|-----------------------------------------------------------|
| template="URI-Vorlage" | Die eigentliche Webdienst-URL mit allen Abfrageparametern |

### <a name="mask-urls-in-content"> </a>URLs in Inhalt maskieren

**Beschreibung:**
Schreibt Links im Antworttext und im Standortheader um (maskiert), sodass diese über den Proxy auf den äquivalenten Link zeigen.

**Richtlinienanweisung:**

    <redirect-body-urls />

**Beispiel:**

    <redirect-body-urls />

**Anwendungsfälle:**
Im *API-* und *Operations-*Geltungsbereich. Sowohl für eingehende als auch für ausgehende Aufrufe.

### <a name="allow-cross-domain-calls"> </a>Domänenübergreifende Aufrufe zulassen

**Beschreibung:**
Erlaubt API-Aufrufe aus browserbasierten Clients, die Adobe Flash und Microsoft Silverlight verwenden.

**Richtlinienanweisung:**

    <cross-domain />

**Beispiel:**

    <cross-domain />

**Anwendungsfälle:**
Für eingehende Aufrufe im *Herausgeber*-Geltungsbereich.

### <a name="jsonp"> </a>JSONP

**Beschreibung:**
Fügt Unterstützung für JSON mit Padding (JSONP) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients mit JavaScript zu ermöglichen. JSONP wird in JavaScript-Programmen verwendet, um Daten von einem Server in einer anderen Domäne anzufordern. JSONP umgeht die Einschränkung der meisten Webbrowser, dass der Zugriff auf Webseiten nur innerhalb derselben Domäne möglich ist.

**Richtlinienanweisung:**

    <jsonp callback-parameter-name="callback function name" />

**Beispiel:**

    <jsonp callback-parameter-name="cb" />

Wenn Sie die Methode ohne den Rückrufparameter `?cb=XXX` aufrufen, wird reines JSON zurückgegeben (ohne einen Wrapper für den Funktionsaufruf).

Mit dem Rückrufparameter `?cb=XXX` wird ein JSONP-Ergebnis zurückgegeben, und das JSON-Ausgangsergebnis wird in der Form `XXX('<json result goes here>')` um die Rückruffunktion gesetzt;

**Anwendungsfälle:**
Für ausgehende Aufrufe.

**Anwendungsfälle:**
Um Daten von einem Server in einer anderen Domäne anzufordern.

| Attribut                | Beschreibung                                                                                                                        |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| callback-parameter-name | Der domänenübergreifende JavaScript-Funktionsaufruf mit dem vollqualifizierten Domänennamen, in dem die Funktion liegt, als Präfix. |

### <a name="cors"> </a>CORS

**Beschreibung:**
Fügt Unterstützung für Cross-Origin Resource Sharing (CORS) zu einer Operation oder einer API hinzu, um domänenübergreifende Aufrufe aus browserbasierten Clients zu ermöglichen.

Mit CORS können Browser und Server miteinander interagieren und ermitteln, ob bestimmte ursprungsübergreifende Anfragen (z. B. XMLHttpRequests-Aufrufe aus JavaScript in einer Webseite an andere Domänen) zulässig sind. Dies bietet mehr Flexibilität als wenn nur Anfragen gleichen Ursprungs erlaubt sind, und ist gleichzeitig sicherer als wenn alle ursprungsübergreifenden Anfragen erlaubt sind.

**Richtlinienanweisung:**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**Beispiel:**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**Anwendungsfälle:**
Für eingehende Aufrufe im *API-* oder *Operations-*Geltungsbereich.

| Attribut             | Beschreibung                                                                                                                         |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| <origin>*</origin>  | Alle ODER eine Liste bestimmter URIs erlauben                                                                                        |
| <origin>URI</origin> | Die URI muss Schema, Host und Port enthalten. Wenn kein Port angegeben ist, wird Port 80 für HTTP und Port 443 für HTTPS angenommen. |

  [Richtlinien für die API-Verwaltung]: ../api-management-howto-policies
  [Richtlinien für die Zugriffsbeschränkung]: #access-restriction-policies
  [Nutzungskontingent]: #usage-quota
  [Durchsatzgrenze]: #rate-limit
  [Beschränkung für Aufrufer-IP]: #caller-ip-restriction
  [Richtlinien für Inhaltstransformationen]: #content-transformation-policies
  [HTTP-Header setzen]: #set-http-header
  [XML zu JSON konvertieren]: #convert-xml-to-json
  [Zeichenfolge in Text ersetzen]: #replace-string-in-body
  [Abfrageparameter setzen]: #set-query-string-parameter
  [Cachingrichtlinien]: #caching-policies
  [In Cache ablegen]: #store-to-cache
  [Aus Cache abrufen]: #get-from-cache
  [Sonstige Richtlinien]: #other-policies
  [URI umschreiben]: #rewrite-uri
  [URLs in Inhalt maskieren]: #mask-urls-in-content
  [Domänenübergreifende Aufrufe zulassen]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors

<!--HONumber=46--> 
