<properties
   pageTitle="Anleitungen zur Datenpartitionierung | Microsoft Azure"
   description="Leitfaden zum Trennen von Partitionen für die separate Verwaltung und den separaten Zugriff."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/20/2015"
   ms.author="masashin"/>

# Leitfaden zur Datenpartitionierung

![](media/best-practices-data-partitioning/pnp-logo.png)

## Übersicht

In vielen umfangreichen Lösungen werden Daten in getrennte Partitionen aufgeteilt, die getrennt verwaltet werden können und auf die separat zugegriffen werden kann. Die Partitionierungsstrategie muss sorgfältig ausgewählt werden, um die Vorteile zu maximieren und gleichzeitig nachteilige Auswirkungen zu minimieren. Partitionierung kann die Skalierbarkeit verbessern, Konflikte reduzieren und die Leistung optimieren. Ein Zusatznutzen der Partitionierung ist, dass sie auch einen Mechanismus zum Unterteilen von Daten nach Anwendungsmuster bieten kann. Sie können ältere, inaktivere (kalte) Daten auf kostengünstigeren Datenspeichern archivieren.

## Gründe für Datenpartitionierung

Die meisten Cloud-Anwendungen und Dienste speichern Daten als Teil ihrer Vorgänge ab und rufen diese auf. Das Design der Datenspeicher, die eine Anwendung nutzt, kann einen erheblichen Einfluss auf die Leistung, den Durchsatz und die Skalierbarkeit eines Systems haben. Eine Technik, die häufig für großmaßstäbliche Systeme eingesetzt wird, ist die Unterteilung von Daten in separate Partitionen.

> Der in diesem Leitfaden verwendete Begriff _Partitionierung_ bezieht sich auf den physischen Prozess der Datenunterteilung in separate Datenspeicher. Dies ist nicht identisch mit der SQL Server-Tabellenpartitionierung, die ein anderes Konzept darstellt.

Partitionieren von Daten kann eine Reihe von Vorteilen bieten. Es kann z. B. angewendet werden für:

- **Verbesserung der Skalierbarkeit**. Die Skalierung eines einzelnen Datenbanksystems erreicht schließlich ein physisches Hardware-Limit. Das Unterteilen von Daten über mehrere Partitionen hinweg, von denen jede auf einem separaten Server gehostet wird, ermöglicht dem System fast eine unbegrenzte Skalierung.
- **Verbesserung der Leistung** Zugriffe auf Daten in jeder Partition finden über eine kleinere Datenmenge statt. Vorausgesetzt, dass die Daten auf geeignete Weise partitioniert sind, ist dies sehr viel effizienter. Vorgänge, die mehr als eine Partition betreffen, können parallel ausgeführt werden. Jede Partition kann in der Nähe der Anwendung gefunden werden, die diese verwendet, um die Netzwerklatenz zu minimieren.
- **Verbesserung der Verfügbarkeit**. Das Aufteilen von Daten über mehrere Server hinweg vermeidet eine einzelne Fehlerquelle. Wenn ein Server ausfällt oder planmäßig gewartet wird, sind nur die Daten in der Partition nicht verfügbar. Vorgänge auf anderen Partitionen können fortgesetzt werden. Ein Erhöhen der Anzahl von Partitionen reduziert die relativen Auswirkungen eines einzelnen Server-Ausfalls, indem der Prozentsatz der Daten reduziert wird, die nicht verfügbar sind. Das Replizieren jeder Partition kann das Risiko eines einzelnen Partitionsausfalls, der sich auf die Vorgänge auswirkt, reduzieren. Ebenfalls wird die Trennung wichtiger Daten ermöglicht, die durch Daten mit geringem Wert (z. B. Protokolldateien), die geringere Verfügbarkeitsanforderungen haben, dauerhaft und hochverfügbar sein müssen.
- **Verbesserung die Sicherheit**. Abhängig von der Art der Daten und wie sie partitioniert werden, kann es möglich sein, sensible und nicht sensible Daten in verschiedenen Partitionen zu trennen, und somit auf unterschiedlichen Servern oder Datenspeichern. Die Sicherheit kann dann speziell für die sensiblen Daten optimiert werden.
- **Bereitstellen von Flexibilität bei Vorgängen**. Partitionieren bietet viele Möglichkeiten für die Feinabstimmung von Vorgängen, das Maximieren administrativer Effizienz und die Minimierung von Kosten. Einige Beispiele definieren basierend auf der Wichtigkeit der Daten in jeder Partition unterschiedliche Strategien für die Verwaltung, Überwachung, Sicherung und Wiederherstellung sowie für andere administrative Aufgaben.
- **Übereinstimmung der Daten mit dem Anwendungsmuster**. Mithilfe der Partitionierung kann jede Partition basierend auf den Kosten und integrierten Funktionen, die dieser Datenspeicher bietet, auf einer anderen Art von Datenspeicher bereitgestellt werden. Große binäre Daten könnten z. B. in einem Blob-Datenspeicher gespeichert werden, während strukturiertere Daten in einer Dokumentendatenbank verwahrt werden könnten. Weitere Informationen finden Sie unter [Erstellen einer Polyglot-Lösung] im Patterns & Practices-Handbuch sowie unter [Datenzugriff für hoch skalierbare Lösungen: mit SQL, NoSQL und Polyglot Persistence] auf der Microsoft-Website.

Einige Systeme implementieren keine Partitionierung, da sie als aufwendig anstatt als vorteilhaft empfunden wird. Häufige Erklärungen für diese Begründung umschließen:

- Viele Datenspeichersysteme unterstützen keine Verknüpfungen über Partitionen hinweg und es kann schwierig sein, in einem partitionierten System referenzielle Integrität zu wahren. Es ist häufig erforderlich, Verknüpfungen zu implementieren und Integritätsprüfungen im Anwendungscode (in der Partitionierungsebene) vorzunehmen, was zu zusätzlichen E/A und höherer Anwendungskomplexität führen kann.
- Verwalten von Partitionen ist nicht immer eine einfache Aufgabe. In einem System, in dem die Daten flüchtig sind, müssen Sie die Partitionen in regelmäßigen Abständen zum Reduzieren von Konflikten und Hotspots neu verteilen.
- Einige häufig verwendete Tools funktionieren mit partitionierten Daten nicht natürlich.

## Entwerfen von Partitionen

Daten können auf unterschiedliche Weise partitioniert werden: horizontal, vertikal oder funktional. Die von Ihnen gewählte Strategie hängt von dem Grund für die Partitionierung der Daten sowie von den Anforderungen der Anwendungen und Dienste ab, die die Daten verwenden werden.

> [AZURE.NOTE]Die in diesem Leitfaden beschriebenen Partitionierungsschemen werden auf eine Weise erläutert, die von der zugrunde liegenden Datenspeicherungstechnologie unabhängig ist. Sie können auf viele Arten von Datenspeichern, einschließlich relationalen und NoSQL-Datenbanken, angewendet werden.

### Partitionierungsstrategien

Die drei typischen Strategien zum Partitionieren von Daten sind:

- **Horizontale Partitionierung** (oft _Sharding_ genannt). Bei dieser Strategie stellt jede Partition einen selbständigen Datenspeicher dar, wobei jedoch alle Partitionen das gleiche Schema aufweisen. Jede Partition ist als ein _Shard_ bekannt und enthält eine bestimmte Teilmenge der Daten, wie z. B. alle Bestellungen für einen bestimmten Satz von Kunden in einer E-Commerce-Anwendung.
- **Vertikale Partitionierung**. Bei dieser Strategie enthält jede Partition eine Teilmenge der Felder für Elemente im Datenspeicher. Die Felder werden nach deren Nutzungsmuster verwendet, z. B. häufig verwendete Felder in einer vertikalen Partition und die weniger häufig verwendeten Felder in einer anderen anzuordnen.
- **Funktionale Partitionierung**. Bei dieser Strategie können Sie Daten nach der Art aggregieren, wie sie im System von einzelnen gebundenen Kontexten verwendet werden. Z. B. ein E-Commerce-System, das separate Geschäftsfunktionen für die Rechnungsstellung und die Verwaltung vom Warenbestand implementiert, speichert Rechnungsdaten möglicherweise in einer Partition und Daten zum Warenbestand in einer anderen.

Es ist wichtig zu beachten, dass die hier beschriebenen Strategien kombiniert werden können. Sie schließen sich gegenseitig nicht aus und Sie sollten sie alle beim Entwurf eines Partitionierungsschemas berücksichtigen. Beispielsweise könnten Sie Daten in Shards unterteilen und dann die Daten mittels vertikaler Partitionierung innerhalb der einzelnen Shards weiter unterteilen. Auf ähnliche Weise können die Daten einer funktionalen Partitionierung in Shards aufgeteilt werden (die auch vertikal partitioniert werden können).

Die unterschiedlichen Anforderungen der einzelnen Strategien können jedoch eine Reihe von in Konflikt stehenden Problemen aufwerfen, die ausgewertet und ausgeglichen werden müssen, wenn ein Partitionierungsschema entworfen wird, das die gesamten Leistungsziele zur Datenverarbeitung für Ihr System erfüllt. Die folgenden Abschnitte untersuchen jede einzelne Strategien eingehender.

### Horizontale Partitionierung (Sharding)

Abbildung 1 zeigt eine Übersicht der horizontalen Partitionierung oder Sharding. In diesem Beispiel werden Warenbestandsdaten basierend auf dem Produktschlüssel in Shards unterteilt. Jedes Shard enthält die Daten für einen zusammenhängenden Bereich von Shard-Schlüsseln (A-G und H-Z) in alphabetischer Anordnung.

![](media/best-practices-data-partitioning/DataPartitioning01.png)

_Abbildung 1. - Horizontale Partitionierung (Sharding) von Daten auf Grundlage eines Partitionsschlüssels_

Durch Sharding können Sie die Last auf mehrere Computer verteilen, was Konflikte reduziert und die Leistung verbessert. Sie können das System skalieren, indem Sie weitere Shards, die auf zusätzlichen Servern ausgeführt werden, hinzufügen.

Der wichtigste Faktor bei der Implementierung dieser Partitionierungsstrategie ist die Auswahl der Shardingschlüssel. Es kann schwierig sein, den Schlüssel zu ändern, nachdem das System in Betrieb ist. Der Schlüssel muss sicherstellen, dass Daten auf eine Weise partitioniert werden, die die Workload möglichst gleichmäßig über die Shards hinweg verteilt. Beachten Sie, dass verschiedene Shards keine ähnliche Datenmengen enthalten müssen. Es ist wichtiger, die Anzahl der Anfragen gleichmäßig zu verteilen. Einige Shards sind möglicherweise sehr groß, wobei jedoch jedes Element Gegenstand einer niedrigen Anzahl von Access-Vorgängen ist, während andere Shards möglicherweise kleiner sind, dafür jedoch auf jedes Element viel häufiger zugegriffen wird. Außerdem ist es wichtig sicherzustellen, dass ein einzelnes Shard die Skalierungsgrenzen (im Hinblick auf Kapazität und Verarbeitungsressourcen) des Datenspeichers, der zum Hosten dieses Shard verwendet wird, nicht überschreitet.

Das Shardingschema sollte auch vermeiden, Hotspots (oder aktive Partitionen) zu erstellen, die möglicherweise die Leistung und Verfügbarkeit beeinträchtigen. Z. B. verhindert das Verwenden eines Hash einer Kunden-ID anstelle des ersten Buchstaben des Kundennamens eine unausgeglichene Verteilung, die durch häufige und weniger häufige Anfangsbuchstaben entstehen würde. Dies ist ein übliches Verfahren, mit dessen Hilfe die Daten gleichmäßiger über die Partitionen verteilt werden können.

Der von Ihnen gewählte Shardingschlüssel sollte alle zukünftigen Anforderungen minimieren, um große Shards in kleinere Teile aufzuteilen, kleinere Shards in größeren Partitionen zusammenzufügen oder das Schema zu ändern, das die in einem Satz von Partitionen gespeicherten Daten beschreibt. Diese Vorgänge können sehr zeitaufwändig sein und erfordern möglicherweise ein oder mehrere Shards offline zu schalten, während sie ausgeführt werden. Wenn Shards repliziert werden, kann es möglich sein, dass einige der Replikate weiterhin online sind, während andere geteilt, zusammengeführt oder neu konfiguriert werden. Doch das System muss möglicherweise die Vorgänge begrenzen, die an den Daten in diesen Shards ausgeführt werden können, während die Neukonfiguration stattfindet. Beispielsweise könnten die Daten in den Replikaten als schreibgeschützt markiert werden, um den Umfang von Inkonsistenzen zu begrenzen, die andernfalls auftreten könnten, während Shards umstrukturiert werden.

> Ausführlichere Informationen und Anweisungen zu vielen dieser Überlegungen sowie zu Good Practice-Verfahren für das Entwerfen von Datenspeichern, die horizontale Partitionierung implementieren, finden Sie unter [Sharding-Muster]

### Vertikale Partitionierung

Die häufigste Verwendung für die vertikale Partitionierung ist, die E/A und die Leistungskosten zu reduzieren, die mit dem Abrufen der Elemente verknüpft sind, auf die am häufigsten zugegriffen wird. Abbildung 2 zeigt eine Übersicht eines Beispiel für vertikale Partitionierung, bei der sich verschiedene Eigenschaften für jedes Datenelement in verschiedenen Partitionen befinden. Auf den Namen, die Beschreibung und Preisinformationen für Produkte wird häufiger als auf das Volumen der Lagerbestände oder das Datum der letzten Lieferung zugegriffen.

![](media/best-practices-data-partitioning/DataPartitioning02.png)

_Abbildung 2. - Vertikale Partitionierung von Daten nach deren Nutzungsmuster_

