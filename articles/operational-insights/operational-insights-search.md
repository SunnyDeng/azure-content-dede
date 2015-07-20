<properties
   pageTitle="Suchen nach Daten in Operational Insights"
   description="Mithilfe der Protokollsuche in Microsoft Azure Operational Insights finden Sie die Daten, die Sie suchen."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="07/02/2015"
   ms.author="banders" />

# Suchen nach Daten in Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Im Kern von Microsoft Azure Operational Insights befindet sich die Protokollsuchfunktion, die Ihnen das Kombinieren und Korrelieren beliebiger Computerdaten aus mehreren Quellen in Ihrer Umgebung ermöglicht. Lösungen werden auch von der Protokollsuche beim Bereitstellen von Metriken unterstützt, die eine Pivotierung in Bezug auf einen bestimmten Problembereich bieten.

Auf der Seite Search können Sie eine Abfrage erstellen und die Ergebnisse dann bei der Suche anhand von Facetsteuerelementen filtern. Sie können auch erweiterte Abfragen zum Transformieren, Filtern und Berichten für Ihre Ergebnisse erstellen.

Allgemeine Protokollsuchabfragen werden auf den meisten Lösungsseiten angezeigt. In der gesamten Operational Insights-Konsole können Sie anhand der Protokollsuche auf Kacheln klicken oder einen Drilldown in andere Elemente ausführen, um Details über das Element anzuzeigen.

In diesem Lernprogramm werden Beispiele zu den Grundlagen der Verwendung von Protokollsuchen veranschaulicht.

Es geht los mit einfachen und praktischen Beispielen, auf denen dann aufgebaut wird, sodass Sie einen Überblick über praktische Anwendungsfälle erhalten, wie Sie die Syntax zur Gewinnung gewünschter Erkenntnisse aus den Daten verwenden.

Wenn Sie Protokollsuchen in Operational Insights durchführen,wenden Sie die folgenden Verfahren an:

- Verwenden einfacher Filter
- Verwenden zusätzlicher Filter
- Bearbeiten von Suchergebnissen
- Verwenden des "Measure"-Befehls
- Verwenden der "Max"- und "Min"-Funktion mit dem "Measure"-Befehl
- Verwenden der "Avg"-Funktion mit dem Measure-Befehl
- Verwenden des "Where"-Befehls

