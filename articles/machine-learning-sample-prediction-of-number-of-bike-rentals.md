<properties title="Azure Machine Learning Sample: Prediction of the number of bike rentals" pageTitle="Machine Learning Sample: Prediction of bike rentals | Azure" description="A sample Azure Machine Learning experiment to develop a regression model that predicts the number of bike rentals hourly." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Azure Machine Learning-Beispiel: Vorhersage der Anzahl ausgeliehener Fahrräder

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiment - Demand Forecasting of Bikes

## Problembeschreibung
Vorhersage der Anzahl ausgeliehener Fahrräder für jede Stunde eines Tages anhand historischer Verleihdaten, stündlichen Wetterdaten und der Angabe, ob heute ein Feiertag/Wochentag/Wochenende ist. Die Vorhersagen unterscheiden sich von Stunde zu Stunde (am Vormittag
 werden z. B. viele Fahrräder ausgeliehen, und nachts praktisch keine).

## Daten
UCI Bike Rental-Dataset basierend auf tatsächlichen Daten der Firma Capital Bikeshare, die ein Fahrradverleih-Netzwerk in Washington DC betreibt. Dieses Dataset enthält eine Zeile pro Stunde für jeden Tag der Jahre 2011 und 2012, insgesamt 17379 Zeilen. Der Bereich der stündlich verliehenen Fahrräder reicht von 1 bis 977.

## Modell
Wir verwenden die 2011-Daten als Trainingsdaten und die 2012-Daten als Testdaten. Wir vergleichen 4 Sätze von Merkmalen:

1. Wetter + Feiertag + Wochentag + Wochenend-Merkmale für den jeweiligen Tag
2. Anzahl der Fahrräder, die in jeder der vorherigen 12 Stunden verliehen wurden
3. Anzahl der Fahrräder, die in jeder der vorherigen 12 Tage zur gleichen Uhrzeit verliehen wurden
4. Anzahl der Fahrräder, die in jeder der vorherigen 12 Wochen zur gleichen Uhrzeit am gleichen Tag verliehen wurden

Merkmale B erfasst eine sehr zeitnahe Nachfrage nach Fahrrädern. Merkmale C erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten. Merkmale D erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten an bestimmten Wochentagen.

Da die Bezeichnung (Anzahl verliehener Fahrräder) einen tatsächlichen Wert enthält, verwenden wir die Regressionseinstellung. Da die Anzahl der Merkmale eher klein ist (\< 100) und die Merkmale nicht spärlich sind, ist die Entscheidungsgrenze vermutlich nichtlinear. Auf dieser Basis haben wir beschlossen, einen Boosted Decision Tree-Regressionsalgorithmus zu verwenden.

## Ergebnisse

<table border="1">
<tr><th>Merkmale</th><th>Mittlerer absoluter Fehler</th> <th>Wurzel der mittleren quadratischen Abweichung</th> </tr>
<tr style="background-color: #fff"><td>A</td> <td> 89.7</td> <td>124.9 </td> </tr>
<tr style="background-color: #fff"><td>A+B</td><td>51.2 </td> <td>86.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C</td><td> 48.5</td> <td> 83.7 </td></tr>
<tr style="background-color: #fff"><td>A+B+C+D</td><td> 48.8 </td> <td>83.2 </td></tr>
</table>

Die besten Ergebnisse erhalten wir aus den Merkmalen A+B+C und A+B+C+D. Überraschenderweise bieten die Merkmale D keine nennenswerte Verbesserung gegenüber A+B+C.

