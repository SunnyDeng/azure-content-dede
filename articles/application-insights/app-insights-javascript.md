<properties
	pageTitle="Application Insights für JavaScript-Web-Apps | Microsoft Azure"
	description="Erhalten Sie die Anzahl der Seitenaufrufe und Sitzungen, rufen Sie Webclientdaten ab, und verfolgen Sie Verwendungsmuster. Erkennen Sie Ausnahmen und Leistungsprobleme in JavaScript-Web-Apps."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/19/2016"
	ms.author="awills"/>

# Application Insights für Webseiten

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Informieren Sie sich über die Leistung und Nutzung Ihrer Webseite oder App. Fügen Sie Visual Studio Application Insights Ihrem Seitenskript hinzu, um Zeitangaben zu Seitenladevorgängen und AJAX-Aufrufen, Anzahl und Details von Browserausnahmen und AJAX-Fehlern sowie die Anzahl von Benutzern und Sitzungen zu erhalten. Diese Informationen können jeweils nach Seite, Clientbetriebssystem und Browserversion, geografischer Position und anderen Dimensionen segmentiert werden. Sie können auch Warnungen für die Fehleranzahl oder das langsame Laden von Seiten festlegen.

Sie können Application Insights mit allen Webseiten verwenden. Hierfür müssen Sie lediglich einen kurzen JavaScript-Code hinzufügen. Wenn Sie als Webdienst [Java](app-insights-java-get-started.md) oder [ASP.NET](app-insights-asp-net.md) verwenden, können Sie Telemetriedaten von Ihrem Server und den Clients integrieren.

