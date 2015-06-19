<properties 
	pageTitle="Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen | Azure" 
	description="Erfahren Sie, wie Sie ein Modell programmgesteuert erneut trainieren und den Webdienst aktualisieren, sodass er das neu trainierte Modell in Azure Azure Machine Learning verwendet." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services" 
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/22/2015"
	ms.author="raymondl;garye"/>


#Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen  
 
Im Rahmen der Operationalisierung der Modelle für das maschinelle Lernen in Azure Machine Learning muss ein Modell trainiert und gespeichert und anschließend zur Erstellung eines Bewertungswebdiensts verwendet werden. Der Webdienst kann dann in Websites, Dashboards und mobilen Apps genutzt werden.

Häufig müssen Sie das Modell, das Sie im ersten Schritt erstellt haben, mit den neuen Daten erneut trainieren. Früher war dies nur über die Azure ML-Benutzeroberfläche möglich, aber mit der Einführung der Programmatic Retraining-API, können Sie jetzt mithilfe der Retraining-APIs ein Modell programmgesteuert erneut trainieren und den Webdienst aktualisieren, sodass er das neu trainierte Modell verwendet.

In diesem Dokument wird der obige Prozess beschrieben und gezeigt, wie Sie die Retraining-APIs verwenden.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)] 
 

##Warum erneut trainieren: Problemdefinition  
Im Rahmen des ML-Trainingsprozesses wird ein Modell mit einem Satz von Daten trainiert. Die Modelle muss in Szenarien neu trainiert werden, in denen neue Daten verfügbar werden oder in denen der Nutzer der API über eigene Daten zum Trainieren des Modells verfügt oder in denen die Daten gefiltert werden müssen und das Modell mit einer Teilmenge der Daten trainiert werden muss.

In diesen Szenarien bietet eine programmgesteuerte API eine komfortable Möglichkeit, mit der Sie oder die Nutzer Ihrer APIs einen Client erstellen können, der das Modell einmalig oder regelmäßig unter Verwendung eigener Daten trainieren kann. Sie können die Ergebnisse des erneuten Trainings dann auswerten und die Webdienst-API aktualisieren, sodass sie das trainierte Modell verwendet.

##Erneutes Trainieren: Prozess  
Der Prozess beinhaltet die folgenden Komponenten: ein Trainingsexperiment und ein Bewertungsexperiment, die als Webdienst veröffentlicht werden. Damit ein trainiertes Modells erneut trainiert werden kann, muss das Trainingsexperiment als Webdienst mit der Ausgabe eines trainierten Modells veröffentlicht werden. Dies ermöglicht der API, zum erneuten Trainieren auf das Modell zuzugreifen. Das Einrichten eines erneuten Trainings umfasst die folgenden Schritte:

![][1]
 
Diagramm 1: Übersicht über den Prozess des erneuten Trainings

1. *Erstellen eines Trainingsexperiments*  
	Wir verwenden hier das Experiment "Sample 5 (Train, Test, Evaluate for Binary Classification: Adult Dataset)" aus den Azure-ML-Beispielexperimenten als Beispiel. Wie Sie unten sehen können, haben ich das Beispiel etwas vereinfacht, indem ich einige Module entfernt habe. Zudem habe ich das Experiment "Census Model" genannt.

 	![][2]

	Nachdem wir diese Komponenten haben, können wir am unteren Bildschirmrand auf "Run" (Ausführen) klicken, um dieses Experiment auszuführen.  
2. *Erstellen eines Bewertungsexperiments und Veröffentlichen des Bewertungsexperiments als Webdienst*  
	
	![][3]

	Nachdem die Ausführung des Experiments beendet ist, klicken wir auf "Create Scoring Experiment" (Bewertungsexperiment erstellen). Wie unten dargestellt, wird daraufhin ein Bewertungsexperiment erstellt. Das Modell wird als trainiertes Modell gespeichert, und es werden Ein- und Ausgabemodule für den Webdienst hinzugefügt. Als Nächstes klicken wir auf "Run" (Ausführen).

	Nach Abschluss der Ausführung des Experiments wird das Bewertungsexperiment durch Klicken auf "Publish Web Service" (Webdienst veröffentlichen) veröffentlicht und ein Standardendpunkt erstellt. Das trainierte Modell in diesem Webdienst ist aktualisierbar, wie unten dargestellt. Die Details für diesen Endpunkt werden dann auf dem Bildschirm angezeigt.  
3. *Veröffentlichen des Trainingsexperiments als Webdienst*  
	Damit das trainierte Modell erneut trainiert werden kann, müssen wir das Trainingsexperiment, das wir in Schritt 1 erstellt haben, als Webdienst veröffentlichen. Dieser Webdienst benötigt ein Webdienst-Ausgabemodul, das mit dem Modul [Train Model][train-model] verbunden ist, um neue trainierte Modelle erzeugen zu können. Klicken Sie auf das Symbol für Experimente im linken Bereich, und klicken Sie dann auf das Experiment namens "Census Model", um wieder zum Trainingsexperiment zurückzukehren.  

	Wir fügen dem Workflow dann ein Webdienst-Eingabemodul und zwei Webdienst-Ausgabemodule hinzu. Die Webdienst-Ausgabe für "Train Model" liefert uns das neue trainierte Modell. Das mit dem Modul "Evaluate Model" verknüpfte Ausgabemodul gibt die Ausgabe dieses Moduls für das Bewertungsmodell zurück.

	Wir können jetzt auf "Run" (Ausführen) klicken. Nachdem die Ausführung des Experiments abgeschlossen wurde, sollte der resultierende Workflow folgendermaßen aussehen:
 
	![][4]

	Wir als Nächstes klicken wir auf die Schaltfläche "Publish Web Service" (Webdienst veröffentlichen) und dann auf "Yes" (Ja). Dadurch wird das Trainingsexperiment als Webdienst veröffentlicht, der trainierte Modelle und Modellbewertungsergebnisse erzeugt. Das Webdienst-Dashboard wird mit dem API-Schlüssel und der API-Hilfeseite für die Batchausführung angezeigt. Beachten Sie, dass nur die Methode Batch Execution (Batchausführung) zum Erstellen von trainierten Modelle verwendet werden kann.  
