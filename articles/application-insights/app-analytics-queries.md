<properties 
	pageTitle="Operatoren und Abfragen in der Application Insights-Analyse" 
	description="Referenz für die Operatoren, die für Abfragen in der Application Insights-Analyse, dem leistungsfähigen Suchtool für Application Insights, verwendet werden." 
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



# Abfragesyntax in der Application Insights-Analyse


[Application Insights-Analyse](app-analytics.md) ist ein leistungsfähiges Suchmodul für Ihre [Application Insights](app-insights-overview.md)-Telemetrie. Auf diesen Seiten wird die Abfragesprache AIQL der Application Insights-Analyse beschrieben.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

Eine Abfrage in Ihrer Telemetrie besteht aus einen Verweis auf einen Quelldatenstrom, gefolgt von einer Pipeline von Filtern. Beispiel:


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
Jeder Filter mit einem senkrechten Strich `|` als Präfix ist eine Instanz eines *Operators* mit einigen Parametern. Die Eingabe des Operators ist die Tabelle, die das Ergebnis der vorhergehenden Pipeline ist. In den meisten Fällen sind alle Parameter [skalare Ausdrücke](app-analytics-scalars.md) über die Spalten der Eingabe. In einigen Fällen sind die Parameter die Namen von Eingabespalten, und in einigen Fällen ist der Parameter eine zweite Tabelle. Das Ergebnis einer Abfrage ist immer eine Tabelle, auch wenn sie nur eine Spalte und eine Zeile enthält.

