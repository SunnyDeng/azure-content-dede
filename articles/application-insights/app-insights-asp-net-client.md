<properties 
	pageTitle="Abhängigkeitsüberwachung in Application Insights" 
	description="Analysieren Sie die Auslastung, Verfügbarkeit und Leistung Ihrer lokalen oder Microsoft Azure-Webanwendung mit Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Einrichten von Application Insights: Webseiten


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Informieren Sie sich über die Leistung und Nutzung Ihrer Webseiten. Fügen Sie Ihren Seiten Visual Studio Application Insights hinzu, und Sie erfahren, wie viele Benutzer die Seite besuchen, wie oft sie zurückkehren und welche Seiten sie am häufigsten verwenden. Außerdem erhalten Sie Berichte zu Ladezeiten und Ausnahmen. Fügen Sie einige [benutzerdefinierte Ereignisse und Metriken][api] hinzu, um die beliebtesten Features und die häufigsten Fehler im Detail zu analysieren und Ihre Seite für den Erfolg bei den Benutzern zu optimieren.

![Wählen Sie "Neu", "Entwicklerdienste", Application Insights.](./media/app-insights-asp-net-client/16-page-views.png)

Wenn Sie bereits Servertelemetrie für Ihre [ASP.NET](app-insights-asp-net.md)- oder [Java](app-insights-java-get-started.md)-Web-App eingerichtet haben, erhalten Sie einen Überblick sowohl aus der Sicht des Clients als auch aus der des Servers. Die beiden Datenströme werden in das Application Insights-Portal integriert.

## Öffnen der Application Insights-Ressource

