<properties title="Step 2: Upload existing data into an Azure Machine Learning experiment" pageTitle="Step 2: Upload data into a Machine Learning experiment | Azure" description="Step 2: Upload existing public data into Azure Machine Learning Studio" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

Dies ist der zweite Teil der Anleitung [Entwickeln einer Vorhersagelösung mit Azure ML][Entwickeln einer Vorhersagelösung mit Azure ML]:

1.  [Erstellen eines ML-Arbeitsbereichs][Erstellen eines ML-Arbeitsbereichs]
2.  **Hochladen vorhandener Daten**
3.  [Erstellen eines neuen Experiments][Erstellen eines neuen Experiments]
4.  [Trainieren und Bewerten der Modelle][Trainieren und Bewerten der Modelle]
5.  [Veröffentlichen des Webdiensts][Veröffentlichen des Webdiensts]
6.  [Zugreifen auf den Webdienst][Zugreifen auf den Webdienst]

------------------------------------------------------------------------

# Schritt 2: Hochladen vorhandener Daten in ein Azure Machine Learning-Experiment

Um ein Vorhersagemodell für Kreditrisiken zu entwickeln, verwenden wir den Datensatz "UCI Statlog (German Credit Data)" aus dem UCI Machine Learning-Repository. Sie finden den Datensatz unter dieser URL:
<http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)>

Wir werden die Datei mit dem Namen **german.data** verwenden. Laden Sie die Datei auf Ihre lokale Festplatte herunter.

Dieser Datensatz enthält Zeilen mit 20 Variablen für 1.000 Kreditantragsteller aus der Vergangenheit. Diese 20 Variablen stellen den Funktionsvektor des Datensatzes dar, der Idenfikationseigenschaften für die einzelnen Kreditantragsteller enthält. Eine zusätzliche Spalte in jeder Zeile enthält das Kreditrisiko der Antragstellet. 700 der Antragsteller wurden mit niedrigem Risiko klassifiziert, und 300 mit hohem Risiko.

Auf der UCI-Website finden Sie eine Beschreibung der Attribute des Funktionsvektors. Enthalten sind z. B. finanzielle Informationen, Bonitätsgeschichte, Beschäftigungsstatus und persönliche Daten. Für jeden Antragstellet wurde eine binäre Bewertung vergeben, um zwischen niedrigem und hohem Kreditrisiko zu unterscheiden.

Wir werden unser Vorhersageanalytikmodell anhand dieser Daten trainieren. Anschließend akzeptiert unser Modell Daten für neue Personen und kann vorhersagen, ob diese ein niedriges oder hohes Kreditrisiko haben.

Hier ist ein interessantes Extra. In der Beschreibung des Datensatzes wird erläutert, dass die Fehlklassifizierung einer Person, die eigentlich ein hohes Kreditrisiko hat, mit niedrigem Kreditrisiko, 5 mal so teuer für die Finanzinstitution ist als eine Fehlklassifizierung in der Gegenrichtung. Wir können dies in unserem Experiment berücksichtigen, indem wir die Einträge von Personen mit hohem Kreditrisiko 5 mal duplizieren. Wenn das Modell anschließend ein hohes Kreditrisiko fälschlicherweise als niedrig klassifiziert, wird diese Fehlklassifizierung 5 mal ausgeführt, ein mal pro Duplikat. Auf diese Weise werden die Kosten für diesen Fehler in den Trainingsergebnissen erhöht.

## Konvertieren des Datensatzformats

Der Originaldatensatz verwendet ein Format mit Trennung durch Leerzeichen. ML Studio funktioniert besser mit Dateien mit Kommatrennung (CSV). Daher werden wir den Datensatz konvertieren, indem wir die Leerzeichen durch Kommas ersetzen.

Dazu können Sie den folgenden PowerShell-Befehl verwenden:

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Alternativ können Sie den folgenden sed-Befehl unter Unix verwenden:

    sed 's/ /,/g' german.data > german.csv  

## Hochladen des Datensatzes in ML Studio

Nach der Konvertierung der Daten in das CSV-Format müssen Sie die Daten in ML Studio hochladen.

1.  Klicken Sie in ML Studio unten in der Seite auf **+NEU**
2.  Klicken Sie auf **DATENSATZ**
3.  Klicken Sie auf **AUS LOKALER DATEI**
4.  Klicken Sie im Dialogfeld **Neuen Datensatz hochladen** auf **Durchsuchen** und suchen Sie nach der zuvor erstellten Datei **german.csv**
5.  Geben Sie einen Namen für den Datensatz ein. Für dieses Beispiel verwenden wir den Namen "UCI German Credit Card Data"
6.  Wählen Sie unter Datentyp "Generische CSV-Datei ohne Kopfzeile (.nh.csv)" aus
7.  Fügen Sie bei Bedarf eine Beschreibung hinzu
8.  Klicken Sie auf **OK**
    ![Hochladen des Datensatzes][Hochladen des Datensatzes]

Daraufhin werden die Daten in ein Datensatzmodul hochgeladen, das wir in einem Experiment verwenden können.

  [Entwickeln einer Vorhersagelösung mit Azure ML]: ../machine-learning-walkthrough-develop-predictive-solution/
  [Erstellen eines ML-Arbeitsbereichs]: ../machine-learning-walkthrough-1-create-ml-workspace/
  [Erstellen eines neuen Experiments]: ../machine-learning-walkthrough-3-create-new-experiment/
  [Trainieren und Bewerten der Modelle]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
  [Veröffentlichen des Webdiensts]: ../machine-learning-walkthrough-5-publish-web-service/
  [Zugreifen auf den Webdienst]: ../machine-learning-walkthrough-6-access-web-service/
  [Hochladen des Datensatzes]: ./media/machine-learning-walkthrough-2-upload-data/upload1.png
