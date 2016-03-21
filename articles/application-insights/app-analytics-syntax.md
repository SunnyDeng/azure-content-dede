<properties 
	pageTitle="Anweisungen in Application Analytics" 
	description="Abfragen, Ausdrücke und let-Anweisungen in Application Analytics, das leistungsfähige Suchtool für Application Insights." 
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
	ms.date="03/01/2016" 
	ms.author="awills"/>


 
# Anweisungen in Application Analytics

[Application Analytics](app-analytics.md) ist ein leistungsfähiges Suchmodul für Ihre [Application Insights](app-insights-overview.md)-Telemetrie. Auf diesen Seiten wird die Abfragesprache CSL von Application Analytics beschrieben.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Datenmodell

In CSL:

* Eine *Datenbank* enthält keine oder mehrere benannte *Tabellen.*
* Eine *Tabelle* enthält:
 * Eine oder mehrere benannte*Spalten.* Jede Spalte besitzt einen *Typ.*
 * Eine oder mehrere *Zeilen.*
* Jede Zeile enthält eine oder mehrere *Zellen,* eine für jede Spalte der Tabelle.
* Eine Zelle kann einen Wert des Typs der Spalte enthalten oder `null`.


## Entitätsnamen

Jede Spalte, Tabelle oder Datenbank verfügt weist in ihrem Container einen eindeutigen Namen auf.

Verweisen auf eine Entität entweder anhand des Namens (wenn der Kontext eindeutig ist) oder sie wird durch den Container qualifiziert. `MyColumn` kann z. B. auch als `MyTable.MyColumn` oder `MyDatabase.MyTable.MyColumn` referenziert werden.

Namen können bis zu 1024 Zeichen lang sein. Die Groß-/Kleinschreibung wird beachtet, und sie können Buchstaben, Ziffern und Unterstriche (`_`) enthalten.

Darüber hinaus können Namen in Anführungszeichen eingeschlossen werden, damit sie andere Zeichen enthalten können. Beispiel:

|||
|---|---|
|`['path\\file\n\'x\'']` | Verwenden Sie `` für Zeichen als Escapezeichen|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| Keine Escapezeichen - `` ist literal|
|`[@"\now & then"]` | |
|`[where]` | Verwenden eines Programmiersprachenschlüsselworts als Name|

Namen können auch mit dem Namen der Datenbank qualifiziert werden (siehe [datenbankübergreifende Abfragen](#cross-database-queries))

```
database("MyDb").Table
```


## Anweisungen

Es gibt vier Arten von Anweisungen in CSL:

### Datenabfragen
  
Schreibgeschützte Abfragen der Daten, die in Application Analytics gespeichert sind. Beispiel:

* `event` - Alle Datensätze in der Tabelle „event“ zurückgeben
* `event | count` - Die Anzahl der Datensätze in „event“ zurückgeben

    
## Let-Anweisungen

#### Übersicht
Es ist möglich, durch eine oder mehrere let-Anweisungen als Präfix für eine Datenabfrageanweisung festzulegen. Diese erlauben, einen Namen (ein gültiger Entitätsname) an einen Ausdruck zu binden. Durch eine let-Anweisung definierte Namen können dann einmal oder mehrmals in der folgenden Datenabfrage-Anweisung verwendet werden.

Eine let-Anweisung kann einen Namen an folgende Ausdrücke binden:
1. Skalar
2. Tabellarische Daten 

Bei der Bindung an Tabellendaten können let-Anweisungen zu Sichten befördert werden, um die Ausführung von Union-Operationen zu ermöglichen.

#### Syntax

`let <name> = <expression>`

<expression> kann eine Lambda-Deklaration mit null, einem oder mehreren Argumenten sein:

`() {...}`

`(<arg0>:<type0>, ...) {...}`

Beispiele: Im folgenden Beispiel wird der Name „X“ an das skalare Literal „1“ gebunden:

```
let x=1;
range y from x to x step x
```

Das folgende Beispiel bindet die Protokolle einer einzelnen Stunde an „Window“ und führt anschließend eine Selbstverknüpfung zu Window aus:  



```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

Die folgenden beiden Beispiele veranschaulichen die Verwendung einer let-Anweisung mit einem Lambda-Ausdruck:


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

Verwenden Sie dass Schlüsselwort „view“, um let-Anweisungen zu einer Ansicht zu befördern, die sich an „union*“-Operationen beteiligen.


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## Anweisung einschränken

#### Übersicht
Es ist möglich, Access-Abfragen den Zugriff innerhalb von Anweisungslisten mithilfe der folgenden Anweisung zu beschränken:


```
restrict access to (<entity1, entity2, ...>);
```
 
Die Anweisung gewährt nur Zugriff auf die Entitäten (let-Anweisungen oder tabellarische Entitäten), die von der restrict-Anweisung zugelassen werden.
 
Beispiele:

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## Abfragekomposition

Eine *Abfrage* folgt folgendem Muster:

- *Quelle* | *Filter* | *Filter* ...

Beispiel:


```
Logs | where Timestamp > ago(1d) | count
```

* *Quelle* ist der Name einer Datenbanktabelle oder einer zuvor definierten Ergebnistabelle.
* Jeder *Filter* ruft, z. B. einen Abfrageoperator wie `where` oder `count` auf, mit entsprechenden Parametern. Parameter können *skalare Ausdrücke*, geschachtelte *Datenabfragen* oder Spaltennamen sein.

Beispiel:


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Let-Anweisungen

Eine let-Anweisung kann verwendet werden, um Funktionen zu definieren – benannte Ausdrücke mit null oder mehreren Argumenten und zurückgegebenen Werten. Weitere Anweisungen können diese Funktionen dann „aufrufen“.

### Benannte Werte

Definieren von Namen, um skalare Werte darzustellen:


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

Definieren von Namen, um Abfrageergebnisse darzustellen:


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

Dies ist besonders nützlich für eine Selbstverknüpfung:


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## Benannte Funktionen

Definieren von Funktionen, die skalare Ergebnisse zurückgeben:


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

Definieren von Funktionen, die Abfrageergebnisse zurückgeben:


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

Parameter für benannte Funktionen müssen Skalare sein.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->