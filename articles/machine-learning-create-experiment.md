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
 
Experimente in der Vorhersageanalytik bestehen im Wesentlichen aus Komponenten zum *Erstellen eines Modells*, zum *Trainieren des Modells* und zum *Testen und Bewerten des Modells*. Mit einer Kombination dieser Komponenten können Sie ein Experiment erstellen, das Daten annimmt, ein Modell mit den Daten trainiert und das Modell anschließend auf neue Daten anwendet. Sie könnten auch Module verwenden, die Daten vorverarbeiten und Funktionen auswählen, Daten in Trainings- und Testsätze aufteilen und die Qualität Ihres Modells bewerten oder per Kreuzvalidierung testen.  

In diesem Artikel verwenden wir das Microsoft Azure Machine Learning-Studio, um ein einfaches Vorhersageanalytik-Experiment zu entwickeln und zu durchlaufen.

##Fünf Schritte zur Erstellung eines Experiments 

Mit den fünf grundlegenden Schritten zur Experimenterstellung in ML Studio können Sie Ihr Modell erstellen, trainieren und bewerten:  

- Modellerstellung 
	- [Schritt 1: Datensammlung]
	- [Schritt 2: Datenvorverarbeitung]
	- [Schritt 3: Definition von Funktionen]
- Modelltraining 
	- [Schritt 4: Auswählen und Anwenden eines Lernalgorithmus]
- Modellbewertung und -Tests 
	- [Schritt 5: Vorhersagen über neue Daten] 

[Schritt 1: Abrufen von Daten]: #Step-1-Get-Daten
[Schritt 2: Datenvorverarbeitung]: #Step-2-Pre-Prozess-Daten
[Schritt 3: Definition von Funktionen]: #Step-3-definieren-Features
[Schritt 4: Auswählen und Anwenden eines Lernalgorithmus]: #step-4-choose-and-apply-a-learning-algorithm
[Schritt 5: Vorhersagen über neue Daten]: #step-5-predict-over-new-data 

In diesem Beispiel besprechen wir die Erstellung eines Regressionsmodells mit Beispieldaten aus dem Automobilbereich. Das Modell soll den Preis von Autos anhand verschiedener Variablen wie z. B. Marke und technischen Daten vorhersagen. 

### Schritt 1: Datensammlung

ML Studio enthält bereits zahlreiche Beispiel-Datensätze, und Sie können Daten aus vielen verschiedenen Quellen importieren. Für dieses Beispiel verwenden wir den mitgelieferten Beispiel-Datensatz **Automobile price data (Raw)**, der Preisdaten für Autos enthält.

1. Starten Sie ein neues Experiment, indem Sie unten im ML Studio-Fenster auf **+NEU** klicken und **EXPERIMENT** auswählen. Ändern Sie den Namen des Experiments von "Unbenannt" zu einem sinnvollen Namen wie "Preisvorhersage Autos".

2. Links vom Experimentbereich finden Sie eine Palette mit Datensätzen und Modulen. Geben Sie "automobile" in das Suchfeld im oberen Bereich der Palette ein, um den Datensatz mit dem Namen **Automobile price data (Raw)** zu finden. 

	![Palette search][screen1a]

3. Ziehen Sie den Datensatz in den Experimentbereich. 

	![Dataset][screen1]

Sie können auf den Ausgabeport im unteren Bereich des Automobil-Datensatzes klicken und **Visualisieren** auswählen, um die enthaltenen Daten anzuzeigen. Die Variablen im Datensatz werden als Spalten angezeigt, und jede Instanz eines Automobils füllt eine Zeile. Die letzte Spalte "price" (Spalte 26) ist die Zielvariable, die wir vorhersagen möchten. 

![Dataset visualization][screen1b]

Schließen Sie das Visualisierungsfenster, indem Sie auf das "**x**" in der oberen rechten Ecke klicken.

### Schritt 2: Datenvorverarbeitung

Datensätze müssen vor der Analyse normalerweise vorverarbeitet werden. Sie werden die fehlenden Werte in manchen Spalten und Zeilen bemerkt haben. Diese fehlenden Werte müssen bereinigt werden, um die Daten analysieren zu können. In unserem Fall entfernen wir einfach alle Zeilen, in denen Werte fehlen. Außerdem enthält die Spalte "normalized-losses" viele fehlende Werte, daher schließen wir diese Spalte komplett aus dem Modell aus. 

>**Hinweis** - Die Bereinigung fehlender Werte aus den Eingabedaten ist eine Voraussetzung für die Verwendung der meisten Module. 

Wir entfernen zunächst die Spalte "normalized-losses" und anschließend alle Zeilen, in denen Daten fehlen. 

