<properties
   pageTitle="Konfigurieren der Resilienz und Wiederherstellung unter Elasticsearch in Azure"
   description="Aspekte der Resilienz und Wiederherstellung für Elasticsearch"
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
   
# Konfigurieren der Resilienz und Wiederherstellung unter Elasticsearch in Azure

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

Ein wichtiges Feature von Elasticsearch ist die vorhandene Unterstützung in Bezug auf die Resilienz bei Knotenausfällen bzw. Netzwerkpartitions-Ereignissen. Die Replikation ist die offensichtlichste Möglichkeit zur Verbesserung der Resilienz eines Clusters. So kann Elasticsearch sicherstellen, dass auf unterschiedlichen Knoten mehr als eine Kopie eines Datenelements verfügbar ist, falls ein Knoten nicht mehr zugänglich sein sollte. Wenn ein Knoten vorübergehend nicht verfügbar ist, können andere Knoten, die Replikate der Daten des ausgefallenen Knotens enthalten, die fehlenden Daten bereitstellen, bis das Problem behoben ist. Falls es sich um ein umfangreicheres Problem handelt, kann der fehlende Knoten durch einen neuen Knoten ersetzt werden, und Elasticsearch kann die Daten von den Replikaten auf dem neuen Knoten wiederherstellen.

In diesem Artikel sind die Optionen für die Resilienz und Wiederherstellung zusammengefasst, die für Elasticsearch beim Hosten in Azure verfügbar sind. Außerdem werden einige wichtige Aspekte eines Elasticsearch-Clusters beschrieben, die Ihnen bekannt sein sollten, um die Wahrscheinlichkeit von Datenverlusten und höherem Zeitaufwand bei der Datenwiederherstellung zu verringern.

In diesem Artikel werden auch einige Beispieltests gezeigt. Sie wurden durchgeführt, um die Auswirkungen unterschiedlicher Arten von Fehlern in einem Elasticsearch-Cluster und die Reaktion des Systems bei der Wiederherstellung zu veranschaulichen.

Ein Elasticsearch-Cluster nutzt Replikate, um die Verfügbarkeit aufrechtzuerhalten und die Leseleistung zu verbessern. Replikate sollten auf anderen VMs als den VMs für die primären Shards gespeichert werden, die sie replizieren. Dies hat den folgenden Zweck: Wenn die VM, die einen Datenknoten hostet, ausfällt bzw. nicht mehr verfügbar ist, kann das System weiter funktionieren, indem die VMs mit den Replikaten verwendet werden.

## Verwenden von dedizierten Masterknoten

Ein Knoten in einem Elasticsearch-Cluster wird als Masterknoten ausgewählt. Der Zweck dieses Knotens ist die Durchführung von Verwaltungsvorgängen für den Cluster, z. B.:

- Erkennen ausgefallener Knoten und Wechseln zu Replikaten

- Neuanordnen von Shards, um die Workload von Knoten auszugleichen

- Wiederherstellen von Shards, wenn ein Knoten wieder online geschaltet wird

Sie sollten den Einsatz von dedizierten Masterknoten in kritischen Clustern erwägen und sicherstellen, dass drei dedizierte Knoten vorhanden sind, deren einzige Rolle die Rolle des Masters ist. Mit dieser Konfiguration wird die Menge an ressourcenintensiver Arbeit reduziert, die von diesen Knoten durchgeführt werden muss (sie speichern keine Daten und behandeln keine Abfragen), und die Stabilität des Clusters wird verbessert. Nur einer dieser Knoten wird ausgewählt, aber die anderen Knoten enthalten eine Kopie des Systemstatus und können einspringen, falls der ausgewählte Master ausfällt.

## Steuern der hohen Verfügbarkeit mit Azure – Updatedomänen und Fehlerdomänen 

Für unterschiedliche VMs kann dieselbe physische Hardware gemeinsam genutzt werden. In einem Azure-Rechenzentrum kann ein einzelnes Rack eine Reihe von VMs hosten, die dann alle über eine gemeinsame Stromversorgung und einen gemeinsamen Netzwerkswitch verfügen. Vom Ausfall eines einzelnen Racks können daher mehrere VMs betroffen sein. In Azure wird das Konzept der Fehlerdomänen (FDs) verwendet, um dieses Risiko möglichst breit zu streuen. Eine FD entspricht grob gesagt einer Gruppe von VMs, die sich in demselben Rack befinden. Um sicherzustellen, dass es bei einem Ausfall auf Rack-Ebene nicht zu einem gleichzeitigen Ausfall eines Knotens und der Knoten kommt, die alle seine Replikate enthalten, sollten Sie darauf achten, dass die VMs über FDs verteilt sind.

VMs können auch vom [Azure Fabric Controller](https://azure.microsoft.com/documentation/videos/fabric-controller-internals-building-and-updating-high-availability-apps/) heruntergefahren werden, um geplante Wartungsschritte und Upgrades des Betriebssystems vorzunehmen. Azure ordnet VMs Updatedomänen (UDs) zu. Bei einem geplanten Wartungsereignis sind jeweils nur VMs in einer einzelnen UD betroffen. VMs in anderen UDs werden weiter ausgeführt, bis die VMs in der UD, die aktualisiert wird, wieder online geschaltet werden. Daher müssen Sie auch sicherstellen, dass VMs, die Knoten und ihre Replikate hosten, nach Möglichkeit immer unterschiedlichen UDs angehören.

> [AZURE.NOTE] Weitere Informationen zu Fehlerdomänen und Updatedomänen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer][].

