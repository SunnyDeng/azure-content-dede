<properties 
	pageTitle="Exemplarische Vorgehensweise, Schritt 4: Trainieren und Auswerten des Predictive Analytics-Modells | Microsoft Azure" 
	description="Exemplarische Vorgehensweise zum Entwickeln einer Vorhersagelösung – Schritt 4: Trainieren, Bewerten und Auswerten mehrerer Modelle in Azure Machine Learning Studio." 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>


# Exemplarische Vorgehensweise, Schritt 4: Trainieren und Auswerten des Predictive Analytics-Modells

Dies ist der vierte Schritt der exemplarischen Vorgehensweise zum [Entwickeln einer Vorhersagelösung mit Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md):


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**Trainieren und Auswerten der Modelle**
5.	[Veröffentlichen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

----------

In diesem Experiment werden verschiedene Algorithmen für das Vorhersagemodell ausprobiert. Es werden zwei verschiedene Modelltypen erstellt und dann deren Bewertungsergebnisse verglichen, um zu entscheiden, welcher Algorithmus im endgültigen Experiment verwendet werden soll.

Es stehen mehrere Modelle zur Auswahl. Um die verfügbaren Modelle anzuzeigen, erweitern Sie den Knoten **Machine Learning** in der Modulpalette, und erweitern Sie dann **Modell initialisieren** und die darunter liegenden Knoten. Für dieses Experiment wählen wir die Module "Support Vector Machine (SVM)" und "Two-Class Boosted Decision Trees". Es werden die passenden Modelle verwendet, um die Lernalgorithmen zu initialisieren und die Module [Modell trainieren][train-model] zum Trainieren der Modelle zu verwenden.

##Trainieren der Modelle
Zuerst wird das Boosted Decision Tree-Modell eingerichtet:

1.	Suchen Sie das Modul [Two-Class Boosted Decision Trees][two-class-boosted-decision-tree] in der Modulpalette, und ziehen Sie es auf die Canvas.
2.	Suchen Sie das Modul [Modell trainieren][train-model], ziehen Sie es in den Bereich, und verbinden Sie den Ausgang des Moduls „Boosted Decision Tree“ mit dem linken Eingabeport („Untrainiertes Modell“) des Moduls [Modell trainieren][train-model].
3.	Verbinden Sie die linke Ausgabe („Ergebnisdataset“) des linken Moduls [Execute R Script][execute-r-script] mit dem rechten Eingabeport („Dataset“) des Moduls [Train Model][train-model].

	> [AZURE.TIP]Zwei der Eingaben und eine der Ausgaben des Moduls [Execute R Script][execute-r-script] werden für dieses Experiment nicht benötigt und daher nicht angefügt. Das kommt bei einigen Modulen häufiger vor.


4.	Wählen Sie das Modul [Modell trainieren][train-model] aus. Klicken Sie im Bereich **Properties** auf **Launch column selector**, wählen Sie in der ersten Dropdownliste **Include** und in der zweiten Dropdownliste **column indices** aus, und geben Sie „21“ in das Textfeld ein (Sie können auch **Column name** auswählen und „Credit Risk“ eingeben). Dadurch wird Spalte 21, der Wert des Kreditrisikos, als die Spalte identifiziert, die vom Modell vorhergesagt werden soll.


Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:

![Trainieren eines Modells][1]
 
Als Nächstes wird das SVM-Modell eingerichtet.

Boosted Decision Trees funktionieren hervorragend mit allen Arten von Merkmalen. Da das SVM-Modul jedoch einen linearen Klassifikator generiert, hat das entsprechende generierte Modell den besten Testfehler, wenn alle numerischen Merkmale dieselbe Skala verwenden. Um alle numerischen Features zur gleichen Skala zu konvertieren, verwenden wir das Modul [Normalize Data][normalize-data] mit einer Tanh-Transformation, die Features in den Bereich [0,1] transformiert. Beachten Sie, dass Zeichenfolgenmerkmale vom SVM-Modul in kategorische Merkmale und anschließend in binäre 0/1-Merkmale konvertiert werden. Daher müssen wir Zeichenfolgenmerkmale nicht manuell transformieren. Zudem darf die Spalte "Credit Risk" (Spalte 21) nicht transformiert werden. Diese ist zwar numerisch, da es sich dabei aber um den Wert handelt, für dessen Vorhersage das Modell trainiert wird, darf er nicht verändert werden.

1.	Suchen Sie das Modul [Zweiklassige Support Vector Machine][two-class-support-vector-machine] in der Modulpalette, und ziehen Sie es in den Bereich.
2.	Klicken Sie mit der rechten Maustaste auf das Modul [Train Model][train-model], und wählen Sie **Copy** aus. Klicken Sie anschließend mit der rechten Maustaste auf die Canvas, und wählen Sie **Paste** aus. Beachten Sie, dass die Kopie des Moduls [Modell trainieren][train-model] die gleiche Spaltenauswahl wie das Original hat.
3.	Verbinden Sie die Ausgabe des SVM-Moduls mit dem linken Eingabeport („Untrainiertes Modell“) des Moduls [Modell trainieren][train-model].
4.	Suchen Sie das Modul [Normalize Data][normalize-data], und ziehen Sie es auf die Canvas.
5.	Verbinden Sie die Eingabe dieses Transformationsmoduls mit der Ausgabe des linken Moduls [Execute R Script][execute-r-script].
6.	Verbinden Sie den linken Ausgabeport („Transformiertes Dataset“) des Transformationsmoduls mit dem rechten Eingabeport („Dataset“) des Moduls [Modell trainieren][train-model].
7.	Wählen Sie im Bereich **Properties** des Transformationsmoduls den Wert **Tanh** für den Parameter **Transformation method** aus.
8.	Klicken Sie auf **Launch column selector**, wählen Sie für **Begin With** „No columns“ und wählen Sie in der ersten Dropdownliste **Include**, in der zweiten Dropdownliste **column type** und in der dritten Dropdownliste **Numeric** aus. Damit wird festgelegt, dass alle numerischen Spalten (und nur die numerischen Spalten) transformiert werden.
9.	Klicken Sie auf das Pluszeichen (+). Dadurch wird eine neue Dropdown-Zeile erstellt. Wählen Sie in der ersten Dropdownliste **Exclude** und in der zweiten Dropdownliste **column indices** aus, und geben Sie „21“ in das Textfeld ein. Dadurch wird angegeben, dass Spalte 21 (die Spalte "Credit Risk") ignoriert wird.
10.	Klicken Sie auf **OK**.  


Das Modul [Normalize Data][normalize-data] ist jetzt für eine Tanh-Transformation aller numerischen Spalten mit Ausnahme der Spalte „Credit Risk“ eingerichtet.

Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:

![Trainieren des zweiten Modells][2]

##Bewerten und Auswerten der Modelle
Wir verwenden die Bewertungsdaten, die durch das Modul **Aufteilen** getrennt wurden, um die trainierten Modelle zu bewerten. Danach können die Ergebnisse der beiden Modelle verglichen werden, um festzustellen, welches bessere Ergebnisse erbrachte.

1.	Suchen Sie das Modul [Modell bewerten][score-model], und ziehen Sie es in den Bereich.
2.	Verbinden Sie den linken Eingabeport dieses Moduls mit dem Boosted Decision Tree-Modell (d. h., verbinden Sie es mit dem Ausgabeport des Moduls [Modell trainieren][train-model], das mit dem Modul [Zweiklassiger Boosted Decision Tree][two-class-boosted-decision-tree] verbunden ist).
3.	Verbinden Sie den rechten Eingabeport des Moduls [Modell bewerten][score-model] mit der Ausgabe des rechten Moduls [R-Skript ausführen][execute-r-script]. 
4.	Kopieren Sie das Modul [Modell bewerten][score-model] und fügen Sie es ein, um eine zweite Kopie zu erstellen, oder ziehen Sie ein neues Modul in den Bereich.
5.	Verbinden Sie den linken Eingabeport dieses Moduls mit dem SVM-Modell (d.h., verbinden Sie es mit dem Ausgabeport des Moduls [Modell trainieren][train-model], das mit dem Modul [Zweiklassige Support Vector Machine][two-class-support-vector-machine] verbunden ist).
6.	Für das SVM-Modell muss die gleiche Transformation für die Testdaten durchgeführt werden wie für die Trainingsdaten. Kopieren Sie also das Modul [Normalize Data][normalize-data], und fügen Sie es ein, um eine zweite Kopie zu erstellen, und verbinden Sie es mit der Ausgabe des rechten Moduls [Execute R Script][execute-r-script].
7.	Verbinden Sie den rechten Eingabeport des Moduls [Score Model][score-model] mit der Ausgabe des Moduls [Normalize Data][normalize-data].  

Zur Evaluierung der beiden Bewertungsergebnisse wird das Modul [Modell evaluieren][evaluate-model] verwendet.

1.	Suchen Sie das Modul [Modell evaluieren][evaluate-model], und ziehen Sie es in den Bereich.
2.	Verbinden Sie den linken Eingangsport mit dem Ausgangsport des Moduls [Modell bewerten][score-model], das dem Boosted Decision Tree-Modell zugeordnet ist.
3.	Verbinden Sie den rechten Eingabeport mit dem anderen Modul [Modell bewerten][score-model].  

Das Experiment sollte in etwa wie folgt aussehen:

![Evaluieren beider Modelle][3]
 
Klicken Sie auf die Schaltfläche **AUSFÜHREN** unter dem Bereich, um das Experiment auszuführen. Dies kann einige Minuten dauern. Für jedes Modul wird ein Drehindikator angezeigt, um anzugeben, dass es ausgeführt wird. Wenn das Modul abgeschlossen ist, wird ein grünes Häkchen angezeigt.

Wenn alle Module ein Häkchen aufweisen, ist die Ausführung des Experiments beendet. Um die Ergebnisse zu prüfen, klicken Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model], und wählen Sie **View Results** aus.

