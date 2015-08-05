<properties
   pageTitle="Laden von Daten in SQL Data Warehouse | Microsoft Azure"
   description="Lernen Sie die allgemeinen Szenarios für das Laden von Daten in SQL Data Warehouse kennen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/21/2015"
   ms.author="mausher;barbkess"/>

# Laden von Daten in SQL Data Warehouse
SQL Data Warehouse bietet zahlreiche Optionen zum Laden von Daten für eine Vielzahl von Szenarios. Beispiele für Ladevorgänge:

- Große Batches einmal pro Tag
- Kleinere Batches im Lauf des Tages
- Einfache Updates für (dimensional) kleinere Tabellen

Die Anforderungen der einzelnen Szenarios sind ebenso vielfältig wie die Arten der Daten, die in SQL Data Warehouse geladen werden sollen. In diesem Artikel werden einige der Möglichkeiten aufgelistet, die Sie für das Laden von Daten haben.

## Technologien
SQL Data Warehouse unterstützt diese Standardtools zum Laden von Daten:

- Azure Data Factory
- Befehlszeilenprogramm bcp
- PolyBase
- SQL Server Integration Services (SSIS)
- Drittanbietertools zum Laden von Daten

### Azure Data Factory (ADF)
ADF ist ein vollständig verwalteter Dienst für das Kombinieren von Diensten zur Datenspeicherung, Datenverarbeitung und Datenverschiebung in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines. SQL Data Warehouse ist eine [unterstützte Quelle/Senke][] für ADF-[Kopieraktivitäten][].

### Befehlszeilenprogramm bcp
Die ausführbare Befehlszeilendatei **bcp** ist ein Microsoft-Dienstprogramm zum Laden und Extrahieren von Daten in SQL Server, SQL-Datenbank und SQL Data Warehouse. Zum Einstieg befolgen Sie das Lernprogramm [Laden von Daten mit bcp][].

### PolyBase
PolyBase ist eine Microsoft-Technologie zur Vereinfachung von Datenanalysen durch eine Möglichkeit zum Abfragen von Blob-Speicher in Hadoop und Azure Storage mit standardmäßigem Transact-SQL und ohne MapReduce. PolyBase kann auch Daten aus dem Azure-Blob-Speicher in SQL Data Warehouse laden. Zum Einstieg befolgen Sie das Lernprogramm [Laden mit PolyBase][].

### SQL Server Integration Services (SSIS)
[SSIS][] ist eine Plattform zum Erstellen von Integrations- und Transformationslösungen auf Unternehmensebene. Um Pakete zu erstellen, die Verbindungen mit SQL Data Warehouse herstellen, verwenden Sie den standardmäßigen [OLE DB-Zieladapter] [] mithilfe des ADO.Net-Verbindungsmanagers.

### Drittanbietertools
SQL Data Warehouse unterstützt führende Branchenlösungen für das Laden von Daten. Weitere Einzelheiten finden Sie in unserer Liste der [Lösungspartner][].

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Laden von Daten mit bcp]: sql-data-warehouse-load-with-bcp.md
[Laden mit PolyBase]: sql-data-warehouse-load-with-polybase.md
[Lösungspartner]: sql-data-warehouse-solution-partners.md
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[unterstützte Quelle/Senke]: https://msdn.microsoft.com/library/dn894007.aspx
[Kopieraktivitäten]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!--Other Web references-->

<!---HONumber=July15_HO4-->