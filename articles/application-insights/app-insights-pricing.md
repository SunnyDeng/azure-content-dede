<properties 
	pageTitle="Verwalten von Preisen und Kontingenten für Application Insights" 
	description="Wählen Sie den benötigten Preisplan aus" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/18/2015" 
	ms.author="awills"/>

# Verwalten von Preisen und Kontingenten für Application Insights

*Application Insights befindet sich in der Vorschau.*

[Preise][pricing] für [Visual Studio Application Insights][start] basieren auf dem Datenvolumen pro Anwendung. Es gibt einen grundlegenden Free-Tarif, in dem Sie mit wenigen Einschränkungen die meisten Features nutzen können.

Jede Application Insights-Ressource wird als separater Dienst abgerechnet und auf der Rechnung für Ihr Azure-Abonnement aufgeführt.

[Informationen hierzu finden Sie in der Preisübersicht.][pricing]

## Anzeigen der Kontingente und des Preisplans für Ihre Application Insights-Ressource

Sie können das Blatt zu Kontingenten und Preisen über die Ressourceneinstellungen Ihrer Anwendung öffnen.

![Wählen Sie "Einstellungen", "Kontingente + Preise".](./media/app-insights-pricing/01-pricing.png)

Die Wahl des Tarifs wirkt sich auf Folgendes aus:

