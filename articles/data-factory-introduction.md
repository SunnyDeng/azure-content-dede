<properties 
	pageTitle="Einführung in Azure Data Factory" 
	description="Erfahren Sie, wie Sie mithilfe des Azure Data Factory-Diensts Verarbeitungs-, Speicher- und Verschiebungsdienste für Daten entwickeln können, um Pipelines zu erstellen, die vertrauenswürdige Informationen erzeugen." 
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
	ms.date="03/09/2015" 
	ms.author="spelluru"/>

# Einführung in den Azure Data Factory-Dienst
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->

Der **Azure Data Factory**-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Verarbeitungs-, Speicher- und Verschiebungsdienste für Daten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines. Der Data Factory-Dienst ermöglichtes Folgendes: 

- Erstellen datengesteuerter Workflows (Pipelines) zum Verknüpfen, Aggregieren und Transformieren von Daten aus lokalen, cloudbasierten und Internetdatenspeichern. 
- Transformieren semistrukturierter, unstrukturierter und strukturierter Daten aus verschiedenen Datenquellen in vertrauenswürdige Informationen.
- Generieren von Daten, die von Business Intelligence (BI)- und Analysetools sowie anderen Anwendungen problemlos genutzt werden können. 
- Einrichten einer komplexen Datenverarbeitung mithilfe einfacher JSON-Skripts.
- Überwachen und Verwalten von Pipelines auf einen Blick dank einer umfassenden visuellen Darstellung im Azure-Vorschauportal.  


<!--
This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
--> 

## Übersicht
Bisher drehten sich Datenintegrationsprojekte um das Erstellen von ETL-Prozessen (Extrahieren, Transformieren und Laden), die Daten aus unterschiedlichen Datenquellen innerhalb einer Organisation extrahieren, die Daten so transformieren, dass sie dem Zielschema eines Enterprise Data Warehouse (EDW) entsprechen, und die Daten in ein EDW laden. Auf das EDW wird dann als einzige zuverlässige Informationsquelle für BI-Lösungen zugegriffen. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

Die heutige Datenlandschaft für Unternehmen wächst im Hinblick auf Volumen, Vielfältigkeit und Komplexität weiterhin exponentiell. Sie ist aufgrund lokaler und cloudbasierter Daten mit unterschiedlichen Formaten und Geschwindigkeiten vielfältiger als je zuvor.  Die Datenverarbeitung muss über geografische Standorte hinweg erfolgen und umfasst eine Kombination aus Open Source-Software, kommerziellen Lösungen und benutzerdefinierten Verarbeitungsdiensten, die teuer und schwierig zu integrieren und zu verwalten sind.  Die erforderliche Flexibilität, um sich an die sich ändernde Big Data-Landschaft von heute anzupassen, ist eine Gelegenheit, um das herkömmliche EDW mit Funktionen zusammenzuführen, die für ein modernes Informationsproduktionssystem erforderlich sind.  

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]

Der **Azure Data Factory**-Dienst ist die zusammengesetzte Plattform, um über herkömmliche EDWs und die sich ändernde Datenlandschaft hinweg so zu arbeiten, dass Unternehmen alle Daten nutzen können, die ihnen für datengesteuerte Entscheidungsprozesse zur Verfügung stehen. Der Dienst gibt Unternehmen die Möglichkeit, diese Vielfältigkeit zu nutzen, indem eine Plattform bereitgestellt wird, um Speicherungs-, Verschiebungs- und Verarbeitungsdienste für Daten in Informationsproduktions-Pipelines zu kombinieren und vertrauenswürdige Datenressourcen zu verwalten.

Der Azure Data Factory-Dienst ermöglichtes Folgendes:

