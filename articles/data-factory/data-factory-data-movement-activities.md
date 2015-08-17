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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>


# Datenverschiebungsaktivitäten
Data Factory weist einen [global verfügbaren Dienst](#global) auf, um Datenverschiebungen mit der [Kopieraktivität](#copyactivity) für eine Vielzahl der unten aufgeführten Datenspeicher zu unterstützen. Data Factory verfügt auch über integrierte Unterstützung für [das sichere Verschieben von Daten zwischen lokalen Speicherorten und der Cloud](#moveonpremtocloud) über ein Datenverwaltungsgateway.

## Unterstützte Datenspeicher für die Kopieraktivität
Die Kopieraktivität kopiert die Daten von einem **Quelldatenspeicher** in einen Zieldatenspeicher (**Senke**). Data Factory unterstützt die folgenden Datenspeicher und Kombinationen aus Quelle und Senke. Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in den/aus dem Speicher kopiert werden.

|  | **Senke** |
| ------ | ---- |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS, Azure DocumentDB |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS, Azure DocumentDB |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS, Azure DocumentDB |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank |
| [SQL Server unter IaaS](data-factory-sqlserver-connector.md) | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |
|  | Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, lokaler SQL Server, SQL Server unter IaaS |

## <a name="copyactivity"></a>Kopieraktivität
Die Kopieraktivität verwendet ein Eingabedataset (**Quelle**) und kopiert Daten gemäß Aktivitätskonfiguration in ein Ausgabedataset (**Senke**). Das Kopieren der Daten erfolgt als Batch entsprechend dem für die Aktivität angegebenen Zeitplan.

> [AZURE.NOTE]Weitere Informationen zum Definieren von Aktivitäten im Allgemeinen, wie z. B. verschiedene JSON-Abschnitte und -Eigenschaften, die für alle Aktivitäten verfügbar sind, finden Sie im Artikel [Grundlegendes zu Pipelines und Aktivitäten](data-factory-create-pipelines.md).

Die Kopieraktivität bietet die folgenden Funktionen:

### <a name="global"></a>Global verfügbare Datenverschiebung
Der Dienst für Datenverschiebungen, der die Kopieraktivität unterstützt, ist in den folgenden Regionen und Ländern global verfügbar. Die global verfügbare Topologie gewährleistet effiziente Datenverschiebungen und vermeidet in den meisten Fällen regionsübergreifende Hops.

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
Für dateibasierte Quellen unterstützt die Kopieraktivität verschiedener Dateiformate, einschließlich Binär-, Text- und Avro-Formate.

### Eigenschaften der Kopieraktivität
Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **TypeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren.

Bei der Kopieraktivität hängt der Abschnitt **TypeProperties** von den Typen der Quellen und Senken ab. Auf jeder der oben genannten Seiten für Datenspeicher sind diese Eigenschaften für den jeweiligen Datenspeichertyp dokumentiert.

<!---HONumber=August15_HO6-->