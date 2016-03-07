<properties
   pageTitle="Optimieren der Datenaggregation und Abfrageleistung mit Elasticsearch in Azure | Microsoft Azure"
   description="Enthält eine Zusammenfassung der Aspekte zur Optimierung der Abfrage- und Suchleistung für Elasticsearch."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>
   
# Optimieren der Datenaggregation und Abfrageleistung mit Elasticsearch in Azure

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

Ein Hauptgrund für die Nutzung von Elasticsearch ist die Unterstützung des Durchsuchens von Daten. Benutzer sollten die gewünschten Informationen immer schnell finden können. Außerdem muss das System Benutzer in die Lage versetzen, Fragen zu Daten zu stellen, nach Korrelationen zu suchen und zu Schlussfolgerungen zu kommen, die als Grundlage für Geschäftsentscheidungen dienen. Diese Art der Verarbeitung unterscheidet Daten von reinen Informationen.

In diesem Dokument sind die Optionen zusammengefasst, deren Verwendung Sie erwägen können, wenn es um den besten Weg zur Optimierung Ihres Systems in Bezug auf die Abfrage- und Suchleistung geht.

Alle Leistungsempfehlungen hängen größtenteils von den Szenarien ab, die für Ihre Situation gelten, sowie vom Volumen der Daten, die Sie indizieren, und der Rate, mit der Anwendungen und Benutzer Ihre Daten abfragen. Sie sollten die Ergebnisse aller Änderungen der Konfiguration oder Indizierungsstruktur sorgfältig testen, indem Sie Ihre eigenen Daten und Workloads verwenden, um die Vorteile Ihrer jeweiligen Szenarien zu bewerten. Aus diesem Grund werden in diesem Dokument auch eine Reihe von Benchmark-Tests beschrieben, die für ein bestimmtes Szenario durchgeführt wurden. Das Szenario wurde jeweils mit unterschiedlichen Konfigurationen implementiert. Sie können den Ansatz anpassen, den Sie zum Bewerten der Leistung Ihrer eigenen Systeme wählen.

## Aspekte der Index- und Abfrageleistung

In diesem Abschnitt werden einige häufige Faktoren beschrieben, die wichtig beim Entwerfen von Indizes sind, mit denen schnelle Abfrage- und Suchvorgänge unterstützt werden sollen.

### Speichern von mehreren Typen in einem Index

Ein Elasticsearch-Index kann mehrere Typen enthalten. Es ist unter Umständen besser, diesen Ansatz zu vermeiden und einen separaten Index für jeden Typ zu erstellen. Beachten Sie die folgenden Punkte:

- Für unterschiedliche Typen werden ggf. unterschiedliche Analysemodule angegeben. Es ist nicht immer eindeutig, welches Analysemodul von Elasticsearch verwendet werden sollte, wenn eine Abfrage auf Indexebene anstatt auf Typebene durchgeführt wird. Ausführliche Informationen hierzu finden Sie unter [Avoiding Type Gotchas](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) (Vermeiden von Typkomplikationen).

- Shards für Indizes, die mehrere Typen enthalten, sind meist größer als Shards für Indizes, die nur einen Typ enthalten. Je größer ein Shard ist, desto höher ist der Aufwand für Elasticsearch beim Filtern von Daten, wenn Abfragen durchgeführt werden.

- Falls zwischen Datenvolumen in Bezug auf die Typen ein signifikanter Konflikt besteht, können die Informationen für einen Typ ggf. in geringer Menge auf viele Shards verteilt werden. Dies führt zu einer Verringerung der Effizienz von Suchen, bei denen diese Daten abgerufen werden.

![](./media/guidance-elasticsearch/query-performance1.png)

***Abbildung 1: Auswirkungen der Freigabe eines Index zwischen Typen***

Dieses Szenario ist in Abbildung 1 dargestellt. Im oberen Teil des Diagramms wird für Dokumente vom Typ A und Typ B derselbe Index verwendet. Es sind noch viel mehr Dokumente vom Typ A und Typ B vorhanden. Bei Suchen nach Typ A werden alle vier Shards abgefragt. Der untere Teil des Diagramms zeigt die Auswirkung, wenn für jeden Typ separate Indizes erstellt werden. In diesem Fall ist bei Suchen nach Typ A nur der Zugriff auf zwei Shards erforderlich.

Kleine Shards können gleichmäßiger als große Shards verteilt werden, sodass es für Elasticsearch einfacher ist, die Last auf die Knoten zu verteilen.

Verschiedene Typen weisen unter Umständen unterschiedliche Aufbewahrungsdauern auf. Es kann schwierig sein, alte Daten zu archivieren, die auf Shards zusammen mit neuen Daten vorhanden sind.

Unter bestimmten Umständen kann die gemeinsame Nutzung eines Index über Typen hinweg aber effizient sein. Hierzu muss Folgendes gelten:

- Suchen werden regelmäßig für Typen durchgeführt, die sich in demselben Index befinden.

- Die Typen weisen jeweils nur eine kleine Zahl von Dokumenten auf. Die Nutzung einer separaten Gruppe von Shards für jeden Typ kann in diesem Fall zu erheblichem Mehraufwand führen.

### Optimieren von Indextypen

