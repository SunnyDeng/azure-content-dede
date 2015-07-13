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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Einführung in den Azure Data Factory-Dienst
<!--
The **Azure Data Factory** service is a fully managed service for composing data storage, processing, and movement services into streamlined, scalable, and reliable data production pipelines.  Developers can use Data Factory to transform semi-structured, unstructured and structured data from on-premises and cloud sources into trusted information. Developers build data-driven workflows (pipelines) that join, aggregate and transform data sourced from their on-premises, cloud-based and internet services, and set up complex data processing through simple JSON scripting. The Azure Data Factory service provides monitoring and management of these pipelines at a glance with a rich visual experience offered through the Azure Preview Portal. The information produced by pipelines can be easily consumed using BI and analytics tools, and other applications to reliably drive key business insights and decisions.
-->


Der Dienst **Azure Data Factory** ist ein vollständig verwalteter Dienst für das Kombinieren von Verarbeitungs-, Speicher- und Verschiebungsdienste für Daten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines. Der Data Factory-Dienst ermöglichtes Folgendes:

- Erstellen datengesteuerter Workflows (Pipelines) zum Verknüpfen, Aggregieren und Transformieren von Daten aus lokalen, cloudbasierten und Internetdatenspeichern. 
- Transformieren semistrukturierter, unstrukturierter und strukturierter Daten aus verschiedenen Datenquellen in vertrauenswürdige Informationen.
- Generieren von Daten, die von Business Intelligence (BI)- und Analysetools sowie anderen Anwendungen problemlos genutzt werden können. 
- Einrichten einer komplexen Datenverarbeitung mithilfe einfacher JSON-Skripts.
- Überwachen und Verwalten von Pipelines auf einen Blick dank einer umfassenden visuellen Darstellung im Azure-Vorschauportal.  

Das folgende Video enthält eine kurze Übersicht über den Dienst Azure Data Factory.


- [Video: Einführung in Azure Data Factory](http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory/)


<!--
> [AZURE.VIDEO azure-data-factory-overview]

This article provides an overview of the Azure Data Factory service, the value it provides, and the scenarios it supports.
-->

## Übersicht
Bisher drehten sich Datenintegrationsprojekte um das Erstellen von ETL-Prozessen (Extrahieren, Transformieren und Laden), die Daten aus unterschiedlichen Datenquellen innerhalb einer Organisation extrahieren, die Daten so transformieren, dass sie dem Zielschema eines Enterprise Data Warehouse (EDW) entsprechen, und die Daten in ein EDW laden. Auf das EDW wird dann als einzige zuverlässige Informationsquelle für BI-Lösungen zugegriffen.

![Herkömmliches ETL][image-data-factory-introduction-traditional-ETL]

Die heutige Datenlandschaft für Unternehmen wächst im Hinblick auf Volumen, Vielfältigkeit und Komplexität weiterhin exponentiell. Sie ist aufgrund lokaler und cloudbasierter Daten mit unterschiedlichen Formaten und Geschwindigkeiten vielfältiger als je zuvor. Die Datenverarbeitung muss über geografische Standorte hinweg erfolgen und umfasst eine Kombination aus Open Source-Software, kommerziellen Lösungen und benutzerdefinierten Verarbeitungsdiensten, die teuer und schwierig zu integrieren und zu verwalten sind. Die erforderliche Flexibilität, um sich an die sich ändernde Big Data-Landschaft von heute anzupassen, ist eine Gelegenheit, um das herkömmliche EDW mit Funktionen zusammenzuführen, die für ein modernes Informationsproduktionssystem erforderlich sind.

![Vielseitige Verarbeitungsumgebung von heute][image-data-factory-introduction-todays-diverse-processing-landspace]

Der Dienst **Azure Data Factory** ist die zusammengesetzte Plattform, um über herkömmliche EDWs und die sich ändernde Datenlandschaft hinweg so zu arbeiten, dass Unternehmen alle Daten nutzen können, die ihnen für datengesteuerte Entscheidungsprozesse zur Verfügung stehen. Der Dienst gibt Unternehmen die Möglichkeit, diese Vielfältigkeit zu nutzen, indem eine Plattform bereitgestellt wird, um Speicherungs-, Verschiebungs- und Verarbeitungsdienste für Daten in Informationsproduktions-Pipelines zu kombinieren und vertrauenswürdige Datenressourcen zu verwalten.

