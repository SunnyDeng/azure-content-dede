<properties
   pageTitle="Ausführen von Elasticsearch unter Azure | Microsoft Azure"
   description="Es wird beschrieben, wie Sie Elasticsearch unter Azure installieren, konfigurieren und ausführen."
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
   ms.date="02/05/2016"
   ms.author="mabsimms"/>

# Ausführen von Elasticsearch für Azure

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch-introduction.md).

## Übersicht

Elasticsearch ist ein hochgradig skalierbares Open-Source-Suchmodul mit Datenbank. Es ist für Situationen geeignet, in denen eine schnelle Analyse und Ermittlung von Informationen in großen Datasets erforderlich ist. Zu den häufigen Szenarios gehören:

- Umfassende Freitextsuche, bei der Dokumente, für die sich bei Kombinationen von Suchbegriffen Übereinstimmungen ergeben, schnell gefunden und abgerufen werden können.
- Ereignisprotokollierung, wobei Informationen aus unterschiedlichen Quellen stammen können. Die Daten müssen unter Umständen analysiert werden, um sicherzustellen, wie eine Ereigniskette zu einer bestimmten Schlussfolgerung geführt hat.
- Speicherung von Daten, die von Remotegeräten und anderen Quellen erfasst wurden. Die Daten können unterschiedliche Informationen enthalten. Eine häufige Anforderung besteht aber darin, diese Informationen in mehreren Dashboards darzustellen, damit ein Bediener den Zustand des gesamten Systems überblicken kann. Von Anwendungen können die Informationen auch genutzt werden, um schnelle Entscheidungen zum Fluss der Daten und zu den geschäftlichen Vorgängen zu treffen, die sich daraus ergeben und durchgeführt werden müssen.
- Bestandskontrolle, bei der Änderungen des Bestands als „verkaufte Waren“ aufgezeichnet werden. Für Geschäftssysteme können diese Informationen verwendet werden, um Benutzern Bestände zu melden und Waren nachzubestellen, wenn die Bestandsmenge eines Produkts niedrig ist. Analysten können die Daten auf Trends untersuchen, um besser ermitteln zu können, welche Produkte sich unter welchen Umständen besser verkaufen.
- Finanzanalyse, bei der Marktinformationen nahezu in Echtzeit eintreffen. Es können Dashboards generiert werden, mit denen die minutengenaue Performance verschiedener Finanzinstrumente angezeigt wird, und diese Informationen können dann zum Treffen von Kauf- bzw. Verkaufsentscheidungen genutzt werden.

Dieses Dokument enthält eine kurze Einführung in die allgemeine Struktur von Elasticsearch. Anschließend wird beschrieben, wie Sie mit Azure einen Elasticsearch-Cluster implementieren können. Im Dokument liegt der Schwerpunkt auf den bewährten Methoden zum Bereitstellen eines Elasticsearch-Clusters und den verschiedenen Anforderungen Ihres Systems an die Funktionsleistung und Verwaltung. Es wird beschrieben, wie Ihre Anforderungen die von Ihnen gewählte Konfiguration und Topologie beeinflussen sollten.

> [AZURE.NOTE] Hierbei wird vorausgesetzt, dass Sie über Grundkenntnisse in Bezug auf [Elasticsearch][] verfügen.

## Aufbau von Elasticsearch

Elasticsearch ist eine Dokumentdatenbank, die stark für die Nutzung als Suchmaschine optimiert ist. Dokumente werden im JSON-Format serialisiert. Daten werden in Indizes vorgehalten, die per [Apache Lucene][] implementiert werden. Die Details befinden sich aber im Hintergrund, und für die Nutzung von Elasticsearch sind keine umfassenden Lucene-Kenntnisse erforderlich.

### Cluster, Knoten, Indizes und Shards

Elasticsearch implementiert eine Clusterarchitektur, bei der das Sharding zum Verteilen von Daten auf mehrere Knoten und die Replikation zur Sicherstellung der hohen Verfügbarkeit eingesetzt werden.

Dokumente werden in Indizes gespeichert. Der Benutzer kann angeben, welche Felder eines Dokuments zur eindeutigen Identifizierung in einem Index verwendet werden, oder das System kann ein Schlüsselfeld und Werte automatisch erstellen. Der Index wird verwendet, um Dokumente physisch zu organisieren, und er wird als wichtigstes Mittel zum Auffinden von Dokumenten genutzt. Außerdem erstellt Elasticsearch automatisch einen Satz mit zusätzlichen Strukturen, die als invertierte Indizes für die verbleibenden Felder dienen, um schnelle Suchen zu ermöglichen und in einer Sammlung Analysen durchzuführen.

Ein Index besteht aus einer Gruppe von Shards. Dokumente werden gleichmäßig auf Shards verteilt, indem ein Hashmechanismus verwendet wird, der auf den Indexschlüsselwerten und der Anzahl der Shards im Index basiert. Nachdem ein Dokument einem Shard zugeordnet wurde, bleibt es dem Shard erhalten, es sei denn, der Indexschlüssel wird geändert. Elasticsearch verteilt Shards auf alle verfügbaren Knoten in einem Cluster. Ein einzelner Knoten kann anfänglich eine oder mehrere Shards enthalten, die zu demselben Index gehören. Wenn dem Cluster aber neue Knoten hinzugefügt werden, ordnet Elasticsearch die Shards neu an, um die gleichmäßige Auslastung des Systems sicherzustellen. Die gleiche Neuanordnung wird auch durchgeführt, wenn Knoten entfernt werden.

Indizes können repliziert werden. In diesem Fall wird jedes Shard in den Index kopiert. Elasticsearch sorgt dafür, dass sich jedes ursprüngliche Shard für einen Index (als „primäres Shard“ bezeichnet) und das dazugehörige Replikat immer auf unterschiedlichen Knoten befinden.

> [AZURE.NOTE] Die Anzahl der Shards in einem Index kann nicht ohne Weiteres geändert werden, nachdem der Index erstellt wurde, aber es können Replikate hinzugefügt werden.

Wenn ein Dokument hinzugefügt oder geändert wird, werden alle Schreibvorgänge zuerst auf dem primären Shard und dann jeweils auf den Replikaten durchgeführt. Standardmäßig läuft dieser Vorgang synchron ab, um die Konsistenz sicherzustellen. Elasticsearch verwendet beim Schreiben von Daten die optimistische Parallelität mit Versionskontrolle. Lesevorgänge können durchgeführt werden, indem entweder das primäre Shard oder eines seiner Replikate verwendet wird.

In Abbildung 1 sind die wesentlichen Aspekte eines Elasticsearch-Clusters mit drei Knoten dargestellt. Es wurde ein Index erstellt, der aus zwei primären Shards mit zwei Replikaten für jedes Shard besteht (insgesamt sechs Shards).

![](media/guidance-elasticsearch-general-cluster1.png)

**Abbildung 1.**
Einfacher Elasticsearch-Cluster mit zwei primären Knoten und zwei Replikatsätzen

In diesem Cluster befinden sich das primäre Shard 1 und das primäre Shard 2 auf separaten Knoten, um die Last gleichmäßig zu verteilen. Die Replikate werden auf ähnliche Weise verteilt. Wenn eine einzelner Knoten ausfällt, verfügen die verbleibenden Knoten über genügend Informationen, damit das System weiterhin funktioniert. Falls erforderlich, macht Elasticsearch ein Replikat-Shard zu einem primären Shard, wenn das entsprechende primäre Shard nicht verfügbar ist.
Zu Beginn der Ausführung eines Knotens kann entweder ein neuer Cluster initiiert (wenn es der erste Knoten im Cluster ist) oder der Beitritt zu einem vorhandenen Cluster durchgeführt werden. Der Cluster, zu dem ein Knoten gehört, wird anhand der Einstellung *cluster.name* in der Datei „elasticsearch.yml“ bestimmt.

### Knotenrollen

Die Knoten in einem Elasticsearch-Cluster können die folgenden Rollen übernehmen:

- Datenknoten, der ein oder mehrere Shards mit Indexdaten aufweisen kann.
- Clientknoten ohne Indexdaten, der aber eingehende Anforderungen von Clientanwendungen an den entsprechenden Datenknoten verarbeitet.
- Masterknoten ohne Indexdaten, der Vorgänge für die Clusterverwaltung übernimmt, z. B. Verwalten und Verteilen von Routinginformationen im Cluster (Liste mit Knoten und den darauf enthaltenen Shards), Ermitteln der Verfügbarkeit von Knoten, Neuanordnen von Shards beim Hinzufügen/Entfernen von Knoten und Koordinieren der Wiederherstellung nach einem Knotenausfall. Mehrere Knoten können als Master konfiguriert werden, aber es wird nur ein Knoten für die Masterfunktionen ausgewählt. Wenn dieser Knoten ausfällt, wird eine andere Wahl getroffen. Einer der anderen passenden Masterknoten wird ausgewählt und übernimmt diese Aufgabe.

Standardmäßig übernehmen Elasticsearch-Knoten alle drei Rollen (damit Sie einen vollständigen funktionierenden Cluster auf einem Computer für die Entwicklung oder für Machbarkeitsstudien erstellen können), aber Sie können die Abläufe über die Einstellungen *node.data* und *node.master* in der Datei *elasticsearch.yml* wie folgt ändern:

```yaml
node.data: true
node.master: false
```

**Konfiguration für einen Datenknoten**

```yaml
node.data: false
node.master: false
```

**Konfiguration für einen Clientknoten**

```yaml
node.data: false
node.master: true
```

**Konfiguration für einen Masterknoten**

> [AZURE.NOTE] Der ausgewählte Masterknoten ist für die Integrität des Clusters von entscheidender Bedeutung. Die anderen Knoten senden regelmäßig Ping-Nachrichten an den Masterknoten, um sicherzustellen, dass er verfügbar ist. Falls der ausgewählte Masterknoten auch als Datenknoten fungiert, weist der Knoten unter Umständen eine hohe Auslastung auf und reagiert nicht mehr auf die Ping-Nachrichten. In diesem Fall wird der Masterknoten als ausgefallen angesehen, und einer der anderen Masterknoten wird an seiner Stelle ausgewählt. Falls der ursprüngliche Master weiterhin verfügbar ist, kann sich ein Cluster mit zwei ausgewählten Masterknoten ergeben. Dies kann zu einem Split-Brain-Problem führen, was eine Beschädigung der Daten und andere Probleme nach sich ziehen kann. Im Dokument „Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery“ (Konfigurieren, Testen und Analysieren der Elasticsearch-Resilienz und -Wiederherstellung) wird beschrieben, wie Sie den Cluster konfigurieren sollten, um diesen Fall nach Möglichkeit zu verhindern. Letztendlich ist es aber eine gute Strategie, für einen mittleren bis großen Cluster dedizierte Masterknoten zu verwenden, die nicht für die Verwaltung von Daten zuständig sind.

Die Knoten in einem Cluster geben Informationen zu den anderen Knoten im Cluster (per [Gossip-Protokoll][]) und zu den darin enthaltenen Shards weiter. Clientanwendungen, die Daten speichern und abrufen, können eine Verbindung mit einem beliebigen Knoten in einem Cluster herstellen. Die Anforderungen werden auf transparente Weise an den richtigen Knoten geleitet. Wenn eine Clientanwendung Daten vom Cluster anfordert, ist der Knoten, der die Anforderung zuerst erhält, für Folgendes zuständig: das Leiten des Vorgangs, das Kommunizieren mit den einzelnen relevanten Knoten, um die Daten abzurufen, und dann das Zusammenstellen des Ergebnisses, bevor die Antwort an die Clientanwendung erfolgt. Durch die Verwendung von Clientknoten zum Verarbeiten von Anforderungen werden Datenknoten von der Durchführung dieser „Scatter-Gather-Aufgaben“ befreit und können sich ganz der Bereitstellung von Daten widmen. Sie können verhindern, dass Clientanwendungen versehentlich mit Datenknoten kommunizieren (und somit als Clientknoten fungieren), indem Sie den HTTP-Transport für die Datenknoten deaktivieren:

```yaml
http.enabled: false
```

Datenknoten können weiterhin mit anderen Datenknoten, Clientknoten und dedizierten Masterknoten in demselben Netzwerk kommunizieren, indem sie das Elasticsearch-Transportmodul nutzen (bei dem TCP-Sockets zum Herstellen einer direkten Verbindung zwischen Knoten verwendet werden). Clientanwendungen können aber nur per HTTP eine Verbindung mit Clientknoten herstellen. In Abbildung 2 ist eine Topologie mit einer Mischung aus dedizierten Master-, Client- und Datenknoten in einem Elasticsearch-Cluster dargestellt.

