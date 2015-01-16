<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning-Beispiel: Stimmungsanalyse | Azure" description="Azure Machine Learning-Beispielexperiment, das eine Stimmungsklassifzierung verwendet, um Produktbewertungen zu prognostizieren" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />
 

# Azure Machine Learning-Beispiel: Stimmungsanalyse

>[AZURE.HINWEIS]
>Das [Beispielexperiment] und das [Beispieldataset] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldataset]: #sample-dataset


##Problembeschreibung
Sagen Sie die Bewertung einer Produktprüfung vorher. Dies ist ein ordinales Regressionsproblem, das auch als Regressionsproblem und Multiklassen-Klassifizierungsproblem gelöst werden kann.
 
##Daten
Buchbewertungen bei Amazon, erfasst von der Amazon-Website von UPenn-Forschern ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). Das ursprüngliche Dataset umfasst 975.000 Bewertungen mit den Rangordnungen 1, 2, 3, 4, 5. Wir haben das Dataset auf die ersten 100.000 Bewertungen begrenzt, um das Experiment zu beschleunigen. Alle Bewertungen liegen auf Englisch vor. Die Bewertungen wurden im Zeitraum 1997-2007 geschrieben. 
 
##Modell
Wir haben das Funktionshash-Modul verwendet, um englischen Text in Funktionen mit ganzzahligen Werten zu transformieren. Es wurden drei Modelle verglichen:  
 
1. Lineare Regression   
2. Ordinale Regression mit logistischer Zwei-Klassen-Regression als Basisklassifizierer
3. Multiklassen-Klassifizierung mit logistischem Multiklassen-Regressionsklassifizierer
 
##Ergebnisse

Algorithmus                 | Mittlerer absoluter Fehler | Mittlerer quadratischer Fehler
:---------                | :-----------------: | :--------------------:
Ordinale Regression        | 0,82                | 1,41
Lineare Regression         | 1,04                | 1,36
Multiclass-Klassifizierung | 0,85                | 1,57
 
Auf Basis dieser Ergebnisse wurde das ordinale Regressionsmodell gewählt und darauf basierend ein Webdienst erstellt.
 
<!-- Removed until this part is fixed
##API
We have built a web service that takes a plain text review and predicts its rating.
-->


## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperiment - Stimmungs-Klassifizierung - Entwicklung**


## Beispieldataset

Das folgende Beispieldataset, das von diesem Experiment verwendet wird, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###Buchbesprechungen von Amazon
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