4. *Hinzufügen eines neuen Endpunkts*  
	Der Bewertungswebdienst, den wir oben in Schritt 2 veröffentlicht haben, wurde mit einem Standardendpunkt erstellt. Die Standardendpunkte werden mit dem ursprünglichen Experiment synchronisiert, und daher kann das trainierte Modell, das einem Standardendpunkt zugeordnet ist, nicht ersetzt werden. Zum Erstellen eines aktualisierbaren Endpunkts besuchen Sie das Azure-Portal, und klicken Sie auf "Add Endpoint" (Endpunkt hinzufügen) (nähere Informationen finden Sie [hier](machine-learning-create-endpoint.md)).	
5. *Erneutes Trainieren des Modells mit neuen Daten und BES*  
	Zum Aufrufen der Retraining-APIs erstellen wir eine neue C#-Konsolenanwendung in Visual Studio (Neu -> Projekt -> Windows Desktop -> Konsolenanwendung).  

	Dann kopieren wir den C#-Beispielcode aus der Hilfeseite des Trainingswebdiensts-API für die Batchausführung (der in Schritt 3 oben erstellt wurde) und fügen ihn in die Datei "Program.cs" ein, wobei wir sicherstellen, dass der Namespace intakt bleibt.

	Beachten Sie, dass der Beispielcode Kommentare enthält, welche auf die Teile des Codes hinweisen, die aktualisiert werden müssen.

	1. Bereitstellen von Azure-Speicherinformationen Der Beispielcode für BES lädt eine Datei von einem lokalen Laufwerk (z. B. "C:\\temp\\CensusIpnput.csv") in den Azure-Speicher zur Verarbeitung hoch und schreibt die Ergebnisse wieder in den Azure-Speicher.  

		Dazu müssen Sie Informationen zum Namen, Schlüssel und Container des Speicherkontos aus dem Azure-Verwaltungsportal für Ihr Speicherkonto abrufen und den vorliegenden Code entsprechend aktualisieren. Sie müssen auch sicherstellen, dass die Eingabedatei an dem im Code angegebenen Speicherort verfügbar ist.

		Da wir dieses Trainingsexperiment mit zwei Ausgabemodulen einrichten mussten, müssen auch die Ergebnisse Speicherortinformationen für beide enthalten (siehe  unten). "output1" ist die Ausgabe des trainierten Modells, und "output2" ist die Ausgabe des Bewertungsmodells.

		![][6]
 
6. *Auswerten der Ergebnisse des erneuten Trainings*  
	Unter Verwendung der Kombination von BaseLocation, RelativeLocaiton und SasBlobToken aus den oben genannten Ausgabeergebnissen für "output2" können wir die Ergebnisse des erneut trainierten Modells anzeigen, indem wir die vollständige URL in die Adressleiste des Browsers einfügen.

	Daran können wir erkennen, ob das neu trainierte Modell gut genug ist, um das vorhandene Modell zu ersetzen.

7. *Aktualisieren des trainierten Modells für den hinzugefügten Endpunkt*  
	Um den Prozess abzuschließen, müssen wir das trainierte Modell des oben in Schritt 4 erstellten Bewertungsendpunkts aktualisieren.

	Die oben genannten BES-Ausgabe zeigt die Informationen für das Ergebnis des erneuten Trainings für "output1", das die Speicherortinformationen für das erneut trainierte Modell enthält. Nun müssen dieses trainierte Modell nehmen und den Bewertungsendpunkt (der in Schritt 4 erstellt wurde) aktualisieren.

	![][7]
  
	Im Endpunkt-Dashboard werden die Werte für "apiKey" und "endpointUrl" für diesen Aufruf angezeigt.

	Nachdem dieser Aufruf erfolgreich ausgeführt wurde, beginnt der neue Endpunkt innerhalb von etwa 15 Sekunden, das neu trainierte Modell zu verwenden.

##Zusammenfassung  
Mithilfe der Retraining-APIs können wir das trainierte Modell eines Vorhersagewebdiensts aktualisieren, sodass Szenarien möglich sind, in denen Modelle regelmäßig mit neuen Daten trainiert werden oder Modelle in der Absicht an Kunden verteilt werden, sie das Modell unter Verwendung ihrer eigenen Daten trainieren zu lassen.

[1]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE01.png
[2]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE02.png
[3]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE03.png
[4]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE04.png
[5]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE05.png
[6]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE06.png
[7]: ./media/machine-learning-retrain-models-programmatically/machine-learning-retrain-models-programmatically-IMAGE07.png


<!-- Module References -->
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

<!--HONumber=54--> 