Eine Abfrage kann eine oder mehrere [let-Klauseln](#let-clause) als Präfix aufweisen, die Skalare, Tabellen oder Funktionen definieren, die in der Abfrage verwendet werden können.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` wird in den folgenden Abfragebeispielen verwendet, um die vorhergehende Pipeline oder Quelltabelle anzugeben.

## count-Operator

Der `count`-Operator gibt die Anzahl von Datensätzen (Zeilen) in der Eingabe-Datensatzgruppe an.

**Syntax**

    T | count

**Argumente**

* *T*: Die tabellarischen Daten, deren Datensätze gezählt werden.

**Rückgabe**

Diese Funktion gibt eine Tabelle mit einem einzelnen Datensatz und einer Spalte vom Typ `long` zurück. Der Wert der einzigen Zelle ist die Anzahl der Datensätze in *T*.

**Beispiel**

```AIQL
requests | count
```



## extend-Operator

     T | extend duration = stopTime - startTime

Fügen Sie eine oder mehrere berechnete Spalten an eine Tabelle an.


**Syntax**

    T | extend ColumnName = Expression [, ...]

**Argumente**

* *T:* Die Eingabetabelle.
* *ColumnName:* Der Name einer hinzuzufügenden Spalte. 
* *Expression:* Eine Berechnung über die vorhandenen Spalten.

**Rückgabe**

Eine Kopie der Eingabetabelle mit den angegebenen zusätzlichen Spalten.

**Tipps**

* Verwenden Sie stattdessen [`project`](#project-operator), wenn Sie auch einige Spalten löschen oder umbenennen möchten.
* Verwenden Sie nicht einfach `extend`, um einen kürzeren Namen zur Verwendung in einem langen Ausdruck zu erhalten. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Die systemeigenen Spalten der Tabelle wurden indiziert; der neue Name definiert eine zusätzliche, nicht indizierte Spalte, d. h. die Abfrage wird wahrscheinlich langsamer ausgeführt.

**Beispiel**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## join-Operator

    Table1 | join (Table2) on CommonColumn

Führt die Zeilen zweier Tabellen anhand von übereinstimmenden Werten der angegebenen Spalte zusammen.


**Syntax**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumente**

* *Table1* – die „linke Seite“ der Verknüpfung.
* *Table2* – die „rechte Seite“ der Verknüpfung. Kann ein geschachtelter Abfrageausdruck sein, der eine Tabelle ausgibt.
* *CommonColumn* – eine Spalte mit dem gleichen Namen in den beiden Tabellen.
* *Kind* – gibt an, wie Zeilen aus den beiden Tabellen abgeglichen werden.

**Rückgabe**

Eine Tabelle mit:

* Einer Spalte für jede Spalte in jeder der beiden Tabellen, einschließlich der übereinstimmenden Schlüssel. Die Spalten der rechten Seite werden bei Namenskonflikten automatisch umbenannt.
* Einer Zeile für jede Übereinstimmung zwischen den Eingabetabellen. Eine Übereinstimmung ist eine ausgewählte Zeile in einer Tabelle, die in allen `on`-Feldern denselben Wert aufweist, wie eine Zeile in der anderen Tabelle. 

* `Kind` – nicht angegeben

    Nur eine Zeile der linken Seite wird für jeden Wert des `on`-Schlüssels abgeglichen. Die Ausgabe enthält eine Zeile für jede Übereinstimmung dieser Zeile mit Zeilen der rechten Seite.

* `Kind=inner`
 
     Der Ausgabe enthält eine Zeile für jede Kombination von übereinstimmenden Zeilen der linken und rechten Seite.

* `kind=leftouter` (oder `kind=rightouter` oder `kind=fullouter`)

     Zusätzlich zu den internen Übereinstimmungen ist eine Zeile für jede Zeile auf der linken (und/oder rechten) Seite vorhanden, selbst wenn es keine Übereinstimmung damit gibt. In diesem Fall enthalten die Ausgabezellen ohne Übereinstimmung NULL-Werte.

* `kind=leftanti`

     Gibt alle Datensätze von der linken Seite zurück, für die keine Übereinstimmungen auf der rechten Seite vorhanden sind. Die Ergebnistabelle enthält nur die Spalten von der linken Seite.
 
Wenn mehrere Zeilen mit denselben Werten für diese Felder vorhanden sind, erhalten Sie Zeilen für alle Kombinationen.

**Tipps**

Für eine optimale Leistung:

* Verwenden Sie `where` und `project`, um die Anzahl von Zeilen und Spalten in den Eingabetabellen vor der `join` zu reduzieren. 
* Wenn eine Tabelle immer kleiner als die andere ist, verwenden Sie diese als die linke (weitergeleitete) Seite der Verknüpfung.
* Die Spalten für die Verknüpfungsübereinstimmung müssen den gleichen Namen haben. Verwenden Sie ggf. den project-Operator, um eine Spalte in einer der Tabellen umzubenennen.

**Beispiel**

Abrufen erweiterter Aktivitäten aus einem Protokoll, in dem einige Einträge den Start und das Ende einer Aktivität markieren

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```

[Informationen zu Verknüpfungskonfigurationen](app-analytics-samples.md#join-flavors)

## let-Klausel

**Tabellarisches let – Benennen einer Tabelle**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Skalares let – Benennen eines Werts**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda-let – Benennen einer Funktion**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

Eine let-Klausel bindet einen Namen an ein tabellarisches Ergebnis, einen Skalarwert oder eine Funktion. Die Klausel ist das Präfix einer Abfrage, und diese Abfrage ist der Bereich der Bindung. (Die let-Klausel bietet keine Möglichkeit, Dinge zu benennen, die Sie später in der Sitzung verwenden.)

**Syntax**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *Typ:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](app-analytics-scalars.md#dynamic-type)
* *plain\_query:* Eine Abfrage ohne vorangestellte let-Klausel.

**Beispiele**




    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


Selbstverknüpfung:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

## limit-Operator

     T | limit 5

Gibt höchstens die angegebene Anzahl von Zeilen aus der Eingabetabelle zurück. Es gibt keine Garantie dafür, welche Datensätze zurückgegeben werden. (Verwenden Sie [`top`](#top-operator), um bestimmte Datensätze zurückzugeben.)

**Alias** `take`

**Syntax**

    T | limit NumberOfRows


**Tipps**

`Take` ist eine einfache und effiziente Möglichkeit, ein Beispiel für die Ergebnisse anzuzeigen, wenn Sie interaktiv arbeiten. Denken Sie daran, dass es keine Garantie dafür gibt, dass bestimmte Zeilen erzeugt bzw. in einer bestimmten Reihenfolge erzeugt werden.

Für die Anzahl von Zeilen, die an den Client zurückgegeben werden, besteht eine implizite Begrenzung, auch wenn Sie nicht `take` verwenden. Um diese Begrenzung aufzuheben, verwenden Sie die Client-Anforderungsoption `notruncation`.



## mvexpand-Operator

    T | mvexpand listColumn 

Erweitert eine Liste aus einer Zelle mit dynamischer Typisierung (JSON) erweitert, sodass jeder Eintrag eine separate Zeile enthält. Alle anderen Zellen in einer erweiterten Zeile sind dupliziert.

(Siehe auch [`summarize makelist`](#summarize-operator), der die gegenteilige Funktion durchführt.)

**Beispiel**

Angenommen, die Eingabetabelle ist:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

Das Ergebnis lautet:

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


**Syntax**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumente**

* *ColumnName:* Im Ergebnis werden Arrays in der benannten Spalte auf mehrere Zeilen erweitert. 
* *ArrayExpression:* Ein Ausdruck, der ein Array zurückgibt. Bei Verwendung dieses Formulars wird eine neue Spalte hinzugefügt, und die vorhandene wird beibehalten.
* *Name:* Ein Name für die neue Spalte.
* *Typename:* Wandelt den erweiterten Ausdruck in einen bestimmten Typ um.
* *RowLimit:* Die maximale Anzahl von Zeilen, die aus jeder ursprünglichen Zeile generiert werden. Der Standardwert ist 128.

**Rückgabe**

Mehrere Zeilen für alle Werte in jedem Array in der benannten Spalte oder im Arrayausdruck.

Die erweiterte Spalte ist immer dynamisch typisiert. Verwenden Sie eine Umwandlung wie `todatetime()` oder `toint()`, wenn Sie Werte berechnen oder aggregieren möchten.

Zwei Erweiterungsmodi für Eigenschaftenbehälter werden unterstützt:

* `bagexpansion=bag`: Eigenschaftenbehälter werden zu Eigenschaftenbehältern mit einem einzelnen Eintrag erweitert. Dies ist die Standarderweiterung.
* `bagexpansion=array`: Eigenschaftenbehälter werden zu Arraystrukturen mit zwei Elementen `[`*Schlüssel*`,`*Wert*`]` erweitert und lassen den einheitlichen Zugriff auf Schlüssel und Werte zu (sowie z. B. auch das Ausführen einer distinct-count-Aggregation über Eigenschaftsnamen). 

**Beispiele**


    exceptions | take 1 
    | mvexpand details[0]

Teilt einen Ausnahmedatensatz für jedes Element im Feld „Details“ in Zeilen auf.

Siehe [Diagramm mit der Anzahl von Live-Aktivitäten im Laufe der Zeit](app-analytics-samples.md#concurrent-activities).


## parse-Operator

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

Extrahiert Werte aus einer Zeichenfolge. Kann einfache oder reguläre Ausdrücke für Übereinstimmungen verwenden.

Die Elemente in der `with`-Klausel werden wiederum mit der Quellzeichenfolge verglichen. Jedes Element entnimmt ein Segment des Quelltexts. Handelt es sich dabei um eine einfache Zeichenfolge, bewegt sich der Abgleich-Cursor so weit wie die Übereinstimmung. Handelt es sich um eine Spalte mit einem Typnamen, bewegt sich der Cursor weit genug, um den angegebenen Typ zu analysieren. (Zeichenfolgenübereinstimmungen bewegen sich, bis eine Übereinstimmung mit dem nächsten Element gefunden wird.) Handelt es sich um einen RegEx, wird der reguläre Ausdruck abgeglichen (und die resultierende Spalte ist immer vom Typ „Zeichenfolge“).

**Syntax**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**Argumente**

* *T:* Die Eingabetabelle.
* *kind:* einfach oder RegEx. Die Standardeinstellung ist „einfach“.
* *StringExpression:* Ein Ausdruck, der als Zeichenfolge ausgewertet wird oder in eine konvertiert werden kann.
* *SimpleMatch:* Eine Zeichenfolge, die mit dem nächsten Teil des Texts übereinstimmt.
* *Column:* Gibt die neue Spalte an, der eine Übereinstimmung zugewiesen wird.
* *Type:* Gibt an, wie der nächste Teil der Quellzeichenfolge zu analysieren ist.
* *Regex:* Ein regulärer Ausdruck für den Abgleich des nächsten Teils der Zeichenfolge. 

**Rückgabe**

Die Eingabetabelle, erweitert gemäß der Liste der Spalten.


**Beispiele**

Der `parse`-Operator bietet eine optimierte Methode zum Erweitern einer `extend`-Tabelle mit mehreren `extract`-Anwendungen auf den gleichen `string`-Ausdruck. Dies ist besonders hilfreich, wenn die Tabelle eine `string`-Spalte mit mehreren Werten enthält, die Sie in einzelne Spalten aufteilen möchten, z. B. eine Spalte, die von einer Entwickler-Ablaufverfolgunganweisung („`printf`“/“`Console.WriteLine`“) erzeugt wurde.

Im folgenden Beispiel wird angenommen, dass die Spalte `EventNarrative` der Tabelle `StormEvents` Zeichenfolgen im Format `{0} at {1} crested at {2} feet around {3} on {4} {5}` enthält. Der folgende Vorgang erweitert die Tabelle mit zwei Spalten: `SwathSize` und `FellLocation`.


|EventNarrative|
|---|
|Der Green River erreichte bei Brownsville am 12. Dezember um 09.30 Uhr EST den Höchststand von 5,7 m. Die Hochwassergrenze bei Brownsville beträgt 5,5 m. Bei diesem Pegel tritt eine geringfügige Überflutung auf. Der Fluss tritt über Schleusenwände und flutet einige der tiefer gelegenen Uferlandschaften und landwirtschaftlichen Nutzflächen.|
|Der Fluss Rolling Fork erreichte bei Boston am 12. Dezember um 17.00 Uhr EST den Höchststand von 12 m. Die Hochwassergrenze bei Boston beträgt 10,7 m. Bei diesem Pegel tritt eine geringfügige Überflutung auf, bei der einige landwirtschaftliche Nutzflächen in der Flussniederung unter Wasser stehen.|
|Der Green River erreichte bei Woodbury am 16. Dezember um 06.00 Uhr EST den Höchststand von 11,2 m. Die Hochwassergrenze bei Woodbury beträgt 10 m. Bei diesem Pegel tritt eine geringfügige Überflutung auf, bei der einige Tiefebenen um die Stadt unter Wasser stehen.|
|Der Ohio erreichte bei Tell Stadt am 18. Dezember um 07.00 Uhr EST den Höchststand von 11,9 m. Die Hochwassergrenze bei Tell Stadt beträgt 11,6 m. Bei diesem Pegel beginnt der Fluss, die darüber gelegenen Uferlandschaften zu überfluten. Der Indiana Highway 66 wird zwischen Rome und Derby überflutet.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|Ort|Höhe|Time|Monat|Tag|
|---|---|---|---|---|---|
|Green River | Woodbury |11,2| 06\.00 Uhr EST | Dezember|16|
|Fluss Rolling Fork | Boston |12| 17\.00 Uhr EST | Dezember|12|
|Green River | Brownsville |5,7| 09\.30 Uhr EST | Dezember|12|
|Ohio River | Tell Stadt |11,9| 7\.00 Uhr EST | Dezember|18|

Die Verwendung von regulären Ausdrücken für den Abgleich ist ebenfalls möglich. Dies führt zum gleichen Ergebnis, alle Ergebnisspalten sind jedoch vom Typ „Zeichenfolge“:

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## project-Operator

    T | project cost=price*quantity, price

Wählen Sie die Spalten aus, die einbezogen, umbenannt oder gelöscht werden sollen, und fügen Sie neue berechnete Spalten ein. Die Reihenfolge der Spalten im Ergebnis wird durch die Reihenfolge der Argumente festgelegt. Nur die in den Argumenten angegebenen Spalten sind im Ergebnis enthalten: alle anderen Spalten in der Eingabe werden gelöscht. (Siehe auch `extend`.)


**Syntax**

    T | project ColumnName [= Expression] [, ...]

**Argumente**

* *T:* Die Eingabetabelle.
* *ColumnName:* Name einer Spalte, der in der Ausgabe angezeigt wird. Wenn kein *Ausdruck* vorhanden ist, muss die Eingabe eine Spalte mit diesem Namen enthalten. 
* *Expression:* Optionaler skalarer Ausdruck, der auf die Eingabespalten verweist. 

    Das Zurückgeben einer neuen berechneten Spalte mit dem gleichen Namen wie eine vorhandene Spalte der Eingabe ist zulässig.

**Rückgabe**

Eine Tabelle, deren Spalten als Argumente benannt sind, und die ebenso viele Zeilen wie die Eingabetabelle aufweist.

**Beispiel**

Das folgende Beispiel zeigt mehrere Arten von Manipulationen, die mithilfe des `project`-Operators durchgeführt werden. Die Eingabetabelle `T` verfügt über drei Spalten vom Typ `int`: `A`, `B` und `C`.

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```


[Weitere Beispiele](app-analytics-samples.md#activities).


## range-Operator

    range LastWeek from ago(7d) to now() step 1d

Erzeugt eine einspaltige Tabelle mit Werten. Beachten Sie, dass keine Pipeline-Eingabe vorhanden ist.

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**Syntax**

    range ColumnName from Start to Stop step Step

**Argumente**

* *ColumnName:* Der Name der einzelnen Spalte in der Ausgabetabelle.
* *Start:* Der kleinste Wert in der Ausgabe.
* *Stop:* Der höchste in der Ausgabe generierte Wert (oder eine Grenze für den höchsten Wert, wenn *step* diesen Wert überschreitet).
* *Step:* Die Differenz zwischen zwei aufeinanderfolgenden Werten. 

Die Argumente müssen numerische Werte, Datums- oder TimeSpan-Werte sein. Sie können nicht auf die Spalten einer Tabelle verweisen. (Wenn Sie den Bereich basierend auf einer Eingabetabelle berechnen möchten, verwenden Sie die [range*-Funktion*](app-analytics-scalars.md#range), vielleicht mit dem [mvexpand-Operator](#mvexpand-operator).)

**Rückgabe**

Eine Tabelle mit einer einzelnen Spalte namens *ColumnName*, deren Werte *Start*, *Start* + *Step*, ... bis zu und einschließlich *Stop* sind.

**Beispiel**

```AIQL
range Steps from 1 to 8 step 3
```

Eine Tabelle mit einer einzelnen Spalte namens `Steps` vom Typ `long`, deren Werte `1`, `4` und `7` sind.

**Beispiel**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Eine Tabelle von Mitternacht der vergangenen sieben Tage. Die bin (floor)-Funktion reduziert jede Zeit auf den Anfang des Tages.

**Beispiel**

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Zeigt, wie mit dem `range`-Operator eine kleine Ad-hoc-Dimensionstabelle erstellt werden kann, mit der anschließend Nullen eingeführt werden, wenn die Quelldaten keine Werte aufweisen.

## reduce-Operator

    exceptions | reduce by outerMessage

Versucht, ähnliche Datensätze zu gruppieren. Der Operator gibt für jede Gruppe das `Pattern` aus, das die Gruppe wahrscheinlich am besten beschreibt, sowie die `Count` der Datensätze in dieser Gruppe.


![](./media/app-analytics-queries/reduce.png)

**Syntax**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumente**

* *ColumnName:* Die zu untersuchende Spalte. Sie muss vom Typ „Zeichenfolge“ sein.
* *Threshold:* Ein Wert im Bereich {0..1}. Der Standardwert ist 0,001. Bei großen Eingaben sollte der Schwellenwert klein sein. 

**Rückgabe**

Zwei Spalten, `Pattern` und `Count`. In vielen Fällen ist das Muster ein vollständiger Wert aus der Spalte. In einigen Fällen kann es allgemeine Begriffe identifizieren und die Variablenteile durch „*“ ersetzen.

Das Ergebnis von `reduce by city` kann z. B. Folgendes enthalten:

|Muster | Count |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moskau | 3726 |
| * -auf- * | 2730 |
| Paris | 27163 |


## render-Anweisung

    T | render [ table | timechart  | barchart | piechart ]

„Render“ weist die Darstellungsschicht an, wie die Tabelle angezeigt werden soll. Es sollte das letzte Element der Pipe sein. Es ist eine praktische Alternative zum Verwenden der Steuerelemente in der Anzeige, und Sie können eine Abfrage mit einer bestimmten Präsentationsmethode speichern.


## sort-Operator 

    T | sort by country asc, price desc

Sortiert die Zeilen der Eingabetabelle in Reihenfolge nach einer oder mehreren Spalten.

**Alias** `order`

**Syntax**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumente**

* *T:* Die zu sortierende Tabelleneingabe.
* *Column:* Spalte von *T*, nach der sortiert werden soll. Der Typ der Werte muss „Numerisch“, „Datum“, „Uhrzeit“ oder „Zeichenfolge“ sein.
* `asc` Sortierung in aufsteigender Reihenfolge von niedrig nach hoch. Die Standardeinstellung ist `desc`, von hoch zu niedrig.

**Beispiel**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Alle Zeilen in der Tabelle „Traces“ mit einer bestimmten `ActivityId`, sortiert nach ihrem Zeitstempel.

## summarize-Operator

Erzeugt eine Tabelle, die den Inhalt der Eingabetabelle aggregiert.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Tabelle, die die Anzahl, durchschnittliche Anforderungsdauer und Menge von Städten in jedem Land zeigt. Die Ausgabe enthält eine Zeile für jedes unterschiedliche Land. Die Ausgabespalten zeigen die Anzahl, durchschnittliche Dauer, Städte und das Land. Alle anderen Eingabespalten werden ignoriert.


    T | summarize count() by price_range=bin(price, 10.0)

Eine Tabelle, die zeigt, wie viele Elemente in jedem Intervall [0, 10,0][10,0, 20,0] usw. Preise aufweisen. In diesem Beispiel ist eine Spalte für die Anzahl und eine für den Preisbereich vorhanden. Alle anderen Eingabespalten werden ignoriert.

[Weitere Beispiele](app-analytics-aggregations.md).



**Syntax**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumente**

* *Column:* Optionaler Name für eine Ergebnisspalte. Nimmt standardmäßig den vom Ausdruck abgeleiteten Namen an.
* *Aggregation:* Ein Aufruf einer [Aggregationsfunktion](app-analytics-aggregations.md) wie z. B. `count()` oder `avg()` mit Spaltennamen als Argumente. Siehe die [Liste der Aggregationsfunktionen](app-analytics-aggregations.md).
* *GroupExpression:* Ein Ausdruck für die Spalten, der einen Satz von unterschiedlichen Werten bereitstellt. Normalerweise handelt es sich entweder um einen Spaltennamen, der bereits einen eingeschränkten Satz von Werten bereitstellt, oder um `bin()` mit einer numerischen Spalte oder Zeitspalte als Argument. 

Wenn Sie einen numerischen Ausdruck oder Zeitausdruck ohne `bin()` bereitstellen, wendet die AI-Analyse ihn automatisch mit einem Intervall von `1h` für Uhrzeiten oder von `1.0` für Zahlen an.

Wenn Sie keine *GroupExpression* angeben, wird die gesamte Tabelle in einer einzelnen Ausgabezeile zusammengefasst.



**Rückgabe**

Die Eingabezeilen sind in Gruppen mit den gleichen Werten der `by`-Ausdrücke angeordnet. Anschließend werden die angegebenen Aggregationsfunktionen über jede Gruppe berechnet, dabei wird eine Zeile für jede Gruppe erzeugt. Das Ergebnis enthält die `by`-Spalten und auch mindestens eine Spalte für jedes berechnete Aggregat. (Einige Aggregationsfunktionen geben mehrere Spalten zurück.)

Das Ergebnis umfasst ebenso viele Zeilen, wie unterschiedliche Kombinationen von `by`-Werten vorhanden sind. Wenn Sie Zusammenfassungen über Bereiche von numerischen Werten erstellen möchten, verwenden Sie `bin()`, um Bereiche auf diskrete Werte zu reduzieren.

**Hinweis**

Auch wenn Sie beliebige Ausdrücke für die Aggregation und Gruppierung von Ausdrücken bereitstellen können, ist es effizienter, einfache Spaltennamen zu verwenden oder `bin()` auf eine numerische Spalte anzuwenden.



## take-Operator

Alias von [limit](#limit-operator)


## top-Operator

    T | top 5 by Name desc

Gibt die ersten *N* Datensätze nach den angegebenen Spalten geordnet zurück.


**Syntax**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Argumente**

* *NumberOfRows:* Die zurückzugebende Anzahl der Zeilen von *T*.
* *Sort\_expression:* Ein Ausdruck, nach dem die Zeilen sortiert werden. Dies ist in der Regel nur ein Spaltenname. Sie können mehrere „Sort\_expression“-Angaben machen.
* Möglicherweise wird `asc` oder `desc` (Standard) angezeigt, um zu steuern, ob die tatsächliche Auswahl am „unteren“ oder „oberen“ Ende des Bereichs erfolgt.


**Tipps**

`top 5 by name` entspricht oberflächlich betrachtet `sort by name | take 5`. Allerdings ist die Ausführung schneller, und es werden immer sortierte Ergebnisse zurückgegeben; dies ist mit `take` nicht garantiert.


## union-Operator

     Table1 | union Table2, Table3

Verwendet mindestens zwei Tabellen und gibt die Zeilen aller Tabellen zurück.

**Syntax**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumente**

* *Table1*, *Table2* ...
 *  Der Name einer Tabelle, wie z. B. `events`; oder
 *  Ein Abfrageausdruck, wie z. B. `(events | where id==42)`
 *  Ein Satz von Tabellen, die mit einem Platzhalterzeichen angegeben sind. `E*` bildet beispielsweise die Union aller Tabellen in der Datenbank, deren Namen mit `E` beginnen .
* `kind`: 
 * `inner` – Das Ergebnis enthält die Teilmenge der Spalten, die alle Eingabetabellen gemeinsam haben.
 * `outer` – Das Ergebnis enthält alle Spalten, die in einer der Eingaben vorkommen. Zellen, die nicht durch eine Eingabezeile definiert wurden, werden auf `null` gesetzt.
* `withsource=`*ColumnName:* Wenn diese Einstellung festgelegt ist, enthält die Ausgabe eine Spalte namens *ColumnName*, deren Wert angibt, aus welchen Quelltabellen die einzelnen Zeilen stammen.

**Rückgabe**

Eine Tabelle mit derselben Anzahl von Zeilen, wie in allen Eingabetabellen vorhanden sind.

**Beispiel**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Union aller Tabellen, deren Namen mit „tt“ beginnen.


**Beispiel**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Die Anzahl der unterschiedlichen Benutzer, die während des vergangenen Tages entweder ein `exceptions`- oder ein `traces`-Ereignis erzeugt haben. Im Ergebnis gibt die Spalte „SourceTable“ entweder „Abfrage“ oder „Befehl“ an.

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Mit dieser effizienteren Version wird dasselbe Ergebnis erzielt. Jede Tabelle wird vor dem Erstellen der Union gefiltert.

## where-Operator

     T | where fruit=="apple"

Filtert eine Tabelle auf die Teilmenge der Zeilen, die ein Prädikat erfüllen.

**Alias** `filter`

**Syntax**

    T | where Predicate

**Argumente**

* *T:* Die tabellarische Eingabe, deren Datensätze gefiltert werden sollen.
* *Predicate:* Ein `boolean`-[Ausdruck](app-analytics-scalars.md#boolean) über Spalten von *T*. Er wird für jede Zeile in *T* ausgewertet.

**Rückgabe**

Zeilen in *T*, für die *Predicate* `true` ist.

**Tipps**

So erzielen Sie die optimale Leistung:

* **Verwenden Sie einfache Vergleiche** zwischen Spaltennamen und Konstanten. („Konstant“ bedeutet konstant innerhalb der Tabelle – d. h. `now()` und `ago()` sind ebenso in Ordnung wie skalare Werte, die mithilfe einer [`let`-Anweisung](app-analytics-syntax.md#let-statements) zugewiesen werden.)

    Beispielsweise wird `where Timestamp >= ago(1d)` gegenüber `where floor(Timestamp, 1d) == ago(1d)` bevorzugt.

* **Einfachste Begriffe zuerst**: Wenn Sie mehrere mit `and` verbundene Klauseln haben, stellen Sie die Klauseln, die nur eine Spalte umfassen, an den Anfang. `Timestamp > ago(1d) and OpId == EventId` ist also besser als anders herum.


**Beispiel**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Datensätze, die nicht älter als 1 Stunde sind, aus der Quelle namens „Kuskus“ stammen und zwei Spalten mit dem gleichen Wert aufweisen.

Beachten Sie, dass wir den Vergleich zwischen zwei Spalten an das Ende stellen, da der Index nicht genutzt werden kann und ein Scan erzwungen wird.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->