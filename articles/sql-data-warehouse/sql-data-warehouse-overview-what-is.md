<properties
   pageTitle="Was ist Azure SQL Data Warehouse? | Microsoft Azure"
   description="Verteilte Datenbank der Enterprise-Klasse, die mehrere Petabyte an relationalen und nicht relationalen Daten verarbeiten kann Sie ist das branchenweit erste Cloud-Data Warehouse mit Funktionen zum Vergrößern, Verkleinern und Anhalten innerhalb weniger Sekunden."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/26/2016"
   ms.author="lodipalm;barbkess;mausher;jrj;sonyama;"/>


# Was ist Azure SQL Data Warehouse?

Azure SQL Data Warehouse ist eine cloudbasierte Datenbank für das horizontale Hochskalieren, mit der sehr große Datenvolumen verarbeitet werden können – sowohl relational als auch nicht relational. SQL Data Warehouse basiert auf unserer MPP-Architektur (Massively Parallel Processing) und ist für die Verarbeitung Ihrer Enterprise-Workloads geeignet.

Für SQL Data Warehouse gilt Folgendes:

- Unsere bewährte relationale SQL Server-Datenbank ist darin mit unseren Azure-Cloudfunktionen für das horizontale Hochskalieren kombiniert. Sie können Computevorgänge innerhalb von Sekunden erhöhen, verringern, anhalten oder fortsetzen. So sparen Sie Kosten, indem Sie die CPU horizontal hochskalieren, wenn es erforderlich ist, und die Nutzung außerhalb der Spitzenzeiten herunterfahren.
- Unsere Azure-Plattform wird genutzt. Die Anwendung kann einfach bereitgestellt und nahtlos gewartet werden und verfügt dank der automatischen Sicherungen über eine umfassende Fehlertoleranz. 
- Stellt eine Ergänzung des SQL Server-Ökosystems dar. Sie können vertraute SQL Server T-SQL und -Tools für die Entwicklung nutzen.

Lesen Sie weiter, um mehr Informationen zu den wichtigsten Features von SQL Data Warehouse zu erhalten.

## Optimiert

### MPP-Architektur (Massively Parallel Processing)

Für SQL Data Warehouse wird die MPP-Architektur (Massively Parallel Processing) von Microsoft verwendet, die entworfen wurde, um einige der weltweit größten lokalen Data Warehouses zu betreiben.

Bei der MPP-Architektur werden Ihre Daten derzeit auf 60 Shared-Nothing-Speicher- und -Verarbeitungseinheiten verteilt. Die Daten werden in redundanten, georeplizierten Azure Storage-Blobs gespeichert und zur Abfrageausführung mit Computeknoten verknüpft. Mit dieser Architektur können wir den Ansatz „Teile und herrsche“ wählen, was die Ausführung komplexer T-SQL-Abfragen betrifft. Bei der Verarbeitung wird die Abfrage vom Steuerknoten analysiert, und jeder Computeknoten verarbeitet (beherrscht) seinen Teil der Daten dann jeweils parallel.

Aufgrund der Kombination aus MPP-Architektur und Azure-Speicherfunktionen ist mit SQL Data Warehouse Folgendes möglich:

- Erhöhen oder Verringern des Speichers unabhängig vom Computevorgang
- Vergrößern oder Verkleinern des Computevorgangs ohne Datenverschiebung 
- Anhalten der Computekapazität unter Beibehaltung der Intaktheit von Daten
- Schnelles erneutes Bereitstellen der Computekapazität

Unten wird die Architektur ausführlich beschrieben.

![SQL Data Warehouse-Architektur][1]


- **Steuerknoten:** Mit dem Steuerknoten wird das System „gesteuert“. Dies ist das Front-End, das mit allen Anwendungen und Verbindungen interagiert. In SQL Data Warehouse basiert der Steuerknoten auf SQL-Datenbank, und die Verbindungsherstellung läuft genauso ab. Im Hintergrund koordiniert der Steuerknoten alle Datenverschiebungen und -berechnungen, die zum Ausführen von parallelen Abfragen für Ihre verteilten Daten erforderlich sind. Wenn Sie eine T-SQL-Abfrage an SQL Data Warehouse übermitteln, wird sie vom Steuerknoten in separate Abfragen aufgeteilt, die auf allen Computeknoten parallel ausgeführt werden.