In diesem Beispiel fragt die Anwendung regelmäßig zusammen den Produktname, die Beschreibung und den Preis ab, wenn die Details der Produkte für Kunden angezeigt werden. Die Lagerbestand und das Datum, an dem das Produkt zuletzt vom Hersteller bestellt wurde, sind in einer separaten Partition enthalten, da diese beiden Elemente häufig zusammen verwendet werden. Dieses Partitionierungsschema bietet den zusätzlichen Vorteil, dass sich relativ langsam bewegende Daten (Produktname, Beschreibung und Preis) von den dynamischeren Daten (Lagerbestand und Datum der letzten Bestellung) getrennt werden. Für eine Anwendung ist es möglicherweise sinnvoll, die sich langsam bewegenden Daten im Arbeitsspeicher zwischenzuspeichern, wenn auf diese regelmäßig zugegriffen wird.

Ein weiteres typisches Szenario für diese Partitionierungsstrategie ist die Maximierung der Sicherheit sensibler Daten, z. B. durch Speichern von Kreditkartennummern und der entsprechenden Kartenprüfnummern in separaten Partitionen.

Vertikale Partitionierung kann auch den erforderlichen gleichzeitigen Zugriff auf die Daten verringern.

> Vertikale Partitionierung findet auf der Entitätsebene in einem Datenspeicher statt, wobei eine Entität teilweise normalisiert wird, um sie von einem _breiten_ Element in einen Satz _schmaler_ Elemente aufzuschlüsseln. Es eignet sich ideal für spaltenorientierte Datenspeicher wie z. B. HBase und Cassandra. Wenn es unwahrscheinlich ist, dass sich Daten in einer Auflistung von Spalten ändern, können Sie auch in Betracht ziehen, Spaltenspeicher im SQL Server zu nutzen.

### Funktionale Partitionierung

Für Systeme, in denen es möglich ist, einen gebundenen Kontext für jeden bestimmten Geschäftsbereich oder Dienst in der Anwendung zu identifizieren, bietet die funktionale Partitionierung ein Verfahren zur Verbesserung der Isolation und Datenzugriffsleistung. Darüber hinaus wird die funktionale Partitionierung häufig verwendet, um Lese-/Schreib-Daten von schreibgeschützten Daten zu trennen, die für Berichtszwecke verwendet werden. Abbildung 3 zeigt eine Übersicht einer funktionalen Partitionierung, in der Bestandsdaten von Kundendaten getrennt werden.

![](media/best-practices-data-partitioning/DataPartitioning03.png)

_Abbildung 3. - Funktionale Datenpartitionierung nach gebundenen Kontext oder Unterdomäne_

Diese Partitionierungsstrategie kann helfen, Datenzugriffskonflikte über verschiedene Teile des Systems hinweg zu reduzieren.

## Entwerfen von Partitionen für Skalierbarkeit

Es ist wichtig die Größe und Workload für jede Partition zu berücksichtigen und sie gleichmäßig zu verteilen, sodass die Daten zum Erreichen maximaler Skalierbarkeit verteilt werden. Allerdings müssen Sie die Daten auch so partitionieren, dass nicht die Skalierungsgrenzen des Speicherplatzes der einzelnen Partitionen überschritten werden.

Gehen Sie beim Entwerfen von Partitionen für die Skalierbarkeit folgendermaßen vor:

1. Analysieren Sie die Anwendung, um die Zugriffsmuster der Daten zu verstehen, wie z. B. die Größe des Resultsets für einzelne Abfragen, die Häufigkeit des Zugriffs, die inhärente Latenz und die serverseitige Anforderungen für die Rechenleistung. In vielen Fällen werden einige Hauptentitäten den Großteil der Verarbeitungsressourcen anfordern.
2. Bestimmen Sie auf Grundlage der Analyse die aktuellen und zukünftigen Skalierbarkeitsziele, wie z. B. Größe der Daten und Workload, und verteilen Sie die Daten auf die Partitionen, um das Skalierbarkeitsziel zu erfüllen. Für die Strategie zur horizontalen Partitionierung ist die Auswahl des angemessenen Shardschlüssels wichtig, um sicherzustellen, dass die Verteilung gleichmäßig erfolgt ist. Weitere Informationen finden Sie unter [Sharding-Muster].
3. Stellen Sie sicher, dass die für jede Partition verfügbaren Ressourcen ausreichen, um den Anforderungen an die Skalierbarkeit im Hinblick auf die Größe der Daten und den Durchsatz zu entsprechen. Z. B. erzwingt der Knoten, der eine Partition hostet, möglicherweise eine harte Grenze für die Menge an Speicherplatz, Verarbeitungsleistung oder Netzwerkbandbreite, die er bereitstellt. Wenn der Datenspeicher und die Verarbeitungsanforderungen wahrscheinlich diese Grenzwerte überschreiten, kann es notwendig sein, Ihre Partitionierungsstrategie zu verfeinern oder ausgehende Daten noch weiter aufzuteilen. Ein Skalierbarkeitsansatz kann möglicherweise sein, die Protokollierungsdaten mithilfe separater Datenspeicher von den Kernfunktionen der Anwendung zu trennen, um zu verhindern, dass die Gesamtanforderung an den Datenspeicher die Skalierungsgrenze des Knotens überschreitet. Wenn die Gesamtzahl der Datenspeicher die Knoten überschreitet, kann es notwendig sein, separate Speicherknoten zu verwenden.
4. Überwachen Sie das verwendete System, um sicherzustellen, dass die Daten wie erwartet verteilt werden und die Partitionen die ihnen auferlegte Last verarbeiten können. Es könnte möglich sein, dass die Verwendung nicht mit den aus der Analyse resultierenden Erwartungen übereinstimmt, wodurch es notwendig sein könnte, die Partitionen neu zu verteilen. Schlägt dies fehl, kann es erforderlich sein, einige Teile des Systems umzugestalten, um die erforderliche Balance zu erhalten.

Beachten Sie, dass einige Cloud-Umgebungen Ressourcen mit Blick auf Infrastrukturgrenzen zuweisen und Sie sollten sicherstellen, dass die Grenzwerte der von Ihnen ausgewählten Grenze ausreichend Platz für das erwartete Wachstum in der Datenmenge bezüglich Datenspeicherung, Verarbeitungsleistung und Bandbreite bieten. Wenn Sie z. B. den Azure-Tabellenspeicher nutzen, kann ein ausgelastetes Shard möglicherweise mehr Ressourcen erfordern, als einer einzelnen Partition zur Verfügung stehen, um Anforderungen zu verarbeiten (das Volumen der Anforderungen, die von einer einzelnen Partition in einem bestimmten Zeitraum verarbeitet werden können, ist beschränkt – weitere Informationen finden Sie unter [Azure Storage-Skalierbarkeit und Leistungsziele] auf der Microsoft-Website). In diesem Fall muss das Shard womöglich neu partitioniert werden, um die Last zu verteilen. Wenn die Gesamtgröße oder der Durchsatz dieser Tabellen die Kapazität eines Speicherkontos überschreitet, kann das Erstellen zusätzlicher Speicherkonten sowie die kontenübergreifende Verteilung der Tabellen erforderlich sein. Überschreitet die Anzahl der Speicherkonten die Anzahl der Konten, die für ein Abonnement verfügbar sind, kann es notwendig sein, mehrere Abonnements zu verwenden.

## Entwerfen von Partitionen für die Abfrageleistung

Die Abfrageleistung kann oft mit kleineren Datensätzen und einer parallelen Abfrageausführung erhöht werden. Jede Partition sollte einen kleinen Anteil des gesamten Datasets enthalten, und diese Reduzierung an Volumen kann die Leistung von Abfragen verbessern. Allerdings ist das Partitionieren keine Alternative zum angemessenen Entwerfen und Konfigurieren einer Datenbank. Stellen Sie z. B. sicher, dass Sie über die erforderlichen Indizes verfügen, wenn Sie eine relationale Datenbank verwenden.

Gehen Sie beim Entwerfen von Partitionen für die Abfrageleistung folgendermaßen vor:

1. Überprüfen Sie die Anwendungsanforderungen und Leistung:
	- Verwenden Sie die Unternehmensanforderungen, um die kritischen Anfragen zu bestimmen, die stets schnell ausgeführt werden müssen.
	- Überwachen Sie das System, um Abfragen zu identifizieren, die nur langsam durchgeführt werden.
	- Ermitteln Sie, welche Abfragen am häufigsten ausgeführt werden. Eine einzelne Instanz einer Abfrage kann minimale Kosten haben, aber der kumulative Ressourcenverbrauch könnte erheblich sein. Möglicherweise wäre es vorteilhaft die Daten, die durch diese Abfragen aufgerufen werden, durch eine bestimmte Partition oder sogar Cache zu trennen.
2. Partitionieren Sie die Daten, die für eine geringe Leistung verantwortlich sind. Stellen Sie sicher, dass:
	- Sie die Größe der einzelnen Partitionen beschränken, sodass die Abfrageantwortzeit innerhalb des Ziels liegt.
	- Sie den Shardschlüssel so entwerfen, dass die Anwendung die Partition problemlos finden kann, wenn Sie horizontale Partitionierung implementieren. Dies verhindert, dass die Abfrage jede Partition durchsuchen muss.
	- Sie den Speicherort einer Partition auf die Leistung von Abfragen betrachten. Versuchen Sie nach Möglichkeit die Daten in Partitionen zu belassen, die geografisch dicht bei den Anwendungen und Benutzern liegen, die darauf zugreifen.
3. Besitzt eine Entität Durchsatz und Anforderungen an die Abfrageleistung, verwenden Sie funktionale Partitionierung basierend auf dieser Entität. Werden dadurch immer noch nicht die Anforderungen erfüllt, wenden Sie auch horizontale Partitionierung an. In den meisten Fällen reicht eine einzelne Partitionierungsstrategie, aber in einigen Fällen ist es effizienter, beide Strategien zu kombinieren.
4. Erwägen Sie asynchrone Abfragen, die zur Verbesserung der Leistung über Partitionen hinweg parallel ausgeführt werden.

## Entwerfen von Partitionen für Verfügbarkeit

Partitionieren von Daten kann die Verfügbarkeit von Anwendungen verbessern, indem Sie sicherstellen, dass das gesamte Dataset keine einzelne Fehlerquelle darstellt und einzelne Teilmengen des Datasets unabhängig voneinander verwaltet werden können. Das Replizieren von Partitionen mit wichtigen Daten kann auch die Verfügbarkeit verbessern.

Berücksichtigen Sie beim Entwerfen und Implementieren von Partitionen die folgenden Faktoren, die sich auf die Verfügbarkeit auswirken:

- Wie wichtig die Daten für den Geschäftsbetrieb sind. Einige Daten können kritische geschäftliche Informationen wie z. B. Rechnungsdetails oder Banktransaktionen umfassen. Andere Daten kann möglicherweise weniger kritisch operative Daten darstellen, wie Protokolldateien, Leistungsnachverfolgungen, usw. Berücksichtigen Sie nach dem Ermitteln der jeweiligen Daten Folgendes:
	- Das Speichern von wichtigen Daten in hochverfügbaren Partitionen mit einem geeigneten Sicherungsplans.
	- Einrichten von separater Verwaltung und Überwachungsmechanismen oder Verfahren für die verschiedenen Kritikalitäten von jedem Dataset. Speichern Sie Daten mit der gleichen Kritikalitätsstufe in der gleichen Partition, sodass sie mit angemessener Häufigkeit zusammen gesichert werden können. Partitionen, die beispielsweise Daten für Banktransaktionen enthalten, müssen möglicherweise häufiger als Partitionen mit Protokollierungs- oder Überwachungsinformationen gesichert werden.
- Wie die einzelne Partitionen verwaltet werden können. Das Entwerfen von Partitionen, um unabhängige Verwaltung und Wartung zu unterstützen, bietet mehrere Vorteile. Beispiel:
	- Wenn eine Partition fehlschlägt, kann sie unabhängig wiederhergestellt werden, ohne Auswirkungen auf Instanzen von Anwendungen zu haben, die auf Daten in anderen Partitionen zugreifen.
	- Das Partitionieren von Daten nach geografischem Bereich kann möglicherweise zu geplanten Wartungsaufgaben außerhalb der Spitzenzeiten für jeden Standort führen. Stellen Sie sicher, dass die Partitionen nicht zu groß sind, um zu verhindern, dass eine geplante Wartung während dieses Zeitraums abgeschlossen wird.
- Kritische Daten partitionsübergreifend zu replizieren oder nicht. Diese Strategie kann Verfügbarkeit und Leistung verbessern, obwohl es auch zu Konsistenzproblemen führen kann. Es dauert, bis Änderungen an Daten in einer Partition mit jedem Replikat synchronisiert werden, und während dieses Zeitraums enthalten verschiedene Partitionen unterschiedliche Datenwerte.

## Probleme und Überlegungen

Durch Partitionierung werden Entwurf und die Entwicklung des Systems komplexer. Es ist wichtig, Partitionierung als grundlegenden Bestandteil des Systementwurfs zu berücksichtigen, selbst wenn das System zunächst nur eine Partitionierung enthält. Die Partitionierung erst vorzunehmen, wenn das System erste Leistungs- und Skalierbarkeitsmängel aufweist, steigert lediglich die Komplexität, da Sie mittlerweile wahrscheinlich ein Live-System zu verwalten haben. Eine Aktualisierung des Systems, um die Partitionierung in dieser Umgebung zu integrieren, erfordert nicht nur eine Änderung der Datenzugriffslogik, sondern kann auch das Migrieren großer Mengen von Daten umschließen, um diese auf die Partitionen zu verteilen – oftmals während Benutzer erwarten, das System weiterhin benutzen zu können.

In einigen Fällen wird die Partitionierung nicht als wichtig angesehen, da das ursprüngliche Dataset klein ist und problemlos von einem einzelnen Server verarbeitet werden kann. Dies mag für ein System gelten, das voraussichtlich nicht über seine Anfangsgröße hinaus skalieren wird, aber viele kommerzielle Systeme müssen mit zunehmender Anwenderzahl erweitert werden können. Diese Erweiterung wird in der Regel von einer Zunahme der Datenmenge begleitet. Sie sollten auch wissen, dass die Partitionierung nicht immer eine Funktion von großen Datenspeichern ist. Beispielsweise kann auf einen kleinen Datenspeicher von mehreren hundert gleichzeitigen Clients intensiv zugegriffen werden. Das Partitionieren der Daten kann in dieser Situation helfen, Konflikte zu minimieren und den Durchsatz verbessern.