Nachdem Sie sich mit den Suchtechniken vertraut gemacht haben, können Sie sich die [Search-Syntaxreferenz](#search-syntax-reference) und die [Search-Feld- und Facetreferenz](#search-field-and-facet-reference).

## Verwenden einfacher Filter

Als erstes sollten Sie wissen, dass der erste Teil einer Suchabfrage vor einem senkrechten Strich "|" immer ein *Filter* ist. Sie können sich dies als WHERE-Klausel in TSQL vorstellen: Der Filter bestimmt *was* die Teilmenge der Daten ist, die aus dem Datenspeicher von Operational Insights Datenspeicher abgerufen werden. Beim Suchen im Datenspeicher geht es größtenteils um die Angabe der Merkmale der Daten, die Sie extrahieren möchten. Daher ist es natürlich, dass eine Abfrage mit der WHERE-Klausel beginnt.

Die grundlegendsten Filter, die Sie verwenden können, sind *Schlüsselwörter* wie 'Error' oder 'Timeout', oder ein Computername. Diese Typen von einfachen Abfragen geben in der Regel verschiedene Datenformen innerhalb des gleichen Ergebnissatzes zurück. Dies liegt daran, dass Operational Insights unterschiedliche *Typen* von Daten im System hat.


### Durchführen einer einfachen Suche
1. Klicken Sie im Operational Insights-Portal auf **Daten-Explorer durchsuchen**. !["search tile" (Kachel suchen)](./media/operational-insights-search/overview-search.png)
2. Geben Sie in das Abfragefeld `error` ein und klicken Sie dann auf **Search**. !["search error" (Fehler suchen)](./media/operational-insights-search/search-error.png) Angenommen, die Abfrage für `error` gibt in der folgenden Abbildung 100.000 **Ereignis**datensätze zurück (von Log Management erfasst), 18 **Warnungs**datensätze (von der Konfigurationsbewertung generiert) und 12 **Konfigurationsänderungs**datensätze (von der Änderungsnachverfolgung aufgezeichnet). !["search results" (Ergebnisse durchsuchen)](./media/operational-insights-search/results01.png)

Diese Filter sind keine echten Objekttypen/-klassen. *Type* ist nur ein Tag, eine Eigenschaft oder eine Zeichenfolge/ein Name/eine Kategorie, der oder die einem Teil der Daten zugeordnet ist. Einige Dokumente im System sind als **Type:Alert**, **Type: PerfHourly** oder **Type:Event** markiert, und so weiter. Alle Suchergebnisse, Dokumente, Datensätze oder Einträge zeigen die Basiseigenschaften und deren Werte für jedes dieser Datenelemente. Sie können die Feldnamen im Filter angeben, wenn Sie nur die Datensätze abrufen möchten, in dem das Feld den angegebenen Wert besitzt.

*Type* ist im Prinzip nur ein Feld, über das alle Datensätze verfügen, es unterscheidet sich nicht von anderen Feldern. Dies wurde basierend auf dem Wert des Type-Felds festgelegt. Dieser Datensatz hat eine unterschiedliche Form oder Art. Übrigens ist **Type=PerfHourly** oder **Type=Ereignis** auch die Syntax, die Sie zur Abfrage von stündlichen Leistungsdatenaggregaten oder Ereignissen benötigen.

Sie können hinter dem Feldnamen und vor dem Wert entweder einen Doppelpunkt (:) oder ein Gleichheitszeichen (=) verwenden. **Type:Event** und **Type=Event** sind bedeutungsgleich, Sie können sich eine Form aussuchen.

Wenn also die Type=PerfHourly-Datensätze ein Feld namens "CounterName" besitzen, dann können Sie eine Abfrage ähnlich `Type=PerfHourly CounterName="% Processor Time"` schreiben.

Dadurch erhalten Sie nur die Leistungsdaten, bei denen der Name des Leistungsindikators "Prozessorzeit (%)" ist.

### Suche nach Leistungsdaten zur Prozessorzeit
- Geben Sie im Suchabfragefeld `Type=PerfHourly CounterName="% Processor Time"` ein.

Sie können auch spezifischer sein und **InstanceName = _ 'Total'** in der Abfrage verwenden, was ein Windows-Leistungsindikator ist. Sie können auch eine Facette und einen weiteren Wert **field:value** auswählen. Der Filter wird automatisch zu Ihrem Filter in der Abfrageleiste hinzugefügt. Dies wird in der folgenden Abbildung veranschaulicht. Hier erfahren Sie, wo Sie zum Hinzufügen von **InstanceName:'_Total'** zur Abfrage klicken müssen, ohne etwas einzugeben.

!["search facet" (Facette durchsuchen)](./media/operational-insights-search/search-facet.png)

Ihre Abfrage wird jetzt zu `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`.

In diesem Beispiel müssen Sie **Type=PerfHourly** nicht angeben, um dieses Ergebnis zu erhalten. Da die Felder "CounterName" und "InstanceName" nur für Datensätze vom Typ "Type= PerfHourly" vorhanden sind, ist die Abfrage spezifisch genug, um die gleichen Ergebnisse wie die vorherige längere Abfrage zurückzugeben: ```
CounterName=”% Processor Time” InstanceName=”_Total”
```

Das liegt daran, dass alle Filter in der Abfrage als in *UND* miteinander ausgewertet werden. Je mehr Felder Sie zu den Kriterien hinzufügen, desto geringer die Anzahl der Ergebnisse, die spezifischer und verfeinerter sind.

Zum Beispiel ist die Abfrage `Type=Event EventLog="Windows PowerShell"` identisch mit `Type=Event AND EventLog="Windows PowerShell"`. Sie gibt alle Ereignisse zurück, die in und aus dem Windows PowerShell-Ereignisprotokoll erfasst wurden. Wenn Sie einen Filter mehrmals durch wiederholtes Auswählen der gleichen Facette hinzufügen, handelt es sich um ein rein kosmetisches Problem: Möglicherweise ist die Suchleiste überladen, es werden jedoch weiterhin dieselben Ergebnisse zurückgegeben, da der implizite AND-Operator immer vorhanden ist.

Sie können den impliziten AND-Operator ganz einfach umkehren, indem Sie explizit einen NOT-Operator verwenden. Beispiel:

Die Abfrage `Type:Event NOT(EventLog:"Windows PowerShell")` oder deren Entsprechung `Type=Event EventLog!="Windows PowerShell"` gibt alle Ereignisse aus allen anderen Protokollen zurück, die KEIN Windows PowerShell-Protokoll sind.

Oder Sie verwenden einen anderen booleschen Operators wie 'OR'. Die folgende Abfrage gibt die Datensätze zurück, für die das Ereignisprotokoll entweder eine Anwendung ODER ein System ist.

```
EventLog=Application OR EventLog=System
```

Verwenden Sie obige Abfrage, um Einträge für beide Protokolle im gleichen Ergebnissatz zu erhalten.

Wenn Sie jedoch den OR-Operator entfernen, indem Sie das implizite AND stehen lassen, gibt die folgende Abfrage keine Ergebnisse zurück, da es keinen Eintrag im Ereignisprotokoll gibt, der zu BEIDEN Protokollen gehört. Jeder Eintrag im Ereignisprotokoll wurde nur in eins der beiden Protokolle geschrieben.

```
EventLog=Application EventLog=System
```


## Verwenden zusätzlicher Filter

Die folgende Abfrage gibt Einträge für zwei Ereignisprotokolle für alle Computer zurück, die Daten gesendet haben.

```
EventLog=Application OR EventLog=System
```

!["search results" (Ergebnisse durchsuchen)](./media/operational-insights-search/search-results03.png)

Durch Auswahl eines der Felder oder Filter wird die Abfrage auf einen bestimmten Computer beschränkt, und alle anderen werden ausgeschlossen. Die resultierende Abfrage ähnelt der folgenden.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

Dies entspricht aufgrund des impliziten AND der folgenden Abfrage.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

Jede Abfrage wird in der folgenden expliziten Reihenfolge ausgewertet. Beachten Sie die Klammer.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

Wie beim Ereignisprotokoll-Feld können Sie Daten nur für einen Satz bestimmter Computern durch Hinzufügen von OR abrufen. Beispiel:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

Ähnlich dazu wird die folgende Abfrage **CPU-Zeit (%)** nur für die beiden ausgewählten Computer zurückgegeben.

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### Boolesche Operatoren
Mit "datetime" und numerischen Feldern können Sie Werte mit *größer als*, *kleiner als*, und *kleiner als oder gleich* suchen. Sie können einfache Operatoren wie z. B. >, <, >=, < =,! = in der Suchabfrageleiste verwenden.


Sie können ein bestimmtes Ereignisprotokoll für einen bestimmten Zeitraum abfragen. Beispielsweise werden die letzten 24 Stunden mit dem folgenden mnemonischen Ausdruck dargestellt.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### Suchen mithilfe eines booleschen Operators
- Geben Sie im Suchabfragefeld `EventLog=System TimeGenerated>NOW-24HOURS"` !["search with boolean" (boolesche Suche)](./media/operational-insights-search/search-boolean.png) ein.

Sie können das Zeitintervall zwar grafisch steuern, was die meiste Zeit auch sinnvoll ist, es hat jedoch auch Vorteile, einen Zeitfilter direkt in die Abfrage einzuschließen. Beispielsweise funktioniert dies hervorragend mit Dashboards, bei denen Sie die Zeit für jede Kachel überschreiben können, unabhängig von der *globalen* Zeitauswahl auf der Dashboardseite. Weitere Informationen finden Sie unter [Time Matters in Dashboard](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/) ("Zeit spielt in Dashboards eine Rolle", auf Englisch).

Beim Filtern nach Zeit sollten Sie bedenken, dass Sie Resultate für die *Schnittmenge* der beiden Zeiträume erhalten: den im Operational Insights-Portal (S1) und den in der Abfrage (S2) angegebenen Zeitraum.

!["intersection" (Schnittmenge)](./media/operational-insights-search/intersection.png)

Das heißt, wenn die Zeiträume sich nicht überschneiden, z. B. wenn im Operational Insights-Portal **diese Woche** und in der Abfrage **letzte Woche** definiert ist, gibt es keine Überschneidung und Sie erhalten keine Ergebnisse.

Vergleichsoperatoren für das Feld "TimeGenerated" sind auch in anderen Situationen nützlich. Beispielsweise in numerischen Feldern.

Beispielsweise wird angenommen, dass Warnungen für die Konfigurationsbewertung die folgenden Schweregrade haben:

- 0 = Information
- 1 = Warnung
- 2 = Kritischer Fehler

Sie können mit der folgenden Abfrage sowohl nach Warnungen als auch kritischen Fehler filtern und Informationen ausschließen:

```
Type=ConfigurationAlert  Severity>=1
```


Sie können auch Bereichsabfragen verwenden. Das bedeutet, dass Sie Anfang und Ende der Wertebereiche in einer Sequenz bereitstellen können. Wenn Sie z. B. Ereignisse aus dem Ereignisprotokoll von Operations Manager erhalten möchten, bei denen die EventID größer als oder gleich 2100, aber nicht größer als 2199 ist, so gibt dies die folgende Abfrage zurück.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]Die zu verwendende Bereichssyntax ist der Doppelpunkt (:) "Feld:Trennzeichen" und *nicht* das Gleichheitszeichen (=). Schließen Sie das untere und obere Ende des Bereichs in eckige Klammern ein, und trennen Sie diese durch zwei Punkte (..).

## Bearbeiten von Suchergebnissen

Wenn Sie nach Daten suchen, sollten Sie Ihre Suchabfrage verfeinern und die Ergebnisse gut kontrollieren können. Wenn Ergebnisse abgerufen werden, können Sie die Befehle anwenden, um sie zu verändern.

Befehle in Operational Insights-Suchen *müssen* nach dem senkrechten Strich (|) folgen. Ein Filter muss immer der erste Teil einer Abfragezeichenfolge sein. Er definiert den Datensatz, mit dem Sie arbeiten, und "leitet" diese Ergebnisse dann in einen Befehl. Mit der Pipe können Sie anschließend zusätzliche Befehle hinzufügen. Dies ist in etwa mit der Windows PowerShell-Pipeline vergleichbar.

Im Allgemeinen versucht die Operational Insights-Suchsprache, sich an PowerShell-Stil und -Richtlinien zu orientieren, um IT-Experten den Einstieg zu erleichtern und die Lernkurve zu verbessern.

Befehle bestehen aus Verben, sodass Sie leicht erkennen können, was diese tun.

### Sortieren

Mit dem Sortierbefehl können Sie die Sortierreihenfolge durch ein oder mehrere Felder festlegen. Auch wenn Sie ihn in der Standardeinstellung nicht verwenden, wird eine zeitlich absteigende Reihenfolge erzwungen. Die aktuellsten Ergebnisse stehen immer am Anfang der Suchergebnisse. Das bedeutet, dass beim Ausführen einer Suche mit `Type=Event EventID=1234` Folgendes wirklich ausgeführt wird:

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

Das liegt daran, dass dies die Art von Erfahrung ist, die Sie aus Protokollen kennen. Beispielsweise in der Windows-Ereignisanzeige.

Sie können die Funktion "Sortieren" verwenden, um die Form der zurückgegebenen Ergebnisse zu ändern. Die folgenden Beispiele veranschaulichen die Funktionsweise.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