Das Modul [Modell evaluieren][evaluate-model] erzeugt ein Paar aus Kurven und Metriken, mit denen die Ergebnisse der beiden bewerteten Modelle verglichen werden können. Sie können die Ergebnisse als ROC-Kurven (Receiver Operator Characteristic), Genauigkeits-/Trefferkurven oder Prognosegütekurven anzeigen. Zu den zusätzlich angezeigten Daten zählen eine Wahrheitsmatrix, kumulative Werte für den Bereich unter der Kurve (AUC) sowie weitere Metriken. Sie können den Schwellwert ändern, indem Sie den Schieberegler nach links oder rechts bewegen und beobachten, wie sich dies auf den Metriksatz auswirkt.

Klicken Sie auf **Scored dataset** oder auf **Scored dataset to compare**, um die zugeordnete Kurve zu markieren und die zugeordneten Metriken darunter anzuzeigen. In der Legende für die Kurven entspricht „Bewertetes Dataset“ dem linken Eingabeport des Moduls [Modell evaluieren][evaluate-model] – in unserem Fall ist dies das Boosted Decision Tree-Modell. "Scored dataset to compare" entspricht dem rechten Eingabeport – in unserem Fall dem SVM-Modell. Wenn Sie auf eine dieser Beschriftungen klicken, werden die Kurve für das betreffende Modell markiert und die entsprechenden Metriken darunter angezeigt.

