<properties 
	pageTitle="Azure Data Factory - Terminologie" 
	description="Dieser Artikel bietet eine Einführung zum Erstellen von Factorys der Daten mithilfe des Diensts für Azure Data Factory Terminologie" 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

#Azure Data Factory - Terminologie

## Data Factory
Ein **Azure Data Factory** verfügt über eine oder mehrere Pipelines, die Verarbeitung von Daten in verknüpften Datenspeichern (Azure-Speicher, Azure SQL-Datenbank für lokale SQL Server usw.) mithilfe von verknüpften Compute-Dienste, wie z. B. Clusterversionen. Eine Azure Data Factory selbst enthält keine Daten. Die Daten werden vielmehr in den oben genannten Datenspeichern gespeichert.

## Verknüpfter Dienst
Ein **Dienst verknüpften** ist ein Dienst, der mit einer Azure Data-Factory verknüpft ist. Ein verknüpfter Dienst kann Folgendes sein:

- Ein **Datenspeicherung** Service z. B. Azure-Speicher, Azure SQL-Datenbank oder lokalen SQL Server-Datenbank. Ein Datenspeicher ist ein Container für Eingabe-/Ausgabedatasets.    
- Ein **berechnen** Dienst, wie etwa Clusterversionen, Azure Computerlernen und Azure-Batch. Ein Serverdienst verarbeitet die Eingabedaten und erzeugt die Ausgabedaten.  

## Dataset
Ein **DataSet** ist eine benannte Ansicht der Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturieren Daten wie CSV-Dateien bis hin zu relationalen Tabellen oder sogar Modellen reichen. Eine Factory Daten **Tabelle** ist ein Satz von Daten, die ein Schema und ist rechteckig. Nach dem Erstellen eines verknüpften Diensts in einem Datenspeicher, der auf einen Datenspeicher verweist, definieren Sie Datasets, die im Datenspeicher gespeicherte Eingabe-/Ausgabedaten darstellen.


##Pipeline
Ein **Pipeline** in ein Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mit verknüpften Serverdienste. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. Angenommen, ein **Kopie Aktivität** kopiert Daten aus einer Quellspeicher an einen Ziel-Speicher und **HDInsight-Aktivität** ein Azure-HDInsight-Cluster zum Verarbeiten von Daten mit Hive-Abfragen oder Pig-Skripts verwenden. Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen.

Typische Schritte zum Erstellen einer Instanz einer Azure Data Factory sind:

1. Erstellen einer **Daten Factory**.
2. Erstellen einer **Dienst verknüpften** für jede speichern oder -Serverdiensts.
3. Erstellen von Eingabe und Ausgabe **Datasets**.
4. Erstellen einer **Pipeline**. 

##Aktivität
Ein Datenverarbeitungsschritt in einer Pipeline, bei dem aus einem oder mehreren Eingabedatasets ein oder mehrere Ausgabedatasets erstellt werden. Aktivitäten, die in einer Ausführungsumgebung ausgeführt (z. B.: Azure HDInsight-Cluster) und Daten in einem Datenspeicher, der mit der Azure Data Factory verknüpft sind oder verknüpfte Lese-/Schreibzugriff.

Der Azure Data Factory-Dienst unterstützt die folgenden Aktivitäten in einer Pipeline:

- **Kopie Aktivität** kopiert die Daten aus einem Datenspeicher in einem anderen Datenspeicher. Finden Sie unter [Kopieren von Daten mit Azure Data Factory][copy-data-with-adf] ausführliche Informationen über welche Daten der Aktivität Kopie speichert unterstützt. 
- **HDInsight-Aktivität** Daten durch Ausführen von Hive/Pig-Skripts oder MapReduce-Programme auf einem HDInsight-Cluster verarbeitet. Finden Sie unter [Verwendung Pig und Hive Data Factory][use-pig-hive] und [Aufrufen MapReduce-Programmen aus Daten Factory][run-map-reduce] Details. 
- **Azure Machine Learning Bewertung Aktivität** Azure Computerlernen Batch Bewertung API aufruft. Finden Sie unter [Predictive Pipelines erstellen mithilfe von Azure Data Factory und Azure Computerlernen][azure-ml-adf] Details. 
- **Gespeicherte Prozedur für die Aktivität** Ruft eine gespeicherte Prozedur in einer Azure SQL-Datenbank. Finden Sie unter der [gespeicherte Prozedur für die Aktivität][msdn-stored-procedure-activity] auf der MSDN-Bibliothek für Details.   

