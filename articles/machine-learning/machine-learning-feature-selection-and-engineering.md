<properties
	pageTitle="Entwicklung und Auswahl von Features in Azure Machine Learning | Microsoft Azure" 
	description="Erläutert den Zweck der Auswahl und Entwicklung von Features und stellt Beispiele ihrer Rolle im Datenaufbereitungsprozess für das maschinelle Lernen vor."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/09/2016"
	ms.author="zhangya;bradsev" />


# Entwicklung und Auswahl von Features in Azure Machine Learning

In diesem Thema wird der Zweck der Entwicklung und Auswahl von Features im Datenaufbereitungsprozess für das maschinelle Lernen erläutert. Zur Veranschaulichung dieser Prozesse werden in Azure Machine Learning Studio bereitgestellte Beispiele verwendet.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Die beim maschinellen Lernen verwendeten Trainingsdaten können häufig durch Auswahl oder Extrahieren von Features aus den gesammelten Rohdaten verbessert werden. Ein Beispiel für ein entwickeltes Feature im Kontext des Lernens, Bilder von handschriftlichen Zeichen zu klassifizieren, ist eine aus den Rohdaten der Bitverteilung erstellte Bitdichtekarte. Diese Karte kann helfen, die Kanten der Zeichen effizienter als mit der Rohdatenverteilung zu suchen.

Entwickelte und ausgewählte Features steigern die Effizienz des Trainingsprozesses, der versucht, die in den Daten enthaltenen Schlüsselinformationen zu extrahieren. Sie verbessern auch die Leistungsfähigkeit dieser Modelle für eine exakte Klassifizierung der Eingabedaten und verlässlichere Vorhersagen von Ergebnissen, die von Interesse sind. Entwicklung und Auswahl von Features können auch kombiniert werden, damit das Lernen besser rechnerisch verfolgt werden kann. Dies erfolgt durch eine Erweiterung und anschließende Verringerung der Anzahl von Features zum Kalibrieren oder Trainieren eines Modells. Mathematisch gesehen stehen die Features, die zum Trainieren des Modells ausgewählt werden, für einen minimalen Satz von unabhängigen Variablen, die Muster in den Daten erklären und dann erfolgreich Vorhersageergebnisse liefern.

Die Entwicklung und Auswahl von Features ist Bestandteil eines größeren Prozesses, der in der Regel aus vier Schritten besteht:

* Datensammlung
* Datenaufbereitung
* Modellentwicklung
* Nachbearbeitung

Entwicklung und Auswahl bilden den Schritt der **Datenaufbereitung** beim maschinellen Lernen. Drei Aspekte dieses Prozesses können für unsere Zwecke unterschieden werden:

* **Datenvorbearbeitung**: Dieser Prozess soll sicherstellen, dass die gesammelten Daten bereinigt und konsistent sind. Er umfasst Aufgaben wie das Integrieren mehrerer Datasets, Behandlung fehlender Daten, Behandlung inkonsistenter Daten und Konvertierung von Datentypen.
* **Featureentwicklung**: Bei diesem Prozess wird versucht, zusätzliche relevante Features aus den vorhandenen Rohfeatures in den Daten zu erstellen und die Vorhersageleistung des Lernalgorithmus zu steigern.
* **Featureauswahl**: Dieser Prozess wählt bei dem Versuch, die Anzahl von Dimensionen des Trainingsproblems zu verringern, die wichtigste Teilmenge der ursprünglichen Datenfeatures aus.

In diesem Thema werden nur die Aspekte Entwicklung und Auswahl von Features des Datenaufbereitungsprozesses behandelt. Weitere Informationen zur Vorbearbeitung von Daten finden Sie im Video [Vorbearbeitung von Daten in Azure ML Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).


## Erstellen von Features aus Ihren Daten – Featureentwicklung

Die Trainingsdaten bestehen aus einer aus Bespielen zusammengesetzten Matrix (in Zeilen gespeicherte Datensätze oder Beobachtungen), von denen jedes eine Reihe von Features (in Spalten gespeicherten Variablen oder Feldern) aufweist. Die Features, die in der Experimentanordnung angegeben werden, beschreiben wahrscheinlich die Muster in den Daten. Obwohl viele der Rohdatenfelder direkt in die ausgewählte Featuregruppe einbezogen werden können, die zum Trainieren eines Modells dient, müssen oft zusätzliche (entwickelte) Features aus den Features in den Rohdaten erstellt werden, um ein verbessertes Trainingsdataset generieren zu können.

