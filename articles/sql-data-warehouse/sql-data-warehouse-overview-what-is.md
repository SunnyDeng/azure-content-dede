<properties
   pageTitle="Was ist Azure SQL Data Warehouse? | Microsoft Azure"
   description="Verteilte Datenbank der Enterprise-Klasse, die mehrere Petabyte an relationalen und nicht relationalen Daten verarbeiten kann Sie ist das branchenweit erste Cloud-Data Warehouse mit Funktionen zum Vergrößern, Verkleinern und Anhalten innerhalb weniger Sekunden."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/18/2015"
   ms.author="lodipalm;barbkess;twounder;JRJ@BigBangData.co.uk;"/>


# Was ist Azure SQL Data Warehouse?

Azure SQL Data Warehouse ist eine verteilte Datenbank der Enterprise-Klasse, die sehr große Mengen an relationalen und nicht relationalen Daten verarbeiten kann. Es handelt sich hierbei um das erste Cloud-Data Warehouse der Branche, bei dem bewährte SQL-Funktionen mit der Fähigkeit verbunden werden, innerhalb von Sekunden zu vergrößern, zu verkleinern und zu pausieren. SQL Data Warehouse ist außerdem tief in Azure integriert und kann problemlos innerhalb weniger Sekunden bereitgestellt werden. Der Dienst ist zudem vollständig verwaltet, und es ist nicht mehr erforderlich, Zeit für Softwarepatches, Wartung und Backups aufzuwenden. Die automatischen integrierten Backups von SQL Data Warehouse unterstützen die Fehlertoleranz und die Self-Service-Wiederherstellung. Bei der Erstellung von SQL Data Warehouse haben wir den Schwerpunkt auf einige wichtige Attribute gelegt. Es sollte sichergestellt werden, dass Azure vollständig genutzt und ein Data Warehouse erstellt wird, das für jede Unternehmens-Workload geeignet ist.

## Optimiert

### Data Warehouse-Architektur

Im Kern wird SQL Data Warehouse mit der MPP-Architektur (Massive Parallel Processing) von Microsoft ausgeführt, die ursprünglich entworfen wurde, um einige der größten lokalen Data Warehouses von Unternehmen auszuführen. Bei dieser Architektur werden integrierte Data Warehousing-Leitungsverbesserungen genutzt. Außerdem ist es für SQL Data Warehouse möglich, ohne Probleme das horizontale Hochskalieren und das Parallelisieren der Berechnung komplexer SQL-Abfragen durchzuführen. Die Architektur von SQL Data Warehouse ist zudem so ausgelegt, dass die Einbindung in Azure genutzt wird. Indem diese beiden Aspekte kombiniert werden, wird die Architektur in vier Hauptkomponenten unterteilt:

![SQL Data Warehouse-Architektur][1]

- **Steuerknoten**: Sie stellen eine Verbindung mit dem Steuerknoten her, wenn Sie SQL Data Warehouse mit Entwicklungs-, Lade- oder Business Intelligence-Tools verwenden. In SQL Data Warehouse ist der Compute-Knoten eine SQL-Datenbank, und beim Herstellen der Verbindung fühlt sich dies wie eine standardmäßige SQL-Datenbank an und sieht auch so aus. Im Hintergrund werden aber alle Datenbewegungen und -berechnungen koordiniert, die im System durchgeführt werden. Wenn ein Befehl an den Steuerknoten ausgegeben wird, wird er in mehrere Abfragen unterteilt, die an die Compute-Knoten des Diensts übergeben werden.

