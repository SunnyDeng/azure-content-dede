<properties title="Azure Machine Learning Sample: Sentiment analysis" pageTitle="Machine Learning Sample: Sentiment analysis | Azure" description="A sample Azure Machine Learning experiment that uses sentiment classification to predict product reviews." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning-Beispiel: Stimmungsanalyse

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiment - Sentiment Classification - Development

## Problembeschreibung
Sagen Sie die Bewertung einer Produktprüfung vorher. Die Bewertungen umfassen die Werte 1,2,3,4,5. Dies ist ein ordinales Regressionsproblem, das auch als Regressionsproblem und Multiklassen-Klassifizierungsproblem gelöst werden kann.

## Daten
Buchbewertungen bei Amazon, erfasst von der Amazon-Website von UPenn-Forschern ([][]<http://www.cs.jhu.edu/~mdredze/datasets/sentiment/></a>). Das ursprüngliche Dataset umfasst 975.000 Bewertungen mit den Rangordnungen 1,2,3,4,5. Wir haben das Dataset auf die ersten 100.000 Bewertungen begrenzt, um das Experiment zu beschleunigen. Alle Bewertungen liegen auf Englisch vor. Die Bewertungen wurden im Zeitraum 1997-2007 geschrieben.

## Modell
Wir haben das Funktionshash-Modul verwendet, um englischen Text in Funktionen mit ganzzahligen Werten zu transformieren. Es wurden drei Modelle verglichen:

1. Lineare Regression
2. Ordinale Regression mit logistischer Zwei-Klassen-Regression als Basisklassifizierer
3. Multiklassen-Klassifizierung mit logistischem Multiklassen-Regressionsklassifizierer

## Ergebnisse

Algorithmus|                         Mittlerer absoluter Fehler|           Wurzel der mittleren quadratischen Abweichung
---------|
Ordinale Regression |             0,82|                                            1,41|
Lineare Regression  |              1,04|                                            1,36|
Multiklassen-Klassifizierung  |    0,85  |                                          1,57

Auf Basis dieser Ergebnisse wurde das ordinale Regressionsmodell gewählt und darauf basierend ein Webdienst erstellt.

<!-- Removed until this part is fixed 
## API 
We have built a web service that takes a plain text review and predicts its rating. 
-->

[]: http://www.cs.jhu.edu/~mdredze/datasets/sentiment/