Sie können eine VM nicht explizit einer bestimmten UD und FD zuordnen. Diese Zuordnung wird von Azure gesteuert, wenn VMs erstellt werden. Weitere Informationen finden Sie im Dokument [Verwalten der Verfügbarkeit virtueller Computer][]. Sie können aber angeben, dass VMs als Teil einer Verfügbarkeitsgruppe erstellt werden. VMs, die in derselben Verfügbarkeitsgruppe enthalten sind, werden auf UDs und FDs verteilt. Wenn Sie VMs manuell erstellen, ordnet Azure jeder Verfügbarkeitsgruppe zwei FDs und fünf UDs zu. Die virtuellen Computer werden FDs und UDs wie folgt zugeordnet (in einem entsprechenden Kreislauf, wenn weitere VMs bereitgestellt werden):

- Die erste VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in der ersten FD (FD 0) und der ersten UD (UD 0) angeordnet.
- Die zweite VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in FD 1 und UD 1 angeordnet.
- Die dritte VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in FD 0 und UD 2 angeordnet. Die vierte VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in FD 1 und UD 3 angeordnet.
- Die fünfte VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in FD 0 und UD 4 angeordnet.
- Die sechste VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in FD 1 und UD 0 angeordnet.
- Die siebte VM, die in der Verfügbarkeitsgruppe bereitgestellt wird, wird in FD 0 und UD 1 angeordnet.

> [AZURE.IMPORTANT] Wenn Sie VMs mit dem Azure Resource Manager (ARM) erstellen, können jeder Verfügbarkeitsgruppe bis zu 3 FDs und 20 UDs zugeordnet werden. Dies ist ein guter Grund für die Verwendung von Azure Resource Manager.

Generell gilt: Ordnen Sie alle VMs, die dem gleichen Zweck dienen, in derselben Verfügbarkeitsgruppe an. Erstellen Sie aber unterschiedliche Verfügbarkeitsgruppen für VMs, die unterschiedlichen Funktionen dienen. Für Elasticsearch bedeutet dies, dass Sie mindestens die Erstellung separater Verfügbarkeitsgruppen für folgende Elemente erwägen sollten:

- VMs, die Datenknoten hosten
- VMs, die Clientknoten hosten (falls zutreffend)
- VMs, die Masterknoten hosten

