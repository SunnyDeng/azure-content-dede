<properties 
	pageTitle="Azure Data Factory – Terminologie" 
	description="Dieser Artikel bietet eine Einführung in die von Azure Data Factory verwendete Terminologie zum Erstellen von Data Factorys." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

#Azure Data Factory – Terminologie

## Data Factory
Eine **Azure Data Factory** verfügt über eine oder mehrere Pipelines, die Daten in verknüpften Datenspeichern (Azure Storage, Azure SQL-Datenbank, lokale SQL Server-Datenbank usw.) mithilfe verknüpfter Datenverarbeitungsdienste wie Azure HDInsight verarbeiten. Eine Azure Data Factory selbst enthält keine Daten. Die Daten werden vielmehr in den oben genannten Datenspeichern gespeichert.

## Verknüpfter Dienst
Ein **verknüpfter Dienst** ist ein Dienst, der mit einer Azure Data Factory verknüpft ist. Ein verknüpfter Dienst kann Folgendes sein:

- Ein **Datenspeicherdienst** wie Azure Storage, Azure SQL-Datenbank oder eine lokale SQL Server-Datenbank. Ein Datenspeicher ist ein Container für Eingabe-/Ausgabedatasets.    
- Ein **Serverdienst** wie Azure HDInsight, Azure Machine Learning und Azure Batch. Ein Serverdienst verarbeitet die Eingabedaten und erzeugt die Ausgabedaten.  

## Dataset
Ein **Dataset** ist eine benannte Ansicht von Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturieren Daten wie CSV-Dateien bis hin zu relationalen Tabellen oder sogar Modellen reichen. Eine Data Factory-**Tabelle** ist ein Dataset, das ein Schema hat und rechteckig ist. Nach dem Erstellen eines verknüpften Diensts in einem Datenspeicher, der auf einen Datenspeicher verweist, definieren Sie Datasets, die im Datenspeicher gespeicherte Eingabe-/Ausgabedaten darstellen.


##Pipeline
Eine **Pipeline** in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Serverdienste. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. Eine **Kopieraktivität** kopiert beispielsweise Daten aus einem Quellspeicher in einen Zielspeicher, und **HDInsight-Aktivitäten** verwenden einen Azure HDInsight-Cluster, um Daten mithilfe von Hive-Abfragen oder Pig-Skripts zu verarbeiten. Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen.

Typische Schritte zum Erstellen einer Instanz einer Azure Data Factory sind:

1. Erstellen einer **Data Factory**
2. Erstellen eines **verknüpften Diensts** für jeden Datenspeicher- oder Serverdienst
3. Erstellen von **Datasets** für Eingabe und Ausgabe
4. Erstellen einer **Pipeline** 

##Aktivität
Ein Datenverarbeitungsschritt in einer Pipeline, bei dem aus einem oder mehreren Eingabedatasets ein oder mehrere Ausgabedatasets erstellt werden. Aktivitäten erfolgen in einer Ausführungsumgebung (z. B. Azure HDInsight-Cluster). Daten werden in einen Datenspeicher gelesen/geschrieben, der mit der Azure Data Factory verbunden ist.

Der Azure Data Factory-Dienst unterstützt die folgenden Aktivitäten in einer Pipeline:

- Die **Kopieraktivität** kopiert die Daten aus einem Datenspeicher in einen anderen. Unter [Kopieren von Daten mit Azure Data Factory][copy-data-with-adf] finden Sie ausführliche Informationen, welche Datenspeicher die Kopieraktivität unterstützt. 
- Die **HDInsight-Aktivität** verarbeitet Daten durch Anwenden von Hive/Pig-Skripts oder MapReduce-Programmen auf einen HDInsight-Cluster. Unter [Verwenden von Pig und Hive mit Data Factory][use-pig-hive] und [Aufrufen von MapReduce-Programmen aus Data Factory][run-map-reduce] finden Sie weitere Informationen. 
- Die Aktivität **Azure Machine Learning-Stapelbewertung** ruft die Azure Machine Learning-Stapelbewertungs-API auf. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Data Factory und Azure Machine Learning][azure-ml-adf]. 
- Die **Aktivität "Gespeicherte Prozedur"** ruft eine gespeicherte Prozedur in einer Azure SQL-Datenbank auf. Weitere Informationen finden Sie in der MSDN-Bibliothek unter [Aktivität "Gespeicherte Prozedur"][msdn-stored-procedure-activity].   

