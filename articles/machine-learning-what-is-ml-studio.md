<properties title="What Is Azure Machine Learning Studio?" pageTitle="Was ist Machine Learning Studio? | Azure" description="Overview of Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/11/2014" ms.author="garye" />

# Was ist Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio ist eine visuelle Entwicklungsumgebung für Zusammenarbeit, mit der Sie Lösungen für prädiktive Analysen erstellen, testen und bereitstellen können, die mit Ihren Daten arbeiten. Die Machine Learning-Dienst- und -Entwicklungsumgebung ist cloudbasiert, ermöglicht Flexibilität bei Rechnerressourcen und Arbeitsspeicher und eliminiert das Kopfzerbrechen bei Einrichtung und Installation, denn Sie arbeiten über einen Webbrowser. 

Bei ML Studio finden Datenwissenschaften, prädiktive Analysen, Cloudressourcen und Ihre Daten zusammen!

## Der interaktive Arbeitsbereich von ML Studio

Für die Entwicklung eines prädiktiven Analysemodells verwendet man typischerweise Daten aus einer oder mehreren Quellen, transformiert und analysiert diese Daten mithilfe verschiedener Datenbearbeitungen und Statistikfunktionen und generiert einen Ergebnissatz. Das Entwickeln eines solchen Modells ist ein iterativer Prozess - Sie ändern die verschiedenen Funktionen und deren Parameter, und die Ergebnisse nähern sich an, bis Sie der Ansicht sind, dass sie ein trainiertes, effektives Modell erreicht haben.

**ML Studio** gibt Ihnen einen interaktiven, visuellen Arbeitsbereich, in dem Sie einfach auf einem prädiktiven Analysemodell entwickeln, testen und iterieren können. Sie fügen per Drag & Drop ***Datasets*** und ***Analysemodule***in einen interaktiven ***Arbeitsbereich*** ein, verbinden sie, sodass sie ein ***Experiment*** bilden, das Sie in ML Studio ***ausführen***. Für das Iterieren an Ihrem Modelldesign ***bearbeiten*** Sie das Experiment, ***speichern*** auf Wunsch eine Kopie und führen es erneut aus. Wenn Sie bereit sind, können Sie Ihr Experiment als ***Webdienst******veröffentlichen***, sodass andere auf Ihr Modell zugreifen können. 

Es ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von Datasets und Modulen zum Erstellen eines prädiktiven Analysemodells.

![ML Studio Overview][ml-studio-overview]

## Erste Schritte mit ML Studio

Wenn Sie ML Studio das erste Mal öffnen, sehen Sie links folgende Registerkarten:

- **Studio Home** - Eine Reihe von Links zu Dokumentation und anderen Ressourcen
- **EXPERIMENTE** - Experimente, die erstellt, ausgeführt und als Entwürfe gespeichert wurden. 
- **WEBDIENSTE** - eine Liste der von Ihnen veröffentlichten Experimente. 
- **EINSTELLUNGEN** - eine Sammlung von Einstellungen, mit denen Sie Ihr Konto und Ihre Ressourcen konfigurieren. 

>[WACOM.NOTE] Wenn Sie ein Experiment erstellen, wird links in dem Bereich eine Arbeitsliste verfügbarer Datasets und Module	angezeigt. Das ist die Liste der Komponenten, die Sie zum Erstellen Ihres Modells verwenden.

## Komponenten eines Experiments

Ein Experiment besteht auf Datasets, die Daten für Analysemodule bereitstellen, die Sie miteinander verbinden und so ein Vorhersagemodell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

- Es besteht aus mindestens einem Dataset und einem Modul. 
- Datasets können nur mit Modulen verbunden sein. 
- Module können entweder mit Datasets oder Modulen verbunden sein. 
- Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss haben. 
- Alle erforderlichen Parameter eines Moduls müssen festgelegt sein. 

Ein Beispiel zum Erstellen eines einfachen Experiments finden Sie unter [Erstellen eines einfachen Experiments in Azure Machine Learning Studio](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-create-experiment/). 
Eine ausführlichere exemplarische Vorgehensweise zum Erstellen einer Lösung für Vorhersageanalysen finden Sie unter [Entwickeln einer Vorhersagelösung mit Azure Machine Learning](http://azure.microsoft.com/de-de/documentation/articles/machine-learning-walkthrough-develop-predictive-solution/).

### Datasets

Ein Dataset sind Daten, die in ML Studio hochgeladen wurden, sodass sie im Modellierungsprozess verwendet werden können.  Eine Reihe von Beispieldatasets sind in ML Studio enthalten, sodass Sie damit experimentieren können, und bei Bedarf lassen sich weitere Datasets hochladen.  Hier einige Beispiele der enthaltenen Datasets:

- **MPG-Daten für verschiedene Autos** - MPG-Werte für Autos, die anhand der Anzahl von Zylindern, Motorleistung usw. aufgeführt sind 
- **Brustkrebsdaten** - Diagnostische Brustkrebsdaten 
- **Waldbranddaten** - Größe von Waldbränden im nordöstlichen Portugal 

Beim Erstellen eines Experiments ist die Arbeitsliste der Datasets links neben dem Arbeitsbereich verfügbar. 

### Module

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können.  ML Studio hat eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen.  Hier einige Beispiele der enthaltenen Module:

- **In ARFF konvertieren** - konvertiert ein .NET-serialisierter Datasets in das ARFF-Format 
- **Elementare Statistiken** - berechnet elementare Statistiken wie beispielsweise Mittelwert, Standardabweichung usw. 
- **Lineare Regression** - erstellt ein lineares Regressionsmodell, das auf einem Gradientenverfahren-Modell beruht 
- **Bewertungsmodell** - bewertet ein trainiertes Klassifzierungs- oder Regressionsmodell 

Beim Erstellen eines Experiments ist die Arbeitsliste der Module links neben dem Arbeitsbereich verfügbar. 

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul im Arbeitsbereich auswählen,   werden dessen Parameter im Abschnitt rechts neben diesem angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.


[ml-studio-overview]:./media/machine-learning-what-is-ml-studio/context.jpg
