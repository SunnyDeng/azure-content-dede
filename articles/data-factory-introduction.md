<properties 
	pageTitle="Einführung in Azure Data Factory" 
	description="Erfahren Sie, wie Sie mithilfe des Azure Data Factory-Diensts Datenverarbeitungs-, Datenspeicher- und Datenverschiebungsdienste entwickeln können, um Pipelines zu erstellen, die vertrauenswürdige Informationen erzeugen." 
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
	ms.date="02/13/2015" 
	ms.author="spelluru"/>

# Einführung in den Azure Data Factory-Dienst
Der **Azure Data Factory**-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Datenspeicherung, -verschiebung und Verarbeitungsdiensten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines.  Entwickler können mit Data Factory halbstrukturierte, unstrukturierte und strukturierte Daten aus lokalen und Cloud-Quellen in vertrauenswürdige Informationen umwandeln. Entwickler erstellen datengesteuerte Workflows (Pipelines), die Daten aus ihren lokalen, cloudbasierten und Internetdiensten verknüpfen, zusammenführen und umwandeln und durch einfache JSON-Skripterstellung eine komplexe Datenverarbeitung einrichten. Der Azure Data Factory-Dienst ermöglicht dank umfassender visueller Darstellung über das Azure-Vorschauportal eine Überwachung und Verwaltung dieser Pipelines auf einen Blick. Die von Pipelines produzierten Informationen können mithilfe von BI- und Analysetools und anderen Anwendungen problemlos verarbeitet werden und ermöglichen so zuverlässige geschäftliche Einblicke und Entscheidungen.

Dieser Artikel bietet einen Überblick über den Azure Data Factory-Dienst, den daraus entstehenden Nutzen sowie die unterstützten Szenarien. 

##Übersicht über Azure Data Factory
Bisher drehten sich Datenintegrationsprojekte um das Erstellen von ETL-Prozessen (Extrahieren, Transformieren und Laden), die Daten aus unterschiedlichen Datenquellen innerhalb einer Organisation extrahieren, die Daten so transformieren, dass sie dem Zielschema eines Enterprise Data Warehouse (EDW) entsprechen, und die Daten, wie in der folgenden Abbildung dargestellt, in ein EDW laden. Auf das EDW wird dann als einzige zuverlässige Informationsquelle für BI-Lösungen zugegriffen. 

![Traditional ETL][image-data-factory-introduction-traditional-ETL]

Die heutige Datenlandschaft für Unternehmen wächst im Hinblick auf Volumen, Vielfältigkeit und Komplexität weiterhin exponential, wie in der folgenden Abbildung dargestellt.  Sie ist aufgrund lokaler und cloudbasierter Daten unterschiedlicher Formen und Geschwindigkeiten vielfältiger als je zuvor.  Die Datenverarbeitung muss über geografische Standorte hinweg erfolgen und umfasst eine Kombination aus Open Source-Software, kommerziellen Lösungen und benutzerdefinierten Verarbeitungsdiensten, die teuer und schwierig zu integrieren und zu verwalten sind.  Die erforderliche Flexibilität, um sich an die sich ändernde Landschaft großer Datenmengen von heute anzupassen, ist eine Gelegenheit, um das herkömmliche EDW mit Funktionen zusammenzuführen, die für ein modernes Informationsproduktionssystem erforderlich sind.  Der Azure Data Factory-Dienst ist die zusammengesetzte Plattform, um über herkömmliche EDWs und die sich ändernde Datenlandschaft hinweg so zu arbeiten, dass Unternehmen alle Daten nutzen können, die ihnen für datengesteuerte Entscheidungsprozesse zur Verfügung stehen.

![Todays Diverse Processing Landscape][image-data-factory-introduction-todays-diverse-processing-landspace]


Der **Azure Data Factory**-Dienst gibt Unternehmen die Möglichkeit, diese Vielfältigkeit zu nutzen, indem eine Plattform bereitgestellt wird, um **Datenspeicherung, -verschiebung und Verarbeitungsdienste in Informationsproduktionspipelines zu kombinieren und vertrauenswürdige Datenressourcen zu verwalten**.

Der Azure Data Factory-Dienst ermöglichtes Folgendes:

