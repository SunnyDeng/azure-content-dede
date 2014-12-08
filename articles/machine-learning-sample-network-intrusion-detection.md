<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning-Beispiel: Erkennen von Netzwerkangriffen | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />



# Azure Machine Learning-Beispiel: Erkennen von Netzwerkangriffen

>[AZURE.HINWEIS]
>Das [Beispielexperiment] und das [Beispieldataset] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldataset]: #sample-dataset


## Problembeschreibung ##

Diese Demo verwendet verschiedene Netzwerkmerkmale, um zu erkennen, welche Netzwerkaktivitäten Teil eines Eindringens bzw. Angriffs sind. Dieses binäre Klassifizierungsproblem ist nicht besonders kompliziert, da die Merkmale recht eindeutig auf Klassen schließen lassen und die Klassen gleichmäßig verteilt sind.  Da verschiedene Merkmale eine Bezeichnung bilden, ist es wichtig, alle Teile der Bezeichnung vor dem Trainieren als Klassifikator zu entfernen. 

## Daten ##

1. Die Daten aus dem KDD Cup 1999 enthalten sowohl Trainings- als auch Testdatasets.  Die Klassifizierungsgenauigkeit in den Trainingsdatasets ist historisch gesehen höher als bei den Testdatasets, da neue Netzwerkangriffe in den Testdatasets zuerst auftauchen.  Das Trainingsdataset enthält ca. 126.000 Zeilen und 43 Spalten, inklusive der Bezeichnungen.  Drei Spalten sind Teil der Bezeichnungsinformationen, daher sind 40 Spalten zum Trainieren des Modells verfügbar.  Diese 40 Spalten sind hauptsächlich numerisch, mit einigen Zeichenfolgen- und kategorischen Merkmalen. Die Testdaten enthalten ca. 22.500 Proben (mit denselben 43 Spalten wie die Trainingsdaten). 
1. Die Daten wurden in einen öffentlichen Blob hochgeladen, um den Zugriff zu erleichtern. In Azure ML können Sie Lesemodule für Daten aus verschiedenen Speicherformen wie z. B. Blobs mit wenigen Klicks einrichten, ohne Code schreiben zu müssen. 

## Modell ##

1. Der erste Schritt im Modell ist die Konditionierung der Daten für die spätere Klassifizierung.  Die Spalte "Klasse" enthält die Bezeichnung Vorhersagen.  Diese Bezeichnungen sind entweder "Normal" (keine Intrusion) oder enthalten den Namen eines Angriffs.   Einige Angriffe haben nicht viele Beispiele in den Trainingsdaten und es gibt neue Angriffe in den Testdatasets. So konvertieren wir diese Multiklassen-Bezeichnungen in Binärklassen-Bezeichnungen, um das Problem realisierbar zu machen.  ML Studio enthält ein integriertes Modul, um diesen Vorverarbeitungsschritt zu vereinfachen. Das Indikatorwert-Modul wird verwendet, um die Klassenbezeichnungen in eine binäre Form zu bringen. Anschließend wird diese binäre Klassenbezeichnungsspalte auszuwählen (unter Ausschluss der Ausgangs-Klassenbezeichnungsspalte).  
1. Bei der Entwicklung dieses Experiments sehen wir im Rahmen der Auswahl und Korrelation der Merkmale, dass viele der Merkmale stark mit der Bezeichnung zusammenhängen. Dies ist bei synthetischen Datensets häufig der Fall.  Cloud ML bietet diese Visualisierung mit wenigen Klicks an. Wählen Sie dazu die Option "Visualisierung" in der Projektmodulausgabe und klicken Sie auf eine Merkmalüberschrift, um die Visualisierung zu starten. Wählen Sie anschließend die zu vergleichende Klassenbezeichnung in der Dropdownliste aus.
1. Da eine kleine Anzahl von Merkmalen stark mit der Bezeichnung zusammenhängen, testen wir Kombinationen von linearen und nichtlinearen Klassifikatoren mit und ohne Auswahl der Merkmale, um herauszufinden, welches Modell am besten geeignet ist.  Für diesen Vergleich verwenden wir das Modul Modell bewerten.    

## Ergebnisse ##

1. Der nichtlineare Klassifikator (Boosted Decision Tree) ist etwas besser geeignet als der lineare Klassifikator (logistische Regression): 

![][1]


Nun werden wir einen Vergleich mit einem Boosted Decision Tree mit Auswahl der Merkmale vergleichen. Die Klassifizierungsleistung ist in diesem Fall ähnlich, aber der Boosted Decision Tree über alle Merkmale ist knapp besser und wird für den Bewertungsworkflow verwendet.  Diese große Fläche unter der Kurve für diese Klassifizierung ist typisch für dieses Dataset. 


![][2]

<!-- Removed until this part is fixed
## Operationalization ##


We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio: 

1. First step is to save the learned model (by right clicking on the classifier module output) 
1. Now create a new experiment and search for saved model and drop it in the panel for new experiment 
1. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment 
1. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service 
1. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio   
-->


## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperiment - Network Intrusion Detection - Entwicklung**


## Beispieldataset

Das folgende Beispieldataset, das von diesem Experiment verwendet wird, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###network_intrusion_detection.csv
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../includes/machine-learning-sample-dataset-network-intrusion.md)]




[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[2]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
