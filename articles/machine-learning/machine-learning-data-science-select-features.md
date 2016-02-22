<properties
	pageTitle="Featureauswahl im Cortana-Analyseprozess | Microsoft Azure" 
	description="Erläutert den Grund zur Featureauswahl und stellt Beispiele der Rolle im Datenaufbereitungsprozess für das maschinelle Lernen vor."
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
	ms.date="02/08/2016"
	ms.author="zhangya;bradsev" />


# Featureauswahl im Cortana-Analyseprozess

In diesem Thema werden die Gründe zur Featureauswahl erläutert und Beispiele der Rolle im Datenaufbereitungsprozess für das maschinelle Lernen vorgestellt. Diese Beispiele stammen aus Azure Machine Learning Studio.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Einführung

In diesem Thema wird der Grund zur Featureauswahl erläutert und Beispiele der Rolle im Datenaufbereitungsprozess für das maschinelle Lernen vorgestellt. Diese Beispiele stammen aus Azure Machine Learning Studio.

Die Entwicklung und die Auswahl der Features sind ein Teil der CAP-Vorgehensweise, die unter [Was ist der Cortana-Analyseprozess?](machine-learning-data-science-the-cortana-analytics-process.md) beschrieben ist. Die Entwicklung und Auswahl von Features stellen einen Teil des Schritts **Entwickeln von Features** des CAP dar. * **Featureentwicklung**: Bei diesem Prozess wird versucht, zusätzliche relevante Features aus den vorhandenen Rohfeatures in den Daten zu erstellen und die Vorhersageleistung des Lernalgorithmus zu steigern. * **Featureauswahl**: Dieser Prozess wählt bei dem Versuch, die Anzahl von Dimensionen des Trainingsproblems zu verringern, die wichtigste Teilmenge der ursprünglichen Datenfeatures aus.

Normalerweise wird die **Featureentwicklung** zuerst ausgeführt, um zusätzliche Features zu generieren, und anschließend wird die **Featureauswahl** ausgeführt, um irrelevante, redundante oder hoch korrelierte Features zu entfernen.


## Filtern von Features aus Ihren Daten – Featureauswahl 

Die Featureauswahl ist ein Prozess, der häufig für die Entwicklung von Trainingsdatasets für die Vorhersagemodellierung, wie z. B. Klassifizierung oder Regressionsaufgaben, angewendet wird. Das Ziel ist, eine Teilmenge der Features aus dem ursprünglichen Dataset auszuwählen, die dessen Dimensionen verringert, indem sie einen minimalen Satz von Features zur Darstellung der maximalen Abweichung in den Daten verwendet. Diese Teilmenge der Features enthält dann die einzigen Features, die zum Trainieren des Modells enthalten sein sollen. Die Featureauswahl dient zwei wesentlichen Zwecken.

* Zunächst steigert die Featureauswahl häufig die Klassifizierungsgenauigkeit durch Eliminieren irrelevanter, redundanter oder hochgradig korrelierter Features.
* Zweitens sinkt die Anzahl von Features, was den Modelltrainingsvorgang effizienter gestaltet. Dies ist besonders wichtig für Lernmodelle, deren Training teuer ist, wie z. B. Support Vector Machines.

Obwohl die Featureauswahl die Anzahl von Features im Dataset reduzieren soll, die zum Trainieren des Modells verwendet werden, wird sie in der Regel nicht mit "Reduzierung der Anzahl von Dimensionen" bezeichnet. Featureauswahlmethoden extrahieren eine Teilmenge der ursprünglichen Features in den Daten, ohne sie zu ändern. Verfahren zur Reduktion der Anzahl von Dimensionen nutzen entwickelte Features, die die ursprünglichen Features transformieren können und sie so ändern. Beispiele für Verfahren zur Verringerung der Anzahl von Dimensionen sind zum Beispiel Principal Component Analysis, kanonische Korrelationsanalyse und Singular Value Decomposition.

