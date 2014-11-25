<properties title="Azure Machine Learning Sample: Credit risk prediction" pageTitle="Machine Learning Sample: Credit risk prediction | Azure" description="A sample Azure Machine Learning experiment to develop a binary classification model that predicts if an applicant is a low credit risk or a high credit risk." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning-Beispiel: Kreditrisiko-Vorhersage

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiment - German Credit - Development

*Eine detaillierte exemplarische Vorgehensweise zur Erstellung und Verwendung einer vereinfachten Version dieses Experiments finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning][Entwickeln einer Vorhersagelösung mit Azure Machine Learning].*

Dieses Experiment erstellt eine Vorhersage für das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag. Die Vorhersage ist ein binärer Wert: niedriges Risiko oder hohes Risiko.

Removed until the Training and Scoring parts are fixed
This example is divided into 3 sample experiments:

- Development Experiment – for experimenting with different models
- Training Experiment – to train the one chosen model
- Scoring Experiment – to set up a web service using the trained model



## Datasetbeschreibung

Das Experiment verwendet das UCI Statlog-Dataset (deutsche Kreditkarten), den Sie unter dem folgenden Link finden:
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>.
Wir verwenden die Datei german.data von dieser Website.

Dieses Dataset klassifiziert Personen anhand verschiedener Attribute in hohes und niedriges Kreditrisiko. Jedes Beispiel stellt eine Person dar. Insgesamt existieren 20 numerische und kategorische Merkmale sowie eine binäre Beschriftung (der Wert für das Kreditrisiko). Einträge mit hohem Kreditrisiko haben die Beschriftung = 2, Einträge mit niedrigem Kreditrisiko Beschriftung = 1. Die Kosten für eine Fehlklassifizierung eines niedrigen Kreditrisikos als hoch ist 1, und die Kosten für eine Fehlklassifizierung eines hohen Kreditrisikos als niedrig ist 5.

## Entwicklungsexperiment

Das Originaldataset verwendet ein Format mit Trennung durch Leerzeichen. Wir haben das Dataset in das CSV-Format transformiert und in ML Studio hochgeladen. Die Transformation kann mithilfe von PowerShell ausgeführt werden:

	cat dataset.txt | %{$_ -replace " ",","} | sc german.csv

Oder mit dem sed-Befehl in Unix:

	sed 's/ /,/g' german.data > german.csv

Wir verwenden zunächst den **Metadaten-Editor**, um die Standard-Spaltennamen durch sinnvolle Spaltennamen zu ersetzen, die wir aus der Datasetbeschreibung auf der UCI-Website entnommen haben. Die neuen Spaltennamen werden mit Komma getrennt in das Feld **Neuer Spaltenname** im **Metadaten-Editor** eingetragen.

Anschließend generieren wir Trainings- und Testdatensätze für die Entwicklung des Risikovorhersagemodells. Wir teilen das Originaldataset in Trainings- und Testsätze mit gleicher Größe auf, indem wir das **Teilen**-Modul mit einem **Anteil der ersten Ausgabezeilen zur Eingabe** von 0,5 verwenden.

Da die Kosten für eine Fehlklassifizierung eines Beispiels mit hohem Risiko als niedrig 5 mal höher sind als für eine Fehlklassifizierung in Gegenrichtung, generieren wir ein neues Dataset, das diese Kostenfunktion abbildet. Im neuen Dataset wird jedes Beispiel mit hohem Risiko 5 mal repliziert, und Beispiele mit niedrigem Risiko werden im Original belassen. Die Aufteilung von Trainings- und Testdatasets erfolgt vor dieser Replikation, um zu verhindern, dass Beispiele in beiden Datensätzen enthalten sind.

Diese Replikation wird mit dem folgenden R-Code erreicht, der mithilfe des Moduls **R-Skript ausführen** ausgeführt wird:

	dataset1 <- maml.mapInputPort(1)
	data.set<-dataset1[dataset1[,21]==1,]
	pos<-dataset1[dataset1[,21]==2,]
	for (i in 1:5) data.set<-rbind(data.set,pos)
	maml.mapOutputPort("data.set")