Melden Sie sich beim [Azure-Portal](http://portal.azure.com) an.

Wenn Sie bereits die Überwachung für die Serverseite der App eingerichtet haben, verfügen Sie schon über eine Ressource:

![Wählen Sie "Durchsuchen", "Entwicklerdienste", "Application Insights" aus.](./media/app-insights-asp-net-client/01-find.png)

Wenn keine Ressource vorhanden ist, erstellen Sie sie:

![Wählen Sie "Neu", "Entwicklerdienste", Application Insights.](./media/app-insights-asp-net-client/01-create.png)


## Hinzufügen des SDK-Skripts zu Ihrer App oder Ihren Webseiten

Rufen Sie im Schnellstart das Skript für Webseiten ab:

![Wählen Sie in Ihrer App auf dem Blatt "Übersicht" im Schnellstart die Option "Code abrufen", um Ihre Webseiten zu überwachen. Kopieren Sie das Skript.](./media/app-insights-asp-net-client/02-monitor-web-page.png)

Fügen Sie das Skript direkt vor dem &lt;/head&gt;-Tag jeder Seite ein, die Sie nachverfolgen möchten. Wenn Ihre Website über eine Masterseite verfügt, können Sie das Skript dort ablegen. Beispiel:

* In einem ASP.NET MVC-Projekt würden Sie es unter "View\\Shared\\_Layout.cshtml" ablegen.
* Öffnen Sie in einer SharePoint-Website in der Systemsteuerung [Websiteeinstellungen / Masterseite](app-insights-sharepoint.md).

Das Skript enthält den Instrumentationsschlüssel, der die Daten an Ihre Application Insights-Ressource leitet.

*(Wenn Sie ein bekanntes Webseitenframework verwenden, suchen Sie nach Application Insights-Adaptern. Beispielsweise gibt es [ein AngularJS-Modul](http://ngmodules.org/modules/angular-appinsights).)*


## <a name="run"></a>Ausführen Ihrer App

Führen Sie die Web-App aus, verwenden Sie sie eine Weile, um Telemetrie zu generieren, und warten Sie einige Sekunden. Sie können sie entweder durch Drücken von **F5** auf dem Entwicklungscomputer ausführen, oder Sie können sie veröffentlichen und von Benutzern testen lassen.

Wenn Sie die Telemetrie, die von einer Web-App an Application Insights gesendet wird, prüfen möchten, verwenden Sie dazu die Debugtools Ihres Browsers (**F12** bei vielen Browsern). Daten werden an "dc.services.visualstudio.com" gesendet.

## Untersuchen Ihrer Daten

Im Blatt mit der Anwendungsübersicht befindet sich im oberen Bereich ein Diagramm, das die durchschnittliche Zeit für das Laden von Seiten im Browser anzeigt:


![](./media/app-insights-asp-net-client/05-browser-page-load.png)


*Noch keine Daten verfügbar? Klicken Sie am oberen Seitenrand auf **Aktualisieren**. Immer noch nichts? Informationen hierzu finden Sie unter [Problembehandlung][qna].*

Klicken Sie auf dieses Diagramm, und Sie erhalten eine ausführlichere Version:

![](./media/app-insights-asp-net-client/07-client-perf.png)

Hierbei handelt es sich um ein gestapeltes Diagramm, das die Gesamtzeit zum Laden der Seite in die [von W3C definierten Standardintervalle](http://www.w3.org/TR/navigation-timing/#processing-model) unterteilt.

![](./media/app-insights-asp-net-client/08-client-split.png)

Beachten Sie, dass die Zeit für die *Netzwerkverbindung* in der Regel geringer ist als Sie erwarten würden, da es sich um einen Durchschnittswert über alle Anforderungen vom Browser an den Server handelt. Viele einzelne Anforderungen haben eine Verbindungszeit von 0, da bereits eine aktive Verbindung mit dem Server besteht.


### Leistung nach Seite

Weiter unten auf dem Blatt "Details" finden Sie ein Raster, das nach Seiten-URL segmentiert ist:


![](./media/app-insights-asp-net-client/09-page-perf.png)

Wenn Sie die Leistung der Seiten im Zeitverlauf anzeigen möchten, doppelklicken Sie auf das Raster, und ändern Sie dessen Diagrammtyp:

![](./media/app-insights-asp-net-client/10-page-perf-area.png)

## Übersicht über die Clientnutzung

Klicken Sie auf dem Blatt "Übersicht" auf **Nutzung**:

![](./media/app-insights-asp-net-client/14-usage.png)

* **Benutzer:** Die Anzahl der unterschiedlichen Benutzer über den Zeitbereich des Diagramms. (Wiederkehrende Benutzer werden mithilfe von Cookies identifiziert.)
* **Sitzungen:** Eine Sitzung wird gezählt, wenn ein Benutzer seit 30 Minuten keine Anforderungen gestellt hat.
* **Seitenaufrufe** Zählt die Anzahl der Aufrufe von trackPageView(), wird in der Regel einmal auf jeder Webseite aufgerufen.


### Für mehr Details durchklicken

Klicken Sie auf ein Diagramm, um weitere Details anzuzeigen. Beachten Sie, dass Sie den Zeitraum der Diagramme ändern können.

![](./media/app-insights-asp-net-client/appinsights-49usage.png)


Klicken Sie auf ein Diagramm, um weitere Metriken anzuzeigen, die dargestellt werden können, oder fügen Sie ein neues Diagramm hinzu, und wählen Sie die anzuzeigenden Metriken aus.

![](./media/app-insights-asp-net-client/appinsights-63usermetrics.png)

> [AZURE.NOTE]Metriken können nur in einigen Kombinationen angezeigt werden. Wenn Sie eine Metrik auswählen, werden die inkompatiblen deaktiviert.



## Benutzerdefinierte Seitenzähler

Standardmäßig wird jedes Mal ein Seitenzähler angezeigt, wenn eine neue Seite in den Client-Browser geladen wird. Sie möchten jedoch ggf. zusätzliche Seitenaufrufe zählen. Beispielsweise könnte eine Seite in Registerkarten gegliedert sein, und Sie möchten, dass jeder Wechsel von Registerkarten als Aufruf gezählt wird. Auf der Seite könnte auch neuer Inhalt durch JavaScript-Code geladen werden, ohne dass sich die Browser-URL ändert.

Fügen Sie an geeigneter Stelle in Ihren Client-Code einen JavaScript-Aufruf wie diesen ein:

    appInsights.trackPageView(myPageName);

Der Seitenname kann die gleichen Zeichen wie eine URL enthalten, allerdings wird alles nach den Zeichen „#“ oder „?“ ignoriert.


## Überprüfen einzelner Seitenaufruf-Ereignisse

Normalerweise wird die Seitenaufruf-Telemetrie von Application Insights analysiert, und Sie erhalten nur kumulative Berichte, gemittelt über alle Benutzer. Sie können jedoch zu Debugging-Zwecken auch einzelne Seitenaufrufereignisse anzeigen.

Legen Sie im Blatt "Diagnosesuche" als Filter die Einstellung "Seitenansicht" fest.

![](./media/app-insights-asp-net-client/12-search-pages.png)

Wählen Sie ein Ereignis, um weitere Details anzuzeigen. Klicken Sie auf der Detailseite auf "...", um weitere Details anzuzeigen.

> [AZURE.NOTE]Beachten Sie bei Verwendung von [Search][diagnostic], dass Sie ganze Wörter suchen müssen: "Info" und "nfo" stimmen nicht mit "Informationen" überein, "Info*" hingegen schon. Ein Suchbegriff darf nicht mit einem Platzhalterzeichen beginnen. Beispielsweise ergibt die Suche nach „*bou“ nicht „About“.

> [Erfahren Sie mehr über Diagnosesuche][diagnostic]

### Eigenschaften von Seitenansichten

* **Dauer der Seitenansicht**: Die Zeit, die das Laden der Seite und das Starten der Ausführung von Skripts in Anspruch nimmt. Insbesondere das Intervall vom Beginn des Ladens der Seite bis zur Ausführung von trackPageView. Wenn Sie nach der Initialisierung des Skripts trackPageView von seiner üblichen Position verschoben haben, wird ein anderer Wert wiedergegeben.

## Benutzerdefinierte Nutzungsverfolgung

Möchten Sie herausfinden, wofür die Benutzer Ihre App verwenden? Durch Einfügen von Aufrufen in den Client- und Servercode können Sie Ihre eigenen Telemetriedaten an Application Insights senden. Beispielsweise können Sie die Anzahl der Benutzer ermitteln, die Aufträge erstellen, ohne sie abzuschließen, oder welche Validierungsfehler am häufigsten auftreten oder was die durchschnittliche Punktzahl in einem Spiel ist.

* [Erhalten Sie weitere Informationen zur API für benutzerdefinierte Ereignisse und Metriken][api].
* [API-Referenz](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)




## <a name="video"></a>Video: Nachverfolgen der Nutzung

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Nächste Schritte

- [Installieren des SDKs](../article/application-insights/app-insights-asp-net.md#selector1)
- [Nutzungsverfolgung mit benutzerdefinierten Ereignissen und Metriken][api]
- [Verfügbarkeit](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)
- [Bereits online geschaltete App](../article/application-insights/app-insights-monitor-performance-live-website-now.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-asp-net-client.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


 

<!---HONumber=Nov15_HO4-->