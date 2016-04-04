<properties 
	pageTitle="Abfragebeispiele in der Application Insights-Analyse" 
	description="Abfragebeispiel in der Application Insights-Analyse, dem leistungsfähigen Suchtool für Application Insights." 
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


# Analytics-Beispiele für Application Insights

[Analytics](app-analytics.md) ist ein leistungsfähiges Suchmodul für Ihre [Application Insights](app-insights-overview.md)-Telemetrie. Auf diesen Seiten wird die Analytics-Abfragesprache beschrieben. Es gibt auch eine [Einführung in die Sprache](app-analytics-tour.md), die sich für die ersten Schritte empfiehlt.


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Best Practices für Abfragen 

Es stehen einige Tipps zur Verfügung, damit die Abfrage schneller ausgeführt wird.

FÜHREN SIE FOLGENDES AUS:

-	Verwenden Sie zunächst Zeitfilter. Die Application Insights-Analyse ist perfekt für die Nutzung von Zeitfiltern optimiert.
-	Fügen Sie Filter an den Anfang der Abfrage (direkt nach den Zeitfiltern) ein, die voraussichtlich den größten Teil der Daten herausfiltern.
-	Stellen Sie sicher, dass die meisten der Filter am Anfang der Abfrage angezeigt werden (bevor Sie „extend“ verwenden). 
-	Verwenden Sie bei der Suche nach vollständigen Token das Schlüsselwort „has“ anstelle von „contains“. „has“ ist leistungsfähiger, da bei dieser Suche nicht nach untergeordneten Zeichenfolgen gesucht wird.
-	Suchen Sie bevorzugt in einer bestimmten Spalte, anstatt die Suche mit „*“ durchzuführen (Volltextsuche in allen Spalten).
-	Bei Verwendung von „join“ werden nur die erforderlichen Spalten auf beiden Seiten des Joins projiziert (dadurch wird die Nutzlast reduziert, die von einem Computer an einen anderen übertragen wird).

VERMEIDEN SIE FOLGENDES:

-	Versuch, neue Abfragen auszuführen ohne „limit [kleine Anzahl]“ oder „count“ am Ende. Das Ausführen von ungebundenen Abfragen für ein unbekanntes DataSet kann zu Ergebnissen im GB-Bereich führen, die an den Client zurückgegeben werden, was zu einer langsamen Reaktion und Auslastung des Clusters beiträgt.
-	Wenn Sie Konvertierungen (JSON, String usw.) auf mehr als 1 Mrd. Datensätze anwenden, formen Sie Ihre Abfrage um, um die in die Konvertierung eingelesene Datenmenge zu reduzieren.





<a name="activities"></a>
## Abrufen von Sitzungen aus Start- und Stop-Ereignissen

Beispiel: Sie verfügen über ein Protokoll mit Ereignissen, bei denen einige Ereignisse den Anfang oder das Ende einer erweiterten Aktivität oder der Sitzung kennzeichnen.

|Name|City (Ort)|SessionId|Timestamp|
|---|---|---|---|
|Starten|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Starten|Manchester|4267667|2015-12-09T10:14:02.23|
|Beenden|London|2817330|2015-12-09T10:23:43.18|
|Abbrechen|Manchester|4267667|2015-12-09T10:27:26.29|
|Beenden|Manchester|4267667|2015-12-09T10:28:31.72|

