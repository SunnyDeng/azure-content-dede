<properties 
    pageTitle="Service Bus – Preise und Abrechnung | Microsoft Azure"
    description="Übersicht über die Service Bus-Preisstruktur."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="03/16/2016"
    ms.author="sethm" />

# Service Bus – Preise und Abrechnung

Service Bus wird in den Ebenen Basic, Standard und [Premium](service-bus-premium-messaging.md) angeboten. Sie können eine Dienstebene für jeden von Ihnen erstellten Service Bus-Dienstnamespace auswählen, und diese Ebenenauswahl gilt für alle Warteschlangen, Themen/Abonnements, Relays und Event Hubs, die in diesem Namespace erstellt werden.

>[AZURE.NOTE] Ausführliche Informationen zu den aktuellen Service Bus-Preisen finden Sie auf der [Azure Service Bus-Preisseite](https://azure.microsoft.com/pricing/details/service-bus/) und unter [Häufig gestellte Fragen zu Service Bus-Preisen](service-bus-pricing-faq.md).

Service Bus verwendet die zwei folgenden Messgrößen für Warteschlangen und Themen/Abonnements:

1. **Messagingvorgänge**: Definiert als API-Aufrufe für Warteschlangen- oder Themen-/Abonnementdienstendpunkte. Diese Messgröße ersetzt gesendete oder empfangene Nachrichten als primäre Einheit der abrechenbaren Nutzung für Warteschlangen und Themen/Abonnements.

2. **Brokerverbindungen**: Definiert als die Höchstanzahl dauerhafter Verbindungen, die für Warteschlangen, Themen/Abonnements oder Event Hubs während eines angegebenen einstündigen Stichprobenzeitraums geöffnet sind. Diese Messgröße gilt nur in der Ebene "Standard", in der Sie zusätzliche Verbindungen für eine nominale Gebühr pro Verbindung öffnen können (zuvor waren Verbindungen auf 100 Verbindungen pro Warteschlange/Thema/Abonnement begrenzt).

Mit der Ebene **Standard** werden gestaffelte Preise für Vorgänge eingeführt, die mit Warteschlangen und Themen/Abonnements ausgeführt werden. Dies führt zu volumenbasierten Rabatten von bis zu 80 % in den höchsten Nutzungsebenen. Außerdem wird eine Basisgebühr von 10 US-Dollar pro Monat für die Ebene "Standard" erhoben, die es Ihnen ermöglicht, bis zu 12,5 Millionen Vorgänge pro Monat ohne zusätzliche Kosten auszuführen.

Die Ebene **Premium** bietet Ressourcenisolierung auf der CPU- und Arbeitsspeicherebene, sodass die Workloads der einzelnen Kunden isoliert ausgeführt werden. Dieser Ressourcencontainer wird als *Messaging-Einheit* bezeichnet. Jedem Premium-Namespace wird mindestens eine Messaging-Einheit zugeordnet. Sie können 1, 2 oder 4 Messaging-Einheiten für jeden Service Bus Premium-Namespace erwerben. Eine einzelne Workload oder Entität kann mehrere Messaging-Einheiten umfassen, und die Anzahl der Einheiten kann beliebig geändert werden. Gebühren werden jedoch im 24-Stunden- bzw. Tagesrhythmus berechnet. Das Ergebnis ist eine vorhersehbare und wiederholbare Leistung Ihrer Service Bus-basierten Lösung. Diese Leistung ist nicht nur besser vorhersehbar und verfügbar, sondern auch schneller. Azure Service Bus Premium-Messaging basiert auf dem in Azure Event Hubs eingeführten Speichermodul. Mit Premium-Messaging wird bei Spitzenleistung eine viel höhere Geschwindigkeit erzielt, als bei der Standard-Stufe.

Beachten Sie, dass die Standardbasisgebühr nur einmal pro Monat pro Azure-Abonnement berechnet wird. Dies bedeutet, dass Sie nach dem Erstellen eines einzelnen Service Bus-Namespaces der Ebene "Standard" oder "Premium" beliebig viele weitere Namespaces der Ebene "Standard" oder "Premium" unter dem gleichen Azure-Abonnement erstellen können, ohne dass zusätzliche Basisgebühren entstehen.

Alle vorhandenen Service Bus-Namespaces, die vor dem 1. November 2014 erstellt wurden, werden automatisch der Ebene "Standard" zugeordnet. Dadurch wird sichergestellt, dass Sie auch weiterhin Zugriff auf alle Funktionen haben, die derzeit mit Service Bus verfügbar sind. Anschließend können Sie über das [klassische Azure-Portal][] bei Bedarf eine Herabstufung auf die Ebene „Basic“ durchführen.

In der folgenden Tabelle sind die Funktionsunterschiede zwischen den Ebenen "Basic" und "Standard"/"Premium" zusammengefasst.

|Funktion|Basic|Standard/Premium|
|---|---|---|
|Event Hubs|Ja|Ja|
|Warteschlangen|Ja|Ja|
|Geplante Nachrichten|Ja|Ja|
|Themen/Abonnements|Nein|Ja|
|Relays|Nein|Ja|
|Transaktionen|Nein|Ja|
|Deduplizierung|Nein|Ja|
|Sitzungen|Nein|Ja|
|Umfangreiche Nachrichten|Nein|Ja|
|ForwardTo|Nein|Ja|
|SendVia|Nein|Ja|
|Brokerverbindungen (enthalten)|100 pro Service Bus-Namespace|1\.000 pro Azure-Abonnement|
|Brokerverbindungen (Überschreitung zulässig)|Nein|Ja (abrechenbar)|

## Messagingvorgänge

Als Teil des neuen Preismodells ändert sich die Abrechnung für Warteschlangen und Themen/Abonnements. Diese Entitäten wechseln aus einer Abrechnung pro Nachricht zu einer Abrechnung pro Vorgang. Ein "Vorgang" bezieht sich auf jeden API-Aufruf für einen Warteschlangen- oder Themen-/Abonnementdienstendpunkt. Dies umfasst Verwaltungs-, Sende-/Empfangs- und Sitzungsstatusvorgänge.

|Vorgangstyp|Beschreibung|
|---|---|
|Verwaltung|Erstellen, Lesen, Aktualisieren und Löschen (CRUD) von Warteschlangen oder Themen/Abonnements.|
|Nachrichten|Senden und Empfangen von Nachrichten mit Warteschlangen oder Themen/Abonnements.|
|Sitzungsstatus|Abrufen oder Festlegen des Sitzungsstatus für eine Warteschlange oder ein Thema/Abonnement.|

Seit dem 1. November 2014 gelten die folgenden Preise:

|Basic|Kosten|
|---|---|
|Vorgänge|0,05 US-Dollar pro 1 Million Vorgänge|

|Standard|Kosten|
|---|---|
|Basisgebühr|10 US-Dollar/Monat|
|Erste 12,5 Millionen Vorgänge/Monat|Enthalten|
|12,5–100 Millionen Vorgänge/Monat|0,80 US-Dollar pro 1 Million Vorgänge|
|100–2.500 Millionen Vorgänge/Monat|0,50 US-Dollar pro 1 Million Vorgänge|
|Mehr als 2.500 Millionen Vorgänge/Monat|0,20 US-Dollar pro 1 Million Vorgänge|

>[AZURE.NOTE] Die Ebene "Premium" befindet sich derzeit in der Vorschauphase. Bei dem folgenden Preis gilt für die Vorschauphase ein Rabatt von 50 %.

|Premium|Kosten|
|---|---|
|Täglich|11,13 US-Dollar Festpreis pro Nachrichteneinheit|

## Brokerverbindungen

*Brokerverbindungen* ermöglichen Nutzungsmuster von Kunden, die eine große Anzahl „dauerhaft verbundener“ Absender/Empfänger für Warteschlangen, Themen/Abonnements oder Event Hubs beinhalten. Dauerhaft verbundene Absender/Empfänger sind die Absender/Empfänger, die mithilfe von AMQP oder HTTP mit einem Empfangstimeout ungleich null eine Verbindung herstellen (z. B. langer HTTP-Abruf). HTTP-Absender und -Empfänger mit einem sofortigen Timeout generieren keine Brokerverbindungen.

Zuvor galt für Warteschlangen und Themen/Abonnements ein Grenzwert von 100 gleichzeitigen Verbindungen pro URL. Mit dem aktuellen Abrechnungsschema entfällt der Grenzwert pro URL für Warteschlangen und Themen/Abonnements. Stattdessen werden Kontingente und Messgrößen für Brokerverbindungen auf der Ebene des Service Bus-Namespaces und des Azure-Abonnements implementiert.

Die Ebene "Basic" umfasst 100 Brokerverbindungen pro Service Bus-Namespace (und ist streng auf diese Anzahl beschränkt). Verbindungen, die diese Anzahl überschreiten, werden in der Ebene "Basic" zurückgewiesen. In der Ebene "Standard" entfällt der Grenzwert pro Namespace. Es zählt die aggregierte Verwendung von Brokerverbindungen im Azure-Abonnement. In der Ebene "Standard" sind 1.000 Brokerverbindungen pro Azure-Abonnement ohne zusätzliche Kosten (die über die Basisgebühr hinausgehen) zulässig. Wenn mehr als insgesamt 1.000 Brokerverbindungen für Service Bus-Namespaces der Ebene "Standard" in einem Azure-Abonnement verwendet werden, erfolgt eine gestaffelte Abrechnung entsprechend der folgenden Tabelle.

|Brokerverbindungen (Ebene "Standard")|Kosten|
|---|---|
|Erste 1.000/Monat|In der Basisgebühr enthalten|
|1\.000–100.000/Monat|0,03 US-Dollar pro Verbindung/Monat|
|100\.000–500.000/Monat|0,025 US-Dollar pro Verbindung/Monat|
|Mehr als 500.000/Monat|0,015 US-Dollar pro Verbindung/Monat|

>[AZURE.NOTE] 1.000 Brokerverbindungen sind in der Messaging-Ebene "Standard" (über die Basisgebühr) enthalten und können für alle Warteschlangen, Themen/Abonnements und Event Hubs im zugehörigen Azure-Abonnement gemeinsam verwendet werden.

>[AZURE.NOTE] Die Abrechnung erfolgt anhand der höchsten Anzahl gleichzeitiger Verbindungen und wird anteilsmäßig auf Basis von 744 Stunden pro Monat berechnet.

|Ebene "Premium"
|---|
|Brokerverbindungen werden in der Ebene "Premium" nicht in Rechnung gestellt.|

Weitere Informationen zu Brokerverbindungen finden Sie weiter unten im Abschnitt [Häufig gestellte Fragen](#FAQ).

## Relay

Relays sind nur in den Namespaces der Ebene "Standard" verfügbar. Preise und Verbindungskontingente für Relays sind ansonsten unverändert. Dies bedeutet, dass Relays auch weiterhin anhand der Anzahl der Nachrichten (nicht der Vorgänge) und der Relaystunden abgerechnet werden.

|Preise für Relays|Kosten|
|---|---|
|Relaystunden|0,10 US-Dollar pro 100 Relaystunden|
|Meldungen|0,01 US-Dollar pro 10.000 Nachrichten|

## Häufig gestellte Fragen

### Wie wird die Verbrauchseinheit "Relaystunden" berechnet?

Informationen dazu finden Sie in [diesem Thema](service-bus-pricing-faq.md#How-is-the-Relay-Hours-meter-calculated?).

### Was sind Brokerverbindungen, und wie erfolgt ihre Abrechnung?

Eine Brokerverbindung ist als eine der folgenden Verbindungen definiert:

1. Eine AMQP-Verbindung von einem Client mit einem Service Bus-Thema/Abonnement, einer Service Bus-Warteschlange oder einem Service Bus-Event Hub.

2. Ein HTTP-Aufruf zum Empfangen einer Nachricht von einem Service Bus-Thema oder einer Service Bus-Warteschlange, das bzw. die ein Empfangstimeout größer als null aufweist.

Für Service Bus wird die höchste Anzahl gleichzeitiger Brokerverbindungen in Rechnung gestellt, die die enthaltene Menge (1.000 Verbindungen in der Ebene "Standard") überschreiten. Spitzenwerte werden stündlich gemessen, anteilig (dividiert durch 744 Stunden pro Monat) ermittelt und für den monatlichen Abrechnungszeitraum addiert. Die enthaltene Menge (1.000 Brokerverbindungen pro Monat) wird am Ende des Abrechnungszeitraums auf die Summe der anteiligen stündlichen Spitzenwerte angewendet.

Zum Beispiel:

1. Jedes Gerät von 10.000 Geräten stellt eine Verbindung über eine einzelne AMQP-Verbindung her und empfängt Befehle von einem Service Bus-Thema. Die Geräte senden Telemetrieereignisse an einen Event Hub. Wenn alle Geräte 12 Stunden täglich verbunden sind, entstehen die folgenden Verbindungsgebühren (zusätzlich zu eventuellen weiteren Gebühren für das Service Bus-Thema): 10.000 Verbindungen * 12 Stunden * 31 Tage/744 = 5.000 Brokerverbindungen. Nach Abzug der monatlich zulässigen Menge von 1.000 Brokerverbindungen werden Ihnen 4.000 Brokerverbindungen zu einem Tarif von 0,03 US-Dollar pro Brokerverbindung berechnet. Dies ergibt eine Summe von 120 US-Dollar.

2. 10\.000 Geräte empfangen Nachrichten von einer Service Bus-Warteschlange über HTTP mit einem festgelegten Timeout ungleich null. Wenn alle Geräte 12 Stunden täglich verbunden sind, entstehen die folgenden Verbindungsgebühren (zusätzlich zu eventuellen weiteren Service Bus-Gebühren): 10.000 HTTP Empfangsverbindungen * 12 Stunden pro Tag * 31 Tage/744 Stunden = 5.000 Brokerverbindungen.

### Gelten für Warteschlangen und Themen/Abonnements Gebühren für Brokerverbindungen?

Ja. Es gibt keine Verbindungsgebühren für das Senden von Ereignissen über HTTP, unabhängig von der Anzahl der sendenden Systeme oder Geräte. Durch das Empfangen von Ereignissen mit HTTP mithilfe eines Timeouts größer als null (manchmal als "langer Abruf" bezeichnet) entstehen Gebühren für Brokerverbindungen. Durch AMQP-Verbindungen entstehen Gebühren für Brokerverbindungen, unabhängig davon, ob die Verbindungen zum Senden oder Empfangen verwendet werden. Beachten Sie, dass 100 Brokerverbindungen in einem Basic-Namespace kostenlos zulässig sind. Dies ist auch die maximale Anzahl von Brokerverbindungen, die in einem Azure-Abonnement zulässig ist. Die ersten 1.000 Brokerverbindungen in allen Standard-Namespaces in einem Azure-Abonnement sind ohne zusätzliche Kosten (über die Basisgebühr hinaus) enthalten. Da diese Kontingente für viele Dienst-zu-Dienst-Messaging-Szenarios ausreichend sind, werden Gebühren für Brokerverbindungen normalerweise nur dann relevant, wenn geplant ist, lange AMQP-oder HTTP-Abrufe mit einer großen Anzahl von Clients zu verwenden (z. B., um effizienteres Ereignisstreaming zu erreichen oder die bidirektionale Kommunikation mit zahlreichen Geräten oder Anwendungsinstanzen zu ermöglichen).

## Nächste Schritte

- Weitere Informationen zu Service Bus-Preisen finden Sie auf der [Azure Service Bus-Preisseite](https://azure.microsoft.com/pricing/details/service-bus/).

- Unter [Häufig gestellte Fragen zu Service Bus-Preisen](service-bus-pricing-faq.md) finden Sie einige häufig gestellte allgemeine Fragen zur Servicebus-Preisgestaltung und -Abrechnung.

[klassische Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0323_2016-->