##Slice
Eine Tabelle in einer Azure Data Factory besteht aus Slices. Die Breite eines Slices wird durch den Zeitplan (stündlich/täglich) bestimmt. Wenn der Zeitplan "Stündlich" ist, wird stündlich ein Slice mit der Start- und Endzeit einer Pipeline erstellt. Ist z. B. wenn die Pipeline Datum-/ Uhrzeit-start 03/03/2015 06:00:00 (6 Uhr) und End Datum-/ Uhrzeit-Datentyp 03-03/2015 09:00:00 (9 Uhr am selben Tag), drei Slices erzeugt, ein Segment für jedes Intervall von 1 Stunde: 6 – 7 Uhr, 7 bis 8 Uhr und 8 und 9 Uhr.

Slices bieten die Möglichkeit, eine Teilmenge der Daten für einen bestimmten Zeitrahmen (z. B.: das Segment, das für die Dauer (Stunde) erzeugt wird: 13:00 Uhr bis 2:00 Uhr).

## Aktivitätsausführung für einen Slice
Die **Ausführen** oder eine Aktivität ausführen, ist eine Verarbeitungseinheit für ein Segment. Im Falle von Wiederholungen, oder wenn Sie Ihren Slice im Falle von Fehlern erneut ausführen, kann es eine oder mehrere Ausführungen für einen Slice geben. Ein Slice wird durch seine Startzeit identifiziert.

##Gateway zur Datenverwaltung
Microsoft **Datenverwaltungsgateway** ist eine Software, die lokalen Datenquellen um cloud-Diensten für den Gebrauch verbindet. Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses beim Azure Data Factory-Portal registrieren, bevor Sie lokale Datenquellen als verknüpfte Dienste hinzufügen.
 
##Datenhub
Ein **Datenhub** ist ein logischer Container für Daten Speicher- und Compute-Dienste. Beispielsweise bildet ein Hadoop-Cluster mit HDFS als Speicherdienst und Hive/Pig als Compute Service (Verarbeitung) einen Datenhub. Auf ähnliche Weise kann ein Enterprise Data Warehouse (EDW) als Daten-Hub modelliert werden (Datenbank als Speicherdienst, gespeicherte Prozeduren und/oder ETL-Tool als Serverdienste). Pipelines werden als Datenspeicher verwendet und werden in einem Datenhub auf Datenverarbeitungsressourcen ausgeführt. Derzeit wird nur ein HDInsight-Hub unterstützt.

Der Datenhub ermöglicht, dass eine Data Factory in logische oder domänenspezifische Gruppierungen aufgeteilt werden kann, z. B. "Azure-Hub USA West", der alle verknüpften Dienste (Datenspeicher und Datenverarbeitungsressourcen) und Pipelines in dem Rechenzentrum "USA West" verwaltet, oder einen "Hub für Vertriebs-EDW", der alle verknüpften Dienste und Pipelines im Zusammenhang mit dem Auffüllen und Verarbeiten von Daten für das Vertriebs-EDW verwaltet.

Ein wichtiges Merkmal eines Hubs besteht darin, dass eine Pipeline auf einem einzelnen Hub ausgeführt wird. Dies bedeutet, dass beim Definieren einer Pipeline alle verknüpften Dienste, auf die von Tabellen oder Aktivitäten innerhalb dieser Pipeline verwiesen wird, den gleichen Hubnamen wie die Pipeline selbst aufweisen müssen. Wenn die "HubName"-Eigenschaft für einen verknüpften Dienst nicht angegeben ist, wird der verknüpfte Dienst im Hub "Standard" platziert.

## Siehe auch

1. [Einführung in Azure Data Factory][adf-intro]. Dieser Artikel bietet einen Überblick über den Azure Data Factory-Dienst, den daraus entstehenden Nutzen sowie die unterstützten Szenarien.
2. [Erste Schritte mit Daten Factory][datafactory-getstarted]. Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.


[Power-Query-Azure-Table]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azuretable-storage-HA104122607.aspx
[Power-Query-Azure-Blob]: http://office.microsoft.com/en-001/excel-help/connect-to-microsoft-azure-blob-storage-HA104113447.aspx
[Power-Query-Azure-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-microsoft-azure-sql-database-HA104019809.aspx
[Power-Query-OnPrem-SQL]: http://office.microsoft.com/en-001/excel-help/connect-to-a-sql-server-database-HA104019808.aspx

[adf-faq]: data-factory-faq.md
[adf-intro]: data-factory-introduction.md
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

<!---HONumber=GIT-SubDir--> 