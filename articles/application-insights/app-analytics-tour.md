<properties 
	pageTitle="Einführung in Analytics in Application Insights" 
	description="Kurze Beispiele für alle Hauptabfragen in Analytics, dem leistungsfähigen Suchtool für Application Insights." 
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


 
# Einführung in Analytics in Application Insights


[Analytics](app-analytics.md) ist die leistungsfähige Suchfunktion von [Application Insights](app-insights-overview.md). Auf diesen Seiten wird die Analytics-Abfragesprache beschrieben.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
Sehen Sie sich zu Beginn einige grundlegende Abfragen im Detail an.

## Verbinden mit Ihren Application Insights-Daten

Öffnen Sie Analytics über das [Blatt „Übersicht“](app-insights-dashboards.md) Ihrer App in Application Insights:

![Öffnen Sie unter „portal.azure.com“ die Application Insights-Ressource, und wählen Sie „Analytics“.](./media/app-analytics/001.png)

## [Count](app-analytics-aggregations.md#count)-Zeilen

Metriken, wie z. B. Leistungsindikatoren, werden in einer Tabelle namens „metrics“ gespeichert. Jede Zeile ist ein Telemetriedatenpunkt, der aus dem Application Insights-SDK in einer App empfangen wird. Um herauszufinden, wie groß die Tabelle ist, leiten wir den Inhalt an einen Operator weiter, der die Zeilen zählt:

```AIQL
	
    requests | count
```

> [AZURE.NOTE] Positionieren Sie den Cursor an einer beliebigen Stelle in der Anweisung, bevor Sie auf „Start“ klicken. Sie können eine Anweisung über mehrere Zeilen aufteilen, setzen Sie jedoch keine Leerzeilen in eine Anweisung. Um mehrere Abfragen im Fenster beizubehalten, trennen Sie diese durch Leerzeilen.

Das Ergebnis lautet wie folgt:


![](./media/app-analytics-tour/010.png)

	
[`Count`](app-analytics-aggregations.md#count) ist einer von vielen [Abfrageoperatoren](app-analytics-queries.md), die wir in einer Pipe anordnen können, wobei die Daten in mehreren Phasen gefiltert, umgeformt und verbunden werden.
	
## [Take](app-analytics-aggregations.md#take): Anzeigen von n Zeilen


Betrachten Sie einige Daten: Was sehen Sie in 5 Beispielzeilen?

```AIQL

	requests | take 5
```

Und dies ist das Ergebnis:

![results](./media/app-analytics-tour/020.png)

Wählen Sie Spalten aus, und passen Sie ihre Positionen an:

![Klicken Sie in der oberen rechten Ecke der Ergebnisse auf die Spaltenauswahl.](./media/app-analytics-tour/030.png)

Erweitern Sie ein Element, um die Details anzuzeigen:
 
![Wählen Sie „Tabelle“ aus, und verwenden Sie „Spalten konfigurieren“.](./media/app-analytics-tour/040.png)


## [Top](app-analytics-aggregations.md#top) und [sort](app-analytics-aggregations.md#sort)

`take` ist hilfreich, um schnell eine Stichprobe abzurufen. Es werden jedoch Zeilen aus der Tabelle in unbestimmter Reihenfolge angezeigt. Verwenden Sie für eine sortierte Ansicht `top` (für eine Stichprobe) oder `sort` (für die gesamte Tabelle).

Zeigen Sie die ersten n Zeilen, sortiert nach einer bestimmten Spalte an:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Syntax:* Die meisten Operatoren besitzen Schlüsselwortparameter wie z.B. `by`.
* `desc` = absteigende Reihenfolge, `asc` = aufsteigende Reihenfolge

![](./media/app-analytics-tour/260.png)

`top...` ist eine leistungsstärkere Methode, um `sort ... | take...` auszudrücken. Wir hätten auch Folgendes schreiben können:

```AIQL

	requests | sort by timestamp desc | take 10
```

Das Ergebnis wäre identisch, würde jedoch etwas langsamer ausgeführt. (Sie könnten auch `order` schreiben, was ein Alias von `sort` ist.)

Die Spaltenüberschriften in der Tabellenansicht können auch zum Sortieren der Ergebnisse auf dem Bildschirm verwendet werden. Wenn Sie jedoch `take` oder `top` verwendet haben, um nur einen Teil einer Tabelle abzurufen, ordnen Sie nur die Reihenfolge der abgerufenen Datensätze neu.


## [Project](app-analytics-aggregations.md#project): Auswählen, Umbenennen und Berechnen von Spalten

Verwenden Sie [`project`](app-analytics-aggregations.md#project), um nur die gewünschten Spalten auszuwählen:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


Sie können auch Spalten umbenennen und neue definieren:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

Im skalaren Ausdruck:

* `%` ist der übliche Modulo-Operator. 
* `1d` (die Ziffer Eins, gefolgt von einem „d“) ist ein Zeitraumliteral für einen Tag. Dies sind einige weitere Zeitraumliterale: `12h`, `30m`, `10s`, `0.01s`.
* `floor` (Alias `bin`) rundet einen Wert auf das nächste Vielfache des von Ihnen angegebenen Basiswerts ab. `floor(aTime, 1s)` rundet demnach eine Zeit auf die nächstniedrigere Sekunde ab.

[Ausdrücke](app-analytics-scalars.md) können alle üblichen Operatoren (`+`, `-`, ...) enthalten. Zudem gibt es zahlreiche nützliche Funktionen.

## [Extend](app-analytics-aggregations.md#extend): Berechnen von Spalten

Wenn Sie nur neue Spalten zu den vorhandenen hinzufügen möchten, verwenden Sie [`extend`](app-analytics-aggregations.md#extend):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

[`extend`](app-analytics-aggregations.md#extend) ist weniger ausführlich als die Nutzung von [`project`](app-analytics-aggregations.md#project), wenn Sie alle vorhandenen Spalten beibehalten möchten.

## [Summarize](app-analytics-aggregations.md#summarize): Aggregieren von Zeilengruppen

In einem Beispiel für eine Tabelle können Sie die Felder sehen, in denen die verschiedenen Telemetriedaten gemeldet werden. `exception | take 20` zeigt beispielsweise schnell, dass Ausnahmemeldungen in einem Feld namens `outerExceptionType` gemeldet werden.

Aber statt die einzelnen Instanzen durchzuarbeiten, fragen Sie sich vielmehr, wie viele Ausnahmen jeden Typs gemeldet wurden:

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` gruppiert Zeilen mit den gleichen Werten in den Feldern, die in der `by`-Klausel genannt wurden, was eine einzelne Ergebniszeile für jede Gruppe ergibt. In diesem Fall also gibt es eine Zeile für jeden Ausnahmetyp. Die Aggregationsfunktion `count()` zählt die Zeilen in jeder Gruppe, wodurch eine Spalte im Ergebnis bereitgestellt wird.


Es gibt verschiedene [Aggregationsfunktionen](app-analytics-aggregations.md), und Sie können mehrere gleichzeitig in einem summarize-Operator verwenden, um mehrere berechnete Spalten zu erstellen.

Listen Sie z. B. die HTTP-Anforderungen auf, für die diese Ausnahmen auftreten. Beim Betrachten einer Ausnahmebeispieltabelle werden Sie feststellen, dass die Pfade der HTTP-Anforderung in einer Spalte namens `operation_Name` gemeldet werden.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

Die Aggregationsfunktion `makeset()` erstellt einen Satz aller angegebenen Werte in jeder Gruppe. In diesem Beispiel gibt es nur einen Vorgang, der zu jeder Ausnahme führt.


Das Ergebnis von „summarize“ verfügt über Folgendes:

* jede in `by` genannte Spalte
* plus eine Spalte für jeden Aggregationsausdruck
* eine Zeile für jede Kombination von `by`-Werten


## Zusammenfassen nach Skalarwerten


Sie können Skalarwerte (Numerisch, Zeit oder Intervall) in der by-Klausel verwenden. Zahlen füllen jedoch in der Regel einen durchgehenden Bereich. Um die Datenpunkte zu gruppieren, sollten Sie diese Behältern mit diskreten Werten zuordnen. Für diesen Zweck ist die `bin`-Funktion nützlich:

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` reduziert alle Zeitstempel auf Intervalle von 1 Tag. Es handelt sich um ein Alias von `floor`, eine Funktion, die in den meisten Sprachen bekannt ist. Jeder Wert wird auf das nächste Vielfache des angegebenen Rundungswerts reduziert, damit `summarize` die Zeilen den Gruppen von geeigneter Größe zuweisen kann. (Ohne dies gäbe es eine Ergebniszeile für jeden eigenständigen Bruchteil einer Sekunde, in der die Daten keinesfalls zusammengefasst werden würden.)

Es gibt jedoch noch eine bessere Ansicht als die hier gezeigte Tabelle. Betrachten Sie die Ergebnisse in der Diagrammansicht mit vertikalem Balken:

![Klicken Sie auf „Diagramm“, wählen Sie dann „Vertikales Balkendiagramm“, und weisen Sie x- und y-Achsen zu.](./media/app-analytics-tour/230.png)

Beachten Sie, dass die Diagrammdarstellung die Uhrzeitangaben immer in der richtigen Reihenfolge anzeigt, auch wenn wir die Ergebnisse nicht nach Zeit sortiert haben (wie es in der Tabellenansicht der Fall ist).


## [Where](app-analytics-aggregations.md#where): Nach einer Bedingung filtern

Wenn Sie die Application Insights-Überwachung für die [Client](app-insights-javascript.md)- und Serverseite der App eingerichtet haben, stammen einige der Telemetriedaten in der Datenbank aus Browsern.

Sehen Sie sich nur die vom Browser gemeldeten Ausnahmen an:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

Der `where`-Operator akzeptiert einen booleschen Ausdruck. Dazu einige wichtige Punkte:

 * `and`, `or`: Boolesche Operatoren
 * `==`, `<>`: gleich und ungleich
 * `=~`, `!=`: Zeichenfolge ohne Beachtung der Groß-/Kleinschreibung, gleich und ungleich. Es gibt viele weitere Zeichenfolgenvergleichsoperatoren.

Erfahren Sie mehr über [skalare Ausdrücke](app-analytics-scalars.md).

### Filtern von Ereignissen

Suchen Sie nach nicht erfolgreichen Anforderungen:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` ist vom Typ „String“, daher müssen wir es für einen numerischen Vergleich [umwandeln](app-analytics-scalars.md#casts).

Fassen Sie die unterschiedlichen Antworten zusammen:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Zeitdiagramme

Zeigen Sie an, wie viele Ereignissen pro Tag stattfinden:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Wählen Sie die Diagrammanzeigeoption aus:

![Zeitdiagramm](./media/app-analytics-tour/080.png)

Die x-Achse für Liniendiagramme muss vom Typ „DateTime“ sein.

## Mehrere Reihen 

Verwenden Sie mehrere Werte in einer `summarize by`-Klausel, um eine separate Zeile für jede Kombination von Werten zu erstellen:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

Um mehrere Linien in einem Diagramm anzuzeigen, klicken Sie auf **Teilen nach**, und wählen Sie eine Spalte aus.

![](./media/app-analytics-tour/100.png)



## Durchschnittlicher Tageszyklus

Inwiefern variiert die Nutzung im Verlauf eines durchschnittlichen Tages?

Zählen Sie die Anforderungen nach Zeitmodule an einem Tag, unterteilt in Stunden:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Liniendiagramm der Stunden an einem durchschnittlichen Tag](./media/app-analytics-tour/120.png)

>[AZURE.NOTE] Beachten Sie, dass Zeitdauern derzeit in datetime-Angaben konvertiert werden müssen, um sie im Diagramm anzuzeigen.


## Vergleichen mehrerer täglicher Reihen

Inwieweit variiert die Nutzung im Verlauf des Tages in unterschiedlichen Staaten?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Teilen Sie das Diagramm in Staaten auf:

![Aufteilen nach „client\_StateOrProvince“](./media/app-analytics-tour/130.png)


## Darstellen einer Verteilung

Wie viele Sitzungen unterschiedlicher Längen gibt es?

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

Die letzte Linie ist erforderlich, um die Konvertierung in datetime-Angaben durchzuführen: Die x-Achse eines Liniendiagramms kann derzeit nur datetime-Angaben enthalten.

Die `where`-Klausel schließt einmalige Sitzungen (sessionDuration==0) aus und legt die Länge der x-Achse fest.


![](./media/app-analytics-tour/290.png)



## [Quantile](app-analytics-aggregations.md#percentiles)

Welche Bereichsdauern decken verschiedene Prozentsätze von Sitzungen ab?

Verwenden Sie die obige Abfrage, aber ersetzen Sie die letzte Zeile:

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s) 
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Wir haben auch die Obergrenze in der Where-Klausel entfernt, um die richtigen Zahlen einschließlich aller Sitzungen mit mehr als einer Anforderung zu ermitteln:

![result](./media/app-analytics-tour/180.png)

Daraus lässt sich Folgendes ablesen:

* 5 % der Sitzungen sind kürzer als 3 Minuten 34 Sekunden. 
* 50 % der Sitzung sind kürzer als 36 Minuten.
* 5 % der Sitzungen dauern länger als 7 Tage.

Um eine separate Aufstellung für jedes Land zu erhalten, müssen Sie die Spalte „client\_CountryOrRegion“ separat durch beide summarize-Operatoren bringen:

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
	  by client_CountryOrRegion
```

![](./media/app-analytics-tour/190.png)


## [Join](app-analytics-aggregations.md#join)

Wir haben Zugriff auf mehrere Tabellen, einschließlich der Anforderungen und Ausnahmen.

Um Ausnahmen im Hinblick auf eine Anforderung zu suchen, die eine Fehlerantwort zurückgegeben hat, können wir die Tabellen anhand von `session_Id` verknüpfen:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Es ist üblich, `project` zu verwenden, um vor dem Verknüpfen nur die Spalten auszuwählen, die wir benötigen. In den gleichen Klauseln benennen wir die Zeitstempelspalte um.



## [Let](app-analytics-aggregations.md#let): Einer Variable ein Ergebnis zuweisen

Verwenden Sie [let](./app-analytics-syntax.md#let-statements), um die einzelnen Teile des vorherigen Ausdrucks auszusortieren. Die Ergebnisse sind wie folgt unverändert:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Tipp: Fügen Sie im Analytics-Client keine Leerzeilen zwischen diesen Teilen ein. Stellen Sie sicher, dass Sie alles ausführen.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->