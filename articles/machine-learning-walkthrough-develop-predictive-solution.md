<properties title="Develop a predictive solution with Azure Machine Learning" pageTitle="Develop a predictive solution with Machine Learning | Azure" description="Walkthrough of how to create a predictive analytics experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Entwickeln einer Vorhersagelösung mit Azure Machine Learning

Stellen Sie sich vor, Sie müssen das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag vorhersagen.

Dies ist natürlich ein komplexes Problem. Wir werden die Parameter der Frage jedoch etwas vereinfachen und als Beispiel dafür verwenden, wie Sie Microsoft Azure Machine Learning mit ML Studio und den ML API-Dienst für die Erstellung einer solchen Vorhersageanalytik-Lösung verwenden können.

In dieser Anleitung erstellen Sie ein Vorhersageanalytik-Modell in ML Studio und veröffentlichen das Modell anschließend im ML API-Dienst. Wir beginnen mit öffentlich verfügbaren Kreditrisikodaten, entwickeln und trainieren ein Vorhersagemodell anhand dieser Daten und veröffentlichen das Modell als Webdienst, den andere Benutzer verwenden können.

Dazu gehören die folgenden Schritte:

1.  [Erstellen eines ML-Arbeitsbereichs][]
2.  [Hochladen vorhandener Daten][]
3.  [Erstellen eines neuen Experiments][]
4.  [Trainieren und Bewerten der Modelle][]
5.  [Veröffentlichen des Webdiensts][]
6.  [Zugreifen auf den Webdienst][]

Diese Anleitung basiert auf einer vereinfachten Version des in ML Studio enthaltenen Artikels
[Experiment zur Kreditrisiko-Vorhersage][].

  [Erstellen eines ML-Arbeitsbereichs]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Hochladen vorhandener Daten]: ../machine-learning-walkthrough-2-upload-data/
  [Erstellen eines neuen Experiments]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Trainieren und Bewerten der Modelle]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Veröffentlichen des Webdiensts]: ../machine-learning-walkthrough-5-publish-web-service/
  [Zugreifen auf den Webdienst]: ../machine-learning-walkthrough-6-access-web-service/
  [Experiment zur Kreditrisiko-Vorhersage]: ../machine-learning-sample-credit-risk-prediction/
