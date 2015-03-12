<properties 
	pageTitle="Schritt 2: Hochladen von Daten in ein Azure Machine Learning-Experiment | Azure" 
	description="Exemplarische Vorgehensweise Schritt 2: Hochladen vorhandener öffentlicher Daten in Azure Machine Learning Studio" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="garye"/>

Dies ist der zweite Teil der Anleitung [Entwickeln einer Vorhersagelösung mit Azure ML][develop]:

[develop]: ../machine-learning-walkthrough-develop-predictive-solution/


1.	[Erstellen eines ML-Arbeitsbereichs][create-workspace]
2.	**Hochladen vorhandener Daten**
3.	[Erstellen eines neuen Experiments][create-new]
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

# Schritt 2: Hochladen vorhandener Daten in ein Azure Machine Learning-Experiment  

Um ein Vorhersagemodell für Kreditrisiken zu entwickeln, verwenden wir den Datensatz "UCI Statlog (German Credit Data)" aus dem UCI Machine Learning-Repository. Sie finden es hier:  
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Sie verwenden die Datei mit dem Namen **german.data**. Laden Sie die Datei auf Ihre lokale Festplatte herunter.  

Dieser Datensatz enthält Zeilen mit 20 Variablen für 1.000 Kreditantragsteller aus der Vergangenheit. Diese 20 Variablen stellen den Funktionsvektor des Datensatzes dar, der Idenfikationseigenschaften für die einzelnen Kreditantragsteller enthält. Eine zusätzliche Spalte in jeder Zeile enthält das Kreditrisiko der Antragsteller. 700 der Antragsteller wurden mit niedrigem Risiko klassifiziert, und 300 mit hohem Risiko.   

Auf der UCI-Website finden Sie eine Beschreibung der Attribute des Funktionsvektors. Enthalten sind z. B. finanzielle Informationen, Bonitätsgeschichte, Beschäftigungsstatus und persönliche Daten. Für jeden Antragsteller wurde eine binäre Bewertung vergeben, um zwischen niedrigem und hohem Kreditrisiko zu unterscheiden.  

Wir werden unser Vorhersageanalytikmodell anhand dieser Daten trainieren. Anschließend akzeptiert unser Modell Daten für neue Personen und kann vorhersagen, ob diese ein niedriges oder hohes Kreditrisiko haben.  

Hier ist ein interessantes Extra. In der Beschreibung des Datensatzes wird erläutert, dass die Fehlklassifizierung einer Person, die eigentlich ein hohes Kreditrisiko hat, mit niedrigem Kreditrisiko, 5 mal so teuer für die Finanzinstitution ist als eine Fehlklassifizierung in der Gegenrichtung. Wir können dies in unserem Experiment berücksichtigen, indem wir die Einträge von Personen mit hohem Kreditrisiko 5 mal duplizieren. Wenn das Modell anschließend ein hohes Kreditrisiko fälschlicherweise als niedrig klassifiziert, wird diese Fehlklassifizierung 5 mal ausgeführt, ein mal pro Duplikat. Auf diese Weise werden die Kosten für diesen Fehler in den Trainingsergebnissen erhöht.  

## Konvertieren des Datensatzformats
Der Originaldatensatz verwendet ein Format mit Trennung durch Leerzeichen. ML Studio funktioniert besser mit Dateien mit Kommatrennung (CSV). Daher werden wir den Datensatz konvertieren, indem wir die Leerzeichen durch Kommas ersetzen.  

Dazu können Sie den folgenden Windows PowerShell-Befehl verwenden:   

	cat german.data | %{$_ -replace " ",","} | sc german.csv  

Alternativ können Sie den folgenden sed-Befehl unter Unix verwenden:  

	sed 's/ /,/g' german.data > german.csv  

## Hochladen des Datensatzes in ML Studio

Nach der Konvertierung der Daten in das CSV-Format müssen Sie die Daten in ML Studio hochladen.  

1.	Klicken Sie in ML Studio unten in der Seite auf **+NEU**.
2.	Wählen Sie **DATASET**.
3.	Klicken Sie auf **AUS LOKALER DATEI**.
4.	Klicken Sie im Dialogfeld **Neuen Datensatz hochladen** auf **Durchsuchen**, und suchen Sie nach der zuvor erstellten Datei **german.csv**.
5.	Geben Sie einen Namen für das Dataset ein. In diesem Beispiel nennen wir es "UCI German Credit Card Data".
6.	Wählen Sie unter Datentyp "Generische CSV-Datei ohne Kopfzeile (.nh.csv)" aus.
7.	Fügen Sie bei Bedarf eine Beschreibung hinzu.
8.	Klicken Sie auf **OK**.  

![Upload the dataset][1]  

 
Daraufhin werden die Daten in ein Datensatzmodul hochgeladen, das wir in einem Experiment verwenden können.

**Nächster Schritt: [Erstellen eines neuen Experiments][create-new]**

[1]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png

<!--HONumber=46--> 