Welche Art von Features sollte erstellt werden, um das Dataset beim Trainieren eines Modells zu verbessern? Entwickelte Features, die das Training verbessern, liefern Informationen, die die Muster in den Daten besser differenzieren. Die neuen Features sollen zusätzliche Informationen bereitstellen, die in den ursprünglichen oder vorhandenen Featuregruppen nicht eindeutig erfasst werden können oder nicht leicht ersichtlich sind. Doch dieser Prozess nicht einfach. Fundierte und produktive Entscheidungen erfordern häufig ein gewisses Fachwissen.

Wenn Sie Azure Machine Learning zum ersten Mal verwenden, ist es am einfachsten, wenn Sie sich diesen Prozess anhand der Beispiele in Studio verdeutlichen. Zwei Beispiele sind hier dargestellt:

* Ein Beispiel für die Regression: [Vorhersage der Anzahl ausgeliehener Fahrräder](../machine-learning-sample-prediction-of-number-of-bike-rentals.md) in einem überwachten Experiment, in dem die Zielwerte bekannt sind
* Ein Text Mining-Klassifizierungsbeispiel mit [Feature Hashing][feature-hashing]

### Beispiel 1: Hinzufügen von temporären Features zu einem Regressionsmodell ###

Das Experiment "Vorhersage der Anzahl ausgeliehener Fahrräder" in Azure Machine Learning Studio soll veranschaulichen, wie Features für Regressionsaufgaben entwickelt werden können. Dieses Experiment soll den Bedarf an Fahrrädern, d. h. die Anzahl von vermieteten Fahrrädern für einen bestimmten Monat/Tag/Stunde, vorhersagen. Das Dataset "Bike Rental UCI" liefert die Rohdaten für die Eingabe. Dieses Dataset basiert auf echten Daten des Unternehmens Capital Bikeshare, das eine Fahrradvermietung in Washington DC in den USA betreibt. Das Dataset stellt die Anzahl von geliehenen Fahrrädern zu einer bestimmten Tageszeit in den Jahren 2011 und 2012 dar und enthält 17.379 Zeilen und 17 Spalten. Die unformatierte Featuregruppe enthält Wetterbedingungen (Temperatur/Luftfeuchtigkeit/Windgeschwindigkeit) und den Typ des Tags (Feiertag/Wochentag). Das Feld für die Vorhersage ist "cnt", ein Zähler, der die ausgeliehenen Fahrräder innerhalb einer bestimmten Tageszeit darstellt und von 1 bis 977 reicht.

Mit dem Ziel, effektive Features in den Trainingsdaten zu entwickeln, werden vier Regressionsmodelle mit demselben Algorithmus, jedoch mit vier verschiedenen Trainingsdatasets erstellt. Die vier Datasets enthalten dieselben Roheingabedaten, jedoch mit einer steigenden Anzahl von festgelegten Features. Diese Features sind in vier Kategorien unterteilt:

1. A = Wetter + Feiertag + Wochenende + Wochenendfeatures für den vorhergesagten Tag
2. B = Anzahl von Fahrrädern, die in jeder der letzten 12 Stunden geliehen wurden
3. C = Anzahl von Fahrrädern, die an jedem der vergangenen 12 Tage zur gleichen Stunde geliehen wurden
4. D = Anzahl von Fahrrädern, die in jeder der vorherigen 12 Wochen zur gleichen Stunde und am gleichen Tag geliehen wurden

Neben Featuregruppe A, die bereits in den ursprünglichen Rohdaten vorhanden ist, werden die anderen drei Featuregruppen über den Featureentwicklungsprozess erstellt. Featuregruppe B erfasst die jüngste Nachfrage nach Fahrrädern. Featuregruppe C erfasst die Nachfrage nach Fahrrädern zu einer bestimmten Stunde Featuregruppe D erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten an bestimmten Wochentagen. Die vier Trainingsdatasets umfassen jeweils Featuregruppe A, A+B, A+B+C und A+B+C+D.

Diese vier Trainingsdatasets werden im Azure Machine Learning-Experiment über vier Verzweigungen aus den vorbearbeiteten Eingabedatasets gebildet. Mit Ausnahme der am weitesten links liegenden Verzweigung enthält jede dieser Verzweigungen ein Modul [Execute R Script][execute-r-script], in dem eine Reihe von abgeleiteten Features (Featuregruppe B, C und D) jeweils erstellt und dem importierten Dataset angehängt werden. Die folgende Abbildung veranschaulicht das R-Skript, das zur Erstellung der Featuregruppe B in der zweiten Verzweigung von links verwendet wird.