![](media/guidance-elasticsearch-general-cluster2.png)

**Abbildung 2.**
Elasticsearch-Cluster mit verschiedenen Knotentypen

### Vor- und Nachteile der Verwendung von Clientknoten

Wenn eine Anwendung eine Abfrage an einen Elasticsearch-Cluster sendet, ist der Knoten, mit dem die Anwendung eine Verbindung herstellt, für die Leitung des Abfrageprozesses zuständig. Der Knoten leitet die Anforderung an die einzelnen Datenknoten weiter, sammelt die Ergebnisse und gibt die zusammengefassten Informationen an die Anwendung zurück. Wenn eine Abfrage Aggregationen und andere Berechnungen umfasst, führt der Knoten, mit dem die Anwendung die Verbindung herstellt, die erforderlichen Vorgänge nach dem Abrufen der Daten von den anderen Knoten durch. Bei diesem Scatter-Gather-Prozess werden unter Umständen Verarbeitungs- und Arbeitsspeicherressourcen in erheblichem Umfang verbraucht.

Die Verwendung dedizierter Clientknoten für diese Aufgaben ermöglicht es den Datenknoten, sich auf die Verwaltung und Speicherung von Daten zu konzentrieren. Viele Szenarien mit komplexen Abfragen und Aggregationen können daher von der Nutzung dedizierter Clientknoten profitieren. Die Auswirkungen des Einsatzes von dedizierten Clientknoten variieren aber in der Regel je nach Szenario, Workload und Clustergröße. Beispielsweise sind Datenerfassungsworkloads unter Umständen weniger effizient, wenn Clientknoten verwendet werden, da beim Speichern von Daten ein zusätzlicher Netzwerkhop erforderlich ist. Wenn das System bei einem Cluster mit drei Knoten und sechs Shards nicht mit dedizierten Clientknoten konfiguriert ist und alle Umgebungsfaktoren und Knotenladevorgänge gleich sind, gilt Folgendes: Es besteht eine Wahrscheinlichkeit von ca. 33 %, dass eine Anwendung beim Speichern oder Ändern von Daten eine direkte Verbindung mit dem am besten geeigneten Shard herstellt. Also ist es in einem Drittel aller Fälle nicht erforderlich, einen weiteren Netzwerksprung durchzuführen. Andererseits können Workloads, die komplexe Aggregationen durchführen, vom Einsatz dedizierter Clients profitieren. Der Grund ist, dass ein einzelner Knoten jeweils für die Scatter-Gather-Vorgänge zuständig ist, die von diesen Vorgängen durchgeführt werden. In einer gemischten Umgebung sollten Sie Leistungstests durchführen, um die Auswirkung des Einsatzes von Clientknoten auf Ihre jeweiligen Workloads bewerten zu können.

> [AZURE.NOTE] Im Dokument „Maximizing Data Aggregation and Query Performance with Elasticsearch on Azure“ (Maximieren der Datenaggregations- und Abfrageleistung mit Elasticsearch unter Azure) sind verschiedene Benchmark-Tests zusammengefasst, die vom Microsoft Patterns & Practices Development Team teilweise zu diesem Zweck durchgeführt wurden.

### Herstellen der Verbindung mit einem Cluster

Elasticsearch macht eine Reihe von REST-APIs zum Erstellen von Clientanwendungen und Senden von Anforderungen an einen Cluster verfügbar. Wenn Sie Anwendungen per .NET Framework entwickeln, sind APIs für die höhere Ebene verfügbar: [Elasticsearch.Net und NEST][].

Wenn Sie Clientanwendungen mit Java erstellen, können Sie die [Node-Client-API][] verwenden, um Clientknoten dynamisch zu erstellen und dem Cluster hinzuzufügen. Das dynamische Erstellen von Clientknoten ist einfach, wenn Sie in Ihrem System eine relativ geringe Anzahl von langlebigen Verbindungen verwenden. Clientknoten, die mit der Node-API erstellt werden, werden vom Masterknoten anhand der Zuordnung für das Routing zwischen Clustern bereitgestellt (enthält Details dazu, welche Knoten welche Shards enthalten). Mit diesen Informationen kann die Java-Anwendung eine direkte Verbindung mit den geeigneten Knoten herstellen, wenn Daten indiziert oder abgefragt werden. So wird die Anzahl der Hops reduziert, die bei Verwendung anderer APIs unter Umständen benötigt werden. Der Kostenfaktor dieses Ansatzes ist der Mehraufwand für die Registrierung des Clientknotens im Cluster. Falls eine große Zahl von Clientknoten schnell auftaucht und wieder verschwindet, kann die Verwaltung und Verteilung der Zuordnung für das Routing zwischen Clustern mit erheblichen Auswirkungen verbunden sein.

**Lastenausgleich für Verbindungen**

Elasticsearch ermöglicht mehrere Mechanismen für die Implementierung des Lastenausgleichs für Verbindungen. In der folgenden Liste sind einige häufige Ansätze zusammengefasst:

**Clientbasierter Lastenausgleich**: Wenn Sie Clientanwendungen mit der Elasticsearch.Net- oder NEST-API erstellen, können Sie einen Verbindungspool verwenden, um Verbindungsanforderungen per Roundrobin-Vorgang auf Knoten zu verteilen. So ist der Lastenausgleich für Anforderungen möglich, ohne dass ein externer Load Balancer eingesetzt werden muss. Der folgende Codeausschnitt zeigt, wie Sie ein *ElasticsearchClient*-Objekt erstellen, für das Adressen von drei Knoten konfiguriert sind. Anforderungen von der Clientanwendung werden auf diese Knoten verteilt:

```csharp
// C#
var node1 = new Uri("http://node1.example.com:9200");
var node2 = new Uri("http://node2.example.com:9200");
var node3 = new Uri("http://node3.example.com:9200");

var connectionPool = new SniffingConnectionPool(new[] {node1, node2, node3});
var config = new ConnectionConfiguration(connectionPool);
var client = new ElasticsearchClient(config);
```

> [AZURE.NOTE] Ähnliche Funktionen sind für Java-Anwendungen über die [Transport-Client-API][] verfügbar.

**Serverbasierter Lastenausgleich**: Sie können einen separaten Load Balancer zum Verteilen von Anforderungen auf Knoten verwenden. Dieser Ansatz hat den Vorteil der Transparenz von Adressen. Clientanwendungen müssen nicht mit den Details der einzelnen Knoten konfiguriert werden, sodass es vereinfacht wird, Knoten hinzuzufügen, zu entfernen oder neu anzuordnen, ohne Clientcode zu ändern. Abbildung 3 zeigt eine Konfiguration, bei der ein Load Balancer zum Weiterleiten von Anforderungen an einen Satz mit Clientknoten verwendet wird. Die gleiche Strategie kann auch zum Herstellen einer direkten Verbindung mit Datenknoten verwendet werden, falls keine Clientknoten genutzt werden.

> [AZURE.NOTE] Sie können den [Azure Load Balancer][] verwenden, um den Cluster für das öffentliche Internet verfügbar zu machen. Oder Sie können einen [internen Load Balancer][] verwenden, wenn sich die Clientanwendungen und der Cluster vollständig in demselben privaten Virtual Network (VNET) befinden.

![](media/guidance-elasticsearch-general-clientappinstances.png)

**Abbildung 3.**
Verbindung von Clientanwendungsinstanzen mit einem Elasticsearch-Cluster über den Azure Load Balancer

**Benutzerdefinierter Lastenausgleich**: Sie können [nginx][] als Reverseproxyserver anstelle von Azure Load Balancer verwenden. „nginx“ bietet mehrere Methoden für den Lastenausgleich, z. B. Roundrobin, „wenigste Verbindungen“ (eine Anforderung wird an das Ziel mit den derzeit wenigsten Verbindungen geleitet) und Hashing basierend auf der IP-Adresse des Clients.

> [AZURE.NOTE] Sie können einen nginx-Server als Azure-VM bereitstellen. Zum Sicherstellen der Verfügbarkeit sollten Sie mindestens zwei nginx-Server in derselben Azure-Verfügbarkeitsgruppe erstellen.

Sie sollten die folgenden Punkte berücksichtigen, wenn Sie ermitteln, ob der Lastenausgleich verwendet und welche Implementierung genutzt werden soll:

- Wenn eine Verbindung mit demselben Knoten zum Verarbeiten aller Anforderungen für alle Instanzen einer Anwendung hergestellt wird, kann dies dazu führen, dass der Knoten zu einem Engpass wird. Falls die Anzahl der im Knoten verfügbaren Threads erschöpft ist, werden Anforderungen in die Warteschlange eingereiht und ggf. verworfen, falls diese zu lang wird. (Vermeiden Sie das Hartcodieren der Verbindungsdetails eines einzelnen Knotens im Anwendungscode, der für viele Benutzer bereitgestellt wird.)
- Der Roundrobin-Mechanismus der Elasticsearch.Net-, NEST- und Transport-Client-APIs behandelt fehlgeschlagene Verbindungsanforderungen, indem die Verbindungsherstellung für den nächsten verfügbaren Knoten im Verbindungspool erneut versucht wird. Eine Verbindung mit einem nicht reagierenden Knoten im Pool kann vorübergehend als *tot* (dead) gekennzeichnet werden. Sie kann später wieder reaktiviert werden, und der Pool kann eine Ping-Nachricht an den Knoten senden, um zu ermitteln, ob er wieder aktiv ist.
- Der Azure Load Balancer kann Anforderungen auf transparente Weise basierend auf verschiedenen Faktoren (Client-IP-Adresse, Clientport, Ziel-IP-Adresse, Zielport, Protokolltyp) an Knoten umleiten. Im Rahmen dieser Strategie wird eine Instanz einer Clientanwendung, die auf einem bestimmten Computer ausgeführt wird, mit großer Wahrscheinlichkeit an denselben Elasticsearch-Knoten geleitet. Wenn der Elasticsearch-Dienst auf diesem Knoten fehlschlägt, die VM selbst aber weiter ausgeführt wird, tritt für alle Verbindungen mit diesem Knoten je nach Testkonfiguration des Load Balancers eine Zeitüberschreitung auf. Verbindungen von anderen Clientinstanzen mit anderen Knoten können dagegen ggf. weiter normal funktionieren.
- Der Azure Load Balancer kann so konfiguriert werden, dass ein Knoten aus der Rotation herausgenommen wird, falls er nicht richtig auf Integritätstestanforderungen antwortet, die vom Load Balancer durchgeführt werden.

### Knotenermittlung

Da Elasticsearch auf Peer-to-Peer-Kommunikation basiert, ist die Ermittlung anderer Knoten eines Clusters ein wichtiger Teil des Lebenszyklus eines Knotens. Die Knotenermittlung ermöglicht das dynamische Hinzufügen neuer Datenknoten zu einem Cluster, sodass der Cluster wiederum das horizontale Hochskalieren transparent durchführen kann. Wenn ein Datenknoten nicht auf Kommunikationsanforderungen von anderen Knoten reagiert, kann ein Masterknoten außerdem entscheiden, dass der Datenknoten ausgefallen ist. Er kann dann die erforderlichen Schritte ausführen, um die darauf enthaltenen Shards auf andere betriebsbereite Datenknoten umzuverteilen.

Die Elasticsearch-Knotenermittlung wird mit einem Ermittlungsmodul durchgeführt. Das Ermittlungsmodul ist ein Plug-In, das zur Verwendung eines anderen Ermittlungsmechanismus umgeschaltet werden kann. Das standardmäßige Ermittlungsmodul ([Zen][]) veranlasst einen Knoten zum Ausgeben von Ping-Anforderungen, um andere Knoten in demselben Netzwerk zu finden. Wenn andere Knoten antworten, führen sie das „Gossiping“ durch, um Informationen auszutauschen. Ein Masterknoten kann dann Shards auf den neuen Knoten verteilen (sofern es ein Datenknoten ist) und die Neuanordnung für den Cluster durchführen.

Das Zen-Ermittlungsmodul übernimmt auch die Auswahl des Masters und den Protokollvorgang für die Erkennung des Knotenausfalls.

