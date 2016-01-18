<properties 
	pageTitle="Tutorial: Azure Stream Analytics und Azure Machine Learning-Integration | Microsoft Azure" 
	description="Verwenden von UDF und Machine Learning in Stream Analytics-Aufträgen"
	keywords=""
	documentationCenter=""
	services="stream-analytics"
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="12/30/2015" 
	ms.author="jeffstok"
/>

# Tutorial: Einführung in Stream Analytics und Machine Learning-Integration #

In diesem Tutorial soll Ihnen die schnelle Einrichtung eines einfachen Stream Analytics-Auftrags mit Machine Learning-Integration vermittelt werden. Wir nutzen ein Sentiment Analytics Machine Learning-Modell aus dem Cortana Analytics-Katalog, um Streamingtextdaten zu analysieren und den Stimmungswert (Sentiment Score) in Echtzeit zu ermitteln. Dieses Tutorial eignet sich gut zum Erlernen folgender Szenarien: Echtzeit-Stimmungsanalyse für Streaming-Twitter-Daten, Datensatzanalyse beim Kundenchat mit Support-Personal, Kommentare zu Foren/Blogs/Videos und viele andere Echtzeitszenarien im Bereich Predictive Scoring.
  
In diesem Tutorial wird eine CSV-Beispieldatei mit Text (siehe Abbildung 1 unten) als Eingabe in den Azure-Blob-Speicher bereitgestellt. Mit dem Auftrag wird das Sentiment Analytics-Modell als benutzerdefinierte Funktion (UDF) auf die Beispieltextdaten aus dem Blob-Speicher angewendet. Das Endergebnis wird in den gleichen Azure-Blob-Speicher in einer anderen CSV-Datei eingefügt. (Ein Diagramm mit dieser Konfiguration ist unten in Abbildung 2 angegeben.) Wenn Sie ein realistischeres Szenario wünschen, können Sie diese Blob-Speichereingabe durch Streaming-Twitter-Daten aus einer Azure Event Hub-Eingabe ersetzen. Außerdem kann eine [Power BI](https://powerbi.microsoft.com/)-Echtzeitvisualisierung der zusammengefassten Stimmung erstellt werden. Zukünftige Versionen dieses Artikels werden Erweiterungen dieser Art enthalten.

Abbildung 1:

![Stream Analytics Machine Learning-Tutorial – Abbildung 1](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)

Abbildung 2:

![Stream Analytics Machine Learning-Tutorial – Abbildung 2](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)

## Voraussetzungen

Für diesen Artikel gelten folgende Voraussetzungen:

1.	Ein aktives Azure-Abonnement
2.	Eine CSV-Datei mit einigen Daten. Sie können die Datei aus Abbildung 2 auf [GitHub](https://github.com/jeffstokes72/azure-stream-analytics-repository/blob/master/sampleinputs.csv) herunterladen oder eine eigene Datei erstellen. In diesem Tutorial wird vorausgesetzt, dass die als Download verfügbare Datei verwendet wird.

Die ausgeführten Schritte lassen sich grob wie folgt zusammenfassen:

1.	Hochladen der CSV-Eingabedatei in Blob Storage
2.	Hinzufügen eines Sentiment Analytics-Modells aus dem Cortana Analytics-Katalog in den Machine Learning-Arbeitsbereich
3.	Bereitstellen dieses Modells als Webdienst im Azure Machine Learning-Arbeitsbereich
4.	Erstellen eines Stream Analytics-Auftrags, mit dem dieser Webdienst als Funktion aufgerufen wird, um die Stimmung für die Texteingabe zu bestimmen
5.	Starten des Stream Analytics-Auftrags und Verfolgen der Ausgabe 


## Hochladen der CSV-Eingabedatei in Blob Storage

Für diesen Schritt können Sie eine beliebige CSV-Datei verwenden, z. B. die in der Einführung erwähnte Datei. Zum Hochladen der Datei können Sie den [Azure-Speicher-Explorer](http://storageexplorer.com/), Visual Studio oder auch benutzerdefinierten Code verwenden. In diesem Tutorial gelten die Beispiele für Visual Studio.

1.	Erweitern Sie Azure, und klicken Sie mit der rechten Maustaste auf **Storage**. Wählen Sie **Externen Speicher anfügen** aus, und geben Sie **Kontoname** und **Kontoschlüssel** an.  

    ![Stream Analytics Machine Learning-Tutorial – Server-Explorer](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)

2.	Erweitern Sie den gerade angefügten Speicher, und wählen Sie **Blob-Container erstellen**. Geben Sie dann einen logischen Namen an. Doppelklicken Sie nach der Erstellung auf den Container, um seinen Inhalt anzuzeigen (zu diesem Zeitpunkt leer).

    ![Stream Analytics Machine Learning-Tutorial – Blob erstellen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)

3.	Laden Sie die CSV-Datei hoch, indem Sie auf das Symbol **BLOB hochladen** klicken und dann **file from the local disk** auswählen.

## Hinzufügen des Sentiment Analytics-Modells aus dem Cortana Analytics-Katalog

1.	Laden Sie das [Predictive Experiment - Mini Twitter sentiment analysis](https://gallery.cortanaanalytics.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) aus dem Cortana Analytics-Katalog herunter.  
2.	Klicken Sie in Studio auf **Öffnen**:  

    ![Stream Analytics Machine Learning-Tutorial – Machine Learning Studio öffnen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)

3.	Melden Sie sich an, um zum Arbeitsbereich zu gelangen. Wählen Sie den Speicherort aus, der für Sie am besten geeignet ist.
4.	Klicken Sie dann unten in Studio auf **Ausführen**.  
5.	Klicken Sie nach der erfolgreichen Ausführung auf **Deploy Web Service**.
6.	Das Sentiment Analytics-Modell ist jetzt bereit für die Verwendung. Klicken Sie zum Überprüfen auf die Schaltfläche **Test**. Wenn Sie beispielsweise den Text „I love Microsoft“ eingeben, ähnelt das Ergebnis des Tests dieser Ausgabe:

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`

![Stream Analytics Machine Learning-Tutorial – Analysedaten](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)

Klicken Sie auf die Arbeitsmappenverknüpfung **Excel 2010 oder früher**, um Ihren API-Schlüssel und die URL abzurufen, die Sie später zum Einrichten des Stream Analytics-Auftrags benötigen. (Dieser Schritt ist nur erforderlich, um ein Machine Learning-Modell aus einem anderen Arbeitsbereich des Azure-Kontos zu nutzen. In diesem Tutorial wird davon ausgegangen, dass dies der Fall ist, und dieses Szenario wird veranschaulicht.)

![Stream Analytics Machine Learning-Tutorial – Analyseexperiment](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-experiement.png)

Notieren Sie sich die Webdienst-URL und den Zugriffsschlüssel aus den heruntergeladenen Excel-Daten wie unten gezeigt:

![Stream Analytics Machine Learning-Tutorial – Auf einen Blick](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)

## Erstellen eines Stream Analytics-Auftrags mit Nutzung des Machine Learning-Modells

1.	Navigieren Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com).  
2.	Klicken Sie auf **Neu**, **Data Services**, **Stream Analytics** und **Schnellerfassung**. Geben Sie den **Auftragsnamen** und die richtige **Region** für den Auftrag an, und wählen Sie ein **Speicherkonto für regionale Überwachung** aus.    
3.	Navigieren Sie nach der Erstellung des Auftrags zur Registerkarte **Eingaben**, und klicken Sie auf **Eingabe hinzufügen**.  

    ![Stream Analytics Machine Learning-Tutorial – Machine Learning-Eingabe hinzufügen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)

4.	Wählen Sie auf der ersten Seite des Assistenten **Eingabe hinzufügen** die Option **Datenstrom** aus, und klicken Sie auf „Weiter“. Wählen Sie auf der zweiten Seite **Blob Storage** als Eingabe aus, und klicken Sie auf **Weiter**.
5.	Geben Sie im Assistenten auf der Seite **Einstellungen des Blobspeichers** den Namen des Speicherkonto-Blob-Containers an, den Sie beim Hochladen der Daten definiert haben. Klicken Sie auf **Weiter**. Wählen Sie als **Ereignisserialisierungsformat** die Option **CSV** aus. Übernehmen Sie für die restlichen **Serialisierungseinstellungen** die Standardwerte. Klicken Sie auf **OK**.  
6.	Navigieren Sie zur Registerkarte **Ausgaben**, und klicken Sie auf **Ausgabe hinzufügen**.  

    ![Stream Analytics Machine Learning-Tutorial – Ausgabe hinzufügen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)

7.	Wählen Sie die Option **Blob Storage** und geben Sie mit Ausnahme des Containers die gleichen Parameter an. Die **Eingabe** wurde so konfiguriert, dass aus dem Container mit dem Namen „test“ gelesen wird, in den die **CSV**-Datei hochgeladen wurde. Geben Sie unter **Ausgabe** „testoutput“ ein. Die Containernamen müssen unterschiedlich sein. Stellen Sie sicher, dass dieser Container vorhanden ist.
8.	Klicken Sie auf **Weiter**, um die **Serialisierungseinstellungen** der Ausgabe zu konfigurieren. Wählen Sie wie bei der Eingabe die Option **CSV**, und klicken Sie auf die Schaltfläche **OK**.
9.	Navigieren Sie zur Registerkarte **Funktionen**, und klicken Sie auf **Add a Machine Learning Function**.  

    ![Stream Analytics Machine Learning-Tutorial – Machine Learning-Funktion hinzufügen](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)

10.	Suchen Sie auf der Seite **Machine Learning Web Service Settings** nach dem Machine Learning-Arbeitsbereich, dem Webdienst und dem Standardendpunkt. Wenden Sie die Einstellungen für dieses Tutorial manuell an, um sich mit der Konfiguration eines Webdiensts für einen Arbeitsbereich vertraut zu machen, wenn die URL und der Schlüssel bekannt sind. Geben Sie die **URL** und den **API-Schlüssel** für den Endpunkt an. Klicken Sie dann auf **OK**.

    ![Stream Analytics Machine Learning-Tutorial – Machine Learning-Webdienst](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)

11.	Navigieren Sie zur Registerkarte **Abfrage**, und ändern Sie die Abfrage wie folgt:

```
	WITH subquery AS (  
		SELECT text, sentiment(text) as result from input  
	)  
	  
	Select text, result.[Score]  
	Into output  
	From subquery  
```

12. Klicken Sie auf **Speichern**, um die Abfrage zu speichern.    

## Starten des Stream Analytics-Auftrags und Verfolgen der Ausgabe

1.	Klicken Sie unten im Auftrag auf **Starten**. 
2.	Wählen Sie im Dialogfeld **Abfrage starten** die Option **Benutzerdefinierte Uhrzeit**, und wählen Sie einen Zeitpunkt aus, der vor dem Hochladen der CSV-Datei in die Blob Storage-Einheit liegt. Klicken Sie auf **OK**.  

    ![Stream Analytics Machine Learning-Tutorial – Benutzerdefinierte Uhrzeit](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)

3.	Navigieren Sie mit dem Tool zur Blob Storage-Einheit, das Sie beim Hochladen der CSV-Datei verwendet haben. In diesem Tutorial wurde Visual Studio verwendet.
4.	Einige Minuten nach dem Starten des Auftrags wird der Ausgabecontainer erstellt und eine CSV-Datei in den Container hochgeladen.  
5.	Wenn Sie auf die Datei doppelklicken, wird der standardmäßige CSV-Editor geöffnet und enthält in etwa Folgendes:  

    ![Stream Analytics Machine Learning-Tutorial – CSV-Ansicht](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)

## Zusammenfassung

In diesem Tutorial wurde ein Stream Analytics-Auftrag erstellt, mit dem Streamingtextdaten gelesen werden und ein Sentiment Analytics-Prozess dafür in Echtzeit durchgeführt wird. Dies alles war möglich, ohne dass Sie sich mit der komplexen Erstellung eines Sentiment Analytics-Modells beschäftigen mussten. Dies ist einer der Vorteile der Cortana Analytics Suite.

Sie können sich auch die auf die Azure Machine Learning-Funktion bezogenen Metriken ansehen. Klicken Sie auf die Registerkarte **ÜBERWACHEN**. Sie sehen drei funktionsbezogene Metriken:
  
- FUNKTIONSANFORDERUNGEN gibt die Anzahl von Anforderungen an den Machine Learning-Webdienst an.  
- FUNKTIONSEREIGNISSE gibt die Anzahl von Ereignissen in der Anforderung an. Standardmäßig enthält jede Anforderung an einen ML-Webdienst bis zu 1.000 Ereignisse.  
- FEHLGESCHLAGENE FUNKTIONSANFORDERUNGEN gibt die Anzahl von fehlgeschlagenen Anforderungen an den Machine Learning-Webdienst an.  

    ![Stream Analytics Machine Learning-Tutorial – ML-Überwachungsansicht](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)

<!---HONumber=AcomDC_0107_2016-->