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
	ms.date="03/11/2016" 
	ms.author="spelluru"/>

# Datenverschiebungsaktivitäten
Die [Kopieraktivität](#copyactivity) führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem [global verfügbaren Dienst](#global) gestützt, mit dem Daten zwischen verschiedenen Datenspeichern auf sichere, zuverlässige und skalierbare Weise kopiert werden können. Der Dienst wählt automatisch die optimale Region zum Durchführen der Datenverschiebung aus. Es wird die dem Senkendatenspeicher am nächsten gelegene Region verwendet.
 
Betrachten wir nun, wie diese Datenverschiebung in verschiedenen Szenarios erfolgt.

## Kopieren von Daten zwischen zwei Clouddatenspeichern
Wenn sowohl die Quell- als auch die Senkendatenspeicher (sprich die Zieldatenspeicher) sich in der Cloud befinden, durchläuft die Kopieraktivität die folgenden Phasen, um die Daten aus der Quelle in die Senke zu kopieren bzw. zu verschieben. Der Dienst, der als Grundlage der Kopieraktivität dient, führt Folgendes durch:

1. liest Daten aus dem Quelldatenspeicher
2.	führt die Serialisierung/Deserialisierung, Komprimierung/Dekomprimierung, Spaltenzuordnung und Typumwandlung basierend auf der Konfiguration des Eingabedatasets, Ausgabedatasets und der Kopieraktivität durch 
3.	schreibt Daten in den Zieldatenspeicher

![Cloud-zu-Cloud-Kopie](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## Kopieren von Daten zwischen einem lokalen Datenspeicher und einem Clouddatenspeicher
Um [Daten sicher zwischen lokalen Datenspeichern hinter der Unternehmensfirewall und einem Clouddatenspeicher zu verschieben](#moveonpremtocloud), müssen Sie auf dem lokalen Computer das Datenverwaltungsgateway installieren, wobei es sich um einen Agent handelt, der eine hybride Datenverschiebung und -verarbeitung ermöglicht. Das Datenverwaltungsgateway kann auf dem gleichen Computer wie der Datenspeicher selbst oder auf einem separaten Computer, der Zugriff auf den Datenspeicher hat, installiert werden. In diesem Szenario werden die Serialisierung/Deserialisierung, Komprimierung/Dekomprimierung, Spaltenzuordnung und Typumwandlung vom Datenverwaltungsgateway ausgeführt. In solch einem Fall fließen die Daten nicht über den Azure Data Factory-Dienst. Das Datenverwaltungsgateway schreibt die Daten direkt in den Zielspeicher.

![Lokal-zu-Cloud-Kopie](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Kopieren von Daten aus einem bzw. in einen Datenspeicher auf einer Azure Iaas-VM 
Sie können auch mithilfe des Datenverwaltungsgateways Daten von bzw. in unterstützte Datenspeicher verschieben, die auf Azure IaaS-VMs (Infrastructure-as-a-Service-VMs) gehostet werden. In diesem Fall kann das Datenverwaltungsgateway auf dem gleichen virtuellen Azure-Computer wie der Datenspeicher selbst oder auf einem separaten virtuellen Computer, der Zugriff auf den Datenspeicher hat, installiert werden.

## Unterstützte Datenspeicher
Die Kopieraktivität kopiert die Daten aus einem **Quelldatenspeicher** in einen **Senkendatenspeicher**. Data Factory unterstützt die folgenden Datenspeicher. **Außerdem können Daten aus beliebigen Quellen in beliebige Senken geschrieben werden.** Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in den/aus dem Speicher kopiert werden.

| Quellen| Senken |
|:------- | :---- |
| <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure Table](data-factory-azure-table-connector.md)</li><li>[Azure SQL-Datenbank](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (siehe Hinweis unten)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake-Speicher](data-factory-azure-datalake-connector.md)</li><li>[SQL Server – lokal/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Dateisystem – lokal/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Oracle-Datenbank – lokal/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[MySQL-Datenbank – lokal/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[DB2-Datenbank – lokal/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Teradata-Datenbank – lokal/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Sybase-Datenbank – lokal/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[PostgreSQL-Datenbank – lokal/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[ODBC-Datenquellen – lokal/Azure IaaS](data-factory-odbc-connector.md)</li><li>[Hadoop Distributed File System (HDFS) – lokal/Azure IaaS](data-factory-hdfs-connector.md)</li><li>[OData-Quellen](data-factory-odata-connector.md)</li><li>[Webtabelle](data-factory-web-table-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure Table](data-factory-azure-table-connector.md)</li><li>[Azure SQL-Datenbank](data-factory-azure-sql-connector.md)</li><li>[Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB (siehe Hinweis unten)](data-factory-azure-documentdb-connector.md)</li><li>[Azure Data Lake-Speicher](data-factory-azure-datalake-connector.md)</li><li>[SQL Server – lokal/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[Dateisystem – lokal/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] Daten können nur zwischen Azure DocumentDB und anderen Azure-Diensten wie Azure-Blob, Azure-Tabelle, Azure SQL-Datenbank, Azure SQL Data Warehouse, Azure DocumentDB und Azure Data Lake-Speicher verschoben werden. Die vollständige Matrix für Azure DocumentDB wird ebenfalls bald unterstützt.

Wenn Sie Daten zu/von einem Datenspeicher verschieben müssen, der nicht von der **Kopieraktivität** unterstützt wird, können Sie die **benutzerdefinierte Aktivität** in Data Factory mit Ihrer eigenen Logik zum Kopieren/Verschieben der Daten verwenden. Weitere Informationen zum Erstellen und Verwenden einer benutzerdefinierten Aktivität finden Sie im Artikel [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](data-factory-use-custom-activities.md).

## Lernprogramm:
Ein kurzes Lernprogramm zur Verwendung der Kopieraktivität finden Sie unter [Lernprogramm: Verwenden der Kopieraktivität in einer Azure Data Factory-Pipeline](data-factory-get-started.md). In diesem Lernprogramm kopieren Sie mit der Kopieraktivität Daten aus einem Azure-Blob-Speicher in eine Azure SQL-Datenbank.

## <a name="copyactivity"></a>Kopieraktivität
Die Kopieraktivität kopiert Daten aus einem Eingabedataset (**Quelle**) zu einem Ausgabedataset (**Senke**). Das Kopieren der Daten erfolgt als Batch entsprechend dem für die Aktivität angegebenen Zeitplan. Informationen zur allgemeinen Definition von Aktivitäten finden Sie im Artikel [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md).

Die Kopieraktivität bietet die folgenden Funktionen:

### <a name="global"></a>Global verfügbare Datenverschiebung
Obwohl Azure Data Factory selbst nur in den Regionen „USA, Westen“ und „Nordeuropa“ verfügbar ist, steht der Dienst, der die Kopieraktivität unterstützt, global in den folgenden Regionen und Gebieten zur Verfügung. Die global verfügbare Topologie gewährleistet effiziente Datenverschiebungen und vermeidet in den meisten Fällen regionsübergreifende Hops.

Das **Datenverwaltungsgateway** oder **Azure Data Factory** führt die Datenverschiebung basierend auf dem Speicherort der Quell- und Zieldatenspeicher als Kopiervorgang durch. Die folgende Tabelle enthält die Details hierzu:

Speicherort der Quelldaten | Speicherort der Zieldaten | Datenverschiebung erfolgt per  
-------------------------- | ------------------------------- | ----------------------------- 
lokal/Azure-VM (IaaS) | Cloud | **Datenverwaltungsgateway** auf lokalem Computer/lokaler Azure-VM. Die Daten fließen nicht über den Dienst in der Cloud. <br/><br/>Hinweis: Das Datenverwaltungsgateway kann sich auf demselben lokalen Computer bzw. derselben lokalen Azure-VM wie der Datenspeicher oder auf einem anderen lokalen Computer bzw. einer anderen lokalen Azure-VM befinden, solange eine Verbindung mit beiden Datenspeichern hergestellt werden kann.
Cloud | lokal/Azure-VM (IaaS) | Wie oben. 
lokal/Azure-VM (IaaS) | lokal/Azure-VM | **Datenverwaltungsgateway, das der Quelle zugeordnet ist**. Die Daten fließen nicht über den Dienst in der Cloud. Siehe Hinweis oben.   
Cloud | Cloud | **Der Clouddienst, auf dem die Kopieraktivität basiert**. Azure Data Factory verwendet die Bereitstellung dieses Diensts in der Region, die der Senkenposition in der gleichen Region am nächsten liegt. Die Zuordnung können Sie der folgenden Tabelle entnehmen: <br/><br/><table><tr><th>Region des Zieldatenspeichers</th> <th>Region für Datenverschiebung</th></tr><tr><td>USA, Osten</td><td>USA, Osten</td></tr><tr><td>USA, Osten 2</td><td>USA, Osten 2</td><tr/><tr><td>USA, Mitte</td><td>USA, Mitte</td><tr/><tr><td>USA, Westen</td><td>USA, Westen</td></tr><tr><td>USA, Norden-Mitte</td><td>USA, Norden-Mitte</td></tr><tr><td>USA, Süden-Mitte</td><td>USA, Süden-Mitte</td></tr><tr><td>Europa, Norden</td><td>Europa, Norden</td></tr><tr><td>Europa, Westen</td><td>Europa, Westen</td></tr><tr><td>Asien, Südosten</td><td>Asien, Südosten</td></tr><tr><td>Asien, Osten</td><td>Asien, Südosten</td></tr><tr><td>Japan, Osten</td><td>Japan, Osten</td></tr><tr><td>Japan, Westen</td><td>Japan, Osten</td></tr><tr><td>Brasilien, Süden</td><td>Brasilien, Süden</td></tr><tr><td>Australien, Osten</td><td>Australien, Osten</td></tr><tr><td>Australien, Südosten</td><td>Australien, Südosten</td></tr></table>


> [AZURE.NOTE] Wenn die Region des Zieldatenspeichers in der obigen Liste nicht enthalten ist, schlägt die Kopieraktivität fehl, anstatt eine Alternativregion zu wählen.



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
Bei der Kopieraktivität werden verschiedene Dateiformate unterstützt, einschließlich Binär-, Text-, Avro- und JSON-Formate für dateibasierte Speicher. Sie können mit der Kopieraktivität Daten von einem Format in ein anderes konvertieren. Beispiel: Text (CSV) in Avro. Bei unstrukturierten Daten können Sie die **Structure**-Eigenschaft in der JSON-Definition des [Datasets](data-factory-create-datasets.md) weglassen.

### Eigenschaften der Kopieraktivität
Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar. Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren.

Bei der Kopieraktivität hängt der **typeProperties**-Abschnitt von den Typen der Quellen und Senken ab. Auf jeder der oben genannten Seiten für Datenspeicher sind diese Eigenschaften für den jeweiligen Datenspeichertyp dokumentiert.

### Sortierte Kopie
Es ist möglich, mehrere Kopiervorgänge nacheinander sequenziell/sortiert auszuführen. Angenommen, Sie haben zwei Kopieraktivitäten in einer Pipeline: CopyActivity1 und CopyActivity2 mit den folgenden Eingabe-/Ausgabedatasets.

CopyActivity1: Eingabe: Dataset1 Ausgabe: Dataset2

CopyActivity2: Eingabe: Dataset2 Ausgabe: Dataset4

CopyActivity2 wird nur ausgeführt, wenn CopyActivity1 erfolgreich ausgeführt wurde und Dataset2 verfügbar ist.

Im obigen Beispiel kann CopyActivity2 eine andere Eingabe haben, z. B. Dataset3. Sie müssen jedoch auch Dataset2 als Eingabe für CopyActivity2 angeben, damit die Aktivität nicht so lange ausgeführt wird, bis CopyActivity1 abgeschlossen ist. Beispiel:

CopyActivity1: Eingabe: Dataset1 Ausgabe: Dataset2

CopyActivity2: Eingabe: Dataset3, Dataset2 Ausgabe: Dataset4

Wenn mehrere Eingaben angegeben wurden, wird nur das erste Eingabedataset zum Kopieren der Daten verwendet, die anderen Datasets werden aber als Abhängigkeiten verwendet. CopyActivity2 wird nur ausgeführt, wenn die folgenden Bedingungen erfüllt sind:

- CopyActivity2 wurde erfolgreich abgeschlossen und Dataset2 ist verfügbar. Dieses Dataset wird beim Kopieren von Daten zu Dataset4 nicht verwendet. Es fungiert nur als Terminplanungs-Abhängigkeit für CopyActivity2.   
- Dataset3 ist verfügbar. Dieses Dataset stellt die Daten dar, die zum Ziel kopiert werden.  


### Leistung und Optimierung der Kopieraktivität 
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken. Außerdem wird die beobachtete Leistung bei internen Tests aufgeführt und verschiedene Methoden zur Optimierung der Leistung der Kopieraktivität erläutert.


## Assistent zum Kopieren in Data Factory
Der **Assistent zum Kopieren in Data Factory** ermöglicht es Ihnen, eine Pipeline zu erstellen, mit der Sie Daten von unterstützten Quellen zu Zielen kopieren können, ohne JSON-Definitionen für verknüpfte Dienste, Datasets und Pipelines zu schreiben. Zum Starten des Assistenten zum Kopieren klicken Sie auf die Kachel **Daten kopieren** auf der Startseite Ihrer Data Factory.

![Assistent zum Kopieren von Daten](./media/data-factory-data-movement-activities/copy-data-wizard.png)

### Merkmale

#### Ein intuitiver und unkomplizierter Assistent zum Kopieren von Daten 
Mit diesem Assistenten können Sie problemlos innerhalb weniger Minuten Daten von einer Quelle zu einem Ziel verschieben. Führen Sie dazu die folgenden einfachen Schritte aus:

1.	Wählen Sie die **Quelle** aus.
2.	Wählen Sie das **Ziel** aus.
3.	Konfigurieren Sie die **Einstellungen**.

![Auswählen einer Datenquelle](./media/data-factory-data-movement-activities/select-data-source-page.png)

#### Umfangreiche Datensuche und Schemazuordnungen
Sie können innerhalb des Assistenten Tabellen/Ordner durchsuchen, eine Datenvorschau anzeigen, Schemata zuordnen, Ausdrücke überprüfen und einfache Datentransformationen durchführen.

**Tabellen/Ordner durchsuchen** ![Durchsuchen von Tabellen und Ordnern](./media/data-factory-data-movement-activities/browse-tables-folders.png)

#### Skalierbare Funktionalität für unterschiedliche Daten- und Objekttypen
Das Szenario ist von Beginn an für Big Data ausgelegt. Es ist einfach und effizient, Data Factory-Pipelines zu erstellen, die hunderte von Ordnern, Dateien oder Tabellen verschieben können.

**Datenvorschau anzeigen, Schemata zuordnen und einfache Transformationen durchführen** ![Dateiformateinstellungen](./media/data-factory-data-movement-activities/file-format-settings.png) ![Schemazuordnung](./media/data-factory-data-movement-activities/schema-mapping.png) ![Überprüfen von Ausdrücken](./media/data-factory-data-movement-activities/validate-expressions.png)

#### Skalierbare Funktionalität für unterschiedliche Daten- und Objekttypen
Das Szenario ist von Beginn an für Big Data ausgelegt. Mithilfe des Assistenten zum Kopieren ist es einfach und effizient, hunderte von Ordnern, Dateien oder Tabellen zu verschieben.

![Auswählen von Tabellen zum Kopieren von Daten](./media/data-factory-data-movement-activities/select-tables-to-copy-data.png)

#### Umfangreichere Planungsoptionen
Sie können den Kopiervorgang nur einmal oder nach einem Zeitplan (stündlich, täglich etc.) ausführen. Beide Optionen können für die verschiedensten Connectors über die lokale Kopie, Cloudkopie und die Kopie auf dem lokalen Desktop hinweg verwendet werden. Das einmalige Kopieren aktiviert eine einmalige Datenverschiebung von einer Quelle zu einem Ziel und wird auf Daten jeglicher Größe und jeglichen unterstützten Formats angewandt. Das Kopieren nach einem Zeitplan aktiviert das Kopieren von Daten nach einem vorgeschriebenen Wiederholungszyklus. Sie können umfangreiche Einstellungen (wie Wiederholen, Timeout, Warnungen etc.) nutzen, um das Kopieren nach einem Zeitplan zu konfigurieren.

![Planen von Eigenschaften](./media/data-factory-data-movement-activities/scheduling-properties.png)


### Ausprobieren 
Eine kurze exemplarische Vorgehensweise zur Verwendung des **Assistenten zum Kopieren in Data Factory** zum Erstellen einer Pipeline mit einer Kopieraktivität finden Sie unter [Tutorial: Create a pipeline with Copy Activity using Data Factory Copy Wizard](data-factory-copy-data-wizard-tutorial.md) (Tutorial: Erstellen einer Pipeline mit Kopieraktivität mithilfe des Assistenten zum Kopieren in Data Factory).


### Variablen im Azure Blob-Ordnerpfad
Sie können Variablen im Ordnerpfad verwenden, um Daten aus einem Ordner zu kopieren, der zur Laufzeit basierend auf der [WindowStart-Systemvariable](data-factory-functions-variables.md#data-factory-system-variables) bestimmt wird. Die unterstützten Variablen sind: **year**, **month**, **day**, **hour**, **minute** und **{custom}**. Beispiel: Eingabeordner/{year}/{month}/{day}.

Angenommen, Sie haben Eingabeordner im folgenden Format:
	
	2016/03/01/01
	2016/03/01/02
	2016/03/01/03
	...

Klicken Sie auf die Schaltfläche **Durchsuchen** für **Datei oder Ordner**, navigieren Sie zu einem dieser Ordner, z. B. 2016->03->01->02, und klicken Sie auf **Auswählen**. Im Textfeld sollte nun **2016/03/01/02** stehen. Ersetzen Sie nun **2016** durch **{year}**, **03** durch **{month}**, **01** durch **{day}**, **02** durch **{hour}**, und drücken Sie **TAB**. Sie sollten Dropdownlisten zum Auswählen des **Formats** für diese vier Variablen wie nachfolgend dargestellt sehen:

![Verwenden von Systemvariablen](./media/data-factory-data-movement-activities/blob-standard-variables-in-folder-path.png)

Sie können auch, wie nachfolgend dargestellt, eine **benutzerdefinierte** Variable und jegliche [unterstützte Formatzeichenfolgen](https://msdn.microsoft.com/library/8kb3ddd4.aspx) verwenden. Stellen Sie sicher, dass Sie einen Ordner mit dieser Struktur auswählen, indem Sie zunächst die Schaltfläche „Durchsuchen“ verwenden, einen Wert durch **{custom}** ersetzen und **TAB** drücken, um das Textfeld anzuzeigen, in das Sie die Formatzeichenfolge eingeben können.

![Verwenden von benutzerdefinierten Variablen](./media/data-factory-data-movement-activities/blob-custom-variables-in-folder-path.png)

<!---HONumber=AcomDC_0316_2016-->