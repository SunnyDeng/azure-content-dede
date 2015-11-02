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
	ms.date="09/23/2015" 
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

![Klicken Sie auf dem Blatt "Warnungsregeln" auf "Warnung hinzufügen". Legen Sie Ihre App als die zu messende Ressource fest, geben Sie einen Namen für die Warnung ein, und wählen Sie eine Metrik.](./media/app-insights-alerts/01-set-metric.png)

Legen Sie die Ressource vor den anderen Eigenschaften fest. **Wählen Sie die Ressource "(Komponenten)" aus**, wenn Sie Benachrichtigungen für Leistungs- oder Nutzungsmetriken festlegen möchten.

Achten Sie auf die Einheiten, die beim Eingeben des Schwellenwerts gefordert sind.

Der Name, den Sie der Warnung zuweisen, muss innerhalb der Ressourcengruppe (nicht nur in Ihrer Anwendung) eindeutig sein.

*Die Schaltfläche "Warnung hinzufügen" wird nicht angezeigt.* – Verwenden Sie ein Organisationskonto? Sie können Warnungen festlegen, wenn Sie für diese Anwendungsressource über Zugriffsberechtigungen für Besitzer oder Mitwirkende verfügen. Sehen Sie unter "Einstellungen" -> "Benutzer" nach. [Erfahren Sie mehr über die Zugriffssteuerung][roles].

## Anzeigen Ihrer Warnungen

Sie erhalten eine E-Mail, wenn sich ein Warnungsstatus von "Inaktiv" in "Aktiv" ändert und umgekehrt.

Der aktuelle Status jeder Warnung wird auf dem Blatt "Warnungsregeln" angezeigt.

Die Dropdownliste "Warnungen" enthält eine Übersicht über die letzten Aktivitäten:

![](./media/app-insights-alerts/010-alert-drop.png)

Der Verlauf von Statusänderungen befindet sich im Ereignisprotokoll "Vorgänge":

![Klicken Sie auf dem Blatt "Übersicht" im unteren Bereich auf "Ereignisse der letzten Woche"](./media/app-insights-alerts/09-alerts.png)

*Beziehen sich diese "Ereignisse" auf Telemetrie- oder benutzerdefinierte Ereignisse?*

* Nein. Diese operativen Ereignisse sind nur ein Protokoll der Geschehnisse auf dieser Anwendungsressource. 


## Funktionsweise von Warnungen

* Eine Warnung verfügt über zwei Zustände: "Warnung" und "Fehlerfrei". 

* Wenn sich der Zustand einer Warnung ändert, wird eine E-Mail gesendet.

* Eine Warnung wird jedes Mal ausgewertet, wenn eine Metrik empfangen wird, sonst aber aus keinem anderen Grund.

* Durch die Auswertung wird die Metrik über den vorangegangenen Zeitraum aggregiert und mit dem Schwellenwert verglichen, um den neuen Zustand zu ermitteln.

* Der von Ihnen ausgewählte Zeitraum gibt das Intervall an, über das Metriken aggregiert werden. Der Zeitraum hat keinen Einfluss darauf, wie häufig die Warnung ausgewertet wird: Dies hängt davon ab, wie häufig Metriken empfangen werden.

* Wenn eine Zeit lang kein Daten für eine bestimmte Metrik empfangen wurden, wirkt sich diese Lücke unterschiedlich auf die Auswertung von Warnungen und die Diagramme im Metrik-Explorer aus. Wenn länger als durch das Samplingintervall des Diagramms vorgegeben keine Daten empfangen werden, hat das Diagramm im Metrik-Explorer den Wert 0. Eine auf derselben Metrik basierende Warnung wird jedoch nicht erneut ausgewertet, und der Zustand der Warnung bleibt unverändert.

    Sobald dann Daten empfangen werden, springt das Diagramm auf einen Wert ungleich 0 (null) zurück. Die Warnung wird auf Grundlage der Daten ausgewertet, die für den angegebenen Zeitraum zur Verfügung stehen. Wenn der neue Datenpunkt der einzige in diesem Zeitraum verfügbare Datenpunkt ist, basiert das Aggregat auf diesem Datenpunkt.