- **Bequemes Arbeiten mit unterschiedlichen Datenspeicherungs- und Datenverarbeitungssystemen.** 
Dank der Data Factory können Sie Daten dort verwenden, wo sie gespeichert sind, und Dienste für Speicherung, Verarbeitung und Datenverschiebung kombinieren.  Sie können z. B. Informationsproduktionspipelines erstellen, um lokale Daten wie SQL Server zusammen mit Cloud-Daten wie Azure SQL-Datenbank, Blobs und Tabellen zu verarbeiten.  
- **Umwandeln von Daten in vertrauenswürdige Informationen.** 
Für das Kombinieren und Formen komplexer Daten ist häufig mehr als ein Versuch erforderlich, und veränderliche Datenmodelle können teuer und zeitaufwändig sein.  Mit Data Factory können Sie sich auf Transformationsanalysen konzentrieren, während der Dienst die Infrastruktur übernimmt.  Data Factory unterstützt neben wichtigen Verarbeitungsfunktionen wie automatisches Hadoop (HDInsight), Clusterverwaltung, Wiederholungen bei vorübergehenden Fehlern, konfigurierbare Zeitüberschreitungsrichtlinien und Warnungen, Hive, Pig und C#-Verarbeitung.  
- **Zentrale Überwachung von Datenpipelines.** 
Bei einem Portfolio mit unterschiedlichen Daten ist es wichtig, eine zuverlässige und umfassende Ansicht der Speicherungs-, Verarbeitungs- und Datenverschiebungsdienste.  Data Factory hilft Ihnen, die Integrität der End-to-End-Datenpipeline schnell zu bewerten, Probleme zu identifizieren und Korrekturmaßnahmen zu ergreifen. Sie können die Datenherkunft und die Beziehungen zwischen den Daten über alle Quellen hinweg visuell darstellen. Über ein einziges Überwachungs-Dashboard können Sie die vollständigen historischen Informationen der Auftragsausführung, der Systemintegrität und Abhängigkeiten anzeigen.
- **Umfassender Einblick anhand transformierter Daten**
Anpassung an die sich ständig verändernden Fragen, die Ihr Unternehmen beantworten muss, und Bewahrung des Überblicks, wenn die Datenproduktion bereit ist.  Optimieren Sie Ihre Möglichkeit, bessere geschäftliche Einblicke zu erhalten, indem zeitnahe und vertrauenswürdige Informationen bereitgestellt werden. Verwenden Sie Datenpipelines zum Übermitteln transformierter Daten aus der Cloud an lokale Datenquellen wie SQL Server, oder speichern Sie die Daten in Ihren Cloud-Speicherquellen, damit diese von BI- und Analysetools und anderen Anwendungen verwendet werden können.

Um heutzutage Nutzen aus den Vorteilen der Data Factory zu ziehen, interagieren Entwickler direkt mit einzelnen Datenpipelines, Speicherdiensten und Serverdiensten.  Im Zuge der Weiterentwicklung des Data Factory-Diensts wird es zusätzliche Speicher- und Verarbeitungsdienste sowie neue Gruppierungsmechanismen von Server- und Speicherdiensten mit Datenpipelines in so genannten 'Hubs' geben.  In dieser Einführung werden Hubs beschrieben, da dieses neu aufkommende Konzept in dem gesamten Dienst als Vorläufer für künftige Versionen erscheint.

Ein Azure Data Factory-Hub ist ein Container für Speicher- und Serverdienste (die beide als verknüpfte Dienste bezeichnet werden) sowie für die Datenpipelines, die diese Ressourcen verwenden und darauf ausgeführt werden. Der Hub-Container ermöglicht, dass Data Factory in logische oder domänenspezifische Gruppierungen aufgeteilt wird.  Ein Unternehmen verfügt möglicherweise über einen "Azure-Hub USA West", der alle verknüpften Dienste und Pipelines in dem Rechenzentrum "USA West" verwaltet, oder einen "Hub Vertriebs-EDW", der alle verknüpften Dienste und Pipelines im Zusammenhang mit dem Auffüllen und Verarbeiten von Daten für das Vertriebs-EDW verwaltet.  Ein wichtiges Merkmal eines Hubs besteht darin, dass eine Pipeline einem einzelnen Hub ausgeführt wird. Dies bedeutet, dass beim Definieren einer Pipeline alle verknüpften Dienste, auf die von Tabellen oder Aktivitäten innerhalb dieser Pipeline verwiesen wird, den gleichen Hub-Namen wie die Pipeline selbst aufweisen müssen.