- **Serverknoten**: Wie der Steuerknoten auch, werden die Serverknoten von SQL Data Warehouse mit SQL-Datenbanken betrieben. Ihre Aufgabe besteht darin, als Compute-Komponente des Diensts zu fungieren. Im Hintergrund werden Daten, die in SQL Data Warehouse geladen werden, auf die Knoten des Diensts verteilt. Immer wenn der Steuerknoten dann einen Befehl erhält, wird er für die einzelnen Compute-Knoten in mehrere Teile unterteilt. Die Compute-Knoten verarbeiten dann jeweils ihre entsprechenden Daten. Nach Abschluss der Berechnung übergeben die Compute-Knoten die Teilergebnisse an den Steuerknoten, auf dem die Ergebnisse dann zusammengefasst werden, bevor eine Antwort zurückgegeben wird.

- **Speicher**: Beim gesamten Speicher für SQL Data Warehouse handelt es sich um standardmäßige Azure Storage-Blobs. Dies bedeutet Folgendes: Beim Interagieren mit Daten führen Compute-Knoten das Schreiben und Lesen direkt auf bzw. von Blobs durch. Da der Azure-Speicher transparent und nahezu unbegrenzt erweitert werden kann, können wir den Speicher automatisch skalieren, und zwar separat vom Compute-Bereich. Mit Azure-Speicher können wir Speicher beim Skalieren oder Pausieren außerdem beständig machen, unseren Sicherungs- und Wiederherstellungsprozess optimieren und einen Speicher mit mehr Sicherheit und höherer Fehlertoleranz schaffen.

- **Datenverschiebungsdienste**: Das Glied, das in SQL Data Warehouse alles zusammenhält, sind die Datenverschiebungsdienste. Mit den Datenverschiebungsdiensten kann der Steuerknoten kommunizieren und Daten an alle Compute-Knoten übergeben. Außerdem können die Compute-Knoten damit Daten untereinander austauschen, um Zugriff auf Daten auf anderen Compute-Knoten zu erhalten. Sie können die Daten abrufen, die sie zum Durchführen von Verknüpfungen und Aggregationen benötigen.

### Moduloptimierungen

Dank des MPP-Verfahrens kann SQL Data Warehouse wie oben beschrieben einen Ansatz der Art „Teile und herrsche“ wählen, um Probleme mit großen Datenmengen zu lösen. Da die Daten in SQL Data Warehouse unterteilt und auf die Compute-Knoten des Diensts verteilt sind, kann jeder Compute-Knoten parallel seinen Teil der Daten verarbeiten. Zuletzt werden die Ergebnisse an den Steuerknoten übergeben und zusammengefasst, bevor sie zurück an die Benutzer übergeben werden. Dieser Ansatz wird auch durch eine Reihe von speziellen Data Warehousing-Leistungsoptimierungen unterstützt:

- SQL Data Warehouse nutzt einen erweiterten Abfrageoptimierer und eine Gruppe komplexer Statistiken über alle Daten des Diensts hinweg, um seine Abfragepläne zu erstellen. Mit den Informationen zur Datengröße und -verteilung kann der Dienst verteilte Abfragen optimieren, indem die Kosten bestimmter Abfragevorgänge bewertet werden.

- Zusätzlich zur Erstellung optimaler Abfragepläne setzt SQL Data Warehouse erweiterte Algorithmen und Techniken ein, mit denen Daten je nach Bedarf auf effiziente Weise zwischen den Berechnungsressourcen verschoben werden, um die Abfrage durchzuführen. Diese Vorgänge sind in die Datenverschiebungsdienste von Data Warehouse integriert, und die Optimierungen werden automatisch durchgeführt.

- Die Einbindung von im Cluster gruppierten Columnstore-Indizes in die Appliance ist ebenfalls wichtig, um eine schnelle Abfrageleistung zu erzielen. Mithilfe des spaltenbasierten Speichers erreicht SQL Data Warehouse eine bis zu fünfmal höhere Komprimierung als mit dem herkömmlichen zeilenorientierten Speicher und eine bis zu zehnmal höhere Abfrageleistung. Data Warehouse-Abfragen sind sehr gut für Columnstore-Indizes geeignet, da hierbei häufig die gesamte Tabelle oder Partition einer Tabelle gescannt wird und die Auswirkungen der Datenverschiebung für Abfrageschritte reduziert werden.

