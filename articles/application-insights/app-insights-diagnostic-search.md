<properties 
	pageTitle="Diagnosesuche öffnen" 
	description="Suchen und Filtern Sie einzelne Ereignisse, Anforderungen und Protokollablaufverfolgungen." 
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
	ms.date="01/26/2016" 
	ms.author="awills"/>
 
# Verwenden der Diagnosesuche in Application Insights

"Diagnosesuche" ist das Blatt in [Application Insights][start], auf dem Sie einzelne Telemetrieelemente wie Seitenaufrufe, Ausnahmen und Webanforderungen suchen und untersuchen. Außerdem können Sie Protokollablaufverfolgungen und Ereignisse anzeigen, die Sie programmiert haben.

## Wann wird die Diagnosesuche angezeigt?


### Im Azure-Portal

Sie können die Diagnosesuche explizit öffnen:

![Diagnosesuche öffnen](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


Sie wird auch geöffnet, wenn Sie sich durch einige Diagramme und Rasterelemente klicken. In diesem Fall werden ihre Filter so voreingestellt, dass der Fokus auf dem ausgewählten Elementtyp liegt.

Wenn Ihre Anwendung z. B. ein Webdienst ist, zeigt das Blatt "Übersicht" ein Diagramm des Anforderungsvolumens. Wenn Sie darauf klicken, erhalten ein detaillierteres Diagramm mit einer Liste, die zeigt, wie viele Anforderungen für jede URL erfolgt sind. Klicken Sie auf eine beliebige Zeile, um eine Liste der einzelnen Anforderungen für diese URL zu erhalten:

![Diagnosesuche öffnen](./media/app-insights-diagnostic-search/07-open-from-filters.png)


Den Hauptteil der Diagnosesuche bildet eine Liste von Telemetrieelementen: Serveranforderungen, Seitenaufrufe, benutzerdefinierte Ereignisse, die Sie programmiert haben, usw. Am Anfang der Liste befindet sich ein Übersichtsdiagramm, das die Anzahl der Ereignisse im Zeitablauf zeigt.

Ereignisse werden in der Regel in der Diagnosesuche angezeigt, bevor sie in Metrik-Explorer angezeigt werden. Obwohl sich das Blatt regelmäßig selbst aktualisiert, können Sie auf "Aktualisieren" klicken, wenn Sie auf ein bestimmtes Ereignis warten.


### In Visual Studio

Öffnen Sie das Suchfenster in Visual Studio:

![](./media/app-insights-diagnostic-search/32.png)

Das Suchfenster bietet dieselben Features wie das Webportal:

![](./media/app-insights-diagnostic-search/34.png)


### Stichproben

Wenn die Anwendung viele Telemetriedaten generiert (und Sie Version 2.0.0-beta3 oder höher des ASP.NET-SDK verwenden), reduziert das adaptive Stichprobenmodul automatisch die an das Portal gesendete Datenmenge, indem nur ein repräsentativer Bruchteil der Ereignisse gesendet wird. Ereignisse, die mit derselben Anforderung im Zusammenhang stehen, werden als Gruppe aus- oder abgewählt, sodass Sie zwischen verwandten Ereignissen navigieren können.
> [Erfahren Sie mehr über das Erstellen von Stichproben](app-insights-sampling.md).


## Überprüfen einzelner Elemente

Wählen Sie ein beliebiges Telemetrieelement aus, um Schlüsselfelder und verwandte Elemente anzuzeigen. Wenn Sie alle Felder anzeigen möchten, klicken Sie auf "...".

![Diagnosesuche öffnen](./media/app-insights-diagnostic-search/10-detail.png)

Um alle Felder zu finden, verwenden Sie einfache Zeichenfolgen (ohne Platzhalterzeichen). Die verfügbaren Felder hängen vom Typ der Telemetrie ab.

## Filtern von Ereignistypen

Öffnen Sie das Blatt "Filter", und wählen Sie die Ereignistypen, die Sie anzeigen möchten. (Wenn Sie später die Filter wiederherstellen möchten, mit denen Sie das Blatt geöffnet haben, klicken Sie auf "Zurücksetzen".)


![Wählen Sie "Filter" und dann Telemetrietypen](./media/app-insights-diagnostic-search/02-filter-req.png)


Die Ereignistypen sind:

* **Ablaufverfolgung** – Diagnoseprotokolle, einschließlich TrackTrace, log4Net, NLog und Aufrufe von "System.Diagnostic.Trace".
* **Anforderung** – Von Ihrer Serveranwendung empfangene HTTP-Anforderungen, einschließlich Seiten, Skripts, Bilder, Formatdateien und Daten. Diese Ereignisse werden verwendet, um die Übersichtsdiagramme für Anforderungen und Antworten zu erstellen.
* **Seitenaufruf** – Vom Webclient gesendete Telemetriedaten, die zum Erstellen von Seitenaufrufberichten verwendet werden. 
* **Benutzerdefiniertes Ereignis** – Wenn Sie Aufrufe von "TrackEvent()" zum [Überwachen der Nutzung][track] eingefügt haben, können Sie diese hier suchen.
* **Ausnahme** – Nicht vom Server abgefangene Ausnahmen und Ausnahmen, die Sie mit "TrackException()" protokollieren können.

## Filtern von Eigenschaftswerten

Sie können Ereignisse anhand der Werte ihrer Eigenschaften filtern. Die verfügbaren Eigenschaften hängen von den ausgewählten Ereignistypen ab.

Wählen Sie z. B. Anforderungen mit einem bestimmten Antwortcode aus.

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/app-insights-diagnostic-search/03-response500.png)

