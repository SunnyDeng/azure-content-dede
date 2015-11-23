<properties 
	pageTitle="Datenverschiebungsaktivitäten" 
	description="Erfahren Sie mehr über Data Factory-Entitäten, die Sie in einer Data Factory-Pipeline zum Verschieben von Daten verwenden können." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Datenverschiebungsaktivitäten
Data Factory weist einen [global verfügbaren Dienst](#global) auf, um Datenverschiebungen mit der [Kopieraktivität](#copyactivity) für eine Vielzahl der unten aufgeführten Datenspeicher zu unterstützen. Data Factory verfügt auch über integrierte Unterstützung für [das sichere Verschieben von Daten zwischen lokalen Speicherorten und der Cloud](#moveonpremtocloud) über ein Datenverwaltungsgateway.

Ein kurzes Lernprogramm zur Verwendung der Kopieraktivität finden Sie unter [Lernprogramm: Verwenden der Kopieraktivität in einer Azure Data Factory-Pipeline](data-factory-get-started.md). In diesem Lernprogramm kopieren Sie mit der Kopieraktivität Daten aus einem Azure-Blob-Speicher in eine Azure SQL-Datenbank. Im folgenden Abschnitt werden alle Quellen und Senken aufgeführt, die von der Kopieraktivität unterstützt werden.


## Unterstützte Datenspeicher für die Kopieraktivität
Die Kopieraktivität kopiert die Daten von einem **Quelldatenspeicher** in einen Zieldatenspeicher (**Senke**). Data Factory unterstützt die folgenden Datenspeicher und Kombinationen aus Quelle und Senke. Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in den/aus dem Speicher kopiert werden.

| **Quelle** | **Senke** |
| ------ | ---- |
| [Azure Blob](data-factory-azure-blob-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure DocumentDB, lokales Dateisystem, Azure Data Lake-Speicher |
| [Azure-Tabelle](data-factory-azure-table-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure DocumentDB, Azure Data Lake-Speicher |
| [Azure SQL-Datenbank](data-factory-azure-sql-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure DocumentDB, Azure Data Lake-Speicher |
| [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure DocumentDB, Azure Data Lake-Speicher |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, Azure Data Lake-Speicher |
| [Azure Data Lake-Speicher](data-factory-azure-datalake-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure DocumentDB, lokales Dateisystem, Azure Data Lake-Speicher | 
| [SQL Server unter IaaS](data-factory-sqlserver-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokales Dateisystem](data-factory-onprem-file-system-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, lokales Dateisystem, Azure Data Lake-Speicher |
| [Lokaler SQL Server](data-factory-sqlserver-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokale Oracle-Datenbank](data-factory-onprem-oracle-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokale MySQL-Datenbank](data-factory-onprem-mysql-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokale DB2-Datenbank](data-factory-onprem-db2-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokale Teradata-Datenbank](data-factory-onprem-teradata-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokale Sybase-Datenbank](data-factory-onprem-sybase-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |
| [Lokale PostgreSQL-Datenbank](data-factory-onprem-postgresql-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, lokaler SQL Server, SQL Server in IaaS, Azure Data Lake-Speicher |

## <a name="copyactivity"></a>Kopieraktivität
Die Kopieraktivität verwendet ein Eingabedataset (**Quelle**) und kopiert Daten gemäß Aktivitätskonfiguration in ein Ausgabedataset (**Senke**). Das Kopieren der Daten erfolgt als Batch entsprechend dem für die Aktivität angegebenen Zeitplan.

> [AZURE.NOTE]Weitere Informationen zum Definieren von Aktivitäten im Allgemeinen, z. B. verschiedene JSON-Abschnitte und -Eigenschaften, die für alle Aktivitäten verfügbar sind, finden Sie im Artikel [Grundlegendes zu Pipelines und Aktivitäten](data-factory-create-pipelines.md).

Die Kopieraktivität bietet die folgenden Funktionen:

### <a name="global"></a>Global verfügbare Datenverschiebung
Obwohl Azure Data Factory selbst nur in der Region "USA Westen" verfügbar ist, steht der Dienst für Datenverschiebungen, der die Kopieraktivität unterstützt, global in den folgenden Regionen und Ländern zur Verfügung. Die global verfügbare Topologie gewährleistet effiziente Datenverschiebungen und vermeidet in den meisten Fällen regionsübergreifende Hops.


Wenn Sie Daten aus einer Cloudquelle zu einem Cloudziel kopieren (z. B. von Azure Blob zu Azure SQL), wählt der Dienst für Datenverschiebungen die Bereitstellung, die der Senke für die Übertragung am nächsten liegt. Wenn Sie Daten aus einer lokalen Datenquelle zu einer Cloud kopieren oder umgekehrt (z. B. vom lokalen SQL Server zu Azure Blob), werden die Daten tatsächlich über das Datenverwaltungsgateway verschoben. Der Dienst für Datenverschiebungen ist daran nicht beteiligt.

| Region | Gebiet |
| ------ | --------- | 
| USA (Mitte) | US |
| USA (Ost) | US |
| USA, Osten 2 | US |
| USA (Mitte/Norden) | US |
| USA (Mitte/Süden) | US |
| USA (West) | US |
| Brasilien Süd | LATAM |
| Nordeuropa | EMEA |
| Westeuropa | EMEA |
| Südostasien | APAC |
| Japan Ost | APAC |



### <a name="moveonpremtocloud"></a>Sicheres Verschieben von Daten zwischen einem lokalen Speicherort und der Cloud
Eine Herausforderung für die moderne Datenintegration ist das nahtlose Verschieben von Daten zwischen lokalen Speicherorten und der Cloud. Das Datenverwaltungsgateway ist ein Agent, den Sie lokal installieren können, um hybride Datenpipelines zu ermöglichen.

Das Datengateway bietet die folgenden Funktionen:

1.	Sicheres Verwalten des Zugriffs auf lokale Datenspeicher.
2.	Modellieren von lokalen Datenspeichern und Clouddatenspeichern innerhalb einer Data Factory und Verschieben von Daten.
3.	Nutzen einer zentralen Konsole für die Überwachung und Verwaltung mit Informationen über den Gatewaystatus über cloudbasierte Data Factory-Dashboards.


Weitere Informationen finden Sie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md).

### Zuverlässige und kostengünstige Datenverschiebung
Die Kopieraktivität wurde entwickelt, um große Datenmengen zuverlässig und geschützt vor vorübergehenden Fehlern über eine Vielzahl von Datenquellen zu verschieben. Daten können auf kostengünstige Weise mit der Option zum Aktivieren der Komprimierung über das Netzwerk kopiert werden.

### Typkonvertierungen in andere Typsysteme
Verschiedene Datenspeicher weisen unterschiedliche systemeigene Typsysteme auf. Die Kopieraktivität führt automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Sie finden die Zuordnung für ein bestimmtes systemeigenes Typsystem zu .NET für den Datenspeicher im entsprechenden Artikel zum Connector des Datenspeichers. Über diese Zuordnung können Sie beim Erstellen von Tabellen die geeigneten Typen bestimmen, damit die richtigen Konvertierungen während der Kopieraktivität ausgeführt werden.

### Arbeiten mit unterschiedlichen Dateiformaten
Bei der Kopieraktivität werden verschiedene Dateiformate unterstützt, einschließlich Binär-, Text- und Avro-Formate für dateibasierte Speicher. Sie können über die Kopieraktivität Daten von einem Format in ein anderes konvertieren. Beispiel: Text (CSV) in Avro.

### Eigenschaften der Kopieraktivität
Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren.

Bei der Kopieraktivität hängt der Abschnitt **typeProperties** von den Typen der Quellen und Senken ab. Auf jeder der oben genannten Seiten für Datenspeicher sind diese Eigenschaften für den jeweiligen Datenspeichertyp dokumentiert.

<!---HONumber=Nov15_HO3-->