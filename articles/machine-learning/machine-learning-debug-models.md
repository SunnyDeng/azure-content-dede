<properties 
	pageTitle="Debuggen Ihres Modells in Azure Machine Learning | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Ihr Modell in Azure Machine Learning debuggen." 
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
	ms.date="09/08/2015" 
	ms.author="bradsev;garye" />

# Debuggen Ihres Modells in Azure Machine Learning

In diesem Artikel wird erläutert, wie Sie Ihre Modelle in Microsoft Azure Machine Learning debuggen. Er behandelt insbesondere die möglichen Gründe, warum bei der Ausführung eines Modells die folgenden zwei Fehlerszenarios auftreten können:

* Das [Train Model][train-model]-Modul löst einen Fehler aus. 
* Das [Score Model][score-model]-Modul liefert falsche Ergebnisse. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## "Train Model"-Modul löst einen Fehler aus

![image1](./media/machine-learning-debug-models/train_model-1.png)

Für das [Train Model][train-model]-Modul werden die folgenden 2 Eingaben erwartet:

1. Der Typ des Klassifizierungs- oder Regressionsmodells aus der in Azure Machine Learning bereitgestellten Modellsammlung
2. Die Trainingsdaten mit einer angegebenen Spalte "Label" Die Spalte "Label" gibt die vorherzusagende Variable an. Bei den restlichen Spalten wird davon ausgegangen, dass es sich um Funktionen handelt.

Dieses Modul löst in den folgenden Fällen einen Fehler aus:

1. Die Spalte "Label" wurde falsch angegeben, da entweder als Bezeichner (Label) mehrere Spalten ausgewählt sind oder ein falscher Spaltenindex ausgewählt ist. Der zweite Fall gilt beispielsweise, wenn der Spaltenindex "30" für ein Eingabe-Dataset verwendet wurde, das nur 25 Spalten umfasst.

2. Das Dataset enthält keine Spalten mit Funktionen. Wenn das Eingabe-Dataset beispielsweise nur 1 Spalte umfasst, die als Spalte "Label" gekennzeichnet ist, sind keine Funktionen vorhanden, mit denen das Modell erstellt werden kann. In diesem Fall löst das [Train Model][train-model]-Modul einen Fehler aus.

3. Das Eingabe-Dataset (Funktionen oder Bezeichner) enthält "Infinity" als Wert.


## "Score Model"-Modul liefert falsche Ergebnisse

![image2](./media/machine-learning-debug-models/train_test-2.png)

In einem typischen Trainings- und Testdiagramm für das beaufsichtigte Lernen teilt das [Split][split]-Modul das ursprüngliche Dataset in zwei Teile auf: in einen Teil, der zum Trainieren des Modells verwendet wird, und in einen zweiten Teil, mit dem die Leistung des trainierten Modells für Daten bewertet wird, für die es nicht trainiert wurde. Mit dem trainierten Modell werden dann die Testdaten bewertet und anschließend die Ergebnisse evaluiert, um die Genauigkeit des Modells zu bestimmen.

Für das [Score Model][score-model]-Modul sind zwei Eingaben erforderlich:

1. Die Ausgabe eines trainierten Modells aus dem [Train Model][train-model]-Modul
2. Ein Dataset für die Bewertung, für das das Modell nicht trainiert wurde

Es kann vorkommen, dass das [Score Model][score-model]-Modul falsche Ergebnisse liefert, auch wenn das Experiment erfolgreich ausgeführt wird. Dies kann durch verschiedene Szenarios verursacht werden:

1. Wenn der angegebene Bezeichner kategorisch ist und ein Regressionsmodell für die Daten trainiert wird, gibt das [Score Model][score-model]-Modul eine falsche Ausgabe aus. Dies ist darauf zurückzuführen, dass für die Regression eine stetige Antwortvariable erforderlich ist. In diesem Fall empfiehlt sich die Verwendung eines Klassifizierungsmodells. 
2. In ähnlicher Weise liefert ein Klassifizierungsmodell möglicherweise unerwünschte Ergebnisse, wenn es für ein Dataset mit Gleitkommazahlen in der Spalte "Label" trainiert wird. Dies liegt daran, dass für die Klassifizierung eine diskrete Antwortvariable erforderlich ist, die nur Werte zulässt, die innerhalb einer begrenzten und normalerweise eher kleinen Gruppe von Klassen liegen.
3. Wenn das Dataset für die Bewertung nicht alle Funktionen enthält, mit denen das Modell trainiert wird, gibt das [Score Model][score-model]-Modul einen Fehler aus.
4. Das [Score Model][score-model]-Modul gibt keine Ergebnisse für eine Zeile im Dataset für die Bewertung aus, die keinen Wert oder einen unendlichen Wert für die zugehörigen Funktionen enthält.
5. Das [Score Model][score-model]-Modul generiert möglicherweise identische Ausgaben für alle Zeilen im Dataset für die Bewertung. Dies ist z. B. möglich, wenn bei der Klassifizierung mithilfe von Decision Forests die ausgewählte minimale Anzahl der Stichproben pro Leaf-Knoten höher ist als die Anzahl der verfügbaren Trainingsbeispiele.


<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
 

<!---HONumber=Oct15_HO3-->