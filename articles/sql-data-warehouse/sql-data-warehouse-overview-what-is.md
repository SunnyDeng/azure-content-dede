<properties
   pageTitle="Was ist Azure SQL Data Warehouse? | Microsoft Azure"
   description="Verteilte Datenbank der Enterprise-Klasse, die mehrere Petabyte an relationalen und nicht relationalen Daten verarbeiten kann Sie ist das branchenweit erste Cloud-Data Warehouse mit Funktionen zum Vergrößern, Verkleinern und Anhalten innerhalb weniger Sekunden."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/08/2015"
   ms.author="barbkess;twounder;JRJ@BigBangData.co.uk;"/>


# Was ist Azure SQL Data Warehouse?

Azure SQL Data Warehouse ist eine verteilte Datenbank der Enterprise-Klasse, die mehrere Petabyte an relationalen und nicht relationalen Daten verarbeiten kann. Sie ist das branchenweit erste Cloud-Data Warehouse mit Funktionen zum Vergrößern, Verkleinern und Anhalten innerhalb weniger Sekunden mit bewährten Funktionen.

SQL Data Warehouse kombiniert das Beste aus der relationalen Datenbankqualität der Enterprise-Klasse von SQL Server mit der Zuverlässigkeit des Cloud Computing. Weitere Informationen zu den wichtigsten Entwurfskomponenten von SQL Data Warehouse.

## Vergrößern, Verkleinern oder Anhalten von Compute-Ressourcen
SQL Data Warehouse führt getrennte Speicher- und Compute-Ressourcen ein, sodass diese unabhängig voneinander skaliert werden können. Mit Speicher auf Cloudebene und Azure Compute-Ressourcen ermöglicht SQL Data Warehouse die bedarfsbasierte Bezahlung der Abfrageleistung. Sie können nun innerhalb von Sekunden dynamisch bereitstellen, vergrößern, verkleinern und sogar anhalten, um Einblicke in Ihre Geschäftsanforderungen zu erwerben. Das Ändern des Umfangs der Computeleistung ist so einfach wie das Bewegen eines Schiebereglers nach links oder rechts.

Dank der Möglichkeit zur vollständigen Kontrolle über den Umfang der Computeleistung unabhängig vom Speicher ermöglicht Ihnen SQL Data Warehouse das Anhalten der Computeressource. Dies wird beibehalten, die Computeressource wird zurück in den zentralen Pool verschoben, um Kosten zu sparen. Setzen Sie die Computeressource bei Bedarf einfach fort, und halten Sie Ihre Daten und Computeleistung für Ihre Arbeitslast zur Verfügung.

> [AZURE.NOTE]Data Warehouse-Einheiten (DWUs) sind die Skalierungseinheiten für Compute-Ressourcen in SQL Data Warehouse.

- Wenn Sie schnellere Ergebnisse benötigen, erhöhen Sie Ihre DWUs und zahlen für die höhere Anzahl der DWUs. Wenn Sie nicht so viel Rechenleistung benötigen, verringern Sie Ihre DWUs und zahlen nur noch für die geringere Anzahl der DWUs. Die Rechenleistung wächst proportional zur Anzahl der DWUs; verdoppeln sich die DWUs, verdoppeln sich auch die Compute-Ressourcen. 
- Wenn Sie keine Abfragen ausführen müssen, z. B. abends oder am Wochenende, halten Sie die Compute-Ressourcen an, um alle ausgeführten Abfragen abzubrechen und alle DWUs zu entfernen, die Ihrem Data Warehouse zugeordnet sind. Ihr Datenspeicher bleibt intakt, aber es fallen keine Gebühren für Compute-Ressourcen an. Wenn Sie Abfragen ausführen müssen, z. B. am Montag Morgen, können Sie die Compute-Ressourcen fortsetzen. 

## Umfangreiche parallele Verarbeitung und ColumnStore-Indizes für bahnbrechende Leistung
SQL Data Warehouse verwendet die MPP-Architektur (Massive Parallel Processing, umfangreiche parallele Verarbeitung) von Microsoft und die ColumnStore-Indextechnologie von SQL Server, um eine bahnbrechende Leistung zu erzielen.

> [AZURE.NOTE]MPP ist ein so genannter "Divide and Conquer"-Ansatz für die Lösung von Problemen mit großen Datenmengen mithilfe der parallelen Verarbeitung. Daten werden dabei geteilt und auf viele Computerressourcen verteilt, und jede Computerressource arbeitet parallel zu den anderen an ihrem Datenteil.