Darüber hinaus sollten Sie sicherstellen, dass jeder Knoten in einem Cluster über die Updatedomäne und Fehlerdomäne informiert ist, der er angehört. Mit diesen Informationen kann sichergestellt werden, dass Elasticsearch Shards und deren Replikate nicht in denselben Fehler- und Updatedomänen erstellt. So wird die Wahrscheinlichkeit verringert, dass ein Shard und seine Replikate gleichzeitig heruntergefahren werden. Sie können einen Elasticsearch-Knoten konfigurieren, um die Hardwareverteilung des Clusters zu spiegeln. Konfigurieren Sie hierfür die [Shard Allocation Awareness](https://www.elastic.co/guide/en/elasticsearch/reference/current/allocation-awareness.html#allocation-awareness) (Bewusstsein für Shardzuordnung). Sie können beispielsweise wie folgt zwei benutzerdefinierte Knotenattribute mit den Namen *faultDomain* und *updateDomain* in der Datei „elasticsearch.yml“ definieren:

```yaml
node.faultDomain: \${FAULTDOMAIN}
node.updateDomain: \${UPDATEDOMAIN}
```

In diesem Fall werden die Attribute mit den Werten festgelegt, die in den Umgebungsvariablen *\\${FAULTDOMAIN}* und *\\${UPDATEDOMAIN}* enthalten sind, wenn Elasticsearch gestartet wird. Sie müssen der Datei „Elasticsearch.yml“ auch die folgenden Werte hinzufügen, um anzugeben, dass die Attribute *faultDomain* und *updateDomain* über die Zuordnung informiert sind. Außerdem müssen Sie für diese Attribute die Gruppe der zulässigen Werte angeben:

```yaml
cluster.routing.allocation.awareness.force.updateDomain.values: 0,1,2,3,4
cluster.routing.allocation.awareness.force.faultDomain.values: 0,1
cluster.routing.allocation.awareness.attributes: updateDomain, faultDomain
```

Sie können das Bewusstsein für die Shardzuordnung zusammen mit [Shard Allocation Filtering](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/shard-allocation-filtering.html#shard-allocation-filtering) (Filterung für Shardzuordnung) verwenden, um explizit anzugeben, welche Knoten Shards für einen bestimmten Index hosten können.

Wenn Sie eine Skalierung über die Anzahl an FDs und UDs in einer Verfügbarkeitsgruppe hinaus durchführen müssen, können Sie VMs in zusätzlichen Verfügbarkeitsgruppen erstellen. Sie müssen sich aber darüber im Klaren sein, dass Knoten in unterschiedlichen Verfügbarkeitsgruppen zu Wartungszwecken gleichzeitig heruntergefahren werden können. Versuchen Sie, Folgendes sicherzustellen: Jedes Shard und mindestens eines seiner Replikate sollten sich in derselben Verfügbarkeitsgruppe befinden.

> [AZURE.NOTE] Derzeit gilt eine Obergrenze von 100 VMs pro Verfügbarkeitsgruppe. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements, -Dienste und -Kontingente sowie allgemeine Beschränkungen](../azure-subscription-service-limits.md).

### Sichern und Wiederherstellen

Die Nutzung von Replikaten stellt keinen vollständigen Schutz vor schwerwiegenden Fehlern dar (z. B. dem versehentlichen Löschen des gesamten Clusters). Sie sollten darauf achten, dass Sie die Daten in einem Cluster regelmäßig sichern und dass Sie über eine bewährte Strategie zur Wiederherstellung des Systems aus diesen Sicherungen verfügen.

Verwenden Sie die [APIs für Momentaufnahmen und die Wiederherstellung](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-snapshots.html) von Elasticsearch, um Indizes zu sichern und wiederherzustellen. Momentaufnahmen können in einem freigegebenen Dateisystem gespeichert werden. Alternativ hierzu sind Plug-Ins verfügbar, mit denen Momentaufnahmen in HDFS ([HDFS-Plug-In](https://github.com/elasticsearch/elasticsearch-hadoop/tree/master/repository-hdfs)) oder Azure Storage ([Azure-Cloud-Plug-In](https://github.com/elasticsearch/elasticsearch-cloud-azure#azure-repository)) geschrieben werden können.

Berücksichtigen Sie beim Auswählen des Speichermechanismus für Momentaufnahmen die folgenden Punkte:

- Sie können [Azure File Storage](https://azure.microsoft.com/services/storage/files/) verwenden, um ein freigegebenes Dateisystem zu implementieren, auf das von allen Knoten zugegriffen werden kann.

- Verwenden Sie das HDFS-Plug-In nur, wenn Sie Elasticsearch zusammen mit Hadoop ausführen.

- Für das HDFS-Plug-In ist es erforderlich, dass Sie den Java Security Manager deaktivieren, der in der Elasticsearch-Instanz der JVM ausgeführt wird.

- Das HDFS-Plug-In unterstützt alle HDFS-kompatiblen Dateisysteme, sofern die richtige Hadoop-Konfiguration mit Elasticsearch verwendet wird.

  
## Behandeln von zeitweiligen Verbindungsproblemen zwischen Knoten

Kurze Netzwerkausfälle, VM-Neustarts nach einer routinemäßigen Wartung im Rechenzentrum und ähnliche Ereignisse können dazu führen, dass Knoten vorübergehend nicht verfügbar sind. In diesen Situationen, in denen das Ereignis eher kurzzeitig auftritt, tritt der erhöhte Aufwand für das Ausgleichen der Shards zweimal kurz nacheinander auf (einmal bei der Erkennung des Ausfalls und dann erneut, wenn der Knoten für den Master sichtbar wird). Dies kann zu erheblichem Mehraufwand führen, der sich auf die Leistung auswirkt. Sie können für die vorübergehende Nichtverfügbarkeit von Knoten verhindern, dass der Cluster vom Master neu ausgerichtet wird, indem Sie die *delayed\_timeout*-Eigenschaft für einen Index oder alle Indizes festlegen. Im Beispiel unten wird die Verzögerung auf fünf Minuten festgelegt:

```http
PUT /_all/settings
{
	"settings": {
    "index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

Weitere Informationen finden Sie unter [Delaying allocation when a node leaves](https://www.elastic.co/guide/en/elasticsearch/reference/current/delayed-allocation.html) (Verzögerung der Zuordnung beim Ausscheiden eines Knotens).

In einem Netzwerk, das anfällig für Unterbrechungen ist, können Sie auch die Parameter ändern, mit denen ein Master konfiguriert wird. So können Sie erkennen, wenn ein anderer Knoten nicht mehr verfügbar ist. Diese Parameter sind Teil des Moduls für die [Zen Discovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-zen.html#modules-discovery-zen) (Zen-Ermittlung) von Elasticsearch. Sie können die Parameter in der Datei „Elasticsearch.yml“ festlegen. Mit dem Parameter *discovery.zen.fd.ping.retries* wird beispielsweise angegeben, wie oft ein Masterknoten versucht, einen anderen Knoten per Ping zu erreichen, bevor dieser als ausgefallen eingestuft wird. Dieser Parameter ist standardmäßig auf „3“ festgelegt, aber Sie können dies wie folgt ändern:

```yaml
discovery.zen.fd.ping_retries: 6
```

## Steuern der Wiederherstellung

Wenn die Verbindung mit einem Knoten nach einem Ausfall wiederhergestellt wird, müssen alle Shards auf diesem Knoten wiederhergestellt werden, damit sie auf dem neuesten Stand sind. Standardmäßig stellt Elasticsearch Shards in der folgenden Reihenfolge wieder her:

- Nach umgekehrtem Erstellungsdatum des Index. Neuere Indizes werden vor älteren Indizes wiederhergestellt.

- Nach umgekehrtem Indexnamen. Indizes, deren Namen alphanumerisch höher als andere Namen sind, werden zuerst wiederhergestellt.

Falls einige Indizes kritischer als andere Indizes sind, aber diese Kriterien nicht erfüllen, können Sie die Vorrangigkeit von Indizes außer Kraft setzen, indem Sie die *index.priority*-Eigenschaft festlegen. Indizes mit einem höheren Wert für diese Eigenschaft werden vor Indizes wiederhergestellt, die einen niedrigeren Wert haben:

```http
PUT low_priority_index
{
	"settings": {
		"index.priority": 1
	}
}

PUT high_priority_index
{
	"settings": {
		"index.priority": 10
	}
}
```

Weitere Informationen finden Sie unter [Index Recovery Prioritization](https://www.elastic.co/guide/en/elasticsearch/reference/2.0/recovery-prioritization.html#recovery-prioritization) (Priorisierung der Indexwiederherstellung).

Sie können den Wiederherstellungsprozess für einen oder mehrere Indizes überwachen, indem Sie die *\_recovery*-API verwenden:

```http
GET /high_priority_index/_recovery?pretty=true
```

Weitere Informationen finden Sie unter [Indices Recovery](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-recovery.html#indices-recovery) (Wiederherstellung von Indizes).

> [AZURE.NOTE] Ein Cluster mit Shards, für die eine Wiederherstellung erforderlich ist, hat den Status *Gelb*. Dies zeigt an, dass derzeit nicht alle Shards verfügbar sind. Wenn alle Shards verfügbar sind, sollte der Cluster den Status *Grün* haben. Der Status *Rot* eines Clusters deutet darauf hin, dass ein oder mehrere Shards physisch nicht vorhanden sind. Es ist unter Umständen erforderlich, Daten aus einer Sicherung wiederherzustellen.

## Verhindern des Split-Brain-Problems 

Ein Split-Brain-Problem kann auftreten, wenn die Verbindungen zwischen Knoten ausfallen. Wenn ein Masterknoten für einen Teil des Clusters nicht mehr erreichbar ist, wird im noch erreichbaren Netzwerksegment eine „Wahl“ durchgeführt, und ein anderer Knoten wird zum Master. In einem schlecht konfigurierten Cluster ist es möglich, dass jeder Teil des Clusters über unterschiedliche Master verfügt. Dies kann zu Inkonsistenzen oder Beschädigungen von Daten führen. Dieses Phänomen wird als *Split-Brain*-Problem bezeichnet.

Sie können die Wahrscheinlichkeit für das Auftreten des Split-Brain-Problems verringern, indem Sie die *minimum\_master\_nodes*-Eigenschaft des Ermittlungsmoduls in der Datei „elasticsearch.yml“ konfigurieren. Mit dieser Eigenschaft wird angegeben, wie viele Knoten verfügbar sein müssen, um die Wahl eines Masters zu ermöglichen. Im folgenden Beispiel wird der Wert dieser Eigenschaft auf „2“ festgelegt:

```yaml
discovery.zen.minimum_master_nodes: 2
```

Dieser Wert sollte auf die niedrigste Mehrheit der Anzahl von Knoten festgelegt werden, die die Masterrolle übernehmen können. Wenn Ihr Cluster beispielsweise drei Masterknoten hat, sollte *minimum\_master\_nodes* auf „2“ festgelegt werden. Wenn Sie fünf Masterknoten haben, sollte *minimum\_master\_nodes* auf „3“ festgelegt werden. Idealerweise sollten Sie über eine ungerade Zahl von Masterknoten verfügen.

**Hinweis:** Es kann zu einem Split-Brain-Problem kommen, wenn mehrere Masterknoten in demselben Cluster gleichzeitig gestartet werden. Dieser Fall tritt zwar nur selten ein, aber Sie können ihn verhindern, indem Sie Knoten nacheinander jeweils mit einer kurzen Verzögerung (fünf Sekunden) starten.

## Behandeln von parallelen Updates

Wenn Sie ein Softwareupgrade für Knoten selbst durchführen (z. B. das Migrieren zu einer neueren Version oder Durchführen eines Patchvorgangs), müssen Sie unter Umständen an einzelnen Knoten arbeiten. Hierbei ist es erforderlich, sie offline zu schalten, während der restliche Cluster verfügbar bleibt. Erwägen Sie in dieser Situation die Implementierung des folgenden Prozesses:

Stellen Sie sicher, dass die Neuzuordnung von Shards ausreichend verzögert erfolgt. So kann verhindert werden, dass der ausgewählte Master Shards eines fehlenden Knotens für den gesamten restlichen Cluster neu anordnet. Die Neuzuordnung der Shards wird standardmäßig um eine Minute verzögert, aber Sie können die Dauer erhöhen, wenn ein Knoten voraussichtlich für einen längeren Zeitraum nicht verfügbar ist. Im folgenden Beispiel wird die Verzögerung auf fünf Minuten erhöht:

```http
PUT /_all/_settings
{
	"settings": {
		"index.unassigned.node_left.delayed_timeout": "5m"
	}
}
```

> [AZURE.IMPORTANT] Sie können die Neuzuordnung von Shards auch ganz deaktivieren, indem Sie *cluster.routing.allocation.enable* für den Cluster auf *none* festlegen. Sie sollten diesen Ansatz aber vermeiden, wenn voraussichtlich neue Indizes erstellt werden, während der Knoten offline ist. Dies kann nämlich dazu führen, dass die Indexzuordnung fehlschlägt und der Cluster den Status „Rot“ erhält.

Beenden Sie Elasticsearch auf dem Knoten, der gewartet werden soll. Wenn Elasticsearch als Dienst ausgeführt wird, können Sie den Prozess unter Umständen auf kontrollierte Weise unterbrechen, indem Sie einen Befehl des Betriebssystems verwenden. Das folgende Beispiel verdeutlicht, wie Sie den Elasticsearch-Dienst auf einem einzelnen Knoten mit Ubuntu anhalten:

```bash
service elasticsearch stop
```

Alternativ hierzu können Sie auch die Shutdown-API zum Herunterfahren direkt auf dem Knoten verwenden:

```http
POST /_cluster/nodes/_local/_shutdown
```

Führen Sie die erforderliche Wartung für den Knoten durch, und starten Sie den Knoten dann neu, und warten Sie, bis er dem Cluster beigetreten ist.

Erneutes Aktivieren der Shardzuordnung:

```http
PUT /_cluster/settings
{
	"transient": {
		"cluster.routing.allocation.enable": "all"
	}
}
```

> [AZURE.NOTE] Wenn Sie mehr als einen Knoten warten müssen, können Sie die Schritte 2, 3 und 4 auf jedem Knoten wiederholen, bevor Sie die Shardzuordnung wieder aktivieren.

Falls möglich, können Sie die Indizierung neuer Daten während dieses Prozesses anhalten. Dies trägt zur Verringerung der Wiederherstellungsdauer bei, wenn Knoten wieder online geschaltet werden und dem Cluster wieder beitreten.

Vermeiden Sie automatisierte Updates von Elementen, z. B. der JVM (idealerweise sollten Sie automatische Updates für diese Elemente deaktivieren). Dies gilt vor allem, wenn Sie Elasticsearch unter Windows ausführen. Der Java-Update-Agent kann die aktuelle Version von Java automatisch herunterladen, aber es kann erforderlich sein, Elasticsearch neu zu starten, damit das Update wirksam wird. Dies kann je nach Konfiguration des Java-Update-Agents zu einem unkoordinierten vorübergehenden Verlust von Knoten führen. Außerdem kann dies dazu führen, dass auf unterschiedlichen Instanzen von Elasticsearch in demselben Cluster verschiedene Versionen der JVM ausgeführt werden. Dies kann der Grund für das Auftreten von Kompatibilitätsproblemen sein.

## Testen und Analysieren der Elasticsearch-Resilienz und -Wiederherstellung

In diesem Abschnitt wird eine Reihe von Tests beschrieben, die durchgeführt wurden, um die Resilienz und Wiederherstellung eines Elasticsearch-Clusters mit drei Datenknoten und drei Masterknoten auszuwerten.

Vier Szenarien wurden getestet:

1.  Ausfall und Neustart eines Knotens ohne Datenverlust: Ein Datenknoten wird beendet und nach fünf Minuten neu gestartet. Elasticsearch wurde so konfiguriert, dass fehlende Shards während dieses Zeitraums nicht neu zugeordnet wurden, damit kein zusätzlicher E/A-Aufwand für der Verschieben von Shards anfällt. Wenn der Knoten neu gestartet wird, bringt der Wiederherstellungsprozess die Shards auf diesem Knoten wieder auf den neuesten Stand.

2.  Ausfall eines Knotens mit schwerwiegendem Datenverlust: Ein Datenknoten wird beendet, und die darauf enthaltenen Daten werden gelöscht, um einen schwerwiegenden Datenträgerausfall zu simulieren. Der Knoten wird dann neu gestartet (nach fünf Minuten) und soll quasi als Ersatz für den ursprünglichen Knoten fungieren. Für den Wiederherstellungsprozess ist die Neuerstellung der fehlenden Daten für diesen Knoten erforderlich, und unter Umständen müssen Shards neu angeordnet werden, die sich auf anderen Knoten befinden.

3.  Ausfall und Neustart eines Knotens ohne Datenverlust, aber mit Neuzuordnung von Shards: Ein Datenknoten wird beendet, und die darauf enthaltenen Shards werden neu auf andere Knoten verteilt. Der Knoten wird dann neu gestartet, und es werden weitere Schritte für die Neuzuordnung ausgeführt, um für den Cluster eine gute Balance zu erzielen.

4.  Parallele Updates: Jeder Knoten im Cluster wird beendet und nach kurzer Zeit neu gestartet, um zu simulieren, wie Computer nach einem Softwareupdate neu gestartet werden. Es wird jeweils nur ein Knoten beendet. Shards werden nicht neu zugeordnet, während ein Knoten heruntergefahren ist.

Für jedes Szenario wurde die gleiche Workload verwendet, die eine Mischung aus Datenerfassungsaufgaben, Aggregationen und Filterabfragen umfasste, während Knoten offline geschaltet und wiederhergestellt wurden. Für die Vorgänge zum Masseneinfügen der Workload wurden jeweils 1.000 Dokumente gespeichert, und die Vorgänge wurden für einen Index durchgeführt, während für die Aggregationen und Filterabfragen ein separater Index mit mehreren Millionen Dokumenten verwendet wurde. Das Ziel war hierbei, die Leistung der Abfragen separat von den Masseneinfügungen bewerten zu können. Jeder Index bestand aus fünf Shards und einem Replikat.

In den folgenden Abschnitten sind die Ergebnisse dieser Tests zusammengefasst. Es wird auf alle Leistungsverschlechterungen hingewiesen, die auftreten, während ein Knoten offline ist oder wiederhergestellt wird, sowie auf alle gemeldeten Fehler. Die Ergebnisse werden grafisch dargestellt. Es werden die Punkte hervorgehoben, an denen ein oder mehrere Knoten fehlen. Außerdem ist eine Schätzung vorhanden, wie lange das System für die vollständige Wiederherstellung und das Erreichen einer ähnlichen Leistungsebene benötigt, die vor dem Offlineschalten der Knoten geherrscht hat.

> [AZURE.NOTE] Die Testumgebungen, die zum Durchführen dieser Tests verwendet wurden, sind online verfügbar. Sie können diese Testumgebungen anpassen und nutzen, um die Resilienz und Wiederherstellbarkeit Ihrer eigenen Clusterkonfigurationen zu überprüfen. Weitere Informationen finden Sie unter [Ausführen der automatisierten Elasticsearch-Resilienztests][].

## Ausfall und Neustart eines Knotens ohne Datenverlust: Ergebnisse

<!-- TODO; reformat this pdf for display inline -->

Die Ergebnisse dieses Tests sind in der Datei [ElasticsearchRecoveryScenario1.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario1.pdf) enthalten. Die Graphen zeigen das Leistungsprofil der Workload und die physischen Ressourcen für jeden Knoten im Cluster. Der Anfangsteil des Graphen zeigt, dass das System ca. 20 Minuten lang normal ausgeführt wird. Danach wird Knoten 0 für fünf Minuten heruntergefahren und anschließend neu gestartet. Die statistischen Daten für weitere 20 Minuten sind angegeben. Die Wiederherstellung und Stabilisierung des Systems dauert ca. zehn Minuten. Dies wird durch die Transaktionsraten und Antwortzeiten für die unterschiedlichen Workloads dargestellt.

Beachten Sie folgende Punkte:

- Während des Tests wurden keine Fehler gemeldet. Es sind keine Daten verloren gegangen, und alle Vorgänge wurden erfolgreich abgeschlossen.

- Die Transaktionsraten für alle drei Vorgangsarten (Masseneinfügung, Aggregatabfrage und Filterabfrage) fielen ab, und die durchschnittlichen Antwortzeiten stiegen an, während Knoten 0 offline war.

- Während des Wiederherstellungszeitraums erreichten die Transaktionsraten und Antwortzeiten für die Aggregatabfrage- und Filterabfragevorgänge allmählich wieder die ursprünglichen Werte. Die Leistung für das Masseneinfügen erholte sich für einen Moment, bevor sie sich verschlechterte. Dies liegt aber vermutlich am Volumen der Daten, was dazu führte, dass der vom Vorgang für das Masseneinfügen verwendete Index anwuchs. Es ist erkennbar, dass die Transaktionsraten für diesen Vorgang sich schon verschlechtern, bevor Knoten 0 offline geschaltet wird.

- Der Graph für die CPU-Auslastung von Knoten 0 zeigt eine verringerte Aktivität während der Wiederherstellungsphase. Dies liegt an der erhöhten Datenträger- und Netzwerkaktivität, die vom Wiederherstellungsmechanismus verursacht wird. Der Knoten muss alle Daten wiederbeschaffen, die er im Offlinezustand verpasst hat, und seine Shards aktualisieren.

- Die Shards für die Indizes werden nicht genau gleich auf alle Knoten verteilt. Es sind zwei Indizes vorhanden, die jeweils fünf Shards und ein Replikat umfassen. Insgesamt ergeben sich also 20 Shards. Zwei Knoten enthalten daher sechs Shards, und die anderen beiden enthalten jeweils sieben Shards. Dies ist an den Graphen für die CPU-Auslastung während des anfänglichen Zeitraums von 20 Minuten erkennbar. Knoten 0 ist weniger stark als die anderen beiden Knoten ausgelastet. Nach Abschluss der Wiederherstellung scheinen Umstellungen durchgeführt zu werden, da Knoten 2 jetzt zum weniger stark ausgelasteten Knoten wird.

    
## Ausfall eines Knotens mit schwerwiegendem Datenverlust: Ergebnisse

<!-- TODO; reformat this pdf for display inline -->

Die Ergebnisse dieses Tests sind in der Datei [ElasticsearchRecoveryScenario2.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario2.pdf) dargestellt. Wie beim ersten Test auch zeigt der Anfangsteil des Graphen, dass das System ca. 20 Minuten lang normal ausgeführt wird. Danach wird Knoten 0 für fünf Minuten heruntergefahren. Während dieses Zeitraums werden die Elasticsearch-Daten dieses Knotens vor dem Neustart entfernt, um einen schwerwiegenden Datenverlust zu simulieren. Die vollständige Wiederherstellung dauert scheinbar 12 bis 15 Minuten, bevor das Leistungsniveau, das vor der Wiederherstellung geherrscht hat, wieder erreicht wird.

Beachten Sie folgende Punkte:

- Während des Tests wurden keine Fehler gemeldet. Es sind keine Daten verloren gegangen, und alle Vorgänge wurden erfolgreich abgeschlossen.

- Die Transaktionsraten für alle drei Vorgangsarten (Masseneinfügung, Aggregatabfrage und Filterabfrage) fielen ab, und die durchschnittlichen Antwortzeiten stiegen an, während Knoten 0 offline war. An diesem Punkt ähnelt das Leistungsprofil des Tests dem ersten Szenario. Dies ist nicht überraschend, weil die Szenarien bis zu diesem Punkt identisch sind.

- Während des Wiederherstellungszeitraums wurden die vorherigen Transaktionsraten und Antwortzeiten wieder erreicht, auch wenn für die Werte dieses Mal deutlich mehr Volatilität zu beobachten war. Dies liegt wahrscheinlich am zusätzlichen Aufwand, den die Knoten im Cluster betreiben, indem sie die Daten zum Wiederherstellen der fehlenden Shards bereitstellen. Dieser zusätzliche Arbeitsaufwand ist anhand der Graphen zur CPU-Auslastung, Datenträgeraktivität und Netzwerkaktivität klar erkennbar.

- Der Graph für die CPU-Auslastung der Knoten 0 und 1 zeigt eine reduzierte Aktivität während der Wiederherstellungsphase. Dies liegt an der vermehrten Datenträger- und Netzwerkaktivität, die durch den Wiederherstellungsprozess verursacht wird. Im ersten Szenario zeigt nur der wiederhergestellte Knoten dieses Verhalten. Aber bei diesem Szenario ist die Wahrscheinlichkeit hoch, dass der Großteil der fehlenden Daten für Knoten 0 von Knoten 1 wiederhergestellt wird.

- Die E/A-Aktivität für Knoten 0 ist im Vergleich zum ersten Szenario niedriger. Der Grund hierfür können die E/A-Vorteile aufgrund des einfachen Kopierens der Daten für ein gesamtes Shard sein, da nicht mehrere kleinere E/A-Anforderungen erforderlich sind, um ein vorhandenes Shard auf den neuesten Stand zu bringen.

- Die Netzwerkaktivität für alle drei Knoten deutet auf Aktivitätsspitzen hin, wenn Daten zwischen Knoten übertragen und empfangen werden. In Szenario 1 hat nur Knoten 0 so viel Netzwerkaktivität gezeigt, aber diese Aktivität hielt über einen längeren Zeitraum an. Dieser Unterschied kann wiederum auf die Vorteile zurückzuführen sein, die sich beim Übertragen der gesamten Daten für ein Shard als einzelne Anforderung ergeben, anstatt beim Wiederherstellen eines Shards viele kleinere Anforderungen empfangen zu müssen.

## Ausfall eines Knotens und Neustart mit Neuzuordnung von Shards: Ergebnisse

<!-- TODO; reformat this pdf for display inline -->

Die Ergebnisse dieses Tests sind in der Datei [ElasticsearchRecoveryScenario3.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario3.pdf) enthalten. Wie beim ersten Test auch zeigt der Anfangsteil der Graphen, dass das System ca. 20 Minuten lang normal ausgeführt wird. Danach wird Knoten 0 für fünf Minuten heruntergefahren. An diesem Punkt versucht der Elasticsearch-Cluster, die fehlenden Shards neu zu erstellen und die Shards gleichmäßig auf die verbleibenden Knoten zu verteilen. Nach fünf Minuten wird Knoten 0 wieder online geschaltet, und der Cluster muss die Verteilung der Shards erneut durchführen. Die ursprüngliche Leistung wird nach 12 bis 15 Minuten wieder erreicht.

Beachten Sie folgende Punkte:

- Während des Tests wurden keine Fehler gemeldet. Es sind keine Daten verloren gegangen, und alle Vorgänge wurden erfolgreich abgeschlossen.

- Die Transaktionsraten für alle drei Vorgangsarten (Masseneinfügung, Aggregatabfrage und Filterabfrage) fielen ab, und die durchschnittlichen Antwortzeiten stiegen im Vergleich zu den beiden vorherigen Tests deutlich an, während Knoten 0 offline war. Dies liegt an der erhöhten Clusteraktivität, die beim Neuerstellen der fehlenden Shards und beim Neuverteilen im Cluster auftritt. Es ist an den erhöhten Werten für die Datenträger- und Netzwerkaktivität für die Knoten 1 und 2 in diesem Zeitraum erkennbar.

- Während des Zeitraums nach dem erneuten Onlineschalten von Knoten 0 bleiben die Transaktionsraten und Antwortzeiten volatil.

- Die Graphen für die CPU-Auslastung und die Datenträgeraktivität von Knoten 0 zeigen während der Wiederherstellungsphase zu Beginn eine deutlich reduzierte Aktivität. Der Grund ist, dass Knoten 0 an diesem Punkt keine Daten bereitstellt. Nach einem Zeitraum von ca. fünf Minuten zeigt der Knoten dann eine hohe Aktivität, und die Werte für Netzwerk-, Datenträger- und CPU-Aktivität steigen plötzlich an. Dies liegt wahrscheinlich daran, dass der Cluster Shards neu auf die Knoten verteilt. Knoten 0 zeigt anschließend wieder normale Aktivität.
  
## Parallele Updates: Ergebnisse

<!-- TODO; reformat this pdf for display inline -->

Die Ergebnisse dieses Tests in der Datei [ElasticsearchRecoveryScenario4.pdf](https://github.com/mspnp/azure-guidance/blob/master/figures/Elasticsearch/ElasticsearchRecoveryScenario4.pdf) zeigen, wie die einzelnen Knoten offline geschaltet und dann nacheinander wieder hochgefahren werden. Jeder Knoten wird für fünf Minuten heruntergefahren, bevor er neu gestartet wird. An diesem Punkt wird der nächste Knoten der Sequenz gestoppt.

Beachten Sie folgende Punkte:

- Während jeder Knoten neu gestartet wird, bleibt die Leistung im Hinblick auf Durchsatz und Antwortzeiten verhältnismäßig gleich.

- Die Datenträgeraktivität steigt für jeden Knoten kurz an, wenn er wieder online geschaltet wird. Dies liegt wahrscheinlich daran, dass beim Wiederherstellungsprozess ein Rollforward für alle Änderungen durchgeführt wird, die aufgetreten sind, während der Knoten heruntergefahren war.

- Wenn ein Knoten offline geschaltet wird, treten für die verbleibenden Knoten Spitzen bei der Netzwerkaktivität auf. Spitzen treten auch auf, wenn ein Knoten neu gestartet wird.

- Nachdem der letzte Knoten wiederhergestellt wurde, beginnt für das System ein Zeitraum erheblicher Volatilität. Dies liegt wahrscheinlich daran, dass während des Wiederherstellungsvorgangs Änderungen für alle Knoten synchronisiert werden müssen. Außerdem muss sichergestellt werden, dass alle Replikate und die dazugehörigen Shards konsistent sind. An einem Punkt führen diese Vorgänge dazu, dass für mehrere aufeinanderfolgende Masseneinfügungen eine Zeitüberschreitung und ein Fehler auftreten. Die hierbei gemeldeten Fehler lauteten:

```
Failure -- BulkDataInsertTest17(org.apache.jmeter.protocol.java.sampler.JUnitSampler$AnnotatedTestCase): java.lang.AssertionError: failure in bulk execution:
[1]: index [systwo], type [logs], id [AVEg0JwjRKxX_sVoNrte], message [UnavailableShardsException[[systwo][2] Primary shard is not active or isn't assigned to a known node. Timeout: [1m], request: org.elasticsearch.action.bulk.BulkShardRequest@787cc3cd]]

```

Bei den nachfolgenden Experimenten wurde ermittelt, dass die Einführung einer Verzögerung von einigen Minuten zwischen dem Durchlaufen der einzelnen Knoten zur Beseitigung dieses Fehlers führte. Die Ursache war also mit großer Wahrscheinlichkeit der Konflikt zwischen dem Wiederherstellungsprozess, bei dem mehrere Knoten gleichzeitig wiederhergestellt werden sollten, und den Masseneinfügungen, bei denen Tausende von neuen Dokumenten gespeichert werden sollten.


## Zusammenfassung

Die durchgeführten Tests haben Folgendes ergeben:

- Elasticsearch wies eine hohe Resilienz gegenüber den gängigsten Fehlermodi auf, die für einen Cluster auftreten können.

- Mit Elasticsearch kann die Wiederherstellung schnell durchgeführt werden, wenn für einen gut aufgebauten Cluster ein schwerwiegender Datenverlust auf einem Knoten auftritt. Dies kann passieren, wenn Sie für Elasticsearch das Speichern von Daten im flüchtigen Speicher konfigurieren und der Knoten nach einem Neustart dann neu bereitgestellt wird. Diese Ergebnisse zeigen, dass auch in diesem Fall die Risiken der Nutzung von flüchtigem Speicher meistens durch die Leistungsvorteile aufgewogen werden, die diese Art von Speicher bietet.

- Bei den ersten drei Szenarien traten bei gleichzeitigen Masseneinfügungs-, Aggregationsabfrage- und Filterabfrage-Workloads keine Fehler auf, während ein Knoten offline geschaltet und wiederhergestellt wurde.

- Nur bei Szenario 4 kam es zu einem potenziellen Datenverlust, und dieser Verlust wirkte sich nur auf neue Daten aus, die hinzugefügt wurden. Für Anwendungen, die eine Datenerfassung durchführen, ist es ratsam, diesem Risiko durch den Versuch zu begegnen, fehlgeschlagene Einfügevorgänge zu wiederholen. Gemeldete Fehler dieser Art sind nämlich in der Regel vorübergehender Natur.

- Die Ergebnisse von Test 4 zeigen auch, dass die Leistung beim Durchführen der geplanten Wartung für Knoten in einem Cluster positiv beeinflusst wird, wenn Sie zwischen zwei Knoten eine Pause von mehreren Minuten einhalten. In einer ungeplanten Situation (z. B. der Wiederherstellung von Knoten durch das Rechenzentrum nach einem Betriebssystemupdate) haben Sie weniger Kontrolle darüber, wie und wann Knoten heruntergefahren und neu gestartet werden. Der Konflikt, der bei dem Versuch von Elasticsearch entsteht, den Zustand des Clusters nach sequenziellen Knotenausfällen wiederherzustellen, kann zu Zeitüberschreitungen und Fehlern führen.

[Verwalten der Verfügbarkeit virtueller Computer]: ../articles/virtual-machines/virtual-machines-manage-availability.md
[Ausführen der automatisierten Elasticsearch-Resilienztests]: guidance-elasticsearch-running-automated-resilience-tests.md

<!---HONumber=AcomDC_0224_2016-->