Beim Entwerfen eines Schemas für die Datenpartitionierung sollten Sie folgende Punkte beachten:

- Wenn möglich, halten Sie die Daten für die am häufigsten verwendeten Datenbankvorgänge in jeder Partition zusammen, um partitionsübergreifende Datenzugriffsvorgänge zu minimieren. Abfragen über Partitionen hinweg kann mehr Zeit in Anspruch nehmen als Abfragen innerhalb einer einzelnen Partition. Das Optimieren der Partitionen für eine Reihe von Abfragen könnte jedoch andere Sätze von Abfragen beeinträchtigen. Um das Abfragen über Partitionen hinweg, in denen dies nicht vermieden werden kann, zu minimieren, führen Sie partitionsübergreifend parallele Abfragen durch und aggregieren Sie die Ergebnisse in der Anwendung. Allerdings ist dieser Ansatz womöglich nicht in allen Fällen möglich, z. B. wenn ein Ergebnis aus einer bestimmten Abfrage für die nächste Abfrage verwendet werden muss.
- Wenn Abfragen relativ statische Verweisdaten nutzen, z. B. Tabellen mit Postleitzahlen oder Produktlisten, überlegen Sie, diese Daten in allen Partitionen zu replizieren, um die Anforderung für einen separaten Suchvorgang in einer anderen Partition zu reduzieren. Dieser Ansatz kann auch die Wahrscheinlichkeit verringern, dass die Verweisdaten ein "heißes" Dataset werden, das umfangreichem Datenverkehr aus dem gesamten System ausgesetzt ist, obwohl mit dem Synchronisieren von Änderungen, die an diesen Verweisdaten auftreten können, zusätzliche Kosten verbunden sind.
- Minimieren Sie gegebenenfalls die Anforderungen für referenzielle Integrität über vertikale und funktionale Partitionen hinweg. In diesen Schemen ist die Anwendung selbst für die Wahrung der referenziellen Integrität über Partitionen hinweg verantwortlich, wenn die Daten aktualisiert und verarbeitet werden. Abfragen, die Daten über mehrere Partitionen hinweg verknüpfen müssen, werden langsamer ausgeführt als Abfragen, die nur Daten in der gleichen Partition verknüpfen, da die Anwendung in der Regel aufeinander folgende Abfragen durchführen muss, basierend auf einem Schlüssel und dann auf einem Fremdschlüssel. Ziehen Sie stattdessen in Betracht, die relevanten Daten zu replizieren oder zu denormalisieren. Um die Zeit für Abfragen zu minimieren, bei denen partitionsübergreifende Verknüpfungen notwendig sind, führen Sie parallele Abfragen über die Partitionen hinweg aus und verknüpfen Sie die Daten in der Partition.
- Wägen Sie die Auswirkungen ab, die das Partitionierungsschema möglicherweise auf die Datenkonsistenz über die Partitionen hinweg hat. Sie sollten überprüfen, ob eine hohe Konsistenz tatsächlich erforderlich ist. Stattdessen ist eine gängige Vorgehensweise in der Cloud, letztendliche Konsistenz zu implementieren. Die Daten in jeder Partition werden separat aktualisiert und die Anwendungslogik kann die Verantwortung für das erfolgreiche Abschließen aller Aktualisierungen übernehmen sowie für den Umgang mit den Inkonsistenzen, die durch Abfragen von Daten auftreten können, während ein letztendlich konsistenter Vorgang ausgeführt wird. Weitere Informationen zum Implementieren von letztendlicher Konsistenz finden Sie im Konsistenz-Leitfaden. (#insertlink#)
- Überlegen Sie, wie Abfragen die richtige Partition finden. Wenn eine Abfrage alle Partitionen durchsuchen muss, um die erforderlichen Daten zu finden, wirkt sich das erheblich auf die Leistung aus, auch wenn mehrere parallele Abfragen verwenden werden. Abfragen, die mit vertikalen und funktionalen Partitionierungsstrategien verwendet werden, können natürlich die Partitionen angeben. Allerdings kann bei der Verwendung von horizontaler Partitionierung (Sharding) die Suche eines Elements schwierig sein, da jedes Shard über das gleiche Schema verfügt. Eine typische Lösung für Sharding ist, eine Zuordnung zu verwalten, die verwendet werden kann, um den Shard-Speicherort für bestimmte Datenelemente zu suchen. Diese Zuordnung kann in der Sharding-Logik der Anwendung implementiert sein oder vom Datenspeicher verwaltet werden, wenn transparentes Sharding unterstützt wird.
- Bei der Verwendung einer horizontalen Partitionierungsstrategie sollten Sie ein regelmäßiges Ausgleichen der Shards erwägen, um die Daten nach Größe und Workload gleichmäßig zu verteilen und so Hotspots zu minimieren, die Abfrageleistung zu maximieren und um physische Speichereinschränkungen herum zu arbeiten. Dies ist jedoch eine komplexe Aufgabe, die häufig ein benutzerdefiniertes Tool oder einen benutzerdefinierten Prozess erfordert.
- Die Replikation jeder Partition bietet zusätzlichen Schutz gegen Fehler. Wenn ein einzelnes Replikat fehlschlägt, können Abfragen gegen eine Arbeitskopie geleitet werden.
- Wenn Sie die physischen Grenzen einer Partitionierungsstrategie erreichen, müssen Sie die Skalierbarkeit auf eine andere Ebene erweitern. Ist die Partitionierung beispielsweise auf der Datenbankebene, kann dies bedeuten, Partitionen in mehreren Datenbanken zu suchen oder zu replizieren. Wenn die Partitionierung bereits auf Datenbankebene ist und physische Einschränkungen ein Problem sind, kann dies bedeuten, Partitionierung in mehreren Hosting-Konten zu suchen oder zu replizieren.
- Vermeiden Sie Transaktionen, die auf Daten in mehreren Partitionen zugreifen. Einige Datenspeicher implementieren Transaktionskonsistenz und Integrität für Vorgänge, die Daten ändern, aber nur, wenn sich diese in einer einzelnen Partition befinden. Wenn Sie Transaktionsunterstützung über mehrere Partitionen benötigen, müssen Sie diese wahrscheinlich als Teil Ihrer Anwendungslogik implementieren, da die meisten Partitionierungssysteme keine systemeigene Unterstützung bereitstellen.

Alle Datenspeicher erfordern ein gewisses Maß an Betriebsverwaltung und Überwachung. Die Aufgaben können vom Laden der Daten, Backup und Wiederherstellen der Daten bis Neuorganisieren der Daten reichen und stellen sicher, dass das System ordnungsgemäß und effizient arbeitet.

Berücksichtigen Sie die folgenden Faktoren, die sich auf die Betriebsverwaltung auswirken:

- Erwägen Sie, wie Sie eine angemessene Verwaltung und operative Aufgaben implementieren, wenn die Daten partitioniert werden, wie z. B. Backup und Wiederherstellung, Archivierung von Daten, die Überwachung des Systems und andere administrativen Aufgaben. Beispielsweise kann das Verwalten von logischer Konsistenz bei Backup- und Wiederherstellungsvorgängen schwierig sein.
- Wie Daten in mehrere Partitionen geladen und möglicherweise neue Daten aus anderen Quellen hinzugefügt werden können Einige Tools und Dienstprogramme unterstützen möglicherweise keine Sharding-Datenvorgänge wie z. B. das Laden von Daten in der richtigen Partition, wodurch das Erstellen und Erhalten neuer Tools und Dienstprogramme erforderlich sein kann.
- Wie die Daten in regelmäßigen Abständen (z. B. monatlich) archiviert und gelöscht werden, um übermäßiges Wachstum von Partitionen zu verhindern. Es kann notwendig sein, die Daten zu transformieren, um einem anderen Archivschema zu entsprechen.
- Es empfiehlt sich, einen regelmäßigen Prozess zum Feststellen möglicher Datenintegritätsprobleme auszuführen, wie z. B. Daten in einer Partition, die auf Informationen in einer anderen verweisen, diese Informationen jedoch fehlen. Der Prozess könnte entweder versuchen, diese Probleme automatisch zu beheben oder eine Warnung an einen Operator zur manuellen Behebung der Probleme geben. In einer E-Commerce-Anwendung können sich beispielsweise die Bestellinformationen in einer Partition befinden, aber die Posten eines jeden Auftrags in einer anderen. Der Prozess einer Bestellaufgabe sieht das Hinzufügen von Daten vor, um sich um Partitionen zu kümmern. Wenn dieser Prozess fehlschlägt, könnten Posten gespeichert werden kann, für die es keine entsprechende Bestellung gibt.

Verschiedene Datenspeicherungstechnologien bieten in der Regel ihre eigenen Funktionen zur Unterstützung der Partitionierung an. Die folgenden Abschnitten fassen die Optionen zusammen, die von Azure-Anwendungen häufig verwendeten Datenspeichern implementiert werden, und beschreiben Überlegungen zum Entwerfen von Anwendungen, mit denen diese Features optimal genutzt werden können.

## Partitionierungsstrategien für Azure SQL-Datenbank

Azure SQL-Datenbank ist eine relationale Datenbank-as-a-Service, die in der Cloud ausgeführt wird. Sie basiert auf dem Microsoft SQL Server. Eine relationale Datenbank teilt die Informationen in Tabellen und jede Tabelle enthält Informationen zu Entitäten als eine Reihe von Zeilen. Jede Zeile enthält Spalten, die die Daten für die einzelnen Felder einer Entität enthalten. Unter [Was ist SQL Database?] auf der Microsoft-Website finden Sie ausführliche Informationen zum Erstellen und Verwenden von SQL-Datenbanken.

## Horizontale Partitionierung mit elastischer Datenbank

Die Datenmenge, die eine einzelne SQL-Datenbank enthalten kann, ist beschränkt und der Durchsatz ist durch architektonische Faktoren sowie die Anzahl der gleichzeitigen Verbindungen, die er unterstützt, eingeschränkt. Die Azure SQL-Datenbank bietet eine elastische Datenbank zur Unterstützung der horizontalen Skalierung für eine SQL-Datenbank. Mithilfe der elastischen Datenbank können Sie Ihre Daten in Shards partitionieren, die über mehrere SQL-Datenbanken verteilt sind, sowie je nach Entwicklung der zu verarbeitenden Daten Shards hinzufügen oder entfernen. Die elastische Datenbank kann auch zur Verringerung von Konflikten beitragen, indem die Last auf mehrere Datenbanken verteilt wird.

> [AZURE.NOTE]Die elastische Datenbank ist seit Dezember 2015 als Vorschauversion verfügbar. Es ist ein Ersatz für die Azure SQL-Datenbankverbände, die abgelöst werden. Bestehende Installationen von Azure SQL-Datenbankverbänden können mithilfe des [Verbund-Migrationsdienstprogramms] zur elastischen Datenbank migriert werden. Alternativ können Sie Ihren eigenen Sharding-Mechanismus implementieren, falls sich Ihr Szenario nicht für die Features der elastischen Datenbank eignet.

Jedes Shard wird als SQL-Datenbank implementiert. Ein Shard kann mehrere Datasets enthalten (genannt ein _Shardlet_) und jede Datenbank verwaltet Metadaten, die die Shardlets beschreiben, die sie enthält. Ein Shardlet kann ein einzelnes Datenelement oder eine Gruppe von Elementen sein, die den gleichen Shardlet-Schlüssel verwenden. Bei Daten-Sharding in einer mehrinstanzenfähigen Anwendung könnte der Shardlet-Schlüssel beispielsweise die Mandanten-ID sein, und alle Daten für einen bestimmten Mandanten würden als Teil der gleichen Shardlet gehalten werden. Daten für andere Mandanten würden in verschiedenen Shardlets gehalten werden.

Der Programmierer ist dafür verantwortlich, einem Dataset einen Shardlet-Schlüssel zuzuordnen. Eine separate SQL-Datenbank fungiert als globaler Shardzuordnungs-Manager, der eine Liste der Datenbanken (Shards) enthält, die das gesamte System zusammen mit Informationen zu den Shardlets in jeder Datenbank enthalten. Eine Clientanwendung, die auf Daten zugreift, verbindet zuerst mit der globalen Shardzuordnungs-Manager-Datenbank, um eine Kopie der Shardzuordnung (Auflistung der Shards und Shardlets) abzurufen, die sie lokal zwischenspeichert. Die Anwendung nutzt dann diese Informationen, um Datenanforderungen an das entsprechenden Shard weiterzuleiten. Diese Funktionalität versteckt sich hinter einer Reihe von APIs, die in der Azure SQL-Datenbank in der Clientbibliothek für elastische Datenbanken enthalten und als NuGet-Paket verfügbar sind. Die Seite [Übersicht über Features für elastische Datenbanken] auf der Microsoft-Website bietet eine ausführlichere Einführung in elastische Datenbanken.

> [AZURE.NOTE]Sie können die globale Shardzuordnungs-Manager-Datenbank replizieren, um die Latenz zu verringern und die Verfügbarkeit zu verbessern. Wenn Sie die Datenbank durch Nutzen einer der Premium-Preisstufen implementieren, können Sie die aktive Georeplikation konfigurieren, um kontinuierlich Daten in Datenbänke in verschiedenen Regionen zu kopieren. Erstellen Sie eine Kopie der Datenbank in jeder Region, in der sich die Benutzer befinden, und konfigurieren Sie Ihre Anwendung für die Verbindung an diese Kopie, um die Shardzuordnung abzurufen.

> Ein alternativer Ansatz ist die Verwendung von Azure SQL-Datensynchronisierung oder ein Azure Data Factory-Pipeline, um die Shardzuordnungs-Manager-Datenbank über Regionen hinweg zu replizieren. Diese Form der Replikation wird in regelmäßigen Abständen ausgeführt und eignet sich besser, wenn die Shardzuordnung unregelmäßig geändert wird. Darüber hinaus muss die Shardzuordnungs-Manager-Datenbank nicht mit einer Premium-Preisstufe erstellt werden.

Elastische Datenbanken bieten zwei Schemas für das Zuordnen von Daten zu Shardlets und deren Speicherung in Shards:

- Eine Listen-Shardzuordnung beschreibt die Zuordnung einzelner Schlüssel zu Shardlets. In einem System mit mehreren Mandanten könnten beispielsweise die Daten für jeden Mandanten einem eindeutigen Schlüssel zugeordnet und in ihrem eigenen Shardlet gespeichert sein. Um Datenschutz und Isolation (um zu verhindern, dass ein Mandant die Datenspeicher-Ressourcen ausschöpft, die anderen zur Verfügung stehen) zu gewährleisten, könnte jedes Shardlet innerhalb seines eigenen Shard gehalten werden.

![](media/best-practices-data-partitioning/PointShardlet.png)

_Abbildung 4. - Verwenden einer Listen-Shardzuordnung, um Mandantendaten in separaten Shards zu speichern_

- Eine Bereichs-Shardzuordnung beschreibt die Zuordnung zwischen einem Satz zusammenhängender Schlüsselwerte und einem Shardlet. Im zuvor beschriebenen Multi-Tenant-Beispiel könnten Sie als Alternative zum Implementieren dedizierter Shardlets die Daten für eine Reihe von Mandanten (jeweils mit ihrem eigenen Schlüssel) innerhalb der gleichen Shardlet gruppieren. Dieses Schema ist kostengünstiger als das erste (Mandanten teilen Datenspeicher-Ressourcen), birgt jedoch das Risiko von geringerem Datenschutz und geringerer Isolation.

![](media/best-practices-data-partitioning/RangeShardlet.png)

_Abbildung 5. - Verwenden einer Bereichs-Shardzuordnung zum Speichern von Daten für einen Reihe von Mandanten in einem Shard_

Beachten Sie, dass ein einzelnes Shard die Daten für mehrere Shardlets enthalten kann. Beispielsweise können Sie die Listen-Shardlets verwenden, um Daten für verschiedene nicht zusammenhängende Mandanten im gleichen Shard zu speichern. Sie können auch Bereichs-Shardlets und Listen-Shardlets im gleichen Shard kombinieren, obwohl sie über verschiedene Zuordnungen hinweg in der globalen Shardzuordnungs-Manger-Datenbank behandelt werden (die globale Shardzuordnungs-Manager-Datenbank kann mehrere Shardzuordnungen enthalten). Abbildung 6 zeigt diesen Ansatz.

![](media/best-practices-data-partitioning/MultipleShardMaps.png)

_Abbildung 6. - Implementierung von mehreren Shardzuordungen_

Das Partitionierungsschema, das Sie implementieren, kann bedeutende Auswirkungen auf die Leistung Ihres Systems haben und sich auch auf die Rate, mit der Shards hinzugefügt oder entfernt werden oder die Daten über Shards hinweg neu partitioniert werden, auswirken. Berücksichtigen Sie beim Partitionieren von Daten mithilfe der elastischen Datenbank folgende Punkte:

- Gruppieren Sie Daten, die in der gleichen Shard verwendet werden, gemeinsam und vermeiden Sie Vorgänge, die auf Daten, die sich in mehreren Shards befinden, zugreifen müssen. Beachten Sie, dass es sich bei einem Shard im Kontext einer elastischen Datenbank um eine selbständige SQL-Datenbank handelt und Azure SQL-Datenbank keine datenbankübergreifenden Verknüpfungen unterstützt. Diese Vorgänge müssen auf der Clientseite ausgeführt werden. Beachten Sie außerdem, dass mit Azure SQL-Datenbank referenzielle Integritätseinschränkungen, Trigger und gespeicherte Verfahren in einer Datenbank auf keine Objekte in einer anderen verweisen können. Entwerfen Sie daher also kein System, in dem Abhängigkeiten zwischen Shards bestehen. Eine SQL-Datenbank kann jedoch Tabellen mit Kopien von Verweisdaten enthalten, die häufig von Abfragen und anderen Vorgängen verwendet werden. Diese Tabellen müssen keinem spezifischen Shardlet angehören. Durch Replizieren dieser Daten über Shards hinweg kann die Notwendigkeit wegfallen, die Daten zu verbinden, die sich über Datenbänke erstrecken. Im Idealfall sollten diese Daten statisch oder langsam bewegend sein, um den Replikationsaufwand zu minimieren und die Wahrscheinlichkeit reduzieren, dass sie veralten.

	> [AZURE.NOTE]Obwohl Azure SQL-Datenbank keine partitionsübergreifende Verknüpfungen unterstützt, können Sie mit der API der elastischen Datenbank shardübergreifende Abfragen durchführen, die Daten in allen Shardlets, auf die durch eine Shardzuordnung verwiesen wird, transparent durchlaufen können. Die API der elastischen Datenbank unterteilt shardübergreifende Abfragen in eine Reihe einzelner Abfragen (eine für jede Datenbank) und führt dann die Ergebnisse zusammen. Weitere Informationen finden Sie auf der Microsoft-Website unter [Multi-Shard-Abfrage].

- Die Daten, die in Shardlets gespeichert sind, die zu der gleichen Shardzuordnung gehören, sollten das gleiche Schema besitzen. Erstellen Sie z. B. keine Listen-Shardzuordnung, die auf einige Shardlets, die Mandantendaten enthalten, oder auf andere Shardlets, die Produktinformationen enthalten, verweist. Diese Regel wird von der elastischen Datenbank zwar nicht erzwungen, die Datenverwaltung und Abfragen werden jedoch sehr komplex, wenn jedes Shardlet ein anderes Schema hat. In dem eben erwähnten Beispiel sollten Sie zwei Listen- Shardzurodnungen erstellen, von denen eine auf Mandantendaten verweist und die andere auf Produktinformationen. Denken Sie daran, dass die Daten, die zu unterschiedlichen Shardlets gehören, in der gleichen Shard gespeichert werden können.

	> [AZURE.NOTE]Die übergreifende Shard-Abfragefunktion der API für die elastische Datenbank hängt von jedem Shardlet in der Shardzuordnung mit dem gleichen Schema ab.
- Transaktionsvorgänge werden nur für die Daten innerhalb der gleichen Shard und nicht über Shards hinweg unterstützt. Transaktionen können sich über Shardlets erstrecken, solange sie dem gleichen Shard angehören. Wenn Ihre Geschäftslogik Transaktionen durchführen muss, sollten Sie daher die betroffenen Daten in der gleichen Shard speichern oder letztendliche Konsistenz implementieren. Weitere Informationen finden Sie im Leitfaden zur Datenkonsistenz.
- Setzen Sie Shards nahe der Benutzer, die auf diese Shards zugreifen (Geolokalisierung von Shards). Diese Strategie wird zur Reduzierung der Latenz beitragen.
- Vermeiden Sie eine Mischung aus sehr aktiven (Hotspots) und relativ inaktiven Shards. Versuchen Sie, die Last gleichmäßig über Shards hinweg zu verteilen. Dies erfordert möglicherweise ein Hashing der Shardlet-Schlüssel.
- Stellen Sie bei der Geolokalisierung von Shards sicher, dass die Schlüssel mit Hash Shardlets zugeordnet sind, die in der Nähe der Benutzer gespeichert sind, die auf diese Daten zugreifen.
- Derzeit wird nur ein begrenzter Satz von SQL-Datentypen als Shardlet-Schlüssel unterstützt: _Int, Bigint, Varbinary_ und _Uniqueidentifier_. SQL _Int-_ und _Bigint-_Typen entsprechen den _Int-_ und _long-_ Datentypen in C# und haben die gleichen Bereiche. Der SQL _Varbinary-_Typ kann mit einer _Byte_ Array im C# verwendet werden und der SQL- _Uniqueidentier-_Typ entspricht der _Guid_ -Klasse in .NET Framework.

Wie der Name schon sagt, kann ein System mit elastischer Datenbank abhängig von der Entwicklung des Datenvolumens Shards hinzufügen oder entfernen. Durch die APIs in der Clientbibliothek für die elastische Datenbank der Azure SQL-Datenbank kann eine Anwendung Shards dynamisch erstellen oder löschen (und transparent den Shardzuordnungs-Manager aktualisieren). Das Entfernen eines Shards ist jedoch ein destruktiver Vorgang, der auch das Löschen aller Daten in diesem Shard erfordert. Wenn eine Anwendung ein Shard in zwei separate Shards aufteilen oder Shards kombinieren muss, bietet die elastische Datenbank einen separaten Split-/Merge-Dienst. Dieser Dienst wird in einem Cloud-gehosteten Dienst ausgeführt (der Entwickler muss diesen Cloud-gehosteten Dienst erstellen) und kümmert sich um das sichere Migrieren von Daten zwischen Shards. Weitere Informationen finden Sie auf der Microsoft-Website unter [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken].

## Partitionierungsstrategien für Azure-Speicher

Azure-Speicher bietet drei Abstraktionen für die Verwaltung von Daten:

- Tabellenspeicher, der skalierbaren Strukturspeicher implementiert. Eine Tabelle enthält eine Auflistung von Entitäten, von denen jede einen Satz von Eigenschaften und Werten enthalten kann.
- Blobspeicher, der Speicher für große Objekte und Dateien bereitstellt.
- Speicherwarteschlangen, die zuverlässiges asynchrones Messaging zwischen Anwendungen unterstützen.

Tabellenspeicher und Blob-Speicher sind im wesentlichen die Schlüsselwert-Speicher, die optimiert sind, um strukturierte und unstrukturierte Daten zu halten. Speicher-Warteschlangen bieten einen Mechanismus zum Erstellen von lose gekoppelten, skalierbaren Anwendungen. Der Tabellenspeicher, Blob-Speicher und Speicher-Warteschlangen werden im Kontext von Azure-Speicherkonten erstellt. Azure-Speicherkonten unterstützen drei Arten von Redundanz:

- Lokal redundanter Speicher, der drei Kopien der Daten in einem einzigen Datacenter verwaltet. Diese Form der Redundanz schützt vor Hardwarefehlern, aber nicht gegen einen Notfall, der das gesamte Datencenter umfasst.
- Zonenredundanter Speicher, der drei Kopien der Daten verwaltet, die in verschiedenen Datencentern innerhalb derselben Region (oder zwischen zwei geografisch nahen Regionen) verteilt sind. Diese Form der Redundanz schützt vor Katastrophen, die innerhalb eines einzigen Datacenters auftreten können, aber nicht vor umfangreichen Netzwerktrennungen, die eine ganze Region betreffen. Beachten Sie, dass zonenredundanter Speicher aktuell nur für Block-Blobs verfügbar ist.
- Georedundanter Speicher, der sechs Kopien von Daten verwaltet: drei Kopien in einer Region (Ihrer lokalen Region) und weitere drei Kopien in einer entfernten Region. Diese Form der Redundanz bietet den höchsten Schutz für Notfälle.

Microsoft hat die Skalierbarkeitsziele für Azure-Speicherkonten veröffentlicht, die Sie unter [Azure Storage-Skalierbarkeit und Leistungsziele] auf der Microsoft-Website finden. Derzeit kann die Gesamtkapazität der Speicherkonten (die Größe der Daten, die im Tabellenspeicher und Blob-Speicher enthalten sind, sowie ausstehende Nachrichten in der Speicherwarteschlange) 500 TB nicht überschreiten. Die maximale Anforderungsrate (sofern eine Entitäts-, BLOB- oder Nachrichtengröße von 1 KB) beträgt 20 KB pro Sekunde. Wenn es wahrscheinlich ist, dass Ihr System diese Grenzen überschreitet, sollten Sie die Partitionierung der Last über mehrere Speicherkonten hinweg in Betracht ziehen. Ein einzelnes Azure-Abonnement kann bis zu 100 Speicherkonten erstellen. Beachten Sie jedoch, dass sich diese Beschränkungen im Laufe der Zeit ändern können.

## Partitionierung des Microsoft Azure-Tabellenspeichers

Azure-Tabellenspeicher ist ein gespeicherter Schlüssel/Wert, der um die Partitionierung herum entworfen wurde. Alle Entitäten werden in einer Partition gespeichert und Partitionen werden intern von Azure-Tabellenspeicher verwaltet. Jede in einer Tabelle gespeicherte Entität muss einen zweiteiligen Schlüssel bereitstellen, der Folgendes umfasst:

- Partitionsschlüssel. Dies ist ein Zeichenfolgewert, der bestimmt, in welcher Partition Azure-Tabellenspeicher die Entität platzieren wird. Alle Entitäten mit demselben Partitionsschlüssel werden in der gleichen Partition gespeichert werden.
- Zeilenschlüssel. Dies ist ein anderer String-Zeichenfolgewert, der die Entität innerhalb der Partition identifiziert. Alle Entitäten innerhalb einer Partition werden durch diesen Schlüssel lexikalisch, in aufsteigender Reihenfolge sortiert. Die Kombination für den Partitions-/Zeilenschlüssel muss für jede Entität eindeutig sein und darf 1 KB Länge nicht überschreiten.

Der Rest der Daten für eine Entität besteht aus anwendungsdefinierten Feldern. Es werden keine bestimmte Schemen erzwungen und jede Zeile kann einen anderen Satz von anwendungsdefinierten Feldern enthalten. Die einzige Einschränkung besteht darin, dass die maximale Größe einer Entität (einschließlich der Partitions- und Zeilenschlüssel) derzeit 1 MB beträgt. Die maximale Größe einer Tabelle ist 200 TB, obwohl diese Zahlen sich in der Zukunft ändern können (Überprüfen Sie die Seite [Azure Storage-Skalierbarkeit und Leistungsziele] auf der Microsoft-Website für die neuesten Informationen zu diesen Grenzen. Wenn Sie versuchen, Entitäten zu speichern, die diese Kapazität überschreiten, sollten Sie diese in mehrere Tabellen aufteilen. Verwenden Sie die vertikale Partitionierung und unterteilen Sie die Felder in Gruppen, auf die höchstwahrscheinlich zusammen zugegriffen wird.

Abbildung 7 zeigt die logische Struktur eines Beispiel-Speicherkontos (Contoso-Daten) für eine fiktive E-Commerce-Anwendung. Das Speicherkonto enthält drei Tabellen (Kundeninformationen, Produktinformationen und Bestellinformationen) und jede Tabelle verfügt über mehrere Partitionen. In der Tabelle Kundeninformationen sind die Daten nach der Stadt partitioniert, in der sich der Kunde befindet, und der Zeilenschlüssel enthält die Kunden-ID. In der Tabelle Produktinformationen sind die Produkte nach Kategorie partitioniert und der Zeilenschlüssel enthält die Produktnummer. In der Tabelle Bestellinformation sind die Bestellungen nach dem Datum partitioniert, an dem sie aufgegeben wurden, und der Zeilenschlüssel gibt die Zeit an, zu der die Bestellung eingegangen ist. Beachten Sie, dass alle Daten anhand der Zeilenschlüssel in jeder Partition sortiert sind.

![](media/best-practices-data-partitioning/TableStorage.png)

_Abbildung 7. - Die Tabellen und Partitionen in einem Beispiel-Speicherkonto_

> [AZURE.NOTE]Azure-Tabellenspeicher fügt auch ein Timestamp-Feld für jede Entität hinzu. Das Timestamp-Feld wird vom Tabellenspeicher verwaltet und wird jedes Mal aktualisiert, wenn die Entität geändert und erneut in eine Partition geschrieben wird. Der Tabellenspeicher-Dienst nutzt dieses Feld, um vollständige Parallelität zu implementieren (jedes Mal, wenn eine Anwendung eine Entität wieder in den Tabellenspeicher schreibt, vergleicht der Tabellenspeicher-Dienst den Wert des Timestamps, der in der Entität geschrieben wird, mit dem Wert im Tabellenspeicher, und wenn sie verschieden sind, muss eine andere Anwendung die Entität seit ihrem Abruf geändert haben und der Schreibvorgang muss fehlgeschlagen sein). Dieses Feld sollten Sie in Ihrem eigenen Code nicht ändern und ebenfalls sollten Sie keinen Wert für dieses Feld festlegen, wenn Sie eine neue Identität erstellen.

Der Azure-Tabellenspeicher verwendet den Partitionsschlüssel, um zu bestimmen, wie die Daten zu speichern sind. Wenn eine Entität einer Tabelle mit einem zuvor nicht verwendetem Partitionsschlüssel hinzugefügt wird, erstellt Azure-Tabellenspeicher eine neue Partition für diese Entität. Andere Entitäten mit demselben Partitionsschlüssel werden in der gleichen Partition gespeichert. Dieser Mechanismus implementiert effektiv eine Strategie für die automatische Skalierungsstrategie. Jede Partition wird auf einem einzelnen Server in einem Azure-Datacentrum gespeichert (um sicherzustellen, dass Abfragen, die Daten aus einer einzigen Partition abrufen, schnell ausgeführt werden), aber unterschiedliche Partitionen können auf mehrere Server verteilt werden. Darüber hinaus kann ein einzelner Server mehrere Partitionen hosten, wenn diese Partitionen in ihrer Größe beschränkt sind.

Beim Entwerfen der Entitäten für Azure-Tabellenspeicher, sollten Sie die folgenden Punkte berücksichtigen:

- Die Auswahl der Partitions- und Zeilenschlüssel sollten auf die Weise gesteuert werden, in der auf die Daten zugegriffen wird. Wählen Sie eine Kombination für die Partitions-/Zeilenschlüssel, die den Großteil Ihrer Abfragen unterstützt. Die effizientesten Abfragen werden Daten durch Angeben des Partitionsschlüssels und des Zeilenschlüssels aufrufen. Abfragen, die einen Partitionsschlüssel und eine Reihe von Zeilenschlüsseln angeben, können durch Scannen einer einzelnen Partition erfüllt werden. Dies geht relativ schnell, da die Daten in der Reihenfolge des Zeilenschlüssels angeordnet sind. Abfragen, die nicht mindestens den Partitionsschlüssel angeben, erfordern Azure-Tabellenspeicher, um jede Partition für Ihre Daten zu scannen.

	> [AZURE.TIP]Wenn eine Entität einen natürlichen Schlüssel hat, dann verwenden Sie diesen als Partitionsschlüssel und geben Sie eine leere Zeichenfolge als Zeilenschlüssel an. Wenn eine Entität über einen zusammengesetzten Schlüssel aus zwei Eigenschaften verfügt, wählen Sie die Eigenschaft, die sich am langsamsten ändert, als Partitionsschlüssel und die andere als Zeilenschlüssel. Wenn eine Entität über mehr als zwei Schlüsseleigenschaften verfügt, verwenden Sie eine Verkettung der Eigenschaften, um die Partitions- und Zeilenschlüssel bereitzustellen.

- Wenn Sie regelmäßig Abfragen durchführen, die Daten mit anderen Feldern als den Partitions- und Zeilenschlüssel aufrufen, erwägen Sie das Implementieren der [Index-Tabellenmuster].
- Wenn Sie Partitionsschlüssel mithilfe einer monoton steigenden oder absteigenden Sequenz generieren (z. B. "0001", "0002", "0003",...) und jede Partition nur eine begrenzte Menge von Daten enthält, dann könnte Azure-Tabellenspeicher diese Partitionen zusammen auf demselben Server physisch gruppieren. Dieser Mechanismus geht davon aus, dass die Anwendung am ehesten Abfragen über einen zusammenhängenden Bereich von Partitionen (Bereichsabfragen) durchführen wird und ist für diesen Fall optimiert. Dieser Ansatz kann jedoch zu Hotspots mit Fokus auf einen einzelnen Server führen, da alle Einfügungen neuer Entitäten wahrscheinlich an einem oder dem anderen Ende der zusammenhängenden Bereiche konzentriert sind. Er kann auch die Skalierbarkeit verringern. Um die Last gleichmäßiger auf Server zu verteilen, sollten Sie ein Hashing des Partitionsschlüssels erwägen, um die Sequenz zufälliger zu gestalten.
- Azure-Tabellenspeicher unterstützt Transaktionsvorgänge für Entitäten, die zur selben Partition gehören. Dies bedeutet, dass eine Anwendung mehrere Einfüge-, Aktualisierungs-, Lösch-, Ersetzungs- oder Zusammenführungsvorgänge als atomare Einheit ausführen kann (vorausgesetzt die Transaktion umschließt nicht mehr als 100 Entitäten und die Nutzlast der Anforderung ist nicht größer als 4 MB). Vorgänge, die sich über mehrere Partitionen erstrecken, sind nicht transaktional und können erfordern, dass Sie gemäß der Beschreibung im Leitfaden für Datenkonsistenz letztendliche Konsistenz implementieren. Weitere Informationen zum Tabellenspeicher und Transaktionen finden Sie unter [Ausführen von Entitätsgruppentransaktionen] auf der Microsoft-Website.
- Achten Sie besonders auf die Granularität der Partitionsschlüssel:
	- Das Verwenden des gleichen Partitionsschlüssels für jede Entität führt dazu, dass der Tabellenspeicherdienst eine einzelne große Partition erstellt, die sich auf einem Server befindet und eine Skalierung verhindert, sodass stattdessen Last auf einem einzelnen Server konzentriert ist. Daher eignet sich diese Vorgehensweise nur für Systeme, die eine kleine Anzahl von Entitäten verwalten. Dieser Ansatz gewährleistet jedoch, dass alle Entitäten in die Entitätsgruppentransaktionen einbezogen werden können.
	- Verwenden eines eindeutigen Partitionsschlüssels für jede Entität führt dazu, dass der Tabellenspeicherdienst eine separate Partition für jede Entität erstellt, was möglicherweise zu einer großen Anzahl von kleinen Partitionen (abhängig von der Größe der Entitäten) führen kann. Dieser Ansatz ist skalierbarer als die Verwendung eines einzelnen Partitionsschlüssels, aber es sind keine Entitätsgruppentransaktionen möglich, und Abfragen, die mehr als eine Entität abrufen, können das Lesen aus mehr als einem Server umfassen. Wenn die Anwendung jedoch Bereichsabfragen ausführt, dann kann das Verwenden einer monotonen Sequenz, um die Partitionsschlüssel zu generieren, zur Optimierung dieser Abfragen beitragen.
	- Durch Nutzung eines einzelnen Partitionsschlüssels für eine Teilmenge von Entitäten können Sie verknüpfte Entitäten in derselben Partition gruppieren. Vorgänge mit verknüpften Entitäten können mithilfe von Entitätsgruppentransaktionen durchgeführt werden und Abfragen, die einen Satz verknüpfter Entitäten abfragen, können mit Zugriff auf einen einzelnen Server erfüllt werden.

Weitere Informationen zum Partitionieren von Daten im Azure-Tabellenspeicher finden Sie auf der Microsoft-Website unter [Azure-Speichertabelle – Entwurfshandbuch].

## Partitionierung des Azure-Blobspeichers

Azure Blob-Speicher ermöglicht es Ihnen, große binäre Objekte, derzeit bis zu 200 GB für Block-Blobs oder 1 TB für Seitenblobs, aufrechtzuerhalten (die neuesten Informationen finden Sie unter [Azure Storage-Skalierbarkeit und Leistungsziele] auf der Microsoft-Website). Verwenden Sie die Block-Blobs in Szenarien wie z. B. Streaming, in denen Sie große Datenmengen schnell hoch- oder herunterladen müssen. Verwenden Sie Seitenblobs für Anwendungen, die eher zufälligen als seriellen Zugriff auf Teile der Daten erfordern.

Jedes Blob (Block oder Seite), wird in einem Container in einem Azure-Speicherkonten aufrechterhalten. Sie können Container verwenden, um verwandte Blobs, die die gleichen Sicherheitsanforderungen haben, gemeinsam zu gruppieren, obwohl diese Gruppierung eher logisch statt physisch ist. In einem Container hat jedes Blob einen eindeutigen Namen.

Der Blob-Speicher wird automatisch basierend auf dem Blob-Namen partitioniert. Jedes Blob wird in einer eigenen Partition gehalten und Blobs in demselben Container teilen sich keine Partition. Diese Architektur ermöglicht Azure Blob-Speicher zum serverübergreifenden transparenten Lastenausgleich, da unterschiedliche Blobs in demselben Container auf verschiedene Server verteilt werden können.

Die Aktionen des Schreibens eines einzelnen Blocks (Block-Blob) oder einer Seite (Seitenblob) sind atomar, aber Vorgänge, die Blöcke, Seiten oder Blobs umfassen, hingegen nicht. Wenn Sie bei der Ausführung von Schreibvorgängen Konsistenz zwischen Blöcken, Seiten und Blobs gewährleisten müssen, werden Sie mithilfe eines Blob-Lease eine Schreibsperre ausführen müssen.

Azure-Blob-Speicher unterstützt Übertragungsraten von bis zu 60 MB pro Sekunde oder 500 Anforderungen pro Sekunde für jeden Blob. Wenn Sie erwarten, diese Grenzen zu überschreiten und die Blob-Daten relativ statisch sind, sollten Sie die Replikation von Blobs mithilfe von Azure Content Delivery Network (CDN) replizieren. Weitere Informationen finden Sie auf der Microsoft-Website unter [Verwenden von CDN für Azure]. Weitere Anleitungen und Überlegungen finden Sie im Artikel Content Delivery Network (CDN).

## Partitionierung von Azure-Speicherwarteschlangen

Azure-Speicherwarteschlangen ermöglichen Ihnen asynchrones Messaging zwischen Prozessen. Ein Azure-Speicherkonto kann eine beliebige Anzahl an Warteschlangen enthalten und jede Warteschlange kann eine beliebige Anzahl an Nachrichten enthalten. Die einzige Beschränkung ist der im Speicherkonto verfügbare Platz. Die maximale Größe einer einzelnen Nachricht beträgt 64 KB. Falls Sie größere Nachrichten erfordern, dann sollten Sie in Betracht ziehen, stattdessen Azure Service Bus-Warteschlangen zu verwenden.

Jede Speicherwarteschlange verfügt über einen eindeutigen Namen innerhalb des Speicherkontos, in dem sie enthalten ist. Azure partitioniert Warteschlangen basierend auf dem Namen und alle Nachrichten für dieselbe Warteschlange werden in derselben Partition gespeichert, die von einem einzelnen Server kontrolliert wird. Verschiedene Warteschlangen können von verschiedenen Servern verwaltet werden, um beim Lastausgleich zu helfen. Die Zuordnung der Warteschlangen zu Servern ist für Anwendungen und Benutzer transparent. Verwenden Sie in einer umfangreichen Anwendung nicht die gleiche Speicherwarteschlange für alle Instanzen der Anwendung, da durch diesen Ansatz der Server, der die Warteschlange hostet, zum Hotspot werden kann. Verwenden Sie verschiedene Warteschlangen für verschiedene Funktionsbereiche der Anwendung. Azure-Speicherwarteschlangen unterstützen keine Transaktionen, d.h. das Weiterleiten von Nachrichten an verschiedene Warteschlangen sollte nur eine geringe Auswirkung auf die Messaging-Konsistenz haben.

Eine Azure-Speicherwarteschlange kann bis zu 2000 Nachrichten pro Sekunde verarbeiten. Wenn Sie Nachrichten schneller verarbeiten müssen, sollten Sie das Erstellen mehrerer Warteschlangen in Betracht ziehen. Erstellen Sie z. B. in einer globalen Anwendung separate Speicherwarteschlangen in separaten Speicherkonten, um Anwendungsinstanzen zu verarbeiten, die in jeder Region ausgeführt werden.

## Partitionierungsstrategien für Azure-Service Bus

Azure-Service Bus verwendet Nachrichtenbroker, um Nachrichten zu verarbeiten, die an eine Service Bus-Warteschlange oder ein Thema gesendet wurden. Standardmäßig werden alle an eine Warteschlange oder ein Thema gesendeten Nachrichten mit dem gleichen Nachrichtenbroker-Prozess verarbeitet. Diese Architektur kann den Gesamtdurchsatz der Nachrichten-Warteschlange eingrenzen. Sie können jedoch auch eine Warteschlange oder ein Thema partitionieren, wenn sie/es erstellt wird, indem die Eigenschaft _EnablePartitioning_ der Warteschlangen- oder Themabeschreibung auf _true_ eingestellt wird. Eine partitionierte Warteschlange oder ein partitioniertes Thema ist in mehrere Fragmente unterteilt, von denen jedes durch einen separaten Nachrichtenspeicher und Nachrichtenbroker gesichert ist. Service Bus übernimmt die Verantwortung zum Erstellen und Verwalten dieser Fragmente. Wenn eine Anwendung eine Nachricht an eine partitionierte Warteschlange oder ein partitioniertes Thema postet, ordnet Service Bus die Nachricht einem Fragment für diese Warteschlange oder dieses Thema zu. Wenn eine Anwendung eine Nachricht von einer Warteschlange oder einem Abonnement empfängt, überprüft Service Bus jedes Fragment auf die nächste verfügbare Nachricht und leitet diese dann an die Anwendung zur Verarbeitung weiter. Diese Struktur hilft die Last zwischen Nachrichtenbrokern und Nachrichtenspeichern zu verteilen, wodurch die Skalierbarkeit erhöht und die Verfügbarkeit verbessert wird. Wenn der Nachrichtenbroker oder der Nachrichtenspeicher für ein Fragment vorübergehend nicht verfügbar ist, kann Service Bus die Nachrichten von einem der restlichen verfügbaren Fragmente abrufen.

Service Bus ordnet jeder Nachricht folgendermaßen ein Fragment zu:

- Wenn die Nachricht zu einer Sitzung gehört, werden alle Nachrichten mit dem gleichen Wert für die\_SessionID\_Eigenschaft an dasselbe Fragment gesendet.
- Wenn die Nachricht nicht zu einer Sitzung gehört, aber der Absender einen spezifischen Wert für die Eigenschaft _PartitionKey_ hat, dann werden alle Nachrichten mit demselben _PartitionKey_ an dasselbe Fragment gesendet.

	> [AZURE.NOTE]Wenn die beiden Eigenschaften _SessionId_ und _PartitionKey_ angegeben werden, müssen sie auf denselben Wert festgelegt werden, andernfalls wird die Nachricht zurückgewiesen.
- Wenn die Eigenschaften _SessionID_ und _PartitionKey_ für eine Nachricht nicht angegeben sind, aber Duplikaterkennung aktiviert ist, wird die Eigenschaft _MessageID_ verwendet. Alle Nachrichten mit derselben _MessageID_ werden an dasselbe Fragment geleitet.
- Wenn Nachrichten nicht die Eigenschaft _SessionID_ oder _PartitionKey_ umschließen, dann ordnet Service Bus die Nachrichten den Fragmenten auf Roundrobin-Weise zu. Wenn ein Fragment nicht verfügbar ist, geht Service Bus zum nächsten über. Auf diese Weise verursacht ein vorübergehender Fehler in der Nachrichteninfrastruktur kein Fehlschlagen des Nachrichtensendevorgangs.

Sie sollten die folgende Punkte in Betracht ziehen, wenn Sie entscheiden, ob und wie sie eine Service Bus-Nachrichtenwarteschlange oder ein Service Bus-Thema partitionieren:

- Service Bus-Warteschlangen und -Themen werden im Umfang des Service Bus-Namespace erstellt. Service Bus erlaubt derzeit bis 100 partitionierte Warteschlangen oder Themen pro Namespace.
- Jeder Service Bus-Namespace setzt Kontingente für die verfügbaren Ressourcen fest, wie die Anzahl der Abonnements pro Thema, die Anzahl gleichzeitiger Sende- und Empfangsanforderungen und die maximale Anzahl gleichzeitiger Verbindungen, die aufgebaut werden können. Diese Kontingente sind auf der Microsoft-Website unter [Service Bus-Kontingente] dokumentiert. Wenn Sie diese Werte überschreiten möchten, dann erstellen Sie zusätzliche Namespaces mit ihren eigenen Warteschlangen und Themen und verteilen Sie die Arbeit auf diese Namespaces. Erstellen Sie z. B. in einer globalen Anwendung separate Namespaces in jeder Region und konfigurieren Sie die Anwendungsinstanzen so, dass sie die Warteschlangen und Themen in nächstliegendem Namespace verwenden.
- Nachrichten, die als Teil einer Transaktion gesendet werden, müssen einen Partitionsschlüssel angeben. Das kann ein/e _SessionID, PartionKey_ oder _MessageID_ sein. Alle Nachrichten, die als Teil derselben Transaktion gesendet werden, müssen denselben Partitionsschlüssel angeben, da sie vom selben Nachrichtenbroker-Prozess verarbeitet werden müssen. Sie können keine Nachrichten an verschiedene Warteschlangen oder Themen innerhalb der gleichen Transaktion senden.
- Sie können keine partitionierte Warteschlange oder ein partitioniertes Thema so konfigurieren, dass automatisch eine Löschung vorgenommen wird, wenn sie/es sich im Leerlauf befindet.
- Wenn Sie plattformübergreifende oder Hybrid-Lösungen bauen, können Sie derzeit partitionierte Warteschlangen und Themen mit dem Advanced Message Queing Protocol (AMQP) verwenden.

## Partitionierungsstrategien für Azure-DocumentDB

Azure DocumentDB ist eine NoSQL-Datenbank, die Dokumente speichern kann. Ein Dokument in DocumentDB ist eine JSON-serialisierte Darstellung eines Objekts oder anderer Datensätze. Es werden keine festgelegten Schemen erzwungen, außer dass jedes Dokument über eine eindeutige ID verfügen muss.

Dokumente werden in Auflistungen organisiert. Mit einer Auflistung können Sie verwandte Dokumente gruppieren. Beispielsweise könnten Sie in einem System, dass Blogbeiträge verwaltet, den Inhalt eines jeden Blogbeitrags als Dokument in einer Auflistung speichern und Auflistungen für jeden Thementyp erstellen. Alternativ könnten Sie in einer mehrinstanzenfähigen Anwendung, wie ein System, das verschiedenen Autoren ermöglicht, ihre eigenen Blogbeiträge zu kontrollieren und zu verwalten, Blogs nach Autor partitionieren und für jeden Autor eine separate Auflistung erstellen. Der Speicherplatz, der Auflistungen zugeordnet ist, ist elastisch und kann je nach Bedarf schrumpfen oder wachsen.

Dokumentenauflistungen bieten einen natürlichen Mechanismus zum Partitionieren von Daten innerhalb einer Einzeldatenbank. Intern kann eine DocumentDB-Datenbank mehrere Server umspannen und DocumentDB versucht möglicherweise die Last auf mehrere Auflistungen zu verteilen. Der einfachste Weg Sharding zu implementieren, ist für jedes Shard eine Auflistung zu erstellen.

> [AZURE.NOTE]Jeder DocumentDB werden hinsichtlich einer _Leistungsebene_ Ressourcen zugeordnet. Jede Leistungsebene verfügt über eine Begrenzung der _Anforderungseinheit_ (RU)-Rate. Die RU-Ratenbegrenzung spezifiziert das Volumen der Ressourcen, die für diese Auflistung reserviert werden und für die ausschließliche Verwendung durch diese Auflistung zur Verfügung stehen. Die Kosten einer Auflistung hängen von der für diese Auflistung gewählten Leistungsebene ab. Je höher die Leistungsebene (und die RU-Ratenbegrenzung), umso höher sind die Gebühren. Sie können die Leistungsebene einer Auflistung mithilfe des Azure-Verwaltungsportals anpassen. Weitere Informationen finden Sie unter [Leistungsebenen in DocumentDB] auf der Microsoft-Website.

Alle Datenbanken werden im Kontext eines DocumentDB-Kontos erstellt. Ein einzelnes DocumentDB-Konto kann mehrere Datenbanken enthalten und gibt an, in welcher Region die Datenbanken erstellt werden. Jedes DocumentDB-Konto erzwingt auch seine eigene Zugriffskontrolle. Sie können DocumentDB-Konten zur Geolokalisierung von Shards (Auflistungen in Datenbanken) verwenden, die in der Nähe der Benutzer sind, die auf diese zugreifen müssen, und Enschränkungen setzen, sodass sich nur diese Benutzer mit ihnen verbinden können.

Jedes DocumentDB-Konto hat ein Kontingent, dass die Anzahl der Datenbanken und Auflistungen, die es enthalten kann, sowie die Menge des verfügbaren Dokumentenspeicherplatzes einschränken kann. Die Einschränkungen unterliegen Veränderungen, aber werden unter [DocumentDB-Einschränkungen und Kontingente] auf der Microsoft-Website beschrieben. Theoretisch ist es möglich, dass Sie bei der Implementierung eines Systems, in dem alle Shards derselben Datenbank angehören, die Speicherkapazitätsgrenze des Kontos erreichen. In diesem Fall müssen Sie wahrscheinlich zusätzliche DocumentDB-Konten einrichten und die Shards zwischen diesen Datenbanken verteilen. Doch selbst wenn es unwahrscheinlich ist, dass Sie die Speicherkapazität einer Datenbank erreichen, ist die Tatsache, dass jede Datenbank über ihren eigenen Satz an Benutzern und Berechtigungen verfügt, ein guter Grund, mehrere Datenbänke zu nutzen. Sie können diesen Mechanismus nutzen, um den Zugriff auf diese Auflistungen auf pro-Datenbank-Basis zu isolieren.

Abbildung 8 zeigt die Struktur auf oberster Ebene der DocumentDB-Architektur

![](media/best-practices-data-partitioning/DocumentDBStructure.png)

_Abbildung 8. - Die Struktur von DocumentDB_

Die Clientanwendung ist dafür verantwortlich, Anforderungen an das entsprechende Shard zu leiten, für gewöhnlich durch Implementieren seines eigenen Zuordnungsmechanismus, der auf einigen Attributen der Daten basiert, die das Shard definieren. Abbildung 9 zeigt zwei DocumentDB-Datenbänke, von denen jede zwei Auflistungen enthält, die als Shards agieren. Das Sharding der Daten erfolgt über die Mandanten-ID und die Daten enthalten die Daten für einen bestimmten Mandanten. Die Datenbanken werden in separaten DocumentDB-Konten erstellt, die sich in der gleichen Region wie die Mandanten befinden, deren Daten sie enthalten. Die Routinglogik in der Clientanwendung verwendet die Mandanten-ID als Shardschlüssel.

![](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_Abbildung 9. - Implementierung von Sharding mithilfe von Azure-DocumentDB_

Sie sollten folgende Punkte berücksichtigen, wenn Sie sich dazu entscheiden, Daten mit DocumentDB zu partitionieren:

- Die verfügbaren Ressourcen für eine DocumentDB-Datenbank unterliegen den Kontingentgrenzen für das DocumentDB-Konto. Jede Datenbank kann eine Reihe von Auflistungen enthalten (mit erneuter Beschränkung) und jeder Auflistung ist eine Leistungsebene zugeordnet, die die RU-Ratenbegrenzung (reservierter Durchsatz) für diese Sammlung regelt. Weitere Informationen finden Sie unter [DocumentDB-Grenzen und Kontingente] auf der Microsoft-Website.
- Jedes Dokument muss über ein Attribut verfügen, das verwendet werden kann, um das Dokument in der Auflistung, in der es enthalten ist, eindeutig zu identifizieren. Dies unterscheidet sich vom Shardschlüssel, der definiert, in welcher Auflistung das Dokument aufrechterhalten wird. Eine Auflistung kann eine große Anzahl von Dokumenten enthalten, die theoretisch nur durch die maximale Länge der Dokument-ID begrenzt ist. Die Dokument-ID kann bis zu 255 Zeichen lang sein.
- Alle Vorgänge für ein Dokument werden innerhalb des Kontexts einer Transaktion, die auf den Umfang einer Auflistung abgestimmt ist, in der das Dokument enthalten ist, ausgeführt. Wenn ein Vorgang fehlschlägt, wird die Arbeit, die durch ihn ausgeführt wurde, zurückgesetzt. Während ein Vorgang an einem Dokument ausgeführt wird, unterliegen alle vorgenommenen Änderungen der Snapshot-Isolation. Dieser Mechanismus gewährleistet, dass - wenn z. B. eine Anforderung zum Erstellen eines neuen Dokuments fehlschlägt - einem anderer Benutzer, der die Datenbank gleichzeitig abfragt, kein Teildokument angezeigt wird, das dann entfernt wird.
- DocumentDB-Abfragen werden auch auf Auflistungsebene begrenzt. Eine einzelne Abfrage kann nur Daten aus einer Auflistung abrufen. Wenn Sie Daten aus mehreren Auflistungen abrufen müssen, müssen Sie jede Auflistung einzeln abfragen und die Ergebnisse in Ihrem Anwendungscode zusammenzuführen.
- DocumentDB unterstützt programmierbare Elemente, die alle in einer Auflistung zusammen mit Dokumenten gespeichert werden können: gespeicherte Prozeduren, benutzerdefinierte Funktionen und Trigger (in JavaScript geschrieben). Diese Elemente können auf alle Dokumente in der gleichen Auflistung zugreifen. Außerdem führen diese Elemente Ausführungen innerhalb des Umfangs der Ambient-Transaktion durch (im Falles eines Triggers, der als Ergebnis eines Erstellungs-, Lösch- oder Ersetzungsvorgangs, der an einem Dokument ausgeführt wird, ausgelöst wird), oder indem sie eine neue Transaktion starten (bei einer gespeicherten Prozedur, die als Ergebnis einer expliziten Clientanforderung ausgeführt wird). Wenn der Code in einem programmierbaren Element eine Ausnahme auslöst, wird für die Transaktion ein Rollback ausgeführt. Sie können gespeicherte Prozeduren und Trigger verwenden, um die Integrität und Konsistenz zwischen den Dokumenten zu verwalten, aber diese Dokumente müssen alle derselben Auflistung angehören.
- Sie sollten sicherstellen, dass es für die Auflistungen, die Sie in den Datenbanken in einem DocumentDB-Konto halten möchten, unwahrscheinlich ist, die von den Leistungsebenen der Auflistungen definierten Durchsatzgrenzen zu überschreiten. Diese Grenzwerte werden unter [Verwaltung der DocumentDB-Kapazitätsanforderungen] auf der Microsoft-Website beschrieben. Wenn Sie davon ausgehen, diese Grenzwerte zu erreichen, sollten Sie in Betracht ziehen, die Auflistungen auf Datenbanken in verschiedenen DocumentDB-Konten zu verteilen, um die Last pro Auflistung zu verringern.

## Partitionierungsstrategien für Azure Search

Die Fähigkeit, Daten zu suchen, ist oftmals die von vielen Webanwendungen bereitgestellte primäre Methode für die Navigation und Untersuchung, die es Benutzern basierend auf einer Kombination von Suchkriterien ermöglicht, Ressourcen (z. B. Produkte in einer E-Commerce-Anwendung) schnell zu finden. Azure Search bietet Volltext-Suchfunktionen über Web-Inhalte und umfasst Funktionen wie z. B. Type-ahead, vorgeschlagene Abfragen basierend auf Übereinstimmungen und Facettennavigation. Eine vollständige Beschreibung dieser Funktionen finden Sie auf der Microsoft-Website unter [Was ist Azure Search?].

Der Suchdienst speichert durchsuchbaren Inhalt als JSON-Dokumente in einer Datenbank. Sie definieren Indizes, die die durchsuchbaren Felder in diesen Dokumenten angeben und diese Definitionen dem Suchdienst zur Verfügung stellen. Wenn ein Benutzer eine Suchanforderung sendet, verwendet der Suchdienst die entsprechenden Indizes, um übereinstimmende Elemente zu finden.

Um Konflikte zu reduzieren, kann der vom Suchdienst verwendete Speicher in bis zu 1, 2, 3, 4, 6 oder 12 Partitionen unterteilt werden, von denen jede bis zu sechs Mal repliziert werden kann. Das Produkt der Anzahl von Partitionen multipliziert mit der Anzahl der Replikate wird _Sucheinheit_ (SU) genannt. Eine einzelne Instanz des Suchdiensts kann maximal 36 SUs enthalten (eine Datenbank mit 12 Partitionen unterstützt nur maximal 3 Replikate). Jede SU, die Ihrem Dienst zugeordnet wird, wird Ihnen berechnet. Mit steigendem Volumen von durchsuchbarem Inhalt oder wachsender Rate der Suchanfragen, können Sie SUs einer vorhandene Instanz des Suchdiensts hinzufügen, um die zusätzliche Last zu bewältigen. Der Suchdienst selbst übernimmt die Verantwortung für die gleichmäßige Verteilung der Dokumente auf die Partitionen, und derzeit werden keine manuellen Partitionierungsstrategien unterstützt.

Jede Partition kann bis zu 15 Millionen Dokumente enthalten oder 300 GB Speicherplatz belegen (je nachdem was niedriger ausfällt, abhängig von der Größe Ihrer Dokumente und Indizes). Sie können bis zu 50 Indizes generieren. Die Leistung des Dienstes variiert je nach Komplexität der Dokumente, der verfügbaren Indizes und der Auswirkungen von Netzwerklatenz. Im Durchschnitt sollte ein einzelnes Replikat (1SU) 15 Abfragen pro Sekunde (QPS) verarbeiten können, obwohl Sie mit Ihren eigenen Daten Benchmarking durchführen sollten, um ein genaueres Maß für den Durchsatz zu erhalten. Weitere Informationen finden Sie auf der Microsoft-Website unter [Grenzwerte für den Azure Search-Dienst].

> [AZURE.NOTE]Sie können eine begrenzte Anzahl von Datentypen in durchsuchbaren Dokumenten speichern: Zeichenfolgen, Boolesche Werte, numerische Daten, Datetime-Daten und geografischen Daten. Weitere Informationen finden Sie unter [Unterstützte Datentypen (Azure Search)] auf der Microsoft-Website.

Sie haben nur begrenzte Kontrolle über die Weise, wie der Azure- Suchdienst Daten für jede Instanz des Diensts partitioniert. Allerdings könnten Sie womöglich in einer globalen Umgebung, die Leistung weiterhin verbessern sowie die Latenz und Konflikte verringern, indem Sie den Dienst selbst über einer der folgenden Strategien partitionieren:

- Erstellen Sie eine Instanz des Suchdiensts in jeder geografischen Region und stellen Sie sicher, dass Clientanwendungen an die nächste verfügbare Instanz geleitet werden. Diese Strategie erfordert, dass alle Aktualisierungen von durchsuchbarem Inhalt für alle Instanzen des Dienstes rechtzeitig repliziert werden.
- Erstellen Sie einen Zwei-Ebenen-Suchdienst: ein lokaler Dienst in jeder Region mit den Daten, auf die von Benutzern in dieser Region am häufigsten zugegriffen wird, und einen globalen Dienst, der alle Daten umfasst. Benutzer können die Anforderungen entweder an den lokalen Dienst (für schnelle, aber eingeschränkte Ergebnisse) oder an den globalen Dienst (für langsamere, aber vollständigere Ergebnisse) richten. Dieser Ansatz ist besonders geeignet, wenn bedeutende regionale Unterschiede in Bezug auf die gesuchten Daten vorhanden sind.

## Partitionierungsstrategien für Azure Redis Cache

Azure Redis Cache bietet einen Shared Caching Service in der Cloud, der auf dem Redis-Schlüssel-/Wert-Datenspeicher basiert. Wie der Name schon sagt, ist Azure Redis Cache als Caching-Lösung gedacht und sollte daher nur für vorübergehende Datenspeicherung und nicht als dauerhafter Datenspeicher genutzt werden. Anwendungen, die Azure Redis Cache nutzen, sollten auch weiterarbeiten können, wenn der Cache nicht verfügbar ist. Azure Redis Cache unterstützt die primäre/sekundäre Replikation zur Gewährleistung hoher Verfügbarkeit, aber derzeit ist die maximale Cachegröße auf 53 GB beschränkt. Wenn mehr Speicherplatz benötigt wird, müssen Sie zusätzliche Caches erstellen. Weitere Informationen finden Sie auf der Microsoft-Website unter [Azure Redis Cache].

Die Partitionierung eines Redis-Datenspeichers umfasst das Aufteilen von Daten über Instanzen des Redis-Diensts hinweg. Jede Instanz stellt eine einzelne Partition dar. Azure Redis Cache abstrahiert die Redis-Dienste hinter einer Fassade und zeigt sie nicht direkt an. Die einfachste Methode zum Implementieren der Partitionierung ist das Erstellen mehrerer Azure Redis-Caches, zwischen denen die Daten verteilt werden. Sie können jedem Datenelement einen Bezeichner (Partitionsschlüssel) zuordnen, der angibt, in welcher Cache es gespeichert werden soll. Die Logik der Clientanwendung kann diesen Bezeichner zum Weiterleiten von Anforderungen an die entsprechende Partition verwenden. Dieses Schema ist sehr einfach, aber wenn das Partitionierungsschema geändert wird (wenn z. B. zusätzliche Azure-Redis-Caches erstellt werden), müssen Client-Anwendungen möglicherweise neu konfiguriert werden.

Systemeigene Redis (nicht Azure Redis Cache) unterstützt die serverseitige Partitionierung basierend auf Redis-Clustering. Bei diesem Ansatz werden die Daten mit einem Hash-Mechanismus gleichmäßig auf die Server verteilt. Jeder Redis-Server speichert Metadaten, die den Bereich der Hash-Schlüssel beschreiben, die die Partition enthält, und enthält außerdem Informationen dazu, welche Hash-Schlüssel sich in den Partitionen auf anderen Servern befinden. Clientanwendungen senden Anforderungen einfach an einen der teilnehmenden Redis-Server (wahrscheinlich zum nächsten Server). Der Redis-Server überprüft die Clientanforderung, und führt - wenn es lokal gelöst werden kann - den angeforderten Vorgang aus. Andernfalls wird er die Anforderung an den entsprechenden Server weiterleiten. Dieses Modell wird mithilfe von Redis-Clustering implementiert und wird ausführlicher im [Redis-Cluster-Lernprogramm] auf der Redis-Website beschrieben. Redis-Clustering ist für Clientanwendungen transparent und dem Cluster können zusätzliche Redis-Server hinzugefügt (und die Daten neu partitioniert) werden, ohne die Clients neu zu konfigurieren.

> [AZURE.IMPORTANT]Azure Redis Cache unterstützt derzeit kein Redis-Clustering. Wenn Sie diesen Ansatz mit Azure implementieren möchten, müssen Sie Ihre eigenen Redis-Server implementieren, indem Sie Redis auf einem Satz von virtuellen Azure-Computern installieren und diese manuell konfigurieren. Ein Beispiel für die Erstellung und Konfiguration eines Redis-Knoten, der als virtueller Azure-Computer ausgeführt wird, finden Sie unter [Ausführen von Redis auf einem virtuellem CentOS Linux Computer in Azure] auf der Microsoft-Website.

Die Seite [Partitionierung: das Teilen von Daten zwischen mehreren Redis-Instanzen] auf die Redis-Website enthält weitere Informationen zur Implementierung der Partitionierung mit Redis. Im restlichen Abschnitt wird davon ausgegangen, dass Sie clientseitige oder Proxy-unterstützte Partitionierung implementieren.

Sie sollten folgende Punkte berücksichtigen, wenn Sie sich dazu entscheiden, Daten mit Azure Redis Cache zu partitionieren:

- Azure Redis Cache ist nicht als dauerhafter Datenspeicher gedacht, d.h. unabhängig des Partitionierungsschemas, das Sie implementieren, sollte Ihr Anwendungscode darauf vorbereitet sein, zu akzeptieren, dass die Daten nicht im Cache gefunden werden und an anderer Stelle aufgerufen werden müssen.
- Halten Sie Daten, auf die häufig zugegriffen werden, in derselben Partition. Redis ist ein leistungsfähiger Schlüssel-/Wert-Speicher, der mehrere stark optimierte Mechanismen zum Strukturieren von Daten bereitstellt, angefangen bei einfachen Zeichenfolgen (tatsächlich Binärdaten bis zu einer Länge von 512 MB) bis zu aggregierten Typen, wie z. B. Listen (die als Warteschlangen und Stapel verwendet werden können), Sätzen (sortiert und unsortiert) und Hashs (die verknüpften Felder gruppieren können, wie z. B. die Elemente, die die Felder in einem Objekt darstellen). Mit den aggregierten Typen können Sie viele verknüpfte Werte dem gleichen Schlüssel zuordnen. Ein Redis-Schlüssel identifiziert eine Liste, einen Satz oder Hash anstatt die Datenelemente, die es enthält. Diese Typen sind alle mit Azure Redis Cache verfügbar und unter [Datentypen] auf der Redis-Website beschrieben. Beispielsweise könnten in dem Teil eines E-Commerce-Systems, das die Bestellungen von Kunden verfolgt, die Details der einzelnen Kunden in einem Redis-Hash sortiert nach Kunden-ID gespeichert werden. Jeder Hash könnte eine Auflistung von Bestellnummern für den jeweiligen Kunden enthalten. Ein separater Redis-Satz könnte die Aufträge halten, die wieder als Hashs strukturiert und nach Bestellnummer sortiert sind. Abbildung 10 zeigt eine solche Struktur. Beachten Sie, dass Redis keine Form der referenziellen Integrität implementiert, daher ist es die Verantwortung des Entwicklers, die Beziehungen zwischen Kunden und Bestellungen zu verwalten.

![](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_Abbildung 10. - Vorgeschlagene Struktur im Redis-Speicher für die Aufzeichnung von Bestellungen und ihren Details_

> [AZURE.NOTE]In Redis sind alle Schlüssel Binärdaten-Werte (wie Redis-Zeichenfolgen) und können bis zu 512 MB Daten enthalten, d.h. theoretisch kann ein Schlüssel nahezu jede Information enthalten. Sie sollten jedoch eine konsistente Benennungskonvention für Schlüssel übernehmen, die den Datentyp beschreibt und die Entität identifiziert, aber nicht übermäßig lang ist. Eine gängige Methode ist die Verwendung von Schlüsseln im Format "Entity\_type:ID", wie z. B. "Customer: 99", um den Schlüssel für den Kunden mit der ID 99 anzugeben.

- Sie können die vertikale Partitionierung durch das Speichern von verwandten Informationen in anderen Aggregationen in derselben Datenbank implementieren. Z.B. könnten Sie in einer E-Commerce-Anwendung häufig abgerufene Informationen zu Produkten in einem Redis-Hash speichern und weniger häufig verwendete Detail-Informationen in einer anderen. Beide Hashs können die gleiche Produkt-ID als Teil des Schlüssels nutzen, z. B. "Produkt: _nn_", wenn _nn_ die Produkt-ID für Produktinformationen ist und "Product\_details: _nn_" für detailliertere Daten. Diese Strategie kann helfen, die Datenmenge zu reduzieren, die die meisten Abfragen wahrscheinlich abrufen werden.
- Das Neupartitionieren von einem Redis-Datenspeicher ist eine komplexe und zeitaufwändige Aufgabe. Redis-Clustering kann Daten automatisch neu partitionieren, aber diese Funktion ist mit Azure Redis Cache nicht verfügbar. Daher sollten Sie beim Entwerfen Ihres Partitionierungsschemas versuchen, ausreichend freien Speicherplatz in jeder Partition zu behalten, um langfristig den erwarteten Datenzuwachs zu ermöglichen. Denken Sie jedoch daran, dass Azure Redis Cache zur vorübergehenden Zwischenspeicherung von Daten vorgesehen ist, und dass im Cache gehaltene Daten eine begrenzte Lebensdauer haben, die als Time-to-live (TTL) Wert angegeben wird. Für relativ flüchtige Daten sollte der TTL-Wert kurz sein, für statische Daten kann der TTL-Wert jedoch sehr viel länger sein. Vermeiden Sie die Speicherung großer Mengen langlebiger Daten im Cache, wenn die Datenmenge wahrscheinlich den kompletten Cache belegt. Sie können eine Entfernungsrichtlinie angeben, die bewirkt, dass Azure Redis Cache die Daten entfernt, wenn der Speicherplatz knapp bemessen ist.

	> [AZURE.NOTE]Azure Redis Cache ermöglicht Ihnen durch Auswahl der entsprechenden Preisstufe, die maximale Größe des Caches (von 250 MB bis 53 GB) anzugeben. Sie können diesen jedoch, nachdem ein Azure Redis Cache erstellt wurde, nicht vergrößern (oder verkleinern).

- Redis-Batches und Transaktionen können nicht mehrere Verbindungen umfassen, d.h. alle Daten, die von einem Batch oder einer Transaktion betroffenen sind, sollten in der gleichen Datenbank (Shard) gespeichert werden.

	> [AZURE.NOTE]Eine Abfolge von Operationen in einer Redis-Transaktion ist nicht unbedingt atomar. Die Befehle, die eine Transaktion bilden, werden vor der Ausführung überprüft und in die Warteschlange gereiht. Tritt während dieser Phase ein Fehler auf, wird die gesamte Warteschlange verworfen. Sobald die Transaktion jedoch erfolgreich übermittelt wurde, werden die Befehle in der Warteschlange der Reihenfolge nach ausgeführt. Schlägt ein Befehl fehl, wird nur dieser Befehl abgebrochen. Alle vorherigen und nachfolgenden Befehle in der Warteschlange werden ausgeführt. Wenn Sie atomare Operationen ausführen müssen. Weitere Informationen finden Sie unter [Redis-Sicherheit] auf der Redis-Website.

- Redis unterstützt eine begrenzte Anzahl von atomaren Vorgängen, und die einzigen Vorgänge dieses Typs, die mehrere Schlüssel und Werte unterstützen, sind MGET (das eine Auflistung von Werten für eine angegebene Liste von Schlüsseln zurückgibt) und MSET (das eine Auflistung von Werten für eine angegebene Liste von Schlüsseln speichern kann). Wenn Sie diese Vorgänge verwenden müssen, müssen die Schlüssel-/Wertpaare, auf die durch die MSET- und MGET-Befehle verwiesen wird, innerhalb derselben Datenbank gespeichert werden.

## Neuverteilen von Partitionen

Mit zunehmender Reife eines Systems und einem besseren Verständnis seines Verwendungsmusters ist es möglich, dass eine Anpassung des Partitionierungsschemas notwendig ist. Ein Grund dafür könnte sein, dass die einzelnen Partitionen ein unverhältnismäßig großes Datenverkehrsvolumen anziehen und aktiv werden, was zu übermäßigen Konflikten führt. Darüber hinaus haben Sie möglicherweise die Datenmenge in einigen Partitionen unterschätzt, wodurch nun die die Grenzen der Speicherkapazität in diesen Partitionen ausgeschöpft werden müssen. Unabhängig von den Ursachen, ist es manchmal notwendig, die Partitionen für einen ausgewogeneren Lastausgleich neu zu verteilen.

In einigen Fällen können Datenspeichersysteme, die nicht öffentlich anzeigen, wie Daten den Servern zugeordnet werden, automatisch Partitionen innerhalb der Grenzen der verfügbaren Ressourcen neu verteilen. In anderen Situationen ist das Neuverteilen eine administrative Aufgabe, die aus zwei Schritten besteht:

1. Bestimmen der neuen Partitionierungsstrategie, um zu ermitteln, welche Partitionen womöglich aufgeteilt (oder kombiniert) werden müssen und wie Daten diesen neuen Partitionen zuzuweisen sind, indem Sie neue Partitionsschlüssel entwerfen.
2. Migrieren der betroffenen Daten vom alten Partitionierungsschema in den neuen Satz von Partitionen.

> [AZURE.NOTE]Die Zuordnung von DocumentDB- Auflistungen zu Servern ist transparent, aber Sie erreichen möglicherweise immer noch die Grenzen der Speicherkapazität und des Durchsatzes eines DocumentDB-Kontos. In diesem Fall müssen Sie Ihr Partitionierungsschema ändern und die Daten migrieren.

Je nach Datenspeicherungstechnologie und Entwurf Ihres Datenpeichersystems ist möglicherweise die Migration von Daten zwischen Partitionen möglich, währen diese verwendet werden (Online-Migration). Wenn dies nicht möglich ist, müssen Sie die Verfügbarkeit der betroffenen Partitionen vorübergehend aufheben, während die Daten ausgelagert werden (Offline-Migration).

## Offline-Migration

Offline-Migration ist wohl der einfachste Ansatz, da er die Wahrscheinlichkeit verringert, dass Konflikte auftreten. Die zu migrierenden Daten sollten nicht geändert werden, während sie verschoben und umstrukturiert werden.

Dieser Prozess umfasst im Prinzip die folgenden Schritte:

1. Offline-Markierung des Shards
2. Teilen/Zusammenführen der Daten und Verschieben in die neuen Shards
3. Überprüfen der Daten
4. Online-Stellen der neuen Shards
5. Entfernen des alten Shards

Um ein wenig Verfügbarkeit beizubehalten, könnte es möglich sein, das ursprüngliche Shard in Schritt 1 als schreibgeschützt zu markieren, anstatt seine Verfügbarkeit aufzuheben. Das würde Anwendungen ermöglichen, die Daten zu lesen, während sie verschoben werden, sie jedoch nicht zu ändern.

## Online-Migration

Online-Migration ist komplizierter auszuführen, doch für Benutzer weniger störend, da die Daten während des gesamten Vorgangs verfügbar bleiben. Der Prozess ähnelt dem der Offline-Migration, außer, dass das ursprüngliche Shard nicht als offline (Schritt 1) markiert ist. Je nach Granularität des Migrationsvorgangs (Element für Element oder Shard für Shard) könnte es sein, dass der Datenzugriffscode in den Clientanwendungen Daten an zwei Orten (das ursprüngliche Shard und das neue Shard) lesen und schreiben muss.

Ein Beispiel für eine Lösung mit Online-Migrationsunterstützung finden Sie auf der Microsoft-Website unter [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken].

## Zugehörige Muster und Anleitungen

Die folgenden Muster können auch für Ihr Szenario relevant sein, wenn Sie Strategien zum Implementieren der Konsistenz der Daten in Betracht ziehen:

- Die [Einführung in die Datenkonsistenz] auf der Microsoft-Website beschreibt Strategien für die Gewährleistung konsistenter Daten in einer verteilten Umgebung (etwa in der Cloud).
- Die[Anleitung zur Datenpartitionierung ] auf der Microsoft-Website bietet eine allgemeine Übersicht über das Entwerfen von Partitionen, um in einer verteilten Lösung zahlreiche Kriterien zu erfüllen.
- Unter [Sharding-Muster], die auf der Microsoft-Website beschrieben werden, sind einige allgemeine Strategien für das Sharding von Daten zusammengefasst.
- Die [Index-Tabellenmuster], die auf der Microsoft-Website beschrieben werden, zeigen, wie Sie sekundäre Indizes für Daten erstellen können. Mit diesem Ansatz kann eine Anwendung Daten durch Abfragen, die nicht auf den Primärschlüssel einer Auflistung verweisen, schnell abrufen.
- Das [Materialisierte Sichtmuster], das auf der Microsoft-Website besprochen wird, beschreibt, wie Sie voreingestellte Ansichten generieren können, die Daten zusammenfassen, um schnelle Abfragevorgänge zu unterstützen. Dieser Ansatz kann in einem partitionierten Datenspeicher hilfreich sein, wenn die Partitionen, die die Daten enthalten, die zusammengefasst werden, über mehrere Standorte verteilt sind.
- Der Artikel [Verwenden von CDN für Azure] bietet eine zusätzliche Anleitung zur Konfiguration und Verwendung von CDN mit Azure.

## Weitere Informationen

- Unter [Was ist SQL Database?] auf der Microsoft-Website finden Sie ausführliche Informationen zum Erstellen und Verwenden von SQL-Datenbanken.
- Die Seite [Übersicht über Features für elastische Datenbanken] auf der Microsoft-Website bietet eine umfassende Einführung in elastische Datenbanken.
- Das Thema [Skalierung mit dem Split-Merge-Tool für elastische Datenbanken] auf der Microsoft-Website enthält Informationen zur Verwaltung elastischer Datenbanken mithilfe des Split/Merge-Diensts.
- [Azure Storage-Skalierbarkeit und Leistungsziele](https://msdn.microsoft.com/library/azure/dn249410.aspx) auf der Microsoft-Website dokumentiert die aktuelle Größen- und Durchsatzgrenzen vom Azure-Speicher.
- Der Abschnitt [Ausführen von Entitätsgruppentransaktionen] auf der Microsoft-Website enthält detaillierte Informationen zur Implementierung der Transaktionsvorgänge über Entitäten, die in Azure-Tabellenspeicher gespeichert sind.
- Der Artikel [Azure-Speichertabelle – Entwurfshandbuch] auf der Microsoft-Website enthält ausführliche Informationen zum Partitionieren von Daten im Azure-Tabellenspeicher.
- Unter [Verwenden des CDNS für Azure] auf der Microsoft Website wird beschrieben, wie Daten in Azure Blob-Speicher mithilfe von Azure Content Delivery Network (CDN) repliziert werden können.
- Der Artikel [Verwalten von DocumentDB-Kapazitätsanforderungen] auf der Microsoft-Website enthält Informationen dazu, wie Azure-DocumentDB den Datenbanken Ressourcen zuordnet.
- Eine vollständige Beschreibung der Möglichkeiten des Azure Search-Diensts finden Sie auf der Microsoft-Website unter [Was ist Azure Search?].
- Der Artikel [Grenzwerte für den Azure Search-Dienst] auf der Microsoft-Website enthält Informationen zur Kapazität der einzelnen Instanzen des Azure Search-Diensts.
- Unter [Unterstützte Datentypen (Azure Search)] auf der Microsoft-Website sind die Datentypen zusammengefasst, die Sie in durchsuchbaren Dokumenten und Indizes verwenden können.
- Der Artikel [Azure Redis Cache] auf der Microsoft-Website bietet eine Einführung in Azure Redis Cache.
- Unter[Partitioning: das Trennen von Daten unter mehreren Redis-Instanzen]auf der Redis-Website finden Sie weitere Informationen zur Implementierung der Partitionierung mit Redis.
- Ein Beispiel für die Erstellung und Konfiguration eines Redis-Knoten, der als virtueller Azure-Computer ausgeführt wird, finden Sie unter [Ausführen von Redis auf einem virtuellem CentOS Linux Computer in Azure] auf der Microsoft-Website.
- Unter [Datentypen] auf der Redis-Website werden die Datentypen beschrieben, die mit Redis und Azure Redis Cache verfügbar sind.

[Azure Redis Cache]: http://azure.microsoft.com/services/cache/
[Azure Storage-Skalierbarkeit und Leistungsziele]: storage/storage-scalability-targets.md
[Azure-Speichertabelle – Entwurfshandbuch]: storage/storage-table-design-guide.md
[Erstellen einer Polyglot-Lösung]: https://msdn.microsoft.com/library/dn313279.aspx
[Datenzugriff für hoch skalierbare Lösungen: mit SQL, NoSQL und Polyglot Persistence]: https://msdn.microsoft.com/library/dn271399.aspx
[Einführung in die Datenkonsistenz]: http://aka.ms/Data-Consistency-Primer
[Anleitung zur Datenpartitionierung ]: https://msdn.microsoft.com/library/dn589795.aspx
[Datentypen]: http://redis.io/topics/data-types
[DocumentDB-Einschränkungen und Kontingente]: documentdb/documentdb-limits.md
[DocumentDB-Grenzen und Kontingente]: documentdb/documentdb-limits.md
[Übersicht über Features für elastische Datenbanken]: sql-database/sql-database-elastic-scale-introduction.md
[Verbund-Migrationsdienstprogramms]: https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1
[Index-Tabellenmuster]: http://aka.ms/Index-Table-Pattern
[Verwalten von DocumentDB-Kapazitätsanforderungen]: documentdb/documentdb-manage.md
[Verwaltung der DocumentDB-Kapazitätsanforderungen]: documentdb/documentdb-manage.md
[Materialisierte Sichtmuster]: http://aka.ms/Materialized-View-Pattern
[Multi-Shard-Abfrage]: sql-database/sql-database-elastic-scale-multishard-querying.md
[Partitionierung: das Teilen von Daten zwischen mehreren Redis-Instanzen]: http://redis.io/topics/partitioning
[Partitioning: das Trennen von Daten unter mehreren Redis-Instanzen]: http://redis.io/topics/partitioning
[Leistungsebenen in DocumentDB]: documentdb/documentdb-performance-levels.md
[Ausführen von Entitätsgruppentransaktionen]: https://msdn.microsoft.com/library/azure/dd894038.aspx
[Redis-Cluster-Lernprogramm]: http://redis.io/topics/cluster-tutorial
[Ausführen von Redis auf einem virtuellem CentOS Linux Computer in Azure]: http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx
[Skalierung mit dem Split-Merge-Tool für elastische Datenbanken]: sql-database/sql-database-elastic-scale-overview-split-and-merge.md
[Verwenden des CDNS für Azure]: cdn/cdn-how-to-use-cdn.md
[Verwenden von CDN für Azure]: cdn/cdn-how-to-use-cdn.md
[Service Bus-Kontingente]: service-bus/service-bus-quotas.md
[Grenzwerte für den Azure Search-Dienst]: search/search-limits-quotas-capacity.md
[Sharding-Muster]: http://aka.ms/Sharding-Pattern
[Unterstützte Datentypen (Azure Search)]: https://msdn.microsoft.com/library/azure/dn798938.aspx
[Redis-Sicherheit]: http://redis.io/topics/transactions
[Was ist Azure Search?]: search/search-what-is-azure-search.md
[Was ist SQL Database?]: sql-database/sql-database-technical-overview.md

<!---HONumber=AcomDC_1223_2015-->