<properties 
	pageTitle="Skalare Ausdrücke in der Application Insights-Analyse" 
	description="Zahlen, Zeichenfolgen, dynamische Ausdrücke und Typen in der Application Insights-Analyse, dem leistungsfähigen Suchtool für Application Insights." 
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
	ms.date="03/05/2016" 
	ms.author="awills"/>


 
# Skalare Ausdrücke in der Application Insights-Analyse


[Application Insights-Analyse](app-analytics.md) ist ein leistungsfähiges Suchmodul für Ihre [Application Insights](app-insights-overview.md)-Telemetrie. Auf diesen Seiten wird die Abfragesprache AIQL der Application Insights-Analyse beschrieben.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) <br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull) <br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [split](#split) | [sqrt](#sqrt) <br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



„Skalar“ bezieht sich auf Werte wie Zahlen oder Zeichenfolgen, die eine einzelne Zelle in einer AIQL-Tabelle einnehmen können. Skalare Ausdrücke werden aus skalaren Funktionen und Operatoren erstellt und als skalare Werte ausgewertet. `sqrt(score)/100 > target+2` ist ein skalarer Ausdruck.

„Skalar“ umfasst auch Arrays und zusammengesetzte Objekte, die ebenfalls in einer einzelnen Datenbankzelle gespeichert werden können.

Skalare Ausdrücke unterscheiden sich von [Abfragen](app-analytics-queries.md), deren Ergebnisse als Tabellen dargestellt werden.

## Skalare

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

Die unterstützten Typen sind:

| Typ | Weitere(r) Name(n) | Entsprechender .NET-Typ |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Typumwandlungen

Sie können einen Typ in einen anderen umwandeln. Wenn die Konvertierung sinnvoll ist, wird sie im Allgemeinen auch funktionieren:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### Skalare Vergleiche

||
---|---
`<` |Kleiner
`<=`|Kleiner oder gleich
`>` |Größer
`>=`|Größer oder gleich
`<>`|Not Equals
`!=`|Not Equals 
`in`| Rechter Operand ist ein (dynamisches) Array, und linker Operand entspricht einem seiner Elemente.
`!in`| Rechter Operand ist ein (dynamisches) Array, und linker Operand entspricht nicht einem seiner Elemente.




### gettype

**Rückgabe**

Eine Zeichenfolge, die den zugrunde liegenden Speichertyp des einzigen Arguments darstellt. Dies ist besonders dann nützlich, wenn Sie über Werte vom Typ `dynamic` verfügen: In diesem Fall zeigt `gettype()`, wie ein Wert codiert ist.

**Beispiele**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### hash

**Syntax**

    hash(source [, mod])

**Argumente**

* *source*: Der Quellskalar, anhand dessen der Hash berechnet wird.
* *mod*: Der Modulowert, der auf das Hashergebnis angewendet werden soll.

**Rückgabe**

Der xxhash (long)-Wert des angegebenen Skalars, Modulo des angegebenen mod-Werts (sofern angegeben).

**Beispiele**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

Die `iff()`-Funktion wertet das erste Argument (Prädikat) aus und gibt entweder den Wert der zweiten oder dritten Argumente zurück, abhängig davon, ob das Prädikat `true` oder `false` ist. Die zweiten und dritten Argumente müssen vom gleichen Typ sein.

**Syntax**

    iff(predicate, ifTrue, ifFalse)


**Argumente**

* *predicate:* Ein Ausdruck, der als `boolean`-Wert ausgewertet wird.
* *ifTrue:* Ein Ausdruck, der ausgewertet und dessen Wert von der Funktion zurückgegeben wird, wenn *predicate* als `true` ausgewertet wird.
* *ifFalse:* Ein Ausdruck, der ausgewertet und dessen Wert von der Funktion zurückgegeben wird, wenn *predicate* als `false` ausgewertet wird.

**Rückgabe**

Diese Funktion gibt den Wert von *ifTrue* zurück, wenn *predicate* als `true` ausgewertet wird, oder anderenfalls den Wert von *ifFalse*.

**Beispiel**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Akzeptiert ein einzelnes Argument und gibt an, ob es null ist.

**Syntax**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Rückgabe**

True oder False, je nachdem, ob ist der Wert null oder nicht null ist.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false

**Beispiel**

    T | where isnotnull(PossiblyNull) | count

Beachten Sie, dass es andere Möglichkeiten gibt, diesen Effekt zu erreichen:

    T | summarize count(PossiblyNull)




## Boolean 

### Boolesche Literale

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Boolesche Operatoren

	and 
    or 

    

## Zahlen

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Numerische Literale

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Arithmetische Operatoren

|| |
|---|-------------|
| + | Hinzufügen |
| - | Subtrahieren | | * | Multiplizieren | | / | Dividieren | | % | Modulo | || |`<` |Kleiner |`<=`|Kleiner oder gleich |`>` |Größer |`>=`|Größer oder gleich |`<>`|Ungleich |`!=`|Ungleich




### bin

Rundet Werte auf eine ganze Zahl ab, die ein Vielfaches der angegebenen bin-Größe ist. Wird häufig in der [`summarize by`](app-analytics-queries.md#summarize-operator)-Abfrage verwendet. Wenn Sie über einen verstreuten Satz von Werten verfügen, werden sie zu einem kleineren Satz bestimmter Werte gruppiert.

Alias `floor`.

**Syntax**

     bin(value, roundTo)

**Argumente**

* *value:* Eine Zahl, ein Datum oder ein Zeitraum. 
* *roundTo:* Die bin-Größe. Eine Zahl, ein Datum oder ein Zeitraum, die den Wert (*value*) teilen. 

**Rückgabe**

Das nächste Vielfache von *roundTo* unter dem Wert (*value*).
 
    (toint((value/roundTo)-0.5)) * roundTo

**Beispiele**

Ausdruck | Ergebnis
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


Der folgende Ausdruck berechnet ein Histogramm der Dauer mit einer Bucketgröße von 1 Sekunde:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

Ein Alias für [`bin()`](#bin).


### rand

Ein Zufallszahlengenerator.

* `rand()` – eine reelle Zahl zwischen 0,0 und 1,0
* `rand(n)` – eine ganze Zahl zwischen 0 und n-1




### sqrt

Die Quadratwurzelfunktion.

**Syntax**

    sqrt(x)

**Argumente**

* *x:* eine reelle Zahl >= 0.

**Rückgabe**

* Eine positive Zahl, sodass `sqrt(x) * sqrt(x) == x`
* `null`, wenn das Argument negativ ist oder nicht in einen `real`-Wert konvertiert werden kann. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Datum und Uhrzeit


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### Datum und Uhrzeit – Literale

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Zeiten werden immer in UTC angegeben. Durch das Auslassen des Datums wird eine Zeit am heutigen Tag angegeben.
`now()`|Die aktuelle Zeit.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 Tage
`1.5h`|1,5 Stunden 
`30m`|30 Minuten
`10s`|10 Sekunden
`0.1s`|0,1 Sekunde
`100ms`| 100 Millisekunden
`10microsecond`|
`1tick`|100 ns
`time("15 seconds")`|
`time("2")`| 2 Tage
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Datum und Uhrzeit – Ausdrücke

Ausdruck |Ergebnis
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Kleiner
`<=`|Kleiner oder gleich
`>` |Größer
`>=`|Größer oder gleich
`<>`|Not Equals
`!=`|Not Equals 




### ago

Subtrahiert den angegebenen Zeitraum von der aktuellen UTC-Uhrzeit. Wie `now()` kann diese Funktion mehrmals in einer Anweisung verwendet werden, und der UTC-Uhrzeit, auf die verwiesen wird, ist für alle Instanziierungen identisch.

**Syntax**

    ago(a_timespan)

**Argumente**

* *a\_timespan*: Intervall, das von der aktuellen UTC-Uhrzeit (`now()`) subtrahiert werden soll.

**Rückgabe**

    now() - a_timespan

**Beispiel**

Alle Zeilen mit einem Zeitstempel der letzten Stunde:

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Die Anzahl Tage (als ganze Zahl) seit dem vorherigen Sonntag, als eine `timespan`.

**Syntax**

    dayofweek(a_date)

**Argumente**

* `a_date`: Ein `datetime`.

**Rückgabe**

Der `timespan` seit Mitternacht zu Beginn des vorangehenden Sonntags, abgerundet auf die Anzahl von Tagen als ganze Zahl.

**Beispiele**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

Rufen Sie die Monatsnummer (1-12) aus einem datetime-Wert ab.

**Beispiel**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Rufen Sie das Jahr aus einem datetime-Wert ab.

**Beispiel**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

Die aktuelle UTC-Uhrzeit, die optional durch einen angegebenen Zeitraum versetzt wird. Diese Funktion kann mehrmals in einer Anweisung verwendet werden, und die Uhrzeit, auf die verwiesen wird, ist für alle Instanzen identisch.

**Syntax**

    now([offset])

**Argumente**

* *offset:* Ein `timespan`, der der aktuellen UTC-Uhrzeit hinzugefügt wird. Standard: 0.

**Rückgabe**

Die aktuelle UTC-Uhrzeit als ein `datetime`.

    now() + offset

**Beispiel**

Bestimmt das Intervall seit dem vom Prädikat identifizierten Ereignis:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

Der Beginn des Monats, der das Datum enthält.

### startofyear

    startofyear(date)

Der Beginn des Jahres, das das Datum enthält.


### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## String

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Zeichenfolgenliterale

Die Regeln sind mit JavaScript identisch.

Zeichenfolgen können entweder in einfachen oder doppelten Anführungszeichen eingeschlossen sein.

Es wird ein umgekehrter Schrägstrich (``) verwendet, um Zeichen wie z. B. `\t` (Tab), `\n` (Zeilenvorschub) und Instanzen der einschließenden Anführungszeichen mit einem Escapezeichen zu versehen.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Verborgene Zeichenfolgenliterale

Verborgene Zeichenfolgenliterale sind Zeichenfolgen, die die AI-Analyse bei der Ausgabe der Zeichenfolge ausblendet (z. B. bei der Ablaufverfolgung). Beim Ausblenden werden alle verborgenen Zeichen durch ein Start (`*`)-Zeichen ersetzt.

Stellen Sie zum Erstellen eines verborgenen Zeichenfolgenliterals `h` oder „H“ voran. Beispiel:

```
h'hello'
h@'world' 
h"hello"
```

### Zeichenfolgenvergleiche

Operator|Beschreibung|Groß-/Kleinschreibung|Beispiel für „True“
---|---|---|---
`==`|Equals |Ja| `"aBc" == "aBc"`
`<>`|Ungleich|Ja| `"abc" <> "ABC"`
`=~`|Equals |Nein| `"abc" =~ "ABC"`
`!~`|Ungleich |Nein| `"aBc" !~ "xyz"`
`has`|Rechte Seite (RS) ist ein ganzer Begriff innerhalb der linken Seite (LS)|Nein| `"North America" has "america"`
`!has`|RS ist kein vollständiger Begriff innerhalb der LS|Nein|`"North America" !has "amer"` 
`contains` | RS tritt als Untersequenz der LS auf|Nein| `"FabriKam" contains "BRik"`
`!contains`| RS tritt nicht in LS auf|Nein| `"Fabrikam" !contains "xyz"`
`containscs` | RS tritt als Untersequenz der LS auf|Ja| `"FabriKam" contains "Kam"`
`!containscs`| RS tritt nicht in LS auf|Ja| `"Fabrikam" !contains "Kam"`
`startswith`|RS ist eine anfängliche Untersequenz der LS|Nein|`"Fabrikam" startswith "fab"`
`matches regex`|LS enthält eine Übereinstimmung für RS|Ja| `"Fabrikam" matches regex "b.*k"`


Verwenden Sie `has` oder `in`, wenn Sie auf das Vorkommnis eines gesamten lexikalischen Begriffs hin testen, sprich ein Symbol oder ein alphanumerisches Wort, begrenzt durch nicht-alphanumerische Zeichen oder den Anfang oder das Ende des Felds. `has` wird schneller ausgeführt als `contains` oder `startswith`. Die erste dieser Abfragen wird schneller ausgeführt:

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.

**Syntax**

    countof(text, search [, kind])

**Argumente**

* *text:* Eine Zeichenfolge.
* *search:* Die einfache Zeichenfolge oder der [reguläre Ausdruck](app-analytics-reference.md#regular-expressions), die bzw. der in *text* abgeglichen werden soll.
* *kind:* `"normal"|"regex"` Standard `normal`. 

**Rückgabe**

Angabe, wie oft die Suchzeichenfolge im Container abgeglichen werden kann. Einfache Zeichenfolgenübereinstimmungen überlappen sich möglicherweise; bei regex-Übereinstimmungen ist dies nicht Fall.

**Beispiele**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (nicht 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Rufen Sie eine Übereinstimmung für einen [regulären Ausdruck](app-analytics-reference.md#regular-expressions) aus einer Textzeichenfolge ab. Optional konvertiert es dann die extrahierte Teilzeichenfolge in den angegebenen Typ.

**Syntax**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumente**

* *regex:* Ein [regulärer Ausdruck](app-analytics-reference.md#regular-expressions).
* *captureGroup:* Eine positive `int`-Konstante, die die zu extrahierende Erfassungsgruppe angibt. 0 steht für die vollständige Übereinstimmung, 1 für den mit der ersten „("Klammer")“ übereinstimmenden Wert im regulären Ausdruck, 2 oder höher für nachfolgende Klammern.
* *text:* Ein zu suchender `string`.
* *typeLiteral:* Ein optionaler Typliteral (z. B. `typeof(long)`). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert. 

**Rückgabe**

Wenn mit *regex* eine Übereinstimmung in *text* gefunden wird: die mit der angegebenen Erfassungsgruppe *captureGroup* abgeglichene Teilzeichenfolge, optional konvertiert in *typeLiteral*.

Wenn keine Übereinstimmung vorhanden ist oder die Konvertierung fehlschlägt: `null`.

**Beispiele**

Die Beispielzeichenfolge `Trace` wird auf eine Definition für `Duration` hin durchsucht. Die Übereinstimmung wird in `real` konvertiert und dann mit einer Zeitkonstanten (`1s`) multipliziert, damit `Duration` den Datentyp `timespan` erhält. In diesem Beispiel entspricht dies 123,45 Sekunden:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Diesem Beispiel entspricht `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

„True“, wenn das Argument eine leere Zeichenfolge oder null ist. Siehe auch [isnull](#isnull).


**Syntax**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Rückgabe**

Gibt an, ob das Argument eine leere Zeichenfolge oder isnull ist.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false


**Beispiel**


    T | where isempty(fieldName) | count




### replace

Ersetzen Sie alle regex-Übereinstimmungen mit einer anderen Zeichenfolge.

**Syntax**

    replace(regex, rewrite, text)

**Argumente**

* *regex:* Der [reguläre Ausdruck](https://github.com/google/re2/wiki/Syntax) zum Durchsuchen von *text*. Er kann Erfassungsgruppen in „('Klammern')“ enthalten. 
* *rewrite:* Der Ersatz-Regex für jede Übereinstimmung, die mit *matchingRegex* erzielt wurde. Verwenden Sie `\0`, um auf die gesamte Übereinstimmung zu verweisen, `\1` für die erste Erfassungsgruppe, `\2` usw. für nachfolgende Erfassungsgruppen.
* *text:* Eine Zeichenfolge.

**Rückgabe**

*text* nach dem Ersetzen aller Übereinstimmungen von *regex* mit Auswertungen von *rewrite*. Übereinstimmungen überlappen sich nicht.

**Beispiel**

Diese Anweisung:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Bietet die folgenden Ergebnisse:

| x | str | replaced|
|---|---|---|
| 1 | Nummer lautet 1.000000 | Nummer lautete 1.000000|
| 2 | Nummer lautet 2.000000 | Nummer lautete 2.000000|
| 3 | Nummer lautet 3.000000 | Nummer lautete 3.000000|
| 4 | Nummer lautet 4.000000 | Nummer lautete 4.000000|
| 5 | Nummer lautet 5.000000 | Nummer lautete 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Teilt eine angegebene Zeichenfolge gemäß einem angegebenen Trennzeichen, und gibt ein Zeichenfolgenarray mit den enthaltenen untergeordneten Zeichenfolgen zurück. Optional kann eine bestimmte Teilzeichenfolge zurückgegeben werden, sofern vorhanden.

**Syntax**

    split(source, delimiter [, requestedIndex])

**Argumente**

* *source*: Die Quellzeichenfolge, die gemäß dem angegebenen Trennzeichen geteilt wird.
* *delimiter*: Das Trennzeichen, das zum Teilen der Quellzeichenfolge verwendet wird.
* *requestedIndex*: Ein optionaler nullbasierter Index `int`. Sofern angegeben, enthält das zurückgegebene Zeichenfolgenarray die angeforderte Teilzeichenfolge, sofern vorhanden. 

**Rückgabe**

Ein Zeichenfolgenarray, das die Teilzeichenfolgen der angegebenen Quellzeichenfolge enthält, die durch das angegebene Trennzeichen getrennt sind.

**Beispiele**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### strcat

    strcat("hello", " ", "world")

Verkettet zwischen 1 und 16 Argumente, bei denen es sich um Zeichenfolgen handeln muss.

### strlen

    strlen("hello") == 5

Länge einer Zeichenfolge.

### substring

    substring("abcdefg", 1, 2) == "bc"

Extrahiert eine Teilzeichenfolge aus einer angegebenen Quellzeichenfolge, beginnend bei einem angegebenen Index. Optional kann die Länge der angeforderten Teilzeichenfolge angegeben werden.

**Syntax**

    substring(source, startingIndex [, length])

**Argumente**

* *source:* Die Quellzeichenfolge, aus der die Teilzeichenfolge entnommen wird.
* *startingIndex:* Die nullbasierte Anfangszeichenposition der angeforderten Teilzeichenfolge.
* *length:* Ein optionaler Parameter, der zur Angabe der angeforderten Anzahl von Zeichen in der Teilzeichenfolge verwendet werden kann. 

**Rückgabe**

Eine Teilzeichenfolge aus der angegebenen Zeichenfolge. Die Teilzeichenfolge beginnt bei der startingIndex (nullbasierten) Zeichenposition und wird bis zum Ende der Zeichenfolge oder der Längenzeichen fortgesetzt, sofern angegeben.

**Beispiele**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

Konvertiert eine Zeichenfolge in Kleinbuchstaben.

### toupper

    toupper("hello") == "HELLO"

Konvertiert eine Zeichenfolge in Großbuchstaben.



## GUIDs

    guid(00000000-1111-2222-3333-055567f333de)


## Arrays und Objekte – dynamische Typen

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Hier ist das Ergebnis einer Abfrage für eine Application Insights-Ausnahme. Der Wert unter `details` ist ein Array.

![](./media/app-analytics-scalars/310.png)

**Indizierung:** Arrays und Objekte werden wie in JavaScript indiziert:

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Verwenden Sie jedoch `arraylength` und andere AIQL-Funktionen (nicht „.length“!)

**Umwandlung** In einigen Fällen ist es erforderlich, ein Element umzuwandeln, das Sie aus einem Objekt extrahieren, da der Typ variieren kann. `summarize...to` benötigt beispielsweise einen bestimmten Typ:

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literale** Schreiben Sie sie zum Erstellen eines expliziten Arrays oder eines Eigenschaftenbehälter-Objekts als JSON-Zeichenfolge und -Typumwandlung:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** Verwenden Sie mvexpand, um die Eigenschaften eines Objekts in separate Zeilen auseinander zu ziehen:

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-analytics-scalars/410.png)


**treepath:** Zum Suchen aller Pfade in einem komplexen Objekt:

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

**buildschema:** Zum Suchen des minimalen Schemas, das alle Werte des Ausdrucks in der Tabelle zulässt:

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



### Array- und Objektliterale

Verwenden Sie zum Erstellen eines dynamischen Literals `parsejson` (Alias `todynamic`) mit einem JSON-Zeichenfolgenargument:

* `parsejson('[43, 21, 65]')` – ein Array von Zahlen
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` – ein einzelner Wert vom dynamischen Typ mit einer Zahl
* `parsejson('"21"')` – ein einzelner Wert vom dynamischen Typ mit einer Zeichenfolge

Beachten Sie, dass bei JSON, im Gegensatz zu JavaScript, die Verwendung doppelter Anführungszeichen (`"`) um Zeichenfolgen unbedingt erforderlich ist. Daher ist es im Allgemeinen einfacher ein JSON-codiertes Zeichenfolgenliteral mit einfachen Anführungszeichen (`'`) zu kennzeichnen.

In diesem Beispiel wird ein dynamischer Wert erstellt, und anschließend werden dessen Felder verwendet:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### Operatoren und Funktionen auf dynamischen Typen

|||
|---|---|
| *value* `in` *array*| „True“, wenn ein Element von *array* == *value*<br/>`where City in ('London', 'Paris', 'Rome')`.
| *value* `!in` *array*| „True“, wenn kein Element von *array* == *value*.
|[`arraylength(`array`)`](#arraylength)| Null, wenn es sich nicht um ein Array handelt.
|[`extractjson(`path,object`)`](#extractjson)|Verwendet den Pfad zum Navigieren in das Objekt.
|[`parsejson(`source`)`](#parsejson)| Wandelt eine JSON-Zeichenfolge in ein dynamisches Objekt um.
|[`range(`from,to,step`)`](#range)| Ein Array von Werten
|[`mvexpand` listColumn](app-analytics-queries.md#mvexpand-operator) | Repliziert eine Zeile für jeden Wert in einer Liste in eine angegebene Zelle.
|[`summarize buildschema(`column`)`](app-analytics-queries.md#summarize-operator) |Leitet das Typschema aus dem Spalteninhalt ab.
|[`summarize makelist(`column`)` ](app-analytics-queries.md#summarize-operator)| Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array.
|[`summarize makeset(`column`)`](app-analytics-queries.md#summarize-operator) | Reduziert die Zeilengruppen und setzt die Werte der Spalte in ein Array ohne Duplizierung.

### Dynamische Objekte in Let-Klauseln


[Let-Klauseln](app-analytics-queries.md#let-clause) speichern dynamische Werte als Zeichenfolgen, sodass diese beiden Klauseln gleichwertig sind und beide vor der Verwendung `parsejson` (oder `todynamic`) benötigen:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

Die Anzahl der Elemente in einem dynamischen Array.

**Syntax**

    arraylength(array)

**Argumente**

* *array:* Ein `dynamic` Wert.

**Rückgabe**

Die Anzahl der Elemente in *array*, oder `null`, wenn *Array* kein Array ist.

**Beispiele**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Rufen Sie ein angegebenes Element aus einem JSON-Text mit einem Pfadausdruck ab. Konvertieren Sie optional die extrahierte Zeichenfolge in einen bestimmten Typ.


**Syntax**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Rückgabe**

Diese Funktion führt eine JsonPath-Abfrage in dataSource durch, die eine gültige JSON-Zeichenfolge enthält. Optional wird dieser Wert, je nach drittem Argument, in einen anderen Typ konvertiert.



**Beispiel**

Die [Klammer]-Notation und Punktnotation sind gleichwertig:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### JSON Path-Ausdrücke

|||
|---|---|
|`$`|Stammobjekt|
|`@`|Aktuelles Objekt|
|`.` oder `[ ]` | Untergeordnet|
|`[ ]`|Array-Subscript|

*(Derzeit werden keine Platzhalter, Rekursionen, Vereinigungen oder Segmente implementiert.)*


**Leistungstipps**

* Wenden Sie vor der Verwendung von `extractjson()` where-Klauseln an.
* Erwägen Sie stattdessen den Einsatz einer regulären Ausdrucksübereinstimmung mit [extract](#extract). Dies kann sehr viel schneller ausgeführt werden und ist effektiv, wenn die JSON aus einer Vorlage erstellt wird.
* Verwenden Sie `parsejson()`, wenn Sie mehr als einen Wert aus der JSON extrahieren müssen.
* Ziehen Sie es in Betracht, die JSON zum Zeitpunkt der Erfassung zu analysieren, indem Sie den Typ der Spalte als dynamisch deklarieren.



### parsejson

Interpretiert eine `string` als einen [JSON-Wert](http://json.org/) und gibt den Wert als `dynamic` zurück. Dies ist der Nutzung von `extractjson()` vorzuziehen, wenn Sie mehr als ein Element eines zusammengesetzten JSON-Objekts extrahieren müssen.

**Syntax**

    parsejson(json)

**Argumente**

* *json:* Ein JSON-Dokument.

**Rückgabe**

Ein Objekt vom Typ `dynamic`, angegeben durch *json*.

**Beispiel**

Für das folgende Beispiel gilt, wenn `context_custom_metrics` eine `string` ist, die folgendermaßen aussieht:

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

dann ruft das folgende AIQL-Fragment den Wert des `duration`-Slots im Objekt ab und daraus zwei Slots, `duration.value` und `duration.min` (bzw. `118.0` und `110.0`).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

Die `range()`-Funktion (nicht zu verwechseln mit dem `range`-Operator) erzeugt ein dynamisches Array mit einer Reihe gleichmäßig verteilter Werte.

**Syntax**

    range(start, stop, step)

**Argumente**

* *start:* Der Wert des ersten Elements im resultierenden Array. 
* *stop:* Der Wert des letzten Elements im resultierenden Array oder der kleinste Wert, der größer ist als das letzte Element in dem resultierenden Array und in einem Vielfachen (ganze Zahl) von *step* von *start*.
* *step:* Die Differenz zwischen zwei aufeinander folgenden Elementen des Arrays.

**Beispiele**

Das folgende Beispiel gibt `[1, 4, 7]` zurück:

```AIQL
range(1, 8, 3)
```

Das folgende Beispiel gibt ein Array mit allen Tagen im Jahr 2015 zurück:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Konvertiert eine Zeichenfolge in einen dynamischen Wert.

### treepath

    treepath(dynamic_object)

Listet alle Path-Ausdrücke auf, die Verzweigungen in einem dynamischen Objekt identifizieren.

**Rückgabe**

Ein Array von Path-Ausdrücken.

**Beispiele**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Beachten Sie, dass „[0]“ auf das Vorhandensein eines Arrays hinweist, aber nicht den von einem bestimmten Pfad verwendeten Index angibt.



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->