##Slice
Eine Tabelle in einer Azure Data Factory besteht aus Slices. Die Breite eines Slices wird durch den Zeitplan (stündlich/täglich) bestimmt. Wenn der Zeitplan "Stündlich" ist, wird stündlich ein Slice mit der Start- und Endzeit einer Pipeline erstellt. Beispiel: Wenn der Startzeitpunkt der Pipeline 03.03.2015 06:00:00 Uhr und der Endzeitpunkt 03.03.2015 09:00:00 Uhr am selben Tag ist, werden drei Slices erzeugt, einer für jedes 1-Stunden-Intervall: 6 bis 7 Uhr, 7 bis 8 Uhr und 8 bis 9 Uhr.

Slices bieten die Möglichkeit, mit einer Teilmenge von Daten für einen bestimmten Zeitraum zu arbeiten (z. B. dem Slice, der für die Dauer (Stunde) von 01:00 bis 02:00 Uhr erstellt wird).

## Aktivitätsausführung für einen Slice
Die **Ausführung** bzw. Aktivitätsausführung ist eine Verarbeitungseinheit für einen Slice. Im Falle von Wiederholungen, oder wenn Sie Ihren Slice im Falle von Fehlern erneut ausführen, kann es eine oder mehrere Ausführungen für einen Slice geben. Ein Slice wird durch seine Startzeit identifiziert.

##Gateway zur Datenverwaltung
Das **Datenverwaltungsgateway** ist Microsoft-Software, die lokale Datenquellen für die Nutzung mit Clouddiensten verbindet. Sie müssen mindestens ein Gateway in Ihrer Unternehmensumgebung installiert haben und dieses beim Azure Data Factory-Portal registrieren, bevor Sie lokale Datenquellen als verknüpfte Dienste hinzufügen.
 
##Datenhub
Ein **Datenhub** ist ein Container für Datenspeicher und Serverdienste. Beispielsweise bildet ein Hadoop-Cluster mit HDFS als Speicherdienst und Hive/Pig als Compute Service (Verarbeitung) einen Datenhub. Auf ähnliche Weise kann ein Enterprise Data Warehouse (EDW) als Daten-Hub modelliert werden (Datenbank als Speicherdienst, gespeicherte Prozeduren und/oder ETL-Tool als Serverdienste). Pipelines werden als Datenspeicher verwendet und werden in einem Datenhub auf Datenverarbeitungsressourcen ausgeführt. Derzeit wird nur ein HDInsight-Hub unterstützt.

Der Datenhub ermöglicht, dass eine Data Factory in logische oder domänenspezifische Gruppierungen aufgeteilt werden kann, z. B. "Azure-Hub USA West", der alle verknüpften Dienste (Datenspeicher und Datenverarbeitungsressourcen) und Pipelines in dem Rechenzentrum "USA West" verwaltet, oder einen "Hub für Vertriebs-EDW", der alle verknüpften Dienste und Pipelines im Zusammenhang mit dem Auffüllen und Verarbeiten von Daten für das Vertriebs-EDW verwaltet.

Ein wichtiges Merkmal eines Hubs besteht darin, dass eine Pipeline auf einem einzelnen Hub ausgeführt wird. Dies bedeutet, dass beim Definieren einer Pipeline alle verknüpften Dienste, auf die von Tabellen oder Aktivitäten innerhalb dieser Pipeline verwiesen wird, den gleichen Hubnamen wie die Pipeline selbst aufweisen müssen. Wenn die "HubName"-Eigenschaft für einen verknüpften Dienst nicht angegeben ist, wird der verknüpfte Dienst im Hub "Standard" platziert.

## Siehe auch

1. [Einführung in Azure Data Factory][adf-intro]. Dieser Artikel bietet einen Überblick über den Azure Data Factory-Dienst, den daraus entstehenden Nutzen sowie die unterstützten Szenarien.
2. [Erste Schritte mit Data Factory][datafactory-getstarted]. Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.


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



 

<!---HONumber=July15_HO3-->