Hubs helfen dabei, Speicher-und Serverdienste so zu kapseln, dass Pipelines nur auf einen Hub anstatt auf die spezifischen Dienste und Tabellen, die dieser verwendet, verweisen können. Der Hub kann dann anhand von Richtlinien entscheiden, wo eine Pipeline ausgeführt werden soll. Dies hat einige wichtige Auswirkungen. Eine besteht darin, dass eine einfache zentrale Hochskalierung ermöglicht wird, wenn zusätzliche verknüpfte Dienste zu einem Hub hinzugefügt werden können, und für Pipelines kann über diese neuen verknüpften Dienste ein Lastenausgleich ausgeführt werden. Ein weiterer Vorteil ist, dass Pipeline-Definitionen in anderen Hubs wiederverwendet werden.

## Szenarien
Es gibt eine Vielzahl von Szenarios für Datenpipelines, die von lokalen Systemen über Cloudsysteme und SaaS-Systeme bis hin zur streaming- und batchbasierten Datenproduktion reichen.  In diesem Abschnitt werden einige Beispielszenarios beschrieben, die Azure Data Factory heute unterstützen kann und die als Hub-Szenarios weiter wachsen werden.

###Szenario 1: Datenquellen für den Daten-Hub
![Source the Data Hub][image-data-factory-introduction-secenario1-source-datahub]

Unternehmen verfügen über Daten unterschiedlichen Typs, die sich in unterschiedlichen Quellen befinden.  Der erste Schritt bei der Erstellung eines Informationsproduktionssystems besteht darin, alle erforderlichen Quellen von Daten und Verarbeitung, z. B. SaaS-Dienste, Dateifreigaben, FTP und Webdienste, miteinander zu verbinden und die Daten wie für die weitere Verarbeitung erforderlich zu verschieben.

Ohne Data Factory müssen Unternehmen benutzerdefinierte Datenverschiebungskomponenten erstellen oder benutzerdefinierte Dienste zur Integration dieser Datenquellen und Verarbeitung schreiben.  Dies ist teuer, und es ist schwierig, solche Systeme zu verwalten. Häufig fehlen dabei auch die robusten Überwachungs- und Warnfunktionen sowie die Steuerungen, die ein vollständig verwalteter Dienst bietet.
  
Bei Azure Data Factory werden Datenspeicherungs- und Verarbeitungsdienste in einem Hub-Container gesammelt, der Verarbeitungs- und Speicheraktivitäten erleichtert und optimiert, eine einheitliche Verwaltung des Ressourcenverbrauchs ermöglicht und bei Bedarf Dienste für die Datenverschiebung bereitstellt.

###Szenario 2: Operationalisieren der Informationsproduktion

Operationalisierungsszenarien sind der nächste logische Schritt nach den Datenquellenszenarios. Sobald in einem Hub Daten vorhanden sind, sollten Sie Datenpipelines erstellen und in Betrieb nehmen, um zuverlässig transformierte Daten basierend auf einem verwaltbaren und kontrollierten Zeitplan zu produzieren, damit Produktionsumgebungen mit zuverlässigen Daten versorgt werden können.  Die Datentransformation in der Azure Data Factory erfolgt über Hive, Pig und benutzerdefinierte C#-Verarbeitung, die auf Hadoop (Azure HDInsight) ausgeführt wird.  Diese Transformationen können dazu verwendet werden, Daten zu bereinigen, wichtige Datenfelder zu maskieren und weitere Vorgänge auf vielfältige, komplexe Weise mit den Daten auszuführen.  Normalerweise wird die Operationalisierung mit einer komplexen und schwer zu verwaltenden Infrastruktur und mit benutzerdefinierten Diensten erreicht. Sie bietet hinsichtlich Implementierung, Verwaltung, Skalierung, Problembehandlung und Versionskontrolle für eine derartige Lösung einige Herausforderungen.
  