1. Ziehen Sie das Modul **Projektspalten** in den Experimentbereich und verbinden Sie es mit dem Datensatz **Automobile price data (Raw)**. Mit diesem Modul können wir auswählen, welche Daten wir in unserem Modell ein- bzw. ausschließen möchten. 

2. Wählen Sie das Modul **Projektspalten** aus und klicken Sie auf **Spaltenauswahl starten** im Eigenschaftenpanel. 

	- Stellen Sie sicher, dass in der Dropdownliste **Beginnen mit** der Eintrag **Alle Spalten** ausgewählt ist. Damit wird **Projektspalten** angewiesen, alle Spalten zu durchlaufen (mit Ausnahme derer, die wir jetzt ausschließen werden). 
	- Wählen Sie in der nächsten Zeile **Ausschließen** und **Spaltennamen** aus und klicken Sie in das Textfeld. Eine Liste der Spalten wird angezeigt - wählen Sie "normalized-losses" aus, um den Eintrag zum Textfeld hinzuzufügen. 
	- Aktivieren Sie das Kontrollkästchen **OK**, um die Spaltenauswahl zu schließen.

    ![Select columns][screen3]
	
	Das Eigenschaftenpanel für **Projektspalten** zeigt an, dass alle Spalten des Datensatzes mit Ausnahme von "normalized-losses" durchlaufen werden. 

    ![Project Columns properties][screen4]

    >**Hinweis** - Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. Doppelklicken Sie auf das Modul **Projektspalten** und geben Sie den Kommentar "normalized-losses ausschließen" ein. 

3. Ziehen Sie das Modul **Bereinigung fehlender Werte** in den Experimentbereich und verbinden Sie es mit dem Modul **Projektspalten**. Wählen Sie im Eigenschaftenpanel den Wert **Gesamte Zeile entfernen** unter **Für fehlende Werte** aus, um alle Zeilen zu entfernen, in denen Werte fehlen.  Doppelklicken Sie auf das Modul und geben Sie den Kommentar "Unvollständige Zeilen entfernen" ein.

	![Missing Values Scrubber properties][screen4a]

4. Führen Sie das Experiment aus, indem Sie unterhalb des Experimentbereichs auf **AUSFÜHREN** klicken.

Nach Abschluss des Experiments sind alle Module mit einem grünen Häkchen markiert, um anzuzeigen, dass diese erfolgreich abgeschlossen wurden. Beachten Sie auch den Status "Ausführung abgeschlossen" in der oberen rechten Ecke.

![First experiment run][screen5]

Das Experiment hat bislang nur Daten bereinigt. Um den bereinigten Datensatz anzuzeigen, klicken Sie auf den Ausgabeport des Moduls **Bereinigung fehlender Werte** und wählen Sie **Visualisieren** aus. Beachten Sie, dass die Spalte "normalized-losses" nicht mehr enthalten ist und dass keine leeren Felder mehr existieren.

Nach der Bereinigung der Daten können wir nun angeben, welche Funktionen wir im Vorhersagemodell verwenden möchten.

### Schritt 3: Definition von Funktionen

*Funktionen* im Bereich maschinelles Lernen sind einzeln messbare Eigenschaften von Dingen, an denen Sie interessiert sind. In unserem Datensatz stellt jede Reihe ein Auto dar, und jede Spalte ist eine Funktion dieses Autos. Einen guten Satz von Funktionen für die Erstellung eines Vorhersagemodells finden Sie durch Ausprobieren und Kenntnisse des vorliegenden Problems. Manche Funktionen eignen sich besser für die Vorhersage des Ziels als andere. Außerdem haben manche Funktionen eine starke Korrelation mit anderen Funktionen, z. B. city-mpg und highway-mpg. Diese liefern kaum neue Informationen für das Modell und können entfernt werden.

Wir werden ein Modell erstellen, das eine Teilmenge der Funktionen in unserem Datensatz verwendet. Sie können später jederzeit andere Funktionen auswählen, das Experiment erneut ausführen und versuchen, bessere Ergebnisse zu erhalten. Als ersten Versuch wählen wir die folgenden Funktionen (Spalten) mit dem Modul **Projektspalten** aus. Beachten Sie, das wir zum Trainieren des Modells den Wert *price* einbeziehen müssen, den wir vorhersagen möchten.

	Marke, Modell, Radstand, Motorgröße, Pferdestärke, Spitzendrehzahl, Autobahn MPG, Preis

1. Ziehen Sie ein weiteres **Projektspalten**-Modul in den Experimentbereich und verbinden Sie es mit dem Modul **Bereinigung fehlender Werte**. Doppelklicken Sie auf das Modul und geben Sie "Funktionsauswahl für Vorhersage" ein.

