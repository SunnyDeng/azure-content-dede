<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning-Beispiel: Vorhersage für die Fahrradvermietung | Azure" description="Azure Machine Learning-Beispielexperiment zur Entwicklung eines Regressionsmodells, das die Anzahl gemieteter Fahrräder pro Stunde prognostiziert" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning-Beispiel: Vorhersage der Anzahl ausgeliehener Fahrräder

>[AZURE.NOTE]
>Das [Beispielexperiment] und das [Beispieldataset] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldataset]: #sample-dataset


## Problembeschreibung ##
Vorhersage der Anzahl ausgeliehener Fahrräder für jede Stunde eines Tages anhand historischer Verleihdaten, stündlichen Wetterdaten und der Angabe, ob heute ein Feiertag/Wochentag/Wochenende ist. Die Vorhersagen unterscheiden sich für jede Stunde (z. B.
 Es gibt viele Vermietung morgens und fast keine Vermietung in der Nacht). 

## Daten ##
UCI Bike Rental-Dataset basierend auf tatsächlichen Daten der Firma Capital Bikeshare, die ein Fahrradverleih-Netzwerk in Washington DC betreibt. Dieses Dataset enthält eine Zeile pro Stunde für jeden Tag der Jahre 2011 und 2012, insgesamt 17379 Zeilen. Der Bereich der stündlich verliehenen Fahrräder reicht von 1 bis 977.

## Modell ##
Wir verwenden die 2011-Daten als Trainingsdaten und die 2012-Daten als Testdaten. Wir vergleichen 4 Sätze von Merkmalen:

1. Wetter + Feiertag + Wochentag + Wochenend-Merkmale für den jeweiligen Tag
1. Anzahl der Fahrräder, die in jeder der vorherigen 12 Stunden verliehen wurden
1. Anzahl der Fahrräder, die in jeder der vorherigen 12 Tage zur gleichen Uhrzeit verliehen wurden
1. Anzahl der Fahrräder, die in jeder der vorherigen 12 Wochen zur gleichen Uhrzeit am gleichen Tag verliehen wurden

Merkmale B erfasst eine sehr zeitnahe Nachfrage nach Fahrrädern. Merkmale C erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten. Merkmale D erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten an bestimmten Wochentagen.

Da die Bezeichnung (Anzahl verliehener Fahrräder) einen tatsächlichen Wert enthält, verwenden wir die Regressionseinstellung. Da die Anzahl der Merkmale eher klein ist (< 100) und die Merkmale nicht spärlich sind, ist die Entscheidungsgrenze vermutlich nichtlinear. Auf dieser Basis haben wir beschlossen, einen Boosted Decision Tree-Regressionsalgorithmus zu verwenden.

## Ergebnisse ##

<table border="1">
<tr><th>Funktionen</th><th>Mittlerer absoluter Fehler</th> <th>Mittlerer quadratischer Fehler</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89,7</td> <td>124,9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51,2 </td> <td>86,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48,5</td> <td> 83,7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48,8 </td> <td>83,2 </td></tr>
</table>

</table>

Die besten Ergebnisse erhalten wir aus den Merkmalen A+B+C und A+B+C+D. Überraschenderweise bieten die Merkmale D keine nennenswerte Verbesserung gegenüber A+B+C. 

## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperiment - verlangen von Fahrrädern Prognose**


## Beispieldataset

Das folgende Beispieldataset, das von diesem Experiment verwendet wird, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###Fahrradvermietung UCI-Datensatz
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]