- Dafür ist vor allem die verteilte Abfragetechnologie von Microsoft verantwortlich. SQL Data Warehouse verwendet einen erweiterten Abfrageoptimierer, der Informationen zum Optimieren von verteilten Abfragen auf der Grundlage der Bewertung der Kosten von Abfragevorgängen ermittelt. SQL Data Warehouse verfügt außerdem über erweiterte Algorithmen und Techniken, um die Daten nach Bedarf effizient zwischen den Computerressourcen zu verschieben und die Abfrage auszuführen.
- ColumnStore-Indizes sind entscheidend für eine schnelle Abfrageleistung für Data Warehouse-Abfragen. Mithilfe des spaltenbasierten Speichers erhalten ColumnStore-Indizes eine bis zu fünfmal höhere Komprimierung als mit dem herkömmlichen zeilenorientierten Speicher und eine bis zu zehnmal höhere Abfrageleistung. Data Warehouse-Abfragen funktionieren hervorragend für ColumnStore-Indizes, da sie häufig die gesamte Tabelle oder die gesamte Partition einer Tabelle durchsuchen. Im Gegensatz dazu funktionieren OLTP-Abfragen gut für binäre Strukturindizes, da sie in bestimmten Zeilen der Tabelle suchen.


## Hybridcloud mit SQL Server-Erfahrung der Enterprise-Klasse
SQL Data Warehouse basiert auf dem bewährten relationalen Datenbankmodul von SQL Server und umfasst die Funktionen, die von einem Enterprise-Data Warehouse erwartet werden, z. B. gespeicherte Prozeduren, benutzerdefinierte Funktionen, Tabellenpartitionierung, Indizes und Sortierungen.

> [AZURE.NOTE]Wenn Sie Transact-SQL bereits kennen, ist es einfach, Ihr Wissen zu SQL Data Warehouse zu übertragen. Mit den Dokumentationsbeispielen werden die ersten Schritte unabhängig davon, ob Sie Anfänger oder Fortgeschrittener sind, sehr einfach.

- SQL Data Warehouse verwendet Transact-SQL von SQL Server, ColumnStore-Index und PolyBase-Technologien sowie die MPP-Architektur (Massively Parallel Processing, umfangreiche parallele Verarbeitung) von Analytic Platform System, um diese einzigartige, integrierte, Platform-as-a-Service (PaaS)-Data Warehouse-Erfahrung zu erstellen.  

- Mit Transact-SQL und der Funktionskompatibilität zwischen SQL Server, SQL Data Warehouse, SQL-Datenbank und Analytic Platform System können Sie eine Lösung entwickeln, die Ihren Bedürfnissen entspricht. Sie können entscheiden, wo Ihre Daten basierend auf der Leistung, der Sicherheit und den Skalierungsanforderungen gespeichert werden sollen, und dann Daten bei Bedarf zwischen lokalen Ressourcen und der Cloud übertragen.


## Abfragen für relationale und nicht relationalen Daten
PolyBase ermöglicht Ihnen das Abfragen nicht relationaler Daten im Azure Blob-Speicher oder im Hadoop File System (HDFS) wie in einer regulären Tabelle. Verwenden Sie PolyBase, um nicht relationale Daten abzufragen oder nicht relationale Daten in SQL Data Warehouse zu importieren.

> [AZURE.NOTE]PolyBase ist benutzerfreundlich und ermöglicht es Ihnen, Ihre Daten aus unterschiedlichen Quellen mithilfe der gleichen T-SQL-Befehle zu nutzen, mit denen Sie bereits vertraut sind. Es müssen kein HiveQL oder anderen Sprachen lernen, um von Hadoop zu profitieren.

- PolyBase verhält sich bei der Integration agnostisch. Es stellt allen unterstützten Verteilungen von Hadoop die gleichen Features und Funktionen bereit. Die von Polybase gelesenen Daten können in einer Vielzahl von Formaten vorliegen, einschließlich Dateien mit Trennzeichen oder ORC-Dateien.
- PolyBase verwendet externe Tabellen für den Zugriff auf nicht relationale Daten. Die Tabellendefinitionen werden in SQL Data Warehouse gespeichert, und die Daten werden extern im Hadoop- oder Azure Blob-Speicher gespeichert.


## Sichere Infrastrukturbereitstellung ohne Wartungskosten
SQL Data Warehouse wird problemlos in wenigen Sekunden bereitgestellt. Dieser Dienst ist ein vollständig verwaltetes Angebot, das den zeitlichen Aufwand für Softwarepatches und Wartung entfernt. SQL Data Warehouse verfügt über integrierte Sicherungen, um Self-Service-Wiederherstellung zu unterstützen. Der Dienst sichert automatisch Ihre Daten im Azure-Speicher, während er Momentaufnahmen für Datenbankwiederherstellungspunkte erstellt.


## Nächste Schritte
Erfahren Sie mehr über die [Data Warehouse-Workload]. [Beispieldaten] [bereitstellen] und laden, um loszulegen.

>[AZURE.NOTE]Wir möchten diesen Artikel verbessern. Wenn Sie auf die Frage „War dieser Artikel hilfreich?“ mit „Nein“ antworten, bitten wir Sie, einen kurzen Vorschlag dazu einzufügen, was fehlt oder wie der Artikel zu verbessern ist. Vielen Dank im Voraus!

<!--Image references-->

<!--Article references-->
[Data Warehouse-Workload]: ./sql-data-warehouse-overview-workload.md
[Beispieldaten]: ./sql-data-warehouse-get-started-load-samples.md
[bereitstellen]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO3-->