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
	ms.date="02/03/2016"
	ms.author="garye"/>


# Anleitung Schritt 6: Zugreifen auf den Azure Machine Learning-Webdienst

Dies ist der letzte Schritt der exemplarischen Vorgehensweise zum [Entwickeln einer Predictive Analytics-Lösung mit Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Trainieren und Bewerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	[Bereitstellen des Webdiensts](machine-learning-walkthrough-5-publish-web-service.md)
6.	**Zugreifen auf den Webdienst**

----------

Im vorherigen Schritt in dieser exemplarischen Vorgehensweise haben wir einen Webdienst bereitgestellt, der unser Vorhersagemodell für Kreditrisiken verwendet. Nun müssen Benutzer in der Lage sein, Daten an ihn zu senden und Ergebnisse zu erhalten.

Der Webdienst ist ein Azure-Webdienst, der Daten auf eine von zwei Arten über REST-APIs empfangen und zurückgeben kann:

-	**Anfrage/Antwort:** Der Benutzer sendet über das HTTP-Protokoll eine oder mehrere Zeilen Kreditdaten an den Dienst, und dieser antwortet mit einem oder mehreren Sätzen von Ergebnissen.
-	**Batchausführung:** Der Benutzer speichert Zeilen von Kreditdaten in einem Azure-Blob und sendet den Speicherorts des Blobs dann an den Dienst. Der Dienst bewertet alle Datenzeilen im Eingabeblob, speichert die Ergebnisse in einem anderen Blob und gibt die URL dieses Containers zurück.  

Die schnellste und einfachste Möglichkeit, auf den Webdienst zuzugreifen, bieten die Web-App-Vorlagen, die im [Azure Marketplace für Web-Apps](https://azure.microsoft.com/marketplace/web-applications/all/) verfügbar sind. Die Web-App-Vorlagen können eine benutzerdefinierte Web-App erstellen, der die Eingabedaten und die zurückgegebenen Ergebnisse des Webdiensts bekannt sind. Dafür müssen Sie nur Zugriff auf den Webdienst und die Daten gewähren, und die Vorlage übernimmt den Rest.

Weitere Informationen zur Verwendung von Web-App-Vorlagen finden Sie unter [Verwenden eines Azure Machine Learning-Webdiensts mit einer Web-App-Vorlage](machine-learning-consume-web-service-with-web-app-template.md).

Sie können auch eine benutzerdefinierte Anwendung entwickeln, die mithilfe von in den Programmiersprachen R, C# und Python bereitgestelltem Startcode auf den Webdienst zugreift. Vollständige Details finden Sie unter [Gewusst wie: Nutzen eines Azure Machine Learning-Webdiensts, der von einem Machine Learning-Experiment aus veröffentlicht wurde](machine-learning-consume-web-services.md).

<!---HONumber=AcomDC_0211_2016-->