Die einfachen Beispiele oben zeigen die Funktionsweise von Befehlen – sie ändern die Form der Ergebnisse, die der Filter zurückgegeben hat.

### "Limit" und "Top"
Eine weiterer wenig bekannter Befehl ist LIMIT. "Limit" ist ein PowerShell-ähnliches Verb. "Limit" ist funktionell identisch zum TOP-Befehl. Die folgenden Abfragen geben die gleichen Ergebnisse zurück.

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Suchen mit "Top"
- Geben Sie im Suchabfragefeld `Type=Event EventID=2110 | Top 1` !["search top" (oben suchen)](./media/operational-insights-search/search-top.png) ein.

In der Abbildung oben gibt es 988 Datensätze mit der EventID = 2110. Die Felder, Facetten und Filter auf der linken Seite zeigen immer Informationen zu den Ergebnissen an, die *vom Filterteil* der Abfrage zurückgegeben werden, also der Teil vor jedem senkrechten Strich. Im **Ergebnis**-Bereich wird nur das einzelne neueste Ergebnis zurückgegeben, da der Beispielbefehl die Ergebnisse geformt und transformiert hat.

### Wählen Sie

Der SELECT-Befehl verhält sich wie das Select-Objekt in PowerShell. Er gibt gefilterte Ergebnisse zurück, die nicht all ihre ursprünglichen Eigenschaften aufweisen. Stattdessen wählt er nur die Eigenschaften aus, die Sie angeben.

#### Suchen mit dem "Select"-Befehl

1. Geben Sie in der Suche `Type=Event` ein und klicken Sie dann auf **Search**.
2. Klicken Sie in einem der Ergebnisse auf **+ mehr anzeigen**, um alle Eigenschaften der Ergebnisse anzuzeigen.
3. Wählen Sie einige davon explizit aus, und die Abfrage ändert sich zu `Type=Event | Select Computer,EventID,RenderedDescription`. !["search select" (Auswahl durchsuchen)](./media/operational-insights-search/search-select.png)

Dieser Befehl ist besonders nützlich, wenn Sie die Suchergebnisse steuern möchten und nur die Teile der Daten wählen, die für Ihre Untersuchung eine Rolle spielen, also häufig nicht der vollständige Datensatz. Dies ist auch nützlich, wenn Datensätze verschiedener Typen *einige* gemeinsame Eigenschaften aufweisen, dies aber nicht für *alle* Eigenschaften gilt. Sie können Ausgaben generieren, die natürlicher wie eine Tabelle aussehen oder gut funktionieren, wenn sie in eine CSV-Datei exportiert und dann in Excel bearbeitet werden.

## Verwenden des "Measure"-Befehls

MEASURE ist einer der vielseitigsten Befehle bei Operational Insights-Suchen. Sie können damit statistische *Funktionen* auf Ihre Daten anwenden und die Ergebnisse nach einem bestimmten Feld gruppieren. Es gibt mehrere statistische Funktionen, die "Measure" unterstützt.

### "Measure count()"

Die erste statistische Funktion, die verwendet werden kann und leicht zu verstehen ist, ist die *count()*-Funktion.

Ergebnisse aus einer beliebigen Suchabfrage wie z. B. `Type=Event` zeigen auf der linken Seite der Suchergebnisse Filter, die auch als Facetten bezeichnet werden. Die Filter zeigen eine Verteilung der Werte anhand eines bestimmten Felds für die Ergebnisse in der ausgeführten Suche an.

!["search measure count" ("Measure"-Count durchsuchen)](./media/operational-insights-search/search-measure-count01.png)

Zum Beispiel sehen Sie in der Abbildung oben das **Computer** Feld, das anzeigt, dass innerhalb der fast 3 Millionen Ereignisse in den Ergebnissen 20 einmalige und eindeutige Werte für das **Computer**-Feld in diesen Datensätzen vorhanden sind. Die Kachel zeigt nur die fünf obersten an, welche die fünf häufigsten Werte sind, die in die **Computer**-Felder geschrieben werden, sortiert nach der Anzahl der Dokumente, die diesen bestimmten Wert in dem Feld enthalten. In der Abbildung sehen Sie, dass unter diesen fast 3 Millionen Ereignissen insgesamt 880.000 aus dem DM-Computer stammen, 602.000 aus dem DE-Computer usw..


Was geschieht, wenn Sie alle Werte sehen möchten, obwohl die Kachel nur die ersten fünf anzeigt?

Dies kann der "Measure"-Befehl mithilfe der "Count()"-Funktion erledigen. Diese Funktion verwendet keine Parameter. Geben Sie einfach das zu gruppieren Feld an, in diesem Fall das **Computer**-Feld:

`Type=Event | Measure count() by Computer`

!["search measure count" ("Measure"-Count durchsuchen)](./media/operational-insights-search/search-measure-count-computer.png)

Allerdings ist **Computer** nur ein Feld, das *in* jedem Datenelement verwendet wird. Es sind keine relationalen Datenbanken vorhanden und es gibt nirgendwo ein separates **Computer**-Objekt. Nur die Werte *in* den Daten beschreiben, durch welche Entität sie generiert wurden, und eine Anzahl weiterer Merkmale und Aspekte der Daten – daher der Begriff *Facette*. Allerdings können Sie auch eine Gruppierung nach anderen Feldern vornehmen. Da die ursprünglichen Ergebnisse von fast 3 Millionen Ereignissen, die in den "Measure"-Befehl geleitet werden, auch über ein Feld namens **EventID** verfügen, können Sie das gleiche Verfahren zum Gruppieren nach diesem Feld verwenden und eine Anzahl der Ereignisse nach EventID erhalten:

```
Type=Event | Measure count() by EventID
```

Wenn Sie sich nicht für die tatsächliche Anzahl an Datensätzen interessieren, die einen bestimmten Wert enthalten, sondern nur eine Liste der Werte selbst benötigen, können Sie einen *Select*-Befehl am Ende hinzufügen und einfach die erste Spalte auswählen:

```
Type=Event | Measure count() by EventID | Select EventID
```

Dann können Sie die Ergebnisse verfeinern und sie in der Abfrage Vorsortieren, oder Sie klicken einfach auf die Spalten im Raster.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### Suchen mit dem "Measures"-Count

- Geben Sie im Suchabfragefeld `Type=Event | Measure count() by EventID` ein.
- Fügen Sie `| Select EventID` an das Ende der Abfrage.
- Fügen Sie schließlich `| Sort EventID asc` an das Ende der Abfrage.


Einige wichtige Punkte sind zu beachten und hervorzuheben:

Erstens sind die angezeigten Ergebnisse nicht mehr die ursprünglichen Rohergebnisse. Stattdessen handelt es sich um aggregierte Ergebnisse – im Wesentlichen Gruppen von Ergebnissen. Während dies kein Problem ist, sollten Sie dennoch wissen, dass Sie mit einer sehr unterschiedliche Form von Daten interagieren, die sich von der ursprünglichen Rohform unterscheidet, die als Ergebnis der Aggregation/statistischen Funktion dynamisch erstellt wird.

Zweitens gibt der **Measure-Count** zurzeit nur die ersten 100 unterschiedlichen Ergebnisse zurück. Diese Beschränkung gilt nicht für andere statistische Funktionen. Daher müssen Sie in der Regel zunächst einen genaueren Filter für bestimmte Elemente zur Suche angeben, bevor Sie "Measure count()" anwenden.

## Verwenden der "Max"- und "Min"-Funktion mit dem "Measure"-Befehl

Es gibt verschiedene Szenarien, in denen **Measure Max()** und **Measure Min()** nützlich sind. Da die Funktionen jedoch genau gegensätzlich sind, illustrieren wie "Max()", sodass Sie mit "Min()" selbst experimentieren können.

