<properties 
	pageTitle="Konstruktion und Auswahl von Merkmalen in Azure Machine Learning | Azure" 
	description="Hier wird der Zweck der Konstruktion und Auswahl von Merkmalen erläutert, und es werden Beispiele für deren Rolle im Datenaufbereitungsprozess für das Machine Learning gezeigt." 
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
	ms.date="04/21/2015" 
	ms.author="zhangya;bradsev" />


# Konstruktion und Auswahl von Merkmalen in Azure Machine Learning

In diesem Thema wird der Zweck der Konstruktion und Auswahl von Merkmalen erläutert im Datenaufbereitungsprozess für das Machine Learning gezeigt. Diese Prozesse werden mithilfe von in Azure Machine Learning Studio bereitgestellten Beispielen veranschaulicht.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)]

Die Trainingsdaten aus dem Machine Learning können häufig durch die Auswahl oder Extrahieren von Merkmalen aus den gesammelten Rohdaten verbessert werden. Ein Beispiel für eine Merkmalskonstruktion im Kontext des Lernens, wie Bilder von handschriftlichen Zeichen klassifiziert werden können, ist das Erstellen einer Bitdichtedarstellung aus den rohen Bitverteilungsdaten. Diese Zuordnung kann helfen, die Kanten der Zeichen effizienter als mit der rohen Verteilung zu erkennen.

Konstruierte und ausgewählte Merkmale steigern die Effizienz des Trainingsprozesses, der versucht, die in den Daten enthaltenen Schlüsselinformationen zu extrahieren. Sie verbessern auch die Leistungsfähigkeit dieser Modelle für exakte Klassifizierung der Eingabedaten und stabilere Vorhersagen von Ergebnissen, die von Interesse sind. Konstruktion und Auswahl von Merkmalen kann auch kombiniert werden, um das Lernen rechnerisch steuerbar zu machen. Dies erfolgt durch Erweiterung und dann Reduzierung der Anzahl von Merkmalen zum Kalibrieren oder Trainieren eines Modells. Mathematisch gesehen, stehen die Merkmale, die zum Trainieren des Modells ausgewählt werden, für einen minimalen Satz von unabhängigen Variablen, die Muster in den Daten erklären und dann erfolgreich Vorhersageergebnisse liefern. 

Die Konstruktion und Auswahl von Merkmalen ist ein Teil eines größeren Prozesses, der in der Regel aus vier Schritten besteht: 

* Datensammlung 
* Datenaufbereitung 
* Modellkonstruktion 
* Nachbearbeitung 

Konstruktion und Auswahl von Merkmalen ergeben dern **Datenaufbereitungsschritt** des Machine Learning. Drei Aspekte dieses Prozesses können für unsere Zwecke unterschieden werden:

* **Vorbearbeitung von Daten**: Dieser Prozess versucht sicherzustellen, dass die gesammelten Daten bereinigt und konsistent ist. Er umfasst Aufgaben wie das Integrieren von mehreren Datasets, Behandlung von fehlenden Daten, Behandlung von inkonsistenten Daten und Konvertierung von Datentypen.
* **Merkmalskonstruktion**: Dieser Prozess versucht, zusätzliche relevante Merkmale aus den vorhandenen Rohmerkmalen in den Daten zu erstellen und die Vorhersageleistung des Machine Learning-Algorithmus zu erhöhen.
* **Merkmalsauswahl**: Dieser Prozess wählt die wichtigste Teilmenge der ursprünglichen Datenmerkmale bei aus, um die Anzahl der Dimensionen des Trainingsproblems zu reduzieren. 

