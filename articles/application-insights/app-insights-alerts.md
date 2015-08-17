<properties 
	pageTitle="Einrichten von Warnungen in Application Insights" 
	description="Erhalten Sie E-Mails zu Abstürzen, Ausnahmen und Metrikänderungen." 
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
	ms.date="07/13/2015" 
	ms.author="awills"/>
 
# Einrichten von Warnungen in Application Insights

[Visual Studio Application Insights][start] kann Sie bei Änderungen der Leistung oder von Nutzungsmetriken in Ihrer App benachrichtigen.

Application Insights überwacht die Live-App auf einer [Vielzahl von Plattformen][platforms], um Sie bei der Diagnose von Leistungsproblemen und beim Auswerten von Nutzungsmustern zu unterstützen.

Es gibt zwei Arten von Warnungen:
 
* **Webtests** informieren Sie, wenn Ihre Website im Internet nicht verfügbar ist oder langsam reagiert. [Weitere Informationen][availability].
* **Metrikwarnungen** teilen Ihnen mit, wenn eine Metrik für einen bestimmten Zeitraum einen Schwellenwert überschreitet – z. B. Fehleranzahl, Arbeitsspeicher oder Seitenaufrufe. 

Es gibt eine [separate Seite zu Webtests][availability], daher konzentrieren wir uns an dieser Stelle auf Metrikwarnungen.

## Metrikwarnungen

Wenn Sie Application Insights noch nicht für Ihre App eingerichtet haben, [holen Sie dies als erstes nach][start].

Um eine E-Mail zu erhalten, wenn eine Metrik einen Schwellenwert überschreitet, starten Sie entweder vom Metrik-Explorer oder von der Kachel "Warnungsregeln" auf dem Blatt "Übersicht".

![Klicken Sie auf dem Blatt "Warnungsregeln" auf "Warnung hinzufügen". Legen Sie Ihre App als die zu messende Ressource fest, geben Sie einen Namen für die Warnung ein, und wählen eine Metrik.](./media/app-insights-alerts/01-set-metric.png)

Legen Sie die Ressource vor den anderen Eigenschaften fest. **Wählen Sie die Ressource "(Komponenten)" aus**, wenn Sie Benachrichtigungen für Leistungs- oder Nutzungsmetriken festlegen möchten.

Achten Sie auf die Einheiten, die beim Eingeben des Schwellenwerts gefordert sind.

*Die Schaltfläche "Warnung hinzufügen" wird nicht angezeigt.* – Verwenden Sie ein Organisationskonto? Sie können Warnungen festlegen, wenn Sie für diese Anwendungsressource über Zugriffsberechtigungen für Besitzer oder Mitwirkende verfügen. Sehen Sie unter "Einstellungen" -> "Benutzer" nach. [Erfahren Sie mehr über die Zugriffssteuerung][roles].

## Anzeigen Ihrer Warnungen

Warnungen werden Ihnen per E-Mail gesendet.

Außerdem werden sie im Ereignisprotokoll "Vorgänge" angezeigt:

![Klicken Sie auf dem Blatt "Übersicht" im unteren Bereich auf "Ereignisse der letzten Woche"](./media/app-insights-alerts/09-alerts.png)

*Beziehen sich diese "Ereignisse" auf Telemetrie- oder benutzerdefinierte Ereignisse?*

* Nein. Diese operativen Ereignisse sind nur ein Protokoll der Geschehnisse auf dieser Anwendungsressource. 

## Verfügbarkeitswarnungen

Sie können Webtests einrichten, um eine beliebige Website von Standorten rund um die Welt aus zu testen. [Weitere Informationen][availability].

## Welche Warnungen sollten festgelegt werden?

Das hängt von Ihrer Anwendung ab. Zunächst einmal sollten Sie nicht zu viele Metriken festlegen. Beobachten Sie Ihre Metrikdiagramme eine Zeitlang, während die App ausgeführt wird, um ein Gefühl für das Normalverhalten zu erhalten. So finden Sie leichter Möglichkeiten zum Verbessern der Leistung. Richten Sie dann Warnungen ein, die Ihnen mitteilen, wenn die Metriken den Normalbereich verlassen.

Zu den gängigen Warnungen zählen Folgende:

* [Webtests][availability] sind wichtig, wenn es sich bei der Anwendung um eine Website oder einen Webdienst handelt, der im öffentlichen Internet sichtbar ist. Sie teilen Ihnen mit, wenn Ihre Website ausfällt oder langsam reagiert – selbst wenn das Problem eher beim Netzbetreiber als bei Ihrer App zu suchen ist. Hierbei handelt es sich allerdings um synthetische Tests, die daher nicht das tatsächliche Benutzererlebnis messen.
* [Browsermetriken][client], insbesondere Browser-Seitenladezeiten, eignen sich für Webanwendungen. Wenn Ihre Seite viele Skripts enthält, sollten Sie auf Browserausnahmen achten. Um diese Metriken und Warnungen zu erhalten, müssen Sie die [Webseitenüberwachung][client] einrichten.
* Serverantwortzeit und Fehler bei Anforderungen für die Serverseite von Webanwendungen. Achten Sie neben der Einrichtung von Warnungen auf diese Metriken, um festzustellen, ob sie bei hohen Anforderungsraten unverhältnismäßig variieren: Dies kann darauf hindeuten, dass für Ihre App nicht genügend Systemressourcen vorhanden sind.



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

 

<!---HONumber=August15_HO6-->