Das Auswählen keiner Werte einer bestimmten Eigenschaft hat dieselbe Wirkung wie das Auswählen aller Werte, nämlich dass das Filtern anhand dieser Eigenschaft deaktiviert wird.


### Eingrenzen der Suche

Beachten Sie, dass die Zahlen rechts neben den Filterwerten anzeigen, wie viele Vorkommen es in der aktuell gefilterten Gruppe gibt.

In diesem Beispiel ist es klar, dass die Anforderung `Reports/Employees` die Mehrzahl der 500-er Fehler ausmacht:

![Erweitern Sie eine Eigenschaft, und wählen Sie einen Wert](./media/app-insights-diagnostic-search/04-failingReq.png)

Wenn Sie darüber hinaus auch anzeigen möchten, welche andere Ereignisse in diesem Zeitraum aufgetreten sind, können Sie **Ereignisse mit nicht definierten Eigenschaften einbeziehen** aktivieren.

## Entfernen von Robot- und Webtest-Datenverkehr

Verwenden Sie den Filter **Realer oder synthetischer Datenverkehr**, und aktivieren Sie **Real**.

Sie können auch nach **Quelle von synthetischem Datenverkehr** filtern.

## Überprüfen einzelner Vorkommen

Fügen Sie den Namen der gewünschten Anforderung der Filtergruppe hinzu, um anschließend einzelne Vorkommen dieses Ereignisses zu überprüfen.

![Wählen Sie einen Wert](./media/app-insights-diagnostic-search/05-reqDetails.png)

Bei Anforderungsereignissen enthalten die Details Ausnahmen, die während der Verarbeitung der Anforderung aufgetreten sind.

Klicken Sie auf eine Ausnahme, um deren Details anzuzeigen, einschließlich der Stapelüberwachung.

![Klicken Sie auf eine Ausnahme](./media/app-insights-diagnostic-search/06-callStack.png)

## Suchen von Ereignissen mit der gleichen Eigenschaft

Suchen Sie alle Elemente mit dem gleichen Eigenschaftswert:

![Klicken Sie mit der rechten Maustaste auf eine Eigenschaft](./media/app-insights-diagnostic-search/12-samevalue.png)

## Suchen nach metrischem Wert

Rufen Sie alle Anforderungen mit einer Antwortzeit > 5 Sek. ab. Zeiten werden mithilfe von Teilstrichen dargestellt: 10.000 Teilstriche = 1 ms.