Der Azure Data Factory-Dienst ermöglichtes Folgendes:

- **Bequemes Arbeiten mit unterschiedlichen Datenspeicher- und -verarbeitungssystemen.** Mit dem Data Factory-Dienst können Sie Informationsproduktions-Pipelines erstellen, mit deren Hilfe lokale Daten (z. B. in SQL Server) und Cloud-Datenquellen, wie z. B. Azure SQL-Datenbanken, Azure-Tabellen und BLOBs, verschoben und verarbeitet werden können. 
- **Transformieren von Daten in vertrauenswürdige Informationen.** Der Data Factory-Dienst unterstützt neben wichtigen Verarbeitungsfunktionen wie automatische Verwaltung von Hadoop (HDInsight)-Clustern, Wiederholungen bei vorübergehenden Fehlern, konfigurierbare Zeitüberschreitungsrichtlinien und Warnungen die Hive-, Pig- und C#-Verarbeitung.  
- **Zentrale Überwachung von Datenpipelines.** Der Data Factory-Dienst bietet eine zuverlässige und umfassende Ansicht der Speicherungs-, Verarbeitungs- und Datenverschiebungsdienste. Der Dienst hilft Ihnen, die Integrität der Datenpipeline von A bis Z schnell zu bewerten, Probleme auszumachen und Korrekturmaßnahmen zu ergreifen. Sie können auch die Datenherkunft und die Beziehungen zwischen Ihrer Daten in allen Ihren Quellen visuell nachverfolgen und eine vollständige Verlaufsübersicht von Auftragsausführung, Systemstatus und Abhängigkeiten in einem zentralen Überwachungsdashboard anzeigen.
- **Umfassende Einblicke anhand transformierter Daten** Mit dem Data Factory-Dienst können Sie Datenpipelines erstellen, die vertrauenswürdige Daten erzeugen, die von Business Intelligence- und Analysetools und anderen Anwendungen genutzt werden können.

<!--
Today, to take advantage of the benefits of Data Factory, developers interact directly with individual data pipelines, storage services, and compute services.  As the Data Factory service evolves over time, we will introduce additional storage and processing services, and new mechanisms of grouping compute and storage services and data pipelines together into ‘Hubs’.  We describe Hubs here in our introduction, as this nascent concept appears throughout the service as a precursor for future releases.

An Azure Data Factory Hub is a container for storage and compute services (both referred to as Linked services), as well as for the data pipelines that use and run on those resources. The Hub container allows the Data Factory to be divided into logical or domain specific groupings.  For example, an enterprise may have a “West US Azure Hub” which manages all of the Linked services and pipelines focused in the West US data center, or a “Sales EDW Hub” which manages all the Linked services and pipelines associated with populating and processing data for the Sales EDW.  An important characteristic of Hubs is that a pipeline runs on a single hub. This means that when defining a pipeline, all of the Linked services referenced by tables or activities within that pipeline must have the same Hub name as the pipeline itself.

Hubs will help to encapsulate storage and compute in a way where pipelines can reference only a Hub rather than the specific services and tables it uses. The Hub can then use policies to decide where to run a pipeline. This will have several important impacts. One is that it will provide easier scale-up as more Linked services can be added to a Hub, and pipelines can be load-balanced across these new Linked services. Another is that it will reuse of pipeline definitions on different Hubs.

-->

## Anwendungsmodell
Das folgende Diagramm veranschaulicht das vom Azure Data Factory-Dienst unterstützte Anwendungsmodell.

![Anwendungsmodell][image-data-factory-application-model]

Eine Azure Data Factory umfasst drei Informationsproduktionsphasen:

- **Verbinden und Sammeln**. In dieser Phase werden Daten aus verschiedenen Datenquellen in Datenhubs importiert. Eine Pipeline in einer Data Factory kann eine oder mehrere Aktivitäten aufweisen. Sie verwenden eine oder mehrere **Kopieraktivitäten** in einer Datenpipeline zum Sammeln von Daten aus Quellendatenspeichern in einem Zieldatenspeicher innerhalb eines Datenhubs zur weiteren Verarbeitung. Ein HDInsight-Cluster (Datenverarbeitung) und der zugehörige Azure-Blob-Speicher (Speicherung) bilden zusammen einen HDInsight-Datenhub. Zum Verwenden eines HDInsight-Datenhubs kopieren Sie alle Quelldaten in einen HDInsight zugeordneten Azure-BLOB-Speicher, damit die Daten im HDInsight-Cluster verarbeitet werden können. Eine Pipeline wird auf einer Datenverarbeitungsressource in einem Datenhub ausgeführt, z. B. einem HDInsight-Cluster.      
- **Transformieren und Verfeinern**. In dieser Phase werden die gesammelten Daten verarbeitet. Eine **HDInsight Activity** in einer Pipeline kann beispielsweise im zugehörigen Azure-Blob-Speicher gespeicherte Daten verarbeiten, indem Transformationen mithilfe von Hive-/Pig-Skripts zum Generieren vertrauenswürdiger Informationen erfolgen. Pipelines können (wie in der Abbildung gezeigt) so verkettet werden, dass Ausgabedatasets einer Pipeline als Eingabedatasets für eine andere Pipeline im selben oder einem anderen Datenhub fungieren können.  
- **Veröffentlichen**. In dieser Phase werden die Daten so veröffentlicht, dass sie von BI-Tools, Analysetools und anderen Anwendungen genutzt werden können. Eine "Kopieraktivität" in der Pipeline kann beispielsweise Ausgabedaten aus der Verarbeitung in der Phase "Transformieren und Verfeinern" in einen Datenspeicher (z. B. lokales SQL Server) kopieren, die als Grundlage von Business Intelligence-Lösungen verwendet werden können.   

<!--

Data Factories enable developers to create pipelines which are groups of data movement and/or processing activities that accept one or more input datasets and produce one or more output datasets. Pipelines can be executed once or on a flexible range of schedules (hourly, daily, weekly, etc…). A dataset is a named view of data. The data being described can vary from simple bytes, semi-structured data like CSV files all the way to tables or models.

Pipelines comprised of data movement activities (for example: Copy Activity) are often used to import/export data from all the data sources (databases, files, SaaS services, etc…) used by the organization into a data hub.
 
Once data is in a **hub**, **pipelines** hosted by the compute services of the hub, are used to transform data into a form suitable for consumption (by BI tools, applications, customers, etc.).  
  
Finally, **pipelines** can be chained (as shown in the diagram) such that the output **dataset(s)** of one are the input(s) of another.  This allows complex data flows to be factored into **pipelines** that run within a data hub or span multiple hubs.  Using **pipelines** in this way provides organizations the building blocks to compose the best of breed on-premises, cloud and Software-as-a-Service (SaaS) services all through the lens of a single, easily managed data factory.
-->


##Nächste Schritte
1. [Erste Schritte mit Data Factory][datafactory-getstarted]. Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.
2. [Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial]. In diesem Artikel wird in einer **umfassenden exemplarischen Vorgehensweise** die Implementierung eines **realen Szenarios** mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.

## Siehe auch
- [Data Factory – Terminologie][adf-terminology]. Dieser Artikel bietet eine Einführung in die von Azure Data Factory verwendete Terminologie zum Erstellen von Data Factorys. 
- [Data Factory – Häufig gestellte Fragen][adf-faq]. Dieser Artikel enthält eine Liste der häufig gestellten Fragen und Antworten.
- [Gängige Szenarien für die Verwendung von Azure Data Factory][adf-common-scenarios]. Dieser Artikel beschreibt einige der häufigeren Szenarien für die Verwendung des Azure Data Factory-Diensts. 


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-terminology]: data-factory-terminology.md
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

[image-data-factory-introduction-todays-diverse-processing-landspace]: ./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-application-model]: ./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]: ./media/data-factory-introduction/DataFactoryDataFlow.png



 

<!---HONumber=62-->