In unserem Experiment vergleichen wir zwei Ansätze für die Modellgenerierung: Training mit dem Originaldataset und Training mit dem replizierten Dataset. In beiden Ansätzen verwenden wir den Testdatensatz mit Replikation, um die Kostenfunktion des Problems zu berücksichtigen. Die folgende Abbildung zeigt ein Beispiel für den vollständigen Workflow mit Aufteilung und Replikation. In diesem Workflow ist die linke Ausgabe des **Teilen**-Moduls ein Trainingsdataset, und die rechte Ausgabe ist ein Testdataset. Beachten Sie, dass das Trainingsdataset anschließend sowohl mit als auch ohne **R-Skript ausführen** verwendet wird, also mit und ohne Replikation.

![Aufteilen von Trainings- und Testdaten][Aufteilen von Trainings- und Testdaten]

Wir prüfen nicht nur den Effekt der Replikation von Beispielen im Trainingsdataset, sondern vergleichen auch die Leistung von zwei Algorithmen: Support Vector Machine (SVM, Stützvektormethode) und Boosted Decision Tree (verstärkter Entscheidungsbaum). Auf diese Weise generieren wir vier verschiedene Modelle:

- SVM, trainiert mit Originaldaten
- SVM, trainiert mit replizierten Daten
- Boosted Decision Tree, trainiert mit Originaldaten
- Boosted Decision Tree, trainiert mit replizierten Daten

Boosted Decision Trees funktionieren hervorragend mit allen Arten von Merkmalen. Da das SVM-Modul jedoch einen linearen Klassifikator generiert, hat das entsprechende generierte Modell den besten Testfehler, wenn alle Merkmale dieselbe Skala verwenden. Um alle Merkmale auf dieselbe Skala zu konvertieren, verwenden wir das Modul **Daten durch Skalierung transformieren** mit einer tanh-Transformation. Diese Transformation transformiert alle numerischen Merkmale in den Bereich [0,1]. Beachten Sie, dass Zeichenfolgenmerkmale vom SVM-Modul in kategorische Merkmale und anschließend in binäre 0/1-Merkmale konvertiert werden. Daher müssen wir Zeichenfolgenmerkmale nicht manuell transformieren.

Wir initialisieren den Lernalgorithmus mit dem **Zweiklassigen Support Vector Machine**-Modul oder dem **Zweiklassigen Boosted Decision Tree**-Modul und verwenden anschließend das **Modell trainieren**-Modul, um das eigentliche Modell zu erstellen. Diese Modelle werden von **Modell bewerten**-Modellen verwendet, um Bewertungen von Testbeispielen zu erstellen. Hier sehen Sie einen Beispielworkflow, der diese beiden Module kombiniert und SVM und den replizierten Trainingsdatensatz verwendet. Beachten Sie, dass **Modell trainieren** mit dem Trainingsdatensatz verbunden ist, und **Modell bewerten** mit dem Testdataset.

![Trainieren und Bewerten von Modellen][Trainieren und Bewerten von Modellen]

In der Auswertungsphase des Experiments berechnen wir die Genauigkeit der obigen 4 Modelle. Dazu verwenden wir das Modul **Modell bewerten**. Beachten Sie, dass dieses Modul die Genauigkeit nur berechnet, wenn alle Beispiele die gleichen Kosten für Fehlklassifizierungen haben. Da wir jedoch zuvor die positiven Beispiele repliziert haben, berücksichtigt die vom Modul **Modell bewerten** berechnete Genauigkeit die Kosten und ist

![Genauigkeitsberechnung][Genauigkeitsberechnung]

wobei *n+* und *n-* die Nummern der positiven und negativen Beispiele im Originaldataset sind, und *e+* und *e-* sind die Nummern der falsch klassifizierten positiven und negativen Beispiele im Originaldataset.