Bei Abfragen von Warnungen für die Konfigurationsbewertung gibt es die Eigenschaft **Schweregrad**, der entweder 0, 1 oder 2 sein kann, also Information, Warnung und kritischer Fehler. Beispiel:

```
Type=Alert
```

!["search measure count start" (Durchsuchen von "Measure"-Count starten)](./media/operational-insights-search/search-measure-max01.png)

Wenn Sie den höchsten Wert für alle Warnungen für einen gemeinsamen Computer nach Feld gruppiert erhalten möchten, verwenden Sie

```
Type=Alert | Measure Max(Severity) by Computer
```

!["search measure max computer" ("Measure max"-Computer durchsuchen)](./media/operational-insights-search/search-measure-max02.png)

Für Computer mit **Warnungen** wird angezeigt, dass für die meisten davon ein kritischer Fehler vorliegt und der Bacc-Computer eine Warnung des höchsten Schweregrads enthält.

```
Type=Alert | Measure Max(Severity) by Computer
```

!["search measure max time generated computer" ("Measure max time" generierten Computer durchsuchen)](./media/operational-insights-search/search-measure-max03.png)

Diese Funktion funktioniert gut mit Zahlen, aber auch mit DateTime-Feldern. Es empfiehlt sich, den letzten oder neuesten Zeitstempel für alle Daten zu prüfen, die für jeden Computer indiziert wurden. Beispiel: Wann wurde die letzte Konfigurationsänderung von der Lösung zur Änderungsnachverfolgung für jeden Computer gemeldet?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## Verwenden der "Avg"-Funktion mit dem Measure-Befehl

Die statistische "Avg()"-Funktion kann zusammen mit "Measure" verwendet werden, sodass Sie den Durchschnittswert für ein Feld berechnen, und die Ergebnisse nach dem gleichen oder einem anderen Feld gruppieren können. Dies ist in einer Vielzahl von Fällen nützlich, z. B. für Leistungsdaten.

Wir beginnen mit den Leistungsdaten. Beachten Sie jedoch, dass Operational Insights derzeit nur bestimmte Fabric-bezogene Leistungsindikatoren für Virtual Machine Manager und Hyper-V-Hosts als Teil der Capacity Management-Lösung erfasst.

Die grundlegende Abfrage zum Suchen *aller* Leistungsdaten ist:

```
Type=PerfHourly
```

!["search avg start" ("Avg"-Suche starten)](./media/operational-insights-search/search-avg01.png)

Als Erstes sehen Sie, dass Operational Insights Diagramme der Leistungsindikatoren anzeigt. Am unteren Teil der Ergebnisse sehen Sie die eigentlichen Datensätze hinter den Diagrammen.

!["search avg start" ("Avg"-Suche starten)](./media/operational-insights-search/search-avg02.png)

In der Abbildung oben gibt es zwei Sätze von gekennzeichneten Feldern, die auf Folgendes hinweisen:

- Der erste Satz identifiziert den Namen des Windows-Leistungsindikators, den Objektnamen und den Instanznamen im Abfragefilter. Dies sind die Felder, die Sie wahrscheinlich am häufigsten als Facetten/Filter verwenden.
- **SampleValue** ist der tatsächliche Wert des Indikators
- in der Abfrage, **Type=PerfHourly** ist eine stündliche Aggregation.
- **TimeGenerated** ist 21:00 Uhr im 24-Stunden-Format. Dies ist die Aggregation für diesen stündlichen Zeitraum zwischen 20:00 Uhr und 21:00 Uhr.
- **SampleCount** ist die Aggregation, die mit 12 Proben berechnet wird (alle 5 Minuten eine).
- Für die stündliche Periode waren **min**, **max** und **Percentile95** in diesem Beispiel für den Arbeitsspeicher in einer virtuellen Maschine 6144 MB (Megabytes).
- **SampleValue** ist eine stündliche Aggregation und wird mit dem *Durchschnitt* für den stündlichen Zeitraum befüllt. Damit werden die Leistungsdiagramme dargestellt.

Nachdem Sie von der Form des "PerfHourly"-Datensatzes und anderen Suchtechniken erfahren haben, können Sie "Measure Avg()" zur Aggregation dieser Art von numerischen Daten verwenden.

Hier ist ein einfaches Beispiel:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

!["search avg samplevalue" ("Avg samplevalue" suchen)](./media/operational-insights-search/search-avg03.png)

In diesem Beispiel wählen Sie den Leistungsindikator für die CPU-Gesamtzeit und den Durchschnitt pro Computer. Da **SampleValue** bereits ein Durchschnittswert ist, fragen Sie tatsächlich den Durchschnitt des Durchschnitts ab. Dies ist zu diesem Zeitpunkt korrekt mit "Type=PerfHourly". Sie sollten immer einen Filter auf "TimeGenerated" verwenden, um den Vorgang auf kleine oder aktuelle Datensätze zu begrenzen, wie z. B. die letzten 4 Stunden anstelle der letzten 7 Tage.

Die obige Abfrage sieht also folgendermaßen aus:

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### Verwenden der "Avg"-Funktion zur Suche mit dem "Measure"-Befehl
1. Geben Sie im Suchabfragefeld `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer` ein.
2. Beachten Sie, dass der aktuelle Durchschnitt in der Regel höher sein wird.
3. Überarbeiten der Suchabfrage mit `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer` zur Berechnung des Durchschnitts der stündlichen Maximalwerte.

Sie können Daten von *mehreren* Computern aggregieren und korrelieren. Angenommen, Sie haben eine Reihe von Hosts in einer Farm, in der jeder Knoten dem anderen entspricht und alle die gleiche Art von Arbeit verrichten und die Last ungefähr ausgeglichen ist. Sie können alle Indikatoren mit der folgenden Abfrage in einem Durchgang abrufen und Durchschnittswerte für die gesamte Farm erhalten. Sie beginnen damit, die Computer wie in folgendem Beispiel auszuwählen:

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Sie verfügen nun über die Computer und sollten jetzt nur zwei Key Performance Indicators (KPIs) auswählen: "CPU-Auslastung in %" und "freier Speicherplatz in %". Daher wird dieser Teil der Abfrage zu:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

Jetzt können Sie Computer und Indikatoren anhand des folgenden Beispiels hinzufügen:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

Da Sie eine sehr spezifische Auswahl haben, kann der **Measure Avg()**-Befehl den Durchschnitt nicht nur pro Computer, sondern über die gesamte Farm hinweg zurückgeben, und zwar ganz einfach durch die Gruppierung nach "CounterName". Beispiel:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

Dadurch erhalten Sie eine nützliche, kompakte Ansicht von einigen KPIs in Ihrer Umgebung.

!["search avg grouping" ("Avg"-Gruppierung durchsuchen)](./media/operational-insights-search/search-avg04.png)


Sie können dies problemlos in einem Dashboard verwenden. Weitere Informationen zum Verwenden von Dashboards finden Sie unter [Operational Insights-Dashboards](operational-insights-use-dashboards).

!["search avg dashboard" ("Avg"-Dashboard durchsuchen)](./media/operational-insights-search/search-avg05.png)

### Verwenden der "Sum"-Funktion mit dem "Measure"-Befehl

Die "Sum"-Funktion ähnelt anderen Funktionen des "Measure"-Befehls. Ein Beispiel zur Verwendung der "Sum"-Funktion finden Sie auf [W3C IIS Logs Search in Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx) ("W3C-IIS-Protokollsuche in Microsoft Azure Operational Insights", auf Englisch).

Sie können "Max()" und "Min()" mit Zahlen, Uhrzeitangaben und Textzeichenfolgen verwenden. Mit Textzeichenfolgen sind diese alphabetisch sortiert, und Sie erhalten die erste und letzte.

Sie können "Sum()" ausschließlich mit numerischen Feldern verwenden. Dies gilt auch für "Avg()".

## Verwenden des "Where"-Befehls

