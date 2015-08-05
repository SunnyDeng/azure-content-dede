<properties
	pageTitle="Erstellen eines einfachen Experiments in Machine Learning-Studio | Microsoft Azure"
	description="Ein erstes Lernprogramm für maschinelles Lernen zum Erstellen eines einfachen Experiments zum Trainieren und Testen eines linearen Regressionsmodells in Azure Machine Learning Studio."
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
	ms.topic="hero-article" 
	ms.date="07/09/2015"
	ms.author="garye"/>

#Lernprogramm für maschinelles Lernen: Erstellen Ihres ersten Experiments im Azure Machine Learning Studio

In diesem Lernprogramm für maschinelles Lernen erstellen wir ein Modell für lineare Regression, das den Preis eines Autos anhand verschiedener Variablen wie Hersteller und technischer Angaben prognostiziert. Für dieses Vorgehen verwenden wir Azure Machine Learning Studio, um ein einfaches Vorhersageanalytik-Experiment zu entwickeln und schrittweise zu verfeinern.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Ein Machine Learning Studio-Experiment umfasst das Ziehen und Ablegen von Komponenten auf einer Canvas und das Verbinden dieser Komponenten, um *ein Modell zu erstellen**das Modell zu trainieren* sowie *zu bewerten und zu testen*. Das Experiment verwendet Techniken für Vorhersagemodelle in Form von Machine Learning Studio-Modulen, die Daten erfassen, das Modell anhand dieser Daten trainieren und das Modell auf die neuen Daten anwenden. Sie könnten auch Module verwenden, die Daten vorverarbeiten und Funktionen auswählen, Daten in Trainings- und Testsätze aufteilen und die Qualität Ihres Modells bewerten oder per Kreuzvalidierung testen.

