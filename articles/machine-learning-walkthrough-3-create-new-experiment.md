<properties title="Step 3: Create a new Azure Machine Learning experiment" pageTitle="Step 3: Create a new Machine Learning experiment | Azure" description="Step 3: Create a new training experiment in Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

Dies ist der dritte Teil der exemplarischen Vorgehensweise [Entwickeln einer Vorhersagelösung mit Azure ML][Entwickeln einer Vorhersagelösung mit Azure ML]:

1.  [Erstellen eines ML-Arbeitsbereichs][Erstellen eines ML-Arbeitsbereichs]
2.  [Hochladen vorhandener Daten][Hochladen vorhandener Daten]
3.  **Erstellen eines neuen Experiments**
4.  [Trainieren und Bewerten der Modelle][Trainieren und Bewerten der Modelle]
5.  [Veröffentlichen des Webdiensts][Veröffentlichen des Webdiensts]
6.  [Zugreifen auf den Webdienst][Zugreifen auf den Webdienst]

----------

# Schritt 3: Erstellen eines neuen Azure Machine Learning-Experiments

In ML Studio muss ein neues Experiment erstellt werden, in dem das hochgeladene Dataset verwendet wird.

1.  Klicken Sie in ML Studio unten in der Seite auf **+NEU**.
2.  Wählen Sie **EXPERIMENT** aus.
3.  Erweitern Sie in der Modulpalette links vom Experimentbereich **Gespeicherte Datasets**.
4.  Suchen Sie das Dataset, das Sie erstellt haben, und ziehen Sie es in den Bereich. Sie können auch nach dem Dataset suchen, indem Sie den Namen in das Feld **Suchen** oberhalb der Palette eingeben.

## Vorbereiten der Daten
Sie können die ersten 100 Datenzeilen sowie einige statistische Informationen für das ganze Dataset anzeigen, indem Sie mit der rechten Maustaste auf den Ausgabeport des Datasets klicken und die Option **Visualisieren** auswählen. Beachten Sie, dass in ML Studio bereits der Datentyp für jede Spalte identifiziert wurde. Zudem haben die Spalten generische Überschriften erhalten, da die Datendatei keine Spaltenüberschriften enthält.

Spaltenüberschriften haben keine entscheidende Bedeutung, erleichtern aber die Arbeit mit den Daten im Modell. Wenn später das Modell in einem Webdienst veröffentlicht wird, kann der Benutzer des Diensts die Spalten anhand der Überschriften auch leichter identifizieren.

Verwenden Sie das Modul **Metadaten-Editor**, um Spaltenüberschriften hinzuzufügen.

1. Geben Sie in der Modulpalette „Metadaten“ in das Feld **Suchen** ein. Der **Metadaten-Editor** wird in der Modulliste angezeigt.
2. Klicken Sie, und ziehen Sie das Modul **Metadaten-Editor** in den Bereich, um es unter dem Dataset abzulegen.
3. Verbinden Sie das Dataset mit dem **Metadaten-Editor**: Klicken Sie auf den Ausgabeport des Datasets, ziehen Sie ihn zum Eingabeport des **Metadaten-Editors**, und lassen Sie die Maustaste los. Das Dataset und das Modul bleiben verbunden, auch wenn Sie diese im Bereich verschieben.
4. Klicken Sie im Bereich **Eigenschaften** rechts neben dem Bereich auf **Spaltenauswahl starten**.
5. Überprüfen Sie, dass „Alle Spalten“ in der Dropdown-Liste ausgewählt ist, und klicken Sie auf **OK**. So wird der **Metadaten-Editor** angewiesen, alle Datenspalten zu bearbeiten.
6. Geben Sie für den Parameter **Neuer Spaltenname** eine Liste der Namen für die 21 Spalten im Dataset ein, getrennt durch Kommata und in der Reihenfolge der Spalten. Die Spaltennamen können Sie der Datasetdokumentation auf der UCI-Website entnehmen, oder Sie können einfach Folgendes kopieren und einfügen:

    Status des Girokontos, Dauer in Monaten, Bonitätsgeschichte, Zweck, Kredithöhe, Sparkonto/Wertpapiere, Derzeitige Beschäftigung seit, Rate als Prozentsatz des verfügbaren Einkommens, Personenstand und Geschlecht, Andere Schuldner, Derzeitiger Wohnsitz seit, Eigentum, Alter in Jahren, Weitere Ratenpläne, Wohnungen, Anzahl vorhandener Kredite, Arbeitsstelle, Anzahl der Personen, für deren Unterhalt gesorgt wird, Telefon, Ausländischer Arbeitnehmer, Kreditrisiko

Der Eigenschaftenbereich sieht folgendermaßen aus:

![Eigenschaften für Metadaten-Editor][Eigenschaften für Metadaten-Editor]

>Tipp - Wenn Sie die Spaltenüberschriften überprüfen möchten, führen Sie das Experiment aus (klicken Sie unterhalb des Experimentbereichs auf **AUSFÜHREN**), klicken Sie mit der rechten Maustaste auf den Ausgabeport des Moduls **Metadaten-Editor**, und wählen Sie die Option **Visualisieren** aus. Auf die gleiche Weise können Sie die Ausgabe jedes anderen Moduls anzeigen, um den Datenfortschritt im Experiment zu sehen.

