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
   ms.date="02/29/2016"
   ms.author="masimms"/>
   
# Optimieren der Datenaggregation und Abfrageleistung mit Elasticsearch in Azure


Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

Ein Hauptgrund für die Nutzung von Elasticsearch ist die Unterstützung des Durchsuchens von Daten. Benutzer sollten die gewünschten Informationen immer schnell finden können. Außerdem muss das System Benutzer in die Lage versetzen, Fragen zu Daten zu stellen, nach Korrelationen zu suchen und zu Schlussfolgerungen zu kommen, die als Grundlage für Geschäftsentscheidungen dienen. Diese Art der Verarbeitung unterscheidet Daten von reinen Informationen.

In diesem Dokument sind die Optionen zusammengefasst, deren Verwendung Sie erwägen können, wenn es um den besten Weg zur Optimierung Ihres Systems in Bezug auf die Abfrage- und Suchleistung geht.

Alle Leistungsempfehlungen hängen größtenteils von den Szenarien ab, die für Ihre Situation gelten, sowie vom Volumen der Daten, die Sie indizieren, und der Rate, mit der Anwendungen und Benutzer Ihre Daten abfragen. Sie sollten die Ergebnisse aller Änderungen der Konfiguration oder Indizierungsstruktur sorgfältig testen, indem Sie Ihre eigenen Daten und Workloads verwenden, um die Vorteile Ihrer jeweiligen Szenarien zu bewerten. Aus diesem Grund werden in diesem Dokument auch eine Reihe von Benchmark-Tests beschrieben, die für ein bestimmtes Szenario durchgeführt wurden. Das Szenario wurde jeweils mit unterschiedlichen Konfigurationen implementiert. Sie können den Ansatz anpassen, den Sie zum Bewerten der Leistung Ihrer eigenen Systeme wählen. Die Details dieser Tests sind im [Anhang](#appendix-the-query-and-aggregation-performance-test) beschrieben.

## Aspekte der Index- und Abfrageleistung

In diesem Abschnitt werden einige häufige Faktoren beschrieben, die wichtig beim Entwerfen von Indizes sind, mit denen schnelle Abfrage- und Suchvorgänge unterstützt werden sollen.

### Speichern von mehreren Typen in einem Index

Ein Elasticsearch-Index kann mehrere Typen enthalten. Es ist unter Umständen besser, diesen Ansatz zu vermeiden und einen separaten Index für jeden Typ zu erstellen. Beachten Sie die folgenden Punkte:

- Für unterschiedliche Typen werden ggf. unterschiedliche Analysemodule angegeben. Es ist nicht immer eindeutig, welches Analysemodul von Elasticsearch verwendet werden sollte, wenn eine Abfrage auf Indexebene anstatt auf Typebene durchgeführt wird. Ausführliche Informationen hierzu finden Sie unter [Avoiding Type Gotchas](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping.html#_avoiding_type_gotchas) (Vermeiden von Typkomplikationen).

- Shards für Indizes, die mehrere Typen enthalten, sind meist größer als Shards für Indizes, die nur einen Typ enthalten. Je größer ein Shard ist, desto höher ist der Aufwand für Elasticsearch beim Filtern von Daten, wenn Abfragen durchgeführt werden.

- Falls zwischen Datenvolumen in Bezug auf die Typen ein signifikanter Konflikt besteht, können die Informationen für einen Typ ggf. in geringer Menge auf viele Shards verteilt werden. Dies führt zu einer Verringerung der Effizienz von Suchen, bei denen diese Daten abgerufen werden.

    ![](./media/guidance-elasticsearch/query-performance1.png)

    ***Abbildung 1: Auswirkungen der Freigabe eines Index zwischen Typen***

    Dieses Szenario ist in Abbildung 1 dargestellt. Im oberen Teil des Diagramms wird für Dokumente vom Typ A und Typ B derselbe Index verwendet. Es sind noch viel mehr Dokumente vom Typ A und Typ B vorhanden. Bei Suchen nach Typ A werden alle vier Shards abgefragt. Der untere Teil des Diagramms zeigt die Auswirkung, wenn für jeden Typ separate Indizes erstellt werden. In diesem Fall ist bei Suchen nach Typ A nur der Zugriff auf zwei Shards erforderlich.

- Kleine Shards können gleichmäßiger als große Shards verteilt werden, sodass es für Elasticsearch einfacher ist, die Last auf die Knoten zu verteilen.

- Verschiedene Typen weisen unter Umständen unterschiedliche Aufbewahrungsdauern auf. Es kann schwierig sein, alte Daten zu archivieren, die auf Shards zusammen mit neuen Daten vorhanden sind.


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

<!-- -->

Eine effektive Neuindizierung umfasst das Erstellen eines neuen Index aus den Daten in einem alten Index und das anschließende Entfernen des alten Index. Wenn ein Index groß ist, kann dieser Prozess lange dauern, und Sie müssen unter Umständen sicherstellen, dass die Daten während dieser Zeit durchsuchbar bleiben. Aus diesem Grund sollten Sie einen [Alias für jeden Index](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) erstellen, und bei Abfragen sollten Daten über diese Aliase abgerufen werden. Behalten Sie den Verweis des Alias auf den alten Index beim Neuindizieren bei, und ändern Sie den Verweis dann in den neuen Index, nachdem die Neuindizierung abgeschlossen ist. Dieser Ansatz ist auch für den Zugriff auf zeitbasierte Daten nützlich, bei dem jeden Tag ein neuer Index erstellt wird. Verwenden Sie zum Zugreifen auf die aktuellen Daten einen Alias, für den ein Rollover zum neuen Index durchgeführt wird, nachdem dieser erstellt wurde.

### Verwalten von Zuordnungen

Elasticsearch verwendet Zuordnungen, um zu bestimmen, wie die Daten interpretiert werden sollen, die in den einzelnen Feldern in einem Dokument anfallen. Jeder Typ verfügt über eine eigene Zuordnung, womit praktisch ein Schema für diesen Typ definiert wird. Elasticsearch verwendet diese Informationen zum Generieren von invertierten Indizes für jedes Feld in den Dokumenten eines Typs. In allen Dokument verfügt jedes Feld über einen Datentyp (z. B. *string*, *date* oder *long*) und einen Wert. Sie können die Zuordnungen für einen Index angeben, wenn der Index am Anfang erstellt wird, oder sie können von Elasticsearch abgeleitet werden, wenn einem Typ neue Dokumente hinzugefügt werden. Beachten Sie hierbei aber die folgenden Punkte:

- Dynamisch generierte Zuordnungen können Fehler verursachen. Dies hängt davon ab, wie Felder interpretiert werden, wenn Dokumente einem Index hinzugefügt werden. Dokument 1 kann beispielsweise ein Feld A aufweisen, das eine Zahl enthält und Elasticsearch zum Hinzufügen einer Zuordnung mit der Angabe bewegt, dass dieses Feld vom Typ *long* ist. Wenn ein nachfolgendes Dokument hinzugefügt wird, in dem Feld A nicht numerische Daten enthält, tritt ein Fehler auf. In diesem Fall hätte Feld A vermutlich als Zeichenfolge interpretiert werden müssen, als das erste Dokument hinzugefügt wurde. Das Angeben dieser Zuordnung beim Erstellen des Index kann dazu beitragen, Probleme dieser Art zu verhindern.

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

    However, this search will fail to return the expected results due to the way in which the string ABC-DEF is tokenized when it is indexed; it will be effectively split into two tokens, ABC and DEF, by the hyphen. This feature is designed to support full text searching, but if you want the string to be interpreted as a single atomic item you should disable tokenization when the document is added to the index. You can use a mapping such as this:

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
> [AZURE.NOTE] Doc Values sind ab Elasticsearch Version 2.0.0 aufwärts automatisch aktiviert.

Die genauen Auswirkungen der Nutzung von Doc Values richten sich in der Regel stark nach Ihren eigenen Daten und Abfrageszenarien. Seien Sie also darauf vorbereitet, Leistungstests durchzuführen, um die Nützlichkeit zu ermitteln. Beachten Sie außerdem, dass Doc Values zusammen mit analysierten Zeichenfolgenfeldern nicht funktionieren. Weitere Informationen finden Sie unter [Doc Values](https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html#doc-values).

### Verwenden von Replikaten zum Reduzieren von Abfragekonflikten

Eine häufige verwendete Strategie zum Verbessern der Leistung von Abfragen ist die Erstellung von vielen Replikaten für jeden Index. Vorgänge zum Abrufen von Daten können durchgeführt werden, indem Daten von einem Replikat beschafft werden. Diese Strategie kann aber erhebliche Auswirkungen auf die Leistung von Vorgängen für die Datenerfassung haben und muss daher mit Bedacht in Szenarios angewendet werden, in denen es um gemischte Workloads geht. Außerdem ist diese Strategie nur von Vorteil, wenn die Replikate über Knoten verteilt sind und nicht mit primären Shards um Ressourcen konkurrieren, die Teil desselben Index sind. Beachten Sie, dass es möglich ist, die Anzahl von Replikaten für einen Index dynamisch zu erhöhen oder zu verringern.

### Verwenden des Caches für Shardanforderungen

Elasticsearch kann die lokalen Daten, die von Abfragen angefordert werden, auf jedem Shard im Arbeitsspeicher zwischenspeichern. So können Suchen, bei denen dieselben Daten abgerufen werden, schneller ausgeführt werden. Daten können aus dem Arbeitsspeicher abgerufen werden, anstatt aus dem Datenträgerspeicher. Das Zwischenspeichern von Daten auf diese Weise kann daher die Leistung einiger Suchvorgänge verbessern. Der Nachteil ist, dass der verfügbare Speicher reduziert wird, der für andere gleichzeitig ausgeführte Aufgaben verfügbar ist. Es besteht auch das Risiko, dass Daten, die aus dem Cache bereitgestellt werden, veraltet sind. Die Daten im Cache werden nur ungültig gemacht, wenn das Shard aktualisiert wird und sich die Daten ändern. Die Häufigkeit der Aktualisierungen richtet sich nach dem Wert der Einstellung *refresh\_interval* des Index.

Die Anforderungszwischenspeicherung für einen Index ist standardmäßig deaktiviert, aber Sie können ihn wie folgt aktivieren:

```http
PUT /myindex/_settings
{
  "index.requests.cache.enable": true
}
```

Am besten eignet sich der Cache für Shardanforderungen für Informationen, die relativ statisch bleiben, z. B. Verlaufs- oder Protokollierungsdaten.

### Verwenden von Clientknoten

Alle Abfragen werden von dem Knoten verarbeitet, der die Anforderung als Erstes empfängt. Dieser Knoten sendet weitere Anforderungen an alle anderen Knoten, die Shards für die abgefragten Indizes enthalten, und sammelt dann die Ergebnisse, um die Antwort zurückzugeben. Wenn eine Abfrage das Aggregieren von Daten oder komplexe Berechnungen umfasst, ist der erste Knoten für die Durchführung der richtigen Verarbeitung verantwortlich. Wenn Ihr System eine relativ kleine Anzahl von komplexen Abfragen unterstützen muss, können Sie erwägen, einen Pool mit Clientknoten zu erstellen, um die Last für die Datenknoten zu verteilen. Wenn Ihr System dagegen eine große Anzahl von einfachen Abfragen verarbeiten muss, sollten Sie diese Anforderungen direkt an die Datenknoten senden und einen Load Balancer verwenden, um die Anforderungen gleichmäßig zu verteilen.

### Optimieren von Abfragen

Unter den folgenden Punkten sind Tipps zur Steigerung der Leistung von Elasticsearch-Abfragen zusammengefasst:

- Vermeiden Sie nach Möglichkeit Abfragen, die Platzhalter enthalten.

- Wenn für ein Feld die Volltextsuche und ein genauer Abgleich verwendet werden, sollten Sie erwägen, die Daten für das Feld in analysierter und nicht analysierter Form zu speichern. Führen Sie Volltextsuchen für das analysierte Feld und den genauen Abgleich für das nicht analysierte Feld durch.

- Geben Sie nur die erforderlichen Daten zurück. Falls Sie über umfangreiche Dokumente verfügen, eine Anwendung aber nur die Informationen einer Teilmenge der Felder benötigt, sollten Sie bei Abfragen diese Teilmenge zurückgeben, nicht die gesamten Dokumente. Mit dieser Strategie können Sie die Anforderungen an die Netzwerkbandbreite für den Cluster reduzieren.

- Verwenden Sie beim Suchen nach Daten anstelle von Abfragen nach Möglichkeit immer Filter. Mit einem Filter wird lediglich bestimmt, ob ein Dokument ein bestimmtes Kriterium erfüllt, während bei einer Abfrage auch berechnet wird, wie groß die Übereinstimmung für ein Dokument ist (Bewertung). Intern werden die von einem Filter generierten Werte als Bitmap gespeichert, und für jedes Dokument wird angegeben, ob es sich eine Übereinstimmung ergibt oder nicht. Die Zwischenspeicherung durch Elasticsearch ist möglich. Wenn dasselbe Filterkriterium noch einmal vorkommt, kann die Bitmap aus dem Cache abgerufen und verwendet werden, um die übereinstimmenden Dokumente schnell bereitzustellen. Weitere Informationen finden Sie unter [Internal Filter Operation](https://www.elastic.co/guide/en/elasticsearch/guide/current/_finding_exact_values.html#_internal_filter_operation) (Interner Filtervorgang).

- Verwenden Sie Filter vom Typ *bool*, um statische Vergleiche durchzuführen. Setzen Sie Filter vom Typ *and*, *or* und *not* nur für dynamisch berechnete Filter ein, z. B. für Fälle, in denen die Skripterstellung oder Filter vom Typ *geo-** genutzt werden.

- Wenn in einer Abfrage Filter vom Typ *bool* mit den Filtern *and*, *or* oder *not* mit dem Filter *geo-** kombiniert werden, sollten Sie die Filter *and*/*or*/*not geo-** als Letztes anordnen, damit sie für das kleinstmögliche Dataset eingesetzt werden.

    Verwenden Sie analog dazu einen *post\_filter*, um umfangreiche Filtervorgänge auszuführen. Diese Filter werden zuletzt ausgeführt.

- Verwenden Sie Aggregationen anstelle von Facetten (Facets). Vermeiden Sie die Berechnung von Aggregaten, die analysiert werden oder über viele mögliche Werte verfügen.

    > **Hinweis**: In Version 2.0.0 von Elasticsearch wurden Facetten entfernt.

- Ziehen Sie die Aggregation *cardinality* der Aggregation *value\_count* vor, es sei denn, für Ihre Anwendung ist eine exakte Anzahl übereinstimmender Elemente erforderlich. Eine exakte Anzahl kann schnell veraltet sein, und für viele Anwendungen ist nur eine gute Schätzung erforderlich.

- Vermeiden Sie die Skripterstellung. Skripts in Abfragen und Filtern können aufwändig sein, und die Ergebnisse werden nicht zwischengespeichert. Bei Skripts mit langer Ausführungsdauer kann es vorkommen, dass Suchthreads unendlich lange belegt werden und nachfolgende Anforderungen in die Warteschlange eingereiht werden. Wenn die Warteschlange voll ist, werden weitere Anforderungen abgelehnt.

## Testen und Analysieren der Leistung von Aggregationen und Suchen

In diesem Abschnitt werden die Ergebnisse einer Reihe von Tests beschrieben, die für verschiedene Cluster- und Indexkonfigurationen durchgeführt wurden. Es wurden wie folgt zwei Arten von Tests durchgeführt:

- ***Erfassungs- und Abfragetest:*** Bei diesem Test wurde mit einem leeren Index begonnen, der im Verlauf des Tests aufgefüllt wurde, indem Masseneinfügungen durchgeführt wurden (bei jedem Vorgang wurden 1.000 Dokumente hinzugefügt). Gleichzeitig wurden mehrere Abfragen in Intervallen von fünf Sekunden wiederholt. Die Abfragen waren so konzipiert, dass nach Dokumenten gesucht wurde, die während der letzten 15 Minuten hinzugefügt wurden, und dass Aggregationen generiert wurden. Dieser Test wurde in der Regel 24 Stunden lang durchgeführt, um die Auswirkungen einer anspruchsvollen Workload mit umfangreicher Datenerfassung und Abfragen nahezu in Echtzeit zu reproduzieren.

- ***Reiner Abfragetest:*** Dieser Test ähnelt dem *Erfassungs- und Abfragetest*, aber mit der Ausnahme, dass der Erfassungsteil weggelassen wird. Außerdem wurde jeder Knoten vorab mit 100 Millionen Dokumenten gefüllt. Es wird eine angepasste Gruppe von Abfragen durchgeführt. Das Zeitelement, mit dem die Dokumente auf die Dokumente beschränkt werden, die innerhalb der letzten 15 Minuten hinzugefügt werden, wurde entfernt. Es handelt sich jetzt um statische Daten. Die Tests wurden 90 Minuten lang durchgeführt. Aufgrund der feststehenden Datenmenge wird weniger Zeit benötigt, um ein Leistungsmuster zu erhalten.

---

Jedes Dokument im Index hatte das gleiche Schema. In dieser Tabelle sind die Felder des Schemas zusammengefasst:

Name | Typ | Hinweise |
  ----------------------------- | ------------ | -------------------------------------------------------- |
 Organisation | String | Für den Test werden 200 eindeutige Organisationen generiert. |
 CustomField1 - CustomField5 |String |Dies sind fünf Zeichenfolgenfelder, die auf die leere Zeichenfolge festgelegt werden.|
 DateTimeReceivedUtc |Timestamp |Das Datum und die Uhrzeit des Zeitpunkts, zu dem das Dokument hinzugefügt wurde.|
 Host |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 HttpMethod |String |Dieses Feld ist auf einen der folgenden Werte festgelegt: „POST“, „GET“, „PUT“.|
 HttpReferrer |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 HttpRequest |String |Dieses Feld wird mit zufälligem Text aufgefüllt, der zwischen 10 und 200 Zeichen lang ist.|
 HttpUserAgent |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 HttpVersion |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 OrganizationName |String |Dieses Feld ist auf den gleichen Wert wie das Feld „Organization“ festgelegt.|
 SourceIp |IP |Dieses Feld enthält eine IP-Adresse, die den „Ursprung“ der Daten angibt. |
 SourceIpAreaCode |Long |Dieses Feld ist auf 0 festgelegt.|
 SourceIpAsnNr |String |Dieses Feld ist auf „AS#####“ festgelegt.|
 SourceIpBase10 |Long |Dieses Feld ist auf 500 festgelegt.|
 SourceIpCountryCode |String |Dieses Feld enthält einen Ländercode mit zwei Zeichen. |
 SourceIpCity |String |Dieses Feld enthält eine Zeichenfolge, mit der ein Ort in einem Land angegeben wird. |
 SourceIpLatitude |Double |Dieses Feld enthält einen zufälligen Wert.|
 SourceIpLongitude |Double |Dieses Feld enthält einen zufälligen Wert.|
 SourceIpMetroCode |Long |Dieses Feld ist auf 0 festgelegt.|
 SourceIpPostalCode |String |Dieses Feld ist auf die leere Zeichenfolge festgelegt.|
 SourceLatLong |Geo point |Dieses Feld ist auf einen zufälligen geografischen Punkt festgelegt.|
 SourcePort |String |Dieses Feld wird durch die Zeichenfolgendarstellung einer zufälligen Zahl aufgefüllt.|
 TargetIp |IP |Wird mit einer zufälligen IP-Adresse im Bereich von 0.0.100.100 bis 255.9.100.100 aufgefüllt.|
 SourcedFrom |String |Dieses Feld ist auf die Zeichenfolge „MonitoringCollector“ festgelegt.|
 TargetPort |String |Dieses Feld wird durch die Zeichenfolgendarstellung einer zufälligen Zahl aufgefüllt.|
 Rating |String |Dieses Feld wird mit einem von zwanzig verschiedenen Zeichenfolgenwerten aufgefüllt, die nach dem Zufallsprinzip ausgewählt werden.|
 UseHumanReadableDateTimes |Boolean |Dieses Feld ist auf „false“ festgelegt.|
 
Die folgenden Abfragen wurden für jeden Durchlauf des Tests als Batch durchgeführt. Die Namen in Kursivschrift werden verwendet, um im restlichen Teil dieses Dokuments auf diese Abfragen zu verweisen. Beachten Sie, dass das Zeitkriterium (innerhalb der letzten 15 Dokumente hinzugefügte Dokumente) für die *reinen Abfragetests* entfernt wurde:

- Wie viele Dokumente mit jedem *Rating*-Wert wurden innerhalb der letzten 15 Minuten eingegeben (*Count By Rating*)?

- Wie viele Dokumente wurden innerhalb der letzten 15 Minuten in jedem 5-Minuten-Intervall hinzugefügt (*Count Over Time*)?

- Wie viele Dokumente mit jedem *Rating*-Wert wurden für jedes Land innerhalb der letzten 15 Minuten hinzugefügt (*Hits By Country*)?

- Welche 15 Organisationen kommen in Dokumenten, die innerhalb der letzten 15 Minuten hinzugefügt wurden, am häufigsten vor (*Top 15 Organizations*)?

- Wie viele unterschiedliche Organisationen kommen in Dokumenten vor, die innerhalb der letzten 15 Minuten hinzugefügt wurden (*Unique Count Organizations*)?

- Wie viele Dokumente wurden innerhalb der letzten 15 Minuten hinzugefügt (*Total Hits Count*)?

- Wie viele unterschiedliche *SourceIp*-Werte kommen in Dokumenten vor, die innerhalb der letzten 15 Minuten hinzugefügt wurden (*Unique IP Count*)?


Die Definition des Index und die Details der Abfragen sind im [Anhang](#appendix-the-query-and-aggregation-performance-test) beschrieben.

Die Tests wurden konzipiert, um die Auswirkungen der folgenden Variablen zu verstehen:

- **Datenträgertyp:** Die Tests wurden in einem 6-Knoten-Cluster mit D4-VMs durchgeführt, indem Standardspeicher (HDDs) verwendet wurde, und in einem 6-Knoten-Cluster mit DS4-VMs wiederholt, indem Premium-Speicher (SSDs) verwendet wurde.

- **Computergröße – Zentrales Hochskalieren:** Die Tests wurden in einem 6-Knoten-Cluster mit DS3-VMs durchgeführt (als *kleiner* Cluster bezeichnet), in einem Cluster mit DS4-VMs wiederholt (*mittelgroßer* Cluster) und dann noch einmal in einem Cluster mit DS14-Computern wiederholt (*großer* Cluster). In der folgenden Tabelle sind die Hauptmerkmale der einzelnen VM-SKUs zusammengefasst:

 Cluster | VM-SKU | Anzahl von Kernen | Anzahl von Datenträgern | RAM (GB) |
---------|---------------|-----------------|----------------------|----------|
 Klein | Standard DS3 | 4 | 8 | 14 |
 Mittel | Standard DS4 | 8 | 16 | 28 |
 Groß | Standard DS14 | 16 | 32 | 112 |

- **Clustergröße – Horizontales Hochskalieren:** Die Tests wurden auf Clustern mit DS14-VMs und einem, drei und sechs Knoten durchgeführt.

- **Anzahl der Indexreplikate:** Die Tests wurden mit Indizes durchgeführt, für die ein und zwei Replikate konfiguriert waren.

- **Doc Values:** Zuerst wurden die Tests durchgeführt, indem die Indexeinstellung *doc\_values* auf *true* (Standardwert) festgelegt war. Ausgewählte Tests wurden wiederholt, als *doc\_values* auf *false* festgelegt war.

- **Zwischenspeicherung:** Die Tests wurden durchgeführt, indem der Cache für Shardanforderungen für den Index aktiviert war.

- **Anzahl von Shards:** Die Tests wurden mit unterschiedlichen Anzahlen von Shards wiederholt, um zu ermitteln, ob Abfragen effizienter für Indizes ausgeführt werden, die weniger große Shards oder mehr kleinere Shards enthalten.


## Leistungsergebnisse – Datenträgertyp

Die Datenträgerleistung wurde ausgewertet, indem der *Erfassungs- und Abfragetest* im 6-Knoten-Cluster mit D4-VMs (HDDs) und im 6-Knoten-Cluster mit DS4-VMs (SSDs) durchgeführt wurde. Die Konfiguration von Elasticsearch war in beiden Clustern identisch. Die Daten wurden auf jedem Knoten auf 16 Datenträger verteilt, und für jeden Knoten wurden 14 GB RAM für die JVM zugeordnet, auf der Elasticsearch ausgeführt wurde. Der restliche Arbeitsspeicher (ebenfalls 14 GB) wurde für die Verwendung durch das Betriebssystem reserviert. Jeder Test wurde 24 Stunden lang ausgeführt. Dieser Zeitraum wurde gewählt, um die Auswirkungen des zunehmenden Datenvolumens zu verdeutlichen und die Stabilisierung des Systems zu ermöglichen. In der Tabelle unten sind die Ergebnisse zusammengefasst. Sie verdeutlichen die Reaktionszeiten der unterschiedlichen Vorgänge, die am Test beteiligt waren.

 Cluster | Vorgang/Abfrage | Durchschnittliche Reaktionszeit (ms) |
---------|----------------------------|----------------------------|
 D4 | Erfassung | 978 |
 | Count By Rating | 103 |
 | Count Over Time | 134 |
 | Hits By Country | 199 |
 | Top 15 Organizations | 137 |
 | Unique Count Organizations | 139 |
 | Unique IP Count | 510 |
 | Total Hits Counts | 89
 DS4 | Erfassung | 511 |
 | Count By Rating | 187 |
 | Count Over Time | 411 |
 | Hits By Country | 402 |
 | Top 15 Organizations | 307 |
 | Unique Count Organizations | 320 |
 | Unique IP Count | 841 |
 | Total Hits Counts | 236 |

Auf den ersten Blick scheint es der Fall zu sein, dass Abfragen im DS4-Cluster weniger gut als im D4-Cluster durchgeführt wurden und die Reaktionszeiten zeitweise doppelt so hoch (oder noch höher) waren. Dies ist aber nur die halbe Wahrheit. In der nächsten Tabelle ist die Anzahl der Erfassungsvorgänge angegeben, die von jedem Cluster durchgeführt wurden (denken Sie daran, dass bei jedem Vorgang 1.000 Dokumente geladen wurden):

 Cluster | Anzahl von Erfassungsvorgängen |
---------|-------------------------|
 D4 | 264\.769 |
 DS4 | 503\.157 |

Während des Tests konnten im DS4-Cluster fast doppelt so viele Daten wie im D4-Cluster geladen werden. Daher müssen Sie beim Analysieren der Reaktionszeiten für jeden Vorgang auch berücksichtigen, wie viele Dokumente von jeder Abfrage gescannt werden müssen und wie viele Dokumente zurückgegeben werden. Dies sind dynamische Zahlen, da das Volumen an Dokumenten im Index ständig zunimmt. Sie können nicht einfach 503.137 durch 264.769 teilen (die Anzahl der von jedem Cluster durchgeführten Erfassungsvorgänge) und das Ergebnis dann mit der durchschnittlichen Reaktionszeit für jede Abfrage multiplizieren, die vom D4-Cluster durchgeführt wurde, um eine Vergleichsinformation zu erhalten. Hierbei wird nämlich die Menge der E/A-Vorgänge ignoriert, die vom Erfassungsvorgang gleichzeitig durchgeführt werden. Stattdessen sollten Sie die physische Menge der Daten messen, die im Verlauf des Tests auf Datenträger geschrieben bzw. vom Datenträger gelesen wird. Mit dem JMeter-Testplan werden diese Informationen für jeden Knoten erfasst. Die zusammengefassten Ergebnisse lauten:

 Cluster | Durchschnittliche Anzahl von Byte, die bei jedem Vorgang geschrieben/gelesen werden |
---------|----------------------------------------------|
 D4 | 13\.471.557 |
 DS4 | 24\.643.470 |

Diese Daten zeigen, dass der DS4-Cluster eine E/A-Rate erzielen konnte, die ca. dem 1,8-fachen der Rate des D4-Clusters entspricht. Angesichts der Tatsache, dass alle anderen Ressourcen bis auf die Art der Datenträger gleich sind, muss der Grund für den Unterschied die Nutzung von SSDs anstelle von HDDs sein.

Zur Untermauerung dieser Schlussfolgerung wird mit den folgenden Graphen dargestellt, wie die E/A-Vorgänge im Laufe der Zeit von jedem Cluster durchgeführt wurden:

![](./media/guidance-elasticsearch/query-performance2.png)

<!-- -->

***Abbildung 2: Datenträgeraktivität für D4- und DS4-Cluster***

Der Graph für den D4-Cluster weist signifikante Schwankungen auf, vor allem während der ersten Hälfte des Tests. Dies liegt wahrscheinlich an einer Drosselung mit dem Ziel, die E/A-Rate zu reduzieren. In den Anfangsphasen des Tests können die Abfragen schnell ausgeführt werden, da nur wenig Daten zu analysieren sind. Die Datenträger im D4-Cluster kommen daher vermutlich nahe an ihre IOPS-Kapazität heran, auch wenn bei jedem E/A-Vorgang ggf. nicht viele Daten zurückgegeben werden. Der DS4-Cluster kann eine höhere IOPS-Rate unterstützen und unterliegt nicht dem gleichen Drosselungsgrad. Die E/A-Raten sind regelmäßiger. Zur Untermauerung dieser Theorie zeigt das nächste Graphenpaar, wie die CPU im Laufe der Zeit durch die E/A-Vorgänge der Datenträger blockiert wurde (die in den Graphen dargestellten Datenträger-Wartezeiten sind der Zeitanteil, den die CPU mit dem Warten auf E/A-Vorgänge verbracht hat):

![](./media/guidance-elasticsearch/query-performance3.png)

***Abbildung 3: Wartezeiten von CPU-Datenträger-E/A-Vorgängen für D4- und DS4-Cluster***

Es ist wichtig zu verstehen, dass es zwei Hauptgründe dafür gibt, warum die CPU durch E/A-Vorgänge blockiert wird:

- Das E/A-Subsystem liest Daten vom Datenträger bzw. schreibt Daten auf den Datenträger.

- Das E/A-Subsystem kann durch die Hostumgebung gedrosselt werden. Azure-Datenträger, die mit HDDs implementiert werden, haben einen maximalen Durchsatz von 500 IOPS, und SSDs haben einen maximalen Durchsatz von 5.000 IOPS.


Für den D4-Cluster korreliert der Zeitraum, der mit dem Warten auf E/A-Vorgänge verbracht wird, während der ersten Hälfte des Tests in umgekehrter Weise eng mit dem Graphen, mit dem die E/A-Raten angezeigt werden. Zeiträume mit niedrigem E/A-Aufwand entsprechen Zeiträumen, in denen die CPU lange blockiert ist. Dies deutet auf eine E/A-Drosselung hin. Wenn dem Cluster weitere Daten hinzugefügt werden, ändert sich die Situation. Während der zweiten Hälfte des Tests entsprechen E/A-Wartezeiten Spitzen beim E/A-Durchsatz. An diesem Punkt ist die CPU blockiert, während echte E/A-Vorgänge durchgeführt werden. Beim DS4-Cluster ist die Zeit, die mit dem Warten auf E/A-Vorgänge verbracht wird, wieder viel gleichmäßiger verteilt, und jede Spitze verfügt anstatt eines Tals über eine äquivalente Spitze bei der E/A-Leistung. Dies deutet darauf hin, dass nur wenig oder gar keine Drosselung auftritt.

Es muss noch ein weiterer Faktor berücksichtigt werden. Während des Tests wurden vom D4-Cluster 10.584 Erfassungsfehler und 21 Abfragefehler generiert. Beim Test im DS4-Cluster wurden keine Fehler produziert.

## Leistungsergebnisse – Zentrales Hochskalieren

Das Testen des zentralen Hochskalierens wurde erreicht, indem Tests mit 6-Knoten-Clustern und DS3-, DS4- und DS14-VMs durchgeführt wurden. Diese SKUs wurden ausgewählt, weil eine DS4-VM doppelt so viele CPU-Kerne und doppelt so viel Arbeitsspeicher wie eine DS3-VM bereitstellt. Mit einem DS14-Computer werden die CPU-Ressourcen noch einmal verdoppelt, und es ist die vierfache Menge an Arbeitsspeicher vorhanden. In der Tabelle unten werden die wichtigsten Aspekte der einzelnen SKUs verglichen:

 SKU | Anzahl CPU-Kerne | Arbeitsspeicher (GB) | Max. Datenträger-IOPS | Max. Bandbreite (MBit/s)|
------|-------------|-------------|---------------|--------------|
 DS3 | 4 | 14 | 12\.800| 128 |
 DS4 | 8 | 28 | 25\.600| 256 |
 DS14 | 16 | 112 | 50\.000| 512 |

In der Tabelle unten sind die Ergebnisse der Tests für den kleinen (DS3), mittelgroßen (DS4) und großen Cluster (DS14) zusammengefasst. Für jeden virtuellen Computer wurden SSDs zum Speichern der Daten verwendet. Jeder Test wurde 24 Stunden lang ausgeführt:

> **Hinweis**: In der Tabelle ist die Anzahl der erfolgreichen Anforderungen für jede Art von Abfrage angegeben (Fehler nicht enthalten). Die Anzahl der Anforderungen, deren Durchführung für jede Art von Abfrage versucht wird, bleibt im Verlauf des Tests nahezu gleich. Der Grund ist, dass der JMeter-Testplan jede Abfrage einmal ausführt (Count By Rating, Count Over Time, Hits By Country, Top 15 Organizations, Unique Count Organizations, Unique IP Count und Total Hits Counts). Dies passiert zusammen in einer einzelnen Einheit, die als *Testtransaktion* bezeichnet wird (diese Transaktion ist unabhängig von der Aufgabe, mit der der Erfassungsvorgang durchgeführt wird, der über einen separaten Thread ausgeführt wird). Mit jedem Durchlauf des Testplans wird eine einzelne Testtransaktion durchgeführt. Die Anzahl der abgeschlossenen Testtransaktionen ist daher ein Maß für die Reaktionszeit der langsamsten Abfrage jeder Transaktion.

| Cluster | Vorgang/Abfrage | Anzahl von Anforderungen | Durchschnittliche Reaktionszeit (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Klein (DS3) | Erfassung | 207\.284 | 3\.328 |
| | Count By Rating | 18\.444 | 268 |
| | Count Over Time | 18\.444 | 340 |
| | Hits By Country | 18\.445 | 404 |
| | Top 15 Organizations | 18\.439 | 323 |
| | Unique Count Organizations | 18\.437 | 338 |
| | Unique IP Count | 18\.442 | 468 |
| | Total Hits Counts | 18\.428 | 294   
|||||
| Mittelgroß (DS4) | Erfassung | 503\.157 | 511 |
| | Count By Rating | 6\.958 | 187 |
| | Count Over Time | 6\.958 | 411 |
| | Hits By Country | 6\.958 | 402 |
| | Top 15 Organizations | 6\.958 | 307 |
| | Unique Count Organizations | 6\.956 | 320 |
| | Unique IP Count | 6\.955 | 841 |
| | Total Hits Counts | 6\.958 | 236 |
|||||
| Groß (DS14) | Erfassung | 502\.714 | 511 |
| | Count By Rating | 7\.041 | 201 |
| | Count Over Time | 7\.040 | 298 |
| | Hits By Country | 7\.039 | 363 |
| | Top 15 Organizations | 7\.038 | 244 |
| | Unique Count Organizations | 7\.037 | 283 |
| | Unique IP Count | 7\.037 | 681 |
| | Total Hits Counts | 7\.038 | 200 |

Diese Zahlen zeigen, dass die Leistung des DS4-Clusters und DS14-Clusters bei diesem Test ähnlich war. Die Reaktionszeiten für die Abfragevorgänge für den DS3-Cluster scheinen zu Beginn auch ähnlich zu sein, und die Anzahl der durchgeführten Abfragevorgänge liegt weit über den Werten für den DS4-Cluster und DS14-Cluster. Beachten Sie aber auch immer die Erfassungsrate und die damit verbundene Anzahl der Dokumente, die durchsucht werden. Im DS3-Cluster ist die Erfassung stärker eingeschränkt, und am Ende des Tests enthält die Datenbank nur ca. 40 % der Dokumente, die von den anderen beiden Clustern eingelesen werden. Dies kann an den Verarbeitungsressourcen, Netzwerkelementen und Datenträgerbandbreiten liegen, die für eine DS3-VM im Vergleich zu einer DS4- oder DS14-VM verfügbar sind. Da für eine DS4-VM doppelt so viele Ressourcen wir für eine DS3-VM verfügbar sind und eine DS14 über die doppelten Ressourcen (das Vierfache an Arbeitsspeicher) einer DS4-VM verfügt, stellt sich folgende Frage: Warum ist der Unterschied bei den Erfassungsraten zwischen dem DS4- und DS14-Cluster deutlich geringer als zwischen dem DS3- und DS4-Cluster? Dies kann an der Netzwerkauslastung und an Bandbreitenbeschränkungen von Azure-VMs liegen. In den Graphen unten sind diese Daten für alle drei Cluster dargestellt:

![](./media/guidance-elasticsearch/query-performance4.png)

***Abbildung 4: Netzwerkauslastung für DS3-, DS4- und DS14-Cluster beim *Erfassungs- und Abfragetest****

<!-- -->

Die Grenzen der verfügbaren Netzwerkbandbreite für Azure-VMs werden nicht veröffentlicht und können variieren. Aber die Tatsache, dass sich die Netzwerkaktivität sowohl für den DS4- als auch für den DS14-Test bei durchschnittlich 2,75 GBit/s eingependelt hat, lässt darauf schließen, dass eine Grenze erreicht wurde und diese in Bezug auf die Beschränkung des Durchsatzes zum bestimmenden Faktor geworden ist. Beim DS3-Cluster lag die Netzwerkaktivität deutlich niedriger, sodass die geringere Leistung vermutlich eher auf die Einschränkungen bei der Verfügbarkeit anderer Ressourcen zurückzuführen ist.

Um die Auswirkungen der Erfassungsvorgänge zu isolieren und darzustellen, wie die Abfrageleistung beim zentralen Hochskalieren von Knoten variiert, wurde mit denselben Knoten eine Reihe von reinen Abfragetests durchgeführt. In der folgenden Tabelle sind die Ergebnisse zusammengefasst, die in den einzelnen Clustern erzielt wurden:

> [AZURE.NOTE] Sie sollten die Leistung und Anzahl der Anforderungen, die von Abfragen beim *reinen Abfragetest* durchgeführt werden, nicht mit den Angaben für den *Erfassungs- und Abfragetest* vergleichen. Der Grund ist, dass die Abfragen geändert wurden und der Umfang der beteiligten Dokumente unterschiedlich ist.

| Cluster | Vorgang/Abfrage | Anzahl von Anforderungen | Durchschnittliche Reaktionszeit (ms) |
|--------------|----------------------------|--------------------|----------------------------|
| Klein (DS3) | Count By Rating | 464 | 11\.758 |
| | Count Over Time | 464 | 14\.699 |
| | Hits By Country | 463 | 14\.075 |
| | Top 15 Organizations | 464 | 11\.856 |
| | Unique Count Organizations | 462 | 12\.314 |
| | Unique IP Count | 461 | 19\.898 |
| | Total Hits Counts | 462 | 8\.882  
|||||
| Mittelgroß (DS4) | Count By Rating | 1045 | 4\.489 |
| | Count Over Time | 1045 | 7\.292 |
| | Hits By Country | 1\.053 | 7\.564 |
| | Top 15 Organizations | 1\.055 | 5\.066 |
| | Unique Count Organizations | 1\.051 | 5\.231 |
| | Unique IP Count | 1\.051 | 9\.228 |
| | Total Hits Counts | 1\.051 | 2\.180 |
|||||
| Groß (DS14) | Count By Rating | 1\.842 | 1\.927 |
| | Count Over Time | 1\.839 | 4\.483 |
| | Hits By Country | 1\.838 | 4\.761 |
| | Top 15 Organizations | 1\.842 | 2\.117 |
| | Unique Count Organizations | 1\.837 | 2\.393 |
| | Unique IP Count | 1\.837 | 7\.159 |
| | Total Hits Counts | 1\.837 | 642 |

In diesem Fall sind die Trends bei den durchschnittlichen Reaktionszeiten für die unterschiedlichen Cluster deutlicher erkennbar. Die Netzwerkauslastung liegt deutlich unterhalb des Werts von 2,75 GBit/s, der zuvor für den DS4- und DS14-Cluster erforderlich war (womit das Netzwerk bei den Erfassungs- und Abfragetests wahrscheinlich ausgelastet war), und des Werts von 1,5 GBit/s für den DS3-Cluster. Der Wert liegt hier in allen Fällen näher an 200 MBit/s, wie an den Graphen unten zu sehen ist:

![](./media/guidance-elasticsearch/query-performance5.png)

***Abbildung 5: Netzwerkauslastung für DS3-, DS4- und DS14-Cluster beim *reinen Abfragetest****

Der einschränkende Faktor scheint für den DS3- und DS4-Cluster jetzt die CPU-Auslastung zu sein, die meist nahe bei 100 % liegt. Im DS14-Cluster liegt die CPU-Auslastung im Durchschnitt knapp über 80 %. Dies ist immer noch ein hoher Wert, aber es wird deutlich, dass die Verfügbarkeit von mehr CPU-Kernen Vorteile hat. In der folgenden Abbildung sind die CPU-Auslastungsmuster für den DS3-, DS4- und DS14-Cluster dargestellt.

![](./media/guidance-elasticsearch/query-performance6.png)

***Abbildung 6: CPU-Auslastung für DS3- und DS14-Cluster beim *reinen Abfragetest****

## Leistungsergebnisse – Horizontales Hochskalieren

Um zu verdeutlichen, wie für das System das horizontale Hochskalieren für eine bestimmte Anzahl von Knoten abläuft, wurden Tests mit DS14-Cluster und ein, drei und sechs Knoten durchgeführt. In diesem Fall wurde nur der *reine Abfragetest* durchgeführt, indem 100 Millionen Dokumente und eine Ausführungsdauer von 90 Minuten verwendet wurden:

> [AZURE.NOTE] Ausführliche Informationen dazu, wie sich das horizontale Hochskalieren auf das Verhalten von Datenerfassungsvorgängen auswirken kann, finden Sie im Dokument [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure).

| Cluster | Vorgang/Abfrage | Anzahl von Anforderungen | Durchschnittliche Reaktionszeit (ms) |
|---------|----------------------------|--------------------|----------------------------|
| 1 Knoten | Count By Rating | 288 | 6\.216 |
| | Count Over Time | 288 | 28\.933 |
| | Hits By Country | 288 | 29\.455 |
| | Top 15 Organizations | 288 | 9\.058 |
| | Unique Count Organizations | 287 | 19\.916 |
| | Unique IP Count | 284 | 54\.203 |
| | Total Hits Counts | 287 | 3\.333 |
|||||
| 3 Knoten | Count By Rating | 1\.194 | 3\.427 |
| | Count Over Time | 1\.194 | 5\.381 |
| | Hits By Country | 1\.191 | 6\.840 |
| | Top 15 Organizations | 1\.196 | 3\.819 |
| | Unique Count Organizations | 1\.190 | 2\.938 |
| | Unique IP Count | 1\.189 | 12\.516 |
| | Total Hits Counts | 1\.191 | 1\.272 |
|||||
| 6 Knoten | Count By Rating | 1\.842 | 1\.927 |
| | Count Over Time | 1\.839 | 4\.483 |
| | Hits By Country | 1\.838 | 4\.761 |
| | Top 15 Organizations | 1\.842 | 2\.117 |
| | Unique Count Organizations | 1\.837 | 2\.393 |
| | Unique IP Count | 1\.837 | 7\.159 |
| | Total Hits Counts | 1\.837 | 642 |

Die Anzahl der Knoten macht einen erheblichen Unterschied in Bezug auf die Abfrageleistung des Clusters aus, wenn auch auf nicht lineare Weise. Für den 3-Knoten-Cluster werden ca. viermal so viele Abfragen wie für den Cluster mit nur einem Knoten durchgeführt, und mit dem 6-Knoten-Cluster können sechsmal so viele Abfragen verarbeitet werden. Zur Erläuterung dieser Nichtlinearität ist in den folgenden Graphen dargestellt, wie die CPU von den drei Clustern genutzt wurde:

![](./media/guidance-elasticsearch/query-performance7.png)

***Abbildung 7: CPU-Auslastung für 1-, 3- und 6-Knoten-Cluster beim *reinen Abfragetest****

Die Cluster mit einem und drei Knoten sind CPU-gebunden, während beim Cluster mit sechs Knoten die CPU-Auslastung zwar hoch ist, aber gleichzeitig zusätzliche Verarbeitungskapazität verfügbar ist. In diesem Fall wird der Durchsatz wahrscheinlich durch andere Faktoren eingeschränkt. Dies kann bestätigt werden, indem Tests mit 9 und 12 Knoten durchgeführt werden, die dann voraussichtlich weitere Verarbeitungskapazität ergeben würden.

Die Daten in der Tabelle oben veranschaulichen auch, wie die durchschnittlichen Reaktionszeiten für Abfragen variieren. Dieser Punkt liefert am meisten Informationen, wenn getestet wird, wie ein System die Skalierung für bestimmte Arten von Abfragen durchführt. Einige Suchen sind eindeutig viel effizienter als andere Suchen, wenn sie übergreifend für eine größere Zahl von Knoten durchgeführt werden. Der Grund hierfür kann sein, dass sich das Verhältnis zwischen der Anzahl von Knoten und der Anzahl von Dokumenten im Cluster erhöht. Jeder Cluster enthielt 100 Millionen Dokumente. Beim Durchführen von Suchvorgängen unter Verwendung von Datenaggregation verarbeitet und puffert Elasticsearch die Daten, die im Rahmen des Aggregationsprozesses abgerufen werden, auf jedem Knoten im Arbeitsspeicher. Wenn mehr Knoten vorhanden sind, müssen auf jedem Knoten weniger Daten abgerufen, gepuffert und verarbeitet werden.

## Leistungsergebnisse – Anzahl der Replikate

Die *Erfassungs- und Abfragetests* wurden für einen Index mit einem einzelnen Replikat ausgeführt. Die Tests wurden auf den DS4- und DS14-Clustern mit sechs Knoten wiederholt, indem ein Index mit zwei konfigurierten Replikaten verwendet wurde. Alle Tests wurden 24 Stunden lang ausgeführt. In der Tabelle unten sind die Vergleichsergebnisse für ein und zwei Replikate angegeben:

| Cluster | Vorgang/Abfrage | Durchschnittliche Reaktionszeit (ms) – 1 Replikat | Durchschnittliche Reaktionszeit (ms) – 2 Replikate | Unterschied bei der Reaktionszeit in % |
|---------|----------------------------|----------------------------------------|-----------------------------------------|-------------------------------|
| DS4 | Erfassung | 511 | 655 | +28 % |
| | Count By Rating | 187 | 168 | -10 % |
| | Count Over Time | 411 | 309 | -25 % |
| | Hits By Country | 402 | 562 | +40 % |
| | Top 15 Organizations | 307 | 366 | +19 % |
| | Unique Count Organizations | 320 | 378 | +18 % |
| | Unique IP Count | 841 | 987 | +17 % |
| | Total Hits Counts | 236 | 236 | +0 % |
||||||
| DS14 | Erfassung | 511 | 618 | +21 % |
| | Count By Rating | 201 | 275 | +37 % |
| | Count Over Time | 298 | 466 | +56 % |
| | Hits By Country | 363 | 529 | +46 % |
| | Top 15 Organizations | 244 | 407 | +67 % |
| | Unique Count Organizations | 283 | 403 | +42 % |
| | Unique IP Count | 681 | 823 | +21 % |
| | Total Hits Counts | 200 | 221 | +11 % |

Die Erfassungsrate nahm ab, wenn die Anzahl von Replikaten erhöht wurde. Dies ist zu erwarten, da Elasticsearch mehr Kopien jedes Dokuments schreibt und zusätzlichen E/A-Datenträgeraufwand generiert. Dies wird in der Abbildung unten durch die Graphen für den DS14-Cluster für Indizes mit einem und zwei Replikaten veranschaulicht. Beim Index mit einem Replikat betrug die durchschnittliche E/A-Rate 16.896.573 Byte/Sekunde. Für den Index mit zwei Replikaten betrug die durchschnittliche E/A-Rate 33.986.843 Byte/Sekunde, also etwas mehr als doppelt so viel.

![](./media/guidance-elasticsearch/query-performance8.png)

***Abbildung 8: E/A-Datenträgerraten für Knoten mit einem Replikat und zwei Replikaten für den *Erfassungs- und Abfragetest****

| Cluster | Abfragen | Durchschnittliche Reaktionszeit (ms) – 1 Replikat | Durchschnittliche Reaktionszeit (ms) – 2 Replikate |
|---------|----------------------------|----------------------------------------|-----------------------------------------|
| DS4 | Count By Rating | 4\.489 | 4\.079 |
| | Count Over Time | 7\.292 | 6\.697 |
| | Hits By Country | 7\.564 | 7\.173 |
| | Top 15 Organizations | 5\.066 | 4\.650 |
| | Unique Count Organizations | 5\.231 | 4\.691 |
| | Unique IP Count | 9\.228 | 8\.752 |
| | Total Hits Counts | 2\.180 | 1\.909 |
|||||
| DS14 | Count By Rating | 1\.927 | 2\.330 |
| | Count Over Time | 4\.483 | 4\.381 |
| | Hits By Country | 4\.761 | 5\.341 |
| | Top 15 Organizations | 2\.117 | 2\.560 |
| | Unique Count Organizations | 2\.393 | 2\.546 |
| | Unique IP Count | 7\.159 | 7\.048 |
| | Total Hits Counts | 642 | 708 |

Diese Ergebnisse zeigen eine Verbesserung der durchschnittlichen Reaktionszeit für den DS4-Cluster, aber eine Zunahme für den DS14-Cluster. Damit Sie diese Ergebnisse besser interpretieren können, sollten Sie auch die Anzahl der Abfragen berücksichtigen, die von den einzelnen Tests durchgeführt werden:

| Cluster | Abfragen | Anz. durchgeführter Abfragen – 1 Replikat | Anz. durchgeführter Abfragen – 2 Replikate |
|---------|----------------------------|------------------------------|-------------------------------|
| DS4 | Count By Rating | 1\.054 | 1\.141 |
| | Count Over Time | 1\.054 | 1\.139 |
| | Hits By Country | 1\.053 | 1\.138 |
| | Top 15 Organizations | 1\.055 | 1\.141 |
| | Unique Count Organizations | 1\.051 | 1\.136 |
| | Unique IP Count | 1\.051 | 1\.135 |
| | Total Hits Counts | 1\.051 | 1\.136 |
|||||
| DS14 | Count By Rating | 1\.842 | 1\.718 |
| | Count Over Time | 1\.839 | 1\.716 |
| | Hits By Country | 1\.838 | 1\.714 |
| | Top 15 Organizations | 1\.842 | 1\.718 |
| | Unique Count Organizations | 1\.837 | 1\.712 |
| | Unique IP Count | 1\.837 | 1\.712 |
| | Total Hits Counts | 1\.837 | 1\.712 |

Diese Daten zeigen, dass sich die Anzahl der Abfragen, die vom DS4-Cluster durchgeführt wurden, analog zur Verringerung der durchschnittlichen Reaktionszeit erhöht hat. Für den DS14-Cluster ist wieder der umgekehrte Fall zu beobachten. Ein wichtiger Faktor ist, dass die CPU-Auslastung des DS4-Clusters bei den Tests mit einem Replikat und zwei Replikaten ungleichmäßig verteilt war. Bei einigen Knoten lag die Auslastung fast bei 100 %, während andere noch über freie Verarbeitungskapazität verfügten. Der Grund für die Verbesserung der Leistung ist wahrscheinlich die Möglichkeit, die Verarbeitung besser auf die Knoten im Cluster zu verteilen. Die folgende Abbildung veranschaulicht die Abweichungen bei der CPU-Verarbeitung zwischen den am wenigsten und am meisten verwendeten virtuellen Computern (Knoten 4 und 3):

![](./media/guidance-elasticsearch/query-performance9.png)

***Abbildung 9: CPU-Auslastung für die am wenigsten und am meisten verwendeten Knoten im DS4-Cluster für den *reinen Abfragetest****

Für den DS14-Cluster war dies nicht der Fall. Die CPU-Auslastung für beide Tests war über alle Knoten hinweg geringer, und die Verfügbarkeit eines zweiten Replikats war nicht mehr unbedingt ein Vorteil, sondern sorgte eher für Mehraufwand:

![](./media/guidance-elasticsearch/query-performance10.png)

***Abbildung 10: CPU-Auslastung für die am wenigsten und am meisten verwendeten Knoten im DS14-Cluster für den *reinen Abfragetest****

Diese Ergebnisse zeigen, dass Sie sorgfältige Benchmark-Tests für Ihr System durchführen sollten, um entscheiden zu können, ob die Verwendung mehrerer Replikate sinnvoll ist. Sie sollten immer mindestens ein Replikat für jeden Index verwenden (falls Sie keine Daten verlieren möchten, wenn ein Knoten ausfällt), aber zusätzliche Replikate können für das System eine Belastung darstellen, während die damit verbundenen Vorteile gering sind. Dies hängt von Ihren Workloads und den Hardwareressourcen ab, die für den Cluster verfügbar sind.

## Leistungsergebnisse – Doc Values

Die *Erfassungs- und Abfragetests* wurden mit aktivierten Doc Values durchgeführt, sodass Daten, die zum Sortieren von Feldern verwendet wurden, von Elasticsearch auf Datenträger gespeichert wurden. Die Tests wurden dann mit deaktivierten Doc Values wiederholt, sodass Felddaten von Elasticsearch dynamisch erstellt und im Arbeitsspeicher zwischengespeichert wurden. Alle Tests wurden 24 Stunden lang ausgeführt. In der Tabelle unten werden die Reaktionszeiten für Tests verglichen, die für Cluster mit sechs Knoten und D4-, DS4- und DS14-VMs durchgeführt werden (im D4-Cluster werden normale Festplatten verwendet, während im DS4- und DS14-Cluster SSDs zum Einsatz kommen).

| Cluster | Vorgang/Abfrage | Durchschnittliche Reaktionszeit (ms) – Doc Values aktiviert | Durchschnittliche Reaktionszeit (ms) – Doc Values deaktiviert | Unterschied bei der Reaktionszeit in % |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| D4 | Erfassung | 978 | 835 | -15 % |
| | Count By Rating | 103 | 132 | +28 % |
| | Count Over Time | 134 | 189 | +41 % |
| | Hits By Country | 199 | 259 | +30 % |
| | Top 15 Organizations | 137 | 184 | +34 % |
| | Unique Count Organizations | 139 | 197 | +42 % |
| | Unique IP Count | 510 | 604 | +18 % |
| | Total Hits Counts | 89 | 134 | +51 % |
||||||
| DS4 | Erfassung | 511 | 581 | +14 % |
| | Count By Rating | 187 | 190 | +2 % |
| | Count Over Time | 411 | 409 | -0,5 % |
| | Hits By Country | 402 | 414 | +3 % |
| | Top 15 Organizations | 307 | 284 | -7 % |
| | Unique Count Organizations | 320 | 313 | -2 % |
| | Unique IP Count | 841 | 955 | +14 % |
| | Total Hits Counts | 236 | 281 | +19 % |
||||||
| DS14 | Erfassung | 511 | 571 | +12 % |
| | Count By Rating | 201 | 232 | +15 % |
| | Count Over Time | 298 | 341 | +14 % |
| | Hits By Country | 363 | 457 | +26 % |
| | Top 15 Organizations | 244 | 338 | +39 % |
| | Unique Count Organizations | 283 | 350 | +24 % |
| | Unique IP Count | 681 | 909 | +33 % |
| | Total Hits Counts | 200 | 245 | +23 % |

In der Tabelle unten wird jeweils die Anzahl der Erfassungsvorgänge verglichen, die von den Tests durchgeführt werden:

| Cluster | Anzahl der Erfassungsvorgänge – Doc Values aktiviert | Anzahl der Erfassungsvorgänge – Doc Values deaktiviert | Unterschied bei der Anz. von Erfassungsvorgängen in % |
|---------|----------------------------------------------|-----------------------------------------------|-----------------------------------------|
| D4 | 264\.769 | 408\.690 | +54 % |
| DS4 | 503\.137 | 578\.237 | +15 % |
| DS14 | 502\.714 | 586\.472 | +17 % |

Die verbesserten Erfassungsraten werden mit deaktivierten Doc Values erzielt, da weniger Daten auf Datenträger geschrieben werden, wenn Dokumente eingefügt werden. Die verbesserte Leistung macht sich besonders bei der D4-VM bemerkbar, für die zum Speichern von Daten HDDs verwendet werden. In diesem Fall hat sich die Reaktionszeit für Erfassungsvorgänge ebenfalls um 15 % verringert (siehe erste Tabelle in diesem Abschnitt). Dies kann an der reduzierten Belastung auf den HDDs liegen, die beim Test mit aktivierten Doc Values vermutlich nahe an ihren IOPS-Grenzwerten gearbeitet haben. Weitere Informationen finden Sie unter dem Test des Datenträgertyps. Im folgenden Graphen werden die E/A-Leistung der D4-VMs mit aktivierten Doc Values (Werte auf Datenträger gespeichert) und deaktivierten Doc Values (Werte im Arbeitsspeicher vorgehalten) verglichen:

![](./media/guidance-elasticsearch/query-performance11.png)

***Abbildung 11: Datenträgeraktivität für D4-Cluster mit aktivierten und deaktivierten Doc Values***

Im Gegensatz hierzu ist für die Erfassungswerte für die VMs mit SSDs ein geringer Anstieg bei der Anzahl der Dokumente zu beobachten, aber gleichzeitig auch ein Anstieg bei der Reaktionszeit der Erfassungsvorgänge. Bis auf ein oder zwei kleinere Ausnahmen waren die Reaktionszeiten für Abfragen ebenfalls schlechter. Für die SSDs ist es weniger wahrscheinlich, dass sie bei aktivierten Doc Values nahe an ihren IOPS-Grenzwerten ausgeführt werden. Gründe für Änderungen bei der Leistung sind also eher eine höhere Verarbeitungsaktivität und der Mehraufwand für die Verwaltung des JVM-Heaps. Dies wird deutlich, wenn Sie die CPU-Auslastung bei aktivierten und deaktivierten Doc Values vergleichen. Im nächsten Graphen sind diese Daten für den DS4-Cluster hervorgehoben. Der Großteil der CPU-Auslastung verschiebt sich aus dem Bereich 30 % - 40 % für aktivierte Doc Values in den Bereich 40 % - 50 % für deaktivierte Doc Values (der DS14-Cluster zeigt einen ähnlichen Trend):

![](./media/guidance-elasticsearch/query-performance12.png)

***Abbildung 12: CPU-Auslastung für DS4-Cluster mit aktivierten und deaktivierten Doc Values***

Zur Unterscheidung der Auswirkungen, die Doc Values auf die Abfrageleistung bei der Datenerfassung haben, wurden jeweils zwei reine Abfragetests für den DS4-Cluster und DS14-Cluster mit aktivierten und deaktivierten Doc Values durchgeführt. In der Tabelle unten sind die Ergebnisse dieser Tests zusammengefasst:

| Cluster | Vorgang/Abfrage | Durchschnittliche Reaktionszeit (ms) – Doc Values aktiviert | Durchschnittliche Reaktionszeit (ms) – Doc Values deaktiviert | Unterschied bei der Reaktionszeit in % |
|---------|----------------------------|-------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Count By Rating | 4\.489 | 3\.736 | -16 % |
| | Count Over Time | 7\.293 | 5\.459 | -25 % |
| | Hits By Country | 7\.564 | 5\.930 | -22 % |
| | Top 15 Organizations | 5\.066 | 3\.874 | -14 % |
| | Unique Count Organizations | 5\.231 | 4\.483 | -2 % |
| | Unique IP Count | 9\.228 | 9\.474 | +3 % |
| | Total Hits Counts | 2\.180 | 1\.218 | -44 % |
||||||
| DS14 | Count By Rating | 1\.927 | 2\.144 | +11 % |
| | Count Over Time | 4\.483 | 4\.337 | -3 % |
| | Hits By Country | 4\.761 | 4\.840 | +2 % |
| | Top 15 Organizations | 2\.117 | 2\.302 | +9 % |
| | Unique Count Organizations | 2\.393 | 2\.497 | +4 % |
| | Unique IP Count | 7\.159 | 7\.639 | +7 % |
| | Total Hits Counts | 642 | 633 | -1 % |

Beachten Sie, dass Doc Values ab Elasticsearch 2.0 standardmäßig aktiviert sind. Bei den Tests für den DS4-Cluster scheint das Deaktivieren der Doc Values insgesamt eine positive Auswirkung zu haben, während es für den DS14-Cluster generell umgekehrt ist (in den beiden Fällen, in denen die Leistung mit deaktivierten Doc Values höher ist, sind die Ergebnisse sehr knapp).

Für den DS4-Cluster lag die CPU-Auslastung in beiden Fällen während der gesamten Testdauer bei fast 100 %. Dies deutet darauf hin, dass der Cluster CPU-gebunden war. Die Anzahl der verarbeiteten Abfragen verringerte sich aber von 7.369 auf 5.894 (20 %). Dies ist in der Tabelle unten zu sehen. Beachten Sie, dass Elasticsearch bei deaktivierten Doc Values Felddaten dynamisch im Arbeitsspeicher generiert und dass dies CPU-Leistung verbraucht. Mit dieser Konfiguration wurde die E/A-Rate für den Datenträger reduziert, während gleichzeitig die Belastung für die CPUs erhöht wurde, die bereits nahe an ihrer Belastungsgrenze gearbeitet haben. In diesem Fall sind Abfragen also bei deaktivierten Doc Values schneller, aber es sind weniger davon vorhanden.

Bei den DS14-Tests mit und ohne Doc Values war die CPU-Aktivität hoch, lag aber nicht bei 100 %. Die Anzahl der durchgeführten Abfragen war etwas höher (ca. 4 %) bei Tests, für die die Doc Values aktiviert waren:

| Cluster | Abfragen | Anz. durchgeführter Abfragen – Doc Values aktiviert | Anz. durchgeführter Abfragen – Doc Values deaktiviert |
|---------|----------------------------|---------------------------------------|----------------------------------------|
| DS4 | Count By Rating | 1\.054 | 845 |
| | Count Over Time | 1\.054 | 844 |
| | Hits By Country | 1\.053 | 842 |
| | Top 15 Organizations | 1\.055 | 846 |
| | Unique Count Organizations | 1\.051 | 839 |
| | Unique IP Count | 1\.051 | 839 |
| | Total Hits Counts | 1\.051 | 839  
||||| |
| DS14 | Count By Rating | 1\.772 | 1\.842 |
| | Count Over Time | 1\.772 | 1\.839 |
| | Hits By Country | 1\.770 | 1\.838 |
| | Top 15 Organizations | 1\.773 | 1\.842 |
| | Unique Count Organizations | 1\.769 | 1\.837 |
| | Unique IP Count | 1\.768 | 1\.837 |
| | Total Hits Counts | 1\.769 | 1\.837 |

## Leistungsergebnisse – Cache für Shardanforderungen

Um zu veranschaulichen, wie das Zwischenspeichern von Indexdaten im Arbeitsspeicher jedes Knotens die Leistung beeinträchtigen kann, wurde der *Erfassungs- und Abfragetest* für einen DS4- und einen DS14-Cluster mit sechs Knoten und aktivierter Indexzwischenspeicherung durchgeführt. Weitere Informationen finden Sie im Abschnitt [Verwenden des Caches für Shardanforderungen](#using-the-shard-request-cache). Die Ergebnisse wurden mit den von vorherigen Tests generierten Ergebnissen verglichen, für die derselbe Index verwendet wurde, die Indexzwischenspeicherung aber deaktiviert war. In der Tabelle unten sind die Ergebnisse zusammengefasst. Beachten Sie, dass die Daten gekürzt wurden, um nur die ersten 90 Minuten des Tests abzudecken. An diesem Punkt war der Vergleichstrend offensichtlich, und die Fortsetzung des Tests hätte vermutlich nicht zu neuen Erkenntnissen geführt:

| Cluster | Vorgang/Abfrage | Durchschnittliche Reaktionszeit (ms) – Indexzwischenspeicherung deaktiviert | Durchschnittliche Reaktionszeit (ms) – Indexzwischenspeicherung aktiviert | Unterschied bei der Reaktionszeit in % |
|---------|----------------------------|---------------------------------------------------|--------------------------------------------------|-------------------------------|
| DS4 | Erfassung | 504 | 3\.260 | +547 % |
| | Count By Rating | 218 | 273 | +25 % |
| | Count Over Time | 450 | 314 | -30 % |
| | Hits By Country | 447 | 397 | -11 % |
| | Top 15 Organizations | 342 | 317 | -7 % |
| | Unique Count Organizations | 370 | 324 | -12 % |
| | Unique IP Count | 760 | 355 | -53 % |
| | Total Hits Counts | 258 | 291 | +12 % |
||||||
| DS14 | Erfassung | 503 | 3\.365 | +569 % |
| | Count By Rating | 234 | 262 | +12 % |
| | Count Over Time | 357 | 298 | -17 % |
| | Hits By Country | 416 | 383 | -8 % |
| | Top 15 Organizations | 272 | 324 | -7 % |
| | Unique Count Organizations | 330 | 321 | -3 % |
| | Unique IP Count | 674 | 352 | -48 % |
| | Total Hits Counts | 227 | 292 | +29 % |

Diese Daten verdeutlichen zwei interessante Punkte:

-  Die Datenerfassungsraten scheinen sich durch das Aktivieren der Indexzwischenspeicherung stark zu verringern.

-  Durch die Indexzwischenspeicherung werden die Reaktionszeiten aller Arten von Abfragen nicht unbedingt verbessert. Außerdem kann sie sich negativ auf bestimmte Aggregatvorgänge auswirken, die beispielsweise von den Abfragen „Count By Rating“ und „Total Hits Counts“ durchgeführt werden.
 

Um zu verstehen, warum das System dieses Verhalten zeigt, sollten Sie die Anzahl der Abfragen berücksichtigen, die während der Testläufe jeweils durchgeführt werden. Diese Daten sind in der folgenden Tabelle zusammengefasst:

| Cluster | Vorgang/Abfrage | Anzahl der Vorgänge/Abfragen – Indexzwischenspeicherung deaktiviert | Anzahl der Vorgänge/Abfragen – Indexzwischenspeicherung aktiviert |
|---------|----------------------------|-------------------------------------------------|------------------------------------------------|
| DS4 | Erfassung | 38\.611 | 13\.232 |
| | Count By Rating | 524 | 18\.704 |
| | Count Over Time | 523 | 18\.703 |
| | Hits By Country | 522 | 18\.702 |
| | Top 15 Organizations | 521 | 18\.706 |
| | Unique Count Organizations | 521 | 18\.700 |
| | Unique IP Count | 521 | 18\.699 |
| | Total Hits Counts | 521 | 18\.701  
|||| |
| DS14 | Erfassung | 38\.769 | 12\.835 |
| | Count By Rating | 528 | 19\.239 |
| | Count Over Time | 528 | 19\.239 |
| | Hits By Country | 528 | 19\.238 |
| | Top 15 Organizations | 527 | 19\.240 |
| | Unique Count Organizations | 524 | 19\.234 |
| | Unique IP Count | 524 | 19\.234 |
| | Total Hits Counts | 527 | 19\.236 |

Sie sehen, dass sich die Anzahl der durchgeführten Abfragen um den Faktor 34 erhöht hat, obwohl die Erfassungsrate bei aktivierter Zwischenspeicherung nur bei einem Drittel der Rate bei deaktivierter Zwischenspeicherung lag. Für Abfragen fällt nicht mehr so ein hoher E/A-Aufwand für Datenträger an, und es entsteht kein Konflikt in Bezug auf Datenträgerressourcen. Dies wird durch die Graphen unten in Abbildung 13 widergespiegelt, mit denen die E/A-Aktivität für alle vier Fälle verglichen wird:

![](./media/guidance-elasticsearch/query-performance13.png)

***Abbildung 13: E/A-Datenträgeraktivität für den *Erfassungs- und Abfragetest* mit deaktivierter und aktivierter Indexzwischenspeicherung***

Die Verringerung des E/A-Aufwands für den Datenträger bedeutet auch, dass die CPU weniger Zeit mit dem Warten auf den Abschluss des E/A-Vorgangs verbringen muss. Dies ist in Abbildung 14 zu sehen:

![](./media/guidance-elasticsearch/query-performance14.png)

***Abbildung 14: CPU-Wartezeit in Bezug auf den Abschluss des Datenträger-E/A-Vorgangs für den *Erfassungs- und Abfragetest* mit deaktivierter und aktivierter Indexzwischenspeicherung***

Die Reduzierung des E/A-Aufwands für den Datenträger bedeutet, dass Elasticsearch einen größeren Anteil der verfügbaren Zeit mit dem Pflegen von Abfragen für Daten verbringen kann, die im Arbeitsspeicher vorgehalten werden. Hierdurch wird die CPU-Auslastung erhöht. Dies wird deutlich, wenn Sie sich die CPU-Auslastung für alle vier Fälle ansehen. Die folgenden Graphen veranschaulichen, dass die CPU-Auslastung nachhaltiger war, als die Zwischenspeicherung aktiviert war:

![](./media/guidance-elasticsearch/query-performance15.png)

***Abbildung 15: CPU-Auslastung für den *Erfassungs- und Abfragetest* mit deaktivierter und aktivierter Indexzwischenspeicherung***

Der Umfang des E/A-Aufwands im Netzwerk war bei beiden Szenarien für die Dauer des Tests sehr ähnlich. Bei den Tests ohne Zwischenspeicherung zeigte sich während des Testzeitraums eine allmähliche Verschlechterung, aber bei den längeren Tests über 24 Stunden wurde ermittelt, dass sich dieser Wert bei ca. 2,75 GBit/s einpendelte. In der folgenden Abbildung sind diese Daten für die DS4-Cluster dargestellt (die Daten für die DS14-Cluster waren sehr ähnlich):

![](./media/guidance-elasticsearch/query-performance16.png)

***Abbildung 16: Umfang des Netzwerk-Datenverkehrs für den *Erfassungs- und Abfragetest* mit deaktivierter und aktivierter Indexzwischenspeicherung***

Wie im Test [Zentrales Hochskalieren](#performance-results-scaling-up) beschrieben, werden die Einschränkungen der Netzwerkbandbreite für Azure-VMs nicht veröffentlicht und können variieren. Aber die niedrigen Werte für die CPU- und Datenträgeraktivität deuten darauf hin, dass die Netzwerkauslastung in diesem Szenario unter Umständen der einschränkende Faktor ist.

Das Zwischenspeichern eignet sich besser für Szenarien, in denen sich die Daten weniger oft ändern. Um die Auswirkungen der Zwischenspeicherung in diesem Szenario zu verdeutlichen, wurden die *reinen Abfragetests* mit aktivierter Zwischenspeicherung durchgeführt. Die Ergebnisse sind unten angegeben (diese Tests wurden 90 Minuten lang durchgeführt, und die getesteten Indizes enthielten 100 Millionen Dokumente):

| Cluster | Abfragen | Durchschnittliche Reaktionszeit (ms) | Anzahl der durchgeführten Abfragen |
|---------|----------------------------|----------------------------|-------------------------|
| | | **Cache deaktiviert** | **Cache aktiviert** |
| DS4 | Count By Rating | 4\.489 | 210 |
| | Count Over Time | 7\.292 | 211 |
| | Hits By Country | 7\.564 | 231 |
| | Top 15 Organizations | 5\.066 | 211 |
| | Unique Count Organizations | 5\.231 | 211 |
| | Unique IP Count | 9\.228 | 218 |
| | Total Hits Counts | 2\.180 | 210  
|||| |
| DS14 | Count By Rating | 1\.927 | 211 |
| | Count Over Time | 4\.483 | 219 |
| | Hits By Country | 4\.761 | 236 |
| | Top 15 Organizations | 2\.117 | 212 |
| | Unique Count Organizations | 2\.393 | 212 |
| | Unique IP Count | 7\.159 | 220 |
| | Total Hits Counts | 642 | 211 |

Die Leistungsabweichung bei den Tests ohne Zwischenspeicherung beruht auf dem Unterschied bei den Ressourcen, die für DS4- und DS14-VMs verfügbar sind. In beiden Fällen des Tests mit Zwischenspeicherung fiel die durchschnittliche Reaktionszeit deutlich ab, als Daten direkt aus dem Arbeitsspeicher abgerufen wurden. Beachten Sie auch, dass die Reaktionszeiten für die Tests mit dem DS4- und DS14-Cluster mit Zwischenspeicherung sehr ähnlich waren, obwohl ein klarer Unterschied zu den Ergebnissen der Tests ohne Zwischenspeicherung besteht. Auch zwischen den Reaktionszeiten für jede Abfrage innerhalb der einzelnen Tests ist nur ein sehr kleiner Unterschied zu beobachten. Die Dauer beträgt immer etwa 220 ms. Die Datenträger-E/A-Raten und die CPU-Auslastung für beide Cluster waren sehr niedrig, da nur noch wenig E/A-Vorgänge bzw. Verarbeitungen erforderlich sind, nachdem sich alle Daten im Arbeitsspeicher befinden. Die Netzwerk-E/A-Rate ähnelte der Rate der Tests ohne Zwischenspeicherung. Dies bestätigt, dass die Netzwerkbandbreite bei diesem Test ein einschränkender Faktor sein könnte. In den folgenden Graphen werden diese Informationen für den DS4-Cluster dargestellt. Das Profil des DS14-Clusters war sehr ähnlich:

![](./media/guidance-elasticsearch/query-performance17.png)

***Abbildung 17: Datenträger-E/A, CPU-Auslastung und Netzwerkauslastung für den *reinen Abfragetest* mit aktivierter Indexzwischenspeicherung***

Die Zahlen in der Tabelle oben deuten darauf hin, dass die Verwendung der DS14-Architektur im Vergleich zu DS4 nur einen geringen Vorteil bietet. Die Anzahl der vom DS14-Cluster generierten Beispielwerte lag zudem ca. 5 % unter der Anzahl des DS4-Clusters. Dies kann aber auch an Netzwerkeinschränkungen liegen, die im Laufe der Zeit leicht variieren können.

## Leistungsergebnisse – Anzahl der Shards

Mit diesem Test sollte ermittelt werden, ob sich die Anzahl der für einen Index erstellten Shards auf die Abfrageleistung des Index auswirkt.

Zuvor durchgeführte separate Tests haben gezeigt, dass sich die Shardkonfiguration eines Index auf die Datenerfassungsrate auswirken kann. Diese Tests sind im Dokument [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure) beschrieben. Bei den Tests, die zur Sicherstellung der Abfrageleistung durchgeführt wurden, wurde eine ähnliche Methodik verwendet, aber sie waren auf einen 6-Knoten-Cluster auf DS14-Hardware beschränkt. Mit diesem Ansatz soll die Anzahl der Variablen verringert werden, sodass alle Unterschiede bei der Leistung auf das Shardvolumen zurückzuführen sein sollten.

Der *reine Abfragetest* wurde mit Kopien desselben Index durchgeführt, der jeweils mit 7, 13, 23, 37 und 61 primären Shards konfiguriert war. Der Index enthielt 100 Millionen Dokumente und ein einzelnes Replikat, sodass sich die Anzahl von Shards im Cluster verdoppelte. Jeder Test wurde 90 Minuten lang durchgeführt. Die Ergebnisse sind in der folgenden Tabelle zusammengefasst. Die angegebene durchschnittliche Reaktionszeit ist die Reaktionszeit für die JMeter-Testtransaktion, die den gesamten Satz mit Abfragen umfasst, die bei jedem Durchlauf des Tests durchgeführt werden. Weitere Informationen finden Sie unter dem Hinweis im Abschnitt [Leistungsergebnisse – Zentrales Hochskalieren](#performance-results-scaling-up):

| Anzahl von Shards | Shardlayout (Shards pro Knoten, einschl. Replikate) | Anzahl der durchgeführten Abfragen | Durchschn. Reaktionszeit (ms) |
|---------------------------|----------------------------------------------------|-----------------------------|------------------------|
| 7 (14 einschl. Replikate) | 3-2-2-2-2-3 | 7\.461 | 40524 |
| 13 (26) | 5-4-5-4-4-4 | 7\.369 | 41\.055 |
| 23 (46) | 7-8-8-7-8-8 | 14\.193 | 21\.283 |
| 37 (74) | 13-12-12-13-12-12 | 13\.399 | 22\.506 |
| 61 (122) | 20-21-20-20-21-20 | 14\.743 | 20\.445 |

Diese Ergebnisse zeigen, dass ein erheblicher Leistungsunterschied zwischen dem Cluster mit 13 (26) Shards und dem Cluster mit 23 (46) Shards besteht. Der Durchsatz verdoppelt sich fast, und die Reaktionszeiten verringern sich um die Hälfte. Der wahrscheinlichste Grund hierfür ist die Konfiguration der VMs und der Strukturen, die von Elasticsearch zum Verarbeiten von Suchanfragen verwendet werden. Suchanfragen werden in die Warteschlange eingereiht, und jede Suchanfrage wird von einem einzelnen Suchthread verarbeitet. Die Anzahl der Suchthreads, die von einem Elasticsearch-Knoten erstellt werden, ist eine Funktion mit der Anzahl von Prozessoren, die auf dem Computer zum Hosten des Knotens verfügbar sind. Die Ergebnisse deuten darauf hin, dass die Verarbeitungsressourcen bei nur vier oder fünf Shards auf einem Knoten nicht vollständig ausgelastet werden. Diese Einschätzung wird untermauert, wenn wir uns während des Tests die CPU-Auslastung ansehen. Die folgende Abbildung ist eine Momentaufnahme aus Marvel während des Tests mit 13 (26) Shards:

![](./media/guidance-elasticsearch/query-performance18.png)

***Abbildung 18: CPU-Auslastung für den *reinen Abfragetest* im Cluster mit 7 (14) Shards***

Vergleichen Sie diese Werte mit den Werten des Tests mit 23 (46) Shards:

![](./media/guidance-elasticsearch/query-performance19.png)

***Abbildung 19: CPU-Auslastung für den *reinen Abfragetest* im Cluster mit 23 (46) Shards***

Beim Test mit 23 (46) Shards war die CPU-Auslastung deutlich höher. Jeder Knoten enthält sieben oder acht Shards. Die DS14-Architektur verfügt über 16 Prozessoren, und Elasticsearch kann diese Zahl von Kernen mit den zusätzlichen Shards besser ausnutzen. Die Werte in der Tabelle oben deuten darauf hin, dass eine Erhöhung der Anzahl von Shards über diesen Punkt hinaus zu einer leichten Verbesserung der Leistung führen kann. Sie sollten diesen Werten aber den zusätzlichen Mehraufwand für die Wartung eines hohen Shardvolumens gegenüberstellen. Für diese Tests liegt die optimale Anzahl von Shards pro Knoten nach Datenlage bei der Hälfte der Anzahl von Prozessorkernen, die auf den einzelnen Knoten verfügbar sind. Beachten Sie aber, dass diese Ergebnisse erzielt wurden, als ausschließlich Abfragen durchgeführt wurden. Wenn Ihr System Daten importiert, sollten Sie auch berücksichtigen, wie sich das Sharding auf die Leistung von Vorgängen zur Datenerfassung auswirken kann. Weitere Informationen zu diesem Aspekt finden Sie im Dokument [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure).

## Zusammenfassung

Elasticsearch umfasst viele Optionen, die Sie zum Strukturieren von Indizes verwenden und optimieren können, um umfangreiche Abfragevorgänge zu unterstützen. In diesem Dokument wurden einige allgemeine Konfigurationen und Verfahren zusammengefasst, die Sie verwenden können, um Ihre Datenbank für Abfragezwecke zu optimieren. Sie sollten sich aber darüber im Klaren sein, dass zwischen dem Optimieren einer Datenbank zur Unterstützung des schnellen Abrufs und der Unterstützung einer Datenerfassung mit hohem Volumen eine Abhängigkeit besteht. Maßnahmen, die für Abfragen gut sind, können in einigen Fällen negative Auswirkungen auf Einfügevorgänge haben. Dies kann aber auch umgekehrt gelten. In einem System, für das gemischte Workloads anfallen, müssen Sie bewerten, wie Sie die richtige Balance erzielen können, und die Parameter entsprechend anpassen.

Darüber hinaus kann die Anwendbarkeit unterschiedlicher Konfigurationen und Verfahren variieren. Dies hängt von der Struktur der Daten und den Einschränkungen (oder anderen Aspekten) der Hardware ab, auf der das System erstellt wird. Viele der in diesem Dokument gezeigten Tests verdeutlichen, wie sich die Auswahl der Hardwareplattform auf den Durchsatz auswirken kann und wie einige Strategien in bestimmten Fällen vorteilhaft sein und in anderen Fällen mit Nachteilen verbunden sein können. Es ist wichtig, dass Sie die verfügbaren Optionen verstehen und dann strenge Benchmark-Tests für Ihre eigenen Daten durchführen, um die optimale Kombination zu ermitteln.

Denken Sie schließlich auch daran, dass es sich bei einer Elasticsearch-Datenbank nicht unbedingt um ein statisches Element handelt. Der Umfang nimmt im Laufe der Zeit meist zu, und die Strategien zum Strukturieren der Daten müssen unter Umständen regelmäßig überprüft werden. Beispielsweise kann es erforderlich sein, für Daten das zentrale Hochskalieren, horizontale Hochskalieren oder Neuindizieren mit zusätzlichen Shards durchzuführen. Wenn die Größe und Komplexität des Systems zunimmt, sollten Sie darauf vorbereitet sein, die Leistung fortlaufend zu testen. So stellen Sie sicher, dass Sie weiterhin alle SLA-Vorgaben für Ihre Kunden erfüllen.

## Anhang: Test der Abfrage- und Aggregationsleistung

Dieser Anhang beschreibt den Leistungstest für den Elasticsearch-Cluster. Die Tests wurden mit JMeter auf separaten virtuellen Maschinen ausgeführt. Ausführliche Informationen zur Konfiguration der Testumgebung finden Sie im Dokument „Gewusst wie: Erstellen einer Leistungstestumgebung für Elasticsearch“. Für Ihre eigenen Tests können Sie einen eigenen JMeter-Testplan manuell erstellen, indem Sie die Anleitung in diesem Anhang nutzen, oder Sie können die separat verfügbaren automatisierten Testskripts verwenden. Weitere Informationen finden Sie im Dokument „Gewusst wie: Ausführen von automatisierten Elasticsearch-Abfragetests“.

Für die Datenabfrage-Workload wurden die unten beschriebenen Abfragen durchgeführt, während gleichzeitig in großem Umfang Dokumente hochgeladen wurden. (Die Daten wurden per JUnit-Test hochgeladen, indem für die Datenerfassung der gleiche Ansatz verwendet wurde, der im Dokument [Maximizing Data Ingestion Performance with Elasticsearch on Azure](https://github.com/mspnp/azure-guidance/blob/master/Elasticsearch-Data-Ingestion-Performance.md) (Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure) beschrieben ist.) Der Zweck dieser Workload war die Simulation einer Produktionsumgebung, in der ständig neue Daten hinzugefügt wurden, während Suchvorgänge durchgeführt wurden. Die Abfragen wurden so strukturiert, dass nur die neuesten Daten aus den Dokumenten abgerufen werden, die innerhalb der letzten 15 Minuten hinzugefügt wurden.

Jedes Dokument wurde in einem eigenen Index mit dem Namen *idx* und dem Typ *doc* gespeichert. Sie können die folgende HTTP-Anforderung zum Erstellen des Index verwenden. Die Einstellungen *number\_of\_replicas* und *number\_of\_shards* (Anzahl der Replikate und Anzahl der Shards) wichen in vielen Tests von den unten angegebenen Werten ab. Außerdem wurde jede Eigenschaft für die Tests, in denen anstelle von Doc Values Felddaten verwendet wurden, mit dem Attribut *"doc\_values" : false* versehen.

> **Wichtig**: Der Index wurde gelöscht und vor jedem Testlauf neu erstellt.

``` http
PUT /idx
{  
    "settings" : {
        "number_of_replicas": 1,
        "refresh_interval": "30s",
        "number_of_shards": "5",
        "index.translog.durability": "async"    
    },
    "doc": {
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

Die folgenden Abfragen wurden im Rahmen des Tests durchgeführt:
* Wie viele Dokumente mit jedem Rating-Wert wurden innerhalb der letzten 15 Minuten eingegeben?

  ```http
  GET /idx/doc/_search
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

* Wie viele Dokumente wurden innerhalb der letzten 15 Minuten in jedem 5-Minuten-Intervall hinzugefügt?

  ```http
  GET /idx/doc/_search
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

* Wie viele Dokumente mit jedem Rating-Wert wurden für jedes Land innerhalb der letzten 15 Minuten hinzugefügt?

  ```HTTP
  GET /idx/doc/_search
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

* Welche 15 Organisationen kommen in Dokumenten, die innerhalb der letzten 15 Minuten hinzugefügt wurden, am häufigsten vor?

  ```http
  GET /idx/doc/_search
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

* Wie viele unterschiedliche Organisationen kommen in Dokumenten vor, die innerhalb der letzten 15 Minuten hinzugefügt wurden?

  ```http
  GET /idx/doc/_search
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

* Wie viele Dokumente wurden innerhalb der letzten 15 Minuten hinzugefügt?

  ```http
  GET /idx/doc/_search
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

* Wie viele unterschiedliche SourceIp-Werte kommen in Dokumenten vor, die innerhalb der letzten 15 Minuten hinzugefügt wurden?

  ```http
  GET /idx/doc/_search
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

<!---HONumber=AcomDC_0302_2016-->