Jedes Ereignis hat eine SessionId, sodass das Problem darin besteht, die Start- und Stop-Ereignisse mit der gleichen ID abzugleichen.

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Verwenden Sie [`let`](app-analytics-syntax.md#let-statements), um eine Projektion der Tabelle zu benennen, die vor Anwenden des Joins so weit wie möglich abgespeckt wird. [`Project`](app-analytics-queries.md#project-operator) wird verwendet, um die Namen der Zeitstempel so zu ändern, dass die Start- und Endzeiten im Ergebnis angezeigt werden können. Es werden auch die anderen Spalten ausgewählt, die im Ergebnis angezeigt werden sollen. [`join`](app-analytics-queries.md#join-operator) gleicht die Start- und Stop-Einträge für die gleiche Aktivität ab, wodurch eine Zeile für jede Aktivität erstellt wird. Mit `project` wird schließlich erneut eine Spalte hinzugefügt, um die Dauer der Aktivität anzuzeigen.


|City (Ort)|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### Abrufen von Sitzungen ohne Sitzungs-ID

Nehmen Sie nun an, dass die Start- und Stop-Ereignisse keine Sitzungs-ID haben, die wir abgleichen können. Sie haben jedoch eine IP-Adresse des Clients, auf dem die Sitzung stattgefunden hat. Vorausgesetzt, für jede Client-Adresse wird nur jeweils eine Sitzung durchführt, dann können wir jedes Start-Ereignis dem nächsten Stop-Ereignis derselben IP-Adresse zuordnen.

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

Das Join ordnet jede Startzeit mit allen Endzeiten von der gleichen Client-IP-Adresse zu. Daher entfernen wir zunächst Übereinstimmungen mit früheren Endzeiten.

Stellen Sie dann eine Gruppe nach Startzeit und IP-Adresse zusammen, um eine Gruppe für jede Sitzung zu erhalten. Sie müssen eine `bin`-Funktion für den StartTime-Parameter angeben. Anderenfalls verwendet Analytics automatisch 1-Stunden-Behälter, die mit einigen Startzeiten mit den falschen Endzeiten übereinstimmen.

`argmin` wählt die Zeile mit der kürzesten Dauer in jeder Gruppe, und der `*`-Parameter durchläuft alle anderen Spalten, wobei an jeden Spaltennamen das Präfix „min\_“ angehängt wird.


![](./media/app-analytics-samples/040.png)

Anschließend können wir Code zum Zählen der Dauer in Behältern mit angemessener Größe hinzufügen. Wir bevorzugen ein Balkendiagramm, daher führen wir eine Division mit `1s` durch, um die Zeiträume (TimeSpan) in Zahlen zu konvertieren.


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### Beispiel aus der Praxis

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## Erstellen eines Diagramms für gleichzeitige Sitzungen im Verlauf der Zeit

Angenommen, Sie haben eine Tabelle der Aktivitäten mit den jeweiligen Start- und Endzeiten. Wir möchten ein Diagramm im Verlauf der Zeit anzeigen, das angibt, wie viele zu einem beliebigen Zeitpunkt gleichzeitig ausgeführt werden.

Nachfolgend sehen Sie eine Beispieleingabe, der wir den Namen „`X`“ geben:

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Wir möchten ein Diagramm in 1-Minute-Behältern erstellen, d. h., wir möchten etwas erstellen, das wir in 1-Minuten-Intervallen für jede ausgeführte Aktivität zählen können.

Hier ein Zwischenergebnis:

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` generiert ein Array von Werten in den angegebenen Abständen:

|SessionId | StartTime | StopTime | Beispiele|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Statt diese Arrays zu behalten, [erweitern wir sie](app-analytics-queries.md#mvexpand-operator):

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | Beispiele|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

Wir können diese Beispielzeit jetzt gruppieren, indem wir zählen, wie häufig jede Aktivitäten auftritt:

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Dazu benötigen wir „todatetime()“, da `mvexpand` eine Spalte vom dynamischen Typ ergibt.
* Darüber hinaus ist „bin()“ erforderlich, da für numerische Werte und Daten „summarize“ immer eine bin-Funktion mit einem Standardintervall anwendet, wenn Sie keine bereitstellen. 


| count\_SessionId | Beispiele|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

Dies kann als Balkendiagramm oder Zeitdiagramm dargestellt werden.


## Join-Varianten

Die genaue Variante des Join-Operators wird mit dem kind-Schlüsselwort angegeben. Ab sofort unterstützt Analytics sechs Varianten des Join-Operators: innerer Join mit linksseitiger Deduplizierung (Standard), standardmäßiger innerer Join, linker äußerer, rechter äußerer, vollständig äußerer und linker Anti-Join.
 
Standardmäßige Join-Variante (ohne angegebenes „kind“). Erläutern Sie den Join-Vorgang anhand von zwei Beispieltabellen:
 
Tabelle X


|Schlüssel |Wert1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

Tabelle Y

|Schlüssel |Wert2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
Das standardmäßige Join führt einen inneren Join nach der Deduplizierung der linken Seite des Join-Schlüssels durch. (Bei der Deduplizierung wird der erste Datensatz beibehalten.) Wenn die folgende Anweisung gilt:

    X | join Y on Key 

Würde die effektive linke Seite des Joins (Tabelle X nach Deduplizierung) folgendermaßen lauten:

|Schlüssel |Wert1 
|---|---
|a |1 
|b |2 
|c |4 

Das Ergebnis des Joins wäre:

|Schlüssel |Wert1 |Schlüssel |Wert2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 
|c |4 |c |30 

(Beachten Sie, dass die Schlüssel „a“ und „d“ nicht in der Ausgabe angezeigt werden, da es keine übereinstimmenden Schlüssel links und rechts gab.)
 
(Bislang handelte es sich hierbei um die erste Implementierung des Joins, die von der ersten Version von Analytics unterstützt wurde. Dies empfiehlt sich in üblichen Protokoll-/Ablaufverfolgungsanalyse-Szenarien, in denen wir zwei Ereignisse (die beiden einigen Filterkriterien entsprechen) unter der gleichen Korrelations-ID korrelieren möchten und alle Vorkommnisse des gesuchten Phänomens zurückbekommen möchten, wobei mehrere Vorkommnisse der zugehörigen Ablaufverfolgungsdatensätze ignoriert werden.)
 
### Innerer Join (kind=inner) 

Dies ist der standardmäßige innere Join wie von SQL bekannt. Der Ausgabedatensatz wird erstellt, wenn ein Datensatz auf der linken Seite über den gleichen Join-Schlüssel verfügt wie der Datensatz auf der rechten Seite.
 
    X | join kind=inner Y on Key 

führt zu:

|Schlüssel |Wert1 |Schlüssel |Wert2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 

Beachten Sie, dass (b,10) von der rechten Seite zweimal verknüpft wurde: mit (b,2) und (b,3) auf der linken Seite; zudem wurde (c,4) auf der linken Seite zweimal verknüpft: mit (c,20) und (c,30) auf der rechten Seite.

### Linker äußerer Join (kind=leftouter) 

Das Ergebnis eines linken äußeren Joins für die Tabellen X und Y enthält immer alle Datensätze der linken Tabelle (X), auch wenn die Join-Bedingung keinen übereinstimmenden Datensatz in der rechten Tabelle (Y) findet.
 
Gegeben:

    X | join kind=leftouter Y on Key 

Ergebnis:

|Schlüssel |Wert1 |Schlüssel |Wert2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
 
### Rechter äußerer Join (kind=rightouter) 

Ähnelt dem linken äußeren Join, allerdings werden die Tabellen umgekehrt behandelt.
 
Gegeben:

    X | join kind=rightouter Y on Key 

Ergebnis:


|Schlüssel |Wert1 |Schlüssel |Wert2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### Vollständiger äußerer Join (kind=fullouter) 

Im Prinzip kombiniert ein vollständiger äußerer Join den Effekt der Anwendung von sowohl linken als auch rechten äußeren Joins. Wenn Datensätze in verknüpften Tabellen nicht übereinstimmen, hat das Resultset für jede Spalte der Tabelle, für die eine übereinstimmende Zeile fehlt, NULL-Werte. Für die Datensätze, die übereinstimmen, wird eine einzelne Zeile im Resultset erstellt (das Felder mit Werten aus beiden Tabellen enthält).
 
Gegeben:

    X | join kind=fullouter Y on Key 

Ergebnis:

|Schlüssel |Wert1 |Schlüssel |Wert2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### Linker Anti-Join (kind=leftanti) 

Bei einem linken Anti-Join werden alle Datensätzen von links zurückgegeben, die nicht mit einem Datensatz von der rechten Seite übereinstimmen.
 
    X | join kind=leftanti Y on Key 
 
Ergebnis:

|Schlüssel |Wert1 
|---|---
|a |1 
 
Anti-Join formt die Abfrage „NOT IN“.



## Join mit Inlinewörterbuchzuordnung

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0323_2016-->