- **Bequemes Arbeiten mit unterschiedlichen Datenspeicher- und -verarbeitungssystemen.** 
Mit dem Data Factory-Dienst können Sie Informationsproduktions-Pipelines erstellen, mit deren Hilfe lokale Daten (z. B. in SQL Server) und Cloud-Datenquellen, wie z. B. Azure SQL-Datenbanken, Azure-Tabellen und BLOBs, verschoben und verarbeitet werden können. 
- **Transformieren von Daten in vertrauenswürdige Informationen.** 
Der Data Factory-Dienst unterstützt neben wichtigen Verarbeitungsfunktionen wie automatische Verwaltung von Hadoop (HDInsight)-Clustern, Wiederholungen bei vorübergehenden Fehlern, konfigurierbare Zeitüberschreitungsrichtlinien und Warnungen die Hive-, Pig- und C#-Verarbeitung.  
- **Zentrale Überwachung von Datenpipelines.** 
Der Data Factory-Dienst bietet eine zuverlässige und umfassende Ansicht der Speicherungs-, Verarbeitungs- und Datenverschiebungsdienste.  Der Dienst hilft Ihnen, die Integrität der Datenpipeline von A bis Z schnell zu bewerten, Probleme auszumachen und Korrekturmaßnahmen zu ergreifen. Sie können auch die Datenherkunft und die Beziehungen zwischen Ihrer Daten in allen Ihren Quellen visuell nachverfolgen und eine vollständige Verlaufsübersicht von Auftragsausführung, Systemstatus und Abhängigkeiten in einem zentralen Überwachungsdashboard anzeigen.
- **Umfassende Einblicke anhand transformierter Daten**
Mit dem Data Factory-Dienst können Sie Datenpipelines erstellen, die vertrauenswürdige Daten erzeugen, die von Business Intelligence- und Analysetools und anderen Anwendungen genutzt werden können.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into 'Hubs'.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a "West US Azure Hub" which manages all of the Linked services and pipelines focused in the West US data center, or a "Sales EDW Hub" which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Anwendungsmodell
Das folgende Diagramm veranschaulicht das vom Azure Data Factory-Dienst unterstützte Anwendungsmodell.

![Application Model][image-data-factory-application-model]

Eine Azure Data Factory umfasst drei Informationsproduktionsphasen:

- **Verbinden und Sammeln**. In dieser Phase werden Daten aus verschiedenen Datenquellen in Datenhubs importiert. Eine Pipeline in einer Data Factory kann eine oder mehrere Aktivitäten aufweisen. Sie verwenden eine oder mehrere **Kopieraktivitäten** in einer Datenpipeline zum Sammeln von Daten aus Quellendatenspeichern in einem Zieldatenspeicher innerhalb eines Datenhubs zur weiteren Verarbeitung. Ein HDInsight-Cluster (Datenverarbeitung) und der zugehörige Azure-Blob-Speicher (Speicherung) bilden zusammen einen HDInsight-Datenhub. Zum Verwenden eines HDInsight-Datenhubs kopieren Sie alle Quelldaten in einen HDInsight zugeordneten Azure-BLOB-Speicher, damit die Daten im HDInsight-Cluster verarbeitet werden können. Eine Pipeline wird auf einer Datenverarbeitungsressource in einem Datenhub ausgeführt, z. B. einem HDInsight-Cluster.      
- **Transformieren und Verfeinern**. In dieser Phase werden die gesammelten Daten verarbeitet. Eine **HDInsight-Aktivität** in einer Pipeline kann beispielsweise im zugehörigen Azure-Blob-Speicher gespeicherte Daten verarbeiten, indem Transformationen mithilfe von Hive-/Pig-Skripts zum Generieren vertrauenswürdiger Informationen erfolgen. Pipelines können (wie in der Abbildung gezeigt) so verkettet werden, dass Ausgabedatasets einer Pipeline als Eingabedatasets für eine andere Pipeline im selben oder einem anderen Datenhub fungieren können.  
- **Veröffentlichen**. In dieser Phase werden die Daten so veröffentlicht, dass sie von BI-Tools, Analysetools und anderen Anwendungen genutzt werden können. Eine "Kopieraktivität" in der Pipeline kann beispielsweise Ausgabedaten aus der Verarbeitung in der Phase "Transformieren und Verfeinern" in einen Datenspeicher (z. B. lokales SQL Server) kopieren, die als Grundlage von Business Intelligence-Lösungen verwendet werden können.   

<!--

Mithilfe von Data Factorys können Entwickler Pipelines erstellen, bei denen es sich um Gruppen von Datenverschiebungs- und/oder -verarbeitungsaktivitäten handelt, die mehrere Eingabedatasets akzeptieren und ein oder mehrere Ausgabedatasets produzieren. Pipelines können einfach oder anhand eines flexiblen Zeitplans (stündlich, täglich, wöchentlich usw.) ausgeführt werden. Ein Dataset ist eine benannte Ansicht von Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturierten Daten wie CSV-Dateien bis hin zu Tabellen oder Modellen reichen.

