<properties 
	pageTitle="Die summarize-Anweisung und Aggregationsfunktionen in der Application Insights-Analyse" 
	description="Referenz für Aggregationsfunktionen und die summarize-Anweisung in der Application Insights-Analyse, dem leistungsfähigen Suchtool für Application Insights." 
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
	ms.date="03/06/2016" 
	ms.author="awills"/>


# Aggregation in Application Insights-Analyse

[Application Insights-Analyse](app-analytics.md) ist ein leistungsfähiges Suchmodul für Ihre [Application Insights](app-insights-overview.md)-Telemetrie. Auf diesen Seiten wird die Abfragesprache AIQL von Application Insights-Analyse beschrieben.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]



## summarize-Operator

Erzeugt eine Tabelle, die den Inhalt der Eingabetabelle aggregiert.

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-analytics-aggregations/01.png)

* Eingabedatensätze werden in Gruppen zusammengefasst, sodass jede Gruppe über eine bestimmte Kombination von Werten in den `by`-Feldern verfügt.
* Die Aggregatausdrücke werden über die Elemente jeder Gruppe ausgewertet.
* Für jede spezielle Kombination von Werten der by-Ausdrücke gibt es eine Ausgabezeile. 
* Es gibt auch eine Ausgabespalte für jeden Aggregatausdruck und jeden by-Ausdruck. Alle anderen Eingabespalten werden gelöscht.

### Syntax

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**Argumente**

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *Aggregation:* Ein Aufruf einer Aggregationsfunktion, wie z. B. `count()` oder `avg()`, mit Spaltennamen als Argumente. Eine Liste der Aggregationsfunktionen finden Sie nachfolgend.
* *GroupExpression:* Ein Ausdruck für die Spalten, der einen Satz von unterschiedlichen Werten bereitstellt. Normalerweise handelt es sich entweder um einen Spaltennamen, der bereits einen eingeschränkten Satz von Werten bereitstellt, oder um `bin()` mit einer numerischen Spalte oder Zeitspalte als Argument. 

Wenn Sie einen numerischen Ausdruck oder Zeitausdruck ohne `bin()` bereitstellen, wendet die AI-Analyse ihn automatisch mit einem Intervall von `1h` für Uhrzeiten oder von `1.0` für Zahlen an.

Wenn Sie keine *GroupExpression* angeben, wird die gesamte Tabelle in einer einzelnen Ausgabezeile zusammengefasst.

Sie müssen in der `by`-Klausel einen einfachen Typ, keinen dynamischen Typ verwenden. Hier ist zum Beispiel die `tostring`-Umwandlung wichtig:

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### Zusammenfassen nach Spalten mit diskreten Werten

Abfrage zur Anzeige von durchschnittlichen Antwortzeiten auf unterschiedliche HTTP-Anforderungen, bei der alle Anfragen aussortiert werden, die andere Antwortcodes aufwiesen:

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![result](./media/app-analytics-aggregations/03.png)


* Die Aggregatausdrücke (wie „count“ und „avg“) müssen aus Aggregationsfunktionen gebildet werden, die in diesem Artikel dokumentiert sind. Bei den Argumenten kann es sich um beliebige skalare Funktionen handeln.
* Die Gruppierungsausdrücke (nach „by“) können skalare Ausdrücke sein; sie sind jedoch leistungsfähiger, wenn es sich nur um die Feldnamen handelt.

### Zusammenfassen nach numerischen Spalten

Wenn Sie anhand eines kontinuierlichen Skalars, z. B. einer Zahl oder einer Zeit, gruppieren möchten, müssen Sie die `bin`-Funktion (auch bekannt als `floor`) verwenden, um den kontinuierlichen Bereich als Pakete in Behälter (bins) zu sortieren.

    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![result](./media/app-analytics-aggregations/04.png)

(Die Dauer der Anforderung ist eine Zahl in Millisekunden.)
 
## Tipps

* Verwenden Sie `where`, um alle nicht gewünschten Zeilen vor `summarize` zu entfernen.
 * Entfernen Sie die Null-Werte. Bei einem Null-Wert in einer Gruppe ist das Ergebnis des Aggregats Null. 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* Auch wenn Sie beliebige Ausdrücke für die Aggregation und Gruppierung von Ausdrücken bereitstellen können, ist es effizienter, einfache Feldnamen zu verwenden oder `bin()` auf ein numerisches Feld anzuwenden.





## Beispiele