Mit Data Factory als vollständig verwalteter Dienst können Benutzer diese Pipelines in Betrieb nehmen, indem sie sie mit einem einmaligen oder komplexen wiederkehrenden Zeitplan definieren; die Orchestrierung wird direkt vom Data Factory-Dienst übernommen.  Mit Hubs erfolgen die Clusterverwaltung für sämtliche Daten und die Verarbeitung innerhalb eines Hubs im Namen des Benutzers, daher können sich die Benutzer anstelle der Infrastrukturverwaltung auf Transformationsanalysen konzentrieren.  Azure Data Factory beseitigt die Herausforderungen bei der Arbeit mit fehleranfälligen, benutzerdefinierten Diensten und ermöglicht es Unternehmen, vertrauenswürdige Informationen zuverlässig und reproduzierbar zu erzeugen.


###Szenario 3:  Integrieren der Informationsproduktion in die Datenermittlung
Herkömmliche BI-Ansätze und -Technologien liefern zwar eine "verlässliche Wahrheitsquelle", haben jedoch fast immer eine schwerwiegende Nebenwirkung: einen konstanten Anforderungsrückstand aufgrund eines sorgfältig kontrollierten Prozesses.  Für eine Anpassung an sich schnell änderende Unternehmensfragen ist eine größere Flexibilität auf Seiten der Unternehmen erforderlich, die ihre Informationsproduktionssysteme mit den Informationsnutzungssystemen verbinden müssen.  Azure Data Factory hilft dabei, der Herausforderung der Verbindung dieser Systeme mit optimierten Datenpipelines für die Informationsproduktion und der Herausforderung des Informationsverbrauchs zu begegnen, indem aktuelle vertrauenswürdige Daten in einfach zu nutzenden Formen erstellt werden.
  
Azure Data Factory unterstützt die folgenden Funktionen für eine einfache Nutzung der produzierten Daten:

- Einfaches Verschieben (einmalig oder geplant) der erzeugten Datenbestände in relationale Datamarts für die Nutzung durch vorhandene BI-Tools (Excel, Tableau usw.).
- Nutzung der produzierten Datenbestände durch eine Data Factory, die Power Query in Excel verwendet.

Informationen zur Nutzung von Daten mithilfe von Power Query finden Sie in den folgenden Themen: 

- [Power Query: Herstellen einer Verbindung mit Microsoft Azure-Tabellenspeicher] [Power-Query-Azure-Table]
- [Power Query: Herstellen einer Verbindung mit Microsoft Azure BLOB-Speicher] [Power-Query-Azure-Blob]
- [Power Query: Herstellen einer Verbindung mit Microsoft Azure SQL-Datenbank] [Power-Query-Azure-SQL]
- [Power Query: Herstellen einer Verbindung mit lokalem Microsoft SQL Server][Power-Query-OnPrem-SQL] 

## Anwendungsmodell
Das folgende Diagramm veranschaulicht das von der Azure Data Factory unterstützte Anwendungsmodell.

![Application Model][image-data-factory-application-model]

Eine Azure Data Factory umfasst drei Informationsproduktionsphasen:


- **Verbinden & Sammeln**. In dieser Phase werden Daten aus verschiedenen Datenquellen in Daten-Hubs importiert.
- **Transformieren & Anreichern**. In dieser Phase werden die gesammelten Daten verarbeitet.
- **Veröffentlichen**. In dieser Phase werden die Daten so veröffentlicht, dass sie von BI-Tools, Analysetools und anderen Anwendungen genutzt werden können.

Mithilfe von Data Factorys können Entwickler **Pipelines** erstellen, bei denen es sich um Gruppen von Datenverschiebungs- und/oder Datenverarbeitungs-**Aktivitäten** handelt, die mehrere Eingabe-**Datasets** akzeptieren und ein oder mehrere Ausgabedatasets produzieren.  Pipelines können einfach oder anhand eines flexiblen Zeitplans (stündlich, täglich, wöchentlich usw.) ausgeführt werden.  Ein **Dataset** ist eine benannte Ansicht von Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturierten Daten wie CSV-Dateien bis hin zu **Tabellen** oder **Modellen** reichen.

**Pipelines**, die sich aus Datenverschiebungs**aktivitäten** zusammensetzen (z. B.: Kopieraktivität) werden häufig zum Importieren/Exportieren von Daten aus allen **Datenquellen** (Datenbanken, Dateien, SaaS-Dienste, usw.) verwendet, die von dem Unternehmen in einem **Daten-Hub** verwendet werden.
    
Sobald sich die Daten in einem **Hub** befinden, werden **Pipelines**, die von den Serverdiensten des Hubs gehostet werden, zum Transformieren der Daten in ein für die Nutzung (durch BI-Tools, Anwendungen, Kunden, usw.) geeignetes Format verwendet.  
  