Pipelines, die sich aus Datenverschiebungsaktivitäten zusammensetzen (z. B.: Kopieraktivität) werden häufig zum Importieren/Exportieren von Daten in/aus allen Datenquellen (Datenbanken, Dateien, SaaS-Diensten usw.) verwendet, die von dem Unternehmen in einem Datenhub verwendet werden.
 
Sobald sich die Daten in einem **Hub** befinden, werden **Pipelines**, die von den Serverdiensten des Hubs gehostet werden, zum Transformieren der Daten in ein für die Nutzung (durch BI-Tools, Anwendungen, Kunden, usw.) geeignetes Format verwendet.  
  
Und **Pipelines** können außerdem so verkettet werden (wie in dem Diagramm dargestellt), dass die Ausgabe-**Datasets** der einen Pipeline als Eingaben für eine andere Pipeline fungieren.  Auf diese Weise können komplexe Datenflüsse in **Pipelines** einbezogen werden, die innerhalb eines Datenhubs ausgeführt werden oder mehrere Hubs umfassen.  Eine derartige Verwendung von **Pipelines** liefert Unternehmen die Grundbausteine für optimale lokale, Cloud- und SaaS-Dienste (Software-as-a-Service) mittels eine zentralen einfach zu verwaltenden Data Factory.
--> 

## Erstellungsumgebung

Sie können Data Factorys mithilfe einer der folgenden Methoden erstellen:

- **Azure-Vorschauportal**. Die Data Factory-Blätter im Azure-Vorschauportal bieten eine umfassende Benutzeroberfläche zum Erstellen von Factorys und zugehöriger Dienste. Der **Data Factory-Editor**, der auch Teil des Portals ist, ermöglicht Ihnen die einfache Erstellung von verknüpften Diensten, Tabellen, Datasets und Pipelines durch Angabe von JSON-Definitionen für diese Artefakte. Unter [Data Factory-Editor][data-factory-editor] finden Sie eine Übersicht über den Editor und unter [Erste Schritte mit Data Factory][datafactory-getstarted] ein Beispiel für das Verwenden des Portals/Editors zum Erstellen und Bereitstellen einer Data Factory.   
- **Azure PowerShell**. Wenn Sie ein PowerShell-Benutzer sind und PowerShell anstelle der Portalbenutzeroberfläche nutzen möchten, können Sie Azure Data Factory-Cmdlets einsetzen, die als Teil von Azure PowerShell zum Erstellen und Bereitstellen von Data Factorys dienen. Siehe [Erstellen und Überwachen von Azure Data Factory mit Azure PowerShell][create-data-factory-using-powershell] (hier finden Sie ein einfaches Beispiel) und das [Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Daten Factory][adf-tutorial] (hier finden Sie ein erweitertes Beispiel der Verwendung von PowerShell-Cmdlets zum Erstellen und Bereitstellen einer Data Factory). In der [Data Factory-Cmdlet-Referenz][adf-powershell-reference] in der MSDN-Bibliothek finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.  
- **Klassenbibliothek von .NET**. Sie können Daten Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen. Unter [Erstellen, Überwachen und Verwalten von Daten Factorys mit dem .NET SDK][create-factory-using-dotnet-sdk] finden Sie eine exemplarische Vorgehensweise zum Erstellen einer Data Factory mit dem .NET SDK. Unter [Data Factory-Klassenbibliotheksreferenz][msdn-class-library-reference] finden Sie eine umfassende Dokumentation zum Data Factory .NET SDK.  
- **REST-API**. Sie können auch die vom Azure-Data Factory-Dienst verfügbar gemachte REST-API zum Erstellen und Bereitstellen von Data Factorys nutzen. Unter [Data Factory-REST-API][msdn-rest-api-reference] finden Sie eine umfassende Dokumentation zur Data Factory-REST-API. 


## Terminologie
In diesem Abschnitt werden die Begriffe im Zusammenhang mit der Azure Data Factory vorgestellt.

### Data Factory
Eine **Azure Data Factory** verfügt über eine oder mehrere Pipelines, die Daten in verknüpften Datenspeichern (Azure Storage, Azure SQL-Datenbank, lokales SQL Server usw.) mithilfe verknüpfter Datenverarbeitungsdienste, wie z. B. Azure HDInsight, verarbeiten. Eine Azure Data Factory selbst enthält keine Daten. Die Daten werden vielmehr in den oben genannten Datenspeichern gespeichert.  