Der Befehl funktioniert wie ein Filter, kann aber in der Pipeline angewendet werden, um aggregierte Ergebnisse weiter zu filtern, die von einem "Measure"-Befehl erzeugt wurden – im Gegensatz zu reinen Ergebnisse, die am Beginn einer Abfrage gefiltert werden.

Beispiel:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

Sie können einen weiteren senkrechten Strich "|" und den "Where"-Befehl hinzufügen, um nur Computer abzurufen, deren durchschnittliche CPU-Nutzung über 80 % liegt, siehe folgendes Beispiel:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Wenn Sie mit Microsoft System Center – Operations Manager vertraut sind, können Sie sich den "Where"-Befehl in der Management Pack-Terminologie denken. Wenn das Beispiel eine Regel wäre, wäre der erste Teil der Abfrage die Datenquelle und der "Where"-Befehl wäre die Bedingungserkennung.

Sie können die Abfrage als Kachel in **Mein Dashboard** verwenden, als eine Art Überwachung, wenn die CPUs des Computers stark ausgelastet sind. Weitere Informationen zu Dashboards finden Sie unter [Operational Insights-Dashboards](operational-insights-use-dashboards). Sie können Dashboards auch mithilfe der mobilen App erstellen und verwenden. Weitere Informationen finden Sie unter [Azure Operational Insights – Mobile App ](http://www.windowsphone.com/de-de/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865). In den unteren beiden Kacheln der folgenden Abbildung bekommen Sie den Monitor als Liste und als Zahl angezeigt. Im Wesentlichen sollte die Zahl immer 0 (null) sein und die Liste leer. Andernfalls könnte es eine Warnungsbedingung geben. Bei Bedarf können sie die Abfrage auch nutzen, um einen Blick auf überlastete Computer zu werden.

!["mobile dashboard" ("mobiles Dashboard")](./media/operational-insights-search/search-mobile.png)

## Search-Syntaxreferenz

Im folgenden Verweisabschnitt zur Suchsprache werden die allgemeinen Optionen zur Abfragesyntax beschrieben, die Sie beim Suchen von Daten und Filtern von Ausdrücken verwenden können, um Ihre Suche einzugrenzen. Außerdem werden die Befehle beschrieben, mit denen Sie Aktionen für die abgerufenen Daten ausführen können.

Sie erhalten Informationen über die von der Suche zurückgegebenen Felder und die Facetten, mit denen Sie in ähnliche Datenkategorien unter [Search-Feld- und Facetreferenz](#Search-field-and-facet-reference) eintauchen können.

### Allgemeine Abfragesyntax

Syntax:

filterExpression | command1 | command2 …

Der Filterausdruck (**filterExpression**) definiert die "Where"-Bedingung für die Abfrage. Die Befehle gelten für die von der Abfrage zurückgegebenen Ergebnisse. Mehrere Befehle müssen durch einen senkrechten Strich (|) getrennt werden.

#### Beispiele für die allgemeine Syntax

Beispiele:

	system

Diese Abfrage gibt Ergebnisse mit dem Wort "System" in jedem Feld zurück, das für die Volltextsuche oder bestimmte Suchbegriffe indiziert wurde.

>[AZURE.NOTE]Nicht alle Felder sind auf diese Weise indiziert, aber die am häufigsten verwendeten Textfelder (z. B. Beschreibungen und Namen) sind es in der Regel schon.

	system error

Diese Abfrage gibt Ergebnisse zurück, die die Wörter "System" und "Fehler" enthalten.

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

Diese Abfrage gibt Ergebnisse zurück, die die Wörter "System" und "Fehler" enthalten. Dann sortiert die Abfrage die Ergebnisse nach dem Feld **ManagementGroupName** (in aufsteigender Reihenfolge), und anschließend nach **TimeGenerated** (in absteigender Reihenfolge). Nur die ersten zehn Ergebnisse werden genommen.

>[AZURE.IMPORTANT]Bei allen Feldnamen und Werten für die Zeichenfolgen- und Textfelder muss die Groß-und Kleinschreibung beachtet werden.

### Filtern von Ausdrücken

In den folgenden Unterabschnitten werden Filterausdrücke erläutert.

#### Zeichenfolgenliterale

Ein Zeichenfolgenliteral ist eine Zeichenfolge, die vom Parser nicht als ein Schlüsselwort oder ein vordefinierter Datentyp (z. B. eine Zahl oder ein Datum) erkannt wird.

Beispiele:

	These all are string literals


Diese Abfrage sucht nach Ergebnissen, die Vorkommen mit allen fünf Wörtern enthalten. Schließen Sie das Zeichenfolgenliteral in Anführungszeichen ein, um eine komplexe Zeichenfolgensuche durchzuführen, beispielsweise:

	" Windows Server"

Dies gibt nur Ergebnisse mit genauen Übereinstimmungen für "Windows Server" zurück.

#### Zahlen

Der Parser unterstützt die Syntax mit ganzen Dezimalzahlen und Gleitkommazahlen für numerische Felder.

Beispiele:

	Type:PerfHourly 0.5


	HTTP 500

#### Datum/Uhrzeit

Jedes Datenelement im System besitzt eine **TimeGenerated**-Eigenschaft, die das ursprüngliche Datum und die Uhrzeit des Datensatzes darstellt. Einige Datentypen können darüber hinaus weitere Datums-/Uhrzeitfelder haben (z. B. **LastModified**).

Das Zeitachsen-Diagramm/der Uhrzeit-Selektor in Operational Insights zeigt eine Verteilung von Ergebnissen im Zeitverlauf an (gemäß der aktuell ausgeführten Abfrage), basierend auf dem Feld **TimeGenerated**. Datums-/Uhrzeit-Felder verfügen über ein bestimmtes Zeichenfolgenformat, das in Abfragen verwendet werden kann, um diese auf einen bestimmten Zeitraum zu beschränken. Sie können mit der Syntax auch auf relative Zeitintervalle verweisen (z. B. "zwischen vor 3 Tagen und vor 2 Stunden") .

Syntax:

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



Beispiel:

	TimeGenerated:2013-10-01T12:20

Der vorherige Befehl gibt nur Datensätze mit einem **TimeGenerated**-Wert von genau 12:20 Uhr, 1. Oktober 2013 zurück. Es ist unwahrscheinlich, dass Sie ein Ergebnis erhalten, aber Sie verstehen nun das Konzept.

Der Parser unterstützt auch den mnemonischen Datums-/Uhrzeitwert "NOW".

Beispiel:


In diesem Fall ist es unwahrscheinlich, dass dies ein Ergebnis gibt, da Daten nicht so schnell durch das System gehen.

Diese Beispiele sind die Bausteine für relative und absolute Datumsangaben. In den nächsten drei Unterabschnitten erläutern wir die Verwendung in erweiterten Filter mit Beispielen, bei denen ein relativer Datumsbereich verwendet wird.

#### Datums-/Uhrzeit-Mathematik

Verwenden Sie mathematische Datums-/Uhrzeit-Operatoren, um Datums-/Uhrzeitwerte zu versetzen oder zu runden, indem einfache Datums-/Uhrzeit-Berechnungen verwendet werden.

Syntax:

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>Operator</th>
		<th>Beschreibung</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>Datum/Uhrzeit, in der angegebenen Einheit gerundet. </p>
		<p>Beispiel: NOW/DAY rundet das aktuelle Datum/die aktuelle Uhrzeit auf Mitternacht des aktuellen Tages. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ oder -</p>
		</td>
		<td>
		<p>Versetzt Datum und Uhrzeit nach angegebener Anzahl von Einheiten.</p>
		<p>Beispiele:&#160; </p>
		<ul>
			<li class="unordered">NOW+1HOUR Stunde versetzt den aktuellen Zeitpunkt (Datum/Uhrzeit) um eine Stunde später.<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS versetzt den Datumswert um 10 Tage zurück.</li>
		</ul>
		</td>
	</tr>
</table>





Sie können Datum/Zeit mit mathematischen Operatoren verketten, z. B.:

	NOW+1HOUR-10MONTHS/MINUTE

Die folgende Tabelle enthält die unterstützten Datums-/Uhrzeit-Einheiten.

<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Datum/Uhrzeit-Einheit </th> <th>Beschreibung </th> </tr> <tr> <td> <p>YEAR, YEARS</p> </td> <td> <p>rundet auf das aktuelle Jahr oder versetzt dieses um die angegebene Anzahl von Jahren.</p> </td> </tr> <tr> <td> <p>MONTH, MONTHS</p> </td> <td> <p>Rundet auf den aktuellen Monat oder versetzt um die angegebene Anzahl von Monaten.</p> </td></tr><tr><td><p>DAY, DAYS, DATE</p></td><td><p>Rundet auf den aktuellen Tag des Monats oder versetzt um die angegebene Anzahl von Tagen.</p> </td></tr><tr><td><p>HOUR, HOURS￼ </p></td><td><p> ￼Rundet auf die aktuelle Stunde oder versetzt um die angegebene Anzahl von Stunden.</p> </td></tr><tr><td><p>MINUTE, MINUTES</p></td><td><p>Rundet auf die aktuelle Minute oder versetzt um die angegebene Anzahl von Minuten.</p> </td></tr><tr><td><p>SECOND, SECONDS</p></td><td><p>Rundet auf die aktuelle Sekunde oder versetzt um die angegebene Anzahl von Sekunden.</p> </td></tr><tr><td><p>MILISECOND, MILLISECONDS, MILLI, MILLIS</p></td><td><p> ￼Rundet auf die aktuelle Millisekunde oder versetzt um die angegebene Anzahl von Millisekunden.</p></td></tr></table>


#### Feldfacetten

Mithilfe von Feldfacetten können Sie die Suchbedingung für bestimmte Felder und deren genaue Werte festlegen, im Gegensatz zum Schreiben von Abfragen mit "freiem Text" mit verschiedenen Begriffe über den gesamten Index hinweg. Wir haben diese Syntax bereits in mehreren Beispielen in den vorherigen Abschnitten verwendet. Es folgen komplexere Beispiele.

**Syntax**

*field:value*

*field=value*

**Beschreibung**

Durchsucht das Feld nach dem spezifischen Wert. Der Wert kann ein Zeichenfolgenliteral, eine Nummer oder Datum/Uhrzeit sein.

Beispiel:


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**Syntax**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**Beschreibung**

Stellt Vergleiche bereit.

Beispiel:

	TimeGenerated>NOW+2HOURS


**Syntax**

*field:[from..to]*

**Beschreibung**

Enthält eine Bereichsfacettierung.

Beispiel:

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### Logische Operatoren

Abfragesprachen unterstützen die logischen Operatoren (AND, OR und NOT) und ihre jeweiligen Aliasse im C-Format (& &, ||, und!). Sie können Klammern verwenden, um diese Operatoren zu gruppieren.

Beispiele:

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
Sie können den logischen Operator für Filterargumente der obersten Ebene auslassen. In diesem Fall wird vom AND-Operator ausgegangen.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Filterausdruck</th> <th>entspricht</th> </tr> <tr> <td> <p>Systemfehler</p> </td> <td> <p>System Fehler</p> </td> </tr> <tr> <td> <p>System & Quot; Windows Server & Quot; ODER Schweregrad: 1</p> </td> <td> <p>System AND (& Quot; Windows Server & Quot; ODER Schweregrad: 1)</p> </td> </tr> </table>



### Befehle

Die Befehle gelten für die von der Abfrage zurückgegebenen Ergebnisse. Verwenden Sie den senkrechten Strich (|), um einen Befehl auf die abgerufenen Ergebnisse anzuwenden. Mehrere Befehle müssen durch einen senkrechten Strich (|) getrennt werden.

>[AZURE.NOTE]Befehlsnamen können in Groß- oder Kleinbuchstaben geschrieben werden, im Gegensatz zu den Feldnamen und den Daten.

#### Sortieren

Syntax:

	sort field1 asc|desc, field2 asc|desc, …

Sortiert die Ergebnisse nach bestimmten Feldern. Das Präfix "Asc/Desc" ist optional. Wenn sie ausgelassen werden, wird von der "Asc"-Sortierreihenfolge ausgegangen. Wenn eine Abfrage den **Sortieren**-Befehl nicht explizit verwendet, ist **TimeGenerated**-"desc" (absteigende Reihenfolge) das Standardverhalten. Es gibt die neuesten Ergebnisse immer zuerst zurück.

#### "Top"/"Limit"

Syntax:

	top number


	limit number
Schränkt die Antwort auf die ersten N-Ergebnisse ein.

Beispiel:

	Type:Alert errors detected | top 10

Gibt die höchsten 10 übereinstimmenden Ergebnisse zurück.

#### Überspringen

Syntax:

	skip number

Überspringt die Anzahl der aufgelisteten Ergebnisse.

Beispiel:

	Type:Alert errors detected | top 10 | skip 200

Gibt die höchsten 10 übereinstimmenden Ergebnisse zurück, beginnend bei 200 Ergebnissen.

#### Wählen Sie

Syntax:

	select field1, field2, ...

Beschränkt die Ergebnisse auf die Felder, die Sie auswählen.

Beispiel:

	Type:Alert errors detected | select Name, Severity

Schränkt die zurückgegebenen Ergebnisfelder auf **Name** und **Schweregrad** ein.

#### "Measure"

Der **Measure**-Befehl wird verwendet, um statistische Funktionen auf rohe Suchergebnisse anzuwenden. Dies ist sehr nützlich, um einen ersten *Group-by*-Überblick über die Daten zu erhalten. Wenn Sie den **Measure**-Befehl verwenden, zeigt Operational Insights eine Tabelle mit aggregierten Ergebnissen an.

Syntax:

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

Aggregiert die Ergebnisse nach **groupField** und berechnet die aggregierten "Measure"-Werte mit **aggregatedField**.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>Messen der Statistikfunktion </th> <th>Beschreibung </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>Der Name der Aggregatfunktion (ohne Berücksichtigung der Groß-und Kleinschreibung). Die folgenden Aggregatfunktionen werden unterstützt:</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>Das Feld, die aggregiert wird. Dieses Feld ist für die Aggregatfunktion COUNT optional, muss jedoch ein vorhandenes numerisches Feld für MAX, SUM, MIN, AVG oder STDDEV sein.</p> </td> </tr> <tr> <td> <p><em>FieldAlias</em> </p> </td> <td> <p>Der (optionale) Alias für den berechneten aggregierten Wert. Wenn nicht angegeben, ist der Name des Felds <em>AggregatedValue.</em></p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>Der Name des Felds, in dem der Ergebnissatz gruppiert ist. </p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>Das angegebene Zeitintervall im Format: </p> <p><em>nnnNAME</em> </p> <p></p> <p>Wobei: </p> <p>nnn ist die positive ganze Zahl.</p> <p><em>NAME</em> ist der Name des Intervalls</p> <p>Unterstützte Intervall Namen enthalten (Groß-und Kleinschreibung): </p> MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S<br><br></li> MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



Die Intervalloption kann nur in Datums-/Uhrzeit-Feldern verwendet werden (z. B. **TimeGenerated** und **TimeCreated**). Aktuell, dies wird nicht vom Dienst erzwungen, aber ein Feld ohne Datum/Uhrzeit, das an das Back-End übergeben wird, führt zu einem Laufzeitfehler. Wenn die Schemaüberprüfung implementiert wird, lehnt die Dienst-API Abfragen ab, die ohne Datum/Uhrzeit-Felder für die Intervall-Aggregation verwenden werden. Die aktuelle **Measure**-Implementierung unterstützt Intervallgruppierungen nur für die **Count**-Aggregatfunktion.

Wenn die BY-Klausel weggelassen wird, aber ein Intervall angegeben (als zweite Syntax), wird das **TimeGenerated**-Feld standardmäßig angenommen.

Beispiele:

**Beispiel 1**

	Type:Alert | measure count() as Count by ObjectId

*Erklärung*

Gruppiert die Warnungen nach **ObjectID** und berechnet die Anzahl der Warnungen für jede Gruppe. Der aggregierte Wert wird als das **Count**-Feld (Alias) zurückgegeben.

**Beispiel 2**

	Type:Alert | measure count() interval 1HOUR

*Erklärung*

Gruppiert die Warnungen in stündlichen Intervallen mithilfe des **TimeGenerated**-Felds, und gibt die Anzahl der Warnungen in jedem Intervall zurück.

**Beispiel 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*Erklärung*

Wie im vorherigen Beispiel, jedoch mit einem aggregierten Feldalias (**AlertsPerHour**).

**Beispiel 4**

	* | measure count() by TimeCreated interval 5DAYS

*Erklärung*

Gruppiert die Ergebnisse in stündlichen Intervallen mithilfe des **TimeCreated**-Felds, und gibt die Anzahl der Warnungen in jedem Intervall zurück.

**Beispiel 5**

	Type:Alert | measure max(Severity) by WorkflowName

*Erklärung*

Gruppiert die Warnungen anhand des Workloadnamens und gibt den Wert des maximalen Schweregrads der Warnung für jeden Workflow zurück.

**Beispiel 6**

	Type:Alert | measure min(Severity) by WorkflowName

*Erklärung*

Wie im vorherigen Beispiel, jedoch mit der aggregierten **Min** -Funktion.

**Beispiel 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*Erklärung*

Gruppiert PerfHourly von ObjectId und berechnet den Mittelwert (Durchschnitt).

**Beispiel 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*Erklärung*

Wie im vorherigen Beispiel, verwendet jedoch **Sum**.

**Beispiel 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*Erklärung*

Wie im vorherigen Beispiel, verwendet jedoch **STDDEV**.

**Beispiel 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*Erklärung*

Ruft die oberen fünf Workflows mit der maximalen Anzahl von Warnungen ab.

#### Where

Syntax:

**where** AggregatedValue > 20

Kann nur nach einem **Measure**-Befehl verwendet werden, um die aggregierten Ergebnisse weiter zu filtern, die die **Measure**-Aggregationsfunktion erzeugt hat.

Beispiele:

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## Search-Feld- und Facetreferenz

Wenn Sie Suche verwenden, um Daten zu finden, zeigen die Ergebnisse verschiedene Feld und Facetten an. Jedoch sind einige Informationen, die Sie sehen, möglicherweise nicht sehr aussagekräftig. Mit den folgenden Informationen können Sie die Ergebnisse besser verstehen.

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>Field</b></th>
		<th><b>Search Type<b></th>
		<th><b>Beschreibung</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>Alle</p>
		</td>
		<td>
		<p>Verwendet für die Datenpartitionierung</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>Alle</p>
		</td>
		<td>
		<p>Zur Ausführung der Zeitachse, des Timeselectors (Suche und andere Bildschirme) verwendet. Hier wird dargestellt, wann die Datenmenge (in der Regel auf dem Agent) generiert wurde. Die Zeit wird im ISO-Format ausgedrückt und ist immer UTC. Im Fall von "Typen", die auf vorhandenen Instrumentation basieren (d. h. die Ereignisse in einem Protokoll), ist dies in der Regel Echtzeit, in der Eintrag/Zeile/Protokolldatensatz protokolliert wurde; für einige andere Typen, die erstellt werden, entweder über Verwaltungspacks oder in der Cloud - d. h. Empfehlungen/Warnungen/Updateagent/usw.; dies der Zeitpunkt, zu dem neue Datenelemente mit einer Momentaufnahme einer Konfiguration erfasst wurden oder eine Empfehlungs-Warnung erzeugt wurde, die darauf basiert.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventId</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>EventID im Windows-Ereignisprotokoll</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Ereignisprotokoll, in dem das Ereignis von Windows protokolliert wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Kritisch / Warnung / Information / Erfolg</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Numerischer Wert für Kritisch / Warnung / Information / Erfolg (verwenden Sie stattdessen EventLevelName für leichtere/lesbarere Abfragen)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>Alle</p>
		</td>
		<td>
		<p>Woher die Daten stammen (im Hinblick auf den Modus "Anfügen" zum Service, d. h. Operations Manager, Operational Insights (= generiert die Daten in der Cloud), Azure-Speicher (Daten aus WAD) usw.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows-Leistungs-Objektname</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Name der Windows-Leistungsindikatorinstanzen</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CountName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Name des Windows-Leistungsindikators</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Anzeigename des Objekts, das das Ziel einer Leistungssammlungsregel in Operations Manager oder das Objekt ist, das von Operational Insights ermittelt wurde, oder für das die Warnung generiert wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Anzeigename des übergeordneten Elements des übergeordneten Elements (in einer doppelten Hostbeziehung: d. h. SqlDatabase gehostet von SqlInstance gehostet von Windows-Computer) des Objekts, das das Ziel einer Leistungssammlungsregel in Operations Manager oder das Objekt ist, das von Operational Insights ermittelt wurde, oder für das die Warnung generiert wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Computer</p>
		</td>
		<td>
		<p>Die meisten Typen. </p>
		</td>
		<td>
		<p>Computername, zu dem die Daten gehören.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Computername, zu dem die Daten gehören (identisch mit "Computer").</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>"ThreatStatusRank" ist eine numerische Darstellung des Bedrohungsstatus und ähnelt HTTP-Antwortcodes. Wir haben zwischen den Zahlen Lücken gelassen (weshalb keine Bedrohungen 150 und nicht 100 oder 0 ist), sodass Platz für das Hinzufügen eines neuen Status ist. Wenn ein Rollup für den Bedrohungs- und Schutzstatus durchgeführt wird, soll der schlechteste Status angezeigt werden, in dem sich der Computer während des ausgewählten Zeitraums befand. Zur Bewertung der verschiedenen Zustände werden Zahlen verwendet, sodass nach dem Datensatz mit der höchsten Nummer gesucht werden kann.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Beschreibung des "ThreatStatus", 1:1 "ThreatStatusRank" zugeordnet.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Antimalware-Produkt, das auf dem Computer erkannt wird: keins, Microsoft Malware Removal Tool, Forefront, usw..</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus, RequiredUpdate</p>
		</td>
		<td>
		<p>Integritätsdienst-ID für den Agent dieses Computers.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>Die meisten Typen. </p>
		</td>
		<td>
		<p>Integritätsdienst-ID für den Agent dieses Computers.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>Die meisten Typen. </p>
		</td>
		<td>
		<p>Name der Verwaltungsgruppe für mit Operations Manager verbundene Agents; andernfalls null/leer.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjektType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>Typ (wie in Management Pack "Type"/Klasse von Operations Manager ) für dieses Objekt, das von der Operational Insights-Konfigurationsbewertung ermittelt wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Name des Updates, das als nicht installiert erkannt wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Wann fand das Update statt, das auf Microsoft Update veröffentlicht wurde?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Server</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Computername, zu dem die Daten gehören (identisch mit "Computer").</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Produkt</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Produkt, auf das die Aktualisierung angewendet wird.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Updatetyp (Updaterollup, Servicepack, usw.).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Die KB-Artikel-ID, die diese bewährte Methode  oder das Update beschreibt.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Name der Regel oder des Monitors, die oder der die Warnung erzeugt hat.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Schweregrad</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Schweregrad der Warnung.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Priorität</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Priorität der Warnung.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Wird diese Warnung von einem Monitor (True) oder einer Regel (False) generiert?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML mit den Parametern der Operational Insights-Warnung.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>XML mit dem "Kontext" der Operational Insights-Warnung.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Workload</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Technologie oder "Arbeitsauslastung", auf die sich die Warnung bezieht. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>Empfehlung</p>
		</td>
		<td>
		<p>Technologie oder "Arbeitsauslastung", auf die sich die Empfehlung bezieht.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Beschreibung</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Beschreibung der Warnung (kurz)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Vor wie vielen Tagen (in Bezug auf 'TimeGenerated' für diesen Datensatz) hat dieser Agent ein Update von WSUS oder Microsoft Update installiert?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p> Eine Kategorisierung in "Perioden" basierend auf DaysSinceLastUpdate, wann zuletzt ein Update von WSUS/Microsoft Update installiert wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Ist die Überprüfung der automatischen Aktualisierung auf diesem Agent aktiviert oder deaktiviert?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Ist die Überprüfung der automatischen Aktualisierung auf automatisches Herunterladen und Installieren, nur Herunterladen oder nur Überprüfen festgelegt?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Versionsnummer des Microsoft Update-Agents</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Auf welchen WSUS-Server ist dieser Update-Agent ausgerichtet?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Version des Betriebssystems, auf dem dieser Update-Agent ausgeführt wird.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Name</p>
		</td>
		<td>
		<p>Empfehlung, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Der Name/Titel der Empfehlung, oder der Name der Eigenschaft von der Operational Insights-Konfigurationsbewertung</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Wert</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Wert einer Eigenschaft aus der Operational Insights-Konfigurationsbewertung</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>Empfehlung</p>
		</td>
		<td>
		<p>URL zum KB-Artikel, der diese bewährte Methode  oder das Update beschreibt.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>Empfehlung</p>
		</td>
		<td>
		<p>Empfehlungen gehören zu den wenigen Typen, deren Datensätze 'aktualisiert' und nicht nur dem Suchindex hinzugefügt werden. Dieser Status ändert, ob die Empfehlung aktiv/offen ist oder ob Operational Insights erkennt, dass das Problem gelöst wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Gerenderte Beschreibung (wiederverwendeter Text mit aufgefüllten Parametern) eines Windows-Ereignisses.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>XML mit den Parametern im Abschnitt 'Data' eines Windows-Ereignisses (wie in der Ereignisanzeige dargestellt).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>XML mit dem gesamten 'Data'-Abschnitt eines Windows-Ereignisses (wie in der Ereignisanzeige dargestellt).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Quelle</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Ereignisprotokoll-Quelle, die das Ereignis generiert hat.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Ereigniskategorie, direkt aus dem Windows-Ereignisprotokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UserName</p>
		</td>
		<td>
		<p>Ereignis</p>
		</td>
		<td>
		<p>Benutzername des Windows-Ereignisses (in der Regel NT AUTHORITY\LOCALSYSTEM).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Durchschnittswert für die stündliche Aggregation eines Leistungsindikators. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Min</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Mindestwert im stündlichen Intervall eines stündlich aggregierten Leistungsindikators.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Max</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Maximalwert im stündlichen Intervall eines stündlich aggregierten Leistungsindikators.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Der 95. Prozentwert für den stündlichen Intervall eines stündlich aggregierten Leistungsindikators.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Wie viele Proben von "rohen" Leistungsindikatoren verwendet wurden, um diesen stündlich aggregierten Datensatz zu erstellen.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Bedrohung</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Name der Malware gefunden</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Azure-Speicherkonto, aus dem das Protokoll gelesen wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Die Azure-Bereitstellungs-ID des Cloud-Dienstes, zu dem das Protokoll gehört.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Rolle</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Rolle des Azure-Cloud-Dienstes, zu dem das Protokoll gehört.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>RoleInstance der Azure-Rolle, zu dem das Protokoll gehört.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>IIS-Website, zu der das Protokoll gehört (Metabasis-Notation); das  "s-Sitename "-Feld im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Das "s-Computername "-Feld im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>IP-Adresse des Servers, an die die HTTP-Anforderung adressiert wurde. Das "s-ip"-Feld im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP-Methode (GET, POST, usw.), die vom Client in der HTTP-Anforderung verwendet wurde. Die cs-Methode im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>IP-Adresse des Client, von dem die HTTP-Anforderung stammt. Das "c-ip"-Feld im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP-Benutzer-Agent, der vom Client deklariert wurde (im Browser oder auf andere Weise). Der "cs-user"-Agent im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP-Statuscode (200/403/500/usw.), der vom Server an den Client zurückgegeben wurde. Der "cs"-Status im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Dauer (in Millisekunden), die die Anforderung bis zum Abschluss gebraucht hat. Das "timetaken"-Feld im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Relativer Uri (ohne Hostadresse, d. h. "/ Suchen"), der angefordert wurde. Das "cs-uristem"-Feld im ursprünglichen Protokoll.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>URI-Abfrage. URI-Abfragen sind nur für dynamische Seiten erforderlich, z. B. ASP-Seiten, also enthält dieses Feld in der Regel einen Bindestrich für statische Seiten.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Serverport, an den die HTTP-Anforderung gesendet wurde (und die IIS überwacht, da sie davon übernommen wurde).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p> Authentifizierter Benutzername, wenn die Anforderung authentifiziert und nicht anonym ist.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Die in der Anforderung verwendete HTTP-Protokoll-Version (d. h.  "HTTP/1.1")</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Cookie-Informationen</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Website, die der Benutzer zuletzt besucht hat. Diese Website stellte eine Verknüpfung zur aktuellen Website her. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Angeforderter Hostheader (d. h. "www.mysite.com").</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Untergeordneter Fehlercode</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Windows-Statuscode</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>In der Anforderung vom Client an den Server gesendete Bytes.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>In der Antwort vom Server an den Client zurückgegebene Bytes.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Art der Änderung (WindowsServices / Software / usw.)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Kategorie der Änderung (Geändert / Hinzugefügt / Entfernt)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Softwaretyp (Update / Anwendung)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Name der Software (gilt nur für Softwareänderungen).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Herausgeber </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Hersteller, der die Software veröffentlicht (gilt nur für Softwareänderungen).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Typ der Änderung, die auf einen Windowsdienst angewendet wurde (State / StartupType / Path / ServiceAccount) – gilt nur für Änderungen eines Windows-Dienstes.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Anzeigename des Dienstes, der geändert wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Name des Dienstes, der geändert wurde.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Neuer (aktueller) Status des Dienstes.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Vorheriger bekannter Dienststatus (gilt nur, wenn der Dienstzustand geändert wurde).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Starttyp des Dienstes</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Vorheriger Starttyp des Dienstes (gilt nur, wenn der Starttyp des Diensts geändert wurde).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Dienstkonto</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Vorheriges Dienstkonto (gilt nur, wenn das Dienstkonto geändert wurde).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Pfad zur ausführbaren Datei des Windows-Dienstes</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Vorherige Pfad der ausführbaren Datei des Windows-Dienstes (gilt nur, wenn dieser geändert wurde).</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Beschreibung des Dienstes</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Zurück </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Vorheriger Zustand dieser Software (Installiert / Nicht Installiert / vorherige Version)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Aktuell</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Neuester Zustand dieser Software (Installiert / Nicht Installiert / aktuelle Version)</p>
		</td>
	</tr>
</table>

## Blogbeiträge - Search-Anwendungsfälle
- [W3C IIS Logs Search in Microsoft Azure Operational Insights](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Monitoring SQL Backup Failures with Azure Operational Insights Search and Dashboards](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [IIS Management Pack Event-Alerting Rules’s OpInsights Searches Equivalents](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [Useful Operational Insights Search Query Collection](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## Weitere Ressourcen
Stefan Roth hat einen praktisch Spickzettel für die Suche erstellt. Besuchen Sie seinen [Blog](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/), um mehr zu erfahren und seinen Spickzettel herunterzuladen.

<!---HONumber=July15_HO2-->