Ein Elasticsearch-Index enthält eine Kopie der ursprünglichen JSON-Dokumente, die zum Auffüllen verwendet wurden. Diese Informationen sind im Feld [*\_source*](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-source-field.html#mapping-source-field) jedes indizierten Elements enthalten. Diese Daten können nicht durchsucht werden, aber sie werden standardmäßig von *get*- und *search*-Anforderungen zurückgegeben. Dieses Feld verursacht jedoch Mehraufwand und belegt Speicher, sodass die Shards größer werden und der Umfang der durchgeführten E/A-Vorgänge zunimmt. Sie können das Feld *\_source* pro Typ deaktivieren:

```http
PUT my_index
{
    "mappings": {
		"my_type": {
			"_source": {
				"enabled": false
			}
		}
	}
}
```

Wenn Sie dieses Feld deaktivieren, ist es auch nicht mehr möglich, die folgenden Vorgänge durchzuführen:

- Aktualisieren von Daten im Index mit der *Update*-API
- Durchführen von Suchvorgängen, bei denen hervorgehobene Daten zurückgegeben werden
- Neuindizieren aus einem Elasticsearch-Index direkt in einen anderen
- Ändern von Zuordnungen oder Analyseeinstellungen
- Debuggen von Abfragen per Anzeige des Originaldokuments
 
### Neuindizieren von Daten

Die Anzahl von Shards, die für einen Index verfügbar sind, bestimmt letztendlich die Kapazität des Index. Sie können eine anfängliche (fundierte) Schätzung dazu abgeben, wie viele Shards erforderlich sind. Berücksichtigen Sie aber immer zuerst Ihre Strategie für die Neuindizierung von Dokumenten. In vielen Fällen kann die Neuindizierung eine beabsichtigte Aufgabe sein, wenn der Umfang der Daten zunimmt. Es kann ratsam sein, einem Index am Anfang keine größere Zahl von Shards zuzuordnen, um die Suche zu optimieren, sondern neue Shards zuzuordnen, wenn die Datenmenge zunimmt. In anderen Fällen muss die Neuindizierung ggf. eher auf Ad-hoc-Basis durchgeführt werden, falls sich Ihre Schätzungen in Bezug auf die Zunahme des Datenvolumens als nicht zutreffend herausstellen.

> [AZURE.NOTE] Die Neuindizierung ist für Daten, die schnell altern, unter Umständen nicht erforderlich. In diesem Fall kann eine Anwendung für jeden Zeitraum ggf. einen neuen Index erstellen. Beispiele hierfür sind Leistungsprotokolle oder Überwachungsdaten, die jeden Tag in einem neuen Index gespeichert werden können.

Eine effektive Neuindizierung umfasst das Erstellen eines neuen Index aus den Daten in einem alten Index und das anschließende Entfernen des alten Index. Wenn ein Index groß ist, kann dieser Prozess lange dauern, und Sie müssen unter Umständen sicherstellen, dass die Daten während dieser Zeit durchsuchbar bleiben. Aus diesem Grund sollten Sie einen [Alias für jeden Index](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) erstellen, und bei Abfragen sollten Daten über diese Aliase abgerufen werden. Behalten Sie den Verweis des Alias auf den alten Index beim Neuindizieren bei, und ändern Sie den Verweis dann in den neuen Index, nachdem die Neuindizierung abgeschlossen ist. Dieser Ansatz ist auch für den Zugriff auf zeitbasierte Daten nützlich, bei dem jeden Tag ein neuer Index erstellt wird. Verwenden Sie zum Zugreifen auf die aktuellen Daten einen Alias, für den ein Rollover zum neuen Index durchgeführt wird, nachdem dieser erstellt wurde.

### Verwalten von Zuordnungen

Elasticsearch verwendet Zuordnungen, um zu bestimmen, wie die Daten interpretiert werden sollen, die in den einzelnen Feldern in einem Dokument anfallen. Jeder Typ verfügt über eine eigene Zuordnung, womit praktisch ein Schema für diesen Typ definiert wird. Elasticsearch verwendet diese Informationen zum Generieren von invertierten Indizes für jedes Feld in den Dokumenten eines Typs. In allen Dokument verfügt jedes Feld über einen Datentyp (z. B. *string*, *date* oder *long*) und einen Wert. Sie können die Zuordnungen für einen Index angeben, wenn der Index am Anfang erstellt wird, oder sie können von Elasticsearch abgeleitet werden, wenn einem Typ neue Dokumente hinzugefügt werden. Beachten Sie hierbei aber die folgenden Punkte:

- Dynamisch generierte Zuordnungen können Fehler verursachen. Dies hängt davon ab, wie Felder interpretiert werden, wenn Dokumente einem Index hinzugefügt werden. Dokument 1 kann beispielsweise ein Feld A aufweisen, das eine Zahl enthält und Elasticsearch zum Hinzufügen einer Zuordnung mit der Angabe bewegt, dass dieses Feld vom Typ *long* ist. Wenn ein nachfolgendes Dokument hinzugefügt wird, in dem Feld A nicht numerische Daten enthält, tritt ein Fehler auf. In diesem Fall hätte Feld A vermutlich als Zeichenfolge interpretiert werden müssen, als das erste Dokument hinzugefügt wurde. Das Angeben dieser Zuordnung beim Erstellen des Index kann dazu beitragen, Probleme dieser Art zu verhindern.

- Entwerfen Sie Ihre Dokumente so, dass das Generieren übermäßig großer Zuordnungen vermieden wird. Dies kann nämlich dazu führen, dass bei Suchen erheblicher Mehraufwand anfällt, viel Arbeitsspeicher verbraucht wird und für Abfragen beim Suchen nach Daten Fehler auftreten. Verwenden Sie eine einheitliche Namenskonvention für Felder in Dokumenten, die denselben Typ haben. Verwenden Sie beispielsweise keine Feldnamen wie „first\_name“, „FirstName“ und „forename“ in unterschiedlichen Dokumenten, sondern in jedem Dokument den gleichen Feldnamen. Versuchen Sie auch nicht, Werte als Schlüssel zu verwenden. (Dies ist ein gängiger Ansatz für Column-Family-Datenbanken, aber bei Elasticsearch kann es zu Ineffizienzen und Fehlern führen.) Weitere Informationen finden Sie unter [Mapping Explosion](https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion) (Zunahme der Zuordnungen).

- Verwenden Sie *not\_analyzed*, um eine Tokenisierung zu verhindern (falls zutreffend). Wenn ein Dokument beispielsweise ein Zeichenfolgenfeld mit dem Namen *data* aufweist, das den Wert „ABC-DEF“ enthält, können Sie wie folgt nach allen Dokumenten suchen, die eine Übereinstimmung für diesen Wert ergeben:

```http
GET /myindex/mydata/_search
{
	"query" : {
		"filtered" : {
			"filter" : {
				"term" : {
					"data" : "ABC-DEF"
				}
			}
		}
	}
}
```

Bei dieser Suche werden aber nicht die erwarteten Ergebnisse zurückgegeben, da die Zeichenfolge „ABC-DEF“ während der Indizierung auf eine bestimmte Weise tokenisiert wird. Aufgrund des Bindestrichs wird sie in zwei Token unterteilt: „ABC“ und „DEF“. Dieses Feature wurde so konzipiert, dass die Volltextsuche unterstützt wird. Wenn die Zeichenfolge aber als einzelnes atomisches Element interpretiert werden soll, sollten Sie die Tokenisierung deaktivieren, wenn das Dokument dem Index hinzugefügt wird. Sie können beispielsweise folgende Zuordnung verwenden:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					"type" : "string",
					"index" : "not_analyzed"
				}
			}
		}
	}
}
```

Weitere Informationen finden Sie unter [Finding Exact Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_term_filter_with_text) (Suchen nach exakten Werten).

### Verwenden von „Doc Values“

Für viele Abfragen und Aggregationen ist es erforderlich, dass Daten im Rahmen eines Suchvorgangs sortiert werden. Für die Sortierung müssen einer Liste mit Dokumenten ein oder mehrere Begriffe hinzugefügt werden können. Um diesen Prozess zu unterstützen, kann Elasticsearch alle Werte für ein Feld, das als Sortierschlüssel verwendet wird, in den Arbeitsspeicher laden. Diese Informationen werden als *Felddaten* bezeichnet. Der Zweck besteht darin, dass beim Zwischenspeichern von Felddaten im Arbeitsspeicher weniger E/A-Aufwand anfällt. Außerdem kann dies schneller sein, als immer wieder dieselben Daten vom Datenträger zu lesen. Falls ein Feld aber über eine hohe Kardinalität verfügt, kann beim Speichern der Felddaten im Arbeitsspeicher viel Heapspeicher verbraucht werden. Unter Umständen kann dies die Durchführung anderer gleichzeitiger Vorgänge beeinträchtigen, oder es kann sogar der Speicherplatz erschöpft sein und ein Elasticsearch-Fehler auftreten.

Ein alternativer Ansatz besteht darin, die Tatsache zu nutzen, dass Elasticsearch *Doc Values* unterstützt. Ein „Doc Value“ ähnelt einem Element mit speicherinternen Felddaten. Die Ausnahme besteht aber darin, dass er auf Datenträger gespeichert und erstellt wird, wenn Daten in einem Index gespeichert werden (Felddaten werden dynamisch erstellt, wenn eine Abfrage durchgeführt wird). Doc Values verbrauchen keinen Heapspeicher und sind daher nützlich für Abfragen, bei denen Daten über Felder hinweg sortiert oder aggregiert werden, die eine sehr große Zahl eindeutiger Werte enthalten. Außerdem kann der reduzierte Druck auf den Heap dazu beitragen, die Leistungsunterschiede zwischen dem Abrufen der Daten vom Datenträger und dem Lesen aus dem Arbeitsspeicher auszugleichen. Die Garbage Collection wird voraussichtlich weniger häufig durchgeführt, und es ist weniger wahrscheinlich, dass sich Auswirkungen auf andere gleichzeitige Vorgänge ergeben, für die der Arbeitsspeicher genutzt wird.

Sie aktivieren und deaktivieren Doc Values jeweils pro Eigenschaft in einem Index, indem Sie das Attribut *doc\_values* verwenden. Dies wird im folgenden Beispiel veranschaulicht:

```http
PUT /myindex
{
	"mappings" : {
		"mydata" : {
			"properties" : {
				"data" : {
					...
				"doc_values": true
				}
			}
		}
	}
}
```

> [AZURE.NOTE] Doc Values sind ab Elasticsearch Version 2.0.0 aufwärts automatisch aktiviert.

Die genauen Auswirkungen der Nutzung von Doc Values richten sich in der Regel stark nach Ihren eigenen Daten und Abfrageszenarien. Seien Sie also darauf vorbereitet, Leistungstests durchzuführen, um die Nützlichkeit zu ermitteln. Beachten Sie außerdem, dass Doc Values zusammen mit analysierten Zeichenfolgenfeldern nicht funktionieren. Weitere Informationen finden Sie unter [Doc Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values).

### Verwenden von Clientknoten

Alle Abfragen werden von dem Knoten verarbeitet, der die Anforderung als Erstes empfängt. Dieser Knoten sendet weitere Anforderungen an alle anderen Knoten, die Shards für die abgefragten Indizes enthalten, und sammelt dann die Ergebnisse, um die Antwort zurückzugeben. Wenn eine Abfrage das Aggregieren von Daten oder komplexe Berechnungen umfasst, ist der erste Knoten für die Durchführung der richtigen Verarbeitung verantwortlich. Wenn Ihr System eine relativ kleine Anzahl von komplexen Abfragen unterstützen muss, können Sie erwägen, einen Pool mit Clientknoten zu erstellen, um die Last für die Datenknoten zu verteilen. Wenn Ihr System dagegen eine große Anzahl von einfachen Abfragen verarbeiten muss, sollten Sie diese Anforderungen direkt an die Datenknoten senden und einen Load Balancer verwenden, um die Anforderungen gleichmäßig zu verteilen.

### Verwenden von Replikaten zum Reduzieren von Abfragekonflikten

Eine häufige verwendete Strategie zum Verbessern der Leistung von Abfragen ist die Erstellung von vielen Replikaten für jeden Index. Vorgänge zum Abrufen von Daten können durchgeführt werden, indem Daten von einem Replikat beschafft werden. Diese Strategie kann aber erhebliche Auswirkungen auf die Leistung von Vorgängen für die Datenerfassung haben und muss daher mit Bedacht in Szenarios angewendet werden, in denen es um gemischte Workloads geht. Außerdem ist diese Strategie nur von Vorteil, wenn die Replikate über Knoten verteilt sind und nicht mit primären Shards um Ressourcen konkurrieren, die Teil desselben Index sind. Beachten Sie, dass es möglich ist, die Anzahl von Replikaten für einen Index dynamisch zu erhöhen oder zu verringern.

### Verwenden des Caches für Shardanforderungen

Elasticsearch kann die lokalen Daten, die von Abfragen angefordert werden, auf jedem Shard im Arbeitsspeicher zwischenspeichern. So können Abfragen, die auf dieselben Daten zugreifen, schneller ausgeführt werden. Daten können aus dem Arbeitsspeicher abgerufen werden, anstatt aus dem Datenträgerspeicher. Die Daten im Cache werden ungültig gemacht, wenn das Shard aktualisiert wird und sich die Daten ändern. Die Häufigkeit der Aktualisierungen richtet sich nach dem Wert der Einstellung *refresh\_interval* des Index. Der Cache für Shardanforderungen eines Index ist standardmäßig deaktiviert, aber Sie können ihn wie folgt aktivieren:

```http
PUT /myindex/_settings
{
	"index.requests.cache.enable": true
}
```

Am besten eignet sich der Cache für Shardanforderungen für Informationen, die relativ statisch bleiben, z. B. Verlaufs- oder Protokollierungsdaten.

## Testen und Analysieren der Leistung von Aggregationen und Suchen
In diesem Abschnitt werden die Ergebnisse einer Reihe von Tests beschrieben, die für verschiedene Cluster- und Indexkonfigurationen durchgeführt wurden. Bei jedem Test wurde mit einem leeren Index begonnen, der im Verlauf des Tests aufgefüllt wurde, indem Masseneinfügungen durchgeführt wurden (bei jedem Vorgang wurden 1.000 Dokumente hinzugefügt). Gleichzeitig wurden verschiedene Abfragen zum Suchen nach bestimmten Daten und Generieren von Aggregationen alle fünf Sekunden wiederholt. Mit den Tests sollte ermittelt werden, wie sich das Datenvolumen auf die Abfrageleistung auswirkt.

Jedes Dokument im Index hatte das gleiche Schema. In dieser Tabelle sind die Felder des Schemas zusammengefasst:

 Name | Typ | Hinweise |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organisation | String | Für den Test werden 200 eindeutige Organisationen generiert. |
 CustomField1 - CustomField5 |String |Dies sind fünf Zeichenfolgenfelder, die auf die leere Zeichenfolge festgelegt werden.|
 DateTimeReceivedUtc |Timestamp |Das Datum und die Uhrzeit des Zeitpunkts, zu dem das Dokument hinzugefügt wurde.|
 Host |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 HttpMethod |String |Dieses Feld ist auf einen der folgenden Werte festgelegt: „POST“, „GET“, „PUT“.|
 HttpReferrer |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 HttpRequest |String |Dieses Feld wird mit zufälligem Text aufgefüllt, der zwischen 10 und 200 Zeichen lang ist.|
 HttpUserAgent |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 HttpVersion |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 OrganizationName |String |Dieses Feld ist auf den gleichen Wert wie das Feld „Organization“ festgelegt.|
 SourceIp |IP |Dieses Feld enthält eine IP-Adresse, die den „Ursprung“ der Daten angibt. |
 SourceIpAreaCode |Long |Dieses Feld ist auf 0 festgelegt.|
 SourceIpAsnNr |String |Dieses Feld ist auf „AS#####“ festgelegt.|
 SourceIpBase10 |Long |Dieses Feld ist auf 500 festgelegt.|
 SourceIpCountryCode |String |Dieses Feld enthält einen Ländercode mit zwei Zeichen. |
 SourceIpCity |String |Dieses Feld enthält eine Zeichenfolge, mit der ein Ort in einem Land angegeben wird. |
 SourceIpLatitude |Double |Dieses Feld enthält einen zufälligen Wert.|
 SourceIpLongitude |Double |Dieses Feld enthält einen zufälligen Wert.|
 SourceIpMetroCode |Long |Dieses Feld ist auf 0 festgelegt.|
 SourceIpPostalCode |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 SourceLatLong |Geo point |Dieses Feld ist auf einen zufälligen geografischen Punkt festgelegt.|
 SourcePort |String |Dieses Feld wird durch die Zeichenfolgendarstellung einer zufälligen Zahl aufgefüllt.|
 TargetIp |IP |Wird mit einer zufälligen IP-Adresse im Bereich von 0.0.100.100 bis 255.9.100.100 aufgefüllt.|
 SourcedFrom |String |Dieses Feld ist auf die Zeichenfolge „MonitoringCollector“ festgelegt.|
 TargetPort |String |Dieses Feld wird durch die Zeichenfolgendarstellung einer zufälligen Zahl aufgefüllt.|
 Rating |String |Dieses Feld wird mit einem von zwanzig verschiedenen Zeichenfolgenwerten aufgefüllt, die nach dem Zufallsprinzip ausgewählt werden.|
 UseHumanReadableDateTimes |Boolean |Dieses Feld ist auf „false“ festgelegt.|

Die folgenden Abfragen wurden von jedem Durchlauf des Tests als Batch durchgeführt (die Namen in Kursivschrift werden verwendet, um im restlichen Teil dieses Dokuments auf diese Abfragen zu verweisen):

- Wie viele Dokumente mit jedem *Rating*-Wert wurden innerhalb der letzten 15 Minuten eingegeben (*Count By Rating*)?

- Wie viele Dokumente wurden innerhalb der letzten 15 Minuten in jedem 5-Minuten-Intervall hinzugefügt (*Count Over Time*)?

- Wie viele Dokumente mit jedem *Rating*-Wert wurden für jedes Land innerhalb der letzten 15 Minuten hinzugefügt (*Hits By Country*)?

- Welche 15 Organisationen kommen in Dokumenten, die innerhalb der letzten 15 Minuten hinzugefügt wurden, am häufigsten vor (*Top 15 Organizations*)?

- Wie viele unterschiedliche Organisationen kommen in Dokumenten vor, die innerhalb der letzten 15 Minuten hinzugefügt wurden (*Unique Count Organizations*)?

- Wie viele Dokumente wurden innerhalb der letzten 15 Minuten hinzugefügt (*Total Hits Count*)?

- Wie viele unterschiedliche *SourceIp*-Werte kommen in Dokumenten vor, die innerhalb der letzten 15 Minuten hinzugefügt wurden (*Unique IP Count*)?

Die Tests wurden durchgeführt, um die Auswirkungen der folgenden Variablen zu verstehen:

- **Datenträgertyp**: Der Test wurde in einem 6-Knoten-Cluster aus D4-VMs durchgeführt, indem Standardspeicher (HDDs) verwendet wurde, und in einem 6-Knoten-Cluster mit DS4-VMs wiederholt, indem Premium-Speicher (SSDs) verwendet wurde.

- **Computergröße – Zentrales Hochskalieren**: Der Test wurde in einem 6-Knoten-Cluster mit DS3-VMs durchgeführt (als *kleiner* Cluster bezeichnet), in einem Cluster mit DS4-VMs wiederholt (*mittelgroßer* Cluster) und dann noch einmal in einem Cluster mit DS14-Computern wiederholt (*großer* Cluster). In der folgenden Tabelle sind die Hauptmerkmale der einzelnen VM-SKUs zusammengefasst:

Cluster |VM-SKU |Anzahl von Kernen |Anzahl von Datenträgern |RAM (GB)|
--------- |--------------- |----------------- |---------------------- |--------|
Klein |Standard DS3 |4 |8 |14 |
Mittel |Standard DS4 |8 |16 |28 |
Groß |Standard DS14 |16 |32 |112 |

- **Doc Values**: Zuerst wurden die Tests durchgeführt, indem die Indexeinstellung *doc\_values* auf *true* festgelegt war. Ausgewählte Tests wurden wiederholt, als *doc\_values* auf *false* festgelegt war.

## Leistungsergebnisse – Datenträgertyp

In der Tabelle unten sind die Reaktionszeiten für die Vorgänge zusammengefasst, die beim Durchführen des Tests in einem 6-Knoten-Cluster mit D4-VMs (mit HDDs) und in einem 6-Knoten-Cluster mit DS4-VMs (mit SSDs) anfallen. Die Konfiguration von Elasticsearch war in beiden Clustern identisch. Die Daten wurden auf jedem Knoten auf 16 Datenträger verteilt, und für jeden Knoten wurden 14 GB RAM für die JVM zugeordnet, auf der Elasticsearch ausgeführt wurde. Der restliche Arbeitsspeicher (ebenfalls 14 GB) wurde für die Verwendung durch das Betriebssystem reserviert. Jeder Test wurde 24 Stunden lang ausgeführt. Dieser Zeitraum wurde gewählt, damit die Auswirkungen des zunehmenden Datenvolumens deutlich wurden und um die Stabilisierung des Systems zu ermöglichen:

 Cluster |Vorgang/Abfrage |Durchschnittliche Reaktionszeit (ms)|
 -----------|---------------------------- |--------------------------|
 D4 |Erfassung |978 |
 |Count By Rating |103 |
 |Count Over Time |134 |
 |Hits By Country |199 |
 |Top 15 Organizations |137 |
 |Unique Count Organizations |139 |
 |Unique IP Count |510 |
 |Total Hits Counts |89 |
 DS4 |Erfassung |511 |
 |Count By Rating |187 |
 |Count Over Time |411 |
 |Hits By Country |402 |
 |Top 15 Organizations |307 |
 |Unique Count Organizations |320 |
 |Unique IP Count |841 |
 |Total Hits Counts |236 |

Auf den ersten Blick scheint es der Fall zu sein, dass Abfragen im DS4-Cluster weniger gut als im D4-Cluster durchgeführt wurden und die Reaktionszeiten zeitweise doppelt so hoch (oder noch höher) waren. Dies ist aber nur die halbe Wahrheit. In der nächsten Tabelle ist die Anzahl der Erfassungsvorgänge angegeben, die von jedem Cluster durchgeführt wurden (denken Sie daran, dass bei jedem Vorgang 1.000 Dokumente geladen wurden):

 Cluster | Erfassungsvorgänge
 ----------|---------------------
 D4 | 264\.769              
 DS4 | 503\.157              

Während des Tests konnten im DS4-Cluster fast doppelt so viele Daten wie im D4-Cluster geladen werden. Daher müssen Sie beim Analysieren der Reaktionszeiten für jeden Vorgang auch berücksichtigen, wie viele Dokumente von jeder Abfrage gescannt werden müssen und wie viele Dokumente zurückgegeben werden.

Dies sind dynamische Zahlen, da das Volumen an Dokumenten im Index ständig zunimmt. Sie können nicht einfach 503.137 durch 264.769 teilen (die Anzahl der von jedem Cluster durchgeführten Erfassungsvorgänge) und das Ergebnis dann mit der durchschnittlichen Reaktionszeit für jede Abfrage multiplizieren, die vom D4-Cluster durchgeführt wurde, um einen Vergleichswert zu erhalten. Hierbei wird nämlich die Menge der E/A-Vorgänge ignoriert, die vom Erfassungsvorgang gleichzeitig durchgeführt wird.

Stattdessen sollten Sie die physische Menge der Daten messen, die im Verlauf des Tests auf Datenträger geschrieben bzw. vom Datenträger gelesen wird. Mit dem JMeter-Testplan werden diese Informationen für jeden Knoten erfasst. Die zusammengefassten Ergebnisse lauten:

 Cluster |Durchschnittliche Anzahl von Byte, die bei jedem Vorgang geschrieben/gelesen werden|
  --------- |--------------------------------------------|
 D4 |13\.471.557 |
 DS4 |24\.643.470 |

Die Abbildungen zeigen, dass der DS4-Cluster eine E/A-Rate erzielen konnte, die ca. dem 1,8-fachen der Rate des D4-Clusters entspricht. Angesichts der Tatsache, dass alle anderen Ressourcen bis auf die Art der Datenträger gleich sind, muss der Grund für den Unterschied die Nutzung von SSDs anstelle von HDDs sein.

Zur Untermauerung dieser Schlussfolgerung wird mit den folgenden Graphen dargestellt, wie die E/A-Vorgänge im Laufe der Zeit von jedem Cluster durchgeführt wurden:

![](./media/guidance-elasticsearch/query-performance2.png)

Der Graph für den D4-Cluster weist signifikante Schwankungen auf, vor allem während der ersten Hälfte des Tests. Dies liegt wahrscheinlich an einer Drosselung mit dem Ziel, die E/A-Rate zu reduzieren. In den Anfangsphasen des Tests können die Abfragen schnell ausgeführt werden, da nur wenig Daten zu analysieren sind. Die Datenträger im D4-Cluster kommen daher vermutlich nahe an ihre IOPS-Kapazität heran, auch wenn bei jedem E/A-Vorgang ggf. nicht viele Daten zurückgegeben werden. Der DS4-Cluster kann eine höhere IOPS-Rate unterstützen und unterliegt nicht dem gleichen Drosselungsgrad. Die E/A-Raten sind regelmäßiger.

Zur Veranschaulichung dieser Theorie zeigt das nächste Graphenpaar, wie die CPU im Laufe der Zeit durch die E/A-Vorgänge der Datenträger blockiert wurde (die in den Graphen dargestellten Datenträger-Wartezeiten sind der Zeitanteil, den die CPU mit dem Warten auf E/A-Vorgänge verbracht hat):

![](./media/guidance-elasticsearch/query-performance3.png)

Es ist wichtig zu verstehen, dass es bei diesem Testszenario zwei Hauptgründe dafür gibt, warum die CPU durch E/A-Vorgänge blockiert wird:

- Das E/A-Subsystem liest Daten vom Datenträger bzw. schreibt Daten auf den Datenträger.

- Das E/A-Subsystem kann durch die Hostumgebung gedrosselt werden. Azure-Datenträger mit Unterstützung durch Standardspeicher haben einen maximalen Durchsatz von 500 IOPS, während Datenträger mit Premium-Speicher auf einen maximalen Durchsatz von 5.000 IOPS kommen.

Für den D4-Cluster korreliert der Zeitraum, der mit dem Warten auf E/A-Vorgänge verbracht wird, während der ersten Hälfte des Tests auf invertierte Weise eng mit dem Graphen, mit dem die E/A-Raten angezeigt werden. Zeiträume mit niedrigem E/A-Aufwand entsprechen Zeiträumen, in denen die CPU lange blockiert ist.

Dies deutet darauf hin, dass die E/A-Vorgänge gedrosselt werden. Wenn dem Cluster weitere Daten hinzugefügt werden, ändert sich die Situation. Während der zweiten Hälfte des Tests entsprechen E/A-Wartezeiten Spitzen beim E/A-Durchsatz. An diesem Punkt ist die CPU blockiert, während echte E/A-Vorgänge durchgeführt werden. Beim DS4-Cluster ist die Zeit, die mit dem Warten auf E/A-Vorgänge verbracht wird, wieder viel gleichmäßiger verteilt, und jede Spitze verfügt anstatt eines Tals über eine äquivalente Spitze bei der E/A-Leistung. Dies deutet darauf hin, dass nur wenig oder gar keine Drosselung auftritt.

Es muss noch ein weiterer Faktor berücksichtigt werden. Während des Tests wurden vom D4-Cluster 10.584 Erfassungsfehler und 21 Abfragefehler generiert. Beim Test im DS4-Cluster wurden keine Fehler produziert.

## Leistungsergebnisse – Zentrales Hochskalieren

In der Tabelle unten sind die Ergebnisse der Tests für den mittelgroßen (DS4) und großen Cluster (DS14) zusammengefasst. Für jeden virtuellen Computer wurden SSDs zum Speichern der Daten verwendet. Jeder Test wurde 24 Stunden lang ausgeführt:

| Cluster |Vorgang/Abfrage |Anzahl von Anforderungen |Durchschnittliche Reaktionszeit (ms)|
|  -------------- |---------------------------- |-------------------- |--------------------------|
| Mittelgroß (DS4) |Erfassung |503\.157 |511 |
| |Count By Rating |6\.958 |187 |
| |Count Over Time |6\.958 |411 |
| |Hits By Country |6\.958 |402 |
| |Top 15 Organizations |6\.958 |307 |
| |Unique Count Organizations |6\.956 |320 |
| |Unique IP Count |6\.955 |841 |
| |Total Hits Counts |6\.958 |236 |
| Groß (DS14) |Erfassung |502\.714 |511 |
| |Count By Rating |7\.041 |201 |
| |Count Over Time |7\.040 |298 |
| |Hits By Country |7\.039 |363 |
| |Top 15 Organizations |7\.038 |244 |
| |Unique Count Organizations |7\.037 |283 |
| |Unique IP Count |7\.037 |681 |
| |Total Hits Counts |7\.038 |200 |

<!-- 
DISCUSSION POINTS:

Similar volume of data ingested – same disk configuration for each cluster, and ingestion rate is 
constrained by I/O performance?

Average response time for queries decreases with SKU.

Show CPU graphs

Show memory utilization – more data cached, fewer GCs, etc.

-->

<!--
To isolate the effects of the ingestion operations and show how query performance varies as nodes scale up, a second set of tests was performed using the same nodes. The ingestion part of the test was omitted, and the index on each node was pre-populated with 100 million documents. An amended set of queries was performed; the time element limiting documents to those added in the last 15 minutes was removed as the data was now static. The tests ran for 90 minutes; there is less need to allow the system to stabilize due to the fixed amount of data. The following table summarizes the results obtained on each cluster:

> [AZURE.NOTE] The amended version of the test that omits the data ingestion process and that uses a set of indexes containing 100 million documents is referred to as the *query-only* test in the remainder of this document. You should not compare the performance of the queries in this test with that of the tests that perform ingestion and query operations because the queries have been modified and the volume of documents involved is different.

 |Cluster        |Operation/Query              |Number of Requests   Average Response Time (ms)
 | --------------| ----------------------------| -------------------- ----------------------------
 | Small (DS3)   | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Medium (DS4)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      
 | Large (DS14)  | Count By Rating             |                      
 |               | Count Over Time             |                      
 |               | Hits By Country             |                      
 |               | Top 15 Organizations        |                      
 |               | Unique Count Organizations  |                      
 |               | Unique IP Count             |                      
 |               | Total Hits Counts           |                      

### Performance Results – Scaling Out
-->


<!--
To show the system scales out with the number of nodes, tests were run using DS14 clusters comprising 1, 3, and 6 nodes. This time, only the query-only test was performed, using 100 million documents and running for 90 minutes:

> [AZURE.NOTE] For detailed information on how scaling out can affect the behavior of data ingestion operations, see the document [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md).

|  Cluster   |Operation/Query              |Number of Requests   |Average Response Time (ms)
|  --------- |---------------------------- |-------------------- |----------------------------
|  1 Node    |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  3 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
|  6 Nodes   |Count By Rating              |                     |
|            |Count Over Time              |                     |
|            |Hits By Country              |                     |
|            |Top 15 Organizations         |                     |
|            |Unique Count Organizations   |                     |
|            |Unique IP Count              |                     |
|            |Total Hits Counts            |                     |
-->

<!--
### Performance Results – Number of Replicas

The ingestion and query tests were run against an index with a single replica. The tests were repeated on the 6-node DS4 and DS14 clusters using an index configured with two replicas. All tests ran for 24 hours. The table below shows the comparative results for one and two replicas:

|  Cluster|  Operation/Query            | response time 1 Replica (ms)| response time 2 Replicas (ms) | % Difference in Response Time
|  -------| ----------------------------| ----------------------------|----------- -------------------|--------------------- --------
|  DS4    |   Ingestion                 |   511                       |               655             |                          +28%
|         |   Count By Rating           |   187                       |               168             |                          -10%
|         |   Count Over Time           |   411                       |               309             |                          -25%
|         |   Hits By Country           |   402                       |               562             |                          +40%
|         |   Top 15 Organizations      |   307                       |               366             |                          +19%
|         |   Unique Count Organizations|   320                       |               378             |                          +18%
|         |   Unique IP Count           |   841                       |               987             |                          +17%
|         |   Total Hits Counts         |   236                       |               236             |                          +0%
|  DS14   |   Ingestion                 |   511                       |               618             |                          +21%
|         |   Count By Rating           |   201                       |               275             |                          +37%
|         |   Count Over Time           |   298                       |               466             |                          +56%
|         |   Hits By Country           |   363                       |               529             |                          +46%
|         |   Top 15 Organizations      |   244                       |               407             |                          +67%
|         |   Unique Count Organizations|   283                       |               403             |                          +42%
|         |   Unique IP Count           |   681                       |               823             |                          +21%
|         |   Total Hits Counts         |   200                       |               221             |                          +11%


NEED ## OF DOCUMENTS RETURNED TO JUSTIFY THIS DATA, OTHERWISE PERF FOR 2 REPLICAS LOOKS OFF!

PRESENT QUERY-ONLY TEST RESULTS TO SHOW BETTER RESULTS
-->

## Leistungsergebnisse – Doc Values

Die Erfassungs- und Abfragetests wurden mit aktivierten Doc Values durchgeführt, sodass Daten, die zum Sortieren von Feldern verwendet wurden, von Elasticsearch auf Datenträger gespeichert wurden. Die Tests wurden dann mit deaktivierten Doc Values wiederholt, sodass Felddaten von Elasticsearch dynamisch erstellt und im Arbeitsspeicher zwischengespeichert wurden. Alle Tests wurden 24 Stunden lang ausgeführt.
 
 In der Tabelle unten werden die Reaktionszeiten für Tests verglichen, die für Cluster mit sechs Knoten und mit D4-, DS4- und DS14-VMs durchgeführt wurden.

| Cluster |Vorgang/Abfrage |Doc Values aktiviert (ms) | Doc Values deaktiviert (ms) | Differenz in % |
|  --------- |---------------------------| -----------------------|--------------------------  |--------------------|
| D4 |Erfassung | 978 | 835 | -15 % |
| |Count By Rating | 103 | 132 | +28 % |
| |Count Over Time | 134 | 189 | +41 % |
| |Hits By Country | 199 | 259 | +30 % |
| |Top 15 Organizations | 137 | 184 | +34 % |
| |Unique Count Organizations | 139 | 197 | +42 % |
| |Unique IP Count | 510 | 604 | +18 % |
| |Total Hits Counts | 89 | 134 | +51 % |
| DS4 |Erfassung | 511 | 581 | +14 % |
| |Count By Rating | 187 | 190 | +2 % |
| |Count Over Time | 411 | 409 | -0,5 % |
| |Hits By Country | 402 | 414 | +3 % |
| |Top 15 Organizations | 307 | 284 | -7 % |
| |Unique Count Organizations | 320 | 313 | -2 % |
| |Unique IP Count | 841 | 955 | +14 % |
| |Total Hits Counts | 236 | 281 | +19 % |
| DS14 |Erfassung | 511 | 571 | +12 % |
| |Count By Rating | 201 | 232 | +15 % |
| |Count Over Time | 298 | 341 | +14 % |
| |Hits By Country | 363 | 457 | +26 % |
| |Top 15 Organizations | 244 | 338 | +39 % |
| |Unique Count Organizations | 283 | 350 | +24 % |
| |Unique IP Count | 681 | 909 | +33 % |
| |Total Hits Counts | 200 | 245 | +23 % |


<!--
The next table compares the number of ingestion operations performed by the tests:

  Cluster   Ingestion Operations – Doc Values Enabled   ## Ingestion Operations – Doc Values Disabled   % Difference in ## Ingestion Operations
  --------- ---------------------------------------------- ----------------------------------------------- -----------------------------------------
  D4        264769                                         408690                                          +54%
  DS4       503137                                         578237                                          +15%
  DS14      502714                                         586472                                          +17%

The improved ingestion rates occur with doc values disabled as less data is being written to disk as documents are inserted. The improved performance is especially noticeable with the D4 VM using HDDs to store data. In this case, the response time for ingestion operations also decreased by 15% (see the first table in this section). This could be due to the reduced pressure on the HDDs which were likely running close to their IOPS limits in the test with doc values enabled; see the [Disk Type](#performance-results-disk-type) test for more information. The following graph compares the I/O performance of the D4 VMs with doc values enabled (values held on disk) and doc values disabled (values held in memory):

![](./media/guidance-elasticsearch/query-performance4.png)

In contrast, the ingestion figures for the VMs using SSDs show a small increase in the number of documents but also an increase in the response time of the ingestion operations. With one or two small exceptions, the query response times were also worse. The SSDs are less likely to be running close to their IOPS limits with doc values enabled, so changes in performance are more likely due to increased processing activity and the overhead of managing the JVM heap. This is evident by comparing the CPU utilization with doc values enabled and disabled. The next graph highlights this data for the DS4 cluster, where most of the CPU utilization moves from the 30%-40% band with doc values enabled, to the 40%-50% band with doc values disabled (the DS14 cluster showed a similar trend):

![](./media/guidance-elasticsearch/query-performance5.png)

To isolate the effects that doc values on query performance from data ingestion, a pair of query-only tests were performed for each cluster as follows:

In the first test, the Elasticsearch index was created with doc values enabled, the index was pre-populated with 100 million documents, and then modified set of queries used by the replica tests were performed repeatedly for 90 minutes while performance statistics were gathered.

In the second test, the Elasticsearch index was created with doc values disabled, populated, and then subjected to the same query load for the same period as the first test.
-->

<!--
## Appendix: The Query and Aggregation Performance Test

This appendix describes the performance test performed against the Elasticsearch cluster. The tests were run by using JMeter running on a separate set of VMs. Details the configuration of the test environment are described in [Creating a Performance Testing Environment for Elasticsearch on Azure][]. To perform your own testing, you can create your own JMeter test plan manually following the guidance in this appendix, or you can use the automated test scripts available separately. See [Running the Automated Elasticsearch Performance Tests][] for further information.

The data query workload performed the set of queries described below while performing a large-scale upload of documents at the same time (the data was uploaded by using a JUnit test, following the same approach for the data ingestion tests described in the document Maximizing Data Ingestion Performance with Elasticsearch on Azure.) The purpose of this workload was to simulate a production environment where new data is constantly being added while searches are performed. The queries were structured to retrieve only the most recent data from documents added in the last 15 minutes.

Each document was stored in a single index named *sample*, and had the type *ctip*. You can use the following HTTP request to create the index. The *number\_of\_replicas* and *number\_of\_shards* settings varied from the values shown below in many of the tests. Additionally, for the tests that used fielddata rather than doc values, each property was annotated with the attribute *"doc\_values" : false*.

**Important**. The index was dropped and recreated prior to each test run.

```http
PUT /sample
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "ctip": {
        "mappings": {
            "event": {
                "_all": {
                    "enabled": false
                },
                "_timestamp": {
                    "enabled": true,
                    "store": true,
                    "format": "date_time"
                },
                "properties": {
                    "Organization": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField1": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField2": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField3": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField4": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "CustomField5": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "DateTimeReceivedUtc": {
                        "type": "date",
                        "format": "dateOptionalTime"
                    },
                    "Host": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpMethod": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpReferrer": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpRequest": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpUserAgent": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "HttpVersion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "OrganizationName": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIp": {
                        "type": "ip"
                    },
                    "SourceIpAreaCode": {
                        "type": "long"
                    },
                    "SourceIpAsnNr": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpBase10": {
                        "type": "long"
                    },
                    "SourceIpCity": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpCountryCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpLatitude": {
                        "type": "double"
                    },
                    "SourceIpLongitude": {
                        "type": "double"
                    },
                    "SourceIpMetroCode": {
                        "type": "long"
                    },
                    "SourceIpPostalCode": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceIpRegion": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourceLatLong": {
                        "type": "geo_point",
                        "doc_values": true,
                        "lat_lon": true,
                        "geohash": true
                    },
                    "SourcePort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "SourcedFrom": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "TargetIp": {
                        "type": "ip"
                    },
                    "TargetPort": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "Rating": {
                        "type": "string",
                        "index": "not_analyzed"
                    },
                    "UseHumanReadableDateTimes": {
                        "type": "boolean"
                    }
                }
            }
        }
    }
}