### Verknüpfter Dienst
Ein **verknüpfter Dienst** ist ein Dienst, der mit einer Azure Data Factory verknüpft ist. Ein verknüpfter Dienst kann Folgendes sein:

- Ein **Datenspeicher** wie Azure Storage, Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank. Ein Datenspeicher ist ein Container für Eingabe-/Ausgabedatasets.    
- Ein **Serverdienst** wie Azure HDInsight und Azure Machine Learning. Ein Serverdienst verarbeitet die Eingabedaten und erzeugt die Ausgabedaten.  

### Dataset
Ein **Dataset** ist eine benannte Ansicht der Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturieren Daten wie CSV-Dateien bis hin zu relationalen Tabellen oder sogar Modellen reichen. Eine Data Factory-**Tabelle** ist ein Dataset, das ein Schema hat und rechteckig ist. Nach dem Erstellen eines verknüpften Diensts in einem Datenspeicher, der auf einen Datenspeicher verweist, definieren Sie Datasets, die im Datenspeicher gespeicherte Eingabe-/Ausgabedaten darstellen. 


### Pipeline
Eine **Pipeline** in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. Eine **Kopieraktivität** kopiert beispielsweise Daten aus einem Quellspeicher in einen Zielspeicher, und **HDInsight-Aktivitäten** verwenden einen Azure HDInsight-Cluster, um Daten mithilfe von Hive-Abfragen oder Pig-Skripts zu verarbeiten. Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen. 

Typische Schritte zum Erstellen einer Instanz einer Azure Data Factory sind:

1. Erstellen einer **Data Factory**
2. Erstellen eines **verknüpften Diensts** für jeden Datenspeicher- oder Serverdienst
3. Erstellen von **Eingabe- und Ausgabedatasets**
4. Erstellen einer **Pipeline** 

### Aktivität
Ein Datenverarbeitungsschritt in einer Pipeline, bei dem aus einem oder mehreren Eingabedatasets ein oder mehrere Ausgabedatasets erstellt werden.  Aktivitäten werden in einer Ausführungsumgebung ausgeführt (zum Beispiel: Azure HDInsight-Cluster), und Daten werden in einen Datenspeicher gelesen/geschrieben, der mit der Azure Data Factory verbunden ist. 

Der Azure Data Factory-Dienst unterstützt die folgenden Aktivitäten in einer Pipeline: 

- Die **Kopieraktivität** kopiert die Daten aus einem Datenspeicher in einen anderen Datenspeicher. Unter [Kopieren von Daten mit Azure Data Factory][copy-data-with-adf] finden Sie ausführliche Informationen, welche Datenspeicher die Kopieraktivität unterstützt. 
- Die **HDInsight-Aktivität** verarbeitet Daten durch Anwenden von Hive-/Pig-Skripts oder MapReduce-Programmen auf einen HDInsight-Cluster. Unter [Verwenden von Pig und Hive mit Data Factory][use-pig-hive] und [Aufrufen von MapReduce-Programmen aus Data Factory][run-map-reduce] finden Sie weitere Informationen. 
- Die **Aktivität "Azure Machine Learning-Stapelbewertung"** ruft die Azure Machine Learning-Stapelbewertungs-API auf. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Data Factory und Azure Machine Learning][azure-ml-adf]. 
- Die **Aktivität "Gespeicherte Prozedur"** ruft eine gespeicherte Prozedur in einer Azure SQL-Datenbank auf. Weitere Informationen finden Sie in der MSDN-Bibliothek unter [Aktivität "Gespeicherte Prozedur"][msdn-stored-procedure-activity].   

### Slice
Eine Tabelle in einer Azure Data Factory besteht aus Slices. Die Breite eines Slices wird durch den Zeitplan (stündlich/täglich) bestimmt. Wenn der Zeitplan "Stündlich" ist, wird stündlich ein Slice mit der Start- und Endzeit einer Pipeline erstellt. Wenn z. B. der Startzeitpunkt der Pipeline 03.03.2015 06:00:00 Uhr und der Endzeitpunkt 03.03.2015 09:00:00 Uhr am selben Tag ist, werden drei Slices erzeugt, einer für jedes 1-Stunden-Intervall: 6 bis 7 Uhr, 7 bis 8 Uhr und 8 bis 9 Uhr.    

