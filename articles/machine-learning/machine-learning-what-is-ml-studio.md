<properties 
	pageTitle="Was ist Azure Machine Learning Studio? | Microsoft Azure"
	description="Übersicht über Azure ML Studio, ein Drag &amp; Drop-Tool zum schnellen Erstellen von Modellen aus einer verwendungsbereiten Bibliothek mit Algorithmen und Modulen."
	keywords="Azure Machine Learning,Azure ML,ML Studio"
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
	ms.topic="get-started-article"
	ms.date="02/03/2016"
	ms.author="garye"/>

# Was ist Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio ist ein Tool für die Zusammenarbeit per Drag & Drop, mit der Sie Lösungen für Vorhersageanalysen erstellen, testen und bereitstellen können, die mit Ihren Daten arbeiten. Machine Learning Studio veröffentlicht Modelle als Webdienste, die von benutzerdefinierten Apps oder BI-Tools wie Excel problemlos genutzt werden können.

In Machine Learning Studio finden Datenwissenschaften, prädiktive Analysen, Cloudressourcen und Ihre Daten zusammen!

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Der interaktive Machine Learning Studio-Arbeitsbereich

Für die Entwicklung eines prädiktiven Analysemodells verwendet man typischerweise Daten aus einer oder mehreren Quellen, transformiert und analysiert diese Daten mithilfe verschiedener Datenbearbeitungen und Statistikfunktionen und generiert einen Ergebnissatz. Das Entwickeln eines solchen Modells ist ein iterativer Prozess. Sie ändern die verschiedenen Funktionen und deren Parameter, und die Ergebnisse nähern sich an, bis Sie der Ansicht sind, dass Sie ein trainiertes, effektives Modell erreicht haben.

**Azure Machine Learning Studio** stellt Ihnen einen interaktiven, visuellen Arbeitsbereich zur Verfügung, in dem Sie einfach ein Vorhersageanalysemodell entwickeln, testen und durchlaufen können. Sie fügen per Drag & Drop ***DataSets*** und ***Analysemodule*** in einen interaktiven ***Arbeitsbereich*** ein und verbinden sie zu einem ***Experiment***, das in Machine Learning Studio ***ausgeführt*** werden kann. Für das Iterieren an Ihrem Modelldesign ***bearbeiten*** Sie das Experiment, ***speichern*** auf Wunsch eine Kopie und führen es erneut aus. Wenn Sie bereit sind, können Sie Ihr ***Trainingsexperiment*** in ein ***Vorhersageexperiment*** konvertieren und anschließend als ***Webdienst*** ***veröffentlichen***, damit andere Benutzer auf das Modell zugreifen können.

>[AZURE.TIP] Informationen zum Herunterladen und Drucken des Diagramms, mit dem Sie sich einen Überblick über die Machine Learning Studio-Funktionen verschaffen können, finden Sie unter [Übersichtsdiagramm der Azure Machine Learning Studio-Funktionen](machine-learning-studio-overview-diagram.md).

Es ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von Datasets und Modulen zum Erstellen eines prädiktiven Analysemodells.

![Azure ML Studio-Diagramm: Erstellen von Experimenten, Lesen von Daten aus vielen Quellen, Schreiben der ausgewerteten Daten, Erstellen der Modelle.][ml-studio-overview]

## Erste Schritte mit Machine Learning Studio