Suchen Sie die minimalen und maximalen Zeitstempel aller Datensätze in der Tabelle mit den Aktivitäten. Es gibt keine group-by-Klausel, daher gibt es nur eine Zeile in der Ausgabe:

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`



#### Sitzungsdauer

Sitzungen in einem Ereignisprotokoll weisen mehrere Ereignisse auf. Suchen Sie die Start- und Endzeit jeder Sitzung, indem Sie in jeder Sitzung nach den frühesten und aktuellsten Ereignissen suchen:

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

Der `extend`-Vorgang fügt die duration-Spalte hinzu, wobei mit `bin` (auch bekannt als `floor`) auf die nächste Sekunde gerundet wird.

![](./media/app-analytics-aggregations/minmax.png)

#### Beispiel: durchschnittliche Dauer

Lassen Sie uns nun die durchschnittliche Sitzungsdauer für Kunden in verschiedenen Städten herausfinden:

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

Wir haben die `client_City`-Spalte der `by`-Klausel hinzugefügt, sodass sie den ersten summarize-Vorgang durchläuft. Wenn alle Ereignisse einer Kundensitzung in derselben Stadt stattfinden, wird die Anzahl der Ausgaben der Zusammenfassung nicht erhöht.


![](./media/app-analytics-aggregations/durationcity.png)


#### Beispiel: 

Suchen Sie die geschäftigste Tageszeit in jeder Stadt des Kunden. „Geschäftig“ bezeichnet hier die Tageszeit, zu der die meisten Sitzungen an einem durchschnittlichen Arbeitstag beginnen.

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## AGGREGATIONEN

## beliebig 

    any(Expression)

Wählt eine Zeile der Gruppe nach dem Zufallsprinzip aus, und gibt den Wert des angegebenen Ausdrucks zurück.

Dies empfiehlt sich beispielsweise, wenn eine Spalte über eine große Anzahl von ähnlichen Werten verfügt (z. B. eine Spalte „Fehlertext“) und Sie einmal pro eindeutigem Wert für den zusammengesetzten Gruppenschlüssel Stichproben aus dieser Spalte abrufen möchten.

**Beispiel**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a> <a name="argmax"></a>
## argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Findet eine Zeile in der Gruppe, die *ExprToMaximize* minimiert/maximiert, und gibt den Wert von *ExprToReturn* zurück (oder `*`, um die gesamte Zeile zurückzugeben).

**Tipp**: Die durchlaufenen Spalten werden automatisch umbenannt. Um sicherzustellen, dass Sie die richtigen Namen verwenden, überprüfen Sie die Ergebnisse mit `take 5`, bevor Sie die Ergebnisse an einen anderen Operator übergeben.

**Beispiele**

Zeigen Sie für jeden Anforderungsnamen, wann die längste Anforderung aufgetreten ist:

    requests | summarize argmax(duration, timestamp) by name

Zeigen Sie alle Details der längsten Anforderung, nicht nur den Zeitstempel:

    requests | summarize argmax(duration, *) by name


Suchen Sie den niedrigsten Wert jeder Metrik, zusammen mit dem Zeitstempel und anderen Daten:

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

Berechnet den Durchschnitt von *Expression* in der Gruppe.

## buildschema

    buildschema(DynamicExpression)

Gibt das minimale Schema zurück, das alle Werte von *DynamicExpression* zulässt.

Der Parameterspaltentyp sollte `dynamic` sein, d. h. ein Array oder ein Eigenschaftenbehälter.

**Beispiel**

    exceptions | summarize buildschema(details)

Ergebnis:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Beachten Sie, dass `indexer` verwendet wird, um zu markieren, an welcher Stelle Sie einen numerischen Index verwenden sollten. Einige gültige Pfade für dieses Schema (vorausgesetzt diese Beispielindizes befinden sich im Bereich):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Beispiel**

Wenn die Eingabespalte drei dynamische Werte hat:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Lautet das resultierende Schema folgendermaßen:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Das Schema weist auf Folgendes hin:

* Das Stammobjekt ist ein Container mit den vier Eigenschaften x, y, z und t.
* Die Eigenschaft „x“ kann entweder den Typ „int“ oder „string“ haben.
* Bei der Eigenschaft „y“ kann es sich entweder um den Typ „double“ handeln oder um einen anderen Container mit einer Eigenschaft namens „w“ vom Typ „string“.
* Das ``indexer``-Schlüsselwort gibt an, dass „z“ und „t“ Arrays sind.
* Jedes Element im Array „z“ ist entweder eine ganze Zahl oder eine Zeichenfolge (string).
* „t“ ist ein Array von Zeichenfolgen.
* Jede Eigenschaft ist implizit optional, und jedes Array kann leer sein.

#### Schemamodell

Die Syntax des zurückgegebenen Schemas lautet folgendermaßen:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Sie entsprechen einer Teilmenge der TypeScript-Typanmerkungen, die als dynamischer AIQL-Wert codiert sind. In TypeScript würde das Beispielschema folgendermaßen lauten:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


## count

    count([ Predicate ])

Gibt die Anzahl der Zeilen zurück, für die *Predicate* als `true` ausgewertet wird. Wenn kein *Predicate* (Prädikat) angegeben ist, wird die Gesamtzahl der Datensätze in der Gruppe zurückgegeben.

**Leistungstipp**: Verwenden Sie `summarize count(filter)` anstelle von `where filter | summarize count()`.
   

## dcount

    dcount( Expression [ ,  Accuracy ])

Gibt eine Schätzung der Anzahl von unterschiedlichen Werten für *Expr* in der Gruppe zurück. (Verwenden Sie zum Auflisten der unterschiedlichen Werte [`makeset`](#makeset).)

Mit *Accuracy* wird, sofern angegeben, der Ausgleich zwischen Geschwindigkeit und Genauigkeit gesteuert.

 * `0` = die am wenigsten präzise und schnellste Berechnung.
 * `1` = die Standardeinstellung, die Genauigkeit und Berechnungszeit ausgleicht; etwa 0,8 % Fehlerwahrscheinlichkeit.
 * `2` = die präziseste und langsamste Berechnung; etwa 0,4 % Fehlerwahrscheinlichkeit.

**Beispiel**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

Gibt ein `dynamic` (JSON)-Array aller Werte von *Expr* in der Gruppe zurück.

* *MaxListSize* ist eine optionale Integerbeschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert: *128*).

## makeset

    makeset(Expression [ , MaxSetSize ] )

Gibt ein `dynamic` (JSON)-Array des Satzes von unterschiedlichen Werten zurück, die *Expr* in der Gruppe annimmt. (Tipp: Verwenden Sie zum Zählen der unterschiedlichen Werte [`dcount`](#dcount).)
  
*  *MaxSetSize* ist eine optionale Integerbeschränkung für die maximale Anzahl von zurückgegebenen Elementen (Standardwert: *128*).

**Beispiel**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-analytics-aggregations/makeset.png)

Siehe auch den [`mvexpand`-Operator](app-analytics-queries.md#mvexpand-operator) für die umgekehrte Funktion.


## max, min

    max(Expr)

Berechnet das Maximum von *Expr*.
    
    min(Expr)

Berechnet das Minimum von *Expr*.

**Tipp**: Damit erhalten Sie die Mindest- oder Maximalwerte, z. B. den höchsten oder niedrigsten Preis. Wenn Sie jedoch andere Spalten in der Zeile abrufen möchten, z. B. den Namen des Lieferanten mit dem niedrigsten Preis, verwenden Sie [argmin oder argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a>
## percentile, percentiles

    percentile(Expression, Percentile)

Gibt eine Schätzung für *Expression* des angegebenen Quantils in der Gruppe zurück. Die Genauigkeit hängt von der Bevölkerungsdichte in der Region des Quantils ab.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

Ähnlich wie `percentile()`, berechnet jedoch eine Reihe von Quantilwerten (was schneller ist, als jedes Quantil einzeln zu berechnen).

**Beispiele**


Der Wert von `duration`, der größer ist als 95 % des Beispielsatzes und kleiner als 5 % des Beispielsatzes, berechnet für jeden Anforderungsnamen:

    request 
    | summarize percentile(duration, 95)
      by name

Lassen Sie „by...“ aus, um den Wert für die gesamte Tabelle zu berechnen.

Berechnen Sie gleichzeitig mehrere Quantile für andere Anforderungsnamen:

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-analytics-aggregations/percentiles.png)

Die Ergebnisse zeigen, dass für die Anforderung „/Events/Index“ auf 5 % der Anforderungen in weniger als 2,44 Sekunden reagiert wird, auf die Hälfte in 3,52 Sekunden und auf 5 % langsamer als 6,85 Sekunden.


Berechnen Sie mehrere Statistiken:

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### Schätzungsfehler in Quantilen

Das Quantilaggregat bietet einen ungefähren Wert mithilfe von [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Einige wichtige Punkte:

* Die Grenzen für den Schätzungsfehler variieren je nach dem Wert des angeforderten Quantils. Die beste Genauigkeit erhalten Sie an den Enden der Skala von [0 bis 100]. Die Quantile 0 und 100 sind die Mindest- und Maximalwerte für die Verteilung. Die Genauigkeit nimmt zur Mitte der Skala hin ab. Am Mittelpunkt ist die Genauigkeit am unpräzisesten und auf 1 % begrenzt. 
* Fehlergrenzen werden in Bezug auf den Rang, nicht auf den Wert sichtbar. Beispiel: Quantil(X, 50) hat den Wert Xm zurückgegeben. Die Schätzung garantiert, dass mindestens 49 % und höchstens 51 % der Werte von X kleiner sind als Xm. Es gibt keine theoretische Beschränkung hinsichtlich des Unterschieds zwischen Xm und dem tatsächlichen Mittelwert von X.

## stdev

     stdev(Expr)

Gibt die Standardabweichung von *Expr* für die Gruppe zurück.

## variance

    variance(Expr)

Gibt die Varianz von *Expr* für die Gruppe zurück.

## sum

    sum(Expr)

Gibt die Summe von *Expr* für die Gruppe zurück.




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->