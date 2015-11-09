<properties 
	pageTitle="Transformationsaktivitäten von Daten | Microsoft Azure" 
	description="Erfahren Sie, wie Sie die Azure Data Factory verwenden können, um Daten zu transformieren und zu analysieren." 
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
	ms.date="10/27/2015" 
	ms.author="spelluru"/>

# Transformation und Analyse mit Azure Data Factory

## Übersicht
Anhand von Transformationsaktivitäten werden in Azure Data Factory Ihre Rohdaten zu Vorhersagen und Erkenntnissen umgewandelt und verarbeitet. Sie werden in einer Compute-Umgebung wie Azure HDInsight-Cluster oder einem Azure Batch ausgeführt. Azure Data Factory unterstützt die folgenden Transformationsaktivitäten, die [Pipelines](data-factory-create-pipelines.md) entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.


Transformationsaktivität | Compute-Umgebung 
----------------------- | --------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop] 
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop] 
[Hadoop Streaming](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop] 
[Machine Learning-Batchausführung](data-factory-azure-ml-batch-execution-activity.md) | Azure-VM 
[Gespeicherte Prozedur](data-factory-stored-proc-activity.md) | Azure SQL |
[Data Lake Analytics U-SQL](data-factory-usql-activity.md) | Azure Data Lake Analytics 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] oder Azure Batch
   

Sie müssen einen verknüpften Dienst für die Compute-Umgebung erstellen und dann den verknüpften Dienst verwenden, wenn Sie eine Transformationsaktivität definieren. Es gibt zwei Typen von Compute-Umgebungen, die von Data Factory unterstützt werden.

1. **Bei Bedarf**: In diesem Fall wird die Compute-Umgebung vollständig von Data Factory verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Benutzer können differenzierte Einstellungen für die bedarfsgesteuerte Compute-Umgebung zur Auftragsausführung, Clusterverwaltung sowie für Bootstrappingaktionen konfigurieren und steuern. 
2. **Eigene verwenden**: In diesem Fall können Sie Ihre eigene Compute-Umgebung (z. B. HDInsight-Cluster) als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird von Ihnen verwaltet und von Data Factory zum Ausführen von Aktivitäten verwendet. 

Unter dem Artikel [Verknüpfte Computedienste](data-factory-compute-linked-services.md) finden Sie Informationen zu verknüpften Computediensten, die von Data Factory unterstützt werden.

## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-data-transformation-activities.md).

<!---HONumber=Nov15_HO1-->