2. Klicken Sie im Eigenschaftenpanel auf **Spaltenauswahl starten**. 

3. Wählen Sie in der Spaltenauswahl **Keine Spalten** unter **Beginnen mit** aus, und wählen Sie **Einbeziehen** und **Spaltennamen** in der Filterzeile aus. Geben Sie unsere Liste der Spaltennamen ein. Damit weisen Sie das Modul an, nur die angegebenen Spalten zu durchlaufen.

	>**Hinweis** - Da wir das Experiment bereits bis zu diesem Punkt ausgeführt haben, sind die Spaltendefinitionen unserer Daten vom Originaldatensatz durch das Modul **Bereinigung fehlender Werte** gelaufen. Wenn Sie **Projektspalten** mit **Bereinigung fehlender Werte** verbinden, erhält das Modul **Projektspalten** Kenntnis von den Spaltendefinitionen in unseren Daten. Wenn Sie in das Feld Spaltennamen klicken, wird eine Liste der Spalten angezeigt, und Sie können die Spalten einzeln auswählen, die Sie zur Liste hinzufügen möchten. 

4. Klicken Sie auf **OK**.

![Select columns][screen6]

Dieser Vorgang produziert den Datensatz, den wir in den nächsten Schritten im Lernalgorithmus verwenden werden. Sie können den Vorgang später jederzeit mit einer anderen Auswahl an Funktionen wiederholen. 

### Schritt 4: Auswählen und Anwenden eines Lernalgorithmus

Nachdem die Daten vorbereitet sind, können Sie das Vorhersagemodell anhand von Training und Tests erarbeiten. *Klassifizierung* und *Regression* sind zwei Techniken für beaufsichtigtes maschinelles Lernen. Bei der Klassifizierung werden Vorhersagen anhand eines definierten Satzes von Werten gemacht, wie z. B. Farben (rot, grün, blau). Bei der Regression werden Vorhersagen anhand eines veränderlichen Satzes von Werten gemacht, wie z. B. das Alter einer Person.

Wir möchten den Preis eines Autos vorhersagen, der beliebige Werte annehmen kann, daher verwenden wir ein Regressionsmodell. Für dieses Beispiel trainieren wir ein einfaches *lineares Regressionsmodell* und testen es anschließend im nächsten Schritt. 

1. Teilen Sie die Daten in Trainings- und Testsätze auf. Ziehen Sie das Modul **Aufteilen** in den Experimentbereich und verbinden Sie es mit der Ausgabe des letzten **Projektspalten**-Moduls. Setzen Sie **Anteil der Zeilen im ersten Ausgabedatensatz** auf 0,75. Mit dieser Einstellung verwenden wir 75 % der Daten zum Trainieren des Modells und halten 25 % für Tests zurück.

	>**Hinweis** - Sie können den Parameter **Zufälliger Ausgangswert** ändern, um unterschiedliche zufällige Proben für Training und Tests zu erstellen. Dieser Parameter steuert den Ausgangswert des Pseudo-Zufallszahlengenerators.
	
2. Führen Sie das Experiment aus. Dabei können die Module **Projektspalten** und **Aufteilen** die Spaltendefinitionen an die Module übergeben, die wir anschließend hinzufügen werden.  

2. Um den Lernalgorithmus auszuwählen, erweitern Sie die Kategorie **Maschinelles Lernen** in der Modulpalette links vom Experimentbereich und erweitern Sie anschließend **Modell initialisieren**. Daraufhin werden verschiedene Kategorien von Modulen angezeigt, die zur Initialisierung eines Lernalgorithmus verwendet werden können. 

	Wählen Sie für dieses Beispielexperiment das Modul **Linear Regression** unter der Kategorie **Regression** aus (oder geben Sie "Lineare Regression" im Suchfeld der Palette ein), und ziehen Sie das Modul in den Experimentbereich.

3. Ziehen Sie das Modul **Modell trainieren** ebenfalls in den Experimentbereich. Klicken Sie auf **Spaltenauswahl starten** und wählen Sie die Spalte *price* aus. Dies ist der Wert, den unser Modell vorhersagen wird.

	![Select "price" column][screen7]

4. Verbinden Sie den linken Eingabeport mit der Ausgabe des Moduls **Lineare Regression**, und den rechten Eingabeport mit der Ausgabe der Trainingsdaten (linker Port) des Moduls **Aufteilen**.  

5. Führen Sie das Experiment aus. 

Als Ergebnis erhalten Sie ein trainiertes Regressionsmodul, mit dem Sie neue Proben bewerten können, um Vorhersagen zu machen. 

![Applying the learning algorithm][screen8]