## Skalierbar

Mit der Architektur von SQL Data Warehouse werden getrennte Speicher- und Compute-Ressourcen eingeführt, sodass diese unabhängig voneinander skaliert werden können. Die schnelle und einfache Bereitstellungsstruktur der SQL-Datenbank ermöglicht es, dass sehr kurzfristig zusätzliche Compute-Ressourcen bereitgestellt werden. Dies wird durch die Verwendung von Azure-Speicher-Blobs vervollständigt. Mit Blobs erhalten wir nicht nur einen stabilen, replizierten Speicher, sondern auch die Infrastruktur für eine mühelose Erweiterung zu niedrigen Kosten. Die Kombination aus der Speicherung auf Cloudebene und Azure Compute-Ressourcen ermöglicht für SQL Data Warehouse die bedarfsabhängige Bezahlung des Abfrageleistungsspeichers. Das Ändern der Compute-Menge ist so einfach wie das Verschieben eines Schiebereglers nach links oder rechts im Azure-Portal. Dieser Vorgang kann aber auch per T-SQL und PowerShell geplant oder einer Workload hinzugefügt werden.

Neben der Möglichkeit zur vollständigen Kontrolle über den Umfang der Computeleistung unabhängig vom Speicher ermöglicht Ihnen SQL Data Warehouse auch das vollständige Anhalten des Data Warehouse. Während Ihr Speicher unverändert bleibt, werden alle Compute-Ressourcen für den Hauptpool von Azure freigegeben, sodass Sie sofort Kosten sparen. Setzen Sie die Computeressource bei Bedarf einfach fort, und halten Sie Ihre Daten und Computeleistung für Ihre Arbeitslast zur Verfügung.

Die Compute-Nutzung in SQL Data Warehouse wird anhand von SQL Data Warehouse-Einheiten (DWUs) gemessen. DWUs sind ein Maß für die zugrunde liegende Leistungsfähigkeit Ihres Data Warehouse und sollen sicherstellen, dass Ihrem Warehouse jederzeit eine standardmäßige Leistungsmenge zugeordnet ist. DWUs werden vor allem verwendet, um Folgendes sicherzustellen:

- Sie können Ihr Data Warehouse effektiv skalieren, ohne sich um die zugrunde liegende Hardware oder Software kümmern zu müssen.

- Sie kennen die Leistung, die Sie auf DWU-Ebene erhalten, bevor Sie die Größe Ihres Data Warehouse ändern.

- Die zugrunde liegende Hardware und Software Ihrer Instanz kann sich ändern oder verschoben werden, ohne dass sich dies auf Ihre Workload-Leistung auswirkt.

- Wir können Anpassungen an der zugrunde liegenden Architektur des Diensts vornehmen, ohne dass sich dies auf die Leistung Ihrer Workload auswirkt.

- Während wir die Leistung in SQL Data Warehouse schnell verbessern, können wir sicherstellen, dass die Skalierbarkeit und eine gleichmäßige Auswirkung auf das System sichergestellt ist.

### Data Warehouse-Einheiten

Wir verstehen Data Warehouse-Einheiten als Maß für drei präzise Metriken, für die eine starke Korrelation mit der Data Warehousing-Workload-Leistung besteht. Unser Ziel in Bezug auf die allgemeine Verfügbarkeit ist, dass diese wichtigen Workload-Metriken linear mit den DWUs skaliert werden können, die Sie für Ihr Data Warehouse gewählt haben.

**Scan/Aggregation**: Bei dieser Workload-Metrik wird eine Data Warehousing-Standardabfrage verwendet, mit der eine große Zahl von Zeilen gescannt und anschließend eine komplexe Aggregation durchgeführt wird. Dies ist ein Vorgang mit hohem E/A- und CPU-Aufwand.

