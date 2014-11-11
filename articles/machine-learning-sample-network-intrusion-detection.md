<properties title="Azure Machine Learning Sample: Network intrusion detection" pageTitle="Machine Learning Sample: Network intrusion detection | Azure" description="A sample Azure Machine Learning experiment that uses a classification model to determine which network activities are a part of network intrusion." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning-Beispiel: Erkennen von Netzwerkangriffen

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiment - Network Intrusion Detection - Development

## Problembeschreibung

Diese Demo verwendet verschiedene Netzwerkmerkmale, um zu erkennen, welche Netzwerkaktivitäten Teil eines Eindringens bzw. Angriffs sind. Dieses binäre Klassifizierungsproblem ist nicht besonders kompliziert, da die Merkmale recht eindeutig auf Klassen schließen lassen und die Klassen gleichmäßig verteilt sind. Da verschiedene Merkmale eine Bezeichnung bilden, ist es wichtig, alle Teile der Bezeichnung vor dem Trainieren als Klassifikator zu entfernen.

## Daten

1. Die Daten aus dem KDD Cup 1999 enthalten sowohl Trainings- als auch Testdatasets. Die Klassifizierungsgenauigkeit in den Trainingsdatasets ist historisch gesehen höher als bei den Testdatasets, da neue Netzwerkangriffe in den Testdatasets zuerst auftauchen. Das Trainingsdataset enthält ca. 126.000 Zeilen und 43 Spalten, inklusive der Bezeichnungen. Drei Spalten sind Teil der Bezeichnungsinformationen, daher sind 40 Spalten zum Trainieren des Modells verfügbar. Diese 40 Spalten sind hauptsächlich numerisch, mit einigen Zeichenfolgen- und kategorischen Merkmalen. Die Testdaten enthalten ca. 22.500 Proben (mit denselben 43 Spalten wie die Trainingsdaten).
2. Die Daten wurden in einen öffentlichen Blob hochgeladen, um den Zugriff zu erleichtern. In Azure ML können Sie Lesemodule für Daten aus verschiedenen Speicherformen wie z. B. Blobs mit wenigen Klicks einrichten, ohne Code schreiben zu müssen.

## Modell

1. Der erste Schritt im Modell ist die Konditionierung der Daten für die spätere Klassifizierung. Die Spalte "Class" enthält die vorherzusagenden Bezeichnungen. Diese Bezeichnungen sind entweder "normal" (kein Angriff) oder der Name eines Angriffs. Für manche Angriff existieren nicht viele Beispiele in den Trainingsdaten, und die Testdatasets enthalten neue Angriffe. Daher konvertieren wir diese mehrklassigen Bezeichnungen in binäre Klassenbezeichnungen, um das Problem lösen zu können. ML Studio enthält ein integriertes Modul, um diesen Vorverarbeitungsschritt zu vereinfachen. Das Indikatorwert-Modul wird verwendet, um die Klassenbezeichnungen in eine binäre Form zu bringen. Anschließend wird diese binäre Klassenbezeichnungsspalte auszuwählen (unter Ausschluss der Ausgangs-Klassenbezeichnungsspalte).
2. Bei der Entwicklung dieses Experiments sehen wir im Rahmen der Auswahl und Korrelation der Merkmale, dass viele der Merkmale stark mit der Bezeichnung zusammenhängen. Dies ist bei synthetischen Datensets häufig der Fall. Cloud ML bietet diese Visualisierung mit wenigen Klicks an. Wählen Sie dazu die Option "Visualisierung" in der Projektmodulausgabe und klicken Sie auf eine Merkmalüberschrift, um die Visualisierung zu starten. Wählen Sie anschließend die zu vergleichende Klassenbezeichnung in der Dropdownliste aus.
3. Da eine kleine Anzahl von Merkmalen stark mit der Bezeichnung zusammenhängen, testen wir Kombinationen von linearen und nichtlinearen Klassifikatoren mit und ohne Auswahl der Merkmale, um herauszufinden, welches Modell am besten geeignet ist. Für diesen Vergleich verwenden wir das Modul Modell bewerten.

## Ergebnisse

1. Der nichtlineare Klassifikator (Boosted Decision Tree) ist etwas besser geeignet als der lineare Klassifikator (logistische Regression):

![][0]


Nun werden wir einen Vergleich mit einem Boosted Decision Tree mit Auswahl der Merkmale vergleichen. Die Klassifizierungsleistung ist in diesem Fall ähnlich, aber der Boosted Decision Tree über alle Merkmale ist knapp besser und wird für den Bewertungsworkflow verwendet. Diese große Fläche unter der Kurve für diese Klassifizierung ist typisch für dieses Dataset.

![][1]


Removed until this part is fixed 
## Operationalization

  
We wanted to see our model in action, to do this we wanted a request-response service around the model we just learned. Doing this is straightforward and can be achieved in few simple clicks in studio:   

1. First step is to save the learned model (by right clicking on the classifier module output)  
2. Now create a new experiment and search for saved model and drop it in the panel for new experiment  
3. Then we need to replicate the steps we did for pre-process, again we can just select the modules from experiment we need and copy and paste to the new experiment  
4. The last step is to plug in the test data and run the experiment, once the run is finished we can define the input and output points for the service  
5. Again, using one click we can publish the service end points that is deployed right away and can be tested using a friendly user interface - all within studio


[0]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-1.png
[1]: ./media/machine-learning-sample-network-intrusion-detection/network-intrusion-detection-2.png
