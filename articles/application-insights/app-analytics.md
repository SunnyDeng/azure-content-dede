<properties 
	pageTitle="Analytics, das leistungsfähige Suchtool von Application Insights" 
	description="Übersicht über Analytics, das leistungsfähige Diagnosesuchtool von Application Insights." 
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
	ms.date="03/21/2016" 
	ms.author="awills"/>




# Analytics in Application Insights


[Analytics](app-analytics.md) ist die leistungsfähige Suchfunktion von [Application Insights](app-insights-overview.md). Auf diesen Seiten wird die Analytics-Abfragesprache beschrieben.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Eine übliche Abfrage ist eine *Quelle*-Tabelle, gefolgt von einer Reihe von *Operatoren*, die durch `|` getrennt sind.

Finden wir also heraus, zu welcher Tageszeit die Einwohner von Hyderabad unsere Web-App testen. Und während wir damit beschäftigt sind, sehen wir, welche Ergebniscodes an ihre HTTP-Anfragen zurückgegeben werden.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Wir zählen verschiedene Client-IP-Adresse der letzten 30 Tage auf und gruppieren sie nach Uhrzeit.

Wir zeigen die Ergebnisse mit der Balkendiagramm-Präsentation und listen die Ergebnisse von verschiedenen Antwortcodes auf.

![Wählen Sie das Balkendiagramm, die X-Achsen und Y-Achsen, dann die Segmentierung](./media/app-analytics/020.png)

Anscheinend ist unserer App zur Mittagszeit und zur Schlafenszeit in Hyderabad am beliebtesten. (Und wir sollten wir diese 500 Codes untersuchen.)


Es gibt auch leistungsstarke statistische Vorgänge:

![](./media/app-analytics/025.png)


Die Sprache verfügt über viele attraktive Features:

* [Filtern](app-analytics-queries.md) Ihrer rohen App-Telemetrie nach beliebigen Feldern, einschließlich Ihrer benutzerdefinierten Eigenschaften und Metriken.
* [Verbinden](app-analytics-queries.md#join-operator) mehrere Tabellen – Korrelation von Anforderungen mit Seitenansichten, Aufrufen von Abhängigkeiten, Ausnahmen und Protokollablaufverfolgungen.
* Leistungsstarke statistische [Aggregationen](app-analytics-aggregations.md).
* Genauso leistungsstark wie SQL, aber viel einfacher für komplexe Abfragen: anstelle der Schachtelung von Anweisungen übergeben Sie die Daten aus einem elementaren Vorgang an den nächsten.
* Sofortige und leistungsfähige Visualisierungen.



>[AZURE.NOTE] Es wird empfohlen, mit der [Einführung in die Abfragesprache](app-analytics-tour.md) anzufangen.


## Verbinden mit Ihren Application Insights-Daten


Öffnen Sie die Analyse über das [Blatt „Übersicht“](app-insights-dashboards.md) Ihrer App in Application Insights:

![Öffnen Sie unter „portal.azure.com“ die Application Insights-Ressource, und wählen Sie „Analytics“.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->