**Laden**: Bei dieser Metrik wird gemessen, wie gut Daten in den Dienst aufgenommen werden können. Ladevorgänge werden mit PolyBase durchgeführt, indem ein repräsentatives Dataset aus dem Azure-Speicher-Blob geladen wird. Diese Metrik ist dafür konzipiert, die Netzwerk- und CPU-Aspekte des Diensts zu verdeutlichen.

**CREATE TABLE AS SELECT (CTAS)**: Mit CTAS wird die Fähigkeit zum Erstellen der Kopie einer Tabelle gemessen. Dies umfasst das Lesen von Daten aus dem Speicher, das Verteilen auf die Knoten der Appliance und das erneute Schreiben in den Speicher. Dies ist ein Vorgang mit hohem CPU- und Netzwerkaufwand.

### Zeitpunkt für die Skalierung

Generell möchten wir DWUs einfach halten. Falls Sie schnellere Ergebnisse benötigen, sollten Sie Ihre DWUs erhöhen und für mehr Leistung zahlen. Falls Sie weniger Computeleistung benötigen, sollten Sie Ihre DWUs verringern und nur für die verbrauchte Leistung bezahlen. Beispiele für das Ändern der Zahl von DWUs sind:

- Wenn Sie keine Abfragen ausführen müssen, z. B. abends oder am Wochenende, halten Sie die Compute-Ressourcen an, um alle ausgeführten Abfragen abzubrechen und alle DWUs zu entfernen, die Ihrem Data Warehouse zugeordnet sind.

- Wenn Sie einen umfangreichen Vorgang zum Laden oder Transformieren von Daten durchführen, sollten Sie zentral hochskalieren, damit die Daten schneller verfügbar sind.

- Damit Sie verstehen, wie Ihr idealer DWU-Wert lautet, sollten Sie versuchsweise hoch- und herunterskalieren und nach dem Laden der Daten einige Abfragen ausführen. Da die Skalierung schnell geht, können Sie es mit mehreren unterschiedlichen Leistungsebenen probieren, ohne dass dies länger als eine Stunde dauert.

> [AZURE.NOTE]Beachten Sie Folgendes: Aufgrund der Architektur bzw. von SQL Data Warehouse erzielen Sie bei kleineren Datenvolumen unter Umständen nicht die erwartete Leistungsskalierung. Wir empfehlen Ihnen, mit Datenvolumen von 1 TB oder höher zu beginnen, um bei Leistungstests genaue Ergebnisse zu erzielen.

## Integriert

SQL Data Warehouse basiert auf dem bewährten relationalen Datenbankmodul von SQL Server und enthält viele Features, die Sie von einem Data Warehouse für Unternehmen erwarten. Wenn Sie Transact-SQL bereits kennen, ist es einfach, Ihr Wissen zu SQL Data Warehouse zu übertragen. Die Beispiele in der gesamten Dokumentation stellen für Fortgeschrittene und Anfänger gleichermaßen eine gute Starthilfe dar. Sie können sich den Aufbau der Sprachelemente von SQL Data Warehouse generell wie folgt vorstellen:

- Für SQL Data Warehouse wird die Transact-SQL-Syntax (T-SQL) von SQL Server für viele Vorgänge verwendet. Außerdem werden viele herkömmliche SQL-Konstrukte unterstützt, z. B. gespeicherte Prozeduren, benutzerdefinierte Funktionen, Tabellenpartitionierung, Indizes und Sortierungen.

- SQL Data Warehouse enthält auch eine Reihe von hochmodernen SQL Server-Features, z. B. in Clustern gruppierte Columnstore-Indizes, PolyBase-Integration und Datenüberwachung (mit Bedrohungsbewertung).

- Da sich SQL Data Warehouse noch in der Entwicklung befindet, sind bestimmte T-SQL-Sprachelemente, die für Data Warehousing-Workloads weniger üblich oder für SQL Server neu sind, unter Umständen derzeit nicht verfügbar. Weitere Informationen hierzu finden Sie in der Dokumentation zur Migration.