Das Modul **Modell bewerten** vergleicht zwei Bewertungsmodelle. Daher können wir ein **Modell bewerten**-Modul verwenden, um die zwei SVM-Modelle zu vergleichen, und ein weiteres zum Vergleichen der beiden Boosted Decision Tree-Modelle. Wir kombinieren diese Vergleiche in eine Tabelle, um alle 4 Ergebnisse anzuzeigen. **Modell bewerten** produziert eine Tabelle mit einer einzigen Zeile, die verschiedene Metriken enthält. Mit dem Modul **Zeilen hinzufügen** können wir die Ergebnisse in einer einzigen Tabelle kombinieren. Anschließend verwenden wir ein R-Skript im Modul **R-Skript ausführen**, um die Tabelle mit den Genauigkeiten der 4 Module zu beschriften. Dabei geben wir die Namen der Zeilen in der Ergebnistabelle manuell ein. Zuletzt verwenden wir das Modul **Projektspalten**, um Spalten mit nicht relevanten Metriken zu entfernen.

Das Endergebnis des Experiments, erhalten per Rechtsklick auf die **Ergebnisdataset**-Ausgabe von **Projektspalten** ist:

![Ergebnisse][Ergebnisse]

Hierbei ist die erste Spalte der Name des Machine Learning-Algorithmus, der bei der Modellgenerierung verwendet wurde. Die zweite Spalte gibt den Typ des Trainingsdatensatzes an, und die dritte Spalte enthält die Genauigkeit unter Berücksichtigung der Kosten. In diesem Experiment liefert das SVM-Modell mit dem replizierten Trainingsdataset die beste Genauigkeit.

<!-- Removed until the Training and Scoring parts are fixed 
## Training Experiment  

The sample training experiment is a simplified version of the larger experiment using just the chosen SVM training model.   

Notice that unlike the development experiment, in the training experiment we chose to load the dataset from Azure blob storage using the **Reader** module. Having the dataset stored in Azure is very common when it is generated by other programs. By reading the dataset directly from Azure we skip the step of manually uploading the dataset into ML Studio. The parameters of the **Reader** module are shown below. In this example, the storage account name is “datascience” and the dataset file “german.csv” is placed in container “sampleexperiments”. The account key is an access key of an Azure storage account. This key can be retrieved from your account in the Azure management portal.  ![Azure storage parameters][screen3]   ##Scoring Experiment  The purpose of the sample scoring experiment is to set up a REST API web service that will score test examples. The trained model in this experiment (“Credit Risk model”) was created from the training experiment by right-clicking the Train Model module and selecting **Save as Trained Model**. In this scoring experiment we load test examples, normalize them, and perform scoring using this saved trained model.   

After running this experiment and verifying that it generates the right scores we prepare to publish it as a web service by defining the service input and output. We define the web service input as the input port to the **Transform Data By Scaling** module by right-clicking the port and selecting **Set as Publish Input**. The web service output is set to the output of the **Score Model** module by right-clicking the output of **Score Model** and selecting **Set as Publish Output**.   After setting up the service input and output we need to rerun the experiment and then click **Publish Web Service**. This publishes the web service to the staging environment and takes us to the ML Studio **WEB SERVICES** page. Here we can configure and test the service with sample data.  When the service is ready to go live, go to the **CONFIGURATION** tab on the **WEB SERVICES** page and click **READY FOR PRODUCTION?**. A request will be sent to the IT administrator for Machine Learning who can promote the service to the production environment.  ![Web service ready for production][screen4]  -->

[Entwickeln einer Vorhersagelösung mit Azure Machine Learning]: http://azure.microsoft.com/de-de/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/
[Aufteilen von Trainings- und Testdaten]: ./media/machine-learning-sample-credit-risk-prediction/screen1.jpg
[Trainieren und Bewerten von Modellen]: ./media/machine-learning-sample-credit-risk-prediction/screen2.jpg
[Genauigkeitsberechnung]: ./media/machine-learning-sample-credit-risk-prediction/formula.jpg
[Ergebnisse]: ./media/machine-learning-sample-credit-risk-prediction/results.jpg