Vor Elasticsearch Version 2.0 wurde für das Zen-Ermittlungsmodul die Multicast-Kommunikation verwendet, um es Knoten zu ermöglichen, miteinander Kontakt aufzunehmen. Dies erleichtert das Einführen eines neuen Knotens in einem Cluster sehr, aber es kann auch zu Sicherheitsproblemen führen, wenn für eine andere Installation von Elasticsearch in demselben Netzwerk der gleiche Clustername verwendet wird. Die neue Installation wird als Teil desselben Clusters angesehen, und Shards werden ggf. an Knoten in dieser Installation geleitet. Außerdem werden Multicast-Nachrichten nicht unterstützt, wenn Sie Elasticsearch-Knoten als virtuelle Azure-Maschinen (VMs) ausführen. Aus diesen Gründen sollten Sie die Zen-Ermittlung so konfigurieren, dass Unicast-Nachrichten verwendet werden, und in der Konfigurationsdatei „elasticsearch.yml“ eine Liste mit gültigen Kontaktknoten angeben.

> [AZURE.NOTE] Bei Elasticsearch 2.0 und höher wird „Multicast“ nicht mehr als standardmäßiger Ermittlungsmechanismus verwendet.

Wenn Sie einen Elasticsearch-Cluster in einem Azure VNET hosten, können Sie angeben, dass die private und per DHCP zugewiesene IP-Adresse der einzelnen VMs im Cluster zugewiesen bleiben soll (statisch). Sie können die Unicast-Nachrichten für die Zen-Ermittlung mit diesen statischen IP-Adressen konfigurieren. Wenn Sie VMs mit dynamischen IP-Adressen verwenden, sollten Sie Folgendes beachten: Wenn eine VM gestoppt und neu gestartet wird, wird ihr unter Umständen eine neue IP-Adresse zugeordnet. Dies kann die Ermittlung erschweren. Zur Bewältigung dieses Szenarios können Sie das Zen-Ermittlungsmodul gegen das [Azure-Cloud-Plug-In][] austauschen. Für dieses Plug-In wird die Azure-API zum Implementieren des Ermittlungsmechanismus verwendet (basierend auf Azure-Abonnementinformationen).

> [AZURE.NOTE] Für die aktuelle Version des Azure-Cloud-Plug-Ins ist es erforderlich, dass Sie das Verwaltungszertifikat für Ihr Azure-Abonnement im Java-Schlüsselspeicher auf dem Elasticsearch-Knoten installieren und den Speicherort und die Anmeldeinformationen für den Zugriff auf den Schlüsselspeicher in der Datei „elasticsearch.yml“ angeben. Diese Datei wird als Klartext vorgehalten. Es ist also sehr wichtig sicherzustellen, dass auf diese Datei nur mit dem Konto zugegriffen werden kann, auf dem der Elasticsearch-Dienst ausgeführt wird. Außerdem ist dieser Ansatz unter Umständen nicht mit Azure-Ressourcen-Manager-Bereitstellungen (ARM) kompatibel. Daher wird empfohlen, statische IP-Adressen für Masterknoten zu verwenden und diese Knoten zum Implementieren von Unicast-Nachrichten für die Zen-Ermittlung im gesamten Cluster einzusetzen. In der folgenden Konfiguration (aus der Datei „elasticsearch.yml“ für einen Beispieldatenknoten) verweisen die Host-IP-Adressen auf Masterknoten im Cluster:

>`discovery.zen.ping.multicast.enabled: false`  
`discovery.zen.ping.unicast.hosts: ["10.0.0.10","10.0.0.11","10.0.0.12"]`

## Allgemeine Systemrichtlinien

Elasticsearch kann auf vielen verschiedenen Computern ausgeführt werden – von einem einzelnen Laptop bis zu einem Cluster mit Highend-Servern. Aber je mehr Ressourcen in Bezug auf Arbeitsspeicher, Rechenleistung und schnelle Datenträger verfügbar sind, desto höher ist die Leistung. In den folgenden Abschnitten sind die grundlegenden Hardware- und Softwareanforderungen für die Ausführung von Elasticsearch zusammengefasst.

### Anforderungen an den Arbeitsspeicher
Elasticsearch versucht, Daten aus Geschwindigkeitsgründen speicherintern zu speichern. Ein Produktionsserver, der einen Knoten für ein typisches Unternehmen oder für eine kommerzielle Bereitstellung moderater Größe unter Azure hostet, sollte zwischen 14 GB und 28 GB Arbeitsspeicher (D3- oder D4-VMs) haben. Verteilen Sie die Last auf weitere Knoten, anstatt Knoten mit mehr Arbeitsspeicher zu erstellen. (Experimente haben gezeigt, dass die Verwendung größerer Knoten mit mehr Arbeitsspeicher bei einem Ausfall zu längeren Wiederherstellungszeiten führen kann.) Auch wenn die Erstellung von Clustern mit einer sehr großen Anzahl kleiner Knoten die Verfügbarkeit und den Durchsatz erhöhen kann, wird dadurch auch der Aufwand für die Verwaltung und Pflege eines Systems dieser Art gesteigert.

Ordnen Sie 50 % des verfügbaren Arbeitsspeichers auf einem Server dem Elasticsearch-Heap zu. Legen Sie die Umgebungsvariable ES\_HEAP\_SIZE bei Nutzung von Linux fest, bevor Sie Elasticsearch ausführen. Falls Sie Windows oder Linux verwenden, können Sie alternativ dazu die Arbeitsspeichergröße unter den Parametern *Xmx* und *Xms* angeben, wenn Sie Elasticseach starten. Legen Sie beide Parameter auf den gleichen Wert fest, um zu verhindern, dass die Java Virtual Machine (JVM) die Größe des Heaps zur Laufzeit ändert. Ordnen Sie aber nicht mehr als 30 GB zu. Verwenden Sie den restlichen Arbeitsspeicher für den Dateicache des Betriebssystems.

> [AZURE.NOTE] Elasticsearch nutzt die Lucene-Bibliothek zum Erstellen und Verwalten von Indizes. Für Lucene-Strukturen wird ein datenträgerbasiertes Format verwendet, und das Zwischenspeichern dieser Strukturen im Dateisystemcache führt zu einer deutlichen Leistungssteigerung.

> Außerdem wurde Elasticsearch in Java geschrieben. Die maximale optimale Heapgröße für Java auf einem 64-Bit-Computer liegt etwas über 30 GB. Oberhalb dieser Größe wechselt Java zur Verwendung eines erweiterten Mechanismus zum Verweisen auf Objekte im Heap. Dies führt dazu, dass die Arbeitsspeicheranforderungen für jedes Objekt zunehmen und die Leistung sinkt. Die Leistung des standardmäßigen Java Garbage Collectors (gleichzeitiges Markieren und Löschen) ist ggf. auch nicht optimal, wenn die Größe des Heaps über 30 GB liegt. Es wird derzeit nicht empfohlen, zu einem anderen Garbage Collector zu wechseln, da Elasticsearch und Lucene nur mit der Standardeinstellung getestet wurden.

Ordnen Sie nicht übermäßig viel Arbeitsspeicher zu, da das Auslagern von Hauptspeicher auf den Datenträger zu einer erheblichen Beeinträchtigung der Leistung führt. Deaktivieren Sie die Auslagerung nach Möglichkeit vollständig (Details hängen jeweils vom Betriebssystem ab). Falls dies nicht möglich ist, sollten Sie die Einstellung *mlockall* in der Elasticsearch-Konfigurationsdatei (elasticsearch.yml) wie folgt aktivieren:

```yaml
bootstrap.mlockall: true
```

Diese Konfigurationseinstellung bewirkt, dass die JVM ihren Arbeitsspeicher sperrt, und es wird verhindert, dass er vom Betriebssystem ausgelagert wird.

### Datenträger- und Dateisystemanforderungen

Verwenden Sie Solid State Drives (SSD) zum Speichern von Shards. Die Größe der Datenträger sollte so bemessen werden, dass sie die maximale Anzahl von Daten aufnehmen können, die für Ihre Shards erwartet werden. Es ist aber möglich, später weitere Datenträger hinzuzufügen. Sie können ein Shard auf mehrere Datenträger eines Knotens erweitern.

> [AZURE.NOTE] Elasticsearch komprimiert die Daten für gespeicherte Felder mit dem LZ4-Algorithmus, und ab Version 2.0 von Elasticsearch können Sie den Komprimierungstyp ändern. Sie können den Komprimierungsalgorithmus in DEFLATE ändern, der auch von den Hilfsprogrammen *zip* und *gzip* verwendet wird. Diese Komprimierungstechnik kann ressourcenintensiver sein, aber Sie sollten den Einsatz für weniger häufig genutzte Indizes erwägen, z. B. archivierte Protokolldaten. Dieser Ansatz kann ein Beitrag zur Reduzierung der Indexgröße sein.

Es ist nicht entscheidend, dass alle Knoten in einem Cluster das gleiche Datenträgerlayout und die gleiche Kapazität aufweisen. Ein Knoten mit einer sehr großen Datenträgerkapazität zieht im Vergleich zu anderen Knoten in einem Cluster aber mehr Daten an und benötigt zum Behandeln dieser Daten eine höhere Verarbeitungsleistung. Als Folge davon kann der Knoten gegenüber anderen Knoten eine hohe Auslastung aufweisen, und dies kann sich wiederum negativ auf die Leistung auswirken.

> [AZURE.NOTE] Azure bietet mehrere Optionen für die Datenträgerspeicherung, z. B. Standardspeicher, Premium-Speicher und flüchtigen Speicher. Standardspeicher basiert auf rotierenden Medien, während für Premium-Speicher SSDs verwendet werden. Je nach SKU, die zum Implementieren einer VM verwendet wird, kann flüchtiger Speicher in Form von rotierenden Medien (VMs der A-Reihe) oder SSDs (VMs der D-Reihe und höher) implementiert werden. Sie müssen jeweils den finanziellen und technischen Aufwand der Speicheroptionen gegeneinander abwägen. Hierauf wird im Dokument „Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure“ ausführlicher eingegangen.

Verwenden Sie nach Möglichkeit RAID 0 (Striping). Andere Formen von RAID mit Implementierung von Parität und Spiegelung sind nicht notwendig. Elasticsearch stellt eine eigene Lösung für hohe Verfügbarkeit in Form von Replikaten bereit, und bei Azure-Datenträgern werden immer drei Kopien der Datenträgerdaten gespeichert.

> [AZURE.NOTE] Vor Version 2.0.0 von Elasticsearch konnten Sie das Striping auch auf der Softwareebene implementieren, indem Sie unter der Konfigurationseinstellung *path.data* mehrere Verzeichnisse angegeben haben. In Elasticsearch 2.0.0 wird diese Form von Striping nicht mehr unterstützt. Stattdessen können unterschiedliche Shards unterschiedlichen Pfaden zugeordnet werden, aber alle Dateien eines einzelnen Shards werden in denselben Pfad geschrieben. Falls Sie das Striping benötigen, sollten Sie das Striping für Daten auf Betriebssystem- oder Hardwareebene durchführen.

Beachten Sie auch, dass der E/A-Gesamtdurchsatz für Azure-Datenträger, die an eine VM angefügt sind, durch die Speichergruppe begrenzt wird, der diese angehören. Ein einzelnes Standardspeicherkonto kann eine Anforderungsrate von bis zu 20.000 IOPS verarbeiten (diese Beschränkung gilt nicht für Premium-Speicher). Falls dieser Wert kleiner als Ihr erwarteter E/A-Datenverkehr für Datenträger ist, können Sie die Datenträger für die VMs in Ihrem Cluster auf mehrere Speicherkonten verteilen. Denken Sie hierbei daran, dass für ein Abonnement bis zu 100 Speicherkonten erstellt werden können.

Die Lucene-Bibliothek kann eine große Anzahl von Dateien zum Speichern von Indexdaten verwenden, und Elasticsearch kann eine beträchtliche Anzahl von Sockets für die Kommunikation zwischen Knoten und mit Clients öffnen. Stellen Sie sicher, dass das Betriebssystem für die Unterstützung einer angemessenen Anzahl von Deskriptoren für offene Dateien konfiguriert ist (bis zu 64.000, falls ausreichend Arbeitsspeicher verfügbar ist). Beachten Sie, dass die Anzahl von Deskriptoren für offene Dateien in der Standardkonfiguration vieler Linux-Distributionen auf 1024 beschränkt ist. Dieser Wert ist viel zu niedrig.

Für Elasticsearch wird eine Kombination aus im Speicher abgebildeter E/A (mmap) und Java New IO (NIO) verwendet, um den gleichzeitigen Zugriff auf Datendateien und Indizes zu optimieren. Wenn Sie Linux verwenden, sollten Sie das Betriebssystem wie folgt konfigurieren: Stellen Sie sicher, dass ausreichend virtueller Arbeitsspeicher mit Platz für 256K-Bereiche für die Speicherabbildung verfügbar ist.