Wenn Sie [Machine Learning Studio](https://studio.azureml.net) zum ersten Mal öffnen, sehen Sie die **Startseite**. Hier finden Sie die Dokumentation, Videos, Webinare und weitere wertvolle Ressourcen.

Im oberen Bereich befinden sich drei Registerkarten: **Home** (Ihr Ausgangspunkt), **Studio** und **Gallery**.

### Studio

Klicken Sie auf die Registerkarte **Studio**. Sie werden aufgefordert, sich mit Ihrem Microsoft-Konto oder Ihrem Geschäfts- oder Schulkonto anzumelden. Nach der Anmeldung sehen Sie auf der linken Seite die folgenden Registerkarten:

- **EXPERIMENTS**: Experimente, die erstellt, ausgeführt und als Entwürfe gespeichert wurden
- **WEB SERVICES**: Webdienste, die Sie über Ihre Experimente bereitgestellt haben
- **NOTEBOOKS**: Jupyter-Notebooks, die Sie erstellt haben
- **DATASETS**: DataSets, die Sie in Studio hochgeladen haben
- **TRAINED MODELS**: Modelle, die Sie in Experimenten trainiert und in Studio gespeichert haben
- **EINSTELLUNGEN** – eine Sammlung von Einstellungen, mit denen Sie Ihr Konto und Ihre Ressourcen konfigurieren

### Katalog

Klicken Sie auf die Registerkarte **Gallery**. Sie gelangen zum Cortana Analytics-Katalog. In diesem Katalog kann eine Community von Datenwissenschaftlern und Entwicklern Lösungen freigeben, die mithilfe von Komponenten der Cortana Analytics Suite erstellt wurden.

Weitere Informationen über den Katalog finden Sie unter [Teilen und Entdecken von Lösungen im Cortana Analytics-Katalog](machine-learning-gallery-how-to-use-contribute-publish.md).

## Komponenten eines Experiments

Ein Experiment besteht auf Datasets, die Daten für Analysemodule bereitstellen, die Sie miteinander verbinden und so ein Vorhersagemodell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

- Das Experiment besteht aus mindestens einem Dataset und einem Modul.
- Datasets können nur mit Modulen verbunden sein.
- Module können entweder mit Datasets oder mit anderen Modulen verbunden sein.
- Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss aufweisen.
- Alle erforderlichen Parameter der einzelnen Module müssen festgelegt sein.

Sie können ein Experiment von Grund auf neu erstellen, oder Sie können ein vorhandenes Beispielexperiment als Vorlage verwenden. Weitere Informationen hierzu finden Sie unter [Verwenden von Beispielexperimenten zum Erstellen neuer Experimente](machine-learning-sample-experiments.md).

Ein Beispiel für das Erstellen eines einfachen Experiments finden Sie unter [Erstellen eines einfachen Experiments in Azure Machine Learning Studio](machine-learning-create-experiment.md).

Eine ausführlichere exemplarische Vorgehensweise zum Erstellen einer Lösung für Vorhersageanalysen finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### Datasets

Ein DataSet besteht aus Daten, die in Machine Learning Studio hochgeladen wurden, sodass sie im Modellierungsprozess verwendet werden können. Machine Learning Studio enthält eine Reihe von Beispiel-DataSets, mit denen Sie experimentieren können, und bei Bedarf lassen sich weitere DataSets hochladen. Hier einige Beispiele der enthaltenen Datasets:

- **MPG-Daten für verschiedene Autos:** Meilen-pro-Gallone-Werte für Autos, die anhand der Anzahl von Zylindern, Motorleistung usw. aufgeführt sind.
- **Brustkrebsdaten:** diagnostische Brustkrebsdaten.
- **Waldbranddaten:** Größe von Waldbränden im nordöstlichen Portugal.

Beim Erstellen eines Experiments können Sie links neben dem Arbeitsbereich aus der Liste der Datasets auswählen.

Eine Liste der in Machine Learning Studio enthaltenen Beispiel-DataSets finden Sie unter [Verwenden von Beispiel-DataSets in Azure Machine Learning Studio](machine-learning-use-sample-datasets.md).

### Module

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Machine Learning Studio hat eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen. Hier einige Beispiele der enthaltenen Module:

- [In ARFF konvertieren:][convert-to-arff] konvertiert ein .NET-serialisiertes DataSet in das ARFF-Format
- [Elementare Statistiken berechnen:][elementary-statistics] berechnet elementare Statistiken wie beispielsweise Mittelwert, Standardabweichung usw.
- [Lineare Regression:][linear-regression] erstellt ein lineares Regressionsmodell online, das auf einem Gradientenverfahren-Modell beruht.
- [Bewertungsmodell:][score-model] bewertet ein trainiertes Klassifizierungs- oder Regressionsmodell

Beim Erstellen eines Experiments können Sie links neben dem Arbeitsbereich aus der Liste der Module auswählen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul im Arbeitsbereich auswählen, werden dessen Parameter rechts neben dem Arbeitsbereich im Bereich **Eigenschaften** angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.

Hilfe zur Navigation durch die umfassende Bibliothek verfügbarer Machine Learning-Algorithmen finden Sie unter [Auswählen von Algorithmen für Microsoft Azure Machine Learning](machine-learning-algorithm-choice.md).

## Bereitstellen eines Predictive Analytics-Webdiensts

Wenn Ihr Predictive Analytics-Modell bereit ist, können Sie es als Webdienst direkt von Machine Learning Studio aus bereitstellen. Weitere Informationen zu diesem Verfahren finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](machine-learning-publish-a-machine-learning-web-service.md).

[ml-studio-overview]: ./media/machine-learning-what-is-ml-studio/azure-ml-studio-diagram.jpg

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/

<!---HONumber=AcomDC_0302_2016-->