- **Computeknoten:** Die Computeknoten fungieren quasi als Antriebsaggregate von SQL Data Warehouse. Es handelt sich um SQL-Datenbanken, mit denen Ihre Abfrageschritte verarbeitet und Ihre Daten verwaltet werden. Wenn Sie Daten hinzufügen, werden die Zeilen von SQL Data Warehouse mit Ihren Computeknoten verteilt. Die Computeknoten dienen auch als „Worker“, die die parallelen Abfragen für Ihre Daten ausführen. Nach der Verarbeitung übergeben sie die Ergebnisse zurück an den Steuerknoten. Um die Abfrage zu beenden, aggregiert der Steuerknoten die Ergebnisse und gibt das endgültige Ergebnis zurück.


- **Storage (Speicher):** Ihre Daten werden in Azure Storage-Blobs gespeichert. Wenn Computeknoten mit Ihren Daten interagieren, werden Schreib- und Lesevorgänge direkt mit dem Blobspeicher durchgeführt. Da Azure-Speicher transparent und unbegrenzt erweitert werden kann, ist dies auch für SQL Data Warehouse möglich. Da Compute- und Speichervorgänge unabhängig voneinander sind, kann in SQL Data Warehouse die Skalierung von Speicher automatisch separat von der Computeskalierung durchgeführt werden (und umgekehrt). Azure Storage ist außerdem vollständig fehlertolerant und führt zu einer Optimierung des Sicherungs- und Wiederherstellungsprozesses.
   

- **Datenverschiebungsdienst:** Der Datenverschiebungsdienst (Data Movement Service, DMS) ist unsere Technologie zum Verschieben von Daten zwischen Knoten. Per DMS erhalten die Computeknoten Zugriff auf Daten, die sie für Verknüpfungen und Aggregationen benötigen. DMS ist kein Azure-Dienst. Es ist ein Windows-Dienst, der auf allen Knoten zusammen mit SQL-Datenbank ausgeführt wird. Da DMS im Hintergrund ausgeführt wird, erfolgt die Interaktion nicht auf direktem Wege. Wenn Sie sich Abfragepläne ansehen, werden Sie merken, dass diese einige DMS-Vorgänge enthalten. Es ist nämlich eine Art von Datenverschiebung erforderlich, um jede Abfrage parallel ausführen zu können.


### Optimierte Abfrageleistung

Zusätzlich zur Strategie „Teile und herrsche“ wird der MPP-Ansatz durch verschiedene Data Warehouse-spezifische Leistungsoptimierungen unterstützt, z.B.:

- Einen verteilten Abfrageoptimierer und einen Satz mit komplexen Statistiken, die datenübergreifend gelten. Mit den Informationen zur Datengröße und -verteilung kann der Dienst Abfragen optimieren, indem die Kosten bestimmter Vorgänge für verteilte Abfragen bewertet werden.

- Erweiterte Algorithmen und Techniken, die in den Datenverschiebungsprozess integriert sind, um Daten bei Bedarf effizient zwischen Computingressourcen zu verschieben und so die Abfrage durchzuführen. Diese Datenverschiebungsvorgänge sind integriert, und alle Optimierungen des Datenverschiebungsdiensts werden automatisch durchgeführt.

- Gruppierte Columnstore-Indizes werden standardmäßig verwendet. Mithilfe des spaltenbasierten Speichers erreicht SQL Data Warehouse eine bis zu fünfmal höhere Komprimierung als mit dem herkömmlichen zeilenorientierten Speicher und eine bis zu zehnmal höhere Abfrageleistung. Analytics-Abfragen, bei denen eine große Zahl von Zeilen durchsucht werden muss, funktionieren hervorragend mit Columnstore-Indizes.

## Skalierbar