!["Antwortzeit":(Schwellenwert AUF *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## Durchsuchen der Daten

Sie können alle Eigenschaftswerte nach Ausdrücken durchsuchen. Dies ist besonders nützlich, wenn Sie [benutzerdefinierte Ereignisse][track] mit Eigenschaftswerten geschrieben haben.

Sie können auch einen Zeitbereich festlegen, da die Suche in einem kürzeren Bereich schneller erfolgt.

![Diagnosesuche öffnen](./media/app-insights-diagnostic-search/appinsights-311search.png)

Suchen Sie nach Begriffen, nicht nach Teilzeichenfolgen. Begriffe sind alphanumerische Zeichenfolgen, die Zeichen wie '.' und '\_' enthalten können. Beispiel:

Begriff|stimmt *nicht* überein mit|stimmt überein mit
---|---|---
HomeController.Info|info<br/>home|h*info<br/>home*
IstLokal|lokal<br/>ist<br/>*lokal|istl*<br/>istlokal<br/>i*l*
Neue Stornierung|e s|neue<br/>stornierung<br/>n* AND s*


Dies sind einige Suchausdrücke, die Sie verwenden können:

Beispielabfrage | Effekt 
---|---
langsam|Findet alle Ereignisse im Datumsbereich, deren Felder den Begriff „langsam“ enthalten
Datenbank??|Stimmt mit „Datenbank01“, „DatenbankAB“, ... überein<br/>? ist am Anfang eines Suchbegriffs nicht zulässig.
Datenbank*|Datenbank, Datenbank01, DatenbankNNNN entspricht<br/> ist am Anfang eines Suchbegriffs nicht zulässig.
Apfel UND Banane|Findet Ereignisse, die beide Begriffe enthalten. Verwenden Sie „AND“ in Großbuchstaben, nicht „and“.
Apfel OR Banane<br/>Apfel Banane|Findet Ereignisse, die einen der beiden Begriffe enthalten. Verwenden Sie "OR" nicht "or".</br/>Kurzform.
Apfel NOT Banane<br/>Apfel -Banane|Findet Ereignisse, die den einen Begriff enthalten, aber nicht den anderen.<br/>Kurzform.
Apf* AND Banane -(Traube Birne)|Logische Operatoren und Klammern.
"Metrik": 0 BIS 500<br/>"Metrik": 500 BIS * | Suchen Sie Ereignisse, die die benannte Messung im Wertebereich enthalten.


## Speichern der Suche

Wenn Sie alle gewünschten Filter festgelegt haben, können Sie die Suche als Favoriten speichern. Wenn Sie mit einem Organisationskonto arbeiten, können Sie wählen, ob Sie sie für andere Teammitglieder freigeben.

![Klicken Sie auf "Favoriten", legen Sie den Namen fest, und klicken Sie auf "Speichern"](./media/app-insights-diagnostic-search/08-favorite-save.png)


Um die Suche erneut zu verwenden, **wechseln Sie zum Blatt "Übersicht"** und öffnen "Favoriten":

![Kachel "Favoriten"](./media/app-insights-diagnostic-search/09-favorite-get.png)

Wenn Sie mit dem Zeitraum "Relativ" gespeichert haben, enthält das neu geöffnete Blatt die neuesten Daten. Wenn Sie mit dem Zeitraum "Absolut" gespeichert haben, werden jedes Mal dieselben Daten gezeigt.


## Senden weiterer Telemetriedaten an Application Insights

Neben der standardmäßig vom Application Insights SDK gesendeten Telemetriedaten können Sie folgende Aktionen ausführen:

* Erfassen von Protokollablaufverfolgungen mithilfe Ihres bevorzugten Protokollierungsframeworks in [.NET][netlogs] oder [Java][javalogs]. Das heißt, dass Sie Ihre Protokollablaufverfolgungen durchsuchen und mit Seitenaufrufen, Ausnahmen und anderen Ereignissen korrelieren können. 
* [Schreiben von Code][track] zum Senden benutzerdefinierter Ereignisse, Seitenaufrufe und Ausnahmen. 

[Erfahren Sie, wie Sie Protokolle und benutzerdefinierte Telemetrie an Application Insights senden können][trace].


## <a name="questions"></a>FRAGEN UND ANTWORTEN

### <a name="limits"></a>Wie viele Daten werden beibehalten?

Bis zu 500 Ereignisse pro Sekunde für jede Anwendung. Ereignisse werden sieben Tage lang aufbewahrt.

### Wie kann ich die POST-Daten in meinen Serveranforderungen anzeigen?

POST-Daten werden nicht automatisch protokolliert. Sie können jedoch ["TrackTrace" oder Protokollaufrufe][trace] verwenden. Fügen Sie die POST-Daten in den "message"-Parameter ein. Sie können die Nachricht nicht wie Eigenschaften filtern, aber dafür ist die Größenbeschränkung höher.

## <a name="add"></a>Nächste Schritte

* [Senden von Protokollen und benutzerdefinierter Telemetrie an Application Insights][trace]
* [Einrichten von Tests der Verfügbarkeit und Reaktionsfähigkeit][availability]
* [Problembehandlung][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-spi-custom-events-metrics.md

 

<!---HONumber=AcomDC_0128_2016-->