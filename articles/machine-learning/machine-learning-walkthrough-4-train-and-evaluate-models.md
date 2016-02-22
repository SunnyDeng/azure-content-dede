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
	ms.date="02/03/2016"
	ms.author="garye"/>


# Exemplarische Vorgehensweise, Schritt 4: Trainieren und Auswerten des Predictive Analytics-Modells

Dies ist der vierte Schritt der exemplarischen Vorgehensweise zum [Entwickeln einer Predictive Analytics-Lösung in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	**Trainieren und Bewerten der Modelle**
5.	[Bereitstellen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

----------

Einer der Vorteile von Azure Machine Learning Studio zum Erstellen von Machine Learning-Modellen ist die Möglichkeit, mehr als einen Typ von Modell gleichzeitig in einem Experiment zu testen und die Ergebnisse zu vergleichen. Dieser Typ von Experiment hilft Ihnen, die beste Lösung für Ihr Problem zu finden.

In dem Experiment, das wir in dieser exemplarischen Vorgehensweise entwickeln, werden zwei verschiedene Modelltypen erstellt und dann deren Bewertungsergebnisse verglichen, um zu entscheiden, welcher Algorithmus im endgültigen Experiment verwendet werden soll.

Es stehen mehrere Modelle zur Auswahl. Um die verfügbaren Modelle anzuzeigen, erweitern Sie den Knoten **Machine Learning** in der Modulpalette, und erweitern Sie dann **Modell initialisieren** und die darunter liegenden Knoten. Für dieses Experiment wählen wir die Module "Support Vector Machine (SVM)" und "Two-Class Boosted Decision Trees".

> [AZURE.TIP] Hilfe für die Entscheidung, welcher Machine Learning-Algorithmus für das Lösen des jeweiligen Problems am besten geeignet ist, finden Sie unter [Auswählen von Algorithmen für Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

##Trainieren der Modelle
Zuerst wird das Boosted Decision Tree-Modell eingerichtet:

1.	Suchen Sie das Modul [Two-Class Boosted Decision Trees][two-class-boosted-decision-tree] in der Modulpalette, und ziehen Sie es auf die Canvas.
2.	Suchen Sie das Modul [Modell trainieren][train-model], ziehen Sie es in den Bereich, und verbinden Sie den Ausgang des Moduls „Boosted Decision Tree“ mit dem linken Eingabeport („Untrainiertes Modell“) des Moduls [Modell trainieren][train-model].
    
    Das Modul [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] initialisiert das allgemeine Modell. Das Modul [Train Model][train-model] nutzt Trainingsdaten zum Trainieren des Modells.
     
3.	Verbinden Sie die linke Ausgabe („Ergebnisdataset“) des linken Moduls [Execute R Script][execute-r-script] mit dem rechten Eingabeport („Dataset“) des Moduls [Train Model][train-model].

	> [AZURE.TIP] Zwei der Eingaben und eine der Ausgaben des Moduls [Execute R Script][execute-r-script] werden für dieses Experiment nicht benötigt und daher nicht angefügt. Das kommt bei einigen Modulen häufiger vor.

4.	Wählen Sie das Modul [Modell trainieren][train-model] aus. Klicken Sie im Bereich **Properties** auf **Launch column selector**. Wählen Sie in der ersten Dropdownliste **Include** und in der zweiten **column indices** aus. Geben Sie „Kreditrisiko“ in das Textfeld ein (Sie können auch **Column name** auswählen und „21“ eingeben). Dadurch wird Spalte 21, der Wert des Kreditrisikos, als die Spalte identifiziert, die vom Modell vorhergesagt werden soll.


Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:

![Trainieren eines Modells][1]

Als Nächstes wird das SVM-Modell eingerichtet.

Zunächst soll SVM ein wenig erläutert werden. Boosted Decision Trees funktionieren hervorragend mit allen Arten von Merkmalen. Da das SVM-Modul jedoch einen linearen Klassifikator generiert, hat das entsprechende generierte Modell den besten Testfehler, wenn alle numerischen Merkmale dieselbe Skala verwenden. Um alle numerischen Merkmale auf dieselbe Skala umzurechnen, verwenden wir eine „Tanh“-Transformation (mit dem Modul [Normalize Data][normalize-data]). Hiermit werden unsere Zahlen in den Bereich [0,1] transformiert (Zeichenfolgenmerkmale werden vom SVM-Modul in kategorische Merkmale und anschließend in binäre 0/1-Merkmale konvertiert. Daher müssen wir Zeichenfolgenmerkmale nicht manuell transformieren). Zudem darf die Spalte "Credit Risk" (Spalte 21) nicht transformiert werden. Diese ist zwar numerisch, da es sich dabei aber um den Wert handelt, für dessen Vorhersage das Modell trainiert wird, darf er nicht verändert werden.

Um das SVM-Modell einzurichten, führen Sie folgende Schritte aus:

1.	Suchen Sie das Modul [Zweiklassige Support Vector Machine][two-class-support-vector-machine] in der Modulpalette, und ziehen Sie es in den Bereich.
2.	Klicken Sie mit der rechten Maustaste auf das Modul [Train Model][train-model], und wählen Sie **Copy** aus. Klicken Sie anschließend mit der rechten Maustaste auf die Canvas, und wählen Sie **Paste** aus. Beachten Sie, dass die Kopie des Moduls [Modell trainieren][train-model] die gleiche Spaltenauswahl wie das Original hat.
3.	Verbinden Sie die Ausgabe des SVM-Moduls mit dem linken Eingabeport („Untrainiertes Modell“) des Moduls [Modell trainieren][train-model].
4.	Suchen Sie das Modul [Normalize Data][normalize-data], und ziehen Sie es auf die Canvas.
5.	Verbinden Sie die Eingabe dieses Moduls mit der linken Ausgabe des linken Moduls [Execute R Script][execute-r-script] (beachten Sie, dass der Ausgabeport eines Moduls mit mehr als einem anderen Modul verbunden sein kann).
6.	Verbinden Sie den linken Ausgabeport („Transformiertes Dataset“) des Moduls [Normalize Data][normalize-data] mit dem rechten Eingabeport („Dataset“) des Moduls [Train Model][train-model].
7.	Wählen Sie im Bereich **Properties** des Moduls [Normalize Data][normalize-data] den Wert **Tanh** für den Parameter **Transformation method** aus.
8.	Klicken Sie auf **Launch column selector**, wählen Sie für **Begin With** „No columns“ und wählen Sie in der ersten Dropdownliste **Include**, in der zweiten Dropdownliste **column type** und in der dritten Dropdownliste **Numeric** aus. Damit wird festgelegt, dass alle numerischen Spalten (und nur die numerischen Spalten) transformiert werden.
9.	Klicken Sie auf das Pluszeichen (+) rechts neben dieser Zeile. Dadurch wird eine neue Zeile mit Dropdownlisten erstellt. Wählen Sie in der ersten Dropdownliste **Exclude** und in der zweiten **column names** aus, und geben Sie „Kreditrisiko“ in das Textfeld ein (oder wählen Sie **column indices** aus, und geben Sie „21“ ein). Dies gibt an, dass die Spalte „Kreditrisiko“ ignoriert werden soll (wir müssen dies tun, da diese Spalte numerisch ist und daher andernfalls transformiert würde).
10.	Klicken Sie auf **OK**.  


Das Modul [Normalize Data][normalize-data] ist jetzt für eine Tanh-Transformation aller numerischen Spalten mit Ausnahme der Spalte „Credit Risk“ eingerichtet.

Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:

![Trainieren des zweiten Modells][2]

##Bewerten und Auswerten der Modelle
Wir verwenden die Testdaten, die durch das Modul [Split Data][split] getrennt wurden, um die trainierten Modelle zu bewerten. Danach können die Ergebnisse der beiden Modelle verglichen werden, um festzustellen, welches bessere Ergebnisse erbrachte.

1.	Suchen Sie das Modul [Modell bewerten][score-model], und ziehen Sie es in den Bereich.
2.	Verbinden Sie den linken Eingabeport dieses Moduls mit dem Boosted Decision Tree-Modell (d. h., verbinden Sie es mit dem Ausgabeport des Moduls [Modell trainieren][train-model], das mit dem Modul [Zweiklassiger Boosted Decision Tree][two-class-boosted-decision-tree] verbunden ist).
3.	Verbinden Sie den rechten Eingabeport des Moduls [Score Model][score-model] mit der linken Ausgabe des rechten Moduls [Execute R Script][execute-r-script].

    Das Modul [Score Model][score-model] kann die Kreditinformationen aus den Testdaten entnehmen, sie durch das Modell laufen lassen und vom Modell generierte Vorhersagen mit der Spalte mit dem tatsächlichen Kreditrisiko in den Testdaten vergleichen.

4.	Kopieren Sie das Modul [Modell bewerten][score-model] und fügen Sie es ein, um eine zweite Kopie zu erstellen, oder ziehen Sie ein neues Modul in den Bereich.
5.	Verbinden Sie den linken Eingabeport dieses Moduls mit dem SVM-Modell (d.h., verbinden Sie es mit dem Ausgabeport des Moduls [Modell trainieren][train-model], das mit dem Modul [Zweiklassige Support Vector Machine][two-class-support-vector-machine] verbunden ist).
6.	Für das SVM-Modell muss die gleiche Transformation für die Testdaten durchgeführt werden wie für die Trainingsdaten. Kopieren Sie also das Modul [Normalize Data][normalize-data], und fügen Sie es ein, um eine zweite Kopie zu erstellen, und verbinden Sie es mit der linken Ausgabe des rechten Moduls [Execute R Script][execute-r-script].
7.	Verbinden Sie den rechten Eingabeport des Moduls [Score Model][score-model] mit der linken Ausgabe des Moduls [Normalize Data][normalize-data].  

Zur Evaluierung der beiden Bewertungsergebnisse wird das Modul [Modell evaluieren][evaluate-model] verwendet.

1.	Suchen Sie das Modul [Modell evaluieren][evaluate-model], und ziehen Sie es in den Bereich.
2.	Verbinden Sie den linken Eingangsport mit dem Ausgangsport des Moduls [Modell bewerten][score-model], das dem Boosted Decision Tree-Modell zugeordnet ist.
3.	Verbinden Sie den rechten Eingabeport mit dem anderen Modul [Modell bewerten][score-model].  

Klicken Sie auf die Schaltfläche **AUSFÜHREN** unter dem Bereich, um das Experiment auszuführen. Dies kann einige Minuten dauern. Für jedes Modul wird ein Drehindikator angezeigt, um anzugeben, dass es ausgeführt wird. Wenn das Modul abgeschlossen ist, wird ein grünes Häkchen angezeigt. Wenn alle Module ein Häkchen aufweisen, ist die Ausführung des Experiments beendet.

Das Experiment sollte in etwa wie folgt aussehen:

![Evaluieren beider Modelle][3]

Um die Ergebnisse zu prüfen, klicken Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model], und wählen Sie **Visualize** aus.

Das Modul [Modell evaluieren][evaluate-model] erzeugt ein Paar aus Kurven und Metriken, mit denen die Ergebnisse der beiden bewerteten Modelle verglichen werden können. Sie können die Ergebnisse als ROC-Kurven (Receiver Operator Characteristic), Genauigkeits-/Trefferkurven oder Prognosegütekurven anzeigen. Zu den zusätzlich angezeigten Daten zählen eine Wahrheitsmatrix, kumulative Werte für den Bereich unter der Kurve (AUC) sowie weitere Metriken. Sie können den Schwellwert ändern, indem Sie den Schieberegler nach links oder rechts bewegen und beobachten, wie sich dies auf den Metriksatz auswirkt.

Klicken Sie rechts neben dem Graph auf **Scored dataset** oder auf **Scored dataset to compare**, um die zugeordnete Kurve zu markieren und die zugeordneten Metriken darunter anzuzeigen. In der Legende für die Kurven entspricht „Bewertetes Dataset“ dem linken Eingabeport des Moduls [Modell evaluieren][evaluate-model] – in unserem Fall ist dies das Boosted Decision Tree-Modell. "Scored dataset to compare" entspricht dem rechten Eingabeport – in unserem Fall dem SVM-Modell. Wenn Sie auf eine dieser Beschriftungen klicken, werden die Kurve für das betreffende Modell markiert und die entsprechenden Metriken darunter angezeigt.

![ROC-Kurven für Modelle][4]

Wenn Sie diese Werte prüfen, können Sie entscheiden, welches Modell am ehesten den gewünschten Ergebnissen entspricht. Sie können zurückgehen und das Experiment erneut ausführen, indem Sie Werte in den verschiedenen Modellen ändern.

> [AZURE.TIP] Jedes Mal, wenn Sie das Experiment ausführen, wird im Ausführungsverlauf ein Dataset für diese Iteration aufbewahrt. Sie können die Iterationen anzeigen und zu jeder von ihnen zurückkehren, indem Sie unter dem Bereich auf **AUSFÜHRUNGSVERLAUF ANZEIGEN** klicken. Sie können auch auf **Vorherige Ausführung** im Fenster **Eigenschaften** klicken, um zur Iteration unmittelbar vor der geöffneten Iteration zurückzukehren. Sie können eine Kopie jeder Iteration des Experiments anfertigen, indem Sie unter dem Bereich auf **SAVE AS** klicken. Verwenden Sie die Eigenschaften **Summary** und **Description** des Experiments, um nachzuhalten, was Sie in den Iterationen Ihres Experiments versucht haben.

>  Weitere Informationen finden Sie unter [Verwalten von Experimentiterationen in Azure Machine Learning-Studio](machine-learning-manage-experiment-iterations.md).


----------

**Nächster Schritt: [Bereitstellen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)**

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
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0211_2016-->