* [Monatliches Kontingent](#monthly-quota) – Die Menge der Telemetriedaten, die Sie jeden Monat analysieren können.
* [Datenrate](#data-rate) – Die maximale Rate, mit der Daten aus Ihrer App verarbeitet werden können.
* [Aufbewahrung](#data-retention) – Die Dauer der Aufbewahrung Ihrer Daten zur Ansicht im Application Insights-Portal.
* [Fortlaufender Export](#continuous-export) – Die Möglichkeit des Exports von Daten in andere Tools und Dienste.

Diese Grenzwerte werden für jede Application Insights-Ressource separat festgelegt.

### Kostenlose Premium-Testversion

Wenn Sie erstmals eine neue Application Insights-Ressource erstellen, wird diese im Free-Tarif gestartet.

Sie können jederzeit zur kostenlosen 30-Tage-Testversion des Premium-Tarifs wechseln. Somit erhalten Sie die Vorteile des Premium-Tarifs. Nach 30 Tagen wird dieser automatisch auf den Tarif zurückgesetzt, den Sie zuvor verwendet haben, sofern Sie nicht explizit einen anderen Tarif auswählen. Wählen Sie den gewünschten Tarif zu einem beliebigen Zeitpunkt während des Testzeitraums aus. Sie erhalten weiterhin die kostenlose Testversion bis Ablauf des Zeitraums von 30 Tagen.


## Monatliches Kontingent

* In jedem Kalendermonat kann Ihre Anwendung maximal eine bestimmte Menge an Telemetriedaten an Application Insights senden. Die genauen Zahlen können Sie der [Preisübersicht][pricing] entnehmen. 
* Das Kontingent hängt von dem Tarif ab, den Sie ausgewählt haben.
* Das Kontingent beginnt um Mitternacht UTC am ersten Tag jedes Monats.
* Das Datenpunktediagramm zeigt, wie viel von Ihrem Kontingent in diesem Monat verwendet wurde.
* Das Kontingent wird in *Datenpunkten* gemessen. Ein einzelner Datenpunkt ist ein Aufruf einer der Nachverfolgungsmethoden, unabhängig davon, ob sie explizit in Ihrem Code oder durch eines der Standardtelemetriemodule aufgerufen wird. Datenpunkte enthalten Folgendes:
 * Alle Zeilen in der [Diagnosesuche](app-insights-diagnostic-search.md) 
 * Jede Rohmessung einer [Metrik](app-insights-metrics-explorer.md), etwa eines Leistungsindikators. (Die Punkte, die Sie in den Diagrammen sehen, sind normalerweise Aggregate von mehreren Rohdatenpunkten).
 * Alle Punkte in den Diagrammen zum [Webtest (Verfügbarkeit)](app-insights-monitor-web-app-availability.md). 
* *Sitzungsdaten* werden im Kontingent nicht berücksichtigt. Hierzu zählen die Anzahl von Benutzern oder Sitzungen sowie Umgebungs- und Gerätedaten.


### Overage

Wenn die Anwendung mehr als das monatliche Kontingent sendet, haben Sie folgende Möglichkeiten:

* Sie bezahlen für zusätzliche Daten. Informationen hierzu finden Sie in der [Preisübersicht][pricing]. Sie können diese Option im Voraus auswählen. Im Free-Tarif ist diese Option nicht verfügbar.
* Sie führen ein Upgrade für Ihren Tarif aus.
* Sie unternehmen nichts. Sitzungsdaten weiterhin aufgezeichnet, aber andere Daten werden in der Diagnosesuche oder im Metrik-Explorer nicht aufgeführt.


### Wie viele Daten sende ich?

Das Diagramm im unteren Bereich des Blatts mit der Preisübersicht zeigt das Datenpunktvolumen Ihrer Anwendung gruppiert nach Datenpunkttypen an. (Sie können dieses Diagramm auch im Metrik-Explorer erstellen.)

![Im unteren Bereich des Blatts mit der Preisübersicht](./media/app-insights-pricing/03-allocation.png)

Klicken Sie auf das Diagramm, um weitere Details einzublenden, oder ziehen Sie den Mauszeiger darüber, und klicken Sie auf „(+)“, um einen Zeitraum im Detail anzuzeigen.


## Datenrate

Zusätzlich zum monatlichen Kontingent gibt es Begrenzungen der Datenrate. Beim Tarif [Free][pricing] liegt die Grenze bei 200 Datenpunkten pro Sekunde, gemittelt über 5 Minuten. Bei kostenpflichtigen Tarifen ist der Grenzwert 500/s, gemittelt über eine Minute.

Es gibt drei Buckets, die getrennt gezählt werden:

* [TrackTrace-Aufrufe](app-insights-api-custom-events-metrics.md#track-trace) und [Erfasste Protokolle](app-insights-asp-net-trace-logs.md)
* [Ausnahmen](app-insights-api-custom-events-metrics.md#track-exception), begrenzt auf 50 Punkte/s
* Alle anderen Telemetriedaten (Seitenaufrufe, Sitzungen, Anforderungen, Abhängigkeiten, Metriken, benutzerdefinierte Ereignisse).

Wenn die App mehrere Minuten lang mehr Daten sendet, als der Grenzwert zulässt, können einige Daten gelöscht werden. Sie erhalten zur Warnung eine Benachrichtigung, dass dies erfolgt ist.

### Tipps zur Reduzierung der Datenrate

Wenn Begrenzungsdrosselungen auftreten, können Sie verschiedene Schritte ausführen:

* Verwenden Sie [Stichproben](app-insights-sampling.md). Diese Technologie verringert die Datenrate, ohne die Metriken zu verzerren und ohne die Navigation zwischen verwandten Elementen bei der Suche zu stören.
* Deaktivieren Sie nicht benötigte Erfassungsmodule durch [Bearbeiten von „ApplicationInsights.config“](app-insights-configuration-with-applicationinsights-config.md). Das kann z. B. für Leistungsindikator- oder Abhängigkeitsdaten gelten.
* Aggregieren Sie Metriken vorab. Wenn Sie Ihrer App Aufrufe an TrackMetric eingefügt haben, können Sie Datenverkehr reduzieren, indem Sie die Überladung verwenden, die Ihre Berechnung des Durchschnitts und die Standardabweichung eines Batches von Messungen akzeptiert. Oder Sie können ein [vorab aggregierendes Paket](https://www.myget.org/gallery/applicationinsights-sdk-labs) verwenden. 


### Begrenzungen von Namen

1.	Bis zu 200 eindeutige Metriknamen und 200 eindeutige Eigenschaftennamen für Ihre Anwendung. Zu den Metriken gehören Daten, die über TrackMetric gesendet werden, sowie Messungen für andere Datentypen wie z. B. Ereignisse. [Metriken und Eigenschaftennamen][api] gelten global pro Instrumentationsschlüssel und werden nicht auf den Datentyp begrenzt.
2.	[Eigenschaften][apiproperties] können nur zur Filterung und zur Gruppierung verwendet werden, solange sie weniger als 100 eindeutige Werte für jede Eigenschaft aufweisen. Sobald es mehr als 100 eindeutige Werte gibt, kann die Eigenschaft zwar noch zur Suche und Filterung, jedoch nicht mehr für Filter verwendet werden.
3.	Standardeigenschaften wie z. B. RequestName und die Seiten-URL, sind auf 1000 eindeutige Werte pro Woche beschränkt. Nach 1000 eindeutigen Werten werden zusätzliche Werte als "Andere Werte" gekennzeichnet. Der ursprüngliche Wert kann nach wie vor für die Volltextsuche und die Filterung verwendet werden.

## Beibehaltung von Daten

Ihr Tarif bestimmt, wie lange Daten im Portal aufbewahrt werden und dadurch auch, wie weit zurück Sie die Zeiträume festlegen können.


* Rohdatenpunkte (also Instanzen, die Sie bei der Diagnosesuche überprüfen können): 7 bis 30 Tage.
* Aggregierte Daten (d. h. Zählungen, Mittelwerte und andere statistischen Daten, die im Metrik-Explorer angezeigt werden) werden im Maß von 1 Minute für 30 Tage und 1 Stunde oder 1 Tag (abhängig vom Typ) für mindestens 13 Monate aufbewahrt.




## Anzeigen der Rechnung für Ihr Azure-Abonnement

Die Gebühren für Application Insights werden Ihrer Azure-Rechnung hinzugefügt. Sie sehen die Details zu Ihrer Azure-Rechnung im Bereich "Abrechnung" des Azure-Portals oder im [Azure-Abrechnungsportal](https://account.windowsazure.com/Subscriptions).

![Wählen Sie im seitlichen Menü die Option "Abrechnung".](./media/app-insights-pricing/02-billing.png)

## Zusammenfassung der Grenzwerte

[AZURE.INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

 

<!---HONumber=AcomDC_1203_2015-->