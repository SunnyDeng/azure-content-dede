<properties 
	pageTitle="Application Analytics - das leistungsfähige Suchtool für Application Insights" 
	description="Übersicht über Application Analytics, dem leistungsfähigen Suchtool für Application Insights." 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>




# Application Insights Analyse: Überblick über die Abfragesprache


[Application Insights-Analyse](app-analytics.md) ist ein leistungsfähiges Abfragemodul für Ihre [Application Insights](app-insights-overview.md)-Telemetrie. Auf diesen Seiten wird die Abfragesprache von Application Insights, AIQL, beschrieben.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Eine übliche AIQL-Abfrage ist eine *Quelle*-Tabelle, gefolgt von einer Reihe von *Operatoren*, die durch `|` getrennt sind.

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

Die Sprache verfügt über viele der Funktionen von SQL und noch vieles mehr. Wie bei SQL können Sie Daten filtern, Datensätze gruppieren sowie Tabellen sortieren und verknüpfen. Sie können auch Berechnungen für die Felder ausführen. Im Gegensatz zu SQL werden diese Funktionen in verschiedene Vorgänge unterteilt, und statt Abfragen zu verschachteln, übergeben Sie die Daten aus einem Vorgang ganz intuitiv zum nächsten. Dies erleichtert es, komplexe Abfragen zu schreiben.


>[AZURE.NOTE] Es wird empfohlen, mit der [Einführung in die Abfragesprache](app-analytics-tour.md) anzufangen.


## Verbinden mit Ihren Application Insights-Daten


Öffnen Sie Analytics auf dem [Blatt „Übersicht“](app-insights-dashboards.md) Ihrer App in Application Insights:

![Öffnen Sie unter „portal.azure.com“ die Application Insights-Ressource, und wählen Sie „Analytics“.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->