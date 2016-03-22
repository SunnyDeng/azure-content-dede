<properties
	pageTitle="Eine Vorhersagelösung für die Kreditrisikobewertung mit Machine Learning| Microsoft Azure"
	description="Eine ausführliche exemplarische Vorgehensweise zum Erstellen einer Lösung für die Vorhersageanalyse zur Kreditrisikobewertung in Azure Machine Learning Studio."
	keywords="Kreditrisiko, Predictive Analytics-Lösung, Risikobewertung"
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
	ms.date="03/09/2016"
	ms.author="garye"/>


# Exemplarische Vorgehensweise: Entwickeln einer Lösung zur Vorhersageanalyse für die Kreditrisikobewertung in Azure Machine Learning

Stellen Sie sich vor, Sie müssen das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag vorhersagen.

Kreditrisikobewertung ist natürlich ein komplexes Problem, wir versuchen jedoch, die Parameter der Fragestellung ein wenig zu vereinfachen. Wir werden dies als Beispiel dafür verwenden, wie Sie Microsoft Azure Machine Learning mit Machine Learning Studio und dem Machine Learning-Webdienst zum Erstellen einer solchen Vorhersageanalyselösung verwenden können.

In dieser ausführlichen Anleitung erstellen Sie ein Predictive Analytics-Modell in Machine Learning Studio und stellen das Modell anschließend im Machine Learning-API-Dienst bereit. Wir beginnen mit öffentlich verfügbaren Kreditrisikodaten, entwickeln und trainieren ein Vorhersagemodell anhand dieser Daten und stellen das Modell als Webdienst bereit, den andere Benutzer für die Kreditrisikobewertung verwenden können.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

>[AZURE.TIP] Informationen zum Herunterladen und Drucken des Diagramms, mit dem Sie sich einen Überblick über die Machine Learning Studio-Funktionen verschaffen können, finden Sie unter [Übersichtsdiagramm der Azure Machine Learning Studio-Funktionen](machine-learning-studio-overview-diagram.md).

Um eine Lösung zur Kreditrisikobewertung zu erstellen, müssen wir die folgenden Schritte ausführen:

1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Trainieren und Bewerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Bereitstellen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

Diese exemplarische Vorgehensweise basiert auf einer vereinfachten Version des Beispielexperiments [Binäre Klassifizierung: Kreditrisiko-Vorhersage](http://go.microsoft.com/fwlink/?LinkID=525270) im [Cortana Analytics-Katalog](http://gallery.cortanaanalytics.com/).

<!---HONumber=AcomDC_0316_2016-->