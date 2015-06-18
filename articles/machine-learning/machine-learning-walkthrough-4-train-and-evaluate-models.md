<properties 
	pageTitle="Schritt 4: Trainieren und Bewerten der Vorhersageanalytikmodelle | Azure" 
	description="Exemplarische Vorgehensweise Schritt 4: Training, Bewertung und Evaluierung für mehrere Modelle in Azure Machine Learning Studio" 
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
	ms.date="01/29/2015" 
	ms.author="garye"/>


Dies ist der vierte Teil der exemplarischen Vorgehensweise [Entwickeln einer Vorhersagelösung mit Azure ML][develop]:

[Entwickeln]: ../machine-learning-walkthrough-develop-predictive-solution/

1.	[Erstellen eines ML-Arbeitsbereichs][create-workspace]
2.	[Hochladen vorhandener Daten][upload-data]
3.	[Erstellen eines neuen Experiments][create-new]
4.	**Trainieren und Bewerten der Modelle**
5.	[Veröffentlichen des Webdiensts][publish]
6.	[Zugreifen auf den Webdienst][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Schritt 4: Trainieren und Bewerten der Vorhersageanalytikmodelle


In diesem Experiment werden verschiedene Algorithmen für das Vorhersagemodell ausprobiert. Es werden zwei verschiedene Modelltypen erstellt und dann deren Bewertungsergebnisse verglichen, um zu entscheiden, welcher Algorithmus im endgültigen Experiment verwendet werden soll.  

Es stehen mehrere Modelle zur Auswahl. Um die verfügbaren Modelle anzuzeigen, erweitern Sie den Knoten **Machine Learning** in der Modulpalette, und erweitern Sie dann **Modell initialisieren** und die darunter liegenden Knoten. Zu Zwecken dieses Experiments wählen wir Support Vector Machine (SVM) und die zweiklassigen Boosted Decision Trees. Es werden die passenden Modelle verwendet, im die Lernalgorithmen zu initialisieren und die Module **Modell trainieren** zum Trainieren der Modelle zu verwenden.   

##Trainieren der Modelle
Zuerst wird das Boosted Decision Tree-Modell eingerichtet:  

1.	Suchen Sie das Modul **Zweiklassiger Boosted Decision Tree** in der Modulpalette, und ziehen Sie es in den Bereich.
2.	Suchen Sie das Modul **Modell trainieren**, ziehen Sie es in den Bereich, und verbinden Sie den Ausgang des Moduls "Boosted Decision Tree" mit dem linken Eingabeport ("Untrainiertes Modell") des Moduls **Modell trainieren**.
3.	Verbinden Sie die Ausgabe des linken Moduls **R-Skript ausführen** mit dem rechten Eingabeport ("Dataset") des Moduls **Modell trainieren**.

	>Tipp: Zwei der Eingaben und eine der Ausgaben des Moduls **R-Skript ausführen** werden für dieses Experiment nicht benötigt; daher lassen wir sie unverbunden. Das kommt bei einigen Modulen häufiger vor.


4.	Wählen Sie das Modul **Modell trainieren** aus. Klicken Sie im Bereich **Eigenschaften** auf **Spaltenauswahl starten**, wählen Sie in der ersten Dropdown-Liste "Spaltenindizes" aus, und geben Sie "21" in das Textfeld ein (Sie können auch "Spaltenname" auswählen und "Kreditrisiko" eingeben). Dadurch wird Spalte 21, der Wert des Kreditrisikos, als die Spalte identifiziert, die vom Modell vorhergesagt werden soll.


Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:  

![Training a model][1]
 
Als Nächstes wird das SVM-Modell eingerichtet.  

Boosted Decision Trees funktionieren hervorragend mit allen Arten von Merkmalen. Da das SVM-Modul jedoch einen linearen Klassifikator generiert, hat das entsprechende generierte Modell den besten Testfehler, wenn alle numerischen Merkmale dieselbe Skala verwenden. Um alle numerischen Merkmale zur gleichen Skala zu konvertieren, verwenden wir das Modul **Normalize Data** mit einer Tanh-Transformation, die Merkmale in den Bereich [0,1] transformiert. Beachten Sie, dass Zeichenfolgenmerkmale vom SVM-Modul in kategorische Merkmale und anschließend in binäre 0/1-Merkmale konvertiert werden. Daher müssen wir Zeichenfolgenmerkmale nicht manuell transformieren. Zudem darf die Spalte "Kreditrisiko" (Spalte 21) nicht transformiert werden - sie ist numerisch, aber es handelt sich hierbei um den Wert, für dessen Vorhersage das Modell trainiert wird. Daher darf er nicht verändert werden.  

1.	Suchen Sie das Modul **Zweiklassige Support Vector Machine** in der Modulpalette, und ziehen Sie es in den Bereich.
2.	Klicken Sie mit der rechten Maustaste auf das Modul **Modell trainieren**, wählen Sie **Kopieren** aus, und klicken Sie dann mit der rechten Maustaste in den Bereich, und wählen Sie **Einfügen** aus. Beachten Sie, dass die Kopie des Moduls **Modell trainieren** die gleiche Spaltenauswahl wie das Original hat.
3.	Verbinden Sie die Ausgabe des SVM-Moduls mit dem linken Eingabeport ("Untrainiertes Modell") des Moduls **Modell trainieren**.
4.	Suchen Sie das Modul **Normalize Data**, und ziehen Sie es in den Bereich.
5.	Verbinden Sie die Eingabe dieses Transformationsmoduls mit der Ausgabe des linken Moduls **R-Skript ausführen**.
6.	Verbinden Sie den linken Ausgabeport ("Transformiertes Dataset") des Transformationsmoduls mit dem rechten Eingabeport ("Dataset") des Moduls **Modell trainieren**.
7.	Wählen Sie im Bereich **Eigenschaften** des Transformationsmoduls den Wert "Tanh" für den Parameter **Transformationsmethode** aus.
8.	Klicken Sie auf **Spaltenauswahl starten**, wählen Sie "Include" in der ersten Dropdownliste, wählen Sie "Spalte" in der zweiten Dropdownliste, und wählen Sie "Numerisch" in der dritten Dropdownliste. Damit wird festgelegt, dass alle numerischen Spalten (und nur die numerischen Spalten) transformiert werden.
9.	Klicken Sie auf das Pluszeichen (+). Dadurch wird eine neue Dropdown-Zeile erstellt. Wählen Sie in der ersten Dropdown-Liste "Exclude", in der zweiten Dropdownliste "Spaltenindizes" aus, und geben Sie "21" in das Textfeld ein. Dadurch wird angegeben, dass Spalte 21 (die Spalte "Kreditrisiko") ignoriert wird.
10.	Klicken Sie auf **OK**.  


Das Modul **Normalize Data** ist jetzt für eine tanh-Transformation aller numerischen Spalten mit Ausnahme der Spalte "Kreditrisiko" eingerichtet.  

Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:  

![Training the second model][2]  

##Bewerten und Evaluieren der Modelle
Sie verwenden die Bewertungsdaten, die durch das Modul **Aufteilen** getrennt wurden, um die trainierten Modelle zu bewerten. Danach können die Ergebnisse der beiden Modelle verglichen werden, um festzustellen, welches bessere Ergebnisse erbrachte.  

1.	Suchen Sie das Modul **Modell bewerten**, und ziehen Sie es in den Bereich.
2.	Verbinden Sie den linken Eingabeport dieses Moduls mit dem Boosted Decision Tree-Modell (d. h., verbinden Sie es mit dem Ausgabeport des Moduls **Modell trainieren**, das mit dem Modul **Zweiklassiger Boosted Decision Tree** verbunden ist).
3.	Verbinden Sie den rechten Eingabeport des Moduls **Modell bewerten** mit der Ausgabe des rechten Moduls **R-Skript** ausführen. Beachten Sie, dass es in Ordnung ist, wenn die Ausgabe eines Moduls an verschiedene Stellen geht.
4.	Kopieren Sie das Modul **Modell bewerten** und fügen Sie es ein, um eine zweite Kopie zu erstellen, oder ziehen Sie ein neues Modul in den Bereich.
5.	Verbinden Sie den linken Eingabeport dieses Moduls mit dem SVM-Modell (d.h., verbinden Sie es mit dem Ausgabeport des Moduls **Modell trainieren**, das mit dem Modul **Zweiklassige Support Vector Machine** verbunden ist).
6.	Für das SVM-Modell muss die gleiche Transformation an den Testdaten durchgeführt werden wie bei den Trainingsdaten. Kopieren Sie also das Modul **Normalize Data** und fügen Sie es ein, um eine zweite Kopie zu erstellen, und verbinden Sie sie mit der Ausgabe des rechten Moduls **R-Skript ausführen**.
7.	Verbinden Sie den rechten Eingabeport des Moduls **Modell bewerten** mit der Ausgabe des Moduls **Normalize Data**.  

Zur Evaluierung der beiden Bewertungsergebnisse wird das Modul **Modell evaluieren** verwendet.  

1.	Suchen Sie das Modul **Modell evaluieren**, und ziehen Sie es in den Bereich.
2.	Verbinden Sie den linken Eingangsport mit dem Ausgangsport des Moduls **Modell bewerten**, das dem Boosted Decision Tree-Modell zugeordnet ist.
3.	Verbinden Sie den rechten Eingabeport mit dem anderen Modul **Modell bewerten**.  

Das Experiment sollte in etwa wie folgt aussehen:  

![Evaluating both models][3]
 
Klicken Sie auf die Schaltfläche **AUSFÜHREN** unter dem Bereich, um das Experiment auszuführen. Dies kann einige Minuten dauern. Für jedes Modul wird ein Drehindikator angezeigt, um anzugeben, dass es ausgeführt wird. Wenn das Modul abgeschlossen ist, wird ein grünes Häkchen angezeigt.   

Wenn alle Module ein Häkchen aufweisen, ist die Ausführung des Experiments beendet. Um die Ergebnisse zu prüfen, klicken Sie auf den Ausgabeport des Moduls **Modell evaluieren**, und wählen Sie **Visualisieren** aus.  

Das Modul **Modell evaluieren** erzeugt ein Paar aus Kurven und Metriken, mit denen die Ergebnisse der beiden bewerteten Modelle verglichen werden können. Sie können die Ergebnisse als ROC-Kurven (Receiver Operator Characteristic), Genauigkeits-/Trefferkurven oder Prognosegütekurven anzeigen. Zu den zusätzlich angezeigten Daten zählen eine Wahrheitsmatrix, kumulative AUC-Werte und andere Metriken. Sie können den Schwellwert ändern, indem Sie den Schieberegler nach links oder rechts bewegen und beobachten, wie sich dies auf den Metriksatz auswirkt.  

Klicken Sie auf "Bewertetes Dataset" oder auf "Bewertetes Dataset zum Vergleich", um die betreffende Kurve zu markieren und die zugeordneten Metriken darunter anzuzeigen. In der Legende für die Kurven entspricht "Bewertetes Dataset" dem linken Eingabeport des Moduls **Modell evaluieren** - in unserem Fall ist dies das Boosted Decision Tree-Modell. Das "Bewertete Dataset zum Vergleich" entspricht dem rechten Eingabeport - in unserem Fall dem SVM-Modell. Wenn Sie auf eine dieser Beschriftungen klicken, werden die Kurve für das betreffende Modell markiert und die entsprechenden Metriken darunter angezeigt.  

![ROC curves for models][4]
 
Wenn Sie diese Werte prüfen, können Sie entscheiden, welches Modell am ehesten den gewünschten Ergebnissen entspricht. Sie können zurückgehen und das Experiment erneut ausführen, indem Sie Werte in den verschiedenen Modellen ändern.  

>Tipp: Jedes Mal, wenn Sie das Experiment ausführen, wird im Ausführungsverlauf einen Dataset für diese Iteration aufbewahrt. Sie können die Iterationen anzeigen und zu jeder von ihnen zurückkehren, indem Sie unter dem Bereich auf **AUSFÜHRUNGSVERLAUF ANZEIGEN** klicken. Sie können auch auf **Vorherige Ausführung** im Fenster **Eigenschaften** klicken, um zur Iteration unmittelbar vor der geöffneten Iteration zurückzukehren.  

Sie können auch eine Kopie jeder Iteration des Experiments anfertigen, indem Sie unter dem Bereich auf **SPEICHERN UNTER** klicken. Dadurch wird ein Duplikat des Experiments angefertigt, und im Ausführungsverlauf werden Ihre Iterationen dieser Version verfolgt. Die neue Kopie wird in der Liste **EXPERIMENTE** zusammen mit dem Original angezeigt. Das kann hilfreich sein, wenn Sie eine neue Reihe Iterationen für das Experiment starten möchten.  

Als zusätzliche Hilfe beim Verfolgen der Änderungen, die Sie an den Modulparametern vornehmen, können Sie jedem Modul im Experimentbereich Kommentare hinzufügen. Doppelklicken Sie einfach in das Modul, oder klicken Sie mit der rechten Maustaste, und wählen Sie **Kommentar bearbeiten** aus. Diese Kommentare werden zusammen mit den Experimentiterationen gespeichert und können Sie beim Kommentieren Ihrer Arbeit unterstützen.


----------

**Nächster Schritt: [Veröffentlichen des Webdiensts][publish]**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png

<!--HONumber=46--> 
 