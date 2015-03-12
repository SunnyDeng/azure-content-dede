<properties 
	pageTitle="Schritt 3: Erstellen eines neuen Machine Learning-Experiments | Azure" 
	description="Exemplarische Vorgehensweise Schritt 3: Erstellen eines neuen Trainingsexperiments in Azure Machine Learning Studio" 
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


Dies ist der dritte Teil der exemplarischen Vorgehensweise [Entwickeln einer Vorhersagelösung mit Azure ML][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Erstellen eines ML-Arbeitsbereichs][create-workspace]
2.	[Hochladen vorhandener Daten][upload-data]
3.	**Erstellen eines neuen Experiments**
4.	[Trainieren und Bewerten der Modelle][train-models]
5.	[Veröffentlichen des Webdiensts][publish]
6.	[Zugreifen auf den Webdienst][access-ws]

[create-workspace]: ../machine-learning-walkthrough-1-create-ml-workspace/
[upload-data]: ../machine-learning-walkthrough-2-upload-data/
[create-new]: ../machine-learning-walkthrough-3-create-new-experiment/
[train-models]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[publish]: ../machine-learning-walkthrough-5-publish-web-service/
[access-ws]: ../machine-learning-walkthrough-6-access-web-service/

----------

# Schritt 3: Erstellen eines neuen Azure Machine Learning-Experiments

In ML Studio muss ein neues Experiment erstellt werden, in dem das hochgeladene Dataset verwendet wird.  

1.	Klicken Sie in ML Studio unten in der Seite auf **+NEU**.
2.	Wählen Sie **EXPERIMENT**.
3.	Erweitern Sie in der Modulpalette links vom Experimentbereich **Gespeicherte Datasets**.
4.	Suchen Sie das Dataset, das Sie erstellt haben, und ziehen Sie es in den Bereich. Sie können auch nach dem Dataset suchen, indem Sie den Namen in das Feld **Suchen** oberhalb der Palette eingeben.  

## Vorbereiten der Daten
Sie können die ersten 100 Datenzeilen sowie einige statistische Informationen für das ganze Dataset anzeigen, indem Sie mit der rechten Maustaste auf den Ausgabeport des Datasets klicken und die Option **Visualisieren** auswählen. Beachten Sie, dass in ML Studio bereits der Datentyp für jede Spalte identifiziert wurde. Zudem haben die Spalten generische Überschriften erhalten, da die Datendatei keine Spaltenüberschriften enthält.  

Spaltenüberschriften haben keine entscheidende Bedeutung, erleichtern aber die Arbeit mit den Daten im Modell. Wenn später das Modell in einem Webdienst veröffentlicht wird, kann der Benutzer des Diensts die Spalten anhand der Überschriften auch leichter identifizieren.  

Verwenden Sie das Modul **Metadaten-Editor**, um Spaltenüberschriften hinzuzufügen.  

1.	Geben Sie in der Modulpalette "Metadaten" in das Feld **Suchen** ein. Der **Metadaten-Editor** wird in der Modulliste angezeigt.
2.	Klicken Sie, und ziehen Sie das Modul **Metadaten-Editor** in den Bereich, um es unter dem Dataset abzulegen.
3.	Verbinden Sie das Dataset mit dem **Metadaten-Editor**: Klicken Sie auf den Ausgabeport des Datasets, ziehen Sie ihn zum Eingabeport des **Metadaten-Editors**, und lassen Sie die Maustaste los. Das Dataset und das Modul bleiben verbunden, auch wenn Sie diese im Bereich verschieben.
4.	Klicken Sie im Bereich **Eigenschaften** rechts neben dem Bereich auf **Spaltenauswahl starten**.
5.	Stellen Sie sicher, dass im Feld **Beginnen mit** "Alle Spalten" ausgewählt ist, entfernen Sie alle Zeilen und klicken Sie auf **OK**. So wird der **Metadaten-Editor** angewiesen, alle Datenspalten zu bearbeiten.
6.	Geben Sie für den Parameter **Neuer Spaltenname** eine Liste der Namen für die 21 Spalten im Dataset ein, getrennt durch Kommata und in der Reihenfolge der Spalten. Die Spaltennamen können Sie der Datasetdokumentation auf der UCI-Website entnehmen, oder Sie können einfach Folgendes kopieren und einfügen:  

	Status des Girokontos, Dauer in Monaten, Bonitätsgeschichte, Zweck, Kredithöhe, Sparkonto/Wertpapiere, Derzeitige Beschäftigung seit, Rate als Prozentsatz des verfügbaren Einkommens, Personenstand und Geschlecht, Andere Schuldner, Derzeitiger Wohnsitz seit, Eigentum, Alter in Jahren, Weitere Ratenpläne, Wohnungen, Anzahl vorhandener Kredite, Arbeitsstelle, Anzahl der Personen, für deren Unterhalt gesorgt wird, Telefon, Ausländischer Arbeitnehmer, Kreditrisiko  