Rufen Sie Machine Learning Studio:[https://studio.azureml.net](https://studio.azureml.net) auf, und klicken Sie auf die Schaltfläche „Erste Schritte“. Wählen Sie entweder den Gastzugang, oder melden Sie sich mit Ihrem Microsoft-Konto an.

Allgemeine Informationen zu Machine Learning Studio finden Sie unter [Was ist Machine Learning Studio?](machine-learning-what-is-ml-studio.md).


##Fünf Schritte zum Erstellen von Hypothesen

In diesem Lernprogramm für maschinelles Lernen führen Sie fünf grundlegende Schritte zur Experimenterstellung in Machine Learning Studio aus, um Ihr Modell zu erstellen, zu trainieren und zu bewerten:

- Modellerstellung
	- [Schritt 1: Bereitstellen von Daten]
	- [Schritt 2: Vorverarbeiten von Daten]
	- [Schritt 3: Definieren von Funktionen]
- Modelltraining
	- [Schritt 4: Auswählen und Anwenden eines Lernalgorithmus]
- Modellbewertung und -Tests
	- [Schritt 5: Erstellen von Preisprognosen für neue Fahrzeuge]

[Schritt 1: Bereitstellen von Daten]: #step-1-get-data
[Schritt 2: Vorverarbeiten von Daten]: #step-2-preprocess-data
[Schritt 3: Definieren von Funktionen]: #step-3-define-features
[Schritt 4: Auswählen und Anwenden eines Lernalgorithmus]: #step-4-choose-and-apply-a-learning-algorithm
[Schritt 5: Erstellen von Preisprognosen für neue Fahrzeuge]: #step-5-predict-new-automobile-prices


## Schritt 1: Bereitstellen von Daten

Machine Learning Studio enthält bereits zahlreiche Beispiel-DataSets, und Sie können Daten aus vielen Quellen importieren. Für dieses Beispiel verwenden wir den mitgelieferten Beispieldatensatz **Automobile price data (Raw)**. Dieses DataSet enthält Einträge für eine Reihe verschiedener Automobile, einschließlich Informationen wie Marke, Modell, technischen Angaben und Preis.

1. Starten Sie ein neues Experiment, indem Sie auf **+NEU** am unteren Rand des Fensters von Machine Learning Studio klicken, **EXPERIMENT** auswählen und dann "Blank Experiment" auswählen. Wählen Sie den Standardnamen am oberen Rand des Bereichs aus, und geben Sie einen aussagekräftigeren Namen ein, z. B. **Automobilpreisvorhersage**.

2. Links vom Experimentbereich finden Sie eine Palette mit Datensätzen und Modulen. Geben Sie **automobile** in das Suchfeld im oberen Bereich dieser Palette ein, um das DataSet mit dem Namen **Automobile price data (Raw)** zu finden.

	![Palettensuche][screen1a]

3. Ziehen Sie den Datensatz in den Experimentbereich.

	![Datensatz][screen1]

Sie können auf den Ausgabeport im unteren Bereich des Automobil-DataSets klicken und **Visualisieren** auswählen, um die enthaltenen Daten anzuzeigen. Die Variablen im Datensatz werden als Spalten angezeigt, und jede Instanz eines Automobils füllt eine Zeile. Die Spalte ganz rechts (Spalte 26 mit dem Titel "price") ist die Zielvariable, die wir vorhersagen möchten.

![Datensatzvisualisierung][screen1b]

Schließen Sie das Visualisierungsfenster, indem Sie auf das "**X**" in der oberen rechten Ecke klicken.

## Schritt 2: Vorverarbeiten von Daten

DataSets müssen vor der Analyse normalerweise vorverarbeitet werden. Möglicherweise sind Ihnen bereits die fehlenden Werte in den Spalten verschiedener Zeilen aufgefallen. Damit das Modell die Daten richtig analysieren kann, müssen diese fehlenden Werte bereinigt werden. In unserem Fall entfernen wir alle Zeilen, in denen Werte fehlen. Außerdem enthält die Spalte **normalisierte Verluste** viele fehlende Werte, daher schließen wir diese Spalte komplett aus dem Modell aus.

> [AZURE.TIP]Die Bereinigung fehlender Werte aus den Eingabedaten ist eine Voraussetzung für die Verwendung der meisten Module.

Wir entfernen zunächst die Spalte **normalized-losses** und anschließend alle Zeilen, in denen Daten fehlen.

1. Geben Sie **project columns** in das Suchfeld im oberen Bereich der Modulpalette ein, um das Modul [Project Columns][project-columns] zu suchen, ziehen Sie dieses in den Experimentbereich, und verbinden Sie es mit dem Ausgangsport des DataSets **Automobile price data (Raw)**. Mit diesem Modul können wir auswählen, welche Daten wir in unserem Modell ein- bzw. ausschließen möchten.

2. Wählen Sie das Modul [Projektspalten][project-columns] aus und klicken Sie auf **Spaltenauswahl starten** im Eigenschaftenpanel.

	- Stellen Sie sicher, dass in der Dropdownliste **Beginnen mit** der Eintrag **Alle Spalten** ausgewählt ist. Damit wird [Project Columns][project-columns] angewiesen, alle Spalten zu durchlaufen (mit Ausnahme derer, die wir jetzt ausschließen werden).
	- Wählen Sie in der nächsten Zeile **Ausschließend** und **Spaltennamen** aus und klicken Sie in das Textfeld. Eine Liste von Spalten wird angezeigt. Wählen Sie **normalized-losses** aus. Die Spalte wird daraufhin dem Textfeld hinzugefügt.
	- Klicken Sie auf die Schaltfläche mit einem Häkchen ("OK"), um die Spaltenauswahl zu schließen.

    ![Spalten auswählen][screen3]

	Das Eigenschaftenpanel für **Project Columns** zeigt an, dass alle Spalten des DataSets mit Ausnahme von **normalized-losses** durchlaufen werden.

    ![Projektspalteneigenschaften][screen4]

    > [AZURE.TIP]Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. Doppelklicken Sie in diesem Fall auf das [Project Columns][project-columns]-Modul, und geben Sie den Kommentar "normalized-losses ausschließen" ein.

3. Ziehen Sie das Modul [Clean Missing Data][clean-missing-data] in den Experimentbereich, und verbinden Sie es mit dem [Project Columns][project-columns]-Modul. Wählen Sie im Panel **Eigenschaften** unter **Bereinigungsmodus** die Option **Gesamte Zeile entfernen** aus, um die Daten zu bereinigen, indem Sie alle Zeilen entfernen, in denen Werte fehlen. Doppelklicken Sie auf das Modul, und geben Sie den Kommentar "Remove missing value rows" ein.

	![Bereinigen fehlender Dateneigenschaften][screen4a]

4. Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf **AUSFÜHREN** klicken.

Nach Abschluss des Experiments sind alle Module mit einem grünen Häkchen markiert, um anzuzeigen, dass diese erfolgreich abgeschlossen wurden. Beachten Sie auch den Status **Finished running** in der oberen rechten Ecke.

![Erste Experimentausführung][screen5]

Bislang haben wir im Experiment nur Daten bereinigt. Wenn Sie das bereinigte DataSet anzeigen möchten, klicken Sie auf den linken Ausgabeports des Moduls [Clean Missing Data][clean-missing-data] ("Cleaned dataset"), und wählen Sie **Visualisieren** aus. Beachten Sie, dass die Spalte **normalized-losses** nicht mehr aufgeführt wird und keine fehlenden Werte auftreten.

Nach der Bereinigung der Daten können wir nun angeben, welche Funktionen wir im Vorhersagemodell verwenden möchten.

## Schritt 3: Definieren von Funktionen

Bei Machine Learning versteht man unter *Funktionen* einzeln messbare Eigenschaften des untersuchten Gesamtobjekts. In unserem DataSet stellt jede Zeile ein Automobil dar, und jede Spalte ist eine Funktion dieses Automobils. Einen guten Satz von Funktionen für die Erstellung eines Vorhersagemodells finden Sie durch Ausprobieren und Kenntnisse des zu lösenden Problems. Manche Funktionen eignen sich besser für die Vorhersage des Ziels als andere. Außerdem haben manche Funktionen eine starke Korrelation mit anderen Funktionen, z. B. "city-mpg" und "highway-mpg". Diese liefern kaum neue Informationen für das Modell und können entfernt werden.

Wir werden ein Modell erstellen, das eine Teilmenge der Funktionen in unserem Datensatz verwendet. Sie können später jederzeit andere Funktionen auswählen, das Experiment erneut ausführen und versuchen, bessere Ergebnisse zu erhalten. Als ersten Versuch wählen wir die folgenden Funktionen (Spalten) mit dem Modul [Projektspalten][project-columns] aus. Beachten Sie, dass wir zum Trainieren des Modells den Wert *price* einbeziehen müssen, den wir vorhersagen möchten.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Ziehen Sie ein weiteres [Project Columns][project-columns]-Modul in den Experimentbereich, und verbinden Sie es mit dem linken Ausgabeport des Moduls [Clean Missing Data][clean-missing-data]. Doppelklicken Sie auf das Modul, und geben Sie "Select features for prediction" ein.

2. Klicken Sie im Bereich **Eigenschaften** auf **Launch column selector**.

3. Wählen Sie in der Spaltenauswahl unter **Begin With** die Option **No columns** aus, und wählen Sie in der Filterzeile **Include** und **column names** aus. Geben Sie unsere Liste der Spaltennamen ein. Damit weisen Sie das Modul an, nur die angegebenen Spalten zu durchlaufen.

	> [AZURE.TIP]Da wir das Experiment bereits ausgeführt haben, haben die Spaltendefinitionen unserer Daten vom Original-DataSet das [Clean Missing Data][clean-missing-data]-Modul durchlaufen. Wenn Sie [Project Columns][project-columns] mit [Clean Missing Data][clean-missing-data] verbinden, erhält das [Project Columns][project-columns]-Modul Kenntnis von den Spaltendefinitionen in unseren Daten. Wenn Sie in das Feld **column names** klicken, wird eine Liste der Spalten angezeigt, und Sie können die Spalten, die Sie der Liste hinzufügen möchten, einzeln auswählen.

4. Klicken Sie auf das Häkchen ("OK").

![Spalten auswählen][screen6]

Dieser Vorgang erzeugt das DataSet, das wir in den nächsten Schritten im Lernalgorithmus verwenden werden. Sie können den Vorgang später jederzeit mit einer anderen Auswahl an Funktionen wiederholen.

## Schritt 4: Auswählen und Anwenden eines Lernalgorithmus

Nachdem die Daten vorbereitet sind, können Sie das Vorhersagemodell anhand von Training und Tests erarbeiten. Wir werden das Modell zunächst mit unseren Daten trainieren und dann testen, wie genau seine Preisvorhersagen zutreffen.

*Klassifizierung* und *Regression* sind zwei Techniken für beaufsichtigtes maschinelles Lernen. Bei der Klassifizierung werden Vorhersagen anhand eines definierten Satzes von Werten gemacht, wie z. B. Farben (rot, grün, blau). Bei der Regression werden Vorhersagen anhand eines veränderlichen Satzes von Werten gemacht, wie z. B. das Alter einer Person.

Wir möchten den Preis eines Autos vorhersagen, der beliebige Werte annehmen kann, daher verwenden wir ein Regressionsmodell. Für dieses Beispiel trainieren wir ein einfaches *lineares Regressionsmodell* und testen es anschließend im nächsten Schritt.

1. Die uns vorliegenden Daten können sowohl zum Trainieren als auch zum Testen verwendet werden, indem wir sie in separate Trainings- und Testsätze aufteilen. Ziehen Sie das Modul [Aufteilen][split] in den Experimentbereich und verbinden Sie es mit der Ausgabe des letzten [Projektspalten][project-columns]-Moduls. Setzen Sie **Anteil der Zeilen im ersten Ausgabedatensatz** auf 0,75. Mit dieser Einstellung verwenden wir 75 % der Daten zum Trainieren des Modells und halten 25 % für Tests zurück.

	> [AZURE.TIP]Sie können den Parameter **Zufälliger Ausgangswert** ändern, um unterschiedliche zufällige Proben für Training und Tests zu erstellen. Dieser Parameter steuert den Ausgangswert des Pseudo-Zufallszahlengenerators.

2. Führen Sie das Experiment aus. Dadurch können die Module [Project Columns][project-columns] und [Split][split] die Spaltendefinitionen an die Module übergeben, die wir als Nächstes hinzufügen werden.

3. Um den Lernalgorithmus auszuwählen, erweitern Sie die Kategorie **Machine Learning** in der Modulpalette links vom Experimentbereich, und erweitern Sie anschließend **Modell initialisieren**. Daraufhin werden verschiedene Kategorien von Modulen angezeigt, die zur Initialisierung eines Algorithmus für maschinelles Lernen verwendet werden können.

	Wählen Sie für dieses Experiment das [Linear Regression][linear-regression]-Modul unter der Kategorie **Regression** aus (oder geben Sie "linear regression" im Suchfeld der Palette ein), und ziehen Sie das Modul in den Experimentbereich.

4. Ziehen Sie das Modul [Modell trainieren][train-model] ebenfalls in den Experimentbereich. Verbinden Sie die Ausgabe des linken Eingangsports mit dem Ausgang des Moduls [Linear Regression][linear-regression]. Verbinden Sie den rechten Eingangsport mit dem Trainingsdatenausgang (linker Port) des [Split][split]-Moduls.

5. Wählen Sie das Modul [Train Model][train-model] aus, klicken Sie im Bereich **Eigenschaften** auf **Spaltenauswahl starten**, und wählen Sie die Spalte**price** aus. Dies ist der Wert, den unser Modell vorhersagen wird.

	![Spaltenauswahl "price"][screen7]

6. Führen Sie das Experiment aus.

Als Ergebnis erhalten Sie ein trainiertes Regressionsmodul, mit dem Sie neue Proben bewerten können, um Vorhersagen zu machen.

![Anwenden des Algorithmus zum maschinellen Lernen][screen8]

## Schritt 5: Erstellen von Preisprognosen für neue Fahrzeuge

Wir haben das Modell nun unter Verwendung von 75 % unserer Daten trainiert und können die restlichen 25 % der Daten dafür aufwenden, zu bewerten, wie gut unser Modell funktioniert.

1. Suchen Sie das Modul [Modell bewerten][score-model], ziehen Sie es in den Experimentbereich, und verbinden Sie den linken Eingangsport mit dem Ausgang des [Train Model][train-model]-Moduls. Verbinden Sie den rechten Eingangsport mit dem Testdatenausgang (rechter Port) des [Split][split]-Moduls.  

	![Modul Modell bewerten][screen8a]

2. Um das Experiment auszuführen und die Ausgabe des [Score Model][score-model]-Moduls anzuzeigen, doppelklicken Sie auf den Ausgabeport, und wählen Sie **Visualisieren** aus. Die Ausgabe zeigt die vorhergesagten Preiswerte zusammen mit den bekannten Werten aus den Testdaten an.

3. Um abschließend die Qualität der Ergebnisse zu überprüfen, ziehen Sie das [Evaluate Model][evaluate-model]-Modul in den Experimentbereich, und verbinden Sie den linken Eingangsport mit dem Ausgang des Modells [Score Model][score-model]. (Es gibt zwei Eingangsports, da das [Evaluate Model][evaluate-model]-Modul verwendet werden kann, um zwei Modelle zu vergleichen.)

4. Führen Sie das Experiment aus.

Um die Ausgabe des [Evaluate Model][evaluate-model]-Moduls anzuzeigen, klicken Sie auf den Ausgabeport, und wählen Sie **Visualisieren** aus. Die folgenden Statistiken werden für unser Modell angezeigt:

- **Mean Absolute Error** (MAE) – der Mittelwert der absoluten Fehler (ein *Fehler* ist die Differenz zwischen vorhergesagtem und tatsächlichem Wert).
- **Root Mean Squared Error** (RMSE) – die Quadratwurzel des Durchschnitts des Quadrats der Vorhersagefehler für das Test-DataSet.
- **Relative Absolute Error** – der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
- **Relative Squared Error** – der Durchschnitt der quadrierten Fehler relativ zur quadrierten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
- **Coefficient of Determination** – dieser auch als **R-Quadrat** bekannte Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.

Für jede Fehlerstatistik sind kleinere Werte besser. Ein kleinerer Wert gibt an, dass die Vorhersagen genauer mit den tatsächlichen Werten übereinstimmen. Für den **Bestimmungskoeffizienten** gilt: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

![Auswertung der Ergebnisse][screen9]

Das fertige Experiment sollte folgendermaßen aussehen:

![Lernprogramm für maschinelles Lernen: Führen Sie ein lineares Regressionsexperiment mit Vorhersagemodell-Techniken aus.][screen10]

## Wie geht es weiter?

Da Sie jetzt ein erstes Lernprogramm zum maschinellen Lernen abgeschlossen und das Experiment eingerichtet haben, können Sie es wiederholen, um das Modell zu verbessern. Sie können z. B. die Funktionen ändern, die Sie in Ihrer Vorhersage verwenden. Oder Sie können die Eigenschaften des [Linearen Regressionsalgorithmus][linear-regression] ändern oder einen völlig anderen Algorithmus ausprobieren. Sie können Ihrem Ereignis sogar mehrere Algorithmen zum maschinellen Lernen gleichzeitig hinzufügen und jeweils zwei vergleichen, indem Sie das [Evaluate Model][evaluate-model]-Modul verwenden.

> [AZURE.TIP]Mit der Schaltfläche **SPEICHERN ALS** unterhalb des Experimentbereichs können Sie Kopien der Iterationen Ihres Experiments speichern. Sie können alle Iterationen Ihres Experiments anzeigen, indem Sie unterhalb des Experimentbereichs auf **AUSFÜHRUNGSVERLAUF ANZEIGEN** klicken. Weitere Informationen finden Sie unter [Verwalten von Experimentiterationen in Azure Machine Learning-Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

Wenn Sie mit Ihrem Modell zufrieden sind, können Sie es als Webdienst veröffentlichen, der Automobilpreise anhand neuer Daten vorhersagt. Weitere Informationen finden Sie unter [Veröffentlichen eines Azure Machine Learning-Webdiensts][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Eine umfassendere und ausführlichere exemplarische Vorgehensweise für Vorhersagemodell-Techniken zum Erstellen, Trainieren, Bewerten und Veröffentlichen eines Modells finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning][walkthrough].

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]: ./media/machine-learning-create-experiment/screen1.png
[screen1a]: ./media/machine-learning-create-experiment/screen1a.png
[screen1b]: ./media/machine-learning-create-experiment/screen1b.png
[screen2]: ./media/machine-learning-create-experiment/screen2.png
[screen3]: ./media/machine-learning-create-experiment/screen3.png
[screen4]: ./media/machine-learning-create-experiment/screen4.png
[screen4a]: ./media/machine-learning-create-experiment/screen4a.png
[screen5]: ./media/machine-learning-create-experiment/screen5.png
[screen6]: ./media/machine-learning-create-experiment/screen6.png
[screen7]: ./media/machine-learning-create-experiment/screen7.png
[screen8]: ./media/machine-learning-create-experiment/screen8.png
[screen8a]: ./media/machine-learning-create-experiment/screen8a.png
[screen9]: ./media/machine-learning-create-experiment/screen9.png
[screen10]: ./media/machine-learning-create-experiment/complete-linear-regression-experiment.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=July15_HO4-->