> [AZURE.NOTE] Für viele Linux-Distributionen wird standardmäßig der Scheduler „Completely Fair Queuing (CFQ)“ genutzt, wenn das Schreiben von Daten auf Datenträger vorbereitet wird. Dieser Scheduler ist nicht für SSDs optimiert. Erwägen Sie, für das Betriebssystem entweder die Verwendung des NOOP-Schedulers oder Deadline-Schedulers zu konfigurieren. Beide Scheduler sind für SSDs effektiver.

### CPU-Anforderungen

Verwenden Sie CPUs mit mehreren Kernen. Geben Sie mehr Kernen gegenüber schnelleren CPUs mit weniger Kernen den Vorzug. Der Grund ist, dass die Standardgröße von Elasticsearch-Threadpools basierend auf der Anzahl von verfügbaren CPU-Kernen konfiguriert wird. Die von Elasticsearch verwendeten Algorithmen werden anhand dieser Berechnungen optimiert, und es wird empfohlen, die standardmäßigen Threadpool-Einstellungen von Elasticsearch nicht zu ändern.

> [AZURE.NOTE] Azure VMs sind in vielen verschiedenen CPU-Konfigurationen verfügbar, und es werden zwischen 1 und 32 Kernen unterstützt. Für einen Datenknoten besteht ein guter Ausgangspunkt darin, eine standardmäßige VM der D-Reihe zu verwenden und entweder die D3-SKU (4 Kerne) oder D4-SKU (8 Kerne) zu wählen. D3 verfügt über 14 GB Arbeitsspeicher, und D4 bietet 28 GB. Falls Sie Premium-Datenträgerspeicher benötigen, können Sie eine VM der DS-Reihe verwenden (z. B. eine DS3- oder DS4-Maschine). Die Dv2-Reihe bietet dagegen Intel Xeon-Prozessoren mit 2,4 GHz und höherer Leistung, wenn die CPU-Leistung bei Ihnen voraussichtlich ein kritischer Faktor in Bezug auf die Begrenzung des Durchsatzes ist. Für die G-Reihe (für Standardspeicher) und GS-Reihe (für Premium-Speicher) werden Xeon E5 V3-Prozessoren eingesetzt. Dies kann für Workloads hilfreich sein, die sehr rechenintensiv sind, z. B. große Aggregationen. Aktuelle Informationen erhalten Sie unter [Größen für Virtual Machines][].

### Netzwerkanforderungen

Für Elasticsearch ist eine Netzwerkbandbreite erforderlich, die zwischen 1 und 10 GBit/s liegt. Dies hängt von der Größe und Volatilität des implementierten Clusters ab. Elasticsearch migriert Shards zwischen Knoten, wenn dem Cluster mehr Knoten hinzugefügt werden. Elasticsearch geht davon aus, dass die Kommunikationszeit zwischen allen Knoten in etwa gleich ist. Die relativen Speicherorte von Shards auf diesen Knoten werden nicht berücksichtigt. Darüber hinaus kann die Replikation mit erheblichem Netzwerk-E/A-Aufwand zwischen Shards verbunden sein. Vermeiden Sie daher die Erstellung von Clustern auf Knoten, die geografisch weit verteilt sind.

Beachten Sie, dass die Netzwerkbandbreite in Azure VMs nicht nur durch die SKU beschränkt wird, sondern auch durch die Netzwerk-Gesamtauslastung der physischen Hardware, auf der die VMs ausgeführt werden. VMs, die Hardware gemeinsam nutzen, haben eine geringere Bandbreite als VMs, die auf dedizierter Hardware ausgeführt werden, aber ihre Bereitstellung ist kostengünstiger. Dies bedeutet, dass Sie über die Auswahl der Reihe und SKU für Ihre VMs hinaus nur eine geringe Kontrolle über die verfügbare Netzwerkbandbreite haben. Beispielsweise können Sie zwar VMs mit mehreren virtuellen Netzwerkschnittstellen erstellen, aber dadurch wird die verfügbare Gesamtbandbreite für Ihre VMs nicht erhöht. Es konkurrieren lediglich mehr virtuelle Netzwerkschnittstellen um dieselben physischen Netzwerkressourcen.

### Softwareanforderungen

Sie können Elasticsearch unter Windows oder Linux ausführen. Der Elasticsearch-Dienst wird als Java-JAR-Bibliothek bereitgestellt und verfügt über Abhängigkeiten von anderen Java-Bibliotheken, die im Elasticsearch-Paket enthalten sind. Sie müssen die Java 7- (Update 55 oder höher) oder die Java 8-JVM (Update 20 oder höher) installieren, um Elasticsearch auszuführen.

> [AZURE.NOTE] Ändern Sie die standardmäßigen JVM-Konfigurationseinstellungen nicht, mit Ausnahme der Arbeitsspeicherparameter *Xmx* und *Xms* (Angabe als Befehlszeilenoptionen für das Elasticsearch-Modul, siehe [Arbeitsspeicheranforderungen][]). Elasticsearch wurde mit den Standardeinstellungen entworfen. Wenn sie geändert werden, kann dies dazu führen, dass Elasticsearch nicht mehr optimal eingestellt ist und eine schlechte Leistung aufweist.

### Bereitstellen von Elasticsearch unter Azure