Dieser Artikel behandelt nur die Aspekte Konstruktion und Auswahl von Merkmalen des Datenaufbereitungsprozesses. Weitere Informationen zur Vorbearbeitung von Daten finden Sie im Video [Pre-processing Data in Azure ML Studio](http://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/) (Vorbearbeitung von Daten in Azure ML Studio, in englischer Sprache).


## Erstellen von Merkmalen aus Ihren Daten - Merkmalskonstruktion

Die Trainingsdaten bestehen aus einer Matrix bestehend aus Beispielen (Datensätze oder Beobachtungen, in Zeilen gespeichert), von denen jedes eine Reihe von Merkmalen besitzt (Variablen oder Felder, in Spalten gespeichert). Die Merkmale, die in der Experimentanordnung angegeben werden, beschreiben voraussichtlich die Muster in den Daten. Obwohl viele der Rohdatenfelder direkt in die ausgewählte Merkmalsmenge, die zum Trainieren eines Modells dient, aufgenommen werden können, ist es oft der Fall, dass zusätzliche (konstruierte) Merkmale erstellt werden müssen, um ein verbessertes Trainingsdataset generieren zu können. 

Welche Art von Merkmalen sollte erstellt werden, um das Dataset beim Trainieren eines Modells zu verbessern? Konstruierte Merkmale, die das Training verbessern, liefern Informationen, mit denen das Muster in den Daten besser herausgestellt wird. Die neuen Merkmale sollen zusätzliche Informationen bereitstellen, die in den ursprünglichen oder vorhandenen Merkmalen nicht eindeutig erfasst werden können oder leicht ersichtlich sind. Doch dieser Prozess nicht einfach. Fundierte und produktive Entscheidungen erfordern häufig Fachwissen. 

Wenn Sie gerade erste Schritte mit Azure Machine Learning unternehmen, ist es am einfachsten, Sie erfassen diesen Vorgang anhand der Beispiele im Studio. Zwei Beispiele sind hier dargestellt:

* Ein Beispiel für die Regression: [Vorhersage der Anzahl ausgeliehener Fahrräder](machine-learning-sample-prediction-of-number-of-bike-rentals.md) in einem überwachten Experiment, in dem die Zielwerte bekannt sind 
* Ein Text Mining-Klassifizierungsbeispiel mit [Feature Hashing][feature-hashing]

### Beispiel 1: Hinzufügen von temporären Merkmalen zu einem Regressionsmodell ###

Verwenden wir das Experiment "Vorhersage der Anzahl ausgeliehener Fahrräder" in Azure Machine Learning Studio, um zu veranschaulichen, wie Merkmale für Regressionsaufgaben entwickelt werden können. Das Ziel dieses Experiment ist es, den Bedarf für Fahrräder, d. h. die Anzahl der vermieteten Fahrräder für einen bestimmten Monat/Tag/Stunde, vorherzusagen. Das Dataset "Bike Rental UCI dataset" wird als Rohdaten für die Eingaben verwendet. Dieses Dataset basieren auf echten Daten des Unternehmens Capital Bikeshare, das eine Fahrradvermietung in Washington DC in den USA betreibt. Das Dataset stellt die Anzahl der geliehenen Fahrräder innerhalb einer bestimmten Tageszeit in den Jahren 2011 und 2012 dar und enthält 17.379 Zeilen und 17 Spalten. Der unformatierte Merkmalsatz enthält Wetterbedingungen (Temperatur, Luftfeuchtigkeit, Windgeschwindigkeit) und den Typ des Tages (Feiertag/Wochentag). Das Feld für die Vorhersage ist "cnt", ein Zähler, der die ausgeliehenen Fahrräder innerhalb einer bestimmten Tageszeit darstellt. Dieser Bereich liegt zwischen 1 und 977.

Mit dem Ziel effektive Merkmale in den Trainingsdaten zu konstruieren, werden vier Regressionsmodelle erstellt, mit dem gleichen Algorithmus jedoch mit vier verschiedenen Trainingsdatasets. Die vier Datasets enthalten dieselben Roheingabedaten, jedoch mit einer steigenden Anzahl von festgelegten Merkmalen. Diese Merkmale sind in vier Kategorien unterteilt:
 
1. A = Die Merkmale Wetter + Feiertag + Wochentag + Wochenende für den vorhergesagten Tag
2. B = Anzahl der Fahrräder, die in jeder der letzten 12 Stunden geliehen wurden
3. C = Anzahl der Fahrräder, die an jedem der vergangenen 12 Tage zur gleichen Stunde geliehen wurden
4. D = Anzahl der Fahrräder, die in jeder der vorherigen 12 Wochen zur gleichen Stunde und am gleichen Tag geliehen wurden

Neben dem Merkmalsatz A, der bereits in den ursprünglichen unverarbeiteten Daten vorhanden ist, werden die anderen drei Sätze mit Merkmalen über den Merkmalkonstruktionsprozess erstellt. Merkmalgruppe B erfasst die jüngste Nachfrage nach Fahrrädern. Merkmalgruppe C erfasst die Nachfrage nach Fahrrädern zu einer bestimmten Stunde. Merkmalgruppe D erfasst die Nachfrage nach Fahrrädern zu bestimmten Uhrzeiten an bestimmten Wochentagen. Die vier Trainingsdatasets umfassen jeweils Merkmalssatz A, A+B, A+B+C und A+B+C+D.

Diese vier Trainingsdatasets werden im Azure Machine Learning-Experiment über vier Verzweigungen aus den vorbearbeiteten Eingabedatasets gebildet. Mit Ausnahme der am weitesten links liegenden Verzweigung enthält jede dieser Verzweigungen ein Modul [R-Skript ausführen][execute-r-script], in dem eine Reihe von abgeleiteten Merkmalen (Merkmalliste B, C und D) jeweils erstellt und an das importierte Dataset angefügt werden. Die folgende Abbildung veranschaulicht das R-Skript, das zur Erstellung des Merkmalsatzes B in der zweiten Verzweigung von links verwendet wird.

![Erstellen von Merkmalen](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png) 

Der Vergleich der Leistungsergebnisse der der vier Modelle ist in der folgenden Tabelle zusammengefasst. Die besten Ergebnisse werden mit den Merkmalen A+B+C angezeigt. Beachten Sie, dass es die Fehlerrate verringert, wenn ein zusätzlicher Merkmalsatz in den Trainingsdaten enthalten ist. Es bestötigt die Vermutung, dass die Merkmalsätze B und C weitere relevante Informationen für die Regressionsaufgabe liefern. Das Hinzufügen des Merkmals D scheint jedoch keine zusätzlichen Verringerung der Fehlerrate zu ergeben.

![Ergebnisvergleich](./media/machine-learning-feature-selection-and-engineering/result1.png) 

### <a name="example2"></a>Beispiel 2: Erstellen von Merkmalen beim Text Mining  

Die Merkmalkonstruktion wird häufig in Aufgaben im Zusammenhang mit Text Mining, z. B. Dokumentanalyse Klassifizierung und Stimmungsanalyse verwendet. Beispielsweise wenn Dokumente in verschiedene Kategorien klassifiziert werden sollen, ist eine typische Annahme, dass die Wörter/Ausdrücke in der einen Dokumentkategorie weniger wahrscheinlich in der anderen Dokumentkategorie auftreten. Anders ausgedrückt kann die Häufigkeit der Verteilung von Wörtern und Ausdrücken verschiedene Dokumenttypen charakterisieren. In Text Mining-Anwendungen, bei denen einzelne Teile des Textinhalts in der Regel als die Eingabedaten dienen, ist der Merkmalkonstruktionsprozess erforderlich, um Merkmale in Zusammenhang mit Häufigkeiten von Wörtern/Ausdrücken zu erstellen.

Für diese Aufgabe wird ein Verfahren namens **Feature Hashing** wird angewendet, um beliebige Textmerkmale effizient in Indizes zu verwandeln. Statt jede Text-Funktion (Wörter und Ausdrücke) einem bestimmten Index zuzuweisen, wird bei dieser Methode durch Anwenden einer Hashfunktion auf die Merkmale und die direkte Verwendung ihrer Hashwerte als Indizes ein Ergebnis erzielt.

In Azure Machine Learning gibt es ein Modul [Feature Hashing][feature-hashing], das diese Word/Satz-Merkmale bequem erstellt. Folgende Abbildung zeigt ein Beispiel für die Verwendung dieses Moduls. Das Eingabedataset enthält zwei Spalten: die Buchbewertung, die im Bereich von 1 bis 5 liegt, und den tatsächlichen Inhalt der Bewertung. Das Ziel dieses [Feature Hashing][feature-hashing]-Moduls ist, eine Reihe von neuen Merkmalen abzurufen, die die Häufigkeit des Auftretens entsprechender Suchbegriffe (Wörter/Ausdrücke) innerhalb der einzelnen Buchbewertung zeigen. Um dieses Modul zu verwenden, müssen Sie die folgenden Schritte ausführen:

* Wählen Sie zuerst die Spalte, die den eingegebenen Text enthält (in diesem Beispiel "Col2"). 
* Legen Sie dann die Hashing-Bitgröße auf 8 fest, d. h. 2^8 = 256 Merkmale werden erstellt. Die Wörter/Ausdrücke im gesamten Text werden auf 256 Indizes gehasht. Der Parameter "Hashing-Bitgröße" reicht von 1 bis 31. Die Wörter/Ausdrücke werden weniger wahrscheinlich in den gleichen Index gehasht, wenn eine größere Zahl festgelegt wird. 
* Legen Sie dann den Parameter "N-gramme" auf 2 fest. Dadurch wird die Häufigkeit der Unigramme (ein Merkmal für jedes einzelne Wort) und Bigramme (ein Merkmal für jedes Paar angrenzender Wörter) aus dem Eingabetext abgerufen. Der Parameter "N-gramme" reicht von 0 bis 10, wodurch die maximale Anzahl von sequenziellen Wörtern in einem Merkmal angezeigt wird.  

![Modul "Feature Hashing"](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png) 

Die folgende Abbildung zeigt, wie dieses neue Merkmal aussehen wird. 

![Beispiel "Feature Hashing"](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png) 

## Filtern von Merkmalen aus Ihren Daten - Merkmalsauswahl ##

Die Merkmalsauswahl ist ein Prozess, der häufig für die Konstruktion von Trainingsdatasets für die Vorhersagemodellierung wie z. B. Klassifizierung oder Regressionsaufgaben angewendet wird. Das Ziel ist eine Teilmenge der Merkmale aus dem ursprünglichen Dataset auszuwählen, die dessen Dimensionen reduziert, indem Sie einen minimalen Satz von Merkmalen zur Darstellung der maximalen Abweichung in den Daten verwendet. Diese Teilmenge der Merkmale enthält dann die einzigen Merkmale, die zum Trainieren des Modells enthalten sein sollen. Die Merkmalsauswahl dient zwei Hauptfunktionen. 

* Zunächst erhöht die Merkmalsauswahl häufig die Klassifizierungsgenauigkeit durch Eliminierung irrelevanter, redundanter oder hochgradig korrelierter Merkmale. 
* Zweitens sinkt die Anzahl an Merkmalen, was den Modelltrainingsvorgang effizienter gestaltet. Dies ist besonders wichtig für Lernmodelle, deren Training teuer ist, wie z. B. Support Vector Machines. 

Obwohl Die Merkmalsauswahl die Anzahl der Merkmale im Dataset reduzieren soll, die zum Trainieren des Modells verwendet werden, wird sie in der Regel nicht mit "Reduzierung der Anzahl der Dimensionen" bezeichnet. Merkmalsauswahlmethoden extrahieren eine Teilmenge der ursprünglichen Merkmale in den Daten, ohne sie zu ändern.  Dimensionsreduktionsverfahren nutzen konstruierte Merkmale, die die ursprünglichen Merkmale transformieren und sie so ändern. Dimensionsreduktionsverfahren sind zum Beispiel Principal Component Analysis, kanonische Korrelationsanalyse und Singular Value Decomposition.

Eine unter anderem häufig angewendete Kategorie von Merkmalsauswahlmethoden in einem überwachten Kontext wird als "Filterbasierte Merkmalsauswahl " bezeichnet. Durch Auswerten der Korrelation zwischen den einzelnen Merkmalen und dem Zielattribut wenden diese Methoden ein statistisches Maß an, um jedem Merkmal eine Bewertung zuzuweisen. Die Merkmale werden dann nach dem Ergebnis geordnet. Das kann helfen, den Schwellenwert zum Beibehalten oder Entfernen eines bestimmten Feature festzulegen. Beispiele für die statistischen Mittel in diesen Methoden sind Pearson-Korrelation, gegenseitige Information und der Chi-Quadrat-Test.

In Azure Machine Learning Studio gibt es für die Merkmalsauswahl Module. Wie in der folgenden Abbildung gezeigt wird, umfassen diese Module [Filterbasierte Merkmalsauswahl][filter-based-feature-selection] und [Fishers Lineare Diskriminatenanalyse][fisher-linear-discriminant-analysis].
 
![Beispiel für Merkmalsauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)


Betrachten Sie beispielsweise die Verwendung von des Moduls [Filterbasierte Merkmalsauswahl][filter-based-feature-selection]. Der Einfachheit halber wird weiterhin das oben beschriebene Text Mining-Beispiel verwendet. Es wird davon ausgegangen, dass ein Regressionsmodell erstellt werden soll, nachdem ein Satz von 256 Merkmalen über das [Feature Hashing][feature-hashing]-Modul erstellt wurde und dass die Antwortvariable "Col1" ist und eine Buchbewertung darstellt, mit Bewertungen im Bereich von 1 bis 5. Legen Sie die Merkmalbewertungsmethode auf Pearson-Korrelation, die Zielspalte auf "Col1" und die Anzahl der gewünschten Funktionen auf 50 fest. Dann erzeugt das Modul [Filterbasierte Merkmalsauswahl][filter-based-feature-selection] ein Dataset mit 50-Funktionen mit dem Zieltattribut "Col1". Die folgende Abbildung zeigt den Ablauf dieses Experiments und die oben beschriebenen Eingabeparameter.

![Beispiel für Merkmalsauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

Die folgende Abbildung zeigt die resultierenden Datasets. Jede Funktion wird basierend auf den Pearson-Korrelation zwischen sich selbst und dem Zielattribut "Col1" bewertet. Die Funktionen mit den besten Werte werden beibehalten.

![Beispiel für Merkmalsauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

Die entsprechenden Ergebnisse der ausgewählten Merkmale sind in der folgenden Abbildung dargestellt.

![Beispiel für Merkmalsauswahl](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Durch Anwenden dieses Moduls [Filterbasierte Merkmalsauswahl][filter-based-feature-selection] werden 50 von 256 Merkmalen ausgewählt, da sie die am meisten korrelierten Merkmale mit der Zielvariablen "Col1" auf Grundlage der Bewertungsmethode Pearson-Korrelation besitzen. 

## Zusammenfassung
Konstruktion und Auswahl von Merkmalen sind zwei häufig ausgeführte Schritte zum Vorbereiten von Trainingsdaten beim Erstellen eines Modells für Machine Learning. Normalerweise wird die Merkmalkonstruktion zuerst durchgeführt, um zusätzliche Merkmale zu generieren, und dann wird die Merkmalsauswahl ausgeführt, um irrelevante, redundante oder hoch korrelierte Merkmale zu entfernen. 

Beachten Sie, dass die Konstruktion und Auswahl von Merkmalen nicht immer unbedingt ausgeführt werden müssen. Ob sie benötigt werden oder nicht, hängt von den vorliegenden oder gesammelten Daten ab, dem gewählten Algorithmus und dem Ziel des Experiments.       

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/

<!--HONumber=54-->