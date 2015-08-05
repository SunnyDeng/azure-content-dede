<properties
	pageTitle="Eine Vorhersagelösung für die Kreditrisikobewertung mit Machine Learning| Microsoft Azure"
	description="Eine ausführliche exemplarische Vorgehensweise zum Erstellen einer Lösung für die Vorhersageanalyse zur Kreditrisikobewertung in Azure Machine Learning Studio."
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
	ms.topic="get-started-article" 
	ms.date="07/10/2015"
	ms.author="garye"/>


# Exemplarische Vorgehensweise: Entwickeln einer Lösung zur Vorhersageanalyse für die Kreditrisikobewertung in Azure Machine Learning

Stellen Sie sich vor, Sie müssen das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag vorhersagen.

Kreditrisikobewertung ist natürlich ein komplexes Problem, wir versuchen jedoch, die Parameter der Fragestellung ein wenig zu vereinfachen. Wir werden dies als Beispiel dafür verwenden, wie Sie Microsoft Azure Machine Learning mit Machine Learning Studio und dem Machine Learning-Webdienst zum Erstellen einer solchen Vorhersageanalyselösung verwenden können.

In dieser ausführlichen Anleitung erstellen Sie ein Vorhersageanalysemodell in Machine Learning Studio und veröffentlichen das Modell anschließend im Machine Learning-API-Dienst. Wir beginnen mit öffentlich verfügbaren Kreditrisikodaten, entwickeln und trainieren ein Vorhersagemodell anhand dieser Daten und veröffentlichen das Modell als Webdienst, den andere Benutzer für die Kreditrisikobewertung verwenden können.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

Öffnen Sie Machine Learning Studio über folgenden Link: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Weitere Informationen zu den ersten Schritten mit Machine Learning Studio finden Sie unter [Microsoft Azure Machine Learning Studio Home](https://studio.azureml.net/).

Um eine Lösung zur Kreditrisikobewertung zu erstellen, müssen wir die folgenden Schritte ausführen:

1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Trainieren und Bewerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Veröffentlichen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

Diese Anleitung basiert auf einer vereinfachten Version des in Machine Learning Studio enthaltenen Artikels [Experiment zur Kreditrisiko-Vorhersage](../machine-learning-sample-credit-risk-prediction.md).
 

<!---HONumber=July15_HO4-->