<properties 
	pageTitle="Schritt 6: Zugreifen auf den Machine Learning-Webdienst | Microsoft Azure" 
	description="Exemplarische Vorgehensweise zur Entwicklung einer Vorhersagelösung – Schritt 6: Zugreifen auf einen aktiven Azure Machine Learning-Webdienst" 
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


# Anleitung Schritt 6: Zugreifen auf den Azure Machine Learning-Webdienst

Dies ist der letzte Teil der Anleitung [Entwickeln einer Vorhersagelösung mit Azure ML](machine-learning-walkthrough-develop-predictive-solution.md)


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Trainieren und Auswerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Veröffentlichen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Zugreifen auf den Webdienst**

----------

Damit ein Webdienst nützlich ist, müssen Benutzer in der Lage sein, Daten an ihn zu senden und Ergebnisse zu erhalten. Der Webdienst ist ein Azure-Webdienst, der Daten auf eine von zwei Arten empfangen und ausgeben kann:

-	**Anfrage/Antwort:** Der Benutzer sendet über das HTTP-Protokoll einen einzigen Satz an Kreditdaten an den Dienst, und dieser antwortet mit einem einzigen Satz an Ergebnissen.
-	**Batchausführung:** Der Benutzer sendet die URL eines Azure-Blobs an den Dienst, auf dem eine oder mehr Zeilen an Kreditdaten enthalten sind. Der Dienst speichert die Ergebnisse in einem anderen Blob und gibt die URL dieses Containers zurück.  

Auf der Registerkarte **DASHBOARD** dieses Webdiensts gibt es zwei Links zu Informationen, mit deren Hilfe Entwickler Code für den Zugriff auf diesen Dienst schreiben können. Klicken Sie auf den Link **API-Hilfeseite** in der Zeile **ANFRAGE/ANTWORT**, und es wird eine Seite geöffnet, die einen Beispielcode für das Verwenden des Anfrage/Antwort-Protokolls des Diensts enthält. In gleicher Weise liefert der Link **BATCHAUSFÜHRUNG** Beispielcode für das Durchführen einer Batchanforderung an den Dienst.

Die API-Hilfeseite enthält Beispiele für die Programmiersprachen R, C\# und Python.

Weitere Informationen zum Zugreifen auf und Verwenden des Webdienstes finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts, der von einem Machine Learning-Experiment aus veröffentlicht wurde](machine-learning-consume-web-services.md).

<!---HONumber=July15_HO5-->