```

The following queries were performed by the test:

- How many documents with each *Rating* value have been entered in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many documents have been added in each 5 minute interval during the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "bool": {
      "must": [
        {
          "range": {
            "DateTimeReceivedUtc": {
              "gte": "now-15m",
              "lte": "now"
            }
          }
        }
      ],
      "must_not": [],
      "should": []
    }
  },
  "from": 0,
  "size": 0,
  "sort": [],
  "aggs": {
    "2": {
      "date_histogram": {
        "field": "DateTimeReceivedUtc",
        "interval": "5m",
        "time_zone": "America/Los_Angeles",
        "min_doc_count": 1,
        "extended_bounds": {
          "min": "now-15m",
          "max": "now"
        }
      }
    }
  }
}

```

- How many documents of each *Rating* value have been added for each country in the last 15 minutes?

```http
GET /sample/ctip/_search 
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Rating",
        "size": 5,
        "order": {
          "_count": "desc"
        }
      },
      "aggs": {
        "3": {
          "terms": {
            "field": "SourceIpCountryCode",
            "size": 15,
            "order": {
              "_count": "desc"
            }
          }
        }
      }
    }
  }
}

```

- Which 15 organizations occur most frequently in documents added in the last 15 minutes?

``` http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "terms": {
        "field": "Organization",
        "size": 15,
        "order": {
          "_count": "desc"
        }
      }
    }
  }
}

```

- How many different organizations occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "Organization"
      }
    }
  }
}

```

- How many documents have been added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "analyze_wildcard": true,
                  "query": "*"
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {}
}

```

- How many different *SourceIp* values occur in documents added in the last 15 minutes?

```http
GET /sample/ctip/_search
{
  "query": {
    "filtered": {
      "query": {
        "query_string": {
          "query": "*",
          "analyze_wildcard": true
        }
      },
      "filter": {
        "bool": {
          "must": [
            {
              "query": {
                "query_string": {
                  "query": "*",
                  "analyze_wildcard": true
                }
              }
            },
            {
              "range": {
                "DateTimeReceivedUtc": {
                  "gte": "now-15m",
                  "lte": "now"
                }
              }
            }
          ],
          "must_not": []
        }
      }
    }
  },
  "size": 0,
  "aggs": {
    "2": {
      "cardinality": {
        "field": "SourceIp"
      }
    }
  }
} 

```

[Running the Automated Elasticsearch Performance Tests]: guidance-elasticsearch-running-automated-performance-tests.md
[Creating a Performance Testing Environment for Elasticsearch on Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
-->

<!---HONumber=AcomDC_0224_2016-->