Mit Transact-SQL und der Funktionskompatibilität zwischen SQL Server, SQL Data Warehouse, SQL-Datenbank und Analytic Platform System können Sie eine Lösung entwickeln, die Ihren Bedürfnissen entspricht. Sie können entscheiden, wo Ihre Daten basierend auf der Leistung, der Sicherheit und den Skalierungsanforderungen gespeichert werden sollen, und dann Daten bei Bedarf zwischen verschiedenen Systemen übertragen.

Zusätzlich zur Übernahme des T-SQL-Oberflächenbereichs von SQL Server kann SQL Data Warehouse auch in viele Tools integriert werden, mit denen SQL Server-Benutzer ggf. vertraut sind. Wir haben den Schwerpunkt auf die Integration einiger Tools in SQL Data Warehouse gelegt, z. B.:

**Herkömmliche SQL Server-Tools**: Eine vollständige Integration in SQL Server Analysis Services, Integration Services und Reporting Services ist für SQL Data Warehouse verfügbar.

**Cloudbasierte Tools**: SQL Data Warehouse kann zusammen mit einer Reihe von neuen Tools in Azure verwendet werden und ist umfassend in Azure Data Factory, Stream Analytics, Machine Learning und Power BI integriert.

**Drittanbietertools**: Viele Anbieter von Drittanbietertools haben sich die Integration ihrer Tools in SQL Data Warehouse zertifizieren lassen. Sie können die vollständige Liste anzeigen.

## Hybrid

Durch die Nutzung von SQL Data Warehouse mit PolyBase können Benutzer Daten jetzt noch besser in ihrem Ökosystem verschieben und erweiterte Hybrid-Szenarien mit nicht relationalen und lokalen Datenquellen einrichten.

PolyBase ist benutzerfreundlich und ermöglicht es Ihnen, Ihre Daten aus unterschiedlichen Quellen mithilfe der gleichen vertrauten T-SQL-Befehle zu nutzen. PolyBase ermöglicht Ihnen das Abfragen nicht relationaler Daten im Azure Blob-Speicher wie in einer normalen Tabelle. Verwenden Sie PolyBase, um nicht relationale Daten abzufragen oder nicht relationale Daten in SQL Data Warehouse zu importieren.

- PolyBase verwendet externe Tabellen für den Zugriff auf nicht relationale Daten. Die Tabellendefinitionen werden im SQL Data Warehouse gespeichert, und der Zugriff ist – wie bei normalen relationalen Daten – per SQL und mit Tools möglich.

- PolyBase verhält sich bei der Integration agnostisch. Für alle unterstützten Quellen werden die gleichen Features und Funktionen bereitgestellt. Die von PolyBase gelesenen Daten können in einer Vielzahl von Formaten vorliegen, einschließlich Dateien mit Trennzeichen oder ORC-Dateien.

- PolyBase kann zum Zugreifen auf Blob Storage verwendet werden, der auch als Speicher für einen HDInsight-Cluster genutzt wird. So erhalten Sie hervorragenden Zugriff auf dieselben Daten mit relationalen und nicht relationalen Tools.


## Nächste Schritte

Nachdem Sie jetzt einige Informationen zu SQL Data Warehouse erhalten haben, können Sie mit der [Data Warehouse-Workload], der [Bereitstellung] und dem Laden von [Beispieldaten] fortfahren.

>[AZURE.NOTE]Wir möchten diesen Artikel verbessern. Wenn Sie auf die Frage „War dieser Artikel hilfreich?“ mit „Nein“ antworten, bitten wir Sie, einen kurzen Vorschlag dazu einzufügen, was fehlt oder wie der Artikel zu verbessern ist. Vielen Dank im Voraus!

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Data Warehouse-Workload]: ./sql-data-warehouse-overview-workload.md
[Beispieldaten]: ./sql-data-warehouse-get-started-load-samples.md
[Bereitstellung]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_1125_2015-->