Der Eigenschaftenbereich sieht folgendermaßen aus:

![Properties for Metadata Editor][1] 

>Tipp: Wenn Sie die Spaltenüberschriften überprüfen möchten, führen Sie das Experiment aus (klicken Sie unterhalb des Experimentbereichs auf **AUSFÜHREN**), klicken Sie mit der rechten Maustaste auf den Ausgabeport des Moduls **Metadaten-Editor**, und wählen Sie die Option **Visualisieren** aus. Auf die gleiche Weise können Sie die Ausgabe jedes anderen Moduls anzeigen, um den Datenfortschritt im Experiment zu sehen.

Das Experiment sollte in etwa wie folgt aussehen:  

![Adding Metadata Editor][2]
 
## Erstellen von Trainings- und Testdatasets
Der nächste Schritt des Experiments besteht im Erstellen von getrennten Datasets, die für das Training und Testen unseres Modells verwendet werden. Dafür wird das Modul **Aufteilen** verwendet.  

1.	Suchen Sie das Modul **Aufteilen**, ziehen Sie es in den Bereich, und verbinden Sie es mit dem letzten Modul **Metadaten-Editor**.
2.	Standardmäßig beträgt das Aufteilungsverhältnis 0,5, und der Parameter **Zufällige Aufteilung** ist festgelegt. Das bedeutet, dass eine zufällig ausgewählte Hälfte der Daten über einen Port des Moduls **Aufteilen** ausgegeben wird, die andere Hälfte über den anderen Port. Sie können diese Werte und den Parameter **Zufälliger Ausgangswert** anpassen, um die Aufteilung zwischen Trainings- und Bewertungsdaten zu ändern. Für dieses Beispiel bleiben die Werte unverändert.
	>Tipp: Das Aufteilungsverhältnis bestimmt im Wesentlichen, wie viele der Daten über den linken Ausgabeport ausgegeben werden. Wenn Sie z. B. ein Aufteilungsverhältnis von 0,7 festlegen, werden 70 % der Daten über den linken Port und 30 % der Daten über den rechten Port ausgegeben.  
	
Die Ausgaben des Moduls **Aufteilen** können beliebig verwendet werden. In diesem Fall wählen wir, die linke Ausgabe als Trainingsdaten und die rechte Ausgabe als Bewertungsdaten zu verwenden.  

Wie auf der UCI-Website erwähnt, sind die Kosten einer Fehlklassifizierung eines hohen Risikos als niedriges Risiko fünf Mal höher als die Kosten einer Fehlklassifizierung eines niedrigen Risikos als hohes Risiko. Um dies zu berücksichtigen, generieren Sie ein neues Dataset, das diese Kostenfunktion darstellt. In dem neuen Dataset wird jedes Hochrisikobeispiel fünf Mal repliziert, während jedes Niedrigrisikobeispiel nicht repliziert wird.   

Für diese Replikation kann der R-Code verwendet werden:  

1.	Suchen Sie das Modul **R-Skript ausführen**, und ziehen Sie es in den Experimentbereich. Verbinden Sie es mit dem linken Ausgabeport des Moduls **Aufteilen**.
2.	Löschen Sie im Bereich **Eigenschaften** den Standardtext im Parameter Skript, und geben Sie dieses **Skript** ein: 

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


Wir müssen den gleichen Replikationsvorgang für jede Ausgabe des Moduls **Aufteilen** durchführen, damit die Trainings- und die Bewertungsdaten die gleiche Kostenanpassung haben.

1.	Klicken Sie mit der rechten Maustaste auf das Modul **R-Skript ausführen**, und wählen Sie **Kopieren** aus.
2.	Klicken Sie mit der rechten Maustaste in den Experimentbereich, und wählen Sie **Einfügen** aus.
3.	Verbinden Sie dieses Modul **R-Skript ausführen** mit dem rechten Ausgabeport des Moduls **Aufteilen**.  

>Tipp: Die Kopie des Moduls "R-Skript ausführen" enthält das gleiche Skript wie das Originalmodul. Wenn Sie ein Modul kopieren und im Bereich einfügen, behält die Kopie alle Eigenschaften des Originals bei.  
>
Unser Experiment sieht nun in etwa wie folgt aus:
 
![Adding Split module and R scripts][3]

**Nächster Schritt: [Trainieren und Bewerten der Modelle][train-models]**


[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png

<!--HONumber=46--> 