Und **Pipelines** können außerdem so verkettet werden (wie in dem Diagramm dargestellt), dass die Ausgabe-**Datasets** der einen Pipeline als Eingaben für eine andere Pipeline fungieren.  Auf diese Weise können komplexe Datenflüsse in **Pipelines** miteinbezogen werden, die innerhalb eines Daten-Hubs ausgeführt werden oder mehrere Hubs umfassen.  Eine derartige Verwendung von **Pipelines** liefert Unternehmen die Grundbausteine für optimale lokale, Cloud- und SaaS-Dienste (Software-as-a-Service) durch eine einzige leicht zu verwaltende Data Factory. 


##Terminologie
In diesem Abschnitt werden die Begriffe im Zusammenhang mit der Azure Data Factory vorgestellt.

### Data Factory

**Azure Data Factory** ist ein vollständig verwalteter Dienst für das Kombinieren von Datenspeicherung, -verschiebung und Verarbeitungsdiensten in optimierten, skalierbaren und zuverlässigen Datenproduktions-Pipelines.

Der Data Factory-Dienst nutzt, erstellt, verwaltet und veröffentlicht **Datasets**. Ein Azure-Abonnement kann eine oder mehrere Azure Data Factory-Instanzen umfassen. Eine Azure Data Factory kann mit einem oder mehreren Speicherungs- oder Serverdiensten (die als verknüpfte Dienste bezeichnet werden) verknüpft werden.
 
Eine Azure Data Factory kann eine oder mehrere Pipelines aufweisen, die Daten in verknüpften Speichern verarbeiten, indem verknüpfte Serverdienste wie Azure HDInsight verwendet werden. Eine Azure Data Factory enthält nicht die darin enthaltenen Daten. Die Daten werden vielmehr außerhalb der Data Factory in dem vorhandenen Speichersystem des Benutzers gespeichert.


### Verknüpfter Dienst
Ein verknüpfter Dienst ist ein Dienst, der mit einer Azure Data Factory verknüpft ist. Ein verknüpfter Dienst kann Folgendes sein:


- Ein Datenspeicher wie Azure-Speicher, Azure SQL-Datenbank und die lokale SQL Server-Datenbank
- Ein Serverdienst wie z. B. Azure HDInsight.

Ein Datenspeicher ist ein Container für Daten oder Datasets, und Azure HDInsight ist der einzige Serverdienst, der derzeit unterstützt wird. Sie erstellen zuerst einen verknüpften Dienst, um auf einen Datenspeicher zu zeigen und definieren dann Datasets, um die Daten aus diesem Datenspeicher darzustellen. 

### Dataset
Eine benannte Ansicht von Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturieren Daten wie CSV-Dateien bis hin zu relationalen Tabellen oder sogar Modellen reichen. Eine **Tabelle** ist ein Dataset, das über ein Schema verfügt und rechteckig ist.

###Pipeline
Eine **Pipeline** in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten durch Verwendung von verknüpften Serverdiensten. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. Eine Kopieraktivität kopiert beispielsweise Daten von einem Quellspeicher in einen Zielspeicher, und Hive/Pig-Aktivitäten verwenden Azure HDInsight-Cluster, um Daten mithilfe von Hive-Abfragen oder Pig-Skripts zu verarbeiten.

Typische Schritte zum Erstellen einer Instanz einer Azure Data Factory sind:

1. Erstellen einer Data Factory
2. Erstellen eines verknüpften Diensts für jeden Datenspeicher- oder Serverdienst
3. Erstellen von Eingabe- und Ausgabedatasets
4. Erstellen einer Pipeline 

###Aktivität
Eine Datenverarbeitungschritt in einer Pipeline, bei dem aus einem oder mehreren Eingabedatasets ein oder mehr Ausgabedatasets erstellt werden.  Aktivitäten werden in einer Ausführungsumgebung ausgeführt (zum Beispiel: Azure HDInsight-Cluster); Daten werden in einen Datenspeicher gelesen/geschrieben, der mit der Azure Data Factory-Instanz verbunden ist.
 