* Eine Warnung kann häufig zwischen den Zuständen "Warnung" und "Fehlerfrei" hin und her wechseln, auch wenn Sie einen langen Zeitraum festlegen. Dies kann vorkommen, wenn sich der metrische Wert um den Schwellenwert herum bewegt. Der Schwellenwert weist keine Hysterese auf: Der Übergang zur Warnung erfolgt beim selben Wert wie der Übergang zum fehlerfreien Zustand.



## Verfügbarkeitswarnungen

Sie können Webtests einrichten, um eine beliebige Website von Standorten rund um die Welt aus zu testen. [Weitere Informationen][availability].

## Welche Warnungen sollten festgelegt werden?

Das hängt von Ihrer Anwendung ab. Zunächst einmal sollten Sie nicht zu viele Metriken festlegen. Beobachten Sie Ihre Metrikdiagramme eine Zeitlang, während die App ausgeführt wird, um ein Gefühl für das Normalverhalten zu erhalten. So finden Sie leichter Möglichkeiten zum Verbessern der Leistung. Richten Sie dann Warnungen ein, die Ihnen mitteilen, wenn die Metriken den Normalbereich verlassen.

Zu den gängigen Warnungen zählen Folgende:

* [Webtests][availability] sind wichtig, wenn es sich bei der Anwendung um eine Website oder einen Webdienst handelt, der im öffentlichen Internet sichtbar ist. Sie teilen Ihnen mit, wenn Ihre Website ausfällt oder langsam reagiert – selbst wenn das Problem eher beim Netzbetreiber als bei Ihrer App zu suchen ist. Hierbei handelt es sich allerdings um synthetische Tests, die daher nicht das tatsächliche Benutzererlebnis messen.
* [Browsermetriken][client], insbesondere **Browser-Seitenladezeiten**, eignen sich für Webanwendungen. Wenn Ihre Seite viele Skripts enthält, sollten Sie auf **Browserausnahmen** achten. Um diese Metriken und Warnungen zu erhalten, müssen Sie die [Webseitenüberwachung][client] einrichten.
* **Serverantwortzeit** und **Anforderungsfehler** für die Serverseite von Webanwendungen. Achten Sie neben der Einrichtung von Warnungen auf diese Metriken, um festzustellen, ob sie bei hohen Anforderungsraten unverhältnismäßig variieren: Dies kann darauf hindeuten, dass für Ihre App nicht genügend Systemressourcen vorhanden sind.
* **Serverausnahmen** erfordern ein [zusätzliches Setup](app-insights-asp-net-exceptions.md), damit sie angezeigt werden.

## Festlegen von Warnungen mit PowerShell

In den meisten Fällen reicht es aus, Warnungen manuell festzulegen. Wenn Sie jedoch Metrikwarnungen automatisch erstellen möchten, können Sie hierzu PowerShell verwenden.

#### Einmalige Konfiguration

Wenn Sie PowerShell noch nicht mit Ihrem Azure-Abonnement verwendet haben:

Installieren Sie das Azure PowerShell-Modul auf dem Computer, auf dem die Skripts ausgeführt werden sollen.

 * Installieren Sie [Microsoft-Webplattform-Installer (Version 5 oder höher)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Installieren Sie hiermit Microsoft Azure PowerShell.


#### Herstellen einer Verbindung mit Azure

Starten Sie Azure PowerShell, und [stellen Sie eine Verbindung mit Ihrem Abonnement her](powershell-install-configure.md):

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


#### Abrufen von Warnungen

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

#### Warnung hinzufügen


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



#### Beispiel 1

E-Mail-Nachricht senden, wenn die Antwort des Servers auf HTTP-Anforderungen, gemittelt über 5 Minuten, langsamer als 1 Sekunde ist. Der Name meiner Application Insights-Ressource lautet IceCreamWebApp, und sie befindet sich in der Ressourcengruppe "Fabrikam". Ich bin Besitzer des Azure-Abonnements.

Die GUID ist die Abonnement-ID (nicht der Instrumentierungsschlüssel der Anwendung).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

#### Beispiel 2

Ich habe eine Anwendung, in der ich mit [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) eine Metrik namens „salesPerHour“ melde. Eine E-Mail an meine Kollegen senden, wenn "salesPerHour" gemittelt über 24 Stunden unter 100 fällt.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Dieselbe Regel kann für die Metrik verwendet werden, die mit dem [Messparameter](app-insights-api-custom-events-metrics.md#properties) eines anderen Trackingaufrufs gemeldet wird, z. B. TrackEvent oder trackPageView.

#### Metriknamen

Metrikname | Anzeigename | Beschreibung
---|---|---
`basicExceptionBrowser.count`|Browserausnahmen|Anzahl nicht erfasster Ausnahmen, die im Browser ausgelöst wurden.
`basicExceptionServer.count`|Serverausnahmen|Anzahl der nicht behandelten Ausnahmen, die von der Anwendung ausgelöst wurden
`clientPerformance.clientProcess.value`|Clientverarbeitungszeit|Zeit zwischen dem Empfang des letzten Byte eines Dokuments und dem Laden des DOM. Asynchrone Anforderungen werden möglicherweise immer noch verarbeitet.
`clientPerformance.networkConnection.value`|Netzwerkverbindungszeit zum Laden der Seite| Zeit, die der Browser für die Verbindung mit dem Netzwerk benötigt. Kann bei Zwischenspeicherung 0 sein.
`clientPerformance.receiveRequest.value`|Empfängt Antwortzeit| Zeit zwischen dem Senden der Anforderung durch den Browser und dem Empfangen der Antwort.
`clientPerformance.sendRequest.value`|Anforderungszeit senden| Vom Browser benötigte Zeit zum Senden der Anforderung.
`clientPerformance.total.value`|Browser-Seitenladezeit|Zeit ab der Benutzeranforderung, bis DOM, Stylesheets, Skripts und Bilder geladen werden.
`performanceCounter.available_bytes.value`|Verfügbarer Arbeitsspeicher|Physischer Arbeitsspeicher, der sofort für einen Prozess oder für die Nutzung durch das System verfügbar ist.
`performanceCounter.io_data_bytes_per_sec.value`|E/A-Rate für Prozess|Gesamtanzahl von pro Sekunde in Dateien, im Netzwerk und auf Geräten gelesenen und geschriebenen Bytes.
`performanceCounter.number_of_exceps_thrown_per_sec`|Ausnahmerate|Pro Sekunde ausgelöste Ausnahmen.
`performanceCounter.percentage_processor_time.value`|Prozess-CPU|Der Prozentsatz der verstrichenen Zeit für alle Prozessthreads, die vom Prozessor zur Ausführung von Anweisungen für den Anwendungsprozess verwendet wird.
`performanceCounter.percentage_processor_total.value`|Prozessorzeit|Der Prozentsatz der Zeit, die der Prozessor nicht im Leerlauf in Threads verbringt.
`performanceCounter.process_private_bytes.value`|Private Bytes für Prozess|Speicher, der exklusiv den überwachten Anwendungsprozessen zugewiesen ist.
`performanceCounter.request_execution_time.value`|Ausführungsdauer für ASP.NET-Anforderungen|Ausführungszeit der aktuellen Anforderung.
`performanceCounter.requests_in_application_queue.value`|ASP.NET-Anforderungen in Ausführungswarteschlange|Länge der Anwendungsanforderungswarteschlange.
`performanceCounter.requests_per_sec`|ASP.NET-Anforderungsrate|Rate aller Anforderungen an die Anwendung pro Sekunde von ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Abhängigkeitsfehler|Anzahl der Aufrufe mit Fehlern von der Serveranwendung an externe Ressourcen.
`request.duration`|Serverantwortzeit|Zeit zwischen dem Empfang einer HTTP-Anforderung und dem Abschluss des Sendevorgangs der Antwort.
`request.rate`|Anforderungsrate|Rate aller Anforderungen an die Anwendung pro Sekunde.
`requestFailed.count`|Anforderungsfehler|Anzahl von HTTP-Anforderungen, die als Ergebnis Antwortcode >= 400 hatten. 
`view.count`|Seitenaufrufe|Anzahl der Clientbenutzeranforderungen für eine Webseite. Synthetischer Datenverkehr wird herausgefiltert.
{benutzerdefinierter Metrikname}|{Ihr Metrikname}|Der von [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) oder im [Messparameter eines Trackingaufrufs](app-insights-api-custom-events-metrics.md#properties) gemeldete Metrikwert.

Die Metriken werden von verschiedenen Telemetriemodulen gesendet:

Metrikgruppe | Erfassungsmodul
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>view | [Browser JavaScript](app-insights-javascript.md)
performanceCounter | [Leistung](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Abhängigkeit](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
request,<br/>requestFailed|[Serveranfrage](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)


<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[platforms]: app-insights-platforms.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO4-->