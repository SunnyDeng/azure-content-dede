<properties
	pageTitle="Handbuch zur Leistung und Optimierung der Kopieraktivität"
	description="Erfahren Sie mehr zu wichtigen Faktoren, die sich auf die Leistung der Datenverschiebung in Azure Data Factory über die Kopieraktivität auswirken."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="spelluru"/>


# Handbuch zur Leistung und Optimierung der Kopieraktivität
Dieser Artikel beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken. Außerdem werden die beobachtete Leistung bei internen Tests aufgeführt und verschiedene Methoden zur Optimierung der Leistung der Kopieraktivität erläutert.

## Übersicht über die Datenverschiebung in Azure Data Factory
Die Kopieraktivität führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem [global verfügbaren Datenverschiebungsdienst](data-factory-data-movement-activities.md#global) gestützt, mit dem Daten zwischen [verschiedenen Datenspeichern](data-factory-data-movement-activities.md#supported-data-stores-for-copy-activity) auf sichere, zuverlässige, skalierbare und leistungsfähige Weise kopiert werden können. Der Datenverschiebungsdienst wählt automatisch die optimale Region aus, um die Datenverschiebung basierend auf dem Speicherort der Quell- und Senkendatenspeicher durchzuführen. Derzeit wird die dem Senkendatenspeicher am nächsten gelegene Region verwendet.

Betrachten wir nun, wie diese Datenverschiebung in verschiedenen Szenarios erfolgt.

### Kopieren von Daten zwischen zwei Clouddatenspeichern
Wenn sowohl die Quell- als auch die Senkendatenspeicher (sprich die Zieldatenspeicher) sich in der Cloud befinden, durchläuft die Kopieraktivität die folgenden Phasen, um die Daten aus der Quelle in die Senke zu kopieren bzw. zu verschieben.

1.	liest Daten aus dem Quelldatenspeicher
2.	führt die Serialisierung/Deserialisierung, Komprimierung/Dekomprimierung, Spaltenzuordnung und Typumwandlung basierend auf der Konfiguration des Eingabedatasets, Ausgabedatasets und der Kopieraktivität durch
3.	schreibt Daten in den Zieldatenspeicher

![Kopieren von Daten zwischen zwei Clouddatenspeichern](./media/data-factory-copy-activity-performance/copy-data-between-two-cloud-stores.png)

**Hinweis:** Formen mit gepunkteten Linien (Komprimierung, Spaltenzuordnung usw.) sind Funktionen, die in Ihrem Anwendungsfall möglicherweise nicht genutzt werden können.


### Kopieren von Daten zwischen einem lokalen Datenspeicher und einem Clouddatenspeicher
Zum [Verschieben von Daten zwischen einem lokalen Datenspeicher und einem Clouddatenspeicher](data-factory-move-data-between-onprem-and-cloud.md) müssen Sie auf dem lokalen Computer das Datenverwaltungsgateway installieren. Dabei handelt es sich um einen Agent, der eine hybride Datenverschiebung und -verarbeitung ermöglicht. In diesem Szenario werden die Serialisierung/Deserialisierung, Komprimierung/Dekomprimierung, Spaltenzuordnung und Typumwandlung basierend auf der Konfiguration des Eingabedatasets, Ausgabedatasets und der Kopieraktivität vom Datenverwaltungsgateway durchgeführt.

![Kopieren von Daten zwischen einem lokalen und einem Clouddatenspeicher](./media/data-factory-copy-activity-performance/copy-data-between-onprem-and-cloud.png)

## Schritte zur Optimierung der Leistung
Nachfolgend finden Sie die typischen Schritte, die wir zur Optimierung der Leistung Ihrer Azure Data Factory-Lösung mit Kopieraktivität vorschlagen.

1.	**Einrichten einer Baseline** Testen Sie Ihre Pipeline mit der Kopieraktivität während der Entwicklungsphase mithilfe von repräsentativen Beispieldaten. Mithilfe des [Modell für das Aufteilen in Slices](data-factory-scheduling-and-execution.md#time-series-datasets-and-data-slices) von Azure Data Factory können Sie die Datenmenge beschränken, mit der Sie arbeiten.

	Erfassen Sie die Ausführungszeit und Leistungsmerkmale durch Überprüfen des Blatts „Datenslice“ des Ausgabedatasets und des Blatts „Aktivitätsausführung – Details“ im Azure-Vorschauportal, das die Kopieraktivität, Dauer und die Größe der kopierten Daten anzeigt.

	![Aktivitätsausführung – Details](./media/data-factory-copy-activity-performance/activity-run-details.png)

	Sie können die Leistung und Konfigurationen Ihres Szenarios basierend auf internen Beobachtungen mit der [Leistungsreferenz](#performance-reference) der Kopieraktivität vergleichen.
2. **Leistungsdiagnose und Optimierung** Wenn die beobachtete Leistung unter Ihren Erwartungen liegt, müssen Sie Leistungsengpässe identifizieren und Optimierungen durchführen, um die Auswirkung von Engpässen zu beseitigen oder zu reduzieren. Eine vollständige Beschreibung der Leistungsdiagnose geht über den Rahmen dieses Artikels hinaus, nachfolgend finden Sie jedoch einige allgemeine Überlegungen.
	- [Quelle](#considerations-on-source)
	- [Senke](#considerations-on-sink)
	- [Serialisierung/Deserialisierung](#considerations-on-serializationdeserialization)
	- [Komprimierung](#considerations-on-compression)
	- [Spaltenzuordnung](#considerations-on-column-mapping)
	- [Gateway zur Datenverwaltung](#considerations-on-data-management-gateway)
	- [Weitere Überlegungen](#other-considerations)
3. **Erweitern der Konfiguration auf Ihre gesamten Daten** Sobald Sie mit den Ergebnissen und der Leistung der Ausführung zufrieden sind, können Sie die Datasetdefinition und den aktiven Zeitraum der Pipeline erweitern, um die gesamten Daten in der Abbildung abzudecken.

## Leistungsreferenz
> [AZURE.IMPORTANT] **Haftungsausschluss:** Die folgenden Daten wurden nur für Anleitungen und allgemeine Planung veröffentlicht. Es wird vorausgesetzt, dass Bandbreite, Hardware, Konfiguration usw. zu den besten in der jeweiligen Kategorie zählen. Verwenden Sie diese Daten nur als Referenz. Der beobachtete Durchsatz bei der Datenverschiebung wird von einer Reihe von Variablen beeinflusst. In den Abschnitten weiter unten erfahren Sie, wie Sie optimieren können und eine bessere Leistung für Ihre Datenverschiebungsanforderungen erzielen. Diese Daten werden aktualisiert, wenn leistungssteigernde Verbesserungen und Funktionen hinzugefügt werden.

![Leistungsmatrix](./media/data-factory-copy-activity-performance/CopyPerfRef.png)

> [AZURE.NOTE] **In Kürze verfügbar:** Wir sind dabei, die grundlegenden Leistungsmerkmale zu verbessern, und in Kürze werden Sie in der obigen Tabelle mehr und bessere Durchsatzzahlen finden.

Beachten Sie Folgendes:

- Der Durchsatz wird anhand der folgenden Formel berechnet: [Größe der aus der Quelle gelesenen Daten]/[Ausführungsdauer der Kopieraktivität]
- Das Dataset [TPC-H](http://www.tpc.org/tpch/) wurde genutzt, um die oben genannten Zahlen zu berechnen.
- Microsoft Azure-Datenspeichern befinden sich Quelle und Senke in derselben Azure-Region.
- Bei der Hybriddatenverschiebung (lokale Daten in die Cloud oder umgekehrt) wurde das Datenverwaltungsgateway (Einzelinstanz) mit der folgenden Konfiguration auf einem anderen Computer gehostet, als der lokale Datenspeicher. Hinweis: Da auf dem Gateway nur eine Aktivitätsausführung erfolgte, verbrauchte der Kopiervorgang nur einen kleinen Teil der CPU-/Speicherressource und Netzwerkbandbreite dieses Computers.
	<table>
	<tr>
		<td>CPU</td>
		<td>32 Prozessorkerne, 2,20&#160;GHz, Intel Xeon® E5-2660 v2</td>
	</tr>
	<tr>
		<td>Arbeitsspeicher</td>
		<td>128&#160;GB</td>
	</tr>
	<tr>
		<td>Netzwerk</td>
		<td>Internetschnittstelle: 10&#160;Gbit/s; Intranetschnittstelle: 40&#160;Gbit/s</td>
	</tr>
	</table>

## Hinweise zur Datenquelle
### Allgemein
Stellen Sie sicher, dass der zugrundeliegende Datenspeicher nicht von anderen darauf oder dafür ausgeführten Workloads überlastet ist, u. a. die Kopieraktivität.

Informationen zu Microsoft-Datenspeichern finden Sie in datenspeicherspezifischen [Themen zur Überwachung und Optimierung](#appendix-data-store-performance-tuning-reference). Diese helfen Ihnen dabei, Leistungsmerkmale, die Minimierung von Antwortzeiten und die Maximierung des Durchsatzes zu verstehen.

### Dateibasierte Datenspeicher
*(Einschließlich Azure-Blob, Azure Data Lake, lokales Dateisystem)*

- **Durchschnittliche Größe und Anzahl der Dateien**: Kopieraktivität überträgt Daten auf Dateibasis. Mit der gleichen zu verschiebenden Datenmenge ist der Gesamtdurchsatz langsamer, wenn die Daten aufgrund der Bootstrap-Phase für jede Datei aus einer großen Anzahl von kleinen Dateien anstelle einer kleinen Anzahl von größeren Dateien besteht. Kombinieren Sie kleine Dateien daher möglichst zu größeren Dateien, um einen höheren Durchsatz zu erzielen.
- **Dateiformat und Komprimierung**: In den Abschnitten [Hinweise zur Serialisierung/Deserialisierung](#considerations-on-serializationdeserialization) und [Hinweise zur Komprimierung](#considerations-on-compression) finden Sie weitere Methoden zur Verbesserung der Leistung.
- Außerdem finden Sie Informationen zum Szenario mit **lokalem Dateisystem**, bei dem die Verwendung des **Datenverwaltungsgateways** erforderlich ist, im Abschnitt [Hinweise zum Gateway](#considerations-on-data-management-gateway).

### Relationale Datenspeicher
*(Einschließlich Azure SQL-Datenbank, Azure SQL Data Warehouse, SQL Server-Datenbank, Oracle-Datenbank, MySQL-Datenbank, DB2-Datenbank, Teradata-Datenbank, Sybase-Datenbank, PostgreSQL-Datenbank)*

- **Datenmuster**: Tabellenschema wirkt sich auf den Kopierdurchsatz aus. Beim Kopieren derselben Datenmenge erzielen Sie mit großen Zeilen eine bessere Leistung als mit kleinen Zeilen, da die Datenbank weniger Batches von Daten mit weniger Zeilen effizienter abrufen kann.
- **Abfrage oder gespeicherte Prozedur**: Optimieren Sie die Logik der Abfrage oder gespeicherten Prozedur, die Sie in der Quelle der Kopieraktivität angeben, um die Daten effizienter abzurufen.
- Außerdem finden Sie Informationen zum Szenario mit **lokalen relationalen Datenbanken** wie SQL Server und Oracle, bei dem die Verwendung des **Datenverwaltungsgateways** erforderlich ist, im Abschnitt [Hinweise zum Gateway](#considerations-on-data-management-gateway).

## Hinweise zur Senke

### Allgemein
Stellen Sie sicher, dass der zugrundeliegende Datenspeicher nicht von anderen darauf oder dafür ausgeführten Workloads überlastet ist, u. a. die Kopieraktivität.

Informationen zu Microsoft-Datenspeichern finden Sie in datenspeicherspezifischen [Themen zur Überwachung und Optimierung](#appendix-data-store-performance-tuning-reference). Diese helfen Ihnen dabei, Leistungsmerkmale, die Minimierung von Antwortzeiten und die Maximierung des Durchsatzes zu verstehen.

### Dateibasierte Datenspeicher
*(Einschließlich der Azure-Blob, Azure Data Lake, lokales Dateisystem)*

- **Kopierverhalten**: Wenn Sie Daten aus einem anderen dateibasierten Datenspeicher kopieren, bietet die Kopieraktivität über die Eigenschaft „copyBehavior“ drei Verhaltenstypen: Beibehalten der Hierarchie, Abflachen der Hierarchie und Zusammenführen von Dateien. Das Beibehalten oder Abflachen der Hierarchie verursacht wenig oder keinen Leistungsaufwand, wohingegen das Zusammenführen von Dateien zusätzlichen Leistungsaufwand verursacht.
- **Dateiformat und Komprimierung**: In den Abschnitten [Hinweise zur Serialisierung/Deserialisierung](#considerations-on-serializationdeserialization) und [Hinweise zur Komprimierung](#considerations-on-compression) finden Sie weitere Methoden zur Verbesserung der Leistung.
- Für **Azure-Blob** unterstützen wir derzeit nur Blockblob für die optimierte Datenübertragung und den Durchsatz.
- Außerdem finden Sie Informationen zum Szenario mit **lokalem Dateisystem**, bei dem die Verwendung des **Datenverwaltungsgateways** erforderlich ist, im Abschnitt [Hinweise zum Gateway](#considerations-on-data-management-gateway).

### Relationale Datenspeicher
*(Einschließlich Azure SQL-Datenbank, Azure SQL Data Warehouse, SQL Server-Datenbank)*

- **Kopierverhalten**: Je nach den für „sqlSink“ konfigurierten Eigenschaften, schreibt die Kopieraktivität auf verschiedene Arten Daten in die Zieldatenbank:
	- Standardmäßig verwendet der Datenverschiebungsdienst die API für Massenkopierfunktionen, um Daten im Anfügemodus einzufügen. Damit wird die beste Leistung erzielt.
	- Wenn Sie eine gespeicherte Prozedur in der Senke konfigurieren, wendet die Datenbank die Daten zeilenweise anstatt durch Massenladen an, sodass die Leistung erheblich sinkt. Wenn die Größe der Daten umfangreich ist, sollten Sie ggf. die Verwendung der Eigenschaft „sqlWriterCleanupScript“ (siehe unten) in Betracht ziehen.
	- Wenn Sie für jede Kopieraktivitätsausführung die Eigenschaft „sqlWriterCleanupScript“ konfigurieren, löst der Dienst zuerst das Skript aus und fügt die Daten dann mithilfe der API für Massenkopierfunktionen ein. Beispiel: Zum Überschreiben der gesamten Tabelle mit den aktuellen Daten können Sie zunächst ein Skript zum Löschen aller Datensätze angeben und dann die neuen Daten durch Massenladen aus der Quelle einfügen.
- **Datenmuster und Batchgröße**:
	- Das Tabellenschema wirkt sich auf den Kopierdurchsatz aus. Beim Kopieren derselben Datenmenge erzielen Sie mit großen Zeilen eine bessere Leistung als mit kleinen Zeilen, da die Datenbank für weniger Datenbatches effizienter einen Commit ausführen kann.
	- Die Kopieraktivität fügt Daten in einer Folge von Batches ein, wobei die Anzahl der Zeilen in einem Batch mithilfe der Eigenschaft „writeBatchSize“ festgelegt werden kann. Wenn Ihre Daten Zeilen mit geringer Größe aufweisen, können Sie für die Eigenschaft „writeBatchSize“ einen höheren Wert festlegen, damit weniger Batchaufwand verursacht und der Durchsatz erhöht wird. Wenn die Zeilen Ihrer Daten sehr umfangreich sind, seien Sie beim Erhöhen des Werts von „writeBatchSize“ vorsichtig – ein großer Wert kann zu einem Kopiefehler aufgrund von Datenbanküberlastung führen.
- Außerdem finden Sie Informationen zum Szenario mit **lokalen relationalen Datenbanken** wie SQL Server und Oracle, bei dem die Verwendung des **Datenverwaltungsgateways** erforderlich ist, im Abschnitt [Hinweise zum Gateway](#considerations-on-data-management-gateway).


### NoSQL-Speicher
*(Einschließlich Azure-Tabelle, Azure DocumentDB)*

- Für **Azure-Tabelle**:
	- **Partition**: Das Schreiben von Daten in überlappende Partitionen beeinträchtigt die Leistung erheblich . Sie können Ihre Daten nach Partitionsschlüssel sortieren, damit Daten effizient in aufeinanderfolgende Partitionen eingefügt werden, oder Sie können die Logik so anpassen, dass die Daten in eine einzige Partition geschrieben werden.
- Für **Azure DocumentDB**:
	- **Batchgröße**: Die Eigenschaft „writeBatchSize“ gibt die Anzahl der parallelen Anforderungen an den DocumentDB-Dienst zum Erstellen von Dokumenten an. Sie können eine bessere Leistung erwarten, wenn Sie „writeBatchSize“ heraufsetzen, da mehr parallele Anforderungen an DocumentDB gesendet werden. Beachten Sie jedoch die Einschränkung beim Schreiben in DocumentDB (Fehlermeldung „Anforderungsrate ist groß“). Eine Einschränkung kann aufgrund einer Reihe von Faktoren erfolgen, einschließlich der Größe bzw. Anzahl der Dokumente und der Indizierungsrichtlinie der Zielsammlung. Erwägen Sie eine bessere Sammlung (z. B. S3), um einen höheren Durchsatz zu erzielen.

## Hinweise zur Serialisierung/Deserialisierung
Serialisierung und Deserialisierung können auftreten, wenn Ihr Eingabe- oder Ausgabedataset eine Datei ist. Derzeit unterstützt die Kopieraktivität Avro- und Textdatenformate (z. B. CSV und TSV).

**Kopierverhalten:**

- Beim Kopieren von Dateien zwischen dateibasierten Datenspeichern:
	- Wenn sowohl das Eingabe- als auch das Ausgabedataset dieselben oder keine Dateiformateinstellungen aufweisen, führt der Datenverschiebungsdienst eine binäre Kopie ohne Serialisierung/Deserialisierung durch. Daher erzielen Sie wahrscheinlich einen besseren Durchsatz verglichen mit dem Szenario, bei dem sich die Dateiformateinstellungen für Quelle und Senke unterscheiden.
	- Wenn sowohl das Eingabe- als auch das Ausgabedataset im Textformat vorliegen und sich nur im Codierungstyp unterscheiden, führt der Datenverschiebungsdienst nur eine Konvertierung der Codierung ohne Serialisierung/Deserialisierung durch. Dies führt im Vergleich zur binären Kopie zu einigem Leistungsaufwand.
	- Wenn das Eingabe- und Ausgabedataset unterschiedliche Dateiformate oder unterschiedliche Konfigurationen wie Trennzeichen aufweisen, deserialisiert und transformiert der Datenverschiebungsdienst die zu streamenden Quelldaten und serialisiert sie anschließend in das gewünschte Ausgabeformat. Dies führt im Vergleich zu den vorherigen Szenarien zu wesentlich höherem Leistungsaufwand.
- Beim Kopieren von Dateien in einen bzw. aus einem nicht dateibasierten Datenspeicher (z. B. aus einem dateibasierten Speicher in einen relationalen Speicher) ist eine Serialisierung oder Deserialisierung erforderlich und führt zu einem hohen Leistungsaufwand.

**Dateiformat:** Die Wahl des Dateiformats kann die Leistung beeinträchtigen. Avro ist z. B. ein kompaktes binäres Format, das Metadaten mit Daten speichert und über umfassende Unterstützung im Hadoop-System für die Verarbeitung und Abfragen verfügt. Avro ist jedoch teurer für die Serialisierung/Deserialisierung, was im Vergleich mit dem Textformat zu einem niedrigeren Durchsatz führt. Die Auswahl des mit dem Verarbeitungsablauf zu verwendenden Dateiformats sollte ganzheitlich getroffen werden. Beginnen Sie dabei mit dem Format, in dem die Daten in Quellendatenspeichern gespeichert oder aus externen Systemen extrahiert werden, dem besten Format für die Speicherung, analytische Verarbeitung und Abfragen, und in welchem Format die Daten in Data Marts für die Berichterstellungs- und Visualisierungstools exportiert werden sollen. Manchmal stellt sich heraus, dass ein für die Lese- und Schreibleistung suboptimales Dateiformat sich unter Berücksichtigung des gesamten analytischen Prozesses gut eignet.

## Hinweise zur Komprimierung
Wenn Ihr Eingabe- oder Ausgabedataset eine Datei ist, können Sie die Kopieraktivität so konfigurieren, dass beim Schreiben von Daten in das Ziel eine Komprimierung oder Dekomprimierung ausgeführt wird. Durch Aktivieren der Komprimierung, stellen Sie einen Kompromiss zwischen E/A und CPU her: Das Komprimieren der Daten kostet zusätzliche Computeressourcen, sorgt jedoch im Gegenzug für eine Reduzierung des Netzwerk-E/A und des Speichers; abhängig von Ihren Daten kann dies zu einer Verbesserung des Gesamtdurchsatzes von Kopien führen.

**Codec:** GZIP-, BZIP2- und Deflate-Komprimierung werden unterstützt. Alle drei Typen können von Azure HDInsight für die Verarbeitung verwendet werden. Jeder Komprimierungscodec ist eindeutig. BZIP2 erzielt z. B. hat den niedrigsten Durchsatz, Sie erhalten jedoch die beste Hive-Abfrageleistung, da für die Verarbeitung eine Aufteilung möglich ist; GZIP bietet die ausgewogenste Option und wird am häufigsten verwendet. Sie sollten den Codec auswählen, der für das End-to-End-Szenario am besten geeignet ist.

**Ebene:** Für jeden Komprimierungscodec Ihnen stehen zwei Optionen zur Verfügung – die schnellste Komprimierung und die optimale Komprimierung. Bei der schnellsten Komprimierung werden die Daten schnellstmöglich komprimiert, auch wenn die resultierende Datei nicht optimal komprimiert ist. Die optimale Komprimierung dauert länger, und die minimale Datenmenge wird zurückgegeben. Sie können beide Optionen testen, um die in Ihrem Fall beste Gesamtleistung zu ermitteln.

**Hinweis:** Zum Kopieren von umfangreichen Daten zwischen einem lokalen Datenspeicher und der Cloud, wobei die Bandbreite des Unternehmensnetzwerks und Azure oft den einschränkenden Faktor darstellt, und weder das Eingabe- noch das Ausgabedataset komprimiert werden soll, können Sie die Verwendung eines **Azure-Interims-BLOBS** mit Komprimierung in Betracht ziehen. Genauer gesagt, können Sie eine Kopieraktivität in zwei aufteilen: Die erste Kopieraktivität kopiert in komprimierter Form aus der Quelle in das Interims- oder Staging-BLOB, und die zweite Kopieraktivität kopiert komprimierte Daten aus der Stagingumgebung und dekomprimiert sie beim Schreiben in die Senke.

## Hinweise für die Zuordnung von Spalten
Mit der Eigenschaft „columnMappings“ in der Kopieraktivität können alle oder ein Teil der Eingabespalten den Ausgabespalten zugeordnet werden. Nach dem Lesen der Daten aus der Quelle muss der Datenverschiebungsdienst eine Spaltenzuordnung für die Daten ausführen, bevor sie in die Senke geschrieben werden. Dieser zusätzliche Verarbeitungsaufwand reduziert den Kopierdurchsatz.

Wenn Ihr Quelldatenspeicher abfragbar ist, z. B. ein relationaler Datenspeicher wie Azure SQL/SQL Server oder ein NoSQL-Datenspeicher wie Azure-Tabelle/Azure DocumentDB, können Sie in Betracht ziehen, einen Pushdownvorgang für die Logik zur Filterung/erneuten Anordnung von Spalten an die Abfrageeigenschaft durchzuführen, anstatt die Spaltenzuordnung zu verwenden. Dabei erfolgt die Projektion beim Lesen von Daten aus dem Datenquellenspeicher und ist wesentlich effizienter.

## Überlegungen zum Datenverwaltungsgateway
Empfehlungen für die Gatewayeinrichtung finden Sie unter [Überlegungen zur Verwendung des Datenverwaltungsgateways](data-factory-move-data-between-onprem-and-cloud.md#Considerations-for-using-Data-Management-Gateway).

**Gatewaycomputerumgebung:** Es wird empfohlen, einen dedizierten Computer zum Hosten des Datenverwaltungsgateways zu verwenden. Verwenden Sie Tools wie Systemmonitor, um die Auslastung von CPU, Arbeitsspeicher und Bandbreite während eines Kopiervorgangs auf Ihrem Gatewaycomputer zu überprüfen. Wechseln Sie zu einem leistungsfähigeren Computer, wenn CPU, Arbeitsspeicher oder Netzwerkbandbreite zu einem Engpass werden.

**Gleichzeitige Ausführungen der Kopieraktivität:** In einer einzelnen Instanz des Datenverwaltungsgateways können mehrere Kopieraktivitäten gleichzeitig ausgeführt werden, d. h. ein Gateway kann eine bestimmte Anzahl gleichzeitig (die Anzahl von gleichzeitigen Aufträgen wird basierend auf der Hardwarekonfiguration des Gatewaycomputers berechnet) von Kopieraufträgen ausführen. Zusätzliche Aufträge werden in der Warteschlange platziert, bis sie vom Gateway abgerufen werden oder die Zeitüberschreitung für den Auftrag erreicht ist – je nachdem, was zuerst eintritt. Zum Vermeiden von Ressourcenkonflikten auf dem Gateway können Sie den Zeitplan Ihrer Aktivitäten bereitstellen, um die Menge von gleichzeitigen Kopieraufträgen in der Warteschlange zu reduzieren, oder teilen Sie die Last auf mehrere Gateways auf.


## Weitere Überlegungen
Wenn die zu kopierenden Daten sehr groß sind, können Sie Ihre Geschäftslogik so anpassen, dass die Daten mithilfe des Aufteilungsmechanismus von Azure Data Factory weiter partitioniert werden und eine häufigere Ausführung der Kopieraktivität planen, um die Datengröße für die einzelnen Kopieraktivitäten zu reduzieren.

Seien Sie vorsichtig bei der Anzahl von Datasets und Kopieraktivitäten, die gleichzeitig für denselben Datenspeicher erfolgen. Eine große Anzahl gleichzeitiger Kopieraufträge kann einen Datenspeicher drosseln und zu einer Beeinträchtigung der Leistung, internen Wiederholungsversuchen von Kopieraufträgen und in einigen Fällen zu Fehlern bei der Ausführung führen.

## Fallstudie – Kopieren von einem lokalen SQL Server in Azure Blob
**Szenario:** Eine Pipeline wird erstellt, um Daten von einem lokalen SQL Server im CSV-Format in Azure Blob zu kopieren. Zum Beschleunigen der Kopie ist angegeben, dass die CSV-Dateien im BZIP2-Format komprimiert werden sollen.

**Test und Analyse:** Für die Kopieraktivität wird ein Durchsatz von weniger als 2 MB beobachtet, d. h. viel langsamer als die Leistungsbenchmark.

**Leistungsanalyse und -optimierung:** Zum Beheben des Leistungsproblems sehen wir uns zunächst im Detail an, wie die Daten verarbeitet und verschoben werden:

1.	**Lesen von Daten:** Gateway öffnet Verbindung mit SQL Server und sendet die Abfrage. SQL Server reagiert mit dem Senden des Datenstroms über das Intranet an das Gateway.
2.	Das Gateway **serialisiert** den Datenstrom im CSV-Format und **komprimiert** die Daten in einem BZIP2-Strom.
3.	**Schreiben von Daten:** Das Gateway lädt BZIP2-Streams über das Internet zu Azure Blob.

Wie Sie sehen, werden die Daten sequenziell durch Streaming verarbeitet und verschoben: SQL Server -> LAN-> Gateway > WAN -> Azure-Blob. **Die Gesamtleistung wird durch den minimalen Durchsatz der gesamten Pipeline abgegrenzt**.

![Datenfluss](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Bei einem oder mehreren der folgenden Faktoren kann es sich um den Leistungsengpass handeln:

1.	**Quelle:** SQL Server selbst hat aufgrund hoher Auslastung einen geringen Durchsatz.
2.	**Gateway zur Datenverwaltung**
	1.	**LAN:** Das Gateway befindet sich bei einer Verbindung mit geringer Bandbreite weit vom SQL Server entfernt.
	2.	Die **Belastung des Gatewaycomputers** hat ihre Grenzen für das Ausführen der folgenden Aufgaben erreicht:
		1.	**Serialisierung:** Der Serialisierungsdatenstrom in eine CSV-Datei weist einen langsamen Durchsatz auf.
		2.	**Komprimierung:** Ein langsamer Komprimierungscodec wurde gewählt (z. B. BZIP2 mit 2 8 MBit/s und Core i7).
	3.	**WAN:** Niedrige Bandbreite zwischen dem Unternehmensnetzwerk und Azure (z. B. T1 = 1544 Kbit/s, T2 = 6312 Kbit/s)
4.	**Senke:** Azure-Blob hat geringen Durchsatz (jedoch unwahrscheinlich, da die SLA mindestens 60 MBit/s garantiert).

In diesem Fall könnte die BZIP2-Datenkomprimierung die gesamte Pipeline verlangsamen. Ein Wechsel zum GZIP-Komprimierungscodec kann diesen Engpass beheben.

## Anhang – Referenz zum Optimieren der Datenspeicherleistung
Hier finden Sie einige Referenzen zur Leistungsüberwachung und -optimierung für einige der unterstützten Datenspeicher:

- Azure-Speicher (einschließlich Azure Blob und Azure-Tabelle): [ Skalierbarkeitsziele für Azure-Speicher](../storage/storage-scalability-targets.md) und [Checkliste zu Leistung und Skalierbarkeit für Azure-Speicher](../storage//storage-performance-checklist.md)
- Azure SQL-Datenbank: Sie können [die Leistung überwachen](../sql-database/sql-database-service-tiers.md#monitoring-performance) und den Prozentsatz der Datenbanktransaktionseinheit (DTU = Database Throughput Unit) überprüfen .
- Azure SQL Datawarehouse: Die Funktionalität wird in Data Warehouse-Einheiten (Data Warehouse Units, DWUs) gemessen. Informationen finden Sie unter [Flexible Leistung und Skalierbarkeit mit SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-performance-scale.md).
- Azure DocumentDB: [Leistungsstufe in DocumentDB](../documentdb/documentdb-performance-levels.md).
- Lokale SQL Server: [Überwachen und Optimieren der Leistung](https://msdn.microsoft.com/library/ms189081.aspx)
- Lokaler Dateiserver: [Leistungsoptimierung für Dateiserver](https://msdn.microsoft.com/library/dn567661.aspx)

<!---HONumber=AcomDC_0302_2016-->