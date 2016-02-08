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
	ms.date="12/01/2015" 
	ms.author="spelluru"/>

# Datenverschiebungsaktivitäten
Die [Kopieraktivität](#copyactivity) führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem [global verfügbaren Datenverschiebungsdienst](#global) gestützt, mit dem Daten zwischen verschiedenen Datenspeichern auf sichere, zuverlässige und skalierbare Weise kopiert werden können. Der Dienst wählt automatisch die optimale Region aus, um die Datenverschiebung basierend auf dem Speicherort der Quell- und Senkendatenspeicher durchzuführen. Derzeit wird die dem Senkendatenspeicher am nächsten gelegene Region verwendet.
 
Betrachten wir nun, wie diese Datenverschiebung in verschiedenen Szenarios erfolgt.

## Kopieren von Daten zwischen zwei Clouddatenspeichern
Wenn sowohl die Quell- als auch die Senkendatenspeicher (sprich die Zieldatenspeicher) sich in der Cloud befinden, durchläuft die Kopieraktivität die folgenden Phasen, um die Daten aus der Quelle in die Senke zu kopieren bzw. zu verschieben. Der Datenverschiebungsdienst

1. liest Daten aus dem Quelldatenspeicher
2.	führt die Serialisierung/Deserialisierung, Komprimierung/Dekomprimierung, Spaltenzuordnung und Typumwandlung basierend auf der Konfiguration des Eingabedatasets, Ausgabedatasets und der Kopieraktivität durch 
3.	schreibt Daten in den Zieldatenspeicher

![Cloud-zu-Cloud-Kopie](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Kopieren von Daten zwischen einem lokalen Datenspeicher und einem Clouddatenspeicher
Um [Daten sicher zwischen lokalen Datenspeichern hinter der Unternehmensfirewall und einem Clouddatenspeicher zu verschieben](#moveonpremtocloud), müssen Sie auf dem lokalen Computer das Datenverwaltungsgateway installieren, wobei es sich um einen Agent handelt, der eine hybride Datenverschiebung und -verarbeitung ermöglicht. Das Datenverwaltungsgateway kann auf dem gleichen Computer wie der Datenspeicher selbst oder auf einem separaten Computer, der Zugriff auf den Datenspeicher hat, installiert werden. In diesem Szenario werden die Serialisierung/Deserialisierung, Komprimierung/Dekomprimierung, Spaltenzuordnung und Typumwandlung vom Datenverwaltungsgateway ausgeführt. Der Datenverschiebungsdienst ist nicht an diesem Szenario beteiligt.

![Lokal-zu-Cloud-Kopie](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Kopieren von Daten aus einem bzw. in einen Datenspeicher auf einer Azure Iaas-VM 
Sie können auch mithilfe des Datenverwaltungsgateways Daten von bzw. in unterstützte Datenspeicher verschieben, die auf Azure IaaS-VMs (Infrastructure-as-a-Service-VMs) gehostet werden. In diesem Fall kann das Datenverwaltungsgateway auf dem gleichen virtuellen Azure-Computer wie der Datenspeicher selbst oder auf einem separaten virtuellen Computer, der Zugriff auf den Datenspeicher hat, installiert werden.

## Unterstützte Datenspeicher
Die Kopieraktivität kopiert die Daten aus einem **Quelldatenspeicher** in einen **Senkendatenspeicher**. Data Factory unterstützt die folgenden Datenspeicher. **Außerdem können Daten aus beliebigen Quellen in beliebige Senken geschrieben werden.** Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in den/aus dem Speicher kopiert werden.

| Quellen| Senken |
|:------- | :---- |
| <ul><li>[Azure-Blob](data-factory-azure-blob-connector.md)</li><li>[Azure-Tabelle](data-factory-azure-table-connector.md)</li><li>[Azure SQL-Datenbank](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (siehe Hinweis unten)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake-Speicher](data-factory-azure-datalake-connector.md)</li><li>[SQL Server lokal/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Dateisystem lokal/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Oracle-Datenbank – lokal/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[MySQL-Datenbank – lokal/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[DB2-Datenbank – lokal/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Teradata-Datenbank – lokal/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Sybase-Datenbank – lokal/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[PostgreSQL-Datenbank – lokal/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[ODBC-Datenquellen](data-factory-odbc-connector.md)</li><li>[Hadoop Distributed File System (HDFS)](data-factory-hdfs-connector.md)</li></ul> | <ul><li>[Azure-Blob](data-factory-azure-blob-connector.md)</li><li>[Azure-Tabelle](data-factory-azure-table-connector.md)</li><li>[Azure SQL-Datenbank](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (siehe Hinweis unten)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake-Speicher](data-factory-azure-datalake-connector.md)</li><li>[SQL Server – lokal/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Dateisystem – lokal/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] Daten können nur zwischen Azure DocumentDB und anderen Azure-Diensten wie Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, Azure DocumentDB und Azure Data Lake-Speicher verschoben werden. Die vollständige Matrix für Azure DocumentDB wird ebenfalls bald unterstützt.

## Lernprogramm:
Ein kurzes Lernprogramm zur Verwendung der Kopieraktivität finden Sie unter [Lernprogramm: Verwenden der Kopieraktivität in einer Azure Data Factory-Pipeline](data-factory-get-started.md). In diesem Lernprogramm kopieren Sie mit der Kopieraktivität Daten aus einem Azure-Blob-Speicher in eine Azure SQL-Datenbank. Im folgenden Abschnitt werden alle Quellen und Senken aufgeführt, die von der Kopieraktivität unterstützt werden.

## <a name="copyactivity"></a>Kopieraktivität
Die Kopieraktivität verwendet ein Eingabedataset (**Quelle**) und ein Ausgabedataset (**Senke**). Das Kopieren der Daten erfolgt als Batch entsprechend dem für die Aktivität angegebenen Zeitplan. Weitere Informationen zum Definieren von Aktivitäten im Allgemeinen, wie z. B. verschiedene JSON-Abschnitte und -Eigenschaften, die für alle Aktivitäten verfügbar sind, finden Sie im Artikel [Grundlegendes zu Pipelines und Aktivitäten](data-factory-create-pipelines.md).

Die Kopieraktivität bietet die folgenden Funktionen:

### <a name="global"></a>Global verfügbare Datenverschiebung
Obwohl Azure Data Factory selbst nur in den Regionen „USA Westen“ und „Nordeuropa“ verfügbar ist, steht der Datenverschiebungsdienst, der die Kopieraktivität unterstützt, global in den folgenden Regionen und Gebieten zur Verfügung. Die global verfügbare Topologie gewährleistet effiziente Datenverschiebungen und vermeidet in den meisten Fällen regionsübergreifende Hops.

| Region | Gebiet |
| ------ | --------- | 
| USA (Mitte) | US |
| USA (Ost) | US |
| USA (Ost 2) | US |
| USA (Mitte/Norden) | US |
| USA (Mitte/Süden) | US |
| USA (West) | US |
| Brasilien Süd | LATAM |
| Nordeuropa | EMEA |
| Westeuropa | EMEA |
| Südostasien | APAC |
| Japan Ost | APAC |

Beachten Sie Folgendes:

- Wenn Sie Daten aus einer **lokalen Datenquelle** in eine **Cloud** kopieren oder umgekehrt (z. B. vom lokalen SQL Server zu Azure Blob), werden die Daten tatsächlich über das **Datenverwaltungsgateway** in Ihrer lokalen Umgebung verschoben. Der Datenverschiebungsdienst ist daran nicht beteiligt.
- Wenn Sie Daten aus einer **Cloudquelle** in ein **Cloudziel** kopieren (z. B. aus Azure-Blob in Azure SQL), wählt der **Datenverschiebungsdienst** die Bereitstellung, die **der Senke für die Übertragung im gleichen Gebiet am nächsten liegt**. Wenn Sie beispielsweise Daten von Südostasien nach Japan West kopieren, wird für den Kopiervorgang die Bereitstellung des Datenverschiebungsdiensts in Japan Ost verwendet. Wenn sich Quelle und Ziel im gleichen Gebiet befinden und in diesem Gebiet kein Datenverschiebungsdienst verfügbar ist (z. B. derzeit Australien), schlägt die Kopieraktivität fehl, anstatt über ein alternatives Gebiet durchgeführt zu werden. Hinweis: Der Datenverschiebungsdienst würde auch auf Australien erweitert werden. 

### <a name="moveonpremtocloud"></a>Sicheres Verschieben von Daten zwischen einem lokalen Speicherort und der Cloud
Eine Herausforderung für die moderne Datenintegration ist das nahtlose Verschieben von Daten zwischen lokalen Speicherorten und der Cloud. Das Datenverwaltungsgateway ist ein Agent, den Sie lokal installieren können, um hybride Datenpipelines zu ermöglichen.

Das Datengateway bietet die folgenden Funktionen:

1.	Sicheres Verwalten des Zugriffs auf lokale Datenspeicher.
2.	Modellieren von lokalen Datenspeichern und Clouddatenspeichern innerhalb einer Data Factory und Verschieben von Daten.
3.	Nutzen einer zentralen Konsole für die Überwachung und Verwaltung mit Informationen über den Gatewaystatus über cloudbasierte Data Factory-Dashboards.

Auch bei Verwendung von **ExpressRoute** und **des Gateways**, um eine Verbindung zwischen dem Dienst und der Datenquelle herzustellen, sollten Sie Ihre Datenquelle wie eine lokale Datenquelle behandeln (die sich hinter einer Firewall befindet).

Weitere Informationen finden Sie unter [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md).

### Zuverlässige und kostengünstige Datenverschiebung
Die Kopieraktivität wurde entwickelt, um große Datenmengen zuverlässig und geschützt vor vorübergehenden Fehlern über eine Vielzahl von Datenquellen zu verschieben. Daten können auf kostengünstige Weise mit der Option zum Aktivieren der Komprimierung über das Netzwerk kopiert werden.

### Typkonvertierungen in andere Typsysteme
Verschiedene Datenspeicher weisen unterschiedliche systemeigene Typsysteme auf. Die Kopieraktivität führt automatische Typumwandlungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Sie finden die Zuordnung für ein bestimmtes systemeigenes Typsystem zu .NET für den Datenspeicher im entsprechenden Artikel zum Connector des Datenspeichers. Über diese Zuordnungen können Sie beim Erstellen von Tabellen die geeigneten Typen bestimmen, damit während der Kopieraktivität die richtigen Umwandlungen ausgeführt werden.

### Arbeiten mit unterschiedlichen Dateiformaten
Bei der Kopieraktivität werden verschiedene Dateiformate unterstützt, einschließlich Binär-, Text- und Avro-Formate für dateibasierte Speicher. Sie können mit der Kopieraktivität Daten von einem Format in ein anderes konvertieren. Beispiel: Text (CSV) in Avro. Bei unstrukturierten Daten können Sie die **Structure**-Eigenschaft in der JSON-Definition des [Datasets](data-factory-create-datasets.md) weglassen.

### Eigenschaften der Kopieraktivität
Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren.

Bei der Kopieraktivität hängt der **typeProperties**-Abschnitt von den Typen der Quellen und Senken ab. Auf jeder der oben genannten Seiten für Datenspeicher sind diese Eigenschaften für den jeweiligen Datenspeichertyp dokumentiert.


### Leistung und Optimierung der Kopieraktivität 
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken. Außerdem wird die beobachtete Leistung bei internen Tests aufgeführt und verschiedene Methoden zur Optimierung der Leistung der Kopieraktivität erläutert.

<!---HONumber=AcomDC_0128_2016-->