### Schritt 5: Vorhersagen über neue Daten 

Wir haben das Modell nun trainiert und können es verwenden, um die restlichen 25 % unserer Daten zu bewerten und zu sehen, wie gut unser Modell funktioniert. 

1. Ziehen Sie das Modul **Modell bewerten** in den Experimentbereich und verbinden Sie den linken Eingabeport mit der Ausgabe des Moduls **Modell trainieren**, und den rechten Eingabeport mit der Ausgabe der Testdaten (rechter Port) des Moduls **Aufteilen**.  

	![Score Model module][screen8a]

2. Führen Sie das Experiment aus und sehen Sie sich die Ausgabe des Moduls **Modell bewerten** an (doppelklicken Sie auf den Ausgabeport und wählen Sie **Visualisieren** aus). Die Ausgabe zeigt die vorhergesagten Preiswerte zusammen mit den bekannten Werten aus den Testdaten.  

3. Um die Qualität der Ergebnisse zu testen, ziehen Sie das Modul **Modell evaluieren** in den Experimentbereich und verbinden Sie den linken Eingabeport mit der Ausgabe des Moduls **Modell bewerten** (das Modul **Modell evaluieren** hat zwei Eingangsports, da es zum Vergleichen zweier Modelle verwendet werden kann.
 
4. Führen Sie das Experiment aus und sehen Sie sich die Ausgabe des Moduls **Modell bewerten** an (doppelklicken Sie auf den Ausgabeport und wählen Sie **Visualisieren** aus). Die folgenden Statistiken werden für unser Modell angezeigt.

	- **Mittlerer absoluter Fehler** - Der Mittelwert der absoluten Fehler (ein *Fehler* ist die Differenz zwischen vorhergesagtem und tatsächlichem Wert).
	- **Wurzel der mittleren quadratischen Abweichung** (RMSE) - Die Quadratwurzel des Durchschnitts des Quadrats der Vorhersagefehler für den Datensatz.
	- **Relativer absoluter Fehler** - Der Mittelwert der absoluten Fehler relativ zur absoluten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
	- **Relativer quadratischer Fehler** - Der Durchschnitt der quadrierten Fehler relativ zur quadrierten Differenz zwischen tatsächlichen Werten und dem Durchschnitt aller tatsächlichen Werte.
	- **Bestimmungskoeffizient** - Dieser auch als "R Quadrat" bekannte Wert ist eine statistische Kenngröße, die angibt, wie gut ein Modell zu den Daten passt.
	
	Für all diese Fehlerstatistiken sind kleinere Werte jeweils besser - ein kleinerer Wert bedeutet, dass die Vorhersage näher an den tatsächlichen Werten liegt. Für den **Bestimmungskoeffizienten** gilt: Je näher der Bestimmungskoeffizient am Wert eins (1,0) liegt, desto besser die Vorhersage.

	![Evaluation results][screen9]

Das fertige Experiment sollte folgendermaßen aussehen:

![Complete experiment][screen10]

### Wie geht es weiter?

Sie haben Ihr Experiment nun eingerichtet und können versuchen, es anhand von Iterationen zu verbessern. Sie können z. B. die Funktionen ändern, die Sie in Ihrer Vorhersage verwenden. Oder Sie können die Eigenschaften des **Linearen Regressionsalgorithmus** ändern oder einen völlig anderen Algorithmus ausprobieren. Sie können sogar mehrere Algorithmen zu Ihrem Ereignis gleichzeitig hinzufügen und diese vergleichen (je 2 Algorithmen auf einmal), indem Sie das Modul **Modell evaluieren** verwenden. 

>**Hinweis** - Verwenden Sie die Schaltfläche **SPEICHERN UNTER** unterhalb des Experimentbereichs, um Kopien der Iterationen Ihres Experiments zu speichern. Sie können alle Iterationen Ihres Experiments anzeigen, indem Sie unter dem Experimentbereich auf **AUSFÜHRUNGSVERLAUF ANZEIGEN** klicken. Weitere Details finden Sie im ML Studio-Hilfethema **Ausführungsverlauf anzeigen**.

Wenn Sie mit Ihrem Modell zufrieden sind, können Sie es als Webdienst veröffentlichen, der Automobilpreise anhand neuer Daten vorhersagt. Weitere Details finden Sie im ML Studio-Hilfethema **Veröffentlichen von Experimenten**.

Eine ausführlichere Anleitung zu Erstellung, Training, Bewertung und Veröffentlichung von Vorhersagemodellen finden Sie unter [Anleitung: Entwickeln einer Vorhersagelösung mit Azure Machine Learning](http://azure.microsoft.com/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/). 


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

<!--HONumber=46--> 