Slices bieten die Möglichkeit, mit einer Teilmenge von Daten für einen bestimmten Zeitraum zu arbeiten (z. B. dem Slice, der für die Dauer (Stunde) von 13:00 Uhr bis 14:00 Uhr erzeugt wird). 

### Aktivitätsausführung für einen Slice
Die **Ausführung** bzw. Aktivitätsausführung ist eine Verarbeitungseinheit für einen Slice. Im Falle von Wiederholungen, oder wenn Sie Ihren Slice im Falle von Fehlern erneut ausführen, kann es eine oder mehrere Ausführungen für einen Slice geben. Ein Slice wird durch seine Startzeit identifiziert.

### Gateway zur Datenverwaltung
Das **Microsoft-Gateway zur Datenverwaltung** ist eine Software, die lokale Datenquellen für die Nutzung mit Cloud-Diensten verbindet. Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses beim Azure Data Factory-Portal registrieren, bevor Sie lokale Datenquellen als verknüpfte Dienste hinzufügen.
 
### Datenhub
Ein **Datenhub** ist ein Container für Datenspeicher und Compute Services. Beispielsweise bildet ein Hadoop-Cluster mit HDFS als Speicherdienst und Hive/Pig als Compute Service (Verarbeitung) einen Datenhub. Auf ähnliche Weise kann ein Enterprise Data Warehouse (EDW) als Datenhub modelliert werden (Datenbank als Speicherdienst, gespeicherte Prozeduren und/oder ETL-Tool als Compute Services).  Pipelines werden als Datenspeicher verwendet und werden in einem Datenhub auf Datenverarbeitungsressourcen ausgeführt. Derzeit wird nur ein HDInsight-Hub unterstützt.

Der Datenhub ermöglicht, dass eine Data Factory in logische oder domänenspezifische Gruppierungen aufgeteilt werden kann, z. B. "Azure-Hub USA West", der alle verknüpften Dienste (Datenspeicher und Datenverarbeitungsressourcen) und Pipelines in dem Rechenzentrum "USA West" verwaltet, oder einen "Hub für Vertriebs-EDW", der alle verknüpften Dienste und Pipelines im Zusammenhang mit dem Auffüllen und Verarbeiten von Daten für das Vertriebs-EDW verwaltet.

Ein wichtiges Merkmal eines Hubs besteht darin, dass eine Pipeline auf einem einzelnen Hub ausgeführt wird. Dies bedeutet, dass beim Definieren einer Pipeline alle verknüpften Dienste, auf die von Tabellen oder Aktivitäten innerhalb dieser Pipeline verwiesen wird, den gleichen Hubnamen wie die Pipeline selbst aufweisen müssen. Wenn die "HubName"-Eigenschaft für einen verknüpften Dienst nicht angegeben ist, wird der verknüpfte Dienst im Hub "Standard" platziert.

## Nächste Schritte

1. [Erste Schritte mit Data Factory][datafactory-getstarted]. Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.
2. [Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial]. In diesem Artikel wird in einer **umfassenden exemplarischen Vorgehensweise** die Implementierung eines **realen Szenarios** mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
3. [Data Factory - Häufig gestellte Fragen][adf-faq]. Dieser Artikel enthält eine Liste der häufig gestellten Fragen und Antworten. 
3. [Allgemeine Szenarien für die Verwendung von Azure Data Factory][adf-common-scenarios]. Dieser Artikel beschreibt einige der häufigeren Szenarien für die Verwendung des Azure Data Factory-Diensts.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md

[copy-data-with-adf]: data-factory-copy-activity.md
[use-pig-hive]: data-factory-pig-hive-activities.md
[run-map-reduce]: data-factory-map-reduce.md
[azure-ml-adf]: data-factory-create-predictive-pipelines.md
[adf-common-scenarios]: data-factory-common-scenarios.md
[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[data-factory-editor]: data-factory-editor.md
[create-data-factory-using-powershell]: data-factory-monitor-manage-using-powershell.md

[adf-powershell-reference]: https://msdn.microsoft.com/library/dn820234.aspx 


[msdn-stored-procedure-activity]: https://msdn.microsoft.com/library/dn912649.aspx
[msdn-class-library-reference]: https://msdn.microsoft.com/library/dn883654.aspx
[msdn-rest-api-reference]: https://msdn.microsoft.com/library/dn906738.aspx

[adf-tutorial]: data-factory-tutorial.md
[datafactory-getstarted]: data-factory-get-started.md

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=49-->