<properties 
	pageTitle="Verschieben von Daten in den und aus dem Azure-Blobspeicher | Microsoft Azure" 
	description="Verschieben von Daten in den und aus dem Azure-Blobspeicher" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/01/2015" 
	ms.author="bradsev;sunliangms;sachouks;mohabib" />

# Verschieben von Daten in den und aus dem Azure-Blobspeicher

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Einführung

In diesem Thema werden drei Methoden zum Verschieben von Daten nach und aus Azure-Blobspeicher beschrieben:

- Mit dem Azure-Speicher-Explorer
- Mit dem Befehlszeilenprogramm AzCopy
- Mit dem Azure SDK in Python

Es hängt vom jeweiligen Szenario ab, welche Methode für Sie am besten geeignet ist. Mithilfe der Informationen im Artikel [Szenarien für die Advanced Analytics Process and Technology (ADAPT) in Azure Machine Learning](../machine-learning-data-science-plan-sample-scenarios.md) können Sie die Ressourcen ermitteln, die Sie für verschiedene, im erweiterten Analyseprozess verwendeten Datenwissenschaftsworkflows benötigen.

> [AZURE.NOTE]Eine umfassende Einführung in Azure-Blobspeicher finden Sie unter [Grundlagen zu Azure-Blobspeicher](../storage-dotnet-how-to-use-blobs.md) und [Azure-Blobdienst](https://msdn.microsoft.com/library/azure/dd179376.aspx).

> [AZURE.TIP]Alternativ dazu können Sie [Azure Data Factory](https://azure.microsoft.com/de-DE/services/data-factory/) zum Erstellen und Planen einer Pipeline verwenden, die Daten aus dem Azure-Blobspeicher herunterlädt, an einen veröffentlichten Azure Machine Learning-Webdienst übergibt, die Predictive Analytics-Ergebnisse empfängt und diese in den Speicher hochlädt. Weitere Informationen finden Sie unter [Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning](../data-factory/data-factory-create-predictive-pipelines.md).

## Voraussetzungen

In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Abonnement, ein Speicherkonto und den zugehörigen Speicherschlüssel für dieses Konto verfügen. Bevor Sie Daten hoch- und herunterladen können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen.

- Informationen zum Einrichten eines Azure-Abonnements finden Sie unter [Kostenlose 1-Monat-Testversion](https://azure.microsoft.com/de-DE/pricing/free-trial/).
- Anleitungen zum Erstellen eines Speicherkontos und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md).




 

<!---HONumber=Oct15_HO1-->