![ROC-Kurven für Modelle][4]
 
Wenn Sie diese Werte prüfen, können Sie entscheiden, welches Modell am ehesten den gewünschten Ergebnissen entspricht. Sie können zurückgehen und das Experiment erneut ausführen, indem Sie Werte in den verschiedenen Modellen ändern.

> [AZURE.TIP]Jedes Mal, wenn Sie das Experiment ausführen, wird im Ausführungsverlauf ein Dataset für diese Iteration aufbewahrt. Sie können die Iterationen anzeigen und zu jeder von ihnen zurückkehren, indem Sie unter dem Bereich auf **AUSFÜHRUNGSVERLAUF ANZEIGEN** klicken. Sie können auch auf **Vorherige Ausführung** im Fenster **Eigenschaften** klicken, um zur Iteration unmittelbar vor der geöffneten Iteration zurückzukehren. Weitere Informationen finden Sie unter [Verwalten von Experimentiterationen in Azure Machine Learning-Studio](machine-learning-manage-experiment-iterations.md).

Sie können auch eine Kopie jeder Iteration des Experiments anfertigen, indem Sie unter dem Bereich auf **SPEICHERN UNTER** klicken. Dadurch wird ein Duplikat des Experiments angefertigt, und im Ausführungsverlauf werden Ihre Iterationen dieser Version verfolgt. Die neue Kopie wird in der Liste **EXPERIMENTE** zusammen mit dem Original angezeigt. Das kann hilfreich sein, wenn Sie eine neue Reihe Iterationen für das Experiment starten möchten.

Als zusätzliche Hilfe beim Verfolgen der Änderungen, die Sie an den Modulparametern vornehmen, können Sie jedem Modul im Experimentbereich Kommentare hinzufügen. Doppelklicken Sie einfach in das Modul, oder klicken Sie mit der rechten Maustaste, und wählen Sie **Kommentar bearbeiten** aus. Diese Kommentare werden zusammen mit den Experimentiterationen gespeichert und können Sie beim Kommentieren Ihrer Arbeit unterstützen.


----------

**Nächster Schritt: [Veröffentlichen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)**

[1]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train1.png
[2]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train2.png
[3]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train3.png
[4]: ./media/machine-learning-walkthrough-4-train-and-evaluate-models/train4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
 

<!---HONumber=August15_HO6-->