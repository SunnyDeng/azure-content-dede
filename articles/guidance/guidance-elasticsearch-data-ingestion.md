<properties
   pageTitle="Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure | Microsoft Azure"
   description="So maximieren Sie die Leistung der Datenerfassung mit Elasticsearch in Azure."
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

# Maximieren der Leistung der Datenerfassung mit Elasticsearch in Azure

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch-introduction.md).

## Übersicht

Ein wichtiger Aspekt beim Erstellen einer Suchdatenbank besteht darin, die beste Methode zur Strukturierung des Systems zu bestimmen, um suchbare Daten schnell und effizient zu erfassen. Die Überlegungen in Zusammenhang mit dieser Anfrage betreffen nicht nur die Auswahl der Infrastruktur, auf der das System implementiert wird, sondern auch die verschiedenen Optimierungen, mit deren Hilfe Sie sicherstellen können, dass das System die erwartete Datenmenge bewältigen kann. In diesem Dokument werden die Bereitstellungs- und Konfigurationsoptionen beschrieben, die Sie bei der Implementierung eines Elasticsearch-Clusters mit umfangreicher Datenerfassung berücksichtigen sollten. Um solide Daten zur Veranschaulichung bereitzustellen, enthält dieses Dokument auch die Ergebnisse von Benchmarktests verschiedener Konfigurationen mit einer einfachen hohen Datenerfassungsworkload. Die Details der Workload werden im [Anhang](#appendix-the-bulk-load-data-ingestion-performance-test) am Ende dieses Dokuments beschrieben.

Der Zweck der Benchmarks bestand nicht darin, absolute Leistungsangaben für die Ausführung von Elasticsearch zu generieren oder sogar eine bestimmte Topologie zu empfehlen. Sie sollten vielmehr Methoden aufzeigen, die Sie zur Bewertung der Leistung, Auswahl der Datenknotengröße und Implementierung von Clustern, die Ihre eigenen Leistungsanforderungen erfüllen, verwenden können. Beim Festlegen der Größe Ihrer eigenen Systeme ist es wichtig, die Leistung basierend auf Ihren Workloads gründlich zu testen. Sammeln Sie Telemetriedaten, die Informationen zur optimalen Hardwarekonfiguration und den zu berücksichtigenden horizontalen Skalierungsfaktoren liefern. Insbesondere sollten Sie Folgendes beachten:

* Berücksichtigen Sie die Gesamtgröße der gesendeten Nutzlast und nicht nur die Anzahl von Elementen in jeder Masseneinfügungsanfrage. Je nach Ressource, die zur Verarbeitung der einzelnen Anfragen verfügbar ist, kann eine kleinere Anzahl großer Massenelemente in jeder Anfrage besser geeignet sein als eine größere Anzahl.

  > [AZURE.NOTE] Sie können die Auswirkungen verschiedener Masseneinfügungsanfrage mit Marvel anhand der E/A-Indikatoren *readbytes*/writebytes* mit JMeter und Betriebssystemtools wie *iostat* und *vmstat* unter Ubuntu überwachen.

* Führen Sie Leistungstests durch, und sammeln Sie Telemetriedaten, um die CPU-Verarbeitungszeiten und E/A-Wartezeiten, die Datenträgerlatenz, den Durchsatz und die Reaktionszeiten zu messen. Diese Informationen können Ihnen dabei helfen, potenzielle Engpässe zu identifizieren sowie die Kosten und Vorteile der Verwendung von Storage Premium zu bewerten. Bedenken Sie, dass die CPU- und Datenträgerauslastung je nach Methode, die zur Verteilung von Shards und Replikaten im Cluster verwendet wird, möglicherweise nicht auf allen Knoten gleich ist (einige Knoten können mehr Shards als andere enthalten).

* Berücksichtigen Sie, wie die Anzahl gleichzeitiger Anfragen für Ihre Workload im Cluster verteilt wird, und bewerten Sie, wie sich die Verwendung einer unterschiedlichen Anzahl von Knoten zur Bewältigung dieser Workload auswirkt.

* Berücksichtigen Sie, wie die Workloads zunehmen können, wenn das Unternehmen wächst. Bewerten Sie die Auswirkung dieses Wachstums auf die Kosten der VMs und den von den Knoten verwendeten Speicher.

* Die Verwendung eines Clusters mit einer größeren Anzahl von Knoten mit normalen Datenträgern kann wirtschaftlicher sein, wenn Ihr Szenario eine große Anzahl von Anfragen erfordert und die Datenträgerinfrastruktur einen Durchsatz gemäß Ihren SLAs aufrechterhält. Eine höhere Anzahl von Knoten kann jedoch Mehraufwand in Form zusätzlicher Kommunikation und Synchronisierung zwischen den Knoten verursachen.

* Beachten Sie, dass eine höhere Anzahl von Kernen pro Knoten die Datenverkehrsrate der Datenträger erhöhen kann, da mehr Dokumente verarbeitet werden können. Messen Sie in diesem Fall die Datenträgerauslastung, um festzustellen, ob sich das E/A-Subsystem zu einem Engpass entwickeln kann, und die Vorteile der Verwendung von Storage Premium zu ermitteln.

* Testen und analysieren Sie die Nachteile, die die Verwendung einer größeren Anzahl von Knoten mit weniger Kernen im Vergleich zu einer geringeren Anzahl von Knoten mit mehr Kernen mit sich bringt. Beachten Sie, dass die Erhöhung der Anzahl von Replikaten die Belastung des Clusters erhöht und möglicherweise das Hinzufügen von Knoten erforderlich macht.

* Bedenken Sie, dass die Verwendung kurzlebiger Datenträger eine häufigere Wiederherstellung der Indizes erfordern kann.

* Messen Sie die Speichervolumeverwendung, um die Kapazität und Unterauslastung des Speichers zu bewerten. In unserem Szenario haben wir z. B. 1,5 Milliarden Dokumente mit 350 GB Speicher gespeichert.

* Messen Sie die Übertragungsraten für Ihre Workloads, und überlegen Sie, wie wahrscheinlich es ist, dass das Gesamtlimit der E/A-Übertragungsrate für ein Speicherkonto erreicht wird, in dem Sie virtuelle Datenträger erstellt haben.

Im letzten Teil des Dokuments werden diese Probleme ausführlicher beschrieben.

## Überlegungen zu den Knoten und zum Index

Stellen Sie sich bei einem System, das eine umfangreiche Datenerfassung unterstützen müssen, die folgenden Fragen:

* **Verändern sich die Daten schnell, oder sind sie relativ statisch?** Je dynamischer die Daten sind, desto größer ist der Wartungsaufwand für Elasticsearch. Wenn die Daten repliziert werden, wird jedes Replikat synchron verwaltet. Bei dynamischen Daten, die eine begrenzte Lebensdauer haben oder mühelos wiederhergestellt werden können, kann es von Vorteil sein, die Replikation komplett zu deaktivieren. Diese Option wird im Abschnitt [Überlegungen zur Optimierung einer umfangreichen Datenerfassung](#_Considerations_for_Tuning) weiter erläutert.

* **Wie aktuell müssen die mit der Suche ermittelten Daten sein?** Zum Aufrechterhalten der Leistung puffert Elasticsearch so viele Daten wie möglich im Arbeitsspeicher. Dies bedeutet, dass nicht alle Änderungen sofort für Suchanfragen verfügbar sind. Eine Beschreibung des Prozesses, mit dem Elasticsearch Änderungen dauerhaft speichert und sichtbar macht, finden Sie online im Dokument zum ??? [dauerhaften Speichern von Änderungen](https://www.elastic.co/guide/en/elasticsearch/guide/current/translog.html#translog). Die Rate, mit der Daten sichtbar werden, hängt von der Einstellung *refresh\_interval* des entsprechenden Index ab. Standardmäßig ist dieses Intervall auf 1 Sekunde festgelegt. Eine so schnelle Aktualisierung ist jedoch nicht immer erforderlich. Indizes zur Aufzeichnung von Protokolldaten müssen z. B. vielleicht einen schnellen und kontinuierlichen Zufluss von Informationen bewältigen, die schnell erfasst werden müssen, aber nicht sofort zur Abfrage verfügbar sein müssen. In diesem Fall kann das Aktualisierungsintervall ggf. reduziert werden. Diese Funktion wird ebenfalls im Abschnitt [Überlegungen zur Optimierung einer umfangreichen Datenerfassung](#_Considerations_for_Tuning) beschrieben.

* **Wie schnell wird die Datenmenge voraussichtlich zunehmen?** Die Indexkapazität richtet sich nach der Anzahl von Shards, die beim Erstellen des Index angegeben wird. Geben Sie entsprechend dem zu erwartenden Datenwachstum eine ausreichende Anzahl von Shards (der Standardwert ist 5) an. Falls der Index zunächst auf einem einzelnen Knoten erstellt wird, befinden sich alle fünf Shards auf diesem Knoten. Wenn die Datenmenge zunimmt, können jedoch zusätzliche Knoten hinzugefügt werden. Elasticsearch verteilt Shards dann dynamisch auf die Knoten. Jeder Shard bedeutet jedoch Mehraufwand. Bei allen Suchvorgängen in einem Index werden sämtliche Shards abgefragt. Wird eine große Anzahl von Shards für eine kleine Menge von Daten erstellt, kann dies folglich den Datenabruf verlangsamen (vermeiden Sie eine [zu hohe Anzahl von Shards](https://www.elastic.co/guide/en/elasticsearch/guide/current/kagillion-shards.html)).

    Einige Workloads (z. B. die Protokollierung) erstellen u. U. jeden Tag einen neuen Index. Wenn Sie feststellen, dass die Anzahl von Shards nicht für die Datenmenge ausreicht, sollten Sie sie vor dem Erstellen des nächsten Indexes ändern (vorhandene Indizes sind davon nicht betroffen). Wenn Sie vorhandene Daten auf mehrere Shards verteilen müssen, ist die erneute Indizierung der Informationen eine Option. Erstellen Sie einen neuen Index mit der entsprechenden Konfiguration, und kopieren Sie die Daten in den Index. Dieser Prozess kann mithilfe von [Indexaliasen](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-aliases.html) für Anwendungen transparent gemacht werden.

* **Müssen Daten in einem mehrinstanzenfähigen Szenario zwischen Benutzern partitioniert werden?** Sie können separate Indizes für jeden Benutzer erstellen, dies kann aber aufwändig sein, wenn jeder Benutzer nur über eine geringe Menge von Daten verfügt. Erstellen Sie stattdessen ggf. [freigegebene Indizes](https://www.elastic.co/guide/en/elasticsearch/guide/current/shared-index.html), und verwenden Sie [Aliase auf Grundlage von Filtern](https://www.elastic.co/guide/en/elasticsearch/guide/current/faking-it.html), um Anfragen an die benutzerspezifischen Daten weiterzuleiten. Um alle Daten für einen Benutzer im selben Shard zu behalten, überschreiben Sie die Standardroutingkonfiguration für den Index, und leiten Sie Daten basierend auf einem identifizierenden Attribut des Benutzers weiter.

* **Sind die Daten lang- oder kurzlebig?** Wenn Sie eine Gruppe von Azure-VMs zum Implementieren eines Elasticsearch-Clusters verwenden, können Sie kurzlebige Daten auf einem lokalen Ressourcensystemdatenträger speichern, anstatt ein angefügtes Laufwerk zu verwenden. Durch die Verwendung einer VM-SKU, die ein SSD für den Ressourcendatenträger nutzt, kann die E/A-Leistung verbessert werden. Alle Informationen auf dem Ressourcendatenträger sind jedoch temporär und können verloren gehen, wenn die VM neu gestartet wird. Weitere Informationen dazu finden Sie im Abschnitt „When Will the Data on a Temporary Drive Be Lost“ (Wann gehen die Daten auf einem temporären Laufwerk verloren?) im Dokument [Understanding the temporary drive on Microsoft Azure Virtual Machines](http://blogs.msdn.com/b/mast/archive/2013/12/07/understanding-the-temporary-drive-on-windows-azure-virtual-machines.aspx) (Grundlegendes zum temporären Laufwerk in Microsoft Azure Virtual Machines). Wenn Daten zwischen Neustarts beibehalten werden müssen, erstellen Sie persistente virtuelle Festplatte (Virtual Hard Drive, VHDs) zum Speichern dieser Informationen, und fügen Sie sie an die VM an.

* **Wie aktiv sind die Daten?** Azure-VHDs unterliegen einer Drosselung, wenn die Anzahl von Lese-/Schreibaktivitäten angegebene Parameter überschreitet (derzeit 500 IOPS für einen Datenträger, der an eine VM der Standard-Ebene angefügt ist, und 5000 IOPS für einen Storage Premium-Datenträger). Um die Wahrscheinlichkeit einer Drosselung zu reduzieren und die E/A-Leistung zu erhöhen, können Sie ggf. mehrere Daten-VHDs für jede VM erstellen und Elasticsearch so konfigurieren, dass Daten mittels Striping auf diese VHDs verteilt werden. Informationen dazu finden Sie im Abschnitt zu den Datenträger- und [Dateisystemanforderungen](#disk-and-file-system-requirements) im Dokument „Implementing Elasticsearch on Azure“ (Implementieren von Elasticsearch in Azure).

    > [AZURE.NOTE] Wählen Sie eine Hardwarekonfiguration, die sicherstellt, dass genügend Arbeitsspeicher zum Zwischenspeichern von häufig verwendeten Daten verfügbar ist, und dadurch die Anzahl von Datenträger-E/A-Lesevorgängen reduziert. Informationen dazu finden Sie im Abschnitt zu den [Arbeitsspeicheranforderungen](#memory-requirements) im Dokument „Implementing Elasticsearch on Azure“ (Implementieren von Elasticsearch in Azure).

* **Welche Art von Workload muss von den einzelnen Knoten unterstützt werden?** Für Elasticsearch ist es von Vorteil, wenn Arbeitsspeicher zum Zwischenspeichern von Daten (in Form von im Dateisystemcache) und für den JVM-Heap verfügbar ist. Informationen dazu finden Sie im Abschnitt zu den [Arbeitsspeicheranforderungen](#memory-requirements) im Dokument „Implementing Elasticsearch on Azure“ (Implementieren von Elasticsearch in Azure). Aufgrund des von Elasticsearch implementierten Threadingmodells ist zudem die Verwendung von CPUs mit mehreren Kernen effizienter als die Verwendung leistungsstärkerer CPUs mit weniger Kernen.

    > [AZURE.NOTE] Die Menge an Arbeitsspeicher, die Anzahl von CPU-Kernen und die Anzahl verfügbarer Datenträger sind durch die SKU der virtuellen Maschine beschränkt. Weitere Informationen finden Sie auf der Seite [Virtual Machines – Preise](http://azure.microsoft.com/pricing/details/virtual-machines/) auf der Azure-Website.

### Virtuelle Maschine - Optionen

Sie können VMs in Azure mit einer Reihe verschiedener SKUs bereitstellen. Die für eine Azure-VM verfügbaren Ressourcen hängen von der ausgewählten SKU ab. Jede SKU bietet eine andere Mischung von Kernen, Arbeitsspeicher und Speicher. Sie müssen eine geeignete VM-Größe auswählen, die die erwartete Workload bewältigen kann, aber dennoch kostengünstig ist. Beginnen Sie mit einer Konfiguration, die Ihre aktuellen Anforderungen erfüllt (führen Sie zum Testen wie weiter unten im Dokument beschrieben Benchmarktests durch). Sie können einen Cluster später skalieren, indem Sie weitere VMs mit Elasticsearch-Knoten hinzufügen.

Auf der Seite [Größen für Virtual Machines](virtual-machines-size-specs/) der Azure-Website sind die verschiedenen Optionen und SKUs aufgeführt, die für VMs verfügbar sind.

Wählen Sie die Größe und die Ressourcen einer VM entsprechend der Funktion, die die auf der VM ausgeführten Knoten übernehmen.

Datenknoten:

* Ordnen Sie dem Java-Heap bis zu 30 GB oder 50 % des verfügbaren RAM zu (je nachdem, welcher dieser Werte niedriger ist). Der restliche RAM sollte für das Betriebssystem zum Zwischenspeichern von Dateien verfügbar sein. Wenn Sie Linux verwenden, können Sie die Menge an Arbeitsspeicher, die dem Java-Heap zugeordnet wird, vor dem Ausführen von Elasticsearch durch Festlegen der Umgebungsvariable ES\_HEAP\_SIZE angeben. Wenn Sie Windows oder Linux verwenden, können Sie die Arbeitsspeichergröße auch beim Starten von Elasticsearch mit den Parametern *Xmx* und *Xms* vorgeben.

    > [AZURE.NOTE]  Je nach Workload kann die Verwendung einer kleineren Anzahl großer VMs weniger effektiv sein als die Verwendung einer größeren Anzahl von VMs mittlerer Größe. Führen Sie Tests durch, mit denen sich die Nachteile des zusätzlichen Netzwerkdatenverkehrs und des damit einhergehenden Wartungsaufwands im Vergleich zu den Kosten einer Erhöhung der Anzahl von Kernen und den reduzierten Datenträgerkonflikten auf jedem Knoten des gemessen werden können.

* Verwenden Sie schnelle Datenträger, idealerweise SSDs mit geringer Latenz und zum Speichern von Elasticsearch-Daten. Dies wird im Abschnitt [Speicheroptionen](#storage-options) ausführlicher erläutert.

* Verwenden Sie mehrere Datenträger (der gleichen Größe), und verteilen Sie Daten mittels Striping auf diese Datenträger. Die SKU Ihrer VMs bestimmt die maximale Anzahl von Datenträgern, die Sie anfügen können. Weitere Informationen finden Sie im Abschnitt zu den [Datenträger- und Dateisystemanforderungen](#disk-and-file-system-requirements).

* Verwenden Sie eine CPU mit mehreren Kernen (mindestens zwei, vorzugsweise vier oder mehr Kerne). Wählen Sie CPUs nicht basierend auf der Leistung, sondern der Anzahl von Kernen aus. Das Threadingmodell von Elasticsearch zur Behandlung gleichzeitiger Anfragen ist bei der Verwendung mit mehreren Kernen effizienter als bei leistungsstarken CPUs mit weniger Kernen.

Clientknoten:

* Ordnen Sie keinen Datenträgerspeicherplatz für Elasticsearch-Daten zu. Dedizierte Clients speichern keine Daten auf Datenträger.

* Stellen Sie sicher, dass ausreichend Arbeitsspeicher zum Bewältigen der Workloads zur Verfügung steht. Masseneinfügungsanfragen werden in den Arbeitsspeicher eingelesen, bevor die Daten an die verschiedenen Datenknoten gesendet werden, und die Ergebnisse von Aggregationen und Anfragen werden im Arbeitsspeicher gesammelt, bevor sie an die Clientanwendung zurückgegeben werden. Führen Sie Benchmarktests für Ihre eigenen Workloads durch, und überwachen Sie die Arbeitsspeicherverwendung mit einem Tool wie Marvel oder den von der *node/stats*-API zurückgegebenen [JVM-Informationen](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_jvm_section), um die optimalen Anforderungen zu ermitteln:

    ```http
    GET _nodes/stats
    ```

    Überwachen Sie insbesondere die Metrik *heap\_used\_percent* für jeden Knoten, und versuchen Sie, die Heapgröße unter 75 % des verfügbaren Speicherplatzes zu halten.

* Stellen Sie sicher, dass genügend CPU-Kerne zum Empfangen und Verarbeiten der erwarteten Menge von Anfragen verfügbar sind. Anfragen werden vor der Verarbeitung in eine Warteschlange gestellt, und die Anzahl von Elementen, die in die Warteschlange gestellt werden können, ist von der Anzahl von CPU-Kernen auf jedem Knoten abhängig. Sie können die Warteschlangenlängen anhand der Daten in den [Threadpool-Informationen](https://www.elastic.co/guide/en/elasticsearch/guide/current/_monitoring_individual_nodes.html#_threadpool_section) überwachen, die von der node/stats-API zurückgegeben werden. Wenn die Anzahl von Ablehnungen (*rejected*) einer Warteschlange angibt, dass Anfragen abgelehnt werden, weist dies darauf hin, dass die Ressourcen des Clusters knapp werden. Dies kann auf die CPU-Bandbreite, aber auch auf andere Faktoren zurückzuführen sein, z. B. unzureichenden Arbeitsspeicher oder eine geringe E/A-Leistung. Verwenden Sie diese Informationen in Verbindung mit anderen Statistiken, um die Ursache zu ermitteln.

    > [AZURE.NOTE]  Ob Clientknoten erforderlich sind, hängt von Ihren Workloads ab. Für Datenerfassungworkloads ist die Verwendung von dedizierten Clients in der Regel nicht von Vorteil. Manche Suchvorgänge und Aggregationen können jedoch mit dedizierten Clients schneller ausgeführt werden. Führen Sie daher Benchmarktests für Ihre eigenen Szenarien durch.

    > Clientknoten sind in erster Linie für Anwendungen nützlich, die die Transport Client-API zum Herstellen einer Verbindung mit dem Cluster verwenden. Sie können auch die Node Client-API verwenden, die mithilfe der Ressourcen der Anwendungshostumgebung dynamisch einen dedizierten Client für die Anwendung erstellt. Wenn Ihre Anwendungen die Knotenclient-API verwenden, sind in Ihrem Cluster möglicherweise keine vorkonfigurierten dedizierten Clientknoten erforderlich. Bedenken Sie jedoch, dass ein mit der Client Node-API erstellter Knoten ein Mitglied erster Ordnung des Clusters ist und als solches an der Netzwerkkommunikation mit anderen Knoten teilnimmt. Häufiges Starten und Beenden von Clientknoten kann unnötige Störungen im gesamten Cluster verursachen.

Masterknoten:

* Ordnen Sie keinen Datenträgerspeicherplatz für Elasticsearch-Daten zu. Dedizierte Masterdaten speichern keine Daten auf Datenträger.

* Die CPU-Anforderungen sollten minimal sein.

* Der Arbeitsspeicherbedarf hängt von der Größe des Clusters ab. Informationen zum Zustand des Clusters werden im Arbeitsspeicher beibehalten. Bei kleinen Clustern ist der erforderliche Arbeitsspeicher minimal. Bei einem großen, sehr aktiven Cluster, in dem häufig Indizes erstellt und Shards verschoben werden, können die Zustandsinformationen jedoch erheblich an Umfang zunehmen. Überwachen Sie die JVM-Heapgröße, um festzustellen, ob Sie mehr Arbeitsspeicher hinzufügen müssen.

    > [AZURE.NOTE]  Erstellen Sie zum Gewährleisten der Clusterzuverlässigkeit immer mehrere Masterknoten, und konfigurieren Sie die verbleibenden Knoten, um die Möglichkeit eines Split Brain-Syndroms auszuschließen. Im Idealfall ist die Anzahl von Masterknoten ungerade. Dieses Thema wird im Dokument „Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery“ (Konfigurieren, Testen und Analysieren der Elasticsearch-Resilienz und -Wiederherstellung) ausführlicher behandelt.

### Speicheroptionen

Es ist eine Reihe von Speicheroptionen für Azure-VMs mit unterschiedlichen Vor- und Nachteilen bezüglich der Kosten, Leistung, Verfügbarkeit und Wiederherstellung verfügbar, die Sie sorgfältig erwägen müssen.

Beachten Sie, dass Elasticsearch-Daten auf anderen Datenträgern gespeichert werden sollten als die Betriebssystemsoftware. Dadurch können Sie Konflikte mit dem Betriebssystem minimieren und sicherstellen, dass große Mengen an Elasticsearch-E/A-Vorgängen nicht mit Betriebssystemfunktionen um E/A-Ressourcen konkurrieren.

Für Azure-Datenträger gelten Leistungseinschränkungen. Wenn bei einem Cluster regelmäßige Aktivitätsspitzen auftreten, können E/A-Anfragen gedrosselt werden. Um dies zu verhindern, sollten Sie Ihren Entwurf so auslegen, dass die Dokumentgröße in Elasticsearch und das voraussichtlich von jedem Datenträger empfangene Anfragevolumen ausgeglichen sind.

Auf Standardspeicher basierende Datenträger unterstützen eine maximale Anfragerate von 500 IOPS, während bei auf Storage Premium basierenden Datenträgern bis zu 5.000 IOPS möglich sind (Standardspeicher verwendet herkömmliche „rotierende“ Medien, wohingegen Storage Premium SSDs verwendet, die eine niedrigere Latenz und einen höheren Durchsatz bieten). Storage Premium-Datenträger sind nur für VMs der DS- und GS-Serie verfügbar. Die maximalen Datenträger-IOPS-Raten für [Azure-VMs sind online dokumentiert](virtual-machines-size-specs/).

> [AZURE.NOTE] Je nach Datenmenge, die von Anfragen zurückgegeben wird, erreichen Sie u. U. nicht die für einen Datenträger angegebene maximale IOPS-Rate, da jede VM abhängig von ihrer Größe auch auf eine maximale Datenträgerbandbreite gedrosselt wird. Ein Datenträger auf einer Standard\_GS5-VM kann z. B. bis zu 5.000 IOPS pro Datenträger unterstützen, dies ist jedoch nur dann der Fall, wenn die gesamte Datenübertragungs-Bandbreite auf allen an die VM angefügten Datenträgern 2.000 MB/s nicht überschreitet.

**Persistente Datenträger**

Persistente Datenträger sind VHDs, die durch Azure Storage gesichert sind. Wenn die VM nach einem schwerwiegenden Fehler neu erstellt werden muss, können vorhandene VHDs problemlos an die neue VM angefügt werden. VHDs können auf Grundlage von Standardspeicher (rotierende Medien) oder Storage Premium (SSDs) erstellt werden. Wenn Sie SSDs verwenden möchten, müssen Sie VMs mindestens mit der DS-Serie erstellen. DS-Computer kosten genauso viel wie entsprechende VMs der D-Serie, zusätzlich fallen aber Kosten für die Verwendung von Storage Premium an.

In Fällen, in denen die maximale Übertragungsrate pro Datenträger nicht für die erwartete Workload ausreicht, sollten Sie entweder mehrere Datenträger erstellen und Elasticsearch für [Datenstriping auf diesen Datenträgern](#disk-and-file-system-requirements) konfigurieren oder [RAID 0-Striping mit virtuellen Datenträgern](virtual-machines-linux-configure-raid/) auf der Systemebene implementieren.

> [AZURE.NOTE] Der Erfahrung von Microsoft zufolge eignet sich die Verwendung von RAID 0 besonders zum Ausgleichen von *ungleichmäßigen* Workloads, die häufige Aktivitätsspitzen generieren.

Verwenden Sie lokal redundante (oder Premium lokal redundante) Replikate für das Speicherkonto mit den Datenträgern. Das Replizieren über Zonen und geografischen Regionen hinweg ist für hohe Verfügbarkeit von Elasticsearch nicht erforderlich.

**Kurzlebige Datenträger**

Die Verwendung persistenter Datenträger auf Grundlage von SSDs erfordert die Erstellung von VMs, die Storage Premium unterstützen. Dies wirkt sich auf den Preis aus. Das Speichern von Elasticsearch-Daten auf dem lokalen kurzlebigen Datenträger kann eine kostengünstige Lösung für mittelgroße Knoten sein, die ungefähr 800 GB Speicher erfordern. Auf den VMs der Standard-D-Serie werden kurzlebige Datenträger mit SSDs implementiert, die eine sehr viel höhere Leistung und deutlich geringere Latenz bieten als gewöhnliche Datenträger. Wenn Sie Elasticsearch verwenden, kann die Leistung – ohne die zugehörigen Kosten – der Verwendung von Storage Premium entsprechen. Weitere Informationen finden Sie im Abschnitt [Behebung von Problemen mit der Datenträgerlatenz](#addressing-disk-latency-issues) .

Wie im Dokument [D-Series Performance Expectations](https://azure.microsoft.com/blog/d-series-performance-expectations/) (Leistungserwartungen – D-Serie) beschrieben beschränkt die Größe der VM den im kurzlebigen Speicher verfügbaren Speicherplatz. Eine Standard\_D1-VM bietet z. B. 50 GB kurzlebigen Speicher, eine Standard\_D2-VM 100 GB kurzlebigen Speicher und eine Standard\_D14-VM 800 GB kurzlebigen Speicher. Bei Clustern, in denen Knoten nur diese Menge an Speicherplatz benötigen, kann die Verwendung einer VM der D-Serie mit kurzlebigem Speicher eine kostengünstige Lösung sein: Zum Zeitpunkt der Erstellung dieses Dokuments betrugen die geschätzten monatlichen Kosten einer D4-VM unter Linux 458 $. Bei der entsprechenden DS4-VM unter Linux mit einem einzelnen P30-SSD mit 1.024 GB betragen die monatlichen Kosten 645 $ (509 $ für die VM und 136 $ für das SSD). Zum Speichern von 1.024 GB an Daten im kurzlebigen Speicher sind drei D4-VMs mit monatlichen Kosten von 1374 $ erforderlich. Ihre Berechnungen sollten jedoch nicht ausschließlich auf der Speicherkapazität beruhen. Eine einzelne DS4-VM bietet 8 CPU-Kerne und 28 GB Arbeitsspeicher, während drei D4-VMs über 24 CPU-Kerne und 84 GB Arbeitsspeicher verfügen. Wenn Ihre Workloads prozessorintensiv sind, kann die Verteilung der Last auf drei VMs eine bessere Leistung bieten als die Ausführung auf einer einzelnen VM. Zudem kann sich die Verwendung einer einzelnen VM (oder einer kleinen Anzahl von VMs) auf die Resilienz und Wiederherstellbarkeit des Clusters auswirken.

> [AZURE.NOTE] Die oben genannten Zahlen dienen nur zur Veranschaulichung. Die Kosten können sich bereits geändert haben, wenn Sie dieses Dokument lesen. Aktuelle Preise finden Sie auf der [Preisrechner](https://azure.microsoft.com/pricing/calculator/) Seite.

Sie müssen den höheren Durchsatz, der mit kurzlebigem Speicher erzielt wird, gegen den Zeit- und Kostenaufwand für die Wiederherstellung der Daten nach einem Neustart abwägen. Der Inhalt des kurzlebigen Datenträgers geht verloren, wenn die VM auf einen anderen Hostserver verschoben wird, der Host aktualisiert wird oder auf dem Host ein Hardwarefehler auftritt. Wenn die Daten selbst eine begrenzte Lebensdauer haben, kann dieser Datenverlust vertretbar sein. Bei Daten mit längerer Lebensdauer kann es möglich sein, einen Index neu zu erstellen oder die fehlenden Informationen aus einem Backup wiederherzustellen. Die Möglichkeit von Datenverlusten kann durch Replikate, die auf anderen VMs gespeichert werden, minimiert werden.

> [AZURE.NOTE] Wichtige Produktionsdaten sollten nicht auf einer **einzelnen** VM gespeichert werden. Wenn der Knoten ausfällt, sind alle Daten nicht verfügbar. Stellen Sie bei wichtigen Informationen sicher, dass die Daten auf mindestens einem anderen Knoten repliziert werden.

**Azure Files**

Die [Azure-Dateidienst](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx) bietet Zugriff auf freigegebene Dateien mit Azure Storage. Sie können Dateifreigaben erstellen und anschließend auf Azure-VMs bereitstellen. Mehrere VMs können die gleiche Dateifreigabe bereitstellen, sodass sie auf dieselben Daten zugreifen können.

Aus Leistungsgründen wird davon abgeraten, Dateifreigaben zum Speichern von Elasticsearch-Daten zu verwenden, die nicht von mehreren Knoten gemeinsam genutzt werden müssen. Reguläre Datenträger sind für diesen Zweck besser geeignet. Dateifreigaben können zum Erstellen von Elasticsearch-[Schattenreplikatindizes](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-shadow-replicas.html) verwendet werden. Diese Funktion befindet sich derzeit jedoch im Versuchsstadium und sollte gegenwärtig nicht in einer Produktionsumgebung implementiert werden. Aus diesem Grund enthält dieses Grund keine weitere Informationen zu Schattenindizes.

**Netzwerkoptionen**

Azure implementiert ein freigegebene Netzwerkschema. VMs, die die gleichen Hardwareracks nutzen, beanspruchen die gleichen Netzwerkressourcen. Die verfügbare Netzwerkbandbreite kann daher abhängig von der Tageszeit und dem täglichen Arbeitszyklus auf VMs mit der gleichen physischen Netzwerkinfrastruktur variieren. Über diese Faktoren haben Sie wenig Kontrolle. Beachten Sie, dass die Leistung des Netzwerks im Laufe der Zeit schwanken wahrscheinlich wird, und informieren Sie die Benutzer hierüber.

## Überlegungen zur zentralen Hochskalierung von Knoten zur Unterstützung einer umfangreichen Datenerfassung

Sie können Elasticsearch-Cluster mit relativ moderater Hardware erstellen und anschließend zentral oder horizontal hochskalieren, wenn die Datenmenge und die Anzahl von Anfragen zunehmen. Bei Azure haben Sie die Möglichkeit, mit größeren und teureren VMs zentral hochzuskalieren oder mit zusätzlichen kleineren und kostengünstigeren VMs horizontal hochzuskalieren. Sie können auch eine Kombination beider Strategien verwenden. Es gibt keine allgemeingültige Lösung für alle Szenarien. Um den besten Ansatz für die jeweilige Situation zu ermitteln, müssen Sie daher vermutlich eine Reihe von Leistungstests durchführen.

In diesem Abschnitt wird das zentrale Hochskalieren erläutert. Das horizontale Hochskalieren wird im Abschnitt [Überlegungen zur horizontalen Hochskalierung von Clustern zur Unterstützung einer umfangreichen Datenerfassung](#scaling-out-clusters) beschrieben. In diesem Abschnitt werden die Ergebnisse einer Reihe von Benchmarktests beschrieben, die für Elasticsearch-Cluster mit VMs unterschiedlicher Größen durchgeführt wurden. Die Cluster wurden als klein, mittel und groß gekennzeichnet. Die folgende Tabelle enthält eine Zusammenfassung der Ressourcen, die den VMs in jedem Cluster zugeordnet wurden.

| bereitstellen | VM-SKU | Anzahl von Kernen | Anzahl von Datenträgern | RAM |
|---------|-------------|-----------------|----------------------|------|
| Klein | Standard D2 | 2 | 4 | 7 GB |
| Mittel | Standard D3 | 4 | 8 | 14 GB |
| Groß | Standard D4 | 8 | 16 | 28 GB |

Jeder Elasticsearch-Cluster enthielt drei Datenknoten. Diese Datenknoten wurden sowohl für Clientanfragen als auch die Datenverarbeitung eingesetzt. Separate Clientknoten wurden nicht verwendet, da diese nur wenig Vorteile für das Datenerfassungsszenario der Tests boten. Der Cluster enthielt außerdem drei Masterknoten, von denen einer von Elasticsearch zum Koordinieren des Clusters ausgewählt wurde.

Die Tests wurden mit ElasticSearch 1.7.3 ausgeführt. Die Tests wurden ursprünglich in Clustern mit Ubuntu Linux 14.0.4 ausgeführt und anschließend mit Windows Server 2012 wiederholt. Die Details der bei den Tests ausgeführten Workload werden im [Anhang](#appendix-the-bulk-load-data-ingestion-performance-test) am Ende dieses Dokuments beschrieben.

> [AZURE.IMPORTANT] Wie im Abschnitt „Netzwerkoptionen“ beschrieben, wird die Leistungsstatistik von verteilten Diensten, die in einer Cloudumgebung ausgeführt werden, erheblich durch die verfügbare Netzwerkbandbreite zum physischen Übertragen und Empfangen von Daten dieser Dienste beeinflusst. Wenn Sie ein System wie einen Elasticsearch-Cluster erstellen und bereitstellen, haben Sie durch die Auswahl der VM-Größe und -SKU ein hohes Maß an Kontrolle über die CPU, den Arbeitsspeicher und die verfügbaren Datenträgerressourcen. Sie haben weit weniger Kontrolle über die verfügbaren Netzwerkressourcen, da diese von VMs, die sich physisch in demselben Hardwarerack befinden, und auch von dem im Datencenter ein- und ausgehenden Datenverkehrsvolumen gemeinsam genutzt werden. Daher ist es wichtig, vergleichende Leistungstests mit demselben Datencenter und ungefähr zur gleichen Tageszeit während der Geschäftszeiten auszuführen. Die Ergebnisse von Tests, die für VMs in verschiedenen Datencentern oder zu unterschiedlichen Tageszeiten ausgeführt werden, können sehr unterschiedlich ausfallen.

### Datenerfassungsleistung – Ubuntu Linux 14.0.4

In der folgenden Tabelle sind die Gesamtergebnisse der zweistündigen Ausführung der Tests für jede Konfiguration zusammengefasst:

| Konfiguration | Anzahl der Stichproben | Durchschnittliche Reaktionszeit (ms) | Durchsatz (Vorgänge/s) |
|---------------|-----------|----------------------------|---------------------------|
| Klein | 67057 | 636 | 9\.3 |
| Mittel | 123482 | 692 | 17\.2 |
| Groß | 197085 | 839 | 27,4 |

Der Durchsatz und die Anzahl verarbeiteter Stichproben für die drei Konfigurationen stehen ungefähr im Verhältnis 1:2:3. Die im Hinblick auf Arbeitsspeicher, CPU-Kerne und Datenträger verfügbaren Ressourcen müssen jedoch das Verhältnis 1:2:4 aufweisen. Es schien sinnvoll, die Leistungsdetails der Knoten im Cluster auf niedriger Ebene zu untersuchen, um herauszufinden, weshalb dies der Fall ist. Anhand dieser Informationen kann ermittelt werden, ob der zentralen Hochskalierung Grenzen gesetzt sind und eine horizontale Hochskalierung möglicherweise sinnvoller ist.

### Ermitteln der begrenzenden Faktoren: Netzwerkauslastung

Elasticsearch erfordert eine ausreichende Netzwerkbandbreite, um die eingehenden Clientanfragen und die zwischen Knoten im Cluster übertragenen Synchronisierungsinformationen zu unterstützen. Wie zuvor erwähnt, haben Sie nur wenig Kontrolle über die verfügbare Bandbreite, da diese von vielen Variablen abhängig ist, z. B. dem verwendeten Datencenter und der aktuellen Netzwerklast von anderen VMs, die die gleiche Netzwerkinfrastruktur nutzen. Es lohnt sich aber trotzdem, die Netzwerkaktivität für jeden Cluster zu untersuchen, und sei es nur, um sicherzustellen, dass das Datenverkehrsvolumen nicht übermäßig groß ist. Das folgende Diagramm zeigt einen Vergleich des Netzwerkdatenverkehrs, der vom Knoten 2 in jedem der Cluster empfangen wurde (die Volumen bei den anderen Knoten in den Clustern waren sehr ähnlich).

![](media/guidance-elasticsearch-data-ingestion-image1.png)

Die durchschnittliche Anzahl empfangener Bytes pro Sekunde für den Knoten 2 in den Clusterkonfigurationen in einem Zeitraum von zwei Stunden war wie folgt:

| Konfiguration | Durchschnittliche Anzahl empfangener Byte/s |
|---------------|--------------------------------------|
| Klein | 3993640\.346 |
| Mittel | 7311689\.897 |
| Groß | 11893874\.2 |

> [AZURE.NOTE] Die Tests wurden durchgeführt, während das System im stabilen Zustand ausgeführt wurde. In Situationen, in denen ein erneuter Ausgleich des Indexes oder eine Knotenwiederherstellung stattfindet, können Datenübertragungen zwischen Knoten mit primären und Replikat-Shards erheblichen Netzwerkdatenverkehr generieren. Die Auswirkungen dieses Prozesses werden im Dokument „Configuring, Testing, and Analyzing Elasticsearch Resilience and Recovery“ (Konfigurieren, Testen und Analysieren der Elasticsearch-Resilienz und -Wiederherstellung) ausführlicher beschrieben.

### Ermitteln der begrenzenden Faktoren: CPU-Auslastung

Die Rate, mit der Anfragen verarbeitet werden, hängt zumindest teilweise von der verfügbaren Kapazität ab. Elasticsearch akzeptiert Masseneinfügungsanfragen für die Masseneinfügungswarteschlange. Jeder Knoten verfügt über eine Reihe von Masseneinfügungswarteschlangen, die durch die Anzahl verfügbarer Prozessoren bestimmt werden. Standardmäßig ist eine Warteschlange für jeden Prozessor vorhanden, und in jede Warteschlange können bis zu 50 ausstehende Anfragen gestellt werden, bevor diese abgelehnt werden. Anwendungen sollten Anfragen mit einer Rate senden, die keinen Überlauf der Warteschlangen verursachen. Die jeweils in den einzelnen Warteschlangen enthaltene Anzahl von Elementen ist abhängig von der Rate, mit der Anfragen von Clientanwendungen gesendet werden, und der Rate, mit der diese Anfragen von Elasticsearch abgerufen und verarbeitet werden. Die in der folgenden Tabelle zusammengefasste Fehlerrate ist aus diesem Grund eine wichtige Statistik.

| Konfiguration | Gesamtzahl der Stichproben | Anzahl von Fehlern | Fehlerrate |
|---------------|---------------|-----------|------------|
| Klein | 67057 | 0 | 0,00 % |
| Mittel | 123483 | 1 | 0,0008 % |
| Groß | 200702 | 3617 | 1,8 % |

Diese Fehler wurden durch die folgende Java-Ausnahme verursacht:

```
org.elasticsearch.action.support.replication.TransportShardReplicationOperationAction$PrimaryPhase$1@75a30c1b]; ]
[219]: index [systembase], type [logs], id [AVEAioKb2TRSNcPa_8YG], message [RemoteTransportException[[esdatavm2][inet[/10.0.1.5:9300]][indices:data/write/bulk[s]]]; nested: EsRejectedExecutionException[rejected execution (queue capacity 50)
```

Eine Erhöhung der Anzahl von Warteschlangen und/oder der Länge jeder Warteschlange kann die Anzahl von Fehlern reduzieren, mit diesem Ansatz können aber nur kurze Bursts bewältigt werden. Wird die Warteschlangenanzahl und/oder -länge während der Ausführung einer kontinuierlichen Reihe von Datenerfassungsaufgaben erhöht, verzögert dies lediglich den Punkt, ab dem Fehler auftreten. Diese Änderung verbessert zudem nicht den Durchsatz und wirkt sich wahrscheinlich nachteilig auf die Reaktionszeit von Clientanwendungen aus, da Anfragen vor der Verarbeitung länger in Warteschlangen gestellt werden.

Die folgenden Diagramme veranschaulichen die CPU-Auslastung für den aktivsten Knoten in jedem Cluster.

> [AZURE.NOTE] Die standardmäßige Indexstruktur von fünf Shards mit einem Replikat (10 Shards insgesamt) führt zu einer geringfügigen Unausgeglichenheit der Lastverteilung zwischen den Knoten in einem Cluster. Zwei Knoten enthalten drei Shards, während der andere Knoten vier Shards enthält. Da der aktivste Knoten den Durchsatz wahrscheinlich am stärksten einschränkt, wurde er in jedem der Fälle ausgewählt:

![](media/guidance-elasticsearch-data-ingestion-image2.png)

![](media/guidance-elasticsearch-data-ingestion-image3.png)

![](media/guidance-elasticsearch-data-ingestion-image4.png)

Für die kleinen, mittleren und großen Cluster betrug die durchschnittliche CPU-Auslastung für diese Knoten 75,01 %, 64,93 % und 64,64 %. Die Auslastung erreicht nur selten tatsächlich 100 % und nimmt ab, wenn die Größe der Knoten und die verfügbare CPU-Leistung zunehmen. Die Wahrscheinlichkeit, dass die CPU-Leistung die Leistung des großen Clusters beeinträchtigt, ist daher eher gering.

### Ermitteln der begrenzenden Faktoren: Arbeitsspeicher

Der Arbeitsspeicher ist ein weiterer wichtiger Aspekt, der die Leistung beeinflussen kann. Für die Tests wurden Elasticsearch 50 % des verfügbaren Arbeitsspeichers zugeordnet. Dies entspricht den [dokumentierten Empfehlungen](https://www.elastic.co/guide/en/elasticsearch/guide/current/heap-sizing.html#_give_half_your_memory_to_lucene). Während der Ausführung der Tests wurde die JVM auf unnötige Aktivität zur automatischen Speicherbereinigung überwacht (ein Anzeichen für unzureichenden Heapspeicher). In allen Fällen war die Heapgröße stabil und die Aktivität zur automatischen Speicherbereinigung der JVM gering. Der Screenshot in Abbildung 1 zeigt eine Momentaufnahme von Marvel mit den wichtigsten JVM-Statistiken für einen kurzen Zeitraum während der Ausführung des Tests auf dem großen Cluster.

![](media/guidance-elasticsearch-data-ingestion-image5.png)

***Abbildung 1: JVM-Arbeitsspeicher und Aktivitäten zur automatischen Speicherbereinigung auf dem großen Cluster.***

### Ermitteln der begrenzenden Faktoren: Datenträger-E/A-Raten
Die letzte serverseitige physische Funktion, die die Leistung einschränken könnte, ist die Leistung des Datenträger-E/A-Subsystems. Das folgende Diagramm vergleicht die Datenträgeraktivität im Hinblick auf die Anzahl geschriebener Bytes für den aktivsten Knoten in jedem Cluster.

![](media/guidance-elasticsearch-data-ingestion-image6.png)

Die folgende Tabelle zeigt die durchschnittliche Anzahl geschriebener Bytes pro Sekunde für den Knoten 2 in jeder Clusterkonfiguration im zweistündigen Zeitraum:

| Konfiguration | Durchschnittliche Anzahl geschriebener Bytes/s |
|---------------|-------------------------------------|
| Klein | 25502361\.94 |
| Mittel | 48856124\.5 |
| Groß | 88137675\.46 |

Die Menge geschriebener Daten nimmt mit der von einem Cluster verarbeiteten Anzahl von Anfragen zu, die E/A-Raten entsprechen aber den Grenzwerten von Azure-Speicher (mit Azure-Speicher erstellte Datenträger können kontinuierlich zwei- bis dreistellige Übertragungsraten (MB/s) unterstützen, je nachdem, ob Standardspeicher oder Storage Premium verwendet wird). Eine Untersuchung der Wartezeit von Datenträger-E/A-Vorgängen kann eine Erklärung dafür liefern, warum der Datenträgerdurchsatz deutlich unter dem theoretischen Maximum liegt. Die folgenden Diagramme und die unten stehende Tabelle zeigen diese Statistiken für die gleichen drei Knoten:

> [AZURE.NOTE] Die Datenträgerwartezeit wird durch eine Überwachung des Prozentsatzes der CPU-Zeit gemessen, während der Prozessoren blockiert sind, weil sie auf den Abschluss von E/A-Vorgängen warten.

![](media/guidance-elasticsearch-data-ingestion-image7.png)

![](media/guidance-elasticsearch-data-ingestion-image8.png)

![](media/guidance-elasticsearch-data-ingestion-image9.png)

| Konfiguration | Durchschnittliche CPU-Zeit/Datenträgerwartezeit (%) |
|---------------|--------------------------------|
| Klein | 21,04 |
| Mittel | 14,48 |
| Groß | 15,84 |

Diesen Daten zufolge wird während eines beträchtlichen Anteils der CPU-Zeit (zwischen nahezu 16 % und 21 %) auf den Abschluss von E/A-Vorgängen gewartet. Dies schränkt die Fähigkeit von Elasticsearch ein, Anfragen zu verarbeiten und Daten zu speichern.

> [AZURE.NOTE]  Während des Testlaufs hat der große Cluster mehr als fünfhundert Millionen Dokumente eingefügt. Eine Fortsetzung des Tests ergab, dass sich die Wartezeiten erheblich erhöhen, wenn die Datenbank über sechshundert Millionen Dokumente enthält. Die Gründe für dieses Verhalten wurden nicht vollständig untersucht. Möglicherweise wird durch Datenträgerfragmentierung eine höhere Datenträgerlatenz verursacht. Das Vergrößern des Clusters über weitere Knoten kann dazu beitragen, die Auswirkungen dieses Problems zu verringern. In extremen Fällen kann es erforderlich sein, einen Datenträger zu defragmentieren, der übermäßig lange E/A-Zeiten anzeigt. Das Defragmentieren eines großen Datenträgers kann jedoch lange dauern (u. U. mehr als 48 Stunden für ein VHD-Laufwerk mit 2 TB). Ein kostengünstigerer Ansatz ist möglicherweise, das Laufwerk einfach neu zu formatieren und Elasticsearch das Wiederherstellen der fehlenden Daten aus Replikat-Shards zu ermöglichen.

### Behandlung von Problemen mit der Datenträgerlatenz

Die Tests wurden ursprünglich auf mit Standarddatenträgern konfigurierten virtuellen Maschinen ausgeführt. Ein Standarddatenträger basiert auf rotierenden Medien und unterliegt demzufolge Rotationslatenz und anderen Engpässen, die E/A-Raten einschränken können. Azure bietet zudem Storage Premium-Speicher, in dem Datenträger mithilfe von SSD-Geräten erstellt werden. Diese Geräte weisen keine Rotationslatenz auf und sollten daher verbesserte E/A-Geschwindigkeiten bereitstellen. Die folgende Tabelle vergleicht die Ergebnisse, nachdem Standarddatenträger im großen Cluster durch Premium-Datenträger ersetzt wurden (die Standard-D4-VMs im großen Cluster wurden durch Standard-DS4-VMs ersetzt; die Anzahl der Kerne, der Arbeitsspeicher und die Datenträger waren in beiden Fällen gleich, der einzige Unterschied besteht darin, dass die DS4-VMS SSDs verwendet).

| Konfiguration | Anzahl der Stichproben | Durchschnittliche Reaktionszeit (ms) | Durchsatz (Vorgänge/s) |
|------------------|-----------|----------------------------|---------------------------|
| Groß – Standard | 197085 | 839 | 27,4 |
| Groß – Premium | 255985 | 581 | 35,6 |

Die Reaktionszeiten wurden deutlich verbessert, was zu einem durchschnittlichen, beinahe viermal so hohen Durchsatz im Vergleich mit dem kleinen Cluster führte. Dies entspricht eher den verfügbaren Ressourcen auf einer Standard-DS4-VM. Durchschnittliche CPU-Auslastung auf dem zentralen Knoten im Cluster (in diesem Fall Knoten 1) wurde erhöht, und es wurde weniger lang auf den Datenträger-E/A-Abschluss gewartet:

![](media/guidance-elasticsearch-data-ingestion-image10.png)

Die Reduzierung der Datenträgerwartezeit wird im folgenden Diagramm offensichtlich. Es zeigt, dass diese Statistik für den zentralen Knoten auf durchschnittlich ca. 1 % gefallen ist:

![](media/guidance-elasticsearch-data-ingestion-image11.png)

Für diese Verbesserung muss jedoch ein Nachteil in Kauf genommen werden. Die Anzahl der Erfassungsfehler stieg um den Faktor 10 auf 35797 (12,3 %). Auch in diesem Fall wurden die meisten dieser Fehler durch einen Überlauf der Warteschlange für das Masseneinfügen verursacht. Angesichts der Tatsache, dass die Hardware jetzt beinahe die Kapazitätsgrenze erreicht hat, müssen u. U. weitere Knoten hinzugefügt oder die Rate der Masseneinfügungen gedrosselt werden, um die Fehlermenge zu reduzieren. Diese Probleme werden weiter unten in diesem Dokument erläutert.

### Testen mit flüchtigen Speicher

Die gleichen Tests wurden auf einem Cluster aus D4-VMs mit flüchtigem Speicher wiederholt. Auf D4-VMs ist flüchtiger Speicher als ein einzelnes SSD mit 400 GB implementiert. Die Anzahl der verarbeiteten Stichproben, die Reaktionszeit und der Durchsatz waren mit den Zahlen vergleichbar, die für den auf DS14-VMs mit Storage Premium basierenden Cluster gemeldet wurden.

| Konfiguration | Anzahl der Stichproben | Durchschnittliche Reaktionszeit (ms) | Durchsatz (Vorgänge/s) |
|-----------------------------------|-----------|----------------------------|---------------------------|
| Groß – Premium | 255985 | 581 | 35,6 |
| Groß – Standard (temporärer Datenträger) | 255626 | 585 | 35,5 |

Die Fehlerrate war ebenfalls ähnlich (33862 Fehler bei insgesamt 289488 Anfragen – 11,7 %).

Die folgenden Diagramme zeigen die CPU-Auslastung und Datenträger-Wartestatistiken für den zentralen Knoten im Cluster (in diesem Fall Knoten 2):

![](media/guidance-elasticsearch-data-ingestion-image12.png)

![](media/guidance-elasticsearch-data-ingestion-image13.png)

In diesem Fall kann die Verwendung von flüchtigem Speicher allein im Hinblick auf die Leistung als eine kostengünstigere Lösung als die Verwendung von Storage Premium angesehen werden.

### Datenerfassungsleistung – Windows Server 2012

Die gleichen Tests wurden mit einer Gruppe von Elasticsearch-Clustern mit Knoten wiederholt, auf denen Windows Server 2012 ausgeführt wird. Mit diesen Tests sollte ermittelt werden, welche Auswirkungen die Auswahl des Betriebssystems ggf. auf die Clusterleistung hat.

Zur Veranschaulichung der Skalierbarkeit von Elasticsearch unter Windows zeigt die folgende Tabelle den Durchsatz und die Reaktionszeiten an, die mit kleinen, mittleren und großen Clusterkonfigurationen erzielt wurden. Beachten Sie, dass Elasticsearch bei allen diesen Tests für die Verwendung von flüchtigem SSD-Speicher konfiguriert war, da die Tests mit Ubuntu ergeben hatten, dass die Datenträgerlatenz wahrscheinlich ein entscheidender Faktor für das Erzielen der optimalen Leistung ist:

| Konfiguration | Anzahl der Stichproben | Durchschnittliche Reaktionszeit (ms) | Durchsatz (Vorgänge/s) |
|---------------|-----------|----------------------------|---------------------------|
| Klein | 90295 | 476 | 12,5 |
| Mittel | 169243 | 508 | 23,5 |
| Groß | 257115 | 613 | 35,6 |

Diese Ergebnisse geben an, wie Elasticsearch mit der Größe der virtuellen Maschine und verfügbaren Ressourcen unter Windows skaliert wird.

In den folgenden Tabellen werden die Ergebnisse für den großen Cluster auf Ubuntu und Windows verglichen:

| Betriebssystem | Anzahl der Stichproben | Durchschnittliche Reaktionszeit (ms) | Durchsatz (Vorgänge/s) | Fehlerrate (%) |
|------------------|-----------|----------------------------|---------------------------|----------------|
| Ubuntu | 255626 | 585 | 35,5 | 11,7 |
| Windows | 257115 | 613 | 35,6 | 7,2 |

Der Durchsatz war mit dem der großen Ubuntu-Cluster konsistent; die Reaktionszeit war zwar etwas höher, dies gleicht sich jedoch durch die geringere Fehlerrate wieder aus (Fehler werden schneller gemeldet, als erfolgreiche Vorgänge und haben daher kürzere Reaktionszeiten).

Die von den Windows-Überwachungstools gemeldete CPU-Auslastung war geringfügig höher als die von Ubuntu. Allerdings sollten Sie direkte Vergleiche von betriebssystemübergreifenden Messungen wie diesen aufgrund der Methode, mit der verschiedene Betriebssysteme diese Statistiken melden, mit äußerster Vorsicht behandeln. Zudem sind Informationen über Datenträgerlatenz in Bezug auf die CPU-Wartezeit auf E/A nicht auf die gleiche Weise wie für Ubuntu verfügbar. Der wichtigste Punkt ist, dass die CPU-Auslastung hoch war, was auf eine kurze Wartezeit auf E/A hinweist:

![](media/guidance-elasticsearch-data-ingestion-image14.png)

### Skalierung: Schlussfolgerungen

Die Elasticsearch-Leistung für einen optimierten Cluster ist bei Windows und Ubuntu wahrscheinlich gleich und wird unter beiden Betriebssysteme mit einem ähnlichen Muster zentral hochskaliert. Verwenden Sie für eine optimale Leistung SSDs zur Aufnahme von Elasticsearch Daten.

## <a name="scaling-out-clusters"></a>Überlegungen zur horizontale Skalierung von Knoten zur Unterstützung einer umfangreichen Datenerfassung

Die horizontale Skalierung ist der kostenlose Ansatz für die vertikale Skalierung, die im vorherigen Abschnitt behandelt wurde. Ein wichtiges Feature von Elasticsearch ist die inhärenten horizontale Skalierbarkeit, die in die Software integriert ist. Zum Vergrößern eines Clusters müssen einfach weitere Knoten hinzugefügt werden. Sie müssen keine manuellen Vorgänge zum erneuten Verteilen von Indizes oder Shards ausführen, da diese Aufgaben automatisch behandelt werden. Es gibt jedoch eine Reihe von Konfigurationsoptionen, mit denen Sie diesen Prozess beeinflussen können. Das Hinzufügen weiterer Knoten verbessert die Leistung durch Verteilen der Last auf mehrere Maschinen. Wenn Sie weitere Knoten hinzufügen, müssen Sie u. U. auch Daten neu indizieren, um die Anzahl von verfügbaren Shards zu erhöhen. Sie können diesem Prozess zu einem gewissen Grad zuvorkommen, indem Sie Indizes mit mehr Shards erstellen, als ursprünglich Knoten verfügbar sind. Wenn weitere Knoten hinzugefügt werden, können die Shards verteilt werden.

Neben der Nutzung der horizontalen Skalierbarkeit von Elasticsearch gibt es noch weitere Gründe für die Implementierung von Indizes, die mehr Shards als Knoten aufweisen. Jeder Shard wird als eine separate Datenstruktur ([Lucene](https://lucene.apache.org/)-Index) implementiert und verfügt über eigene interne Mechanismen für die Aufrechterhaltung der Konsistenz und die Behandlung von Parallelität. Das Erstellen mehrerer Shards trägt zum Erhöhen der Parallelität in einem Knoten bei und kann die Leistung verbessern. Das Aufrechterhalten der Leistung beim Skalieren ist jedoch ein Balanceakt. Je mehr Knoten und Shards ein Cluster enthält, desto mehr Aufwand ist erforderlich, um die Arbeit des Clusters zu synchronisieren. Dadurch kann der Durchsatz verringert werden. Für alle gegebenen Workloads gibt es einen Vorteil, mit dem die Erfassungsleistung maximiert und der Wartungsaufwand minimiert wird. Dieser Vorteil hängt stark von der Art der Workload und des Cluster ab, insbesondere von Volumen, Größe und Inhalt von Dokumenten, Erfassungsrate und der Hardware, auf der das System ausgeführt wird.

In diesem Abschnitt werden die Ergebnisse von Untersuchungen der Größenanpassung von Clustern zur Unterstützung der von den zuvor beschriebenen Leistungstests verwendeten Workload zusammengefasst. Der gleiche Test erfolgte in Clustern mit virtuellen Maschinen basierend auf der Größe der großen VM (Standard-D4 mit 8 CPU-Kernen, 16 Datenträger und 28 GB RAM), auf der Ubuntu Linux 14.0.4 ausgeführt wird, die jedoch mit einer unterschiedlichen Anzahl von Knoten und Shards konfiguriert ist. Die Ergebnisse gelten nicht als endgültig, da sie nur für ein bestimmtes Szenario zutreffen, sie dienen jedoch als guter Ausgangspunkt für die Analyse der horizontalen Skalierbarkeit Ihrer Cluster und zum Generieren von Zahlen für das optimale Verhältnis von Shards zu Knoten, die Ihren Anforderungen am besten entsprechen.

### Grundwertergebnisse – 3 Knoten

Der Leistungstest der Datenerfassung wurde für einen Cluster mit 3 Knoten, 5 Shards und 1 Replikat ausgeführt, um einen Grundwert zu erhalten. Dies ist die Standardkonfiguration für einen Elasticsearch-Index. In dieser Konfiguration verteilt Elasticsearch 2 primäre Shards auf 2 der Knoten, und der verbleibende primäre Shard wird auf dem dritten Knoten gespeichert. In der folgenden Tabelle wird der Durchsatz im Hinblick auf die Sammelerfassungsvorgänge pro Sekunde und die Anzahl der vom Test erfolgreich gespeicherten Dokumente zusammengefasst.

> [AZURE.NOTE] In den folgenden Tabellen in diesem Abschnitt wird die Verteilung der primären Shards als eine Zahl für die einzelnen Knoten getrennt durch Bindestriche angezeigt. Beispielsweise wird das Layout mit 5 Shards und 3 Knoten als „2-2-1“ beschrieben. Das Layout von Replikat-Shards ist nicht enthalten; diese folgen einem ähnlichen Schema wie die primären Shards.

| Konfiguration | Anzahl von Dokumenten | Durchsatz (Vorgänge/s) | Shard-Layout |
|---------------|--------------|-----------------------------|--------------|
| 5 Shards | 200560412 | 27,86 | 2-2-1 |

### 6 Knoten – Ergebnisse

Der Test wurde auf einem Cluster mit 6 Knoten wiederholt. Mit diesen Tests wurde versucht, die Auswirkungen der Speicherung von mehr als einem Shard in einem Knoten zu genauer zu ermitteln.

| Konfiguration | Anzahl von Dokumenten | Durchsatz (Vorgänge/s) | Shard-Layout |
|---------------|--------------|-----------------------------|--------------|
| 4 Shards | 227360412 | 31,58 | 1-1-0-1-1-0 |
| 7 Shards | 268013252 | 37,22 | 2-1-1-1-1-1 |
| 10 Shards | 258065854 | 35,84 | 1-2-2-2-1-2 |
| 11 Shards | 279788157 | 38,86 | 2-2-2-1-2-2 |
| 12 Shards | 257628504 | 35,78 | 2-2-2-2-2-2 |
| 13 Shards | 300126822 | 41,68 | 2-2-2-2-2-3 |

Diese Ergebnisse weisen offenbar auf folgende Trends hin:

* Mehrere Shards pro Knoten verbessern den Durchsatz. Bei der kleinen Anzahl von Shards pro Knoten, die für diese Tests erstellt wurden, wurde dieses Phänomen aus zuvor beschriebenen Gründen erwartet.

* Eine ungerade Anzahl von Shards bietet eine bessere Leistung als eine gerade Anzahl. Die Gründe dafür sind weniger deutlich; *möglicherweise* ist der von Elasticsearch verwendete Routingalgorithmus in diesem Fall besser in der Lage, die Daten über Shards hinweg zu verteilen, was zu einer gleichmäßigeren Auslastung pro Knoten führt.

Um diese Hypothesen zu testen, wurden einige weitere Tests mit einer größeren Anzahl von Shards ausgeführt. Auf mündliche Ratschläge von Elasticsearch wurde beschlossen, eine Primzahl als Anzahl von Shards für jeden Test zu verwenden, da diese eine sinnvolle Verteilung der ungeraden Zahlen für den relevanten Bereich vornehmen.

| Konfiguration | Anzahl von Dokumenten | Durchsatz (Vorgänge/s) | Shard-Layout |
|---------------|--------------|-----------------------------|-------------------|
| 23 Shards | 312844185 | 43,45 | 4-4-4-3-4-4 |
| 31 Shards | 309930777 | 43,05 | 5-5-5-5-6-5 |
| 43 Shards | 316357076 | 43,94 | 8-7-7-7-7-7 |
| 61 Shards | 305072556 | 42,37 | 10-11-10-10-10-10 |
| 91 Shards | 291073519 | 40,43 | 15-15-16-15-15-15 |
| 119 Shards | 273596325 | 38,00 | 20-20-20-20-20-19 |

Diese Ergebnisse deuten darauf hin, dass bei ca. 23 Shards ein Wendepunkt erreicht wurde. Nach diesem Punkt verursachte das Erhöhen der Shard-Anzahl eine geringfügige Beeinträchtigung der Leistung (der Durchsatz für 43 Shards ist möglicherweise eine Anomalie).

### 9 Knoten – Ergebnisse

Die Tests wurden mit einem Cluster von 9 Knoten wiederholt; dabei wurde erneut eine Primzahl als Shard-Anzahl verwendet.

| Konfiguration | Anzahl von Dokumenten | Durchsatz (Vorgänge/s) | Shard-Layout |
|---------------|--------------|-----------------------------|----------------------------|
| 17 Shards | 325165364 | 45,16 | 2-2-2-2-2-2-2-2-1 |
| 19 Shards | 331272619 | 46,01 | 2-2-2-2-2-2-2-2-3 |
| 29 Shards | 349682551 | 48,57 | 3-3-3-4-3-3-3-4-3 |
| 37 Shards | 352764546 | 49,00 | 4-4-4-4-4-4-4-4-5 |
| 47 Shards | 343684074 | 47,73 | 5-5-5-6-5-5-5-6-5 |
| 89 Shards | 336248667 | 46,70 | 10-10-10-10-10-10-10-10-9 |
| 181 Shards | 297919131 | 41,38 | 20-20-20-20-20-20-20-20-21 |

Diese Ergebnisse zeigten ein ähnliches Muster mit einem Wendepunkt bei etwa 37 Shards.

### Horinzontale Skalierung: Schlussfolgerungen

Bei Verwendung eine groben Extrapolation deuteten die Ergebnisse der Tests mit 6 und 9 Knoten darauf hin, dass in diesem Szenario die ideale Anzahl von Shards zur Optimierung der Leistung 4n +/-1 war, wobei n die Anzahl der Knoten ist. Dies ist *möglicherweise* eine Funktion der Anzahl von verfügbaren Threads zum Masseneinfügen, die wiederum von der Anzahl der CPU-Kerne abhängt. Dabei gilt folgende Begründung (Details finden Sie unter [Muster bei mehreren Dokumenten](https://www.elastic.co/guide/en/elasticsearch/guide/current/distrib-multi-doc.html#distrib-multi-doc)):

* Jede von der Clientanwendung gesendete Masseneinfügungsanfrage wird von einem einzelnen Datenknoten empfangen.

* Der Datenknoten erstellt eine Masseneinfügungsanfrage für jeden von der ursprünglichen Anfrage betroffenen primären Shard und leitet sie gleichzeitig an die anderen Knoten weiter.

* Während die einzelnen primären Shards geschrieben werden, wird eine weitere Anfrage für diesen Shard an jedes Replikat gesendet. Der primäre Shard wartet mit dem Beenden, bis die an das Replikat gesendete Anfrage abgeschlossen ist.

Elasticsearch erstellt standardmäßig einen Masseneinfügungsthread für jeden verfügbaren CPU-Kern auf einer virtuellen Maschine. Bei den in diesem Test verwendeten D4-VMs enthielt jede CPU 8 Kerne, also wurden 8 Masseneinfügungsthreads erstellt. Der verwendete Index umfasste 4 (in einem Fall 5) primäre Shards auf jedem Knoten, es befanden sich jedoch auch 4 (5) Replikate auf jedem Knoten. Das Einfügen von Daten in diese Shards und Replikate hat auf jedem Knoten pro Anfrage bis zu 8 Threads beansprucht; dies entsprach der verfügbaren Anzahl. Das Erhöhen oder Verringern der Shard-Anzahl verursacht möglicherweise Threading-Ineffizienzen, da Threads u. U. unbelegt bleiben oder Anfragen in der Warteschlange platziert werden. Ohne weitere Experimente ist dies jedoch nur eine Theorie und kann nicht als endgültig betrachtet werden.

Die Tests veranschaulichten auch einen anderen wichtigen Punkt. In diesem Szenario kann das Erhöhen der Knotenanzahl den Datenerfassungsdurchsatz verbessern, die Ergebnisse werden jedoch nicht unbedingt linear skaliert. Mit weiteren Tests mit Clustern mit 12 und 15 Knoten könnte der Punkt ermittelt werden, an dem eine horizontale Skalierung wenig zusätzliche Vorteile bringt. Wenn diese Anzahl der Knoten nicht genügend Speicherplatz bietet, müssen Sie möglicherweise zur zentralen Hochskalierungsstrategie zurückkehren und weitere oder größere, auf Premium-Speicher basierende Datenträger verwenden.

> [AZURE.IMPORTANT] Das Verhältnis 4n+/-1 ist keine Standardlösung, die für jeden Cluster geeignet ist. Wenn weniger oder mehr CPU-Kerne verfügbar sind, sieht die optimale Shard-Konfiguration möglicherweise anders aus. Die Ergebnisse basierten auf einem bestimmten Workload, der nur für die Datenerfassung zuständig war. Bei Workloads, die Abfragen und Aggregationen enthalten, können sich unterschiedliche Ergebnisse ergeben.

> Der Datenerfassungsworkload hat einen einzigen Index verwendet. In den meisten Fällen sind die Daten wahrscheinlich auf mehrere Indizes verteilt, was zu verschiedenen Mustern oder Ressourcen führt.

> Bei dieser Übung geht es eher darum, die angewandte Methode zu verstehen anstatt die erhaltenen Ergebnisse. Danach sollten Sie selbst eine Skalierungsbewertung für Ihre eigenen Workloads durchführen können, um Informationen abzurufen, die am ehesten auf Ihr eigenes Szenario zutreffen.

<span id="_Considerations_for_Tuning" class="anchor"></span>
## Überlegungen zur Optimierung einer umfangreichen Datenerfassung

Elasticsearch ist hochgradig konfigurierbar. Mit vielen Switches und Einstellungen können Sie die Leistung für bestimmte Anwendungsfälle und Szenarien optimieren. In diesem Abschnitt finden Sie einige häufige Beispiele. Die Flexibilität, die Elasticsearch in dieser Hinsicht bietet, ist jedoch mit Vorsicht zu genießen – durch eine falsche Abstimmung von Elasticsearch kann sich die Leistung schnell verschlechtern. Nehmen Sie bei der Optimierung nie mehrere Änderungen gleichzeitig vor, und prüfen Sie stets die Ergebnisse, um negative Auswirkungen auf Ihr System auszuschließen.

### Optimieren von Ressourcen für Indizierungsvorgänge

In der folgenden Liste finden Sie einige Punkte, die Sie berücksichtigen sollten, wenn Sie einen Elasticsearch-Cluster zur Unterstützung einer umfangreichen Datenerfassung optimieren. Die ersten beiden Elemente haben wahrscheinlich die größten wahrnehmbaren Auswirkungen auf die Leistung. Die restlichen sind je nach Workload marginaler:

*  Neue Dokumente, die zu einem Index hinzugefügt werden, sind nur für Suchvorgänge sichtbar, wenn der Index aktualisiert wird. Die Aktualisierung eines Indexes ist ein aufwendiger Prozess und erfolgt daher in regelmäßigen Abständen und nicht bei jeder Dokumenterstellung. Das standardmäßige Aktualisierungsintervall beträgt 1 Sekunde. Beim Durchführen von Massenvorgängen sollten Sie eine vorübergehende Deaktivierung der Indexaktualisierung erwägen. Setzen Sie dazu den Index *refresh\_interaval* auf -1.

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"refresh_interval": -1
		}
	}
	```

	Sie können eine Aktualisierung manuell auslösen, indem Sie der [*\_refresh*](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-refresh.html)-API am Ende des Vorgangs verwenden, um die Daten sichtbar zu machen. Weitere Informationen erhalten Sie im Abschnitt zur [Verwendung der Massenindizierung](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-update-settings.html#bulk). Weitere Informationen zu den [Auswirkungen der Änderung des Aktualisierungsintervalls für die Datenerfassung](#the-impact-of-changing-the-index-refresh-interval-on-data-ingestion-performance) finden Sie weiter unten.

* Beim Replizieren eines Indexes wird jeder Indizierungsvorgang (Dokument erstellen, aktualisieren oder löschen) in den Replikat-Shards so wiederholt, wie er im primären Shard erfolgt. Deaktivieren Sie die Replikation während Massenimportvorgängen und aktivieren Sie diese nach Abschluss des Imports wieder:

    ```http
	PUT /my_busy_index
	{
		"settings" : {
			"number_of_replicas": 0
		}
	}
	```

	Wenn Sie die Replikation erneut aktivieren, überträgt Elasticsearch Daten Byte für Byte über das Netzwerk aus dem Index in jedes Replikat. Dies ist viel effizienter, als den Indizierungsprozess für jedes Dokument auf jedem Knoten zu wiederholen. Beim Durchführen des Massenimports besteht die Gefahr von Datenverlust, falls der primäre Knoten ausfällt. Zum Wiederherstellen reicht es vermutlich aus, den Import erneut zu starten. Auf die [Auswirkung der Replikation auf die Datenerfassungsleistung](#the-impact-of-replicas-on-data-ingestion-performance) gehen wir später genauer ein.

* Elasticsearch versucht, die verfügbaren Ressourcen gleichmäßig für die Abfrage und Erfassung von Daten zu verteilen. Das verringert möglicherweise die Datenerfassungsleistung (Drosselungsereignisse werden im Elasticsearch-Protokoll aufgezeichnet). Diese Einschränkung soll verhindern, dass eine große Anzahl von Indexsegmenten gleichzeitig erstellt wird, die auf einem Datenträger zusammengeführt und gespeichert werden müssen und zu einer Monopolisierung der Ressourcen führen können. Wenn in Ihrem System gerade keine Abfragen durchgeführt werden, können Sie die Drosselung der Datenerfassung deaktivieren. Somit können Sie eine maximale Indizierungsleistung erreichen. Sie können die Drosselung für einen gesamten Cluster wie folgt deaktivieren:

	```http
	PUT /_cluster/settings
	{
		"transient" : {
			"indices.store.throttle.type": "none"
		}
	}
	```

  > [AZURE.IMPORTANT] Ändern Sie den Drosselungstyp des Clusters in *"merge"* (Zusammenführen) zurück, wenn die Erfassung abgeschlossen wurde. Beachten Sie zudem, dass das Deaktivieren der Drosselung zu Instabilität im Cluster führen kann. Stellen Sie daher unbedingt sicher, dass Sie den Cluster bei Bedarf wiederherstellen können.

* Elasticsearch reserviert einen Anteil des Heapspeiches für Indizierungsvorgänge; der Rest wird hauptsächlich von Abfragen und Suchvorgängen belegt. Diese Puffer dienen dazu, die Anzahl der Datenträger-E/A-Vorgänge zu verringern, um weniger größere statt mehrere kleinere Schreibvorgänge auszuführen. Standardmäßig werden 10 % des Heapspeichers zugewiesen. Wenn Sie eine große Datenmenge indizieren, reicht dieser Wert möglicherweise nicht aus. Für Systeme, die die Erfassung großer Datenmengen unterstützen, sollten Sie bis zu 512 MB Arbeitsspeicher für jeden aktiven Shard im Knoten zulassen. Wenn Sie beispielsweise Elasticsearch auf D4-VMs (28 GB RAM) ausführen und 50 % des verfügbaren Arbeitsspeichers der JVM (14 GB) zugeordnet haben, stehen 1,4 GB für Indexvorgänge zur Verfügung. Enthält ein Knoten 3 aktive Shards, ist diese Konfiguration wahrscheinlich ausreichend. Enthält ein Knoten jedoch noch mehr Shards, erhöhen Sie den Wert des Parameters *indices.memory.index\_buffer\_size* in der Konfigurationsdatei „elasticsearch.yml“. Weitere Informationen finden Sie unter [Leistungsüberlegungen zur Elasticsearch-Indizierung](https://www.elastic.co/blog/performance-considerations-elasticsearch-indexing).

  > [AZURE.NOTE] Wenn Sie mehr als 512 MB pro aktiven Shard zuweisen, wird die Indizierungsleistung wahrscheinlich nicht verbessert, sondern verschlechtert, da weniger Speicher für die Ausführung anderer Aufgaben verfügbar ist. Bedenken Sie auch Folgendes: Wenn Sie den Indexpuffern mehr Heapspeicher zuweisen, steht weniger Speicher für andere Vorgänge wie das Suchen und Aggregieren von Daten zur Verfügung, und die Leistung von Abfragevorgängen wird möglicherweise beeinträchtigt.

* Elasticsearch schränkt die Anzahl der Threads ein (der Standardwert ist 8), die gleichzeitig Indizierungsvorgänge in einem Shard ausführen können. Enthält ein Knoten nur eine geringe Anzahl von Shards, sollten Sie die *index\_concurrency*-Einstellung für einen Index erhöhen, der einer großen Menge von Indizierungsvorgängen unterliegt oder ein Masseneinfügungsziel ist:

	```http
	PUT /my_busy_index
	{
		"settings" : {
			"index_concurrency": 20
		}
	}
	```

* Wenn Sie viele Indizierungs- und Massenvorgänge über einen kurzen Zeitraum ausführen, können Sie die Anzahl der verfügbaren *Index*- und *Massenthreads* im Threadpool erhöhen und die Größe der *Masseneinfügungswarteschlange* für jeden Datenknoten erweitern. Dadurch können mehr Anfragen in die Warteschlange gestellt werden anstatt verworfen zu werden. Weitere Informationen finden Sie im Abschnitt zum [Threadpool](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-threadpool.html). Wenn Sie über längere Zeit eine umfangreiche Datenerfassung ausführen, ist eine Aufstockung der Massenthreads nicht ratsam. Erstellen Sie stattdessen zusätzliche Knoten, und verwenden Sie die Indizierungsauslastung mittels Sharding auf diese Knoten. Alternativ können Sie Masseneinfügungsbatches seriell statt parallel senden, da dadurch ein natürlicher Drosselungsmechanismus entsteht, der die Wahrscheinlichkeit von Fehlern verringert, die durch einen Überlauf der Masseneinfügungswarteschlange verursacht wurden.

### Die Auswirkungen der Änderung des Indexaktualisierungsintervalls auf die Datenerfassungsleistung

Das Aktualisierungsintervall bestimmt die Rate, mit der erfassten Daten für Abfragen und Aggregationen sichtbar werden. Die häufige Aktualisierung kann jedoch die Leistung von Datenerfassungsvorgängen beeinträchtigen. Das standardmäßige Aktualisierungsintervall beträgt 1 Sekunde. Sie können die Aktualisierung komplett deaktivieren. Dieser Ansatz ist jedoch für Ihren Workload unter Umständen nicht geeignet. Sie können experimentieren, indem Sie verschiedene Intervalle testen und den Punkt finden, der die Erfassungsleistung gegen die Notwendigkeit ausgleicht, aktuelle Informationen darzustellen.

Zur Veranschaulichung diesef Auswirkung wurde beispielsweise der Datenerfassungsleistungstest auf einem Elasticsearch-Cluster mit 7 Shards auf 3 Datenknoten wiederholt. Der Index verfügt über ein einziges Replikat. Jeder Datenknoten basierte auf einer D4-VM (28 GB RAM, 8 Prozessorkerne) mit einem flüchtigen Speicher mit SSD-Sicherung zum Aufnehmen der Daten. Jeder Test wurde über eine Stunde ausgeführt.

In diesem Test wurde als Aktualisierungsrate der Standardwert von 1 Sekunde festgelegt. Die folgende Tabelle zeigt die Durchsatz- und Reaktionszeiten für diesen Test im Vergleich zu einer Ausführung, in der die Aktualisierungsrate auf 30 Sekunden reduziert war.

| Aktualisierungsrate | Anzahl der Stichproben | Durchschnittliche Reaktionszeit – erfolgreiche Vorgänge (ms) | Durchsatz – erfolgreiche Vorgänge (Vorgänge/s) |
|--------------|------------|----------------------------------------------------|---------------------------------------------------|
| 1 Sekunde | 93755 | 460 | 26,0 |
| 30 Sekunden | 117758 | 365 | 32,7 |

In diesem Test führte die Verringerung der Aktualisierungsrate zu einer Steigerung von 18 % beim Durchsatz und einer Reduzierung der durchschnittlichen Reaktionszeit um 21 %. Die folgenden Diagramme von Marvel zeigen die Hauptursache für diesen Unterschied auf. Abbildung 2 zeigt die Index-Zusammenführungsaktivität, bei der das Aktualisierungsintervall auf 1 Sekunde festgelegt ist, und Abbildung 3 zeigt die Aktivitätsebene, bei der das Aktualisierungsintervall auf 30 Sekunden festgelegt ist. Indexzusammenführungen werden durchgeführt, um zu verhindern, dass die Anzahl der Indexsegmente zu groß wird. Ein Aktualisierungsintervall von 1 Sekunde führt zur einer großen Anzahl von kleinen Segmenten, die häufig zusammengeführt werden müssen, während ein Aktualisierungsintervall von 30 Sekunden weniger große Segmente generiert, die optimaler zusammengeführt werden können.

![](media/guidance-elasticsearch-data-ingestion-image15.png)

***Abbildung 2. Indexzusammenführungsaktivität mit Indexaktualisierungsrate von 1 Sekunde***

![](media/guidance-elasticsearch-data-ingestion-image16.png)

***Abbildung 3 Indexzusammenführungsaktivität mit Indexaktualisierungsrate von 30 Sekunden***

### Die Auswirkung der Replikate auf die Datenerfassungsleistung

Replikate sind ein wichtiges Feature eines robusten Clusters. Ohne sie besteht die Gefahr von Datenverlust, wenn ein Knoten ausfällt. Replikate erhöhen jedoch die Anzahl der ausgeführten Datenträger sowie Netzwerk-E/A und beeinträchtigen möglicherweise die Rate der Datenerfassung. Wie bereits erwähnt, kann es von Vorteil sein, Replikate während des Uploads großer Datenmengen vorübergehend zu deaktivieren.

Datenerfassungsleistungstests wurden mit drei Konfigurationen wiederholt:

* Ein Cluster ohne Replikate

* Ein Cluster mit 1 Replikat

* Ein Cluster mit 2 Replikaten

In allen Fällen wurde der aus 7 Shards bestehende Cluster auf 3 Knoten verteilt und auf wie bei den vorherigen Tests konfigurierten VMs ausgeführt. Der Testindex verwendete ein Aktualisierungsintervall von 30 Sekunden.

In der folgenden Tabelle werden die Reaktionszeiten und der Durchsatz der einzelnen Tests zu Vergleichszwecken zusammengefasst:

| Konfiguration | Anzahl der Stichproben | Durchschnittliche Reaktionszeit – erfolgreiche Vorgänge (ms) | Durchsatz – erfolgreiche Vorgänge (Vorgänge/s) | Anzahl der Fehler bei der Datenerfassung |
|---------------|------------|----------------------------------------------------|---------------------------------------------------|--------------------------|
| 0 Replikate | 215451 | 200 | 59,8 | 0 |
| 1 Replikat: | 117758 | 365 | 32,7 | 0 |
| 2 Replikate | 94218 | 453 | 26,1 | 194262 |


Der Leistungsabfall, wenn die Anzahl an Replikaten erhöht wird, ist eindeutig. Beachten Sie jedoch auch die große Menge an Datenerfassungsfehlern im dritten Test. Die Fehlermeldungen zeigten, dass die durch einen Überlauf der Masseneinfügungswarteschlange verursachten Fehler dazu führten, dass Anfragen abgewiesen wurden. Diese Ablehnungen erfolgten sehr schnell – daher die große Anzahl.

> [AZURE.NOTE]  Die Ergebnisse des dritten Tests zeigen, wie wichtig eine intelligente Wiederholungsstrategie ist, wenn vorübergehende Fehler wie diese auftreten: Warten Sie vor dem Wiederholen der Masseneinfügung einen Moment, bis die Masseneinfügungswarteschlange geleert ist.

In den nächsten Diagrammen werden die Reaktionszeiten während den Tests verglichen. In allen Fällen veranschaulicht das erste Diagramm die gesamten Reaktionszeiten, während sich das zweite Diagramm auf die Reaktionszeiten für die schnellsten Vorgänge beschränkt (Beachten Sie, dass die Skala des ersten Diagramms im Vergleich zum zweiten die zehnfache Menge aufweist). Sie können sehen, wie das Profil der Reaktionszeiten bei den drei Tests variiert.

Ohne Replikate nahmen die meisten Vorgänge zwischen 75 ms und 750 ms in Anspruch, die schnellste Reaktionszeit betrug 25 ms:

![](media/guidance-elasticsearch-data-ingestion-image17.png)

Bei einem Replikat lag die operative Reaktionszeit im Bereich von 125 ms bis 1250 ms. Die schnellsten Reaktionen dauerten etwa 75 ms, auch wenn weniger dieser schnellen Reaktionen verzeichnet wurden als bei den Fällen ohne Replikate. Es gab auch weitaus mehr Reaktionen, die wesentlich länger benötigten als die in den meisten Fällen verzeichneten 1250 ms:

![](media/guidance-elasticsearch-data-ingestion-image18.png)

Bei 2 Replikaten lag die Reaktionszeit zwischen 200 ms und 1500 ms. Jedoch lagen weitaus weniger Ergebnisse unter dem minimalen Bereich als beim Test mit einem Replikat. Das Muster der Ergebnisse über der oberen Grenze weist jedoch große Ähnlichkeiten zum Test mit einem Replikat auf. Dies ist wahrscheinlich auf die Auswirkungen des Überlaufs der Masseneinfügungswarteschlange zurückzuführen (ab einer Warteschlangenlänge von 50 Anfragen). Der zusätzliche Verwaltungsaufwand bei 2 Replikaten führt häufiger zu einem Warteschlangenüberlauf, der übermäßig lange Reaktionszeiten bei Erfassungsvorgängen verhindert. Vorgänge werden schnell abgelehnt, anstatt viel Zeit in Anspruch zu nehmen, was möglicherweise zu Timeoutausnahmen führt oder die Reaktionsfähigkeit von Clientanwendungen beeinträchtigt (Dies ist der Zweck des Mechanismus der Masseneinfügungswarteschlange.):

![](media/guidance-elasticsearch-data-ingestion-image19.png)

<span id="_The_Impact_of_1" class="anchor"><span id="_Impact_of_Increasing" class="anchor"></span></span>Mit Marvel erfahren Sie, wie sich die Anzahl der Replikate auf die Massenindizierungswarteschlange auswirkt. Abbildung 4 zeigt die Daten aus Marvel, die veranschaulichen, wie die Masseneinfügungswarteschlange beim Test gefüllt wird. Die durchschnittliche Warteschlangenlänge betrug etwa 40 Anfragen. Regelmäßige Spitzen verursachten jedoch einen Überlauf, wodurch Anfragen abgelehnt wurden:

![](media/guidance-elasticsearch-data-ingestion-image20.png)

***Abbildung 4 Größe der Massenindizierungswarteschlange und Anzahl der abgelehnten Anfragen bei 2 Replikaten.***

Vergleichen Sie dies mit Abbildung 5, die die Ergebnisse mit einem einzelnen Replikat veranschaulicht. Das Elasticsearch-Modul konnte Anfragen schnell genug verarbeiten, um die durchschnittliche Warteschlangenlänge bei etwa 25 zu halten. Da die Länge der Warteschlange zu keinem Zeitpunkt mehr als 50 Anfragen enthielt, wurde somit keine Arbeit abgelehnt.

![](media/guidance-elasticsearch-data-ingestion-image21.png)

***Abbildung 5. Größe der Massenindizierungswarteschlange und Anzahl der abgelehnten Anfragen bei einem Replikat.***

## Bewährte Methoden für Clients, die Daten an Elasticsearch senden

Viele Aspekte der Leistung werden nicht nur intern innerhalb des Systems, sondern auch durch die Auslastung des Systems durch Clientanwendungen beeinflusst. Elasticsearch bietet viele Funktionen, die von der Datenerfassung verwendet werden können, um z. B. eindeutige Bezeichner für Dokumente zu generieren, Dokumentanalysen auszuführen und sogar durch Skripterstellung Daten beim Speichern zu transformieren. Allerdings tragen all diese Funktionen zur Last auf das Elasticsearch-Modul bei, und in vielen Fällen können sie effizienter ausgeführt werden, wenn vor der Übertragung Clientanwendungen hinzugefügt werden. Darüber hinaus sollten Sie gegebenenfalls die folgenden Methoden erwägen:

> [AZURE.NOTE] Diese Liste der bewährten Methoden trifft eher auf das Erfassen von neuen Daten zu als auf das Ändern vorhandener Daten, die bereits in einem Index gespeichert sind. Datenerfassungsworkloads werden von Elasticsearch als Anfügevorgänge ausgeführt. Datenänderungen wie Löschen und Anfügen werden hierbei ausgeführt. Das liegt daran, dass Dokumente in einem Index unveränderlich sind. Soll ein Dokument geändert werden, muss das gesamte Dokument durch eine neue Version ersetzt werden. Sie können entweder eine HTTP-PUT-Anfrage durchführen, um ein vorhandenes Dokument zu überschreiben, oder Sie können mit der *update*-API von Elasticsearch eine Anfrage abstrahieren, die ein vorhandenes Dokument abruft, die Änderungen zusammenführt und dann das neue Dokument mit der PUT-Methode speichert.

* Deaktivieren Sie die Textanalyse für Indexfelder, die nicht analysiert werden müssen. Bei der Analyse wird Text mit Token versehen, um die Suche nach bestimmten Begriffen zu ermöglichen. Jedoch kann das zu einer hohen CPU-Auslastung führen und sollte nur selektiv durchgeführt werden. Wenn Sie Elasticsearch zum Speichern von Protokolldaten verwenden, kann es hilfreich sein, die detaillierten Protokollmeldungen mit Token zu versehen, um komplexe Suchvorgänge zu ermöglichen. Andere Felder, die z. B. Fehlercodes oder Bezeichner enthalten, sollten besser nicht mit Token versehen werden (wie häufig fordern Sie beispielsweise die Details aller Meldungen an, deren Fehlercode eine „3“ enthält?) Mit dem folgenden Code deaktivieren Sie die Analyse für die *name* und *hostip*-Felder im *Protokolltyp* des *systembase*-Index:

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			...
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			...
		}
	}
	```

* Deaktivieren Sie das *\_all*-Feld eines Indexes, wenn es nicht benötigt wird. Das Feld *\_all* verkettet die Werte der anderen Felder im Dokument zur Analyse und Indizierung. Es eignet sich zum Ausführen von Anfragen, die jedem Feld in einem Dokument entsprechen können. Wenn Clients und Namensfelder übereinstimmen, hat die Aktivierung von *\_all* lediglich eine hohe CPU- und Speicherauslastung zur Folge. Das folgende Beispiel zeigt die Vorgehensweise beim Deaktivieren des Felds *\_all* für den *Protokolltyp* im Index *systembase*.

	```http
	PUT /systembase
	{
		"settings" : {
			...
		},
		"logs" : {
			"_all": {
				"enabled" : false
			},
			...,
		...
		}
	}
	```

    Beachten Sie, dass Sie eine selektive Variante von *\_all* erstellen können, die nur Informationen über bestimmte Felder enthält. Weitere Informationen finden Sie unter [Deaktivieren des „\_all“-Felds](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-all-field.html#disabling-all-field).

* Vermeiden Sie dynamische Zuordnungen in Indizes. Die dynamische Zuordnung ist ein leistungsstarkes Feature, doch zum Hinzufügen neuer Felder zu einem vorhandenen Index müssen Änderungen an der Indexstruktur zwischen Knoten koordiniert werden. Das kann vorübergehend zu Indexsperren führen. Die dynamische Zuordnung kann auch zu einem starken Anstieg der Anzahl von Feldern und der entsprechenden Menge an Index-Metadaten führen. Diese Ergebnisse wiederum führen zu einem erhöhten Speicherbedarf und E/A bei der Erfassung von Daten und Ausführung von Anfragen. Diese beiden beeinträchtigen die Leistung. Deaktivieren Sie die dynamische Zuordnung, und definieren Sie Ihre Indexstrukturen explizit. Weitere Informationen finden Sie unter [Dynamische Feldzuordnung](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html#dynamic-field-mapping).

* Sorgen Sie für ausgewogene Workloads, um in Konflikt stehende Anfragen zu erfüllen. Bedenken Sie stets, dass die Datenerfassung erhebliche Auswirkungen auf die Leistung von anderen gleichzeitigen Vorgängen haben kann – z. B. Benutzer, die Anfragen durchführen. Die Datenerfassung ist möglicherweise plötzlichen Verkehrsspitzen ausgesetzt. Wenn das System versucht, all die eingehenden Daten sofort zu verarbeiten, könnte der Zufluss die Anfrageraten beträchtlich verlangsamen. Elasticsearch versucht, diese Situation durch das Regulieren der Rate, mit der Erfassungsanfragen über die Masseneinfügungswarteschlange (weitere Informationen im Abschnitt [Ermitteln der begrenzenden Faktoren – CPU-Auslastung](#determining-limiting-factors-cpu-utilization)) verarbeitet werden, zu verhindern. Dies sollte jedoch als letzter Ausweg verwendet werden – wenn Ihr Anwendungscode nicht bereit ist, abgelehnte Anfragen zu verarbeiten, besteht das Risiko von Datenverlust. Verwenden Sie stattdessen ein Muster wie z. B. einen [warteschlangenbasierten Lastenausgleich](https://msdn.microsoft.com/library/dn589783.aspx), um die Rate der Datenweitergabe an Elasticsearch zu regeln.

* Stellen Sie sicher, dass dem Cluster genügend Ressourcen zum Verarbeiten des Workloads zur Verfügung stehen, insbesondere dann, wenn Indizes mit mehreren Replikaten konfiguriert sind.

* Verwenden Sie die Masseneinfügungs-API, um große Dokumentbatches hochzuladen. Passen Sie die Anfragegröße entsprechend an. Manchmal wirken sich größere Batches nicht positiv auf die Leistung aus und können zu einer Überlastung von Elasticsearch-Threads und anderen Ressourcen führen und somit andere gleichzeitige Vorgänge verzögern. Die Dokumente in einem Masseneinfügungsstapel werden im Arbeitsspeicher auf dem koordinierenden Knoten aufrechterhalten, während der Vorgang ausgeführt wird. Die physische Größe von jedem Batch ist wichtiger als die Dokumentanzahl. Zur idealen Batchgröße gibt es keine feste Regelung. In der Elasticsearch-Dokumentation werden jedoch als Ausgangsgröße zwischen 5 MB und 15 MB empfohlen. Führen Sie Leistungstests durch, um die optimale Batchgröße für eigene Szenarien und Workloads zu ermitteln

* Stellen Sie sicher, dass die Masseneinfügungsanfragen auf Knoten verteilt werden anstatt auf einen einzelne Knoten. Werden alle Anfragen an einen einzelnen Knoten weitergeleitet, kann das zur Ausschöpfung des Speichers führen, da jede verarbeitete Masseneinfügungsanfrage im Knoten im Arbeitsspeicher gespeichert wird. Sie können auch die Netzwerklatenz erhöhen, wenn Anfragen an andere Knoten umgeleitet werden.

* Elasticsearch verwendet ein Quorum, das beim Schreiben von Daten aus dem Großteil der primären und Replikatknoten besteht. Schreibvorgänge werden nur mit erfolgreichem Quorum abgeschlossen. Mit diesem Ansatz wird sichergestellt, dass Daten nicht geschrieben werden, wenn die Mehrheit der Knoten aufgrund eines Netzwerkpartitionsereignisses (Ausfalls) nicht verfügbar ist. Die Verwendung eines Quorums kann die Leistung der Schreibvorgänge beeinträchtigen. Sie können das quorumbasierte Schreiben deaktivieren, indem Sie den *Konsistenzparameter* beim Schreiben von Daten auf *1* festlegen. Im folgenden Beispiel wird ein neues Dokument hinzugefügt, sobald das Schreiben in den primären Shard abgeschlossen wurde.

	```http
	PUT /my_index/my_data/104?consistency=one
	{
		"name": "Bert",
		"age": 23
	}
	```

	Beachten Sie, dass das Deaktivieren des quorumbasierten Schreibens wie bei der asynchronen Replikation zu Inkonsistenzen zwischen dem primären Shard und den Replikaten führen kann.

* Wenn Quoren verwendet werden, wartet Elasticsearch, ob genügend Knoten verfügbar sind, bevor bestimmt wird, dass ein Schreibvorgang abgebrochen werden soll, da kein Quorum erreicht werden kann. Diese Wartezeit wird mit dem Timeout-Abfrageparameter festgelegt (der Standardwert ist 1 Minute). Sie können diese Einstellung mit dem Timeout-Abfrageparameter ändern. Im folgenden Beispiel wird ein neues Dokument erstellt und bis zu 5 Sekunden auf das Quorum gewartet, bevor der Vorgang abgebrochen wird:

	```http
	PUT /my_index/my_data/104?timeout=5s
	{
		"name": "Sid",
		"age": 27
	}
	```

	In Elasticsearch können Sie auch eigene, [extern generierte ](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html#_version_types) Versionsnummern verwenden.

* Deaktivieren Sie ggf. das *\_source*-Feld eines Indexes. Dieses Feld enthält eine Kopie des ursprünglichen JSON-Dokuments, das beim Speichern eines Dokuments verwendet wurde. Durch Speichern dieses Felds fallen zusätzliche Speicherkosten und Datenträger-E/A an. Jedoch handelt es sich je nach Dokumentstruktur um marginale Kosten. Bedenken Sie auch, dass das Deaktivieren des *\_source*-Felds einen Client am Ausführen der folgenden Vorgänge hindert:

	* Verwenden der Update-API, um ein Dokument zu ändern
	* Ausführen von Hervorhebungen während Anfragen
	* Erneutes Indizieren von Daten
	* Debuggen von Anfragen und Aggregationen durch Anzeige des Originaldokuments

	Im folgenden Beispiel wird das Feld *\_source*für den *Protokolltyp* im *systembase*-Index deaktiviert.

  ```http
  PUT /systembase
  {
		"settings" : {
			...
		},
		"logs" : {
			"_source": {
				"enabled": false
			},
			...,
		...
		}
  }
  ```

## Allgemeine Richtlinien für Datenerfassungsleistungstests mit Elasticsearch

Nachfolgend finden Sie einige Punkte, die Sie beim Durchführen von Leistungstests mit Elasticsearch und Analysieren der Ergebnisse berücksichtigen sollten.

* Leistungstests sind zeitaufwendig und teuer. Sammeln Sie zumindest Statistiken, die Übertragungsraten auf Datenträger und Netzwerke, CPU-Auslastung, CPU-Wartezeiten und Datenträgerlatenz (sofern möglich) messen. Dadurch erhalten Sie schnelles Feedback zu Ihren Tests und profitieren von einer guten Rendite.

* Nutzen Sie die Skriptfunktionen Ihres Auslastungstesttools, um Metriken zu erfassen, die möglicherweise andernfalls nicht verfügbar wären. Linux bietet beispielsweise unzählige zuverlässige und gute Leistungsstatistiken, die Sie mithilfe von Hilfsprogrammen wie z. B. *vmstat* und *iostat* erfassen können. Sie können die Skripterstellung mit JMeter verwenden, um diese Daten als Teil eines Testplans zu erstellen.

* Leistungstests drehen sich größtenteils um die Analyse von Statistiken, die auf zuverlässigen und wiederkehrenden Daten basieren. Halten Sie sich nicht mit übersichtlichen Metriken auf, die nicht die gewünschten Einblicke liefern. Gewinnen Sie Erkenntnisse aus den Daten, und gestalten Sie den Leistungstest als Dev-Ops-Prozess mit einer schnellen Feedbackschleife. Prüfen Sie stets die Statistiken, indem Sie Trends vergleichen und Ergebnisse bzw. Konfigurationen vergleichen. Wenn Sie diesen Vorgang regelmäßig durchführen, erhalten Sie verständliche Daten, die Sie auf Ihre Workloads übertragen können und mit denen Sie die Auswirkungen der Änderungen in der Konfiguration und Bereitstellung in Erfahrung bringen können.

* Verwenden Sie ein Tool wie Marvel, um Cluster und Knotenleistung beim Testen zu überwachen und zusätzliche Erkenntnisse zu gewinnen. JMeter kann für das Erfassen von Rohdaten für nachfolgende Analysen hilfreich sein, aber mit Marvel gewinnen Sie in Echtzeit Erkenntnisse über die Leistung und die möglichen Ursachen von Störungen und Verzögerungen. Darüber hinaus bieten viele Auslastungstesttools keine Einblicke in die internen Metriken von Elasticsearch. Nutzen und vergleichen Sie Indizierungsdurchsatzraten, und führen Sie Segmentzahlen, GC-Statistiken und Drosselungszeiten in den Indexstatistiken zusammen. Wiederholen Sie diese Analyse in regelmäßigen Abständen.

* Vergleichen Sie die Statistiken Ihres Lastentesttools mit Knotenstatistiken in Marvel (Datenträger- und Datenverkehr, CPU-Auslastung, Arbeitsspeicher und Threadpool-Nutzung), um das Korrelationsmuster zwischen den von der Infrastruktur und bestimmten Elasticsearch-Statistiken gemeldeten Ergebnissen zu verstehen.

* In der Regel sollten Sie *einen Knoten und ein Shard* als Grundlage für das Testen der Leistung und Bewerten der Anwendungskosten durch Hinzufügen von Knoten verwenden. Verlassen Sie sich jedoch nicht vollständig auf das Hochrechnen der Leistung auf Basis weniger Knoten und Shards. Die Synchronisierungs- und Kommunikationskosten im Cluster können mit steigender Anzahl an Knoten und Shards unverhältnismäßig hoch werden.

* Prüfen Sie die Shard-Zuordnung der Knoten, um Statistiken zu vergleichen. Einige Knoten verfügen über weniger Replikate und Shards, wodurch ein Ungleichgewicht bei der Ressourcenverwendung entsteht.

* Wenn Sie Auslastungstests ausführen, erhöhen Sie die Anzahl der Threads, die Ihr Testtool verwendet, um Aufgaben an den Cluster zu übermitteln, bis Fehler auftreten. Legen Sie eine *Obergrenze* für Ihre Tests fest, um nachhaltige Durchsatztests zu erzielen. Überschreitet die Fehlerrate diese Obergrenze, verursachen Fehler aufgrund der Wiederherstellungskosten für Back-End-Ressourcen. In diesen Situationen wird der Durchsatz unweigerlich sinken.

* Um zu simulieren, wie das System auf unerwartet viele Aktivitäten reagiert, können Sie Tests durchführen, die zu einer Fehlerrate führen, die Ihre Obergrenze für die Fehlerrate überschreitet. Dadurch erhalten Sie nicht nur Durchsatzzahlen im Hinblick auf Kapazität, sondern auch auf die Wiederherstellungskosten.

* Verwenden Sie eine Dokumentanzahl, um Ihr Leistungsprofil zu bewerten, und verwenden Sie Dokumente entsprechend Ihres Workloadmusters wieder. Bedenken Sie, dass sich das Leistungsprofil ändern kann, wenn weitere Dokumente hinzugefügt werden.

* Beachten Sie die SLAs für IOPS und Einschränkungen der Übertragungsraten für den verwendeten Speicher. Verschiedene Speichertypen (SSD, rotierende Medien) verwenden unterschiedliche Übertragungsraten.

* Die CPU-Leistung kann nicht durch von Datenträger- und Netzwerkaktivität abfallen: Back-End-Anwendungen können Sperr- und Kommunikationsmechanismen mit verteilter Verarbeitung verwenden, die eine Unterauslastung des Prozessors bewirken können.

* Führen von Leistungstests über mindestens zwei Stunden aus (nicht nur wenige Minuten). Die Indizierung kann die Leistung so beeinträchtigen, dass es nicht sofort bemerkt wird. Beispielsweise kann sich das Leistungsprofil im Laufe der Zeit durch Zusammenführungen der JVM-Garbage Collection-Statistiken und Indizierung ändern.

* So haben Indexaktualisierungen unter Umständen große Auswirkungen auf Datenerfassungsdurchsatz und Drosselung mit einem Cluster.

## Zusammenfassung

Es ist wichtig zu wissen, wie Ihre Lösung mit steigender Datenmenge und Anzahl der Anfragen skalieren können. Wird Elasticsearch auf Azure ausgeführt, ermöglicht das die vertikale und horizontale Skalierung. Sie können größere VMs mit mehr Ressourcen ausführen und einen Elasticsearch-Cluster auf ein Netzwerk aus mehreren VMs verteilen. Die vielen Optionen können verwirrend sein: Ist es kostengünstiger, einen Cluster auf eine große Anzahl von kleinen virtuellen Maschinen zu implementieren als einen Cluster mit einer kleinen Anzahl von großen virtuellen Maschinen, oder ist der Mittelweg die richtige Wahl? Wie viele Shards sollte jeder Index enthalten, und was sind die Vor-und Nachteile der Datenerfassung im Vergleich zu Leistungsanfragen? Die Verteilung der Shards auf den Knoten kann einen erheblichen Einfluss auf den Datenerfassungsdurchsatz haben. Mit mehr Shards können Sie die Anzahl der internen Konflikte verringern, die innerhalb eines Shards auftreten. Sie müssen diesen Vorteil jedoch gegen den Aufwand abwägen, den die Verwendung von vielen Shards auf einen Cluster haben kann. Um diese Fragen zu beantworten, sollten Sie einen Systemtest erwägen, um die am besten geeignete Strategie zu ermitteln.

Die Leistung des E/A-Subsystems des Datenträgers ist ein wichtiger Faktor für Datenerfassungsworkloads. Mit SSDs können Sie den Durchsatz steigern, da Sie die Datenträgerlatenz der Schreibvorgänge verringern. Wenn Sie keine große Mengen an Speicherplatz auf einem Knoten benötigen, könnten Standard-VMs mit flüchtigen Speicher anstelle teurer VMs, die Premium-Speicher unterstützen, für Sie geeignet sein.

## Anhang: Testen der Datenerfassungsleistung beim Massenladen

Dieser Anhang beschreibt den Leistungstest für den Elasticsearch-Cluster. Die Tests wurden mit JMeter auf separaten virtuellen Maschinen ausgeführt. Ausführliche Informationen zur Konfiguration der Testumgebung finden Sie im Dokument „Gewusst wie: Erstellen einer Leistungstestumgebung für Elasticsearch“. Für Ihre eigenen Tests können Sie einen eigenen JMeter-Testplan erstellen oder die automatisierten Testskripts verwenden, die separat verfügbar sind. Weitere Informationen finden Sie im Dokument „Gewusst wie: Ausführen von automatisierten Elasticsearch-Tests“.

Der Datenerfassungsworkload hat einen umfangreichen Upload von Dokumenten über die Masseneinfügungs-API durchgeführt. Dieser Index diente zum Simulieren eines Repositorys, das Protokolldaten über Systemereignisse für nachfolgende Suchvorgänge und Analysen empfängt. Jedes Dokument wurde in einem eigenen Index namens *indexbase* gespeichert und wies den Typ *logs* auf. Alle Dokumente entsprachen demselben festen Schema wie in der folgenden Tabelle:

| Feld | Datentyp | Beispiel |
|---------------|---------------------|-----------------------------------|
| @timestamp | datetime | 2013-12-11T08:01:45.000Z |
| name | string | checkout.payment |
| message | string | Eingehende Anforderungsnachricht |
| severityCode | integer | 1 |
| severity | string | info |
| hostname | string | sixshot |
| hostip | Zeichenfolge (IP-Adresse) | 10\.0.0.4 |
| pid | int | 123 |
| Tid | int | 4325 |
| appID | Zeichenfolge (uuid) | {00000000-0000-0000-000000000000} |
| appName | string | mytestapp |
| appVersion | string | 0\.1.0.1234 |
| Typ | int | 5 |
| subtype | int | 1 |
| correlationId | GUID | {00000000-0000-0000-000000000000} |
| Betriebssystem | string | Linux |
| osVersion | string | 4\.1.1 |
| parameters | [ ] | {key:value,key:value} |

Sie können die folgende Anfrage zum Erstellen des Index verwenden. Die Einstellungen *number\_of\_replicas*, *refresh\_interval* und *number\_of\_shards* (Anzahl der Replikate, Aktualisierungsintervall und Anzahl der Shards) wichen in vielen Tests von den nachfolgend angezeigten Werten ab.

> [AZURE.IMPORTANT] Der Index wurde gelöscht und vor jedem Testlauf neu erstellt.

```http
PUT /systembase
{
	"settings" : {
		"number_of_replicas": 1,
		"refresh_interval": "30s",
		"number_of_shards": "5"
	},
	"logs" : {
		"properties" : {
			"@timestamp": {
			"type": "date",
			"index" : "not_analyzed"
			},
			"name": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"message": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"severityCode": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"severity": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostname": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"hostip": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"pid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"tid": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"appId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appName": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"appVersion": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"type": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"subtype": {
				"type": "integer",
				"index" : "not_analyzed"
			},
			"correlationId": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"os": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"osVersion": {
				"type": "string",
				"index" : "not_analyzed"
			},
			"parameters": {
				"type": "string",     
				"index" : "not_analyzed"
			}
		}
	}
}
```

Jeder Masseneinfügungsbatch umfasst 1.000 Dokumente. Alle Dokumente wurden auf Basis von zufälligen Werten für die Felder *severityCode*, *hostname*, *hostip*, *pid*, *tid*, *appName*, *appVersion*, *type*, *subtype* und *correlationId* sowie per Zufallsprinzip ausgewähltem Text aus festen Bedingungen für die Felder *name*, *message*, *severity*, *os*, *osVersion*, *parameters*, *data1* und *data2* erstellt. Die Anzahl der zum Hochladen von Daten verwendeten Clientinstanzen wurde mit Bedacht gewählt, um das erfolgreiche Eingabevolumen zu maximieren. Die Tests wurden über zwei Stunden durchgeführt, um dem Cluster den Start zu erleichtern und die Auswirkungen von temporären Fehlern in den Ergebnissen zu verringern. In diesem Zeitraum wurden in einigen Tests knapp 1,5 Milliarden Dokumente hochgeladen.

Die Daten wurde mithilfe eines benutzerdefinierten JUnit-Request-Samplers generiert, der einer Threadgruppe in einem JMeter-Testplan hinzugefügt wurde. Der JUnit-Code wurde unter Verwendung der JUnit-Testfallvorlage in der Eclipse-IDE erstellt.

> [AZURE.NOTE] Weitere Informationen zum Erstellen eines JUnit-Tests für JMeter finden Sie im Dokument „Gewusst wie: Erstellen und Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Elasticsearch-Leistung“.

Der folgende Codeausschnitt zeigt den Java-Code zum Testen von Elasticsearch 1.7.3. Beachten Sie, dass die JUnit-Testklasse in diesem Beispiel *ElasticSearchLoadTest2* heißt:

```java
/* Java */
package elasticsearchtest2;

	import static org.junit.Assert.*;

	import org.junit.*;

	import java.util.*;

	import java.io.*;

	import org.elasticsearch.action.bulk.*;
	import org.elasticsearch.common.transport.*;
	import org.elasticsearch.client.transport.*;
	import org.elasticsearch.common.settings.*;
	import org.elasticsearch.common.xcontent.*;

	public class ElasticSearchLoadTest2 {

		private String [] names={"checkout","order","search","payment"};
		private String [] messages={"Incoming request from code","incoming operation succeeded with code","Operation completed time","transaction performed"};
		private String [] severity={"info","warning","transaction","verbose"};
		private String [] apps={"4D24BD62-20BF-4D74-B6DC-31313ABADB82","5D24BD62-20BF-4D74-B6DC-31313ABADB82","6D24BD62-20BF-4D74-B6DC-31313ABADB82","7D24BD62-20BF-4D74-B6DC-31313ABADB82"};

		private String hostname = "";
		private String indexstr = "";
		private String typestr = "";
		private int port = 0;
		private int itemsPerInsert = 0;
		private String clustername = "";
		private static Random rand=new Random();

		@Before
		public void setUp() throws Exception {
		}

		public ElasticSearchLoadTest2(String paras) {
		* Paras is a string containing a set of comma separated values for:
			hostname
			indexstr
			typestr
			port
			clustername
			node
			itemsPerInsert
		*/

			// Note: No checking/validation is performed

			String delims = "[ ]*,[ ]*"; // comma surrounded by zero or more spaces
			String[] items = paras.split(delims);

			hostname = items[0];
			indexstr = items[1];
			typestr = items[2];
			port = Integer.parseInt(items[3]);
			clustername = items[4];
			itemsPerInsert = Integer.parseInt(items[5]);

			if (itemsPerInsert == 0)
				itemsPerInsert = 1000;
			}

		@After
		public void tearDown() throws Exception {
		}

		@Test
		public void BulkBigInsertTest() throws IOException {

			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();
				Random random = new Random();
				char[] exmarks = new char[12000];
				Arrays.fill(exmarks, 'x');
				String dataString = new String(exmarks);

				for(int i=1; i &lt; itemsPerInsert; i++){
					random.nextInt(10);
					int host=random.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[random.nextInt(names.length)])
						.field("message", messages[random.nextInt(messages.length)])
						.field("severityCode", random.nextInt(10))
						.field("severity", severity[random.nextInt(severity.length)])
						.field("hostname", "Hostname"+host)
						.field("hostip", "10.1.0."+host)
						.field("pid",random.nextInt(10))
						.field("tid",random.nextInt(10))
						.field("appId", apps[random.nextInt(apps.length)])
						.field("appName", "application" + host)
						.field("appVersion", random.nextInt(5))
						.field("type", random.nextInt(6))
						.field("subtype", random.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
						.field("data1",dataString)
						.field("data2",dataString)
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}

		@Test
		public void BulkDataInsertTest() throws IOException {
			Settings settings = ImmutableSettings.settingsBuilder().put("cluster.name", clustername).build();

			TransportClient client;
			client = new TransportClient(settings);

			try {
				client.addTransportAddress(new InetSocketTransportAddress(hostname, port));
				BulkRequestBuilder bulkRequest = client.prepareBulk();

				for(int i=1; i&lt; itemsPerInsert; i++){
					rand.nextInt(10);
					int host=rand.nextInt(20);

					bulkRequest.add(client.prepareIndex(indexstr, typestr).setSource(XContentFactory.jsonBuilder().startObject()
						.field("@timestamp", new Date())
						.field("name", names[rand.nextInt(names.length)])
						.field("message", messages[rand.nextInt(messages.length)])
						.field("severityCode", rand.nextInt(10))
						.field("severity", severity[rand.nextInt(severity.length)])
						.field("hostname", "Hostname" + host)
						.field("hostip", "10.1.0."+host)
						.field("pid",rand.nextInt(10))
						.field("tid",rand.nextInt(10))
						.field("appId", apps[rand.nextInt(apps.length)])
						.field("appName", "application"+host)
						.field("appVersion", rand.nextInt(5))
						.field("type", rand.nextInt(6))
						.field("subtype", rand.nextInt(6))
						.field("correlationId", UUID.randomUUID().toString())
						.field("os", "linux")
						.field("osVersion", "14.1.5")
						.field("parameters", "{key:value,key:value}")
					.endObject()));
				}

				BulkResponse bulkResponse = bulkRequest.execute().actionGet();
				assertFalse(bulkResponse.hasFailures());
			}
			finally {
				client.close();
			}
		}
	}
```

Die privaten *String*-Arrays *names*, *messages*, *severity* und *apps* enthalten einen kleine Gruppe von Werten aus Elementen, die nach dem Zufallsprinzip ausgewählt werden. Die verbleibenden Datenelemente für jedes Dokument werden zur Laufzeit generiert.

Der Konstruktor mit dem *String*-Parameter wird aus JMeter ausgerufen, und die in der Zeichenfolge übergebenen Werte werden als Teil der JUnit Request Sampler-Konfiguration angegeben. Bei diesem JUnit-Test wird erwartet, dass der *String* -Parameter die folgenden Informationen enthält:

* **Hostname**. Dies ist der Name oder die IP-Adresse des Azure Load Balancers. Der Load-Balancer versucht, die Anfrage auf die Datenknoten im Cluster zu verteilen. Wenn Sie keinen Load Balancer verwenden, können Sie die Adresse eines Knotens im Cluster angeben. Jedoch werden alle Anforderungen an diesen Knoten weitergeleitet, war zu einem Engpass führen könnte.

* **Indexstr**. Dies ist der Name des Indexes, dem die von JUnit-Test generierten Daten hinzugefügt werden. Wenn Sie den Index wie oben beschrieben erstellt haben, lautet dieser Wert *systembase*.

* **Typestr**. Dies ist der Typ in dem Index, in dem die Daten gespeichert werden. Wenn Sie den Index wie oben beschrieben erstellt haben, lautet dieser Wert *logs*.

* **Port**. Dies ist der Port, mit dem die Verbindung auf dem Host hergestellt werden soll. In den meisten Fällen sollte hierfür 9300 festgelegt werden (der Port, auf dem Elasticsearch auf Client-API-Anfragen wartet; Port 9200 wird nur für HTTP-Anforderungen verwendet).

* **Clustername**. Dies ist der Name des Elasticsearch-Clusters, der den Index enthält.

* **ItemsPerInsert**. Dies ist der numerische Parameter, der die Anzahl der Dokumente angibt, die in jedem Masseneinfügungsbatch hinzugefügt werden. Die Standard-Batchgröße ist 1000.

Sie legen die Daten für die Konstruktorzeichenfolge auf der JUnit Request-Seite fest, um den JUnit-Sampler in JMeter zu konfigurieren. Die folgende Abbildung zeigt ein Beispiel:.

![](media/guidance-elasticsearch-data-ingestion-image22.png)

Die *BulkInsertTest*- und *BigBulkInsertTest*-Methoden sind für das Generieren und Hochladen der Daten zuständig. Beide Methoden sind sehr ähnlich: Sie stellen eine Verbindung zum Elasticsearch-Cluster her und erstellen dann einen Batch von Dokumenten (vom *ItemsPerInsert*-Konstruktorparameter bestimmt). Die Dokumente werden dem Index mit der Elasticsearch-Massen-API hinzugefügt. Der Unterschied zwischen den beiden Methoden ist, dass die Zeichenfolgenfelder *data1* und *data2* in jedem Dokument beim Hochladen über die *BulkInsertTest*-Methode ausgelassen werden, während bei der *BigBulkInsertTest*-Methode Zeichenfolgen von 12.000 Zeichen eingefügt werden. Mit dem Feld *Test Method* auf der JUnit Request-Seite in JMeter (siehe vorherige Abbildung) können Sie auswählen, welche der folgenden Methoden ausgeführt werden soll.

> [AZURE.NOTE] Im hier dargestellten Beispielcode wird die Elasticsearch 1.7.3 Transport-Clientbibliothek verwendet. Wenn Sie Elasticsearch 2.0.0 oder höher verwenden, müssen Sie die entsprechende Bibliothek für die ausgewählte Version verwenden. Weitere Informationen zur Elasticsearch 2.0.0 Transport-Clientbibliothek finden Sie auf der [Transport Client](https://www.elastic.co/guide/en/elasticsearch/client/java-api/2.0/transport-client.html)-Seite auf der Elasticsearch-Website.

<!---HONumber=AcomDC_0211_2016-->