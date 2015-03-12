<properties 
	pageTitle="Hinzufügen von Operationen zu einer API in der Azure API-Verwaltung" 
	description="Erfahren Sie, wie Sie Operationen zu einer API in Azure API Management hinzufügen." 
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

# Hinzufügen von Operationen zu einer API in der Azure API-Verwaltung

Bevor eine API in der API-Verwaltung (Vorschau) verwendet werden kann, müssen Operationen hinzugefügt werden. Diese Anleitung beschreibt, wie Sie unterschiedliche Typen von Operationen zu einer API in der API-Verwaltung hinzufügen können.

## In diesem Thema

-   [Hinzufügen einer Operation][Hinzufügen einer Operation]
-   [Operations-Caching][Operations-Caching]
-   [Anforderungsparameter][Anforderungsparameter]
-   [Anforderungstext][Anforderungstext]
-   [Antworten][Antworten]
-   [Nächste Schritte][Nächste Schritte]

## <a name="add-operation"> </a>Hinzufügen einer Operation

Operationen werden in der Verwaltungskonsole zu einer API hinzugefügt und konfiguriert. Um die Verwaltungskonsole zu erreichen, klicken Sie im Azure-Portal auf **Verwaltungskonsole** für Ihren API-Verwaltungsdienst.

> Falls Sie noch keine API-Verwaltungs-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API-Verwaltungsinstanz][Erstellen einer API-Verwaltungsinstanz] im Lernprogramm [Erste Schritte mit der Azure API-Verwaltung][Erste Schritte mit der Azure API-Verwaltung].

![API-Verwaltungskonsole][API-Verwaltungskonsole]

Wählen Sie die gewünschte API im API-Verwaltungsportal aus und öffnen Sie die Registerkarte **Operationen**.

![Operationen][Operationen]

Klicken Sie auf **Operation hinzufügen**, um eine neue Operation zu erstellen. **Neue Operation** wird angezeigt, und die Registerkarte **Signatur** ist standardmäßig ausgewählt.

![Operation hinzufügen][Operation hinzufügen]

Wählen Sie das gewünschte **HTTP-Verb** aus der Dropdownliste aus.

![HTTP-Methode][HTTP-Methode]

Definieren Sie die URL-Vorlage, indem Sie ein URL-Fragment bestehend aus einem oder mehreren URL-Pfadsegmenten und null oder mehreren Abfrageparametern ein. Die URL-Vorlage wird an die Basis-URL der API angehängt und identifiziert eine einzige HTTP-Operation. Die Vorlage enthält einen oder mehrere variable Bestandteile, die durch geschweifte Klammern gekennzeichnet sind. Diese variablen Teile nennt man Vorlageparameter. Sie enthalten dynamische Werte aus der URL der Anforderung, wenn die Anforderung von der API-Verwaltungsplattform verarbeitet wird.

![URL-Vorlage][URL-Vorlage]

Geben Sie ggf. die **URL-Umschreibevorlage** ein. Mit dieser Vorlage können Sie die Standard-URL-Vorlage für die Verarbeitung eingehender Anforderungen im Front-End verwenden und gleichzeitig das Back-End über eine konvertierte URL anhand der Umschreibevorlage aufrufen. Die Vorlageparameter aus der URL-Vorlage sollten in der Umschreibevorlage verwendet werden. Im folgenden Beispiel wird der als Pfadsegment im Webdienst aus dem vorherigen Beispiel codierte Inhaltstyp mithilfe der URL-Vorlagen als Abfrageparameter an eine API übergeben werden kann, die über die API-Verwaltungsplattform veröffentlicht wurde.

![URL-Umschreibevorlage][URL-Umschreibevorlage]

Aufrufe an die Operation verwenden das Format `/customers?customerid=ALFKI`. Dieser Wert wird zu `/Customers('ALFKI')` umgewandelt, wenn das Back-End aufgerufen wird.

**Anzeigename** und **Beschreibung** enthalten eine Beschreibung der Operation und dienen als Dokumentation für Entwickler, die diese API im Entwicklerportal verwenden.

![Beschreibung][Beschreibung]

Die Operationsbeschreibung kann als Nur-Text oder HTML im Textfeld **Beschreibung** eingegeben werden.

## <a name="operation-caching"> </a>Operations-Caching

Das Caching von Antworten verbessert die Latenz der API-Consumer und senkt Bandbreitenverbrauch sowie Prozessorlast auf dem HTTP-Webdienst, der die API implementiert.

Wählen Sie die Registerkarte **Caching** und markieren Sie das Kontrollkästchen **Aktivieren**, um Caching für die Operation schnell und einfach zu aktivieren.

![Cachedienst][Cachedienst]

**Dauer** gibt den Zeitraum an, für den eine Operationsantwort im Cache verbleibt. Der Standardwert beträgt 3600 Sekunden oder 1 Stunde.

Cache-Schlüssel werden zur Unterscheidung von Antworten verwendet, und die Antwort für jeden einzelnen Cache-Schlüssel erhält einen eigenen Wert im Cache. Geben Sie optionale Abfrageparameter und/oder HTTP-Header in die Textfelder **Nach Abfrageparametern variieren** bzw. **Nach Headern variieren** ein, die bei der Berechnung der Cache-Schlüsselwerte verwendet werden sollen. Wenn Sie keine Parameter angeben, werden die komplette Anforderungs-URL und die folgenden HTTP-Headerwerte beim Generieren der Cache-Schlüssel verwendet: **Accept** und **Accept-Charset**.

