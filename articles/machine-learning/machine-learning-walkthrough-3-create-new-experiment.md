<properties
	pageTitle="Schritt 3: Erstellen eines neuen Machine Learning-Experiments | Microsoft Azure"
	description="Exemplarische Vorgehensweise zum Entwickeln einer Vorhersagelösung – Schritt 3: Erstellen eines neuen Trainingsexperiments in Azure Machine Learning Studio."
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


# Exemplarische Vorgehensweise, Schritt 3: Erstellen eines neuen Azure Machine Learning-Experiments

Dies ist der dritte Schritt der exemplarischen Vorgehensweise zum [Entwickeln einer Predictive Analytics-Lösung in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	**Erstellen eines neuen Experiments**
4.	[Trainieren und Bewerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Bereitstellen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

----------

Der nächste Schritt in dieser exemplarischen Vorgehensweise ist die Erstellung eines neuen Experiments in Machine Learning Studio, welches das von uns hochgeladene Dataset verwendet.

1.	Klicken Sie in Studio unten auf der Seite auf **+NEW**.
2.	Wählen Sie **EXPERIMENT** und anschließend "Blank Experiment" aus. Wählen Sie oben in der Canvas den Namen des Standardexperiments aus, und geben Sie einen aussagekräftigen Namen ein.

	> [AZURE.TIP] Es ist eine bewährte Methode, die Felder **Zusammenfassung** und **Beschreibung** für das Experiment im Bereich **Eigenschaften** auszufüllen. Diese Eigenschaften geben Ihnen die Möglichkeit, das Experiment zu dokumentieren. Wenn Benutzer später darauf zugreifen, können sie Ihre Ziele und die Methodik verstehen.

3.	Erweitern Sie in der Modulpalette links vom Experimentbereich **Gespeicherte Datasets**.
4.	Suchen Sie das Dataset, das Sie unter **Meine Datasets** erstellt haben, und ziehen Sie es in den Bereich. Sie können auch nach dem Dataset suchen, indem Sie den Namen in das Feld **Suchen** oberhalb der Palette eingeben.  

##Vorbereiten der Daten
Sie können die ersten 100 Datenzeilen sowie einige statistische Informationen für das ganze Dataset anzeigen, indem Sie auf den Ausgabeport des Datasets (den kleinen Kreis unten) klicken und die Option **Visualize** auswählen.

Da die Datendatei keine Spaltenüberschriften aufweist, hat Studio allgemeine Überschriften (Col1, Col2 *usw.*) bereitgestellt. Aussagekräftige Überschriften haben keine Bedeutung für die Erstellung eines Modells, erleichtern aber die Arbeit mit den Daten im Experiment. Wenn später das Modell in einem Webdienst veröffentlicht wird, kann der Benutzer des Diensts die Spalten anhand der Überschriften auch leichter identifizieren.

Verwenden Sie das Modul [Metadaten-Editor][metadata-editor], um Spaltenüberschriften hinzuzufügen. Sie verwenden das Modul [Metadata Editor][metadata-editor] zum Ändern der Metadaten, die einem Dataset zugeordnet sind. In diesem Fall kann es aussagekräftigere Namen für die Spaltenüberschriften bereitstellen.

Zum Verwenden von [Metadata Editor][metadata-editor] geben Sie zuerst an, welche Spalten geändert werden sollen (in diesem Fall alle). Dann geben Sie die Aktion an, die auf diese Spalten angewendet werden soll (in diesem Fall das Ändern der Spaltenüberschriften).

1.	Geben Sie in der Modulpalette „Metadaten“ in das Feld **Suchen** ein. [Metadata Editor][metadata-editor] wird in der Modulliste angezeigt.
2.	Klicken und ziehen Sie das Modul [Metadata Editor][metadata-editor] in den Experimentbereich, und legen Sie es unter dem zuvor hinzugefügten Dataset ab.
3.	Verbinden Sie das Dataset mit dem [Metadata Editor][metadata-editor]\: Klicken Sie auf den Ausgabeport des Datasets (den kleinen Kreis unten im Dataset), ziehen Sie ihn zum Eingabeport von [Metadata Editor][metadata-editor] (den kleinen Kreis oben im Modul), und lassen Sie die Maustaste los. Das Dataset und das Modul bleiben verbunden, auch wenn Sie diese im Bereich verschieben.

    Das Experiment sollte in etwa wie folgt aussehen:

    ![Hinzufügen des Metadaten-Editors][2]
    
    Das rote Ausrufezeichen gibt an, dass wir noch nicht die Eigenschaften für dieses Modul festgelegt haben. Das machen wir als Nächstes.
    
    > [AZURE.TIP] Sie können einen Kommentar zu einem Modul eingeben, indem Sie auf das Modul doppelklicken und Text eingeben. Auf diese Weise können Sie mit einem Blick sehen, welche Funktion das Modul in Ihrem Experiment erfüllt. Doppelklicken Sie hierfür auf das Modul [Metadata Editor][metadata-editor], und geben Sie den Kommentar „Spaltenüberschriften hinzufügen“ ein. Klicken Sie auf eine Stelle des Experimentbereichs, um das Textfeld zu schließen. Klicken Sie auf den nach unten zeigenden Pfeil für das Modul, um den Kommentar anzuzeigen.

4.	Klicken Sie bei ausgewähltem [Metadata Editor][metadata-editor] im Bereich **Eigenschaften** rechts neben dem Experimentbereich auf **Launch column selector**.
5.	Legen Sie im Dialogfeld **Select columns** das Feld **Begin With** auf „All columns“ fest.
6.	In der Zeile unterhalb von **Begin With** können Sie bestimmte Spalten für die Bearbeitung durch [Metadata Editor][metadata-editor] ein- oder ausschließen. Da in diesem Beispiel *alle* Spalten bearbeitet werden sollen, löschen Sie diese Zeile, indem Sie auf das Minuszeichen (-) rechts neben der Zeile klicken. Das Dialogfeld sollte wie folgt aussehen: ![Spaltenauswahl, alle Spalten ausgewählt][4]
7.	Klicken Sie auf das Häkchen **OK**.
8.	Suchen Sie im Bereich **Properties** nach dem Parameter **New column name**. Geben Sie in diesem Feld eine Liste der Namen für die 21 Spalten im Dataset ein, durch Kommas getrennt und in der Reihenfolge der Spalten. Die Spaltennamen können Sie der Datasetdokumentation auf der UCI-Website entnehmen, oder Sie können einfach die folgende Liste kopieren und einfügen:  

		  Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  

    Der Eigenschaftenbereich sieht folgendermaßen aus:

    ![Eigenschaften für Metadaten-Editor][1]

> [AZURE.TIP] Wenn Sie die Spaltenüberschriften überprüfen möchten, führen Sie das Experiment aus (klicken Sie unter dem Experimentbereich auf **RUN**). Wenn die Ausführung abgeschlossen ist (in [Metadata Editor][metadata-editor] wird ein grünes Häkchen angezeigt), klicken Sie auf den Ausgabeport des Moduls [Metadata Editor][metadata-editor], und wählen Sie **Visualize** aus. Auf die gleiche Weise können Sie die Ausgabe jedes anderen Moduls anzeigen, um den Datenfortschritt im Experiment zu sehen.

##Erstellen von Trainings- und Testdatasets
Der nächste Schritt des Experiments besteht im Erstellen getrennter Datasets, die für das Trainieren und Testen unseres Modells verwendet werden.

Dafür wird das Modul [Split Data][split] verwendet.

1.	Suchen Sie das Modul [Split Data][split], ziehen Sie es in den Experimentbereich, und verbinden Sie es mit dem letzten Modul [Metadata Editor][metadata-editor].
2.	Standardmäßig beträgt das Aufteilungsverhältnis 0,5, und der Parameter **Randomized split** ist festgelegt. Das bedeutet, dass eine zufällig ausgewählte Hälfte der Daten über einen Port des Moduls [Split Data][split] ausgegeben wird, die andere Hälfte über den anderen Port. Sie können diese Werte und den Parameter **Random seed** anpassen, um die Aufteilung zwischen Trainings- und Bewertungsdaten zu ändern. Für dieses Beispiel bleiben die Werte unverändert.
	> [AZURE.TIP] Die Eigenschaft **Fraction of rows in the first output dataset** bestimmt, welcher Anteil der Daten über den linken Ausgabeport ausgegeben werden. Wenn Sie z. B. ein Aufteilungsverhältnis von 0,7 festlegen, werden 70 % der Daten über den linken Port und 30 % der Daten über den rechten Port ausgegeben.  
3. Doppelklicken Sie auf das Modul [Split Data][split], und geben Sie den Kommentar „Aufteilung zwischen Trainings- und Testdaten 50 %“ ein. 

Die Ausgaben des Moduls [Split Data][split] können beliebig verwendet werden. In diesem Fall wählen wir die linke Ausgabe als Trainingsdaten und die rechte Ausgabe als Testdaten.

Wie auf der UCI-Website erwähnt, sind die Kosten einer Fehlklassifizierung eines hohen Risikos als niedriges Risiko fünf Mal höher als die Kosten einer Fehlklassifizierung eines niedrigen Risikos als hohes Risiko. Um dies zu berücksichtigen, generieren Sie ein neues Dataset, das diese Kostenfunktion darstellt. Im neuen Dataset wird jedes Hochrisikobeispiel fünf Mal repliziert, während keines der Niedrigrisikobeispiele repliziert wird.

Für diese Replikation kann der R-Code verwendet werden:

1.	Suchen Sie im Experimentbereich das Modul [Execute R Script][execute-r-script], und verbinden Sie den linken Ausgabeport des Moduls [Split Data][split] mit dem ersten Eingabeport („Dataset1“) des Moduls [Execute R Script][execute-r-script].
2. Doppelklicken Sie auf das Modul [Execute R Script][execute-r-script], und geben Sie den Kommentar „Kostenanpassung festlegen“ ein.
2.	Löschen Sie im Bereich mit den **Eigenschaften** den Standardtext im Parameter **R Script**, und geben Sie dieses Skript ein:

		  dataset1 <- maml.mapInputPort(1)
		  data.set<-dataset1[dataset1[,21]==1,]
		  pos<-dataset1[dataset1[,21]==2,]
		  for (i in 1:5) data.set<-rbind(data.set,pos)
		  maml.mapOutputPort("data.set")


Wir müssen den gleichen Replikationsvorgang für jede Ausgabe des Moduls [Split Data][split] durchführen, damit die Trainings- und die Testdaten die gleiche Kostenanpassung haben.

1.	Klicken Sie mit der rechten Maustaste auf das Modul [R-Skript ausführen][execute-r-script], und wählen Sie **Kopieren** aus.
2.	Klicken Sie mit der rechten Maustaste in den Experimentbereich, und wählen Sie **Einfügen** aus.
3.	Verbinden Sie den ersten Eingabeport dieses Moduls [Execute R Script][execute-r-script] mit dem rechten Ausgabeport des Moduls [Split Data][split].  

> [AZURE.TIP] Die Kopie des Moduls "Execute R Script" enthält das gleiche Skript wie das Originalmodul. Wenn Sie ein Modul kopieren und im Bereich einfügen, behält die Kopie alle Eigenschaften des Originals bei.

Unser Experiment sieht nun in etwa wie folgt aus:

![Hinzufügen des Aufteilungsmoduls und der R-Skripte][3]

Weitere Informationen zum Verwenden von R-Skripts in Ihren Experimenten finden Sie unter [Erweitern Sie Ihr Experiment mit R](machine-learning-extend-your-experiment-with-r.md).

**Nächster Schritt: [Trainieren und Auswerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

<!---HONumber=AcomDC_0211_2016-->