Mit der Architektur von SQL Data Warehouse werden getrennte Speicher- und Compute-Ressourcen eingeführt, sodass diese unabhängig voneinander skaliert werden können. Die schnelle und einfache Bereitstellungsstruktur der SQL-Datenbank ermöglicht es, dass sehr kurzfristig zusätzliche Compute-Ressourcen bereitgestellt werden. Dies wird durch die Verwendung von Azure-Speicher-Blobs vervollständigt. Mit Blobs erhalten wir nicht nur einen stabilen, replizierten Speicher, sondern auch die Infrastruktur für eine mühelose Erweiterung zu niedrigen Kosten. Die Kombination aus der Speicherung auf Cloudebene und Azure Compute-Ressourcen ermöglicht für SQL Data Warehouse die bedarfsabhängige Bezahlung des Abfrageleistungsspeichers. Das Ändern der Computemenge ist so einfach wie das Verschieben eines Schiebereglers nach links oder rechts im Azure-Portal. Dieser Vorgang kann aber auch per T-SQL und PowerShell geplant werden.

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

> [AZURE.NOTE] Beachten Sie Folgendes: Aufgrund der Architektur bzw. von SQL Data Warehouse erzielen Sie bei kleineren Datenvolumen unter Umständen nicht die erwartete Leistungsskalierung. Wir empfehlen Ihnen, mit Datenvolumen von 1 TB oder höher zu beginnen, um bei Leistungstests genaue Ergebnisse zu erzielen.

## Integriert

SQL Data Warehouse basiert auf dem bewährten relationalen Datenbankmodul von SQL Server und enthält viele Features, die Sie von einem Data Warehouse für Unternehmen erwarten. Wenn Sie Transact-SQL bereits kennen, ist es einfach, Ihr Wissen zu SQL Data Warehouse zu übertragen. Die Beispiele in der gesamten Dokumentation stellen für Fortgeschrittene und Anfänger gleichermaßen eine gute Starthilfe dar. Sie können sich den Aufbau der Sprachelemente von SQL Data Warehouse generell wie folgt vorstellen:

- Für SQL Data Warehouse wird die Transact-SQL-Syntax (T-SQL) von SQL Server für viele Vorgänge verwendet. Außerdem werden viele herkömmliche SQL-Konstrukte unterstützt, z. B. gespeicherte Prozeduren, benutzerdefinierte Funktionen, Tabellenpartitionierung, Indizes und Sortierungen.

- SQL Data Warehouse enthält auch eine Reihe von hochmodernen SQL Server-Features, z. B. in Clustern gruppierte Columnstore-Indizes, PolyBase-Integration und Datenüberwachung (mit Bedrohungsbewertung).

- Da sich SQL Data Warehouse noch in der Entwicklung befindet, sind bestimmte T-SQL-Sprachelemente, die für Data Warehousing-Workloads weniger üblich oder für SQL Server neu sind, unter Umständen derzeit nicht verfügbar. Weitere Informationen hierzu finden Sie in der Dokumentation zur Migration.

Mit Transact-SQL und der Funktionskompatibilität zwischen SQL Server, SQL Data Warehouse, SQL-Datenbank und Analytic Platform System können Sie eine Lösung entwickeln, die Ihren Bedürfnissen entspricht. Sie können entscheiden, wo Ihre Daten basierend auf der Leistung, der Sicherheit und den Skalierungsanforderungen gespeichert werden sollen, und dann Daten bei Bedarf zwischen verschiedenen Systemen übertragen.

Zusätzlich zur Übernahme des T-SQL-Oberflächenbereichs von SQL Server kann SQL Data Warehouse auch in viele Tools integriert werden, mit denen SQL Server-Benutzer ggf. vertraut sind. Wir haben den Schwerpunkt auf die Integration einiger Tools in SQL Data Warehouse gelegt, z. B.:

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

>[AZURE.NOTE] Wir möchten diesen Artikel verbessern. Wenn Sie auf die Frage „War dieser Artikel hilfreich?“ mit „Nein“ antworten, bitten wir Sie, einen kurzen Vorschlag dazu einzufügen, was fehlt oder wie der Artikel zu verbessern ist. Vielen Dank im Voraus!

<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Data Warehouse-Workload]: ./sql-data-warehouse-overview-workload.md
[Beispieldaten]: ./sql-data-warehouse-get-started-load-samples.md
[Bereitstellung]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->