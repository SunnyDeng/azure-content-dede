<properties title="Step 6: Access the Azure Machine Learning web service" pageTitle="Step 6: Access the Machine Learning web service | Azure" description="Step 6: Access an active Azure Machine Learning API web service" metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

Dies ist der letzte Teil der exemplarischen Vorgehensweise [Entwickeln einer Vorhersagelösung mit Azure ML][Entwickeln einer Vorhersagelösung mit Azure ML]:

1.  [Erstellen eines ML-Arbeitsbereichs][Erstellen eines ML-Arbeitsbereichs]
2.  [Hochladen vorhandener Daten][Hochladen vorhandener Daten]
3.  [Erstellen eines neuen Experiments][Erstellen eines neuen Experiments]
4.  [Trainieren und Bewerten der Modelle][Trainieren und Bewerten der Modelle]
5.  [Veröffentlichen des Webdiensts][Veröffentlichen des Webdiensts]
6.  **Zugreifen auf den Webdienst**

----------

# Schritt 6: Zugreifen auf den Azure Machine Learning-Webdienst

Damit der Webdienst nützlich ist, müssen Benutzer in der Lage sein, Daten an den Dienst zu senden und Ergebnisse zu erhalten. Der Webdienst ist ein Azure-Webdienst, der Daten auf eine von zwei Weisen empfangen und ausgeben kann:

- **Anfrage/Antwort** – Der Benutzer sendet über das HTTP-Protokoll einen einzigen Satz an Kreditdaten an den Dienst und dieser antwortet mit einem einzigen Satz an Ergebnissen.
- **Batchausführung** – Der Benutzer sendet die URL eines Azure-BLOBs an den Dienst, auf dem eine oder mehr Zeilen an Kreditdaten enthalten sind. Der Dienst speichert die Ergebnisse in einem anderen BLOB und gibt die URL dieses Containers zurück.

Auf der Registerkarte **DASHBOARD** dieses Webdiensts gibt es zwei Links zu Informationen, mit deren Hilfe Entwickler Code für den Zugriff auf diesen Dienst schreiben können. Klicken Sie den Link **API-Hilfeseite** in der Zeile **ANFRAGE/ANTWORT**, und es wird eine Seite geöffnet, die einen Beispielcode für das Verwenden des Anfrage/Antwort-Protokoll des Diensts enthält. In gleicher Weise liefert der Link **BATCHAUSFÜHRUNG** Beispielcode für das Durchführen einer Batchanforderung an den Dienst.

Die API-Hilfeseite enthält Beispiele für die Programmiersprachen R, C# und Python. Hier ist beispielsweise der von uns veröffentlichte R-Code, den Sie für den Zugriff auf den Webdienst verwenden sollten (die tatsächliche URL wird in Ihrem Beispielcode angezeigt):

	library("RCurl")
	library("RJSONIO")
	
	h = basicTextGatherer()
	req = list(Id="score00001",
	 Instance=list(FeatureVector=list(
	    "row.names"= "0",
	    "Status of checking account"= "0",
	    "Duration in months"= "0",
	    "Credit history"= "0",
	    "Purpose"= "0",
	    "Credit amount"= "0",
	    "Savings account/bond"= "0",
	    "Present employment since"= "0",
	    "Installment rate in percentage of disposable income"= "0",
	    "Personal status and sex"= "0",
	    "Other debtors"= "0",
	    "Present residence since"= "0",
	    "Property"= "0",
	    "Age in years"= "0",
	    "Other installment plans"= "0",
	    "Housing"= "0",
	    "Number of existing credits"= "0",
	    "Job"= "0",
	    "Number of people providing maintenance for"= "0",
	    "Telephone"= "0",
	    "Foreign worker"= "0",
	    "Credit risk"= "0"
	 ),GlobalParameters=fromJSON('{}')))
	
	body = toJSON(req)
	api_key = "abc123" # You can obtain the API key from the publisher of the web service
	
	h$reset()
	curlPerform(url = "http://xxx.cloudapp.net/workspaces/xxx/services/xxx/score",
	            httpheader=c('Content-Type' = "application/json", 'Authorization' = "Bearer " + api_key),
	            postfields=body,
	            writefunction = h$update,
	            verbose = TRUE
	            )
	
	result = h$value()



[Entwickeln einer Vorhersagelösung mit Azure ML]: ../machine-learning-walkthrough-develop-predictive-solution/
[Erstellen eines ML-Arbeitsbereichs]: ../machine-learning-walkthrough-1-create-ml-workspace/
[Hochladen vorhandener Daten]: ../machine-learning-walkthrough-2-upload-data/
[Erstellen eines neuen Experiments]: ../machine-learning-walkthrough-3-create-new-experiment/
[Trainieren und Bewerten der Modelle]: ../machine-learning-walkthrough-4-train-and-evaluate-models/
[Veröffentlichen des Webdiensts]: ../machine-learning-walkthrough-5-publish-web-service/