Das Experiment sollte in etwa wie folgt aussehen:

![Hinzufügen des Metadaten-Editors][Hinzufügen des Metadaten-Editors]

## Erstellen von Trainings- und Testdatasets
Der nächste Schritt des Experiments besteht im Erstellen von getrennten Datasets, die für das Training und Testen unseres Modells verwendet werden. Dafür wird das Modul **Aufteilen** verwendet.

1.  Suchen Sie das Modul **Aufteilen**, ziehen Sie es in den Bereich, und verbinden Sie es mit dem letzten Modul **Metadaten-Editor**.
2.  Standardmäßig beträgt das Aufteilungsverhältnis 0,5, und der Parameter **Zufällige Aufteilung** ist festgelegt. Das bedeutet, dass eine zufällig ausgewählte Hälfte der Daten über einen Port des Moduls **Aufteilen** ausgegeben wird, die andere Hälfte über den anderen Port. Sie können diese Werte und den Parameter **Zufälliger Ausgangswert** anpassen, um die Aufteilung zwischen Trainings- und Bewertungsdaten zu ändern. Für dieses Beispiel bleiben die Werte unverändert.
	>Tipp: Das Aufteilungsverhältnis bestimmt im Wesentlichen, wie viele der Daten über den linken Ausgabeport ausgegeben werden. Wenn Sie z. B. ein Aufteilungsverhältnis von 0,7 festlegen, werden 70 % der Daten über den linken Port und 30 % der Daten über den rechten Port ausgegeben.

Die Ausgaben des Moduls **Aufteilen** können beliebig verwendet werden. In diesem Fall wählen wir, die linke Ausgabe als Trainingsdaten und die rechte Ausgabe als Bewertungsdaten zu verwenden.

Wie auf der UCI-Website erwähnt, sind die Kosten einer Fehlklassifizierung eines hohen Risikos als niedriges Risiko fünf Mal höher als die Kosten einer Fehlklassifizierung eines niedrigen Risikos als hohes Risiko. Um dies zu berücksichtigen, generieren wir ein neues Dataset, das diese Kostenfunktion darstellt. In dem neuen Dataset wird jedes Hochrisikobeispiel fünf Mal repliziert, während jedes Niedrigrisikobeispiel nicht repliziert wird.

Für diese Replikation kann der R-Code verwendet werden:

1. Suche Sie das Modul **R-Skript ausführen**, und ziehen Sie es in den Experimentbereich. Verbinden Sie es mit dem linken Ausgabeport des Moduls **Aufteilen**.
2. Löschen Sie im Bereich **Eigenschaften** den Standardtext im Parameter **Skript**, und geben Sie dieses Skript ein:

		dataset1 <- maml.mapInputPort(1)
		data.set<-dataset1[dataset1[,21]==1,]
		pos<-dataset1[dataset1[,21]==2,]
		for (i in 1:5) data.set<-rbind(data.set,pos)
		maml.mapOutputPort("data.set")


Wir müssen den gleichen Replikationsvorgang für jede Ausgabe des Moduls **Aufteilen** durchführen, damit die Trainings- und die Bewertungsdaten die gleiche Kostenanpassung haben.

1. Klicken Sie mit der rechten Maustaste auf das Modul **R-Skript ausführen**, und wählen Sie **Kopieren** aus.
2. Klicken Sie mit der rechten Maustaste in den Experimentbereich, und wählen Sie **Einfügen** aus.
3. Verbinden Sie dieses Modul **R-Skript ausführen** mit dem rechten Ausgabeport des Moduls **Aufteilen**.

>Tipp - Die Kopie des Moduls „R-Skript ausführen“ enthält das gleiche Skript wie das Originalmodul. Wenn Sie ein Modul kopieren und im Bereich einfügen, behält die Kopie alle Eigenschaften des Originals bei.
>
Unser Experiment sieht nun in etwa wie folgt aus:

![Hinzufügen des Aufteilungsmoduls und der R-Skripte][Hinzufügen des Aufteilungsmoduls und der R-Skripte]

[Entwickeln einer Vorhersagelösung mit Azure ML]: ../machine-learning-walkthrough-develop-predictive-solution/
[Erstellen eines ML-Arbeitsbereichs]: ../machine-learning-walkthrough-1-create-ml-workspace/
[Hochladen vorhandener Daten]: ../machine-learning-walkthrough-2-upload-data/
[Trainieren und Bewerten der Modelle]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[Veröffentlichen des Webdiensts]: ../machine-learning-walkthrough-5-publish-web-service/
[Zugreifen auf den Webdienst]: ../machine-learning-walkthrough-6-access-web-service/
[Eigenschaften für Metadaten-Editor]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png
[Hinzufügen des Metadaten-Editors]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png
[Hinzufügen des Aufteilungsmoduls und der R-Skripte]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png
