<properties title="What Is Azure Machine Learning Studio?" pageTitle="What Is Machine Learning Studio? | Azure" description="Overview of Azure Machine Learning Studio" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Was ist Azure Machine Learning Studio?

Microsoft Azure Machine Learning Studio ist eine visuelle Entwicklungsumgebung für Zusammenarbeit, mit der Sie Lösungen für prädiktive Analysen erstellen, testen und bereitstellen können, die mit Ihren Daten arbeiten. Die Machine Learning-Dienst- und -Entwicklungsumgebung ist Cloud-basiert, ermöglicht Flexibilität bei Rechnerressourcen und Arbeitsspeicher und eliminiert das Kopfzerbrechen bei Einrichtung und Installation, denn Sie arbeiten über einen Webbrowser.

Bei ML Studio finden Datenwissenschaften, prädiktive Analysen, Cloudressourcen und Ihre Daten zusammen!

## Der interaktive Arbeitsbereich von ML Studio

Für die Entwicklung eines prädiktiven Analysemodells verwendet man typischerweise Daten aus einer oder mehreren Quellen, transformiert und analysiert diese Daten mithilfe verschiedener Datenbearbeitungen und Statistikfunktionen und generiert einen Ergebnissatz. Das Entwickeln eines solchen Modells ist ein iterativer Prozess – Sie ändern die verschiedenen Funktionen und deren Parameter, und die Ergebnisse nähern sich an, bis Sie der Ansicht sind, dass sie ein trainiertes, effektives Modell erreicht haben.

**ML Studio** gibt Ihnen einen interaktiven, visuellen Arbeitsbereich, in dem Sie einfach auf einem prädiktiven Analysemodell entwickeln, testen und iterieren können. Sie fügen per Drag & Drop ***Datasets*** und ***Analysemodule***
in einen interaktiven ***Arbeitsbereich*** ein, verbinden sie, sodass sie ein ***Experiment*** bilden, das Sie dann an ML Studio zur Ausführung ***senden***. Für das Iterieren an Ihrem Modelldesign ***bearbeiten*** Sie das Experiment, ***speichern*** auf Wunsch eine Kopie und senden es erneut.

Es ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von Datasets und Modulen zum Erstellen eines prädiktiven Analysemodells.

![ML Studio-Übersicht][ML Studio-Übersicht]

## Erste Schritte mit ML Studio

Wenn Sie ML Studio das erste Mal öffnen, sehen Sie links folgende Registerkarten:

- **EXPERIMENTE** – Experimente, die erstellt, ausgeführt und als Entwürfe gespeichert wurden
- **WEBDIENSTE** – eine Liste der von Ihnen veröffentlichten Experimente
- **MODULE** – eine Referenzliste der Analysemodule, die in ML Studio zur Verfügung stehen
- **DATASETS** – eine Referenzliste der Datasets, die in ML Studio zur Verfügung stehen
- **EINSTELLUNGEN** – eine Sammlung von Einstellungen, mit denen Sie Ihr Konto und Ihre Ressourcen konfigurieren

> [WACOM.NOTE] Die Liste der auf diesen Registerkarten angezeigten **Module** und **Datasets** dient nur als Referenz. Diese Listen enthalten weitere Informationen über Datasets und Module, darunter das Erstellungsdatum und die Versionsnummer.
> Wenn Sie ein Experiment konstruieren, wird links neben dem Arbeitsbereich eine Arbeitsliste verfügbarer Datasets und Module angezeigt. Das ist die Liste der Komponenten, die Sie zum Erstellen Ihres Modells verwenden.


## Komponenten eines Experiments

Ein Experiment besteht auf Datasets, die Daten für Analysemodule bereitstellen, die Sie miteinander verbinden und so ein Vorhersagemodell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

- Es besteht aus mindestens einem Dataset und einem Modul.
- Datasets können nur mit Modulen verbunden sein.
- Module können entweder mit Datasets oder Modulen verbunden sein.
- Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss haben.
- Alle erforderlichen Parameter eines Moduls müssen festgelegt sein.

Ein Beispiel für das Erstellen eines einfachen Experiments finden Sie unter **Erstellen eines Beispielexperiments**.
Eine umfassendere exemplarische Vorgehensweise beim Erstellen einer prädiktiven Analyselösung finden Sie unter **Erstellen und Iterieren eines Machine Learning-Experiments**.

### Datasets

Ein Dataset sind Daten, die in ML Studio hochgeladen wurden, sodass Sie im Modelierungsprozess verwendet werden können. Eine Reihe von Beispieldatasets sind in ML Studio enthalten, sodass Sie damit experimentieren können, und bei Bedarf lassen sich weitere Datasets hochladen. Hier einige Beispiele der enthaltenen Datasets:

- **MPG-Daten für verschiedene Autos** – MPG-Werte für Autos, die anhand der Anzahl von Zylindern, Motorleistung usw. aufgeführt sind
- **Brustkrebsdaten** – Diagnostische Brustkrebsdaten
- **Waldbranddaten** – Größe von Waldbränden im nordöstlichen Portugal

Beim Erstellen eines Experiments ist die Arbeitsliste der Datasets links neben dem Arbeitsbereich verfügbar. Sie können auch die Liste der hochgeladenen Datasets anzeigen, indem Sie die Registerkarte **DATASETS** beim Start von ML Studio auswählen.

### Module

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. ML Studio hat eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen. Hier einige Beispiele der enthaltenen Module:

- **In ARFF konvertieren** – konvertiert ein .NET-serialisierter Datasets in das ARFF-Format
- **Elementare Statistiken** – berechnet elementare Statistiken wie beispielsweise Mittelwert, Standardabweichung usw.
- **Lineare Regression** – erstellt ein lineares Regressionsmodell, das auf einem Gradientenverfahren-Modell beruht
- **Bewertungsmodell** – bewertet ein trainiertes Klassifzierungs- oder Regressionsmodell

Beim Erstellen eines Experiments ist die Arbeitsliste der Module links neben dem Arbeitsbereich verfügbar. Sie können auch die Liste der enthaltenen Module anzeigen, indem Sie die Registerkarte **MODULE** beim Start von ML Studio auswählen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sei ein Modul im Arbeitsbereich auswählen, werden dessen Parameter im Abschnitt rechts neben diesem angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.


[ML Studio-Übersicht]: ./media/machine-learning-what-is-ml-studio/context.jpg