Sie benötigen ein [Microsoft Azure](https://azure.com)-Abonnement. Falls Ihr Team über ein Unternehmensabonnement verfügt, können Sie den Besitzer bitten, Ihr Microsoft-Konto hinzuzufügen. Es wird der Tarif „Free“ angeboten, sodass für die Entwicklung und Nutzung in kleinerem Umfang keine Kosten entstehen.


## Einrichten von Application Insights für Ihre Webseite

Es kann sein, dass dies bereits erledigt ist. Wenn Ihre App ein neues ASP.NET-Projekt ist und Sie in Visual Studio im Dialogfeld „Neues Projekt“ das Hinzufügen von Application Insights gewählt haben, wurde das Skript bereits hinzugefügt, und die Einrichtung ist abgeschlossen.

Andernfalls müssen Sie Ihrer Website wie folgt einen Codeausschnitt hinzufügen.

### Öffnen einer Application Insights-Ressource

Daten zur Leistung und Nutzung Ihrer Seite werden in der Application Insights-Ressource angezeigt.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

Wenn Sie bereits die Überwachung für die Serverseite der App eingerichtet haben, verfügen Sie schon über eine Ressource:

![Wählen Sie "Durchsuchen", "Entwicklerdienste", "Application Insights" aus.](./media/app-insights-javascript/01-find.png)

Wenn keine Ressource vorhanden ist, erstellen Sie sie:

![Wählen Sie "Neu", "Entwicklerdienste", Application Insights.](./media/app-insights-javascript/01-create.png)


*Schon Fragen?* [Weitere Informationen zum Erstellen einer Ressource](app-insights-create-new-resource.md).


### Hinzufügen des SDK-Skripts zu Ihrer App oder Ihren Webseiten

Rufen Sie im Schnellstart das Skript für Webseiten ab:

![Wählen Sie in Ihrer App auf dem Blatt "Übersicht" im Schnellstart die Option "Code abrufen", um Ihre Webseiten zu überwachen. Kopieren Sie das Skript.](./media/app-insights-javascript/02-monitor-web-page.png)

Fügen Sie das Skript direkt vor dem `<head>`-Tag jeder Seite ein, die Sie nachverfolgen möchten. Wenn Ihre Website über eine Masterseite verfügt, können Sie das Skript dort ablegen. Beispiel:

* Bei einem ASP.NET MVC-Projekt fügen Sie es in `View\Shared\_Layout.cshtml` ein.
* Öffnen Sie in einer SharePoint-Website in der Systemsteuerung [Websiteeinstellungen / Masterseite](app-insights-sharepoint.md).

Das Skript enthält den Instrumentationsschlüssel, der die Daten an Ihre Application Insights-Ressource leitet.

([Detailliertere Erläuterung des Skripts.](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(Wenn Sie ein bekanntes Webseitenframework verwenden, suchen Sie nach Application Insights-Adaptern. Beispielsweise gibt es [ein AngularJS-Modul](http://ngmodules.org/modules/angular-appinsights).)*


## Ausführliche Konfiguration

Es sind mehrere [Parameter](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) vorhanden, die Sie festlegen können. In den meisten Fällen sollte dies aber nicht erforderlich sein. Beispielsweise können Sie die Anzahl von Ajax-Aufrufen, die pro Seitenansicht gemeldet werden, begrenzen oder den Vorgang deaktivieren (zur Reduzierung des Datenverkehrs). Außerdem können Sie den Debugmodus festlegen, damit der Telemetrieprozess die Pipeline schnell durchläuft, ohne dass Batches erstellt werden.

Zum Festlegen dieser Parameter suchen Sie im Codeausschnitt nach dieser Zeile und fügen danach mehr kommagetrennte Elemente hinzu:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Zu den [verfügbaren Parametern](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) gehören:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>Ausführen Ihrer App

Führen Sie die Web-App aus, verwenden Sie sie eine Weile, um Telemetrie zu generieren, und warten Sie einige Sekunden. Sie können sie entweder durch Drücken von **F5** auf dem Entwicklungscomputer ausführen, oder Sie können sie veröffentlichen und von Benutzern testen lassen.

Wenn Sie die Telemetrie, die von einer Web-App an Application Insights gesendet wird, prüfen möchten, verwenden Sie dazu die Debugtools Ihres Browsers (**F12** bei vielen Browsern). Daten werden an "dc.services.visualstudio.com" gesendet.

## Untersuchen der Leistungsdaten des Browsers

Öffnen Sie das Blatt „Browser“, um die aggregierten Leistungsdaten für die Browser Ihrer Benutzer anzuzeigen.

![Öffnen Sie unter „portal.azure.com“ die Ressource Ihrer App, und klicken Sie auf „Einstellungen“ > „Browser“.](./media/app-insights-javascript/03.png)


*Noch keine Daten verfügbar? Klicken Sie am oberen Seitenrand auf **Aktualisieren**. Immer noch nichts? Informationen hierzu finden Sie unter [Problembehandlung](app-insights-troubleshoot-faq.md).*

Das Blatt „Browser“ ist ein [Metrik-Explorer-Blatt](app-insights-metrics-explorer.md) mit voreingestellter Filter- und Diagrammauswahl. Sie können bei Bedarf den Zeitbereich, die Filter und die Diagrammkonfiguration ändern und das Ergebnis als Favorit speichern. Klicken Sie auf **Standard wiederherstellen**, um zur ursprünglichen Blattkonfiguration zurückzukehren.

## Leistung des Seitenladevorgangs

Oben befindet sich ein segmentiertes Diagramm mit Seitenladezeiten. Die Gesamthöhe des Diagramms steht für die durchschnittliche Dauer, die zum Laden und Anzeigen von Seiten aus Ihrer App in den Browsern Ihrer Benutzer benötigt wird. Die Dauer wird ab dem Punkt gemessen, an dem der Browser die erste HTTP-Anforderung sendet, bis zu dem Punkt, an dem alle synchronen Ladeereignisse verarbeitet wurden, einschließlich Layout und ausgeführte Skripts. Hierin sind keine asynchronen Aufgaben enthalten, z. B. das Laden von Webparts aus AJAX-Aufrufen.

Im Diagramm werden die Gesamtdauern der Seitenladevorgänge in die [vom W3C definierten Standardzeiten](http://www.w3.org/TR/navigation-timing/#processing-model) segmentiert.

![](./media/app-insights-javascript/08-client-split.png)

Beachten Sie, dass die Zeit für die *Netzwerkverbindung* häufig geringer als erwartet ist, da es sich um einen Durchschnittswert über alle Anforderungen vom Browser an den Server handelt. Viele einzelne Anforderungen haben eine Verbindungszeit von 0, da bereits eine aktive Verbindung mit dem Server besteht.

### Dauert der Ladevorgang lange?

Das langsame Laden von Seiten ist für Ihre Benutzer ein wichtiger Auslöser von Frust. Wenn im Diagramm auf das langsame Laden von Seiten hingewiesen wird, können Sie mit wenig Aufwand Diagnoseschritte ausführen.

Im Diagramm wird der Durchschnitt aller Seitenladevorgänge Ihrer App angezeigt. Um festzustellen, ob das Problem auf bestimmte Seiten beschränkt ist, können Sie weiter unten auf dem Blatt forschen. Dort ist ein Raster angegeben, das nach Seiten-URL segmentiert ist:

![](./media/app-insights-javascript/09-page-perf.png)

Beachten Sie die Anzahl der Seitenaufrufe und die Standardabweichung. Wenn die Seitenanzahl sehr niedrig ist, stellt das Problem keine große Beeinträchtigung für die Benutzer dar. Eine hohe Standardabweichung (vergleichbar mit dem Durchschnitt) deutet darauf hin, dass die einzelnen Messungen stark variieren.

**Vergrößern Sie eine URL und einen Seitenaufruf.** Klicken Sie auf einen beliebigen Seitennamen, um ein Blatt mit Browserdiagrammen anzuzeigen, für die ein Filter nur für diese URL angewendet wurde. Klicken Sie dann auf eine Instanz eines Seitenaufrufs.

![](./media/app-insights-javascript/35.png)

Klicken Sie auf `...`, um eine Liste mit allen Eigenschaften für das Ereignis anzuzeigen, oder untersuchen Sie die Ajax-Aufrufe und die dazugehörigen Ereignisse. Langsame Ajax-Aufrufe beeinträchtigen die Gesamtdauer der Seitenladevorgänge, wenn sie synchron erfolgen. Zu den verwandten Ereignissen gehören Serveranforderungen für dieselbe URL (wenn Sie Application Insights auf Ihrem Webserver eingerichtet haben).


**Seitenleistung in Abhängigkeit der Zeit:** Wechseln Sie wieder zum Blatt „Browser“, und ändern Sie das Raster für die „Dauer der Seitenansicht“ in ein Liniendiagramm, um zu ermitteln, ob zu bestimmten Zeiten Spitzen aufgetreten sind:

![Klicken Sie auf den oberen Bereich des Rasters, und wählen Sie einen neuen Diagrammtyp aus.](./media/app-insights-javascript/10-page-perf-area.png)

**Segmentieren nach anderen Dimensionen:** Es kann beispielsweise sein, dass Ihre Seiten für einen bestimmten Browser, ein Clientbetriebssystem oder einen Benutzerstandort langsamer geladen werden. Fügen Sie ein neues Diagramm hinzu, und experimentieren Sie mit der Dimension **Group-by**.

![](./media/app-insights-javascript/21.png)


## AJAX-Leistung

Stellen Sie sicher, dass alle AJAX-Aufrufe auf Ihren Webseiten eine gute Leistung aufweisen. Sie werden häufig genutzt, um Teile Ihrer Seite asynchron zu füllen. Auch wenn die Gesamtseite vielleicht schnell geladen wird, können Ihre Benutzer frustriert sein, wenn sie mit leeren Webparts konfrontiert werden und warten müssen, bis darin Daten angezeigt werden.

AJAX-Aufrufe, die über Ihre Webseite erfolgen, werden auf dem Blatt „Browser“ als Abhängigkeiten angezeigt.

Dies sind Zusammenfassungsdiagramme im oberen Teil des Blatts:

![](./media/app-insights-javascript/31.png)

Weiter unten werden ausführliche Raster angezeigt:

![](./media/app-insights-javascript/33.png)

Klicken Sie auf eine beliebige Zeile, um besondere Details anzuzeigen.


> [AZURE.NOTE] Wenn Sie den Filter „Browser“ auf dem Blatt löschen, werden sowohl die Serverabhängigkeiten als auch die AJAX-Abhängigkeiten in diese Diagramme eingebunden. Klicken Sie auf „Standard wiederherstellen“, um den Filter neu zu konfigurieren.

Führen Sie zum **Ausführen eines Drilldowns für fehlgeschlagene Ajax-Aufrufe ** einen Bildlauf nach unten zum Raster mit den Abhängigkeitsfehlern aus, und klicken Sie anschließend auf eine Zeile, um bestimmte Instanzen anzuzeigen.

![](./media/app-insights-javascript/37.png)

Klicken Sie auf `...`, um die gesamten Telemetriedaten für einen Ajax-Aufruf anzuzeigen.

### Es wurden keine Ajax-Aufrufe gemeldet?

Zu Ajax-Aufrufen gehören sämtliche HTTP-Aufrufe, die vom Skript für Ihre Webseite durchgeführt werden. Falls keine Aufrufe gemeldet werden, sollten Sie sicherstellen, dass im Codeausschnitt die [Parameter](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableAjaxTracking` und `maxAjaxCallsPerView` nicht festgelegt sind.

## Browserausnahmen

Auf dem Blatt „Browser“ befindet sich ein Übersichtsdiagramm zu Ausnahmen und weiter unten auf dem Blatt ein Raster mit Ausnahmetypen.

![](./media/app-insights-javascript/39.png)

Falls keine gemeldeten Browserausnahmen angezeigt werden, sollten Sie sicherstellen, dass im Codeausschnitt der [Parameter](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) `disableExceptionTracking` nicht festgelegt ist.

## Überprüfen einzelner Seitenaufruf-Ereignisse

Normalerweise wird die Seitenaufruf-Telemetrie von Application Insights analysiert, und Sie erhalten nur kumulative Berichte, gemittelt über alle Benutzer. Sie können jedoch zu Debugging-Zwecken auch einzelne Seitenaufrufereignisse anzeigen.

Legen Sie im Blatt "Diagnosesuche" als Filter die Einstellung "Seitenansicht" fest.

![](./media/app-insights-javascript/12-search-pages.png)

Wählen Sie ein Ereignis, um weitere Details anzuzeigen. Klicken Sie auf der Detailseite auf "...", um weitere Details anzuzeigen.

> [AZURE.NOTE] Beachten Sie bei Verwendung von [Search](app-insights-diagnostic-search.md), dass Sie ganze Wörter suchen müssen: "Info" und "nfo" stimmen nicht mit "Informationen" überein, "Info*" hingegen schon. Ein Suchbegriff darf nicht mit einem Platzhalterzeichen beginnen. Beispielsweise ergibt die Suche nach „*bou“ nicht „About“.

> [Erfahren Sie mehr über Diagnosesuche](app-insights-diagnostic-search.md)

### Eigenschaften von Seitenansichten

* **Dauer der Seitenansicht**: Die Zeit, die das Laden der Seite und das Starten der Ausführung von Skripts in Anspruch nimmt. Insbesondere das Intervall vom Beginn des Ladens der Seite bis zur Ausführung von trackPageView. Wenn Sie nach der Initialisierung des Skripts trackPageView von seiner üblichen Position verschoben haben, wird ein anderer Wert wiedergegeben.


## Benutzerdefinierte Seitenzähler

Standardmäßig wird jedes Mal ein Seitenzähler angezeigt, wenn eine neue Seite in den Client-Browser geladen wird. Sie möchten jedoch ggf. zusätzliche Seitenaufrufe zählen. Beispielsweise könnte eine Seite in Registerkarten gegliedert sein, und Sie möchten, dass jeder Wechsel von Registerkarten als Aufruf gezählt wird. Auf der Seite könnte auch neuer Inhalt durch JavaScript-Code geladen werden, ohne dass sich die Browser-URL ändert.

Fügen Sie an geeigneter Stelle in Ihren Client-Code einen JavaScript-Aufruf wie diesen ein:

    appInsights.trackPageView(myPageName);

Der Seitenname kann die gleichen Zeichen wie eine URL enthalten, allerdings wird alles nach den Zeichen „#“ oder „?“ ignoriert.



## Nutzungsverfolgung


Möchten Sie herausfinden, wofür die Benutzer Ihre App verwenden?

* [Weitere Informationen zur Nutzungsnachverfolgung](app-insights-web-track-usage.md)
* [Informationen zur API für benutzerdefinierte Ereignisse und Metriken](app-insights-api-custom-events-metrics.md).


#### <a name="video"></a>Video: Nachverfolgen der Nutzung

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Nächste Schritte

* [Nutzung nachverfolgen](app-insights-web-track-usage.md)
* [Benutzerdefinierte Ereignisse und Metriken](app-insights-api-custom-events-metrics.md)
* [Erstellen-Messen-Lernen](app-insights-overview-usage.md)

<!---HONumber=AcomDC_0224_2016-->