###Daten-Hub
Ein Azure-Daten-Hub ist ein Container für Datenspeicher- und Serverdienste. Beispielsweise sind ein Hadoop-Cluster mit HDFS als Speicherdienst und Hive/Pig als Serverdienst (Verarbeitung) ein Daten-Hub. Auf ähnliche Weise kann ein Enterprise Data Warehouse (EDW) als Daten-Hub modelliert werden (Datenbank als Speicherdienst, gespeicherte Prozeduren und/oder ETL-Tool als Serverdienste).  Pipelines werden als Datenspeicher verwendet und werden in einem Daten-Hub auf Serverdiensten ausgeführt. Derzeit wird nur ein HDInsight-Hub unterstützt.

Der Daten-Hub ermöglicht, dass eine Data Factory in logische oder domänenspezifische Gruppierungen aufgeteilt werden kann, z. B. "Azure-Hub USA West", der alle verknüpften Dienste (Datenspeicher- und Serverdienst) und Pipelines in dem Rechenzentrum "USA West" verwaltet, oder einen "Hub Vertriebs-EDW", der alle verknüpften Dienste und Pipelines im Zusammenhang mit dem Auffüllen und Verarbeiten von Daten für das Vertriebs-EDW verwaltet.

Ein wichtiges Merkmal eines Hubs besteht darin, dass eine Pipeline auf einem einzelnen Hub ausgeführt wird. Dies bedeutet, dass beim Definieren einer Pipeline alle verknüpften Dienste, auf die von Tabellen oder Aktivitäten innerhalb dieser Pipeline verwiesen wird, den gleichen Hub-Namen wie die Pipeline selbst aufweisen müssen. Wenn die HubName-Eigenschaft für einen verknüpften Dienst nicht angegeben ist, wird der verknüpfte Dienst im Hub "Standard" platziert.

###Slice
Eine Tabelle in einer Azure Data Factory besteht aus Slices auf der Zeitachse. Die Breite eines Slices wird durch den Zeitplan (stündlich/täglich) bestimmt. Wenn der Zeitplan "stündlich" ist, wird stündlich einen Slice mit der Start- und Endzeit einer Pipeline erstellt usw.  

Slices bieten IT-Experten die Möglichkeit, mit einer Teilmenge von Daten für einen bestimmten Zeitrahmen zu arbeiten (z. B. der für die Dauer (Stunde) erzeugte Slice: 13:00 Uhr bis 14:00 Uhr). Sie können auch alle nachgeschalteten Datenslices für einen bestimmten Zeitraum anzeigen und einen Slice im Falle eines Fehlers erneut ausführen.

Die Ausführung ist eine Verarbeitungseinheit für einen Slice. Im Falle von Wiederholungen, oder wenn Sie Ihren Slice im Falle von Fehlern erneut ausführen, kann es eine oder mehrere Ausführungen für einen Slice geben. Ein Slice wird durch seine Startzeit identifiziert. 

###Datenverwaltungsgateway
Das Microsoft-Datenverwaltungsgateway ist eine Software, die lokale Datenquellen mit Cloud-Diensten für die Nutzung verbindet. Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses beim Azure Data Factory-Portal registrieren, bevor Sie lokale Datenquellen als verknüpfte Dienste hinzufügen.


##Nächste Schritte

1. [Erste Schritte mit Data Factory][datafactory-getstarted]. Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.
2. [Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial]. In diesem Artikel wird in einer **umfassenden exemplarischen Vorgehensweise** die Implementierung eines **realen Szenarios** mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.

[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-tutorial]: ../data-factory-tutorial
[datafactory-getstarted]: ../data-factory-get-started/

[image-data-factory-introduction-traditional-ETL]: ./media/data-factory-introduction/TraditionalETL.PNG

[image-data-factory-introduction-todays-diverse-processing-landspace]:./media/data-factory-introduction/TodaysDiverseDataProcessingLandscape.PNG

[image-data-factory-introduction-secenario1-source-datahub]:./media/data-factory-introduction/Scenario1SourceDataHub.png

[image-data-factory-introduction-secenario2-operationalize-infoproduction]:./media/data-factory-introduction/Scenario2-OperationalizeInformationProduction.png

[image-data-factory-application-model]:./media/data-factory-introduction/DataFactoryApplicationModel.png

[image-data-factory-data-flow]:./media/data-factory-introduction/DataFactoryDataFlow.png




<!--HONumber=35.2-->

<!--HONumber=46--> 