Es ist nicht schwierig, eine einzelne Instanz von Elasticsearch bereitzustellen, aber das Erstellen von mehreren Knoten und das Installieren und Konfigurieren von Elasticsearch auf jedem Knoten kann ein zeitaufwändiger und fehleranfälliger Prozess sein. Wenn Sie die Ausführung von Elasticsearch auf Azure VMs erwägen, haben Sie zwei Möglichkeiten, um die Wahrscheinlichkeit von Fehlern zu reduzieren.
- Verwenden Sie die [Azure-Ressourcen-Manager-Vorlage (ARM)](http://azure.microsoft.com/documentation/templates/elasticsearch/) zum Erstellen des Clusters. Diese Vorlage ist vollständig parametrisiert, damit Sie die Größe und Leistungsebene für die VMs, die die Knoten implementieren, die Anzahl der zu verwendenden Datenträger und andere gängige Faktoren angeben können. Mit der Vorlage kann ein Cluster basierend auf Windows Server 2012 oder Ubuntu Linux 14.0.4 erstellt werden.
- Verwenden Sie Skripts, die automatisiert oder unbeaufsichtigt ausgeführt werden können. Skripts, mit denen ein Elasticsearch-Cluster erstellt und bereitgestellt werden kann, finden Sie unter [Azure-Schnellstartvorlagen][].

## Aspekte der Größenauslegung von Clustern und Knoten und der Skalierbarkeit

Elasticsearch ermöglicht verschiedene Bereitstellungstopologien, die zur Unterstützung unterschiedlicher Anforderungen und Skalierungsebenen entworfen wurden. In diesem Abschnitt werden einige allgemeine Topologien erläutert, und es wird beschrieben, was bei der Implementierung von Clustern basierend auf diesen Topologien berücksichtigt werden sollte.

### Elasticsearch-Topologien

In Abbildung 4 ist ein Ausgangspunkt zum Entwerfen einer Elasticsearch-Topologie für die Cloud basierend auf Azure VMs dargestellt.

![](media/guidance-elasticsearch-general-startingpoint.png)

**Abbildung 4.**
Vorschlag für einen Ausgangspunkt zum Erstellen eines Elasticsearch-Clusters mit Azure

Diese Topologie besteht aus sechs Datenknoten sowie drei Clientknoten und drei Masterknoten (es ist nur ein Masterknoten ausgewählt, aber die anderen beiden Knoten können bei einem Ausfall des Masterknotens einspringen). Jeder Knoten wird als separate VM implementiert. Azure-Webanwendungen werden über einen Load Balancer an Clientknoten geleitet. In diesem Beispiel befinden sich alle Knoten und die Webanwendungen in demselben Azure VNET, sodass sie praktisch von der Außenwelt abgeschnitten sind. Falls der Cluster extern verfügbar sein muss (z. B. als Teil einer Hybridlösung mit lokalen Clients), können Sie den Azure Load Balancer zum Bereitstellen einer öffentlichen IP-Adresse verwenden. Sie müssen aber zusätzliche Sicherheitsvorkehrungen treffen, um den unbefugten Zugriff auf den Cluster zu verhindern. Die optionale „Jumpbox“ ist eine VM, die nur für Administratoren verfügbar ist. Diese VM verfügt über eine Netzwerkverbindung mit dem Azure VNET, aber auch über eine nach außen gerichtete Netzwerkverbindung, um die Administratoranmeldung aus einem externen Netzwerk zu ermöglichen (diese Anmeldung sollte durch ein sicheres Kennwort oder ein Zertifikat geschützt sein). Ein Administrator kann sich an der Jumpbox anmelden und von dort aus eine direkte Verbindung mit den Knoten im Cluster herstellen. Alternative Ansätze sind beispielsweise die Verwendung einer Site-to-Site-VPN-Verbindung zwischen einem Unternehmen und dem VNET oder von [ExpressRoute][]-Verbindungen mit dem VNET. Diese Verfahren ermöglichen den Administratorzugriff auf den Cluster, ohne dass der Cluster für das öffentliche Internet verfügbar gemacht wird.

Zur Sicherstellung der VM-Verfügbarkeit sind die Datenknoten in derselben Azure-Verfügbarkeitsgruppe gruppiert. Auf ähnliche Weise werden die Clientknoten in einer anderen Verfügbarkeitsgruppe vorgehalten, und die Masterknoten werden in einer dritten Verfügbarkeitsgruppe gespeichert.

Das horizontale Hochskalieren ist für diese Topologie relativ einfach. Fügen Sie einfach weitere Knoten des passenden Typs hinzu, und achten Sie darauf, dass sie in der Datei „elasticsearch.yml“ mit demselben Clusternamen konfiguriert sind. Clientknoten müssen außerdem dem Back-End-Pool für den Azure Load Balancer hinzugefügt werden.

**Geolokalisierung von Clustern**

Verteilen Sie Knoten in einem Cluster nicht auf Regionen, da dies die Leistung der Kommunikation zwischen Knoten beeinträchtigen kann (siehe [Netzwerkanforderungen][]). Für die Geolokalisierung von Daten in der Nähe von Benutzern in unterschiedlichen Regionen ist die Erstellung mehrerer Cluster erforderlich. In diesem Fall müssen Sie sich überlegen, wie (bzw. ob) Sie Cluster synchronisieren. Beispiele für mögliche Lösungen:

**Stellen Sie eine Verbindung mit dem Cluster über [Tribe-Knoten][] (etwa: Stammesknoten) her.** Ein Tribe-Knoten ähnelt einem Clientknoten, aber mit der Ausnahme, dass er an mehreren Elasticsearch-Clustern beteiligt sein kann und alle wie einen großen Cluster ansehen kann. Die Daten werden weiterhin lokal von jedem Cluster verwaltet (Updates werden nicht über die Clustergrenzen hinweg propagiert), aber alle Daten sind sichtbar. Ein Tribe-Knoten kann Dokumente in jedem Cluster abfragen, erstellen und verwalten. Die primären Einschränkungen lauten, dass ein Tribe-Knoten nicht zum Erstellen eines neuen Index verwendet werden kann, und Indexnamen müssen über alle Cluster hinweg eindeutig sein. Überlegen Sie sich daher gut, wie die Indizes benannt werden, wenn Sie Cluster entwerfen, auf die über Tribe-Knoten zugegriffen werden soll.

Bei diesem Verfahren kann jeder Cluster die Daten enthalten, auf die von lokalen Clientanwendungen mit der größten Wahrscheinlichkeit zugegriffen wird. Diese Client können aber weiterhin auf Remotedaten zugreifen und diese ändern, auch wenn es dabei zu einer erhöhten Latenz kommen kann. Abbildung 5 enthält ein Beispiel für diese Topologie. Der Tribe-Knoten in Cluster 1 ist markiert. Die anderen Cluster können auch über Tribe-Knoten verfügen, aber sie werden im Diagramm nicht angezeigt:

![](media/guidance-elasticsearch-general-tribenode.png)

**Abbildung 5.**
Clientanwendung, die über einen Tribe-Knoten auf mehrere Cluster zugreift

In diesem Beispiel stellt die Clientanwendung eine Verbindung mit dem Tribe-Knoten in Cluster 1 her (in derselben Region angeordnet). Dieser Knoten ist aber so konfiguriert, dass auf Cluster 2 und Cluster 3 zugegriffen werden kann, die sich in unterschiedlichen Regionen befinden können. Die Clientanwendung kann Anforderungen senden, mit denen Daten in den Clustern abgerufen oder geändert werden können.

> [AZURE.NOTE] Für Tribe-Knoten ist eine Multicast-Ermittlung erforderlich, um eine Verbindung mit Clustern herzustellen. Dies kann ein Sicherheitsproblem darstellen. Weitere Informationen finden Sie im Abschnitt [Knotenermittlung][].

*	Implementieren Sie die Georeplikation zwischen Clustern. Bei diesem Ansatz werden Änderungen an den einzelnen Clustern nahezu in Echtzeit auf die Cluster propagiert, die sich in anderen Rechenzentren befinden. Es sind Drittanbieter-Plug-Ins für Elasticsearch verfügbar, die diese Funktion unterstützen, z. B. das [PubNub Changes-Plug-In][].
*	Verwenden Sie das [Elasticsearch Snapshot and Restore module][] (Elasticsearch-Modul für Momentaufnahmen und Wiederherstellung). Wenn sich die Daten sehr langsam bewegen und nur von einem einzelnen Cluster geändert werden, können Sie die Verwendung von Momentaufnahmen erwägen, um regelmäßig eine Kopie der Daten zu erstellen. Anschließend können Sie diese Momentaufnahmen in anderen Clustern wiederherstellen (Momentaufnahmen können in Azure Blob Storage gespeichert werden, wenn Sie das [Azure Cloud-Plug-In][] installiert haben). Diese Lösung funktioniert aber nicht gut für sich schnell ändernde Daten, oder wenn Daten in mehr als einem Cluster geändert werden können.

**Kleinere Topologien**

Große Topologien, bei denen die Cluster aus dedizierten Master-, Client- und Datenknoten bestehen, eignen sich unter Umständen nicht für jedes Szenario. Wenn Sie ein kleines Produktions- oder Entwicklungssystem erstellen, können Sie den Cluster mit drei Knoten in Abbildung 6 in Erwägung ziehen. Clientanwendungen stellen eine direkte Verbindung mit allen verfügbaren Datenknoten im Cluster her. Der Cluster enthält drei Shards mit der Bezeichnung P1 - P3 (um Wachstum zu ermöglichen) sowie Replikate mit der Bezeichnung R1 - R3. Mithilfe von drei Knoten kann Elasticsearch die Shards und Replikate so verteilen, dass keine Daten verloren gehen, wenn ein einzelner Knoten ausfällt.

![](media/guidance-elasticsearch-general-threenodecluster.png)

**Abbildung 6.**
Cluster mit drei Knoten sowie drei Shards und Replikaten

Wenn Sie eine Entwicklungsinstallation auf einem eigenständigen Computer ausführen, können Sie einen Cluster mit einem einzelnen Knoten konfigurieren, der als Master, Client und Datenspeicher fungiert. Alternativ dazu können Sie mehrere Knoten starten, die auf demselben Computer als Cluster ausgeführt werden, indem Sie mehr als eine Instanz von Elasticsearch starten. Abbildung 7 enthält ein Beispiel.

![](media/guidance-elasticsearch-general-developmentconfiguration.png)

**Abbildung 7.**
Entwicklungskonfiguration mit Ausführung mehrerer Elasticsearch-Knoten auf demselben Computer

Beachten Sie, dass keine dieser eigenständigen Konfigurationen für eine Produktionsumgebung zu empfehlen ist. Sie können zu Konflikten führen, es sei denn, Ihr Entwicklungscomputer verfügt über sehr viel Arbeitsspeicher und mehrere schnelle Datenträger. Außerdem kann die hohe Verfügbarkeit nicht sichergestellt werden. Wenn der Computer ausfällt, gehen alle Knoten verloren.

### Aspekte der Skalierung eines Clusters und von Datenknoten

Elasticsearch kann in zwei Richtungen skaliert werden: vertikal (mit größeren Computern mit höherer Leistung) und horizontal (mit Verteilung der Auslastung auf die Computer).

**Vertikales Skalieren von Elasticsearch-Datenknoten**

Wenn Sie einen Elasticsearch-Cluster hosten, indem Sie Azure VMs verwenden, kann jeder Knoten einer VM entsprechen. Die vertikale Skalierbarkeit eines Knotens wird vor allem durch die SKU der VM und die allgemeinen Einschränkungen bestimmt, die für einzelne Speicherkonten und Azure-Abonnements gelten. Auf der Seite [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](azure-subscription-service-limits/) sind die Einschränkungen ausführlich beschrieben. Was die Erstellung eines Elasticsearch-Clusters betrifft, sind die Angaben in der folgenden Liste am wichtigsten. Außerdem sollten Sie ohne triftigen Grund keine VMs mit mehr als 64 GB Arbeitsspeicher verwenden. Wie im Abschnitt [Arbeitsspeicheranforderungen][] beschrieben, sollten Sie nicht mehr als 30 GB RAM auf jeder VM für die JVM zuordnen und es dem Betriebssystem ermöglichen, den restlichen Arbeitsspeicher für die E/A-Pufferung zu nutzen:
- Jedes Speicherkonto ist auf 20.000 IOPS beschränkt. Die Verwendung desselben Speicherkontos für mehrere VHDs kann zu einer Beeinträchtigung der Leistung dieser VHDs führen.
- Die Anzahl der Datenknoten in einem VNET. Wenn Sie nicht den Azure-Ressourcen-Manager (ARM) verwenden, gilt eine Obergrenze von 2048 VM-Instanzen pro VNET. Dies ist für viele Fälle zwar ausreichend, aber es kann eine Einschränkung darstellen, wenn Sie über eine sehr große Konfiguration mit Tausenden von Knoten verfügen.
- Anzahl von Speicherkonten pro Abonnement und Region. Sie können bis zu 100 Speicherkonten pro Azure-Abonnement in jeder Region erstellen. Speicherkonten werden zum Vorhalten von virtuellen Datenträgern verwendet, und jedes Speicherkonto weist eine Speicherplatzbeschränkung von 500 TB auf.
- Anzahl der Kerne pro Abonnement. Die Standardobergrenze beträgt 20 Kerne pro Abonnement, aber dies kann von Microsoft auf bis zu 10.000 Kerne erhöht werden. Denken Sie daran, dass einige VM-Größen (A9, A11, D14 und DS14) 16 Kerne enthalten können, während eine G5-VM 32 Kerne hat.
- Die Arbeitsspeichermenge pro VM-Größe. Für kleinere VMs sind beschränkte Mengen von Arbeitsspeicher verfügbar (D1-Maschinen haben 3,5 GB, und D2-Maschinen haben 7 GB). Diese Maschinen sind unter Umständen nicht für Szenarien geeignet, in denen Elasticsearch große Mengen von Daten zwischenspeichern muss, um eine gute Leistung zu erzielen (z. B. das Aggregieren von Daten oder Analysieren einer großen Zahl von Dokumenten während der Datenerfassung).
- Die maximale Anzahl von Datenträgern pro VM-Größe. Durch diese Einschränkung können Größe und Leistung eines Clusters begrenzt werden. Eine geringere Zahl von Datenträgern bedeutet, dass weniger Daten vorgehalten werden können, und die Leistung kann reduziert werden, indem weniger Datenträger für das Striping verfügbar sind.
- Die Anzahl von Updatedomänen/Fehlerdomänen pro Verfügbarkeitsgruppe. Wenn Sie VMs per ARM erstellen, kann jede Verfügbarkeitsgruppe bis zu drei Fehlerdomänen und 20 Updatedomänen zugeordnet werden. Diese Einschränkung kann sich auf die Resilienz eines großen Clusters auswirken, für das häufig rollierende Updates durchgeführt werden.

Aufgrund dieser Einschränkungen sollten Sie die virtuellen Datenträger für die VMs in einem Cluster immer auf Speicherkonten verteilen, um die Wahrscheinlichkeit einer E/A-Drosselung zu reduzieren. In einem sehr großen Cluster müssen Sie unter Umständen Ihre logische Infrastruktur neu entwerfen und in separate funktionale Partitionen aufteilen. Unter Umständen müssen Sie den Cluster beispielsweise auf Abonnements aufteilen. Dieser Prozess kann aber zu weiteren Komplikationen führen, weil VNETs verbunden werden müssen.

>	[AZURE.NOTE] Beachten Sie, dass Speicherkonten in Azure mit einem spezifischen Speicherstempel verknüpft werden. Dies ist ein interner Testmechanismus, mit dem Konsistenz und Verfügbarkeit sichergestellt werden. Im Dokument [A Highly Available Cloud Storage Service with Strong Consistency][] (Ein hoch verfügbarer Cloudspeicherdienst mit hoher Konsistenz) werden weitere Informationen zur Funktionsweise bereitgestellt. Wenn es für einen spezifischen Stempel zu einem Speicherausfall kommt,  erhalten Sie Fehler für alle Laufwerke, die mit diesem Konto erstellt wurden. Wenn dieser Fall eintritt, kann es zu Fehlern für alle virtuellen Computer kommen, die diese Laufwerke verwenden. Das Verwenden mehrerer Speicherkonten zum Hosten unterschiedlicher Laufwerke für einen virtuellen Computer kann deshalb das Risiko eines Ausfalls für diesen virtuellen Computer erhöhen. Aus diesem Grund wird empfohlen, pro Knoten ein einziges Speicherkonto zu verwenden und das Systemlaufwerk und alle Datenlaufwerke in diesem Konto zu speichern.

**Horizontales Skalieren eines Elasticsearch-Clusters**

Intern wird die Obergrenze für die horizontale Skalierbarkeit in Elasticsearch durch die Anzahl von Shards bestimmt, die für jeden Index definiert sind. Am Anfang können viele Shards demselben Knoten in einem Cluster zugeordnet werden, aber wenn der Umfang der Daten zunimmt, können weitere Knoten hinzugefügt werden, und Shards können auf diese Knoten verteilt werden. Theoretisch beendet das System die horizontale Skalierung nur, wenn die Anzahl der Knoten die Anzahl der Shards erreicht.

Wie bei der vertikalen Skalierung auch, sollten Sie einige Punkte berücksichtigen, wenn Sie die Implementierung der horizontalen Skalierung in Erwägung ziehen, z. B.:

- Die maximale Anzahl von VMs, die Sie in einem Azure-VNET verbinden können. Hierdurch kann die horizontale Skalierbarkeit für einen sehr großen Cluster eingeschränkt werden. Sie können einen Cluster mit Knoten erstellen, der über mehr als ein VNET verläuft, um diese Einschränkung zu umgehen. Dieser Ansatz kann aber zu einer verringerten Leistung führen, da die einzelnen Knoten sich nicht in der Nähe der anderen Knoten befinden.
- Die Anzahl von Laufwerken pro VM-Größe. Für unterschiedliche Reihen und SKUs wird eine unterschiedliche Anzahl von angefügten Datenträgern unterstützt. Außerdem können Sie den flüchtigen Speicher verwenden, der in der VM enthalten ist, um eine begrenzte Menge an schnellerem Datenspeicher bereitzustellen. Dies ist aber mit Resilienz- und Wiederherstellungsaspekten verbunden, die Sie berücksichtigen sollten. (Weitere Informationen finden Sie im Dokument „Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery“ (Konfigurieren, Testen und Analysieren der Elasticsearch-Resilienz und -Wiederherstellung).) Für die D-Reihe, DS-Reihe, Dv2-Reihe und GS-Reihe der VMs werden für den flüchtigen Speicher SSDs verwendet.
- Der maximale IOPS-Wert pro virtuellem Datenträger. Normale angefügte Datenträger, die per Standardspeicher erstellt werden, sind auf 500 IOPS pro Datenträger beschränkt. Angefügte SSDs können bis zu 5.000 IOPS pro Datenträger unterstützen. Zum Verwenden von SSDs für angefügte Datenträger (kein flüchtiger Speicher) müssen Sie eine VM erstellen, die Premium-Speicher unterstützt (Maschinen der DS-Reihe oder GS-Reihe).

Sie können die Verwendung von Azure-Skalierungsgruppen erwägen, um VMs je nach Bedarf zu starten und zu beenden (Details finden Sie unter [Automatisches Skalieren von Computern in einer VM-Skalierungsgruppe][]). Allerdings ist dieser Ansatz für einen Elasticsearch-Cluster aus den folgenden Gründen unter Umständen nicht geeignet:

- Dieser Ansatz eignet sich am besten für zustandslose VMs. Bei jedem Hinzufügen oder Entfernen eines Knotens aus einem Elasticsearch-Cluster werden die Shards neu zugeordnet, um die Last zu verteilen. Bei diesem Prozess können erhebliche Mengen von Netzwerkdatenverkehr und Datenträger-E/A-Vorgängen generiert werden, und es können sich große Auswirkungen auf die Datenerfassungsraten ergeben. Sie müssen entscheiden, ob dieser Mehraufwand dadurch aufgewogen wird, dass zusätzliche Verarbeitungs- und Arbeitsspeicherressourcen verfügbar sind, indem mehr VMs dynamisch gestartet werden.
- Der VM-Start findet nicht sofort statt, und es kann mehrere Minuten dauern, bevor weitere VMs verfügbar sind oder heruntergefahren werden. Die Skalierung auf diese Weise sollte nur verwendet werden, um auf anhaltende Veränderungen des Bedarfs zu reagieren.
- Müssen Sie den Prozess nach dem horizontalen Hochskalieren wirklich rückgängig machen? Das Entfernen einer VM aus einem Elasticsearch-Cluster kann ein ressourcenintensiver Prozess sein. Es kann erforderlich sein, dass Elasticsearch die Shards und Replikate der VM wiederherstellt und auf einem oder mehreren verbleibenden Knoten neu erstellt. Durch das gleichzeitige Entfernen mehrerer VMs kann die Integrität des Clusters beeinträchtigt werden, wodurch die Wiederherstellung erschwert wird. Außerdem wachsen viele Elasticsearch-Implementierungen im Laufe der Zeit an, aber für die Daten gilt, dass ihr Volumen normalerweise nicht abnimmt. Dokumente können auch manuell gelöscht und außerdem mit einer Gültigkeitsdauer (Time To Live, TTL) konfiguriert werden, nach der sie ablaufen und entfernt werden. In den meisten Fällen wird der bisher zugewiesene Speicherplatz aber von neuen oder geänderten Dokumenten nach kurzer Zeit wiederverwendet. Es kann zu einer Fragmentierung innerhalb eines Index kommen, wenn Dokumente entfernt oder geändert werden. In diesem Fall können Sie die Elasticsearch-HTTP-[Optimize][]-API (Elasticsearch 2.0.0 und früher) oder die [Force Merge][]-API (Elasticsearch 2.1.0 und höher) verwenden, um die Defragmentierung durchzuführen.

> [AZURE.NOTE] Die Optimierung ist ein sehr aufwändiger Vorgang und sollte nicht für einen Index durchgeführt werden, der sehr aktiv ist. Es ist aber ratsam, einen untätigen Index zu optimieren, da dies dazu beiträgt, die für die Durchführung der Suche erforderlichen Ressourcen zu reduzieren.

### Bestimmen der Anzahl von Shards für einen Index

Die Anzahl der Knoten in einem Cluster kann sich im Laufe der Zeit verändern, aber die Anzahl der Shards in einem Index ist nach seiner Erstellung festgelegt. Zum Hinzufügen oder Entfernen von Shards ist eine Neuindizierung der Daten erforderlich. Bei diesem Prozess wird ein neuer Index mit der erforderlichen Anzahl von Shards erstellt, und dann werden die Daten aus dem alten Index in den neuen Index kopiert. (Sie können Aliase verwenden, um Benutzer von der Neuindizierung der Daten abzuschotten. Weitere Informationen finden Sie im Dokument „Maximieren der Datenaggregations- und Abfrageleistung mit Elasticsearch unter Azure“.) Daher ist es wichtig, die Anzahl der voraussichtlich benötigten Shards zu bestimmen, bevor Sie den ersten Index im Cluster erstellen. Sie können die folgenden Schritte ausführen, um diese Zahl zu ermitteln:

1. Erstellen Sie einen Cluster mit einem Knoten, indem Sie die gleiche Hardwarekonfiguration nutzen, die Sie auch in der Produktion bereitstellen möchten.
2. Erstellen Sie einen Index, der mit der Struktur übereinstimmt, deren Verwendung Sie für die Produktion planen. Weisen Sie dem Index ein einzelnes Shard und keine Replikate zu.
3. Fügen Sie dem Index eine bestimmte Menge realistischer Produktionsdaten hinzu.
4. Führen Sie typische Abfrage-, Aggregations- und andere Workloadvorgänge für den Index durch, und messen Sie den Durchsatz und die Reaktionszeit.
5. Wenn der Durchsatz und die Reaktionszeit innerhalb akzeptabler Grenzen liegen, können Sie den Prozess ab Schritt 3 (Hinzufügen weiterer Daten) wiederholen.
6. Notieren Sie sich das Volumen der Dokumente, wenn die Kapazität des Shards erschöpft zu sein scheint (Reaktionszeiten und Durchsatz sind nicht mehr akzeptabel).
7. Führen Sie eine Extrapolation von der Kapazität eines einzelnen Shards auf die erwartete Anzahl von Dokumenten in der Produktion durch, um die erforderliche Anzahl von Shards zu berechnen (unter Verwendung einer Fehlertoleranz, da die Extrapolation kein präzises Verfahren ist).

> [AZURE.NOTE] Beachten Sie, dass jedes Shard als Lucene-Index implementiert wird, der Arbeitsspeicher, CPU-Leistung und Dateihandles verbraucht. Je mehr Shards Sie verwenden, desto mehr Ressourcen dieser Art benötigen Sie.

Außerdem kann die Erstellung weiterer Shards zu einer besseren Skalierbarkeit (je nach Workloads und Szenario) und einem Anstieg des Durchsatzes bei der Datenerfassung führen, aber gleichzeitig wird unter Umständen die Effizienz vieler Abfragen reduziert. Standardmäßig wird bei einer Abfrage jedes Shard befragt, das von einem Index verwendet wird. (Sie können das [benutzerdefinierte Routing][] verwenden, um dieses Verhalten zu ändern, wenn Sie wissen, auf welchen Shards sich die benötigten Daten befinden.)

Mit diesem Prozess kann nur eine Schätzung der Anzahl von Shards produziert werden, und das Volumen der Dokumente, die in der Produktion zu erwarten sind, ist ggf. nicht bekannt. In diesem Fall sollten Sie das Anfangsvolumen (wie oben) und die vorhergesagte Wachstumsrate ermitteln. Erstellen Sie eine passende Anzahl von Shards, die das Wachstum der Daten bis zu dem Zeitpunkt verarbeiten können, an dem Sie die Datenbank neu indizieren möchten. Bei anderen Strategien für Szenarien wie die Ereignisverwaltung und -protokollierung werden rollierende Indizes verwendet. Jeden Tag wird für die erfassten Daten ein neuer Index erstellt, auf den mit einem Alias zugegriffen wird, der täglich so angepasst wird, dass er auf den neuesten Index verweist. Mit diesem Ansatz können Sie den Alterungsprozess alter Daten vereinfachen (Sie können Indizes mit Informationen löschen, die nicht mehr erforderlich sind), und das Datenvolumen bleibt gut verwaltbar.

Denken Sie daran, dass die Anzahl von Knoten nicht mit der Anzahl von Shards übereinstimmen muss. Wenn Sie beispielsweise 50 Shards erstellen, können Sie sie am Anfang auf zehn Knoten verteilen und dann mehr Knoten hinzufügen, um das System horizontal zu skalieren, falls das Arbeitsvolumen zunimmt. Vermeiden Sie es aber, eine außergewöhnlich große Zahl von Shards auf einer geringen Zahl von Knoten zu erstellen (z. B. 1.000 Shards verteilt auf zwei Knoten). Auch wenn das System mit dieser Konfiguration theoretisch auf 1.000 Knoten skaliert werden kann, besteht bei der Ausführung von 500 Shards auf nur einem Knoten das Risiko, dass die Leistung des Knotens einbricht.

> [AZURE.NOTE] Erwägen Sie für Systeme, bei denen der Aufwand für die Datenerfassung hoch ist, die Verwendung einer Primzahl als Shard-Anzahl. Der Standardalgorithmus, der von Elasticsearch zum Weiterleiten von Dokumenten an Shards verwendet wird, führt in diesem Fall zu einer gleichmäßigeren Verteilung.

### Sicherheitshinweise

Standardmäßig werden für Elasticsearch nur minimale Sicherheitsmaßnahmen implementiert, und es werden keine Mittel für die Authentifizierung und Autorisierung bereitgestellt. Aufgrund dieser Aspekte ist es erforderlich, das zugrunde liegende Betriebssystem und Netzwerk zu konfigurieren und Plug-Ins und Hilfsprogramme von Drittanbietern zu verwenden. Beispiele hierfür sind [Shield][] und [Search Guard][].

> [AZURE.NOTE] Shield ist ein Plug-In von Elastic für die Benutzerauthentifizierung, Datenverschlüsselung, rollenbasierte Zugriffssteuerung, IP-Filterung und Überwachung. Es kann erforderlich sein, das zugrunde liegende Betriebssystem zu konfigurieren, um weitere Sicherheitsmaßnahmen zu implementieren, z. B. die Datenträgerverschlüsselung.

Für ein Produktionssystem sollten Sie überlegen, wie Sie Folgendes sicherstellen:

- Verhindern des unberechtigten Zugriffs auf den Cluster
- Identifizieren und Authentifizieren von Benutzern
- Autorisieren der Vorgänge, die authentifizierte Benutzer durchführen können
- Schützen des Clusters vor nicht autorisierten oder schädlichen Vorgängen
- Schützen der Daten vor unberechtigtem Zugriff
- Erfüllen rechtlicher Anforderungen an die Sicherheit kommerzieller Daten (falls zutreffend)

### Schützen des Zugriffs auf den Cluster

Elasticsearch ist ein Netzwerkdienst. Die Knoten in einem Elasticsearch-Cluster lauschen auf eingehende Clientanforderungen per HTTP und kommunizieren über einen TCP-Kanal miteinander. Sie sollten Maßnahmen ergreifen, um zu verhindern, dass unberechtigte Clients oder Dienste Anforderungen sowohl über HTTP-Pfade als auch über TCP-Pfade senden können. Erwägen Sie folgende Maßnahmen:

- Definieren Sie Netzwerksicherheitsgruppen, um den eingehenden und ausgehenden Netzwerkdatenverkehr für ein VNET oder eine VM nur auf bestimmte Ports zu beschränken.
- Ändern Sie die Standardports, die für den Clientwebzugriff (9200) und den programmgesteuerten Netzwerkzugriff (9300) verwendet werden. Verwenden Sie eine Firewall, um jeden Knoten vor schädlichem Internetdatenverkehr zu schützen.
- Ordnen Sie den Cluster je nach Standort und Konnektivität der Clients in einem privaten Subnetz ohne direkten Internetzugriff an. Leiten Sie – wenn der Cluster außerhalb des Subnetzes verfügbar gemacht werden muss – alle Anforderungen über einen „Bastionsserver“ oder Proxy, der für den Schutz des Clusters ausreichend ausgestattet ist.
- Falls Sie den direkten Zugriff auf Knoten gewähren müssen, verwenden Sie das Elasticsearch-Jetty-Plug-In, um SSL-Konnektivität, -Authentifizierung und -Verbindungsprotokollierung bereitzustellen. Alternativ dazu können Sie einen nginx-Proxyserver und die HTTPS-Authentifizierung konfigurieren.

> [AZURE.NOTE] Wenn Sie einen Proxyserver verwenden, z. B. nginx, können Sie auch den Zugriff auf Funktionen beschränken. Sie können nginx beispielsweise so konfigurieren, dass nur Anforderungen an den Endpunkt „\_search“ zugelassen werden, wenn Sie verhindern möchten, dass Clients andere Vorgänge durchführen.

- Verwenden Sie das Shield- oder Search Guard-Plug-In, falls Sie umfassendere Sicherheitsmaßnahmen für den Netzwerkzugriff benötigen.

### Identifizieren und Authentifizieren von Benutzern

Alle Anforderungen von Clients an den Cluster sollten authentifiziert werden. Außerdem sollten Sie verhindern, dass nicht autorisierte Knoten dem Cluster beitreten, da auf diese Weise eine „Backdoor“ für das System geschaffen werden kann, um die Authentifizierung zu umgehen.

Es sind Elasticsearch-Plug-Ins verfügbar, mit denen unterschiedliche Arten von Authentifizierung durchgeführt werden können, z. B.:
- HTTP-Standardauthentifizierung. Hierbei sind jeweils Benutzernamen und Kennwörter enthalten. Alle Anforderungen sollten per SSL/TLS oder unter Verwendung einer gleichwertigen Schutzebene verschlüsselt werden.
- LDAP- und Active Directory-Integration. Bei diesem Ansatz ist es erforderlich, dass Clients Rollen in LDAP- oder AD-Gruppen zugewiesen werden.
- Native Authentifizierung mit Identitäten, die im Elasticsearch-Cluster selbst definiert werden.
- TLS-Authentifizierung in einem Cluster zum Authentifizieren aller Knoten.
- IP-Filterung, um für Clients aus nicht autorisierten Subnetzen die Verbindungsherstellung zu verhindern und auszuschließen, dass Knoten aus diesen Subnetzen dem Cluster beitreten.

### Autorisieren von Clientanforderungen

Die Autorisierung ist von dem Elasticsearch-Plug-In abhängig, das zum Bereitstellen dieses Diensts verwendet wird. Ein Plug-In für die Standardauthentifizierung stellt normalerweise Features bereit, mit denen die Ebene der Authentifizierung definiert wird. Bei einem Plug-In, für das LDAP oder AD verwendet wird, werden dagegen Clients normalerweise Rollen zugeordnet, und diesen Rollen werden dann Zugriffsrechte zugewiesen. Bei Verwendung aller Plug-Ins sollten Sie die folgenden Punkte berücksichtigen:
- Müssen Sie die Vorgänge beschränken, die von einem Client durchgeführt werden können? Sollte es für einen Client beispielsweise möglich sein, den Status des Clusters zu überwachen oder Indizes zu erstellen und zu löschen?
- Sollte der Client auf bestimmte Indizes beschränkt werden? Dies ist in Situationen mit mehreren Mandanten hilfreich, in denen Mandanten ein eigener spezieller Satz von Indizes zugewiesen werden kann und diese Indizes für andere Mandanten unzugänglich sein sollen.
- Sollte der Client Daten in einem Index lesen und in einen Index schreiben können? Beispielsweise kann ein Client in der Lage sein, Suchen durchzuführen, bei denen Daten mit einem Index abgerufen werden. Es soll aber verhindert werden, dass dem Index Daten hinzugefügt oder daraus gelöscht werden.

Bei den meisten Sicherheits-Plug-Ins sind die Vorgänge auf die Cluster- oder Indexebene beschränkt, und nicht auf Subnetze mit Dokumenten in Indizes. Dies ist aus Effizienzgründen der Fall. Daher ist es nicht einfach, Anforderungen auf bestimmte Dokumente in einem Index zu beschränken. Falls Sie diesen Grad an Granularität benötigen, sollten Sie Dokumente in separaten Indizes speichern und Aliase verwenden, unter denen Indizes gruppiert werden. Gehen Sie beispielsweise wie folgt vor, wenn Benutzer A in einem Personalverwaltungssystem Zugriff auf alle Dokumente benötigt, die Informationen zu Mitarbeitern von Abteilung X enthalten, Benutzer B Zugriff auf alle Dokumente mit Informationen zu Mitarbeitern in Abteilung Y und Benutzer C Zugriff auf alle Dokumente mit Informationen zu Mitarbeitern in beiden Abteilungen: Erstellen Sie zwei Indizes (für Abteilung X und Abteilung Y) und einen Alias, der auf beide Indizes verweist. Gewähren Sie Benutzer A Lesezugriff auf den ersten Index, gewähren Sie Benutzer B Lesezugriff auf den zweiten Index, und gewähren Sie Benutzer C Lesezugriff auf beide Indizes über den Alias. Weitere Informationen finden Sie unter [Faking Index per User with Aliases][] („Faken“ des Index pro Benutzer mit Aliasen).

### Schützen des Clusters

Der Cluster kann anfällig für missbräuchliche Nutzung werden, wenn er nicht ausreichend geschützt ist. Dieser Schutz wird in den folgenden Punkten beschrieben:

- Deaktivieren Sie die dynamische Abfrageskripterstellung in Elasticsearch-Abfragen, da dies zu Sicherheitsrisiken führen kann. Ziehen Sie der Abfrageskripterstellung die Verwendung von nativen Skripts vor. Ein natives Skript ist ein Elasticsearch-Plug-In, das in Java geschrieben ist und zu einer JAR-Datei kompiliert wird.

> [AZURE.NOTE] Die dynamische Abfrageskripterstellung ist jetzt standardmäßig deaktiviert. Aktivieren Sie diese Funktion nur, wenn Sie dafür einen triftigen Grund haben.

- Vermeiden Sie es, Abfragezeichenfolgen-Suchvorgänge für Benutzer verfügbar zu machen. Abfragezeichenfolgen-Suchvorgänge ermöglichen Benutzern die ungehinderte Durchführung von ressourcenintensiven Abfragen. Diese Suchvorgänge können sich stark auf die Leistung des Clusters auswirken und das System anfällig für DoS-Angriffe machen. Bei Abfragezeichenfolgen-Suchvorgängen können unter Umständen außerdem private Informationen offengelegt werden.
- Verhindern Sie, dass Vorgänge eine große Menge von Arbeitsspeicher verbrauchen, da dies zu Ausnahmen aufgrund von unzureichendem Speicher und dem Ausfall von Elasticsearch auf einem Knoten führen kann. Ressourcenintensive Vorgänge mit langer Ausführungsdauer können auch zum Implementieren von DoS-Angriffen verwendet werden. Beispiele:
- Suchanforderungen, bei denen versucht wird, sehr große Felder in den Arbeitsspeicher zu laden (wenn eine Abfrage Sortier-, Skript- oder Facettenvorgänge für diese Felder durchführt).
  
> [AZURE.NOTE] Mit so genannten [Doc Values][] können die Arbeitsspeicheranforderungen von Indizes reduziert werden, indem Felddaten auf dem Datenträger gespeichert und nicht in den Arbeitsspeicher geladen werden. Dies kann dazu beitragen, die Wahrscheinlichkeit einer Ausschöpfung des Speichers auf einem Knoten zu verringern. Dies ist aber auch mit einer Reduzierung der Geschwindigkeit verbunden.

- Suchvorgänge, bei denen mehrere Indizes gleichzeitig abgefragt werden.
- Suchvorgänge, bei denen eine große Zahl von Feldern abgerufen wird. Diese Suchvorgänge können dazu führen, dass der Arbeitsspeicher erschöpft ist, weil eine große Menge von Felddaten zwischengespeichert wird. Standardmäßig gilt für den Felddatencache keine Größenbegrenzung, aber Sie können die [indices.fielddata.cache.*](https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html)-Eigenschaften in der Konfigurationsdatei „elasticsearch.yml“ festlegen, um die verfügbaren Ressourcen zu beschränken. Sie können auch den [Field Data Circuit Breaker][] (Felddaten-Schutzschalter) konfigurieren, um zu verhindern, dass die zwischengespeicherten Daten eines Felds zu einer Erschöpfung des Arbeitsspeichers führen. Außerdem können Sie den [Request Circuit Breaker][] (Anforderungsschutzschalter) konfigurieren, um zu verhindern, dass einzelne Abfragen den Arbeitsspeicher belegen. Der Kostenfaktor für das Einstellen dieser Parameter ist die erhöhte Wahrscheinlichkeit, dass einige Abfragen fehlschlagen oder die Zeitüberschreitung erreichen.
  
> [AZURE.NOTE] Elasticsearch geht immer davon aus, dass genügend Arbeitsspeicher zum Verarbeiten der aktuellen Workload vorhanden ist. Wenn dies nicht der Fall ist, kann der Elasticsearch-Dienst abstürzen. Elasticsearch stellt Endpunkte bereit, die Informationen zur Ressourcenauslastung zurückgeben (HTTP-[cat-APIs][]). Sie sollten diese Informationen sorgfältig überwachen.

- Zu langes Warten, bis ein mit einem Vorgang belegtes Speichersegment geleert wird. Dies kann dazu führen, dass der speicherinterne Pufferspeicher erschöpft ist. [Konfigurieren Sie bei Bedarf das Translog][], um die Schwellenwerte zu reduzieren, bei denen Daten auf den Datenträger verschoben werden.

- Erstellen von Indizes mit großen Mengen von Metadaten. Ein Index, der Dokumente mit vielen unterschiedlichen Feldnamen enthält, kann viel Arbeitsspeicher verbrauchen. Weitere Informationen finden Sie unter [Mapping Explosion][] (Zunahme der Zuordnungen).
  
> [AZURE.NOTE] Die Definition eines Vorgangs mit langer Ausführungsdauer oder hoher Abfrageintensität ist sehr szenariospezifisch. Die Workload, die von einem Cluster normalerweise erwartet wird, kann gegenüber der Workload auf einem anderen Cluster ein vollständig anderes Profil aufweisen. Für die Ermittlung, welche Vorgänge nicht akzeptabel sind, sind erheblicher Rechercheaufwand und umfangreiche Tests Ihrer Anwendungen erforderlich.

Gehen Sie proaktiv vor. Erkennen und stoppen Sie schädliche Aktivitäten, bevor sie zu erheblichen Schäden oder Datenverlusten führen. Erwägen Sie die Verwendung eines Systems für die Sicherheitsüberwachung und Benachrichtigungen, mit dem ungewöhnliche Datenzugriffsmuster schnell erkannt und beispielsweise Warnungen ausgelöst werden können, wenn Anforderungen für Benutzeranmeldungen fehlschlagen, unerwartete Knoten dem Cluster beitreten oder diesen verlassen oder Vorgänge länger als erwartet dauern. Zu den Tools, mit denen diese Aufgaben durchgeführt werden können, gehört der Elasticsearch-[Watcher][].

### Schützen der Daten

Sie können Daten quasi „im Vorübergehen“ schützen, indem Sie SSL/TLS nutzen. Elasticsearch bietet aber keine integrierte Form der Datenverschlüsselung für Informationen, die auf Datenträger gespeichert werden. Beachten Sie, dass diese Informationen in normalen Datenträgerdateien vorgehalten werden. Jeder Benutzer mit Zugriff auf diese Dateien kann die darin enthaltenen Daten manipulieren, indem er sie beispielsweise in seinen eigenen Cluster kopiert. Beachten Sie die folgenden Punkte:
- Schützen Sie die Dateien, die von Elasticsearch zum Vorhalten der Daten verwendet werden. Verhindern Sie den willkürlichen Lese- oder Schreibzugriff auf andere Identitäten als den Elasticsearch-Dienst.

- Verschlüsseln Sie die Daten in diesen Dateien mit einem Encrypting File System.

> [AZURE.NOTE] Azure unterstützt jetzt die Datenträgerverschlüsselung für Linux- und Windows-VMs. Weitere Informationen finden Sie unter [Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer (Vorschau)][].

### Erfüllen rechtlicher Anforderungen

Bei rechtlichen Anforderungen geht es hauptsächlich um Überwachungsvorgänge zur Aufrechterhaltung eines Ereignisverlaufs und die Sicherstellung des damit verbundenen Datenschutzes, um die Überwachung (und Wiedergabe) durch eine externe Stelle zu verhindern. Überlegen Sie sich besonders die folgenden Vorgehensweisen:

- Verfolgen Sie alle Anforderungen (ob erfolgreich oder nicht) und alle Versuche, auf das System zuzugreifen.
- Verschlüsseln Sie die Kommunikation von Clients mit dem Cluster sowie die Kommunikation des Clusters von Knoten zu Knoten. Sie sollten für die gesamte Clusterkommunikation SSL/TLS implementieren. Elasticsearch unterstützt auch die Plug-fähige Verschlüsselung, wenn für Ihr Unternehmen Anforderungen gelten, die mit SSL/TLS nicht erfüllt werden können.
- Speichern Sie alle Überwachungsdaten an einem sicheren Ort. Der Umfang der Überwachungsinformationen kann sehr schnell zunehmen, und die Daten müssen stark geschützt werden, um die Manipulation von Überwachungsinformationen zu verhindern.
- Archivieren Sie die Überwachungsdaten auf sichere Weise.

### Aspekte der Überwachung

Die Überwachung ist sowohl auf Betriebssystemebene als auch auf Elasticsearch-Ebene wichtig.

Sie können die Überwachung auf Betriebssystemebene mit Tools des Betriebssystems durchführen. Unter Windows umfasst dies beispielsweise den Systemmonitor mit den entsprechenden Leistungsindikatoren, während Sie unter Linux Tools wie *vmstat*, *iostat* und *top* nutzen können. Zu den wichtigsten Elementen, die auf Betriebssystemebene überwacht werden müssen, gehören die CPU-Auslastung, Datenträger-E/A-Volumen, Datenträger-E/A-Wartezeiten und Netzwerkdatenverkehr. In einem gut eingestellten Elasticsearch-Cluster sollte die CPU-Auslastung durch den Elasticsearch-Prozess hoch sein, und die Datenträger-E/A-Wartezeiten sollten möglichst gering gehalten werden.

Auf Softwareebene sollten Sie den Durchsatz und die Reaktionszeiten von Anforderungen überwachen, sowie die Details der Anforderungen, die nicht erfolgreich sind. Elasticsearch stellt eine Reihe von APIs bereit, die Sie verwenden können, um die Leistung unterschiedlicher Aspekte eines Clusters zu untersuchen. Die zwei wichtigsten APIs sind *\_cluster/health* und *\_nodes/stats*. Die API *\_cluster/health* kann zum Bereitstellen einer Momentaufnahme der Gesamtintegrität des Clusters und von ausführlichen Informationen für jeden Index verwendet werden. Dies wird im folgenden Beispiel veranschaulicht:

`GET _cluster/health?level=indices`

Die folgende Ausgabe wurde mit dieser API generiert:

```json
{
    "cluster_name": "elasticsearch",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 6,
    "number_of_data_nodes": 3,
    "active_primary_shards": 10,
    "active_shards": 20,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "indices": {
        "systwo": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        },
        "sysfour": {
            "status": "green",
            "number_of_shards": 5,
            "number_of_replicas": 1,
            "active_primary_shards": 5,
            "active_shards": 10,
            "relocating_shards": 0,
            "initializing_shards": 0,
            "unassigned_shards": 0
        }
    }
}
```

Dieser Cluster enthält zwei Indizes mit den Namen *systwo* und *sysfour*. Wichtige Statistiken für die Überwachung eines Index sind „status“, „active\_shards“ und „unassigned\_shards“. Die Statusanzeige sollte grün sein, die Anzahl unter „active\_shards“ sollte die Anzahl von „number\_of\_shards“ und „number\_of\_replicas“ widerspiegeln, und „unassigned\_shards“ sollte den Wert 0 haben. Wenn die Statusanzeige rot ist, fehlt ein Teil des Index oder wurde beschädigt. Sie können dies überprüfen, indem Sie sich die Einstellungen ansehen: *active\_shards* ist kleiner als *number\_of\_shards* - (*number\_of\_replicas* + 1), und „unassigned\_shards“ ist nicht 0. Beachten Sie, dass eine gelbe Statusanzeige auf Folgendes hinweist: Ein Index befindet sich in einem Übergangszustand, entweder als Ergebnis der Hinzufügung weiterer Replikate oder aufgrund einer Neuanordnung von Shards. Der Status sollte grün angezeigt werden, wenn der Übergang abgeschlossen ist. Falls der Status längere Zeit gelb angezeigt wird oder sich in rot ändert, sollten Sie nachsehen, ob auf Betriebssystemebene signifikante E/A-Ereignisse (z. B. ein Datenträger- oder Netzwerkausfall) aufgetreten sind.
Die API „\_nodes/stats“ liefert umfassende Informationen zu jedem Knoten im Cluster:

`GET _nodes/stats`

Die generierte Ausgabe enthält Details dazu, wie Indizes auf den einzelnen Knoten gespeichert werden (einschließlich der Größe und Anzahl von Dokumenten), sowie Dauer der Indizierung, Abfragen, Suchvorgänge und Zusammenführung, Caching, Betriebssystem- und Prozessinformationen, Statistiken zur JVM (z. B. Leistung der Garbage Collection) und Threadpools. Weitere Informationen finden Sie unter [Monitoring Individual Nodes][] (Überwachen einzelner Knoten).

Wenn für einen größeren Teil der Elasticsearch-Anforderungen *EsRejectedExecutionException*-Fehlermeldungen auftreten, hat Elasticsearch Schwierigkeiten mit dem Abarbeiten der Aufgaben, die an den Dienst gesendet werden. In diesem Fall müssen Sie den Engpass identifizieren, der dazu führt, dass Elasticsearch zurückfällt. Erwägen Sie folgende Maßnahmen:

- Wenn der Grund für den Engpass eine Ressourceneinschränkung ist, z. B. eine übermäßig hohe Anzahl von Garbage Collection-Vorgängen aufgrund von unzureichendem Speicher für die JVM, sollten Sie erwägen, zusätzliche Ressourcen zuzuordnen. (Konfigurieren Sie die JVM in diesem Fall für die Verwendung von mehr Arbeitsspeicher, und zwar bis zu 50 % des verfügbaren Speichers auf dem Knoten – siehe [Arbeitsspeicheranforderungen][].)
- Wenn der Cluster lange E/A-Wartezeiten aufweist und die statistischen Zusammenführungsdaten, die für einen Index mit der API „\_node/stats“ erfasst wurden, hohe Werte enthalten, liegt für den Index eine hohe Schreibintensität vor. Sehen Sie sich noch einmal die Punkte an, die im Abschnitt [Optimieren von Ressourcen für Indizierungsvorgänge](Optimieren von Ressourcen für Indizierungsvorgänge) in Bezug auf die Optimierung der Indizierungsleistung angegeben sind.
- Drosseln Sie Clientanwendungen, die Datenerfassungsvorgänge durchführen, und ermitteln Sie die damit verbundenen Auswirkungen auf die Leistung. Wenn dieser Ansatz zu einer erheblichen Verbesserung führt, sollten Sie entweder die Drosselung beibehalten oder das horizontale Hochskalieren durchführen, indem Sie die Auslastung für schreibintensive Indizes auf mehr Knoten verteilen. Weitere Informationen finden Sie im Dokument „Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure“.
- Wenn die Suchstatistiken für einen Index darauf hinweisen, dass Abfragen sehr lange dauern, sollten Sie sich die Optimierung der Abfragen ansehen. Weitere Informationen finden Sie im Abschnitt [Abfragenoptimierung][]. Beachten Sie, dass Sie die von den Suchstatistiken gemeldeten Werte *query\_time\_in\_millis* und *query\_total* verwenden können, um grobe Daten zur Effizienz der Abfragen zu berechnen. Mit der Gleichung *query\_time\_in\_millis*/*query\_total* können Sie die durchschnittliche Dauer für jede Abfrage ermitteln.

### Tools zum Überwachen von Elasticsearch

Für die tägliche Überwachung von Elasticsearch in der Produktion sind viele verschiedene Tools verfügbar. Für diese Tools werden normalerweise die zugrunde liegenden Elasticsearch-APIs verwendet, um Informationen zu sammeln und die Details so darzustellen, dass sie einfacher als unformatierte Daten verwendet werden können. Häufige Beispiele sind [Elasticsearch-Head][], [Bigdesk][], [Kopf][] und [Marvel][].

Elasticsearch-Head, Bigdesk und Kopf werden als Plug-Ins für die Elasticsearch-Software ausgeführt. Neuere Versionen von Marvel können separat ausgeführt werden, aber es muss [Kibana][] verwendet werden, damit eine Umgebung für die Datenerfassung und das Hosting vorhanden ist. Der Vorteil der Nutzung von Marvel mit Kibana besteht darin, dass Sie die Überwachung in einer anderen Umgebung als im Elasticsearch-Cluster implementieren können. So können Sie Probleme mit Elasticsearch untersuchen, was unter Umständen nicht möglich wäre, wenn die Überwachungstools unter der Elasticsearch-Software ausgeführt werden. Falls Elasticsearch beispielsweise wiederholt ausfällt oder sehr langsam ausgeführt wird, werden auch Tools beeinträchtigt, die als Elasticsearch-Plug-Ins ausgeführt werden. Dies erschwert die Überwachung und Diagnose.

Auf Betriebssystemebene können Sie Tools wie das Protokollanalysefeature (Log Analytics) der [Azure Operations Management Suite][] oder die [Azure-Diagnose mit dem Azure-Portal][] verwenden, um die Leistungsdaten für VMs zu erfassen, auf denen Elasticsearch-Knoten gehostet werden. Ein anderer Ansatz ist die Verwendung von [Logstash][] zum Erfassen von Leistungs- und Protokolldaten, Speichern dieser Informationen in einem separaten Elasticsearch-Cluster (verwenden Sie nicht denselben Cluster wie für Ihre Anwendung) und anschließenden Nutzen von Kibana zum Visualisieren der Daten. Weitere Informationen finden Sie unter [Microsoft Azure Diagnostics with ELK][] (Microsoft Azure-Diagnose mit ELK).

### Tools zum Testen der Elasticsearch-Leistung

Es sind auch andere Tools verfügbar, wenn Sie das Benchmarking für Elasticsearch durchführen oder einen Cluster einem Leistungstest unterziehen. Diese Tools sind für die Verwendung in einer Entwicklungs- oder Testumgebung bestimmt, nicht für die Nutzung in der Produktion. Ein Beispiel für ein häufig verwendetes Tool ist [Apache JMeter][].

JMeter wurde zum Durchführen von Benchmarking und anderen Auslastungstests genutzt, die in den verwandten Dokumenten dieses Artikels beschrieben werden. Im Dokument „Running Performance Tests on Elasticsearch Using JMeter“ (Ausführen von Leistungstests für Elasticsearch mit JMeter) ist ausführlich beschrieben, wie JMeter konfiguriert und verwendet wurde.

## Nächste Schritte
- [Elasticsearch: The Definitive Guide](https://www.elastic.co/guide/en/elasticsearch/guide/master/index.html) (Elasticsearch: Die ultimative Anleitung)

[Apache JMeter]: http://jmeter.apache.org/
[Apache Lucene]: https://lucene.apache.org/
[Automatisches Skalieren von Computern in einer VM-Skalierungsgruppe]: virtual-machines-vmss-walkthrough/
[Azure-Datenträgerverschlüsselung für virtuelle Windows- und Linux-IaaS-Computer (Vorschau)]: azure-security-disk-encryption/
[Azure Load Balancer]: load-balancer-overview/
[ExpressRoute]: expressroute-introduction/
[internen Load Balancer]: load-balancer-internal-overview/
[Größen für Virtual Machines]: virtual-machines-size-specs/

[Arbeitsspeicheranforderungen]: #memory-requirements
[Netzwerkanforderungen]: #network-requirements
[Knotenermittlung]: #node-discovery
[Abfragenoptimierung]: #query-tuning

[A Highly Available Cloud Storage Service with Strong Consistency]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx
[Azure Cloud-Plug-In]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Azure-Cloud-Plug-In]: https://www.elastic.co/blog/azure-cloud-plugin-for-elasticsearch
[Azure-Diagnose mit dem Azure-Portal]: https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/
[Azure Operations Management Suite]: https://www.microsoft.com/server-cloud/operations-management-suite/overview.aspx
[Azure-Schnellstartvorlagen]: https://azure.microsoft.com/documentation/templates/
[Bigdesk]: http://bigdesk.org/
[cat-APIs]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/cat.html
[Konfigurieren Sie bei Bedarf das Translog]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-translog.html
[benutzerdefinierte Routing]: https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-routing-field.html
[Doc Values]: https://www.elastic.co/guide/en/elasticsearch/guide/current/doc-values.html
[Elasticsearch]: https://www.elastic.co/products/elasticsearch
[Elasticsearch-Head]: https://mobz.github.io/elasticsearch-head/
[Elasticsearch.Net und NEST]: http://nest.azurewebsites.net/
[Elasticsearch Snapshot and Restore module]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html
[Faking Index per User with Aliases]: https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html
[field data circuit breaker]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#fielddata-circuit-breaker
[Force Merge]: https://www.elastic.co/guide/en/elasticsearch/reference/2.1/indices-forcemerge.html
[Gossip-Protokoll]: https://en.wikipedia.org/wiki/Gossip_protocol
[Kibana]: https://www.elastic.co/downloads/kibana
[Kopf]: https://github.com/lmenezes/elasticsearch-kopf
[Logstash]: https://www.elastic.co/products/logstash
[Mapping Explosion]: https://www.elastic.co/blog/found-crash-elasticsearch#mapping-explosion
[Marvel]: https://www.elastic.co/products/marvel
[Microsoft Azure Diagnostics with ELK]: https://github.com/mspnp/semantic-logging/tree/elk
[Monitoring Individual Nodes]: https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_monitoring_individual_nodes
[nginx]: http://nginx.org/en/
[Node-Client-API]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/node-client.html
[Optimize]: https://www.elastic.co/guide/en/elasticsearch/reference/1.7/indices-optimize.html
[PubNub Changes-Plug-In]: http://www.pubnub.com/blog/quick-start-realtime-geo-replication-for-elasticsearch/
[request circuit breaker]: https://www.elastic.co/guide/en/elasticsearch/reference/current/index-modules-fielddata.html#request-circuit-breaker
[Search Guard]: https://github.com/floragunncom/search-guard
[Shield]: https://www.elastic.co/products/shield
[Transport-Client-API]: https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/transport-client.html
[Tribe-Knoten]: https://www.elastic.co/blog/tribe-node
[Watcher]: https://www.elastic.co/products/watcher
[Zen]: https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html

<!---HONumber=AcomDC_0211_2016-->
