<properties 
	pageTitle="Erstellen eines einfachen Experiments Machine Learning-Studio | Azure" 
	description="So erstellen Sie ein Experiment zum Trainieren und Testen eines einfachen Modells in Azure Machine Learning Studio" 
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
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="garye"/>

#Erstellen eines einfachen Experiments im Azure Machine Learning-Studio 
 
Ein Vorhersageanalytik-Experiment besteht im Kern aus Komponenten zum *Erstellen*, *Trainieren* sowie *Bewerten* und *Testen* eines Modells. Mit einer Kombination dieser Komponenten können Sie ein Experiment erstellen, das Daten annimmt, ein Modell mit den Daten trainiert und das Modell anschließend auf neue Daten anwendet. Sie könnten auch Module verwenden, die Daten vorverarbeiten und Funktionen auswählen, Daten in Trainings- und Testsätze aufteilen und die Qualität Ihres Modells bewerten oder per Kreuzvalidierung testen.  

In diesem Artikel verwenden wir Azure Machine Learning Studio, um ein einfaches Vorhersageanalytik-Experiment zu entwickeln und zu durchlaufen. Um Machine Learning Studio zu öffnen, klicken Sie auf diesen Link: [https://studio.azureml.net/Home](https://studio.azureml.net/Home). Weitere Informationen zu den ersten Schritten mit Machine Learning Studio finden Sie unter [Microsoft Azure Machine Learning Studio Home](https://studio.azureml.net/). 
 

##Fünf Schritte zum Erstellen von Hypothesen 

Mit den folgenden fünf grundlegenden Schritten zur Experimenterstellung in Machine Learning Studio können Sie Ihr Modell erstellen, trainieren und bewerten:  

- Modellerstellung 
	- [Schritt 1: Datensammlung]
	- [Schritt 2: Datenvorverarbeitung]
	- [Schritt 3: Definition von Funktionen]
- Modelltraining 
	- [Schritt 4: Auswählen und Anwenden eines Lernalgorithmus]
- Modellbewertung und -Tests 
	- [Schritt 5: Vorhersagen neuer Daten] 

[Schritt 1: Datensammlung]: #step-1-get-data
[Schritt 2: Datenvorverarbeitung]: #step-2-preprocess-data
[Schritt 3: Definition von Funktionen]: #step-3-define-features
[Schritt 4: Auswählen und Anwenden eines Lernalgorithmus]: #step-4-choose-and-apply-a-learning-algorithm
[Schritt 5: Vorhersagen neuer Daten]: #step-5-predict-over-new-data 

In diesem Beispiel besprechen wir die Erstellung eines Regressionsmodells mit Beispieldaten aus dem Automobilbereich. Das Modell soll den Preis von Autos anhand verschiedener Variablen wie z. B. Marke und technischen Daten vorhersagen. 

### Schritt 1: Datensammlung

Machine Learning Studio enthält bereits zahlreiche Beispiel-DataSets, und Sie können Daten aus vielen Quellen importieren. Für dieses Beispiel verwenden wir das mitgelieferte Beispiel-DataSet **Automobile price data (Raw)**, das Preisdaten für Autos enthält.

1. Starten Sie ein neues Experiment, indem Sie Machine Learning Studio am unteren Fensterrand in auf **+ NEW** klicken, **EXPERIMENT** auswählen und dann "Blank Experiment" auswählen. Wählen Sie den Standardnamen am oberen Rand des Bereichs aus, und geben Sie einen aussagekräftigeren Namen ein, z. B. **Automobile price prediction**.

2. Links vom Experimentbereich finden Sie eine Palette mit DataSets und Modulen. Geben Sie **automobile** in das Suchfeld im oberen Bereich dieser Palette ein, um das DataSet mit dem Namen **Automobile price data (Raw)** zu finden. 

	![Palette search][screen1a]

3. Ziehen Sie den Datensatz in den Experimentbereich. 

	![Dataset][screen1]

Sie können auf den Ausgabeport im unteren Bereich des Automobil-DataSets klicken und **Visualize** auswählen, um die enthaltenen Daten anzuzeigen. Die Variablen im Datensatz werden als Spalten angezeigt, und jede Instanz eines Automobils füllt eine Zeile. Die Spalte ganz rechts (Spalte 26 mit dem Titel "price") ist die Zielvariable, die wir vorhersagen möchten. 

![Dataset visualization][screen1b]

Schließen Sie das Visualisierungsfenster, indem Sie auf das "**X**" in der oberen rechten Ecke klicken.

### Schritt 2: Datenvorverarbeitung

DataSets müssen vor der Analyse normalerweise vorverarbeitet werden. Möglicherweise sind Ihnen bereits die fehlenden Werte in den Spalten verschiedener Zeilen aufgefallen. Um die Daten zu analysieren, müssen diese fehlenden Werte bereinigt werden. In unserem Fall entfernen wir alle Zeilen, in denen Werte fehlen. Außerdem enthält die Spalte **normalized-losses** viele fehlende Werte, daher schließen wir diese Spalte komplett aus dem Modell aus. 

> [AZURE.TIP] Die Bereinigung fehlender Werte aus den Eingabedaten ist eine Voraussetzung für die Verwendung der meisten Module. 

Wir entfernen zunächst die Spalte **normalized-losses** und anschließend alle Zeilen, in denen Daten fehlen. 

1. Geben Sie **project columns** in das Suchfeld im oberen Bereich der Modulpalette ein, um das Modul **Project Columns** zu suchen, ziehen Sie dieses in den Experimentbereich, und verbinden Sie es mit dem Ausgangsport des DataSets **Automobile price data (Raw)**. Mit diesem Modul können wir auswählen, welche Daten wir in unserem Modell ein- bzw. ausschließen möchten. 

2. Wählen Sie das Modul **Project Columns** aus, und klicken Sie im Bereich "Eigenschaften" auf **Launch column selector**. 

	- Stellen Sie sicher, dass in der Dropdownliste **Begin With** der Eintrag **All columns** ausgewählt ist. Damit wird **Project Columns** angewiesen, alle Spalten zu durchlaufen (mit Ausnahme derer, die wir jetzt ausschließen werden). 
	- Wählen Sie in der nächsten Zeile **Exclude** und **column names** aus, und klicken Sie in das Textfeld. Eine Liste von Spalten wird angezeigt. Wählen Sie **normalized-losses** aus. Die Spalte wird daraufhin dem Textfeld hinzugefügt. 
	- Klicken Sie auf die Schaltfläche mit einem Häkchen ("OK"), um die Spaltenauswahl zu schließen.

    ![Select columns][screen3]
	
	Der Bereich "Properties" für **Project Columns** zeigt an, dass alle Spalten des DataSets mit Ausnahme von **normalized-losses** durchlaufen werden. 

    ![Project Columns properties][screen4]

    > [AZURE.TIP] Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. Doppelklicken Sie auf das Modul **Project Columns**, und geben Sie den Kommentar "Exclude normalized-losses" ein. 

3. Ziehen Sie das Modul **Missing Values Scrubber** in den Experimentbereich, und verbinden Sie es mit dem Modul **Project Columns**. Wählen Sie im Bereich **Properties** die Option **Remove entire row** unter **For missing values** aus, um alle Zeilen zu entfernen, in denen Werte fehlen. Doppelklicken Sie auf das Modul, und geben Sie den Kommentar "Remove missing value rows" ein.

	![Missing Values Scrubber properties][screen4a]

4. Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf **RUN** klicken.

Nach Abschluss des Experiments sind alle Module mit einem grünen Häkchen markiert, um anzuzeigen, dass diese erfolgreich abgeschlossen wurden. Beachten Sie auch den Status **Finished running** in der oberen rechten Ecke.

![First experiment run][screen5]

Bislang haben wir im Experiment nur Daten bereinigt. Um das bereinigte DataSet anzuzeigen, klicken Sie auf den Ausgabeport des Moduls **Missing Values Scrubber**, und wählen Sie **Visualize** aus. Beachten Sie, dass die Spalte **normalized-losses** nicht mehr enthalten ist und keine leeren Felder mehr existieren.

Nach der Bereinigung der Daten können wir nun angeben, welche Funktionen wir im Vorhersagemodell verwenden möchten.

### Schritt 3: Definition von Funktionen

*Funktionen* im Bereich des maschinellen Lernens sind einzeln messbare Eigenschaften von Dingen, an denen Sie interessiert sind. In unserem DataSet stellt jede Reihe ein Auto dar, und jede Spalte ist eine Funktion dieses Autos. Einen guten Satz von Funktionen für die Erstellung eines Vorhersagemodells finden Sie durch Ausprobieren und Kenntnisse des zu lösenden Problems. Manche Funktionen eignen sich besser für die Vorhersage des Ziels als andere. Außerdem haben manche Funktionen eine starke Korrelation mit anderen Funktionen, z. B. "city-mpg" und "highway-mpg". Diese liefern kaum neue Informationen für das Modell und können entfernt werden.

Wir werden ein Modell erstellen, das eine Teilmenge der Funktionen in unserem Datensatz verwendet. Sie können später jederzeit andere Funktionen auswählen, das Experiment erneut ausführen und versuchen, bessere Ergebnisse zu erhalten. Als ersten Versuch wählen wir die folgenden Funktionen (Spalten) mit dem Modul **Project Columns** aus. Beachten Sie, dass wir zum Trainieren des Modells den Wert *price* einbeziehen müssen, den wir vorhersagen möchten.

	make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price

1. Ziehen Sie ein weiteres Modul **Project Columns** in den Experimentbereich und verbinden Sie es mit dem Modul **Missing Values Scrubber**. Doppelklicken Sie auf das Modul, und geben Sie "Select features for prediction" ein.

2. Klicken Sie im Bereich **Properties** auf **Launch column selector**. 

3. Wählen Sie in der Spaltenauswahl unter **Begin With** die Option **No columns** aus, und wählen Sie in der Filterzeile **Include** und **column names** aus. Geben Sie unsere Liste der Spaltennamen ein. Damit weisen Sie das Modul an, nur die angegebenen Spalten zu durchlaufen.

	> [AZURE.TIP] Da wir das Experiment bereits ausgeführt haben, haben die Spaltendefinitionen unserer Daten vom Original-DataSet das Modul **Missing Values Scrubber** durchlaufen. Wenn Sie **Project Columns** mit **Missing Values Scrubber** verbinden, erhält das Modul **Project Columns** Kenntnis von den Spaltendefinitionen in unseren Daten. Wenn Sie in das Feld **column names** klicken, wird eine Liste der Spalten angezeigt und Sie können die Spalten, die Sie der Liste hinzufügen möchten, einzeln auswählen. 

4. Klicken Sie auf das Häkchen ("OK").

![Select columns][screen6]

Dieser Vorgang erzeugt das DataSet, das wir in den nächsten Schritten im Lernalgorithmus verwenden werden. Sie können den Vorgang später jederzeit mit einer anderen Auswahl an Funktionen wiederholen. 

### Schritt 4: Auswählen und Anwenden eines Lernalgorithmus

Nun, da die Daten bereit sind, umfasst das Erstellen eines Vorhersagemodells noch das Trainieren und das Testen. *Klassifizierung* und *Regression* sind zwei Arten von überwachten Techniken für maschinelles Lernen. Bei der Klassifizierung werden Vorhersagen anhand eines definierten Satzes von Werten gemacht, wie z. B. Farben (rot, grün, blau). Bei der Regression werden Vorhersagen anhand eines veränderlichen Satzes von Werten gemacht, wie z. B. das Alter einer Person.

Wir möchten den Preis eines Autos vorhersagen, der beliebige Werte annehmen kann, daher verwenden wir ein Regressionsmodell. Für dieses Beispiel trainieren wir ein einfaches Modell der *linearen Regression* und testen es anschließend im nächsten Schritt. 

1. Teilen Sie die Daten in Trainings- und Testsätze auf. Ziehen Sie das Modul **Split** in den Experimentbereich, und verbinden Sie es mit der Ausgabe des letzten Moduls **Project Columns**. Legen Sie für **Fraction of rows in the first output dataset** den Wert "0,75" fest. Mit dieser Einstellung verwenden wir 75 % der Daten zum Trainieren des Modells und halten 25 % für Tests zurück.

	> [AZURE.TIP] Sie können den Parameter **Random seed** ändern, um unterschiedliche zufällige Proben für Training und Tests zu erstellen. Dieser Parameter steuert den Ausgangswert des Pseudo-Zufallszahlengenerators.
	
2. Führen Sie das Experiment aus. Dabei können die Module **Project Columns** und **Split** die Spaltendefinitionen an die Module übergeben, die wir anschließend hinzufügen werden.  

3. Um den Lernalgorithmus auszuwählen, erweitern Sie die Kategorie **Machine Learning** in der Modulpalette links vom Experimentbereich, und erweitern Sie anschließend **Initialize Model**. Daraufhin werden verschiedene Kategorien von Modulen angezeigt, die zur Initialisierung eines Lernalgorithmus verwendet werden können. 

	Wählen Sie für dieses Experiment das Modul **Linear Regression** unter der Kategorie **Regression** aus (oder geben Sie "linear regression" im Suchfeld der Palette ein), und ziehen Sie das Modul in den Experimentbereich.

4. Ziehen Sie das Modul **Train Model** ebenfalls in den Experimentbereich. Verbinden Sie die Ausgabe des linken Eingangsports mit dem Modul **Linear Regression**. Verbinden Sie den rechten Eingangsport mit dem Trainingsdatenausgang (linker Port) des Moduls **Split**.

5. Führen Sie das Experiment aus, um die Spaltendefinitionen an das Modul **Train Model** zu übergeben.
 
6. Wählen Sie das Modul **Train Model** aus, klicken Sie auf **Launch column selector** im Bereich **Properties**, und wählen Sie die Spalte **price** aus. Dies ist der Wert, den unser Modell vorhersagen wird.

	![Select "price" column][screen7]

7. Führen Sie das Experiment aus. 

Als Ergebnis erhalten Sie ein trainiertes Regressionsmodul, mit dem Sie neue Proben bewerten können, um Vorhersagen zu machen. 

![Applying the learning algorithm][screen8]

### Schritt 5: Vorhersagen neuer Daten 

Wir haben das Modell nun trainiert und können es verwenden, um die restlichen 25 % unserer Daten zu bewerten und zu sehen, wie gut unser Modell funktioniert. 

1. Suchen Sie das Modul **Score Model**, ziehen Sie es in den Experimentbereich, und verbinden Sie den linken Eingangsport mit dem Ausgang des Moduls **Train Model**. Verbinden Sie den rechten Eingangsport mit dem Testdatenausgang (rechter Port) des Moduls **Split**.  

	![Score Model module][screen8a]

2. Um das Experiment auszuführen und die Ausgabe des Moduls **Score Model** anzuzeigen, doppelklicken Sie auf den Ausgabeport, und wählen Sie **Visualize** aus. Die Ausgabe zeigt die vorhergesagten Preiswerte zusammen mit den bekannten Werten aus den Testdaten an.  

3. Um abschließend die Qualität der Ergebnisse zu überprüfen, ziehen Sie das Modul **Evaluate Model** in den Experimentbereich, und verbinden Sie den linken Eingangsport mit dem Ausgang des Moduls **Score Model**. (Es gibt zwei Eingangsports, da das Modul **Evaluate Model** verwendet werden kann, um zwei Modelle zu vergleichen.)
 
4. Um das Experiment auszuführen und die Ausgabe des Moduls **Evaluate Model** anzuzeigen, doppelklicken Sie auf den Ausgabeport, und wählen Sie **Visualize** aus. Die folgenden Statistiken werden für unser Modell angezeigt:

	- **Mean Absolute Error** (MAE): Der Mittelwert der absoluten Fehler (ein *Fehler* ist die Differenz zwischen vorhergesagtem und tatsächlichem Wert).
	- **Root Mean Squared Error** (RMSE): Die Quadratwurzel des Durchschnitts des Quadrats der Vorhersagefehler für das DataSet.
	- **Relative Absolute Error**: Der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
	- **Relative Squared Error**: Der Durchschnitt des Quadrats der Fehler relativ zur quadratischen Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
	- **Coefficient of Determination**: Dieser auch als **R-Quadrat** bekannte Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.
	
	Für jede Fehlerstatistik sind kleinere Werte besser. Ein kleinerer Wert gibt an, dass die Vorhersagen genauer mit den tatsächlichen Werten übereinstimmen. Für **Coefficient of Determination** gilt: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

	![Evaluation results][screen9]

Das fertige Experiment sollte folgendermaßen aussehen:

![Complete experiment][screen10]

### Wie geht es weiter?

Sie haben Ihr Experiment nun eingerichtet und können versuchen, es anhand von Iterationen zu verbessern. Sie können z. B. die Funktionen ändern, die Sie in Ihrer Vorhersage verwenden. Oder Sie können die Eigenschaften des **linearen Regressionsalgorithmus** ändern oder einen völlig anderen Algorithmus ausprobieren. Sie können Ihrem Ereignis sogar mehrere Algorithmen gleichzeitig hinzufügen und jeweils zwei vergleichen, indem Sie das Modul **Evaluate Model** verwenden. 

> [AZURE.TIP] Mit der Schaltfläche **SAVE AS** unterhalb des Experimentbereichs können Sie Kopien der Iterationen Ihres Experiments speichern. Sie können alle Iterationen Ihres Experiments anzeigen, indem Sie unter dem Experimentbereich auf **VIEW RUN HISTORY** klicken. Weitere Informationen finden Sie unter [Verwalten von Experimentiterationen in Azure Machine Learning Studio][runhistory].

[runhistory]: machine-learning-manage-experiment-iterations.md

Wenn Sie mit Ihrem Modell zufrieden sind, können Sie es als Webdienst veröffentlichen, der Automobilpreise anhand neuer Daten vorhersagt. Weitere Informationen finden Sie unter [Veröffentlichen des Azure Machine Learning-Webdiensts][publish].

[publish]: machine-learning-publish-a-machine-learning-web-service.md

Eine umfassendere und ausführlichere exemplarische Vorgehensweisen zum Erstellen, Trainieren, Bewerten und Veröffentlichen eines Vorhersagemodells finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning][walkthrough]. 

[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md

<!-- Images -->
[screen1]:./media/machine-learning-create-experiment/screen1.png
[screen1a]:./media/machine-learning-create-experiment/screen1a.png
[screen1b]:./media/machine-learning-create-experiment/screen1b.png
[screen2]:./media/machine-learning-create-experiment/screen2.png
[screen3]:./media/machine-learning-create-experiment/screen3.png
[screen4]:./media/machine-learning-create-experiment/screen4.png
[screen4a]:./media/machine-learning-create-experiment/screen4a.png
[screen5]:./media/machine-learning-create-experiment/screen5.png
[screen6]:./media/machine-learning-create-experiment/screen6.png
[screen7]:./media/machine-learning-create-experiment/screen7.png
[screen8]:./media/machine-learning-create-experiment/screen8.png
[screen8a]:./media/machine-learning-create-experiment/screen8a.png
[screen9]:./media/machine-learning-create-experiment/screen9.png
[screen10]:./media/machine-learning-create-experiment/screen10.png

<!--HONumber=49--> 