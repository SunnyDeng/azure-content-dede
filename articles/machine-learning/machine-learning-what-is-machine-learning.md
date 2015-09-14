<properties
    pageTitle="Was ist Machine Learning in Azure ? | Microsoft Azure"
	description="Dieser Artikel erläutert die grundlegenden Konzepte des vollständig verwalteten Machine Learning-Diensts, eine Cloudtechnologie, mit der Sie Lösungen erstellen, operationalisieren und vermarkten können."
	services="machine-learning"
	documentationCenter=""
	authors="cjgronlund"
	manager="neerajkh"
	editor="cgronlun"/>

<tags
    ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="cgronlun;tedway;olgali"/>


# Einführung in das maschinelle Lernen in Microsoft Azure

## Was ist maschinelles Lernen?

Maschinelle Lernprozesse verwenden Computer, um Vorhersagemodelle auszuführen, die aus vorhandenen Daten lernen, um zukünftige Verhaltensweisen, Ergebnisse und Trends vorherzusagen.

Dank solcher Vorhersagen oder Prognosen aus maschinellen Lernprozessen können Apps und Geräte "intelligenter" werden. Wenn Sie online einkaufen, trägt maschinelles Lernen dazu bei, dass Ihnen anhand der gekauften Produkte weitere Produkte empfohlen werden, die Ihnen gefallen könnten. Wenn Ihre Kreditkarte verwendet wird, vergleichen maschinelle Lernprozesse die Transaktion mit einer Transaktionsdatenbank und helfen der Bank bei der Betrugserkennung.

## Was ist Microsoft Azure Machine Learning?

Azure Machine Learning ist ein leistungsfähiger cloudbasierter Predictive Analytics-Dienst, der die schnelle Erstellung und Bereitstellung von Vorhersagemodellen als Analyselösungen ermöglicht.

Azure Machine Learning bietet nicht nur Tools zur Modellierung von Predictive Analytics-Lösungen, sondern auch einen vollständig verwalteten Dienst, über den Sie Ihre Vorhersagemodelle als sofort nutzbare Webdienste veröffentlichen können. Azure Machine Learning bietet Tools zum Erstellen vollständiger Predictive Analytics-Lösungen in der Cloud: Erstellen, testen, operationalisieren und verwalten Sie Vorhersagemodelle schnell und mühelos. Sie müssen weder Hardware kaufen noch virtuelle Computer manuell verwalten.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Was ist Predictive Analytics?

Predictive Analytics verwendet verschiedene statistische Verfahren – in diesem Fall maschinelle Lernprozesse –, um gesammelte oder aktuelle Daten auf Muster oder Trends zu untersuchen und anschließend zukünftige Ereignisse vorhersagen zu können.

Azure Machine Learning ist eine besonders leistungsstarke Predictive Analytics-Lösung: Sie können mit einer sofort nutzbaren Algorithmusbibliothek arbeiten, Modelle auf einem mit dem Internet verbundenen Computer erstellen – ohne weitere Geräte oder Infrastruktur erwerben zu müssen – und Ihre Vorhersagelösung schnell bereitstellen. Im [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) und dem [Machine Learning-Katalog](http://gallery.azureml.net/) finden Sie sofort nutzbare Beispiele und Lösungen.

## Entwickeln von vollständigen Lösungen für das maschinelle Lernen in der Cloud

Azure Machine Learning bietet alles, was Sie zum Erstellen von Predictive Analytics-Lösungen in der Cloud benötigen – eine große Algorithmusbibliothek, ein Studio zum Entwickeln von Modellen sowie eine einfache Möglichkeit, Ihr Modell als Webdienst bereitzustellen.

### Machine Learning Studio: Erstellen von Vorhersagemodellen

Erstellen Sie Vorhersagemodelle in [Machine Learning Studio](machine-learning-what-is-ml-studio.md), einem browserbasierten Tool, indem Sie Module per Drag & Drop platzieren und miteinander verbinden.

![Was ist Predictive Analytics? Beispiel eines Predictive Analytics-Experiments in Azure Machine Learning Studio](./media/machine-learning-what-is-machine-learning/azure-machine-learning-studio-predictive-score-experiment.png)

* Nutzen Sie die umfangreiche Bibliothek mit [Algorithmen und Modulen zum maschinellen Lernen](https://msdn.microsoft.com/library/azure/f5c746fd-dcea-4929-ba50-2a79c4c067d7) in Machine Learning Studio, um sofort mit dem Entwickeln von Vorhersagemodellen zu beginnen. Wählen Sie aus einer Bibliothek mit Beispielexperimenten, R- und Python-Paketen sowie erstklassigen Algorithmen aus Microsoft-Produktbereichen wie Xbox und Bing aus. Erweitern Sie Studio-Module mit Ihren eigenen benutzerdefinierten [R](machine-learning-r-quickstart.md)- und [Python](machine-learning-execute-python-scripts.md)-Skripts.
* Der [Katalog der Machine Learning-Community](machine-learning-gallery-how-to-use-contribute-publish.md) macht Benutzern den Einstieg in Azure Machine Learning leichter und ermöglicht ihnen, von anderen in der Community zu lernen. Probieren Sie Experimente anderer Benutzer aus, stellen Sie Fragen, posten Sie Kommentare zu Experimenten, oder veröffentlichen Sie Ihre eigenen Experimente. Sie können auch Links zu Experimenten über soziale Netzwerke wie z. B. LinkedIn und Twitter teilen.  

	![Probieren Sie Beispiele für Vorhersageanalyse-Experimente aus, oder ergänzen Sie die Azure Machine Learning-Galerie durch eigene Experimente](./media/machine-learning-what-is-machine-learning/azure-machine-learning-gallery-resources.png)

### Operationalisieren von Predictive Analytics-Lösungen: Erwerben von Webdiensten oder Veröffentlichen eigener Dienste

* Erwerben Sie im [Microsoft Azure Marketplace](https://datamarket.azure.com/browse?query=machine+learning) sofort nutzbare Webdienste z. B. für Empfehlungen, Textanalysen und Anomalieerkennung.

* Operationalisieren Sie Ihre Predictive Analytics-Modelle:
    * [Veröffentlichen von Webdiensten](machine-learning-publish-a-machine-learning-web-service.md)
    * [Trainieren und erneutes Trainieren von Modellen über APIs](machine-learning-retrain-models-programmatically.md)
    * [Verwalten von Webdienstendpunkten](machine-learning-create-endpoint.md)
    * [Skalieren von Webdiensten](machine-learning-scaling-endpoints.md)
    * [Nutzen von Webdiensten](machine-learning-consume-web-services.md)

## Die wichtigsten Begriffe und Konzepte des maschinellen Lernens
### Durchsuchen von Daten, beschreibende Analyse und Predictive Analytics

Beim **Durchsuchen von Daten** werden Informationen über ein umfangreiches und oft unstrukturiertes Dataset erfasst, um Merkmale für eine gezielte Analyse zu ermitteln. Der Begriff **Data Mining** bezieht sich auf das automatisierte Durchsuchen von Daten.

Bei der **beschreibenden Analyse** wird ein Dataset analysiert, um Vorgänge zusammenzufassen. Bei den weitaus meisten Business Analytics-Prozessen – z. B. Verkaufsberichten, Webmetriken und Analysen sozialer Netzwerke – handelt es sich um beschreibende Analysen.

Bei der **Predictive Analytics** werden Modelle basierend auf vergangenen oder aktuellen Daten entwickelt, um zukünftige Ergebnisse vorhersagen zu können.


### Überwachtes und nicht überwachtes Lernen
 Algorithmen für das **überwachte Lernen** werden mit bezeichneten Daten trainiert, also mit Daten, die aus Beispielen der gewünschten Antworten bestehen. Ein Modell zur Identifizierung von betrügerischer Kreditkartennutzung würde beispielsweise mit einem Dataset trainiert, in dem Datenpunkte für bekannte betrügerische und gültige Buchungen bezeichnet wurden. Die meisten maschinellen Lernprozesse sind überwacht.

 **Nicht überwachte Lernprozesse** werden bei Daten ohne Bezeichnungen verwendet. Das Ziel hierbei ist es, Beziehungen zwischen den Daten zu finden. Ein Beispiel hierfür ist das Ermitteln von Kundengruppen mit ähnlichem Kaufverhalten.

### Trainieren und Auswerten des Modells
Ein Modell zum maschinellen Lernen ist eine Abstraktion der Frage, die Sie beantworten möchten, oder des Ergebnisses, das Sie vorhersagen möchten. Modelle werden anhand vorhandener Daten trainiert und ausgewertet.

#### Trainieren anhand von Daten
In Azure Machine Learning wird ein Modell aus einem Algorithmusmodul entwickelt, das Trainingsdaten und funktionale Module, wie z. B. ein Bewertungsmodul, verarbeitet.

Wenn Sie ein Betrugserkennungsmodell in einem überwachten Lernprozess trainieren, verwenden Sie einen Satz Transaktionen, die entweder als betrügerisch oder als gültig bezeichnet sind. Sie teilen Ihr Dataset nach dem Zufallsprinzip und verwenden einen Teil zum Trainieren und einen Teil zum Testen oder Auswerten des Modells.

#### Auswertungsdaten
Nachdem Sie das Modell trainiert haben, werden Sie es mithilfe der verbleibenden Testdaten aus. Hierzu verwenden Sie Daten, deren Ergebnisse Sie bereits kennen, um herauszufinden, ob die Vorhersage Ihres Modells genau ist.

### Weitere gängige Begriffe des maschinellen Lernens

* **Algorithmus**: Ein eigenständiger Regelsatz, der zum Lösen von Problemen mithilfe von Datenverarbeitung, Berechnungen oder automatisierter Argumentation verwendet wird.
* **Kategorische Daten**: Daten, die nach Kategorien organisiert sind und in Gruppen unterteilt werden können. Ein kategorisches Dataset für Fahrzeuge könnte z. B. Jahr, Marke, Modell und Preis angeben.
* **Klassifizierung**: Ein Modell für die Einordnung von Datenpunkten in Kategorien, basierend auf einem Dataset, für das die Kategoriegruppierungen bereits bekannt sind.
* **Featureentwicklung**: Der Prozess des Extrahierens oder Auswählens von Features in Zusammenhang mit einem Dataset, um das Dataset zu erweitern und die Ergebnisse zu verbessern. Flugpreisdaten könnten z. B. durch Wochentage und Ferien erweitert werden. Siehe [Entwicklung und Auswahl von Features in Azure Machine Learning](machine-learning-feature-selection-and-engineering.md).
* **Modul**: Ein Funktionselement in einem Machine Learning Studio-Modell, wie beispielsweise das Modul zur Dateneingabe, das die Eingabe und Bearbeitung kleiner Datasets ermöglicht. Auch bei einem Algorithmus handelt es sich um eine Art Modul in Machine Learning Studio.
* **Modell**: Beim überwachten Lernen ist ein Modell das Produkt eines Maschinenlernexperiments, das aus einem Trainingsdataset, einem Algorithmusmodul und Funktionsmodulen – wie z. B. einem Bewertungsmodellmodul – besteht.
* **Numerische Daten**: Daten, die eine Bedeutung als Messung (kontinuierliche Daten) oder Zählung (diskrete Daten) haben. Diese Daten werden auch als *quantitative Daten* bezeichnet.
* **Partitionieren**: Die Methode, mit der Sie Daten in Stichproben unterteilen. Weitere Informationen finden Sie unter [Partition and Sample](https://msdn.microsoft.com/library/azure/dn905960.aspx).
* **Vorhersage**: Eine Vorhersage ist die Prognose eines oder mehrerer Werte aus einem Maschinenlernmodell. Ihnen wird möglicherweise auch der Begriff "vorhergesagte Bewertung" begegnen, hierbei handelt es sich jedoch nicht um die finalen Ergebnisse eines Modells. Der Bewertung folgt eine Auswertung des Modells.
* **Regression**: Ein Modell zur Vorhersage eines kontinuierlichen Werts basierend auf unabhängigen Variablen, um z. B. den Preis eines Autos anhand des Baujahrs und der Marke vorherzusagen.
* **Bewertung**: Ein vorhergesagter Wert, der mithilfe des Moduls [Score Model](https://msdn.microsoft.com/library/azure/dn905995.aspx) in Machine Learning Studio aus einem trainierten Klassifizierungs- oder Regressionsmodell generiert wurde. Klassifizierungsmodelle geben auch eine Bewertung für die Wahrscheinlichkeit des vorhergesagten Werts zurück. Sobald Sie Bewertungen aus einem Modell generiert haben, können Sie die Genauigkeit des Modells mithilfe des Moduls [Evaluate Model](https://msdn.microsoft.com/library/azure/dn905915.aspx) auswerten.
* **Stichprobe**: Ein Teil eines Datasets, das repräsentativ für das gesamte Dataset steht. Stichproben können nach dem Zufallsprinzip oder basierend auf bestimmten Features des Datasets ausgewählt werden.



## Nächste Schritte
Die Grundlagen der Vorhersageanalyse und des maschinellen Lernens werden anhand eines [schrittweises Lernprogramms](machine-learning-create-experiment.md) und auf der [Grundlage von Beispielen](machine-learning-sample-experiments.md) vermittelt.


<!-- Module References -->
[learning-with-counts]: https://msdn.microsoft.com/library/azure/81c457af-f5c0-4b2d-922c-fdef2274413c/

<!---HONumber=September15_HO1-->