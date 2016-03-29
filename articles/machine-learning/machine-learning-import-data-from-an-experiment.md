<properties
	pageTitle="Importieren von Daten in Azure Machine Learning Studio aus einem anderen Experiment | Microsoft Azure"
	description="Speichern von Trainingsdaten in Azure Machine Learning Studio und Verwenden der Daten in einem anderen Experiment."
	keywords="Importieren von Daten,Daten,Datenquellen,Trainingsdaten"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/09/2016"
	ms.author="garye;bradsev" />


# Importieren der Daten aus einem anderen Experiment in Azure Machine Learning Studio

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]


Hin und wieder wird ein Zwischenergebnis aus einem Experiment benötigt, um es als Teil eines anderen Experiments zu verwenden. Dazu speichern Sie das Modul als Dataset:

1. Klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls, die Sie als Dataset speichern möchten.

2. Klicken Sie auf **Save as Dataset**.

3. Wenn Sie dazu aufgefordert werden, geben Sie einen Namen und eine Beschreibung ein, mit denen das Dataset leicht wiederzuerkennen ist.

4. Klicken Sie auf das Häkchen **OK**.

Wenn der Speichervorgang abgeschlossen ist, ist das Dataset für die Verwendung in allen Experimenten in Ihrem Arbeitsbereich verfügbar. Sie finden es in der Liste **Saved Datasets** in der Modulpalette.

<!---HONumber=AcomDC_0316_2016-->