![Features erstellen](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

Der Vergleich der Leistungsergebnisse der vier Modelle ist in der folgenden Tabelle zusammengefasst. Die besten Ergebnisse werden mit den Features A+B+C angezeigt. Beachten Sie, dass die Fehlerrate niedriger ist, wenn eine zusätzliche Featuregruppe in den Trainingsdaten enthalten ist. Dies bestätigt die Vermutung, dass die Featuregruppen B und C weitere relevante Informationen für die Regressionsaufgabe liefern. Das Hinzufügen des Features D scheint jedoch keine zusätzliche Verringerung der Fehlerrate zu ergeben.

![Ergebnisvergleich](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="example2"></a> Beispiel 2: Erstellen von Features beim Text Mining  

Die Featureentwicklung wird häufig in Aufgaben verwendet, die mit Text Mining im Zusammenhang stehen, z. B. Dokumentklassifizierung und Stimmungsanalyse. Wenn z. B. Dokumente in verschiedene Kategorien klassifiziert werden sollen, ist eine typische Annahme, dass die in der einen Dokumentkategorie enthaltenen Wörter/Ausdrücke mit geringerer Wahrscheinlichkeit in der anderen Dokumentkategorie auftreten. Anders ausgedrückt, kann die Häufigkeit der Verteilung von Wörtern und Ausdrücken verschiedene Dokumenttypen charakterisieren. In Text Mining-Anwendungen, bei denen einzelne Teile des Textinhalts in der Regel als Eingabedaten dienen, ist der Featureentwicklungsprozess erforderlich, um Features in Zusammenhang mit Häufigkeiten von Wörtern/Ausdrücken zu erstellen.

Für diese Aufgabe wird ein Verfahren namens **Feature Hashing** angewendet, um beliebige Textfeatures effizient in Indizes zu verwandeln. Statt jedes Textfeature (Wörter/Ausdrücke) einem bestimmten Index zuzuweisen, wird bei dieser Methode durch Anwenden einer Hashfunktion auf die Features und die direkte Verwendung ihrer Hashwerte als Indizes ein Ergebnis erzielt.

Azure Machine Learning enthält ein Modul [Feature Hashing][feature-hashing], das diese Wort/Ausdruck-Features bequem erstellt. Die folgende Abbildung zeigt ein Beispiel für die Verwendung dieses Moduls. Das Eingabedataset enthält zwei Spalten: die Buchbewertung, die im Bereich von 1 bis 5 liegt, und den tatsächlichen Inhalt der Bewertung. Das Ziel dieses [Feature Hashing][feature-hashing]-Moduls ist, eine Reihe von neuen Features abzurufen, die die Häufigkeit des Auftretens entsprechender Wörter/Ausdrücke innerhalb der jeweiligen Buchbewertung zeigen. Um dieses Modul zu verwenden, müssen Sie die folgenden Schritte ausführen:

* Wählen Sie zuerst die Spalte, die den eingegebenen Text enthält (in diesem Beispiel "Col2").
* Legen Sie dann "Hashing bitsize" auf 8 fest, d. h. 2^8 = 256 Features werden erstellt. Die Wörter/Ausdrücke im gesamten Text werden auf 256 Indizes gehasht. Der Parameter "Hashing bitsize" reicht von 1 bis 31. Die Wörter/Ausdrücke werden mit geringerer Wahrscheinlichkeit in den gleichen Index gehasht, wenn eine größere Zahl festgelegt wird.
* Setzen Sie dann den Parameter "N-grams" auf 2. Dadurch wird die Häufigkeit der Unigramme (ein Feature für jedes einzelne Wort) und Bigramme (ein Feature für jedes Paar angrenzender Wörter) aus dem Eingabetext abgerufen. Der Parameter "N-grams" reicht von 0 bis 10, wodurch die maximale Anzahl sequenzieller Wörter in einem Feature angezeigt wird.  

!["Feature Hashing"-Modul](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

Die folgende Abbildung zeigt, wie dieses neue Feature aussehen wird.

!["Feature Hashing"-Beispiel](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## Filtern von Features aus Ihren Daten – Featureauswahl  ##

Die Featureauswahl ist ein Prozess, der häufig für die Entwicklung von Trainingsdatasets für die Vorhersagemodellierung, wie z. B. Klassifizierung oder Regressionsaufgaben, angewendet wird. Das Ziel ist, eine Teilmenge der Features aus dem ursprünglichen Dataset auszuwählen, die dessen Dimensionen verringert, indem sie einen minimalen Satz von Features zur Darstellung der maximalen Abweichung in den Daten verwendet. Diese Teilmenge der Features enthält dann die einzigen Features, die zum Trainieren des Modells enthalten sein sollen. Die Featureauswahl dient zwei wesentlichen Zwecken.

* Zunächst steigert die Featureauswahl häufig die Klassifizierungsgenauigkeit durch Eliminieren irrelevanter, redundanter oder hochgradig korrelierter Features.
* Zweitens sinkt die Anzahl von Features, was den Modelltrainingsvorgang effizienter gestaltet. Dies ist besonders wichtig für Lernmodelle, deren Training teuer ist, wie z. B. Support Vector Machines.

Obwohl die Featureauswahl die Anzahl von Features im Dataset reduzieren soll, die zum Trainieren des Modells verwendet werden, wird sie in der Regel nicht mit "Reduzierung der Anzahl von Dimensionen" bezeichnet. Featureauswahlmethoden extrahieren eine Teilmenge der ursprünglichen Features in den Daten, ohne sie zu ändern. Verfahren zur Reduktion der Anzahl von Dimensionen nutzen entwickelte Features, die die ursprünglichen Features transformieren können und sie so ändern. Beispiele für Verfahren zur Verringerung der Anzahl von Dimensionen sind zum Beispiel Principal Component Analysis, kanonische Korrelationsanalyse und Singular Value Decomposition.

Eine unter anderem häufig angewendete Kategorie von Featureauswahlmethoden in einem überwachten Kontext wird als "Filterbasierte Featureauswahl" bezeichnet. Durch Auswerten der Korrelation zwischen den einzelnen Features und dem Zielattribut wenden diese Methoden ein statistisches Maß an, um jedem Feature eine Bewertung zuzuweisen. Die Features werden dann nach dem Ergebnis geordnet. Das kann helfen, den Schwellenwert zum Beibehalten oder Entfernen eines bestimmten Features festzulegen. Beispiele für die statistischen Mittel in diesen Methoden sind Pearson-Korrelation, gegenseitige Information und der Chi-Quadrat-Test.

Azure Machine Learning Studio enthält Module zur Featureauswahl. Wie in der folgenden Abbildung gezeigt wird, gehören hierzu die Module [Filter-Based Feature Selection][filter-based-feature-selection] und [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis].

![Beispiel für Featureauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


Betrachten Sie beispielsweise die Verwendung des Moduls [Filter-Based Feature Selection][filter-based-feature-selection]. Der Einfachheit halber wird weiterhin das oben beschriebene Text Mining-Beispiel verwendet. Es wird vorausgesetzt, dass ein Regressionsmodell erstellt werden soll, nachdem ein Satz von 256 Features über das [Feature Hashing][feature-hashing]-Modul erstellt wurde, dass die Antwortvariable "Col1" ist und eine Buchbewertung mit Bewertungen im Bereich von 1 bis 5 darstellt. Setzen Sie die "Feature scoring method" (Featurebewertungsmethode) auf "Pearson Correlation" (Pearson-Korrelation), die "Target Column" (Zielspalte) auf "Col1" und die "Number of desired features" (Anzahl von gewünschten Features) auf 50. Dann erzeugt das Modul [Filter-Based Feature Selection][filter-based-feature-selection] ein DataSet mit 50 Features mit dem Zieltattribut "Col1". Die folgende Abbildung zeigt den Ablauf dieses Experiments und die oben beschriebenen Eingabeparameter.

![Beispiel für Featureauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

Die folgende Abbildung zeigt die resultierenden Datasets. Jedes Feature wird auf Basis der Pearson-Korrelation zwischen sich selbst und dem Zielattribut "Col1" bewertet. Die Features mit den besten Werten werden beibehalten.

![Beispiel für Featureauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

Die entsprechenden Ergebnisse der ausgewählten Features sind in der folgenden Abbildung dargestellt.

![Beispiel für Featureauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Durch Anwenden dieses Moduls [Filter-Based Feature Selection][filter-based-feature-selection] werden 50 von 256 Features ausgewählt, da sie die am meisten korrelierten Features mit der Zielvariable „Col1“ auf Grundlage der Bewertungsmethode „Pearson Correlation“ besitzen.

## Zusammenfassung
Entwicklung und Auswahl von Features sind zwei häufig ausgeführte Schritte zum Vorbereiten von Trainingsdaten beim Erstellen eines Modells für das maschinelle Lernen. Normalerweise wird die Featureentwicklung zuerst ausgeführt, um zusätzliche Features zu generieren, und anschließend wird die Featureauswahl ausgeführt, um irrelevante, redundante oder hoch korrelierte Features zu entfernen.

Beachten Sie, dass die Entwicklung bzw. Auswahl von Features nicht immer unbedingt ausgeführt werden müssen. Ob sie benötigt werden oder nicht, hängt von den vorliegenden oder gesammelten Daten ab, dem gewählten Algorithmus und dem Ziel des Experiments.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 

<!---HONumber=AcomDC_0211_2016-->