> Weitere Informationen zum Caching und zu Caching-Richtlinien finden Sie unter [Caching von Operationsergebnissen in der Azure API-Verwaltung][Caching von Operationsergebnissen in der Azure API-Verwaltung].

## <a name="request-parameters"> </a>Anforderungsparameter

Operationsparameter werden auf der Registerkarte Parameter verwaltet. Die in der **URL-Vorlage** auf der Registerkarte **Signatur** angegebenen Parameter werden automatisch hinzugefügt und können nur durch Bearbeiten der URL-Vorlage geändert werden. Zusätzliche Parameter können manuell eingegeben werden.

Klicken Sie auf **Abfrageparameter hinzufügen** und geben Sie die folgenden Informationen ein, um einen neuen Abfrageparameter zu hinzuzufügen:

-   **Name** - Parametername.
-   **Beschreibung** - eine kurze Beschreibung des Parameters (optional).
-   **Typ** - Parametertyp aus einer Dropdownliste ausgewählt.
-   **Werte** - Werte, die dieser Parameter annehmen kann. Einer der Werte kann als Standard markiert werden (optional).
-   **Erforderlich** - Markieren Sie dieses Kontrollkästchen, um diesen Parameter obligatorisch zu machen.

![Anforderungsparameter][1]

## <a name="request-body"> </a>Anforderungstext

Wenn die Operation einen Anforderungstext erlaubt und benötigt (z. B. PUT, POST), dann können Sie ein Beispiel in allen unterstützten Darstellungsformaten angeben (z. B. JSON, XML).

> Der Anforderungstext wird nur zu Dokumentationszwecken verwendet und wird nicht geprüft.

Wechseln Sie zur Registerkarte **Text**, um einen Anforderungstext einzugeben.

Klicken Sie auf **Darstellung hinzufügen**, geben Sie den gewünschten Inhaltstyp ein (z. B. application/json), wählen Sie den Typ in der Dropdownliste aus und fügen Sie den gewünschten Beispiel-Anforderungstext für das jeweilige Format in das Textfeld ein.

![Anforderungstext][2]

Neben den Darstellungen können Sie auch eine optionale **Beschreibung** in Textform in das Textfeld eingeben.

## <a name="responses"> </a>Antworten

Sie sollten nach Möglichkeit Beispielantworten für alle Statuscodes angeben, die die Operation zurückgeben kann. Jeder Statuscode kann mehr als einen Antworttext existieren, nämlich einen pro unterstütztem Inhaltstyp.

Klicken Sie auf **Hinzufügen** und geben Sie den gewünschten Statuscode ein, um eine Antwort hinzuzufügen. Dieses Beispiel verwendet den Statuscode **200 OK**. Sobald der Code in der Dropdownlist angezeigt wird, können Sie ihn auswählen. Daraufhin wird der Antwortcode angelegt und zu Ihrer Operation hinzugefügt.

![Antwortcode][Antwortcode]

Klicken Sie auf **Darstellung hinzufügen**, geben Sie den gewünschten Inhaltstyp ein (z. B. application/json) und wählen Sie den Typ in der Dropdownliste aus.

![Inhaltstyp des Texts][Inhaltstyp des Texts]

Fügen Sie den Beispiel-Antworttext für das entsprechende Format in das Textfeld ein.

![Antworttext][Antworttext]

Fügen Sie bei Bedarf eine optionale Beschreibung in das Textfeld **Beschreibung** ein.

Wenn Sie die Operation konfiguriert haben, klicken Sie auf **Speichern**.

## <a name="next-steps"> </a>Nächste Schritte

Nachdem Sie die Operationen zur API hinzugefügt haben, können Sie die API zu einem Produkt zuordnen und veröffentlichen, sodass Entwickler die Operationen aufrufen können.

-   [Erstellen und Veröffentlichen eines Produkts][Erstellen und Veröffentlichen eines Produkts]

  [Hinzufügen einer Operation]: #add-operation
  [Operations-Caching]: #operation-caching
  [Anforderungsparameter]: #request-parameters
  [Anforderungstext]: #request-body
  [Antworten]: #responses
  [Nächste Schritte]: #next-steps
  [Erstellen einer API-Verwaltungsinstanz]: ../api-management-get-started/#create-service-instance
  [Erste Schritte mit der Azure API-Verwaltung]: ../api-management-get-started
  [API-Verwaltungskonsole]: ./media/api-management-howto-add-operations/api-management-management-console.png
  [Operationen]: ./media/api-management-howto-add-operations/api-management-operations.png
  [Operation hinzufügen]: ./media/api-management-howto-add-operations/api-management-add-operation.png
  [HTTP-Methode]: ./media/api-management-howto-add-operations/api-management-http-method.png
  [URL-Vorlage]: ./media/api-management-howto-add-operations/api-management-url-template.png
  [URL-Umschreibevorlage]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
  [Beschreibung]: ./media/api-management-howto-add-operations/api-management-description.png
  [Cachedienst]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
  [Caching von Operationsergebnissen in der Azure API-Verwaltung]: ../api-management-howto-cache
  [1]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
  [2]: ./media/api-management-howto-add-operations/api-management-request-body.png
  [Antwortcode]: ./media/api-management-howto-add-operations/api-management-response-code.png
  [Inhaltstyp des Texts]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
  [Antworttext]: ./media/api-management-howto-add-operations/api-management-response-body.png
  [Erstellen und Veröffentlichen eines Produkts]: ../api-management-howto-add-products

<!--HONumber=46--> 