Eine unter anderem häufig angewendete Kategorie von Featureauswahlmethoden in einem überwachten Kontext wird als "Filterbasierte Featureauswahl" bezeichnet. Durch Auswerten der Korrelation zwischen den einzelnen Features und dem Zielattribut wenden diese Methoden ein statistisches Maß an, um jedem Feature eine Bewertung zuzuweisen. Die Features werden dann nach dem Ergebnis geordnet. Das kann helfen, den Schwellenwert zum Beibehalten oder Entfernen eines bestimmten Features festzulegen. Beispiele für die statistischen Mittel in diesen Methoden sind Pearson-Korrelation, gegenseitige Information und der Chi-Quadrat-Test.

Azure Machine Learning Studio enthält Module zur Featureauswahl. Wie in der folgenden Abbildung gezeigt wird, gehören hierzu die Module [Filter-Based Feature Selection][filter-based-feature-selection] und [Fisher Linear Discriminant Analysis][fisher-linear-discriminant-analysis].

![Beispiel für Featureauswahl](./media/machine-learning-data-science-select-features/feature-Selection.png)


Betrachten Sie beispielsweise die Verwendung des Moduls [Filter-Based Feature Selection][filter-based-feature-selection]. Der Einfachheit halber wird weiterhin das oben beschriebene Text Mining-Beispiel verwendet. Es wird vorausgesetzt, dass ein Regressionsmodell erstellt werden soll, nachdem ein Satz von 256 Features über das [Feature Hashing][feature-hashing]-Modul erstellt wurde, dass die Antwortvariable "Col1" ist und eine Buchbewertung mit Bewertungen im Bereich von 1 bis 5 darstellt. Setzen Sie die "Feature scoring method" (Featurebewertungsmethode) auf "Pearson Correlation" (Pearson-Korrelation), die "Target Column" (Zielspalte) auf "Col1" und die "Number of desired features" (Anzahl von gewünschten Features) auf 50. Dann erzeugt das Modul [Filter-Based Feature Selection][filter-based-feature-selection] ein DataSet mit 50 Features mit dem Zieltattribut "Col1". Die folgende Abbildung zeigt den Ablauf dieses Experiments und die oben beschriebenen Eingabeparameter.

![Beispiel für Featureauswahl](./media/machine-learning-data-science-select-features/feature-Selection1.png)

Die folgende Abbildung zeigt die resultierenden Datasets. Jedes Feature wird auf Basis der Pearson-Korrelation zwischen sich selbst und dem Zielattribut "Col1" bewertet. Die Features mit den besten Werten werden beibehalten.

![Beispiel für Featureauswahl](./media/machine-learning-data-science-select-features/feature-Selection2.png)

Die entsprechenden Ergebnisse der ausgewählten Features sind in der folgenden Abbildung dargestellt.

![Beispiel für Featureauswahl](./media/machine-learning-data-science-select-features/feature-Selection3.png)

Durch Anwenden dieses Moduls [Filter-Based Feature Selection][filter-based-feature-selection] werden 50 von 256 Features ausgewählt, da sie die am meisten korrelierten Features mit der Zielvariable „Col1“ auf Grundlage der Bewertungsmethode „Pearson Correlation“ besitzen.

## Zusammenfassung
Featureentwicklung und Featureauswahl sind zwei allgemein entwickelte und ausgewählte Features, die die Effizienz des Trainingsprozesses steigern, der versucht, die in den Daten enthaltenen Schlüsselinformationen zu extrahieren. Sie verbessern auch die Leistungsfähigkeit dieser Modelle für eine exakte Klassifizierung der Eingabedaten und verlässlichere Vorhersagen von Ergebnissen, die von Interesse sind. Entwicklung und Auswahl von Features können auch kombiniert werden, damit das Lernen besser rechnerisch verfolgt werden kann. Dies erfolgt durch eine Erweiterung und anschließende Verringerung der Anzahl von Features zum Kalibrieren oder Trainieren eines Modells. Mathematisch gesehen stehen die Features, die zum Trainieren des Modells ausgewählt werden, für einen minimalen Satz von unabhängigen Variablen, die Muster in den Daten erklären und dann erfolgreich Vorhersageergebnisse liefern.

Beachten Sie, dass die Entwicklung bzw. Auswahl von Features nicht immer unbedingt ausgeführt werden müssen. Ob sie benötigt werden oder nicht, hängt von den vorliegenden oder gesammelten Daten ab, dem gewählten Algorithmus und dem Ziel des Experiments.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/
 

<!---HONumber=AcomDC_0211_2016-->