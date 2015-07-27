<properties 
	pageTitle="Was ist Azure Machine Learning Studio? | Microsoft Azure" 
	description="Übersicht über Azure ML Studio, ein Drag &amp; Drop-Tool zum schnellen Erstellen von Modellen aus einer verwendungsbereiten Bibliothek mit Algorithmen und Modulen." 
	keywords="azure machine learning,azure ml, ml studio"	
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

# Was ist Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio ist ein Tool für die Zusammenarbeit per Drag & Drop, mit der Sie Lösungen für Vorhersageanalysen erstellen, testen und bereitstellen können, die mit Ihren Daten arbeiten. Machine Learning Studio veröffentlicht Modelle als Webdienste, die von benutzerdefinierten Apps oder BI-Tools wie Excel problemlos genutzt werden können.

In Machine Learning Studio – auch als Azure ML Studio bezeichnet – finden Datenverarbeitung, Vorhersageanalysen, Cloudressourcen und Ihre Daten zusammen.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Der interaktive Machine Learning Studio-Arbeitsbereich

Für die Entwicklung eines prädiktiven Analysemodells verwendet man typischerweise Daten aus einer oder mehreren Quellen, transformiert und analysiert diese Daten mithilfe verschiedener Datenbearbeitungen und Statistikfunktionen und generiert einen Ergebnissatz. Das Entwickeln eines solchen Modells ist ein iterativer Prozess. Sie ändern die verschiedenen Funktionen und deren Parameter, und die Ergebnisse nähern sich an, bis Sie der Ansicht sind, dass Sie ein trainiertes, effektives Modell erreicht haben.

**Azure Machine Learning Studio** stellt Ihnen einen interaktiven, visuellen Arbeitsbereich zur Verfügung, in dem Sie einfach ein Vorhersageanalysemodell entwickeln, testen und durchlaufen können. Sie fügen per Drag & Drop ***DataSets*** und ***Analysemodule*** in einen interaktiven ***Arbeitsbereich*** ein und verbinden sie zu einem ***Experiment***, das in Machine Learning Studio ***ausgeführt*** werden kann. Für das Iterieren an Ihrem Modelldesign ***bearbeiten*** Sie das Experiment, ***speichern*** auf Wunsch eine Kopie und führen es erneut aus. Wenn Sie bereit sind, können Sie Ihr Experiment als ***Webdienst*** ***veröffentlichen***, sodass andere auf Ihr Modell zugreifen können.

Es ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von Datasets und Modulen zum Erstellen eines prädiktiven Analysemodells.

![Azure ML Studio-Diagramm: Erstellen von Experimenten, Lesen von Daten aus vielen Quellen, Schreiben der ausgewerteten Daten, Erstellen der Modelle.][ml-studio-overview]

## Erste Schritte mit Machine Learning Studio

Wenn Sie Machine Learning Studio zum ersten Mal öffnen, sehen Sie links folgende Registerkarten:

- **Studio Home:** eine Reihe von Links zu Dokumentation und anderen Ressourcen
- **EXPERIMENTE** – Experimente, die erstellt, ausgeführt und als Entwürfe gespeichert wurden 
- **WEBDIENSTE** – eine Liste der von Ihnen veröffentlichten Experimente 
- **EINSTELLUNGEN** – eine Sammlung von Einstellungen, mit denen Sie Ihr Konto und Ihre Ressourcen konfigurieren 

>[AZURE.NOTE]Wenn Sie ein Experiment erstellen, wird links neben dem Arbeitsbereich eine Arbeitsliste verfügbarer DataSets und Module angezeigt. Das ist die Liste der Komponenten, die Sie zum Erstellen Ihres Modells verwenden.

## Komponenten eines Experiments

Ein Experiment besteht auf Datasets, die Daten für Analysemodule bereitstellen, die Sie miteinander verbinden und so ein Vorhersagemodell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

- Das Experiment besteht aus mindestens einem DataSet und einem Modul. 
- Datasets können nur mit Modulen verbunden sein. 
- Module können entweder mit DataSets oder anderen Modulen verbunden sein. 
- Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss haben. 
- Alle erforderlichen Parameter eines Moduls müssen festgelegt sein. 

Ein Beispiel für das Erstellen eines einfachen Experiments finden Sie unter [Erstellen eines einfachen Experiments in Azure Machine Learning Studio](machine-learning-create-experiment.md). Eine ausführlichere exemplarische Vorgehensweise zum Erstellen einer Lösung für Vorhersageanalysen finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### Datasets

Ein DataSet besteht aus Daten, die in Machine Learning Studio hochgeladen wurden, sodass sie im Modellierungsprozess verwendet werden können. Machine Learning Studio enthält eine Reihe von Beispiel-DataSets, mit denen Sie experimentieren können, und bei Bedarf lassen sich weitere DataSets hochladen. Hier einige Beispiele der enthaltenen Datasets:

- **MPG-Daten für verschiedene Autos:** Meilen-pro-Gallone-Werte für Autos, die anhand der Anzahl von Zylindern, Motorleistung usw. aufgeführt sind. 
- **Brustkrebsdaten:** diagnostische Brustkrebsdaten. 
- **Waldbranddaten:** Größe von Waldbränden im nordöstlichen Portugal. 

Beim Erstellen eines Experiments ist die Arbeitsliste der Datasets links neben dem Arbeitsbereich verfügbar.

### Module

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Machine Learning Studio hat eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen. Hier einige Beispiele der enthaltenen Module:

- [In ARFF konvertieren:][convert-to-arff] konvertiert ein .NET-serialisiertes DataSet in das ARFF-Format 
- [Elementare Statistiken:][elementary-statistics] berechnet elementare Statistiken wie beispielsweise Mittelwert, Standardabweichung usw. 
- [Lineare Regression:][linear-regression] erstellt ein lineares Regressionsmodell online, das auf einem Gradientenverfahren-Modell beruht. 
- [Bewertungsmodell:][score-model] bewertet ein trainiertes Klassifizierungs- oder Regressionsmodell 

Beim Erstellen eines Experiments ist die Arbeitsliste der Module links neben dem Arbeitsbereich verfügbar.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sei ein Modul im Arbeitsbereich auswählen, werden dessen Parameter im Abschnitt rechts neben diesem angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.


[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg


<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
 

<!---HONumber=July15_HO3-->