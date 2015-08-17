<properties 
	pageTitle="Auswerten der Modellleistung in Azure Machine Learning | Microsoft Azure" 
	description="Erläutert, wie die Modellleistung in Azure Machine Learning ausgewertet wird." 
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
	ms.date="07/14/2015" 
	ms.author="bradsev;garye" />


# Auswerten der Modellleistung in Azure Machine Learning

In diesem Thema wird veranschaulicht, wie die Leistung eines Modells in Azure Machine Learning Studio ausgewertet wird. Zudem werden die für diese Aufgabe verfügbaren Kennzahlen kurz erläutert. Es werden drei häufig eingesetzte Szenarios des beaufsichtigten Lernens vorgestellt:

* Regression
* Binäre Klassifizierung 
* Multiklassenklassifizierung

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Die Auswertung der Leistung eines Modells ist eine der wesentlichen Phasen im Datenanalyseprozess. Sie gibt an, wie erfolgreich die Bewertung (die Vorhersagen) eines Datasets eines trainierten Modells war.

In Azure Machine Learning wird die Auswertung von Modellen durch zwei der wichtigsten Machine Learning-Module unterstützt: [Evaluate Model][evaluate-model] und [Cross-Validate Model][cross-validate-model]. Mit diesen Modulen können Sie die Leistung Ihres Modells im Hinblick auf verschiedene Kennzahlen anzeigen, die beim maschinellen Lernen und in der Statistik häufig verwendet werden.

##Auswertung und Kreuzvalidierung im Vergleich##
Die Auswertung und die Kreuzvalidierung sind Standardmethoden zum Messen der Leistung von Modellen. Bei beiden Methoden werden Auswertungskennzahlen generiert, die mit denen anderer Modelle geprüft oder verglichen werden können.

Beim [Evaluate Model][evaluate-model] wird ein bewertetes Dataset als Eingabe erwartet (oder zwei Datasets, wenn Sie die Leistung zweier verschiedener Modelle vergleichen möchten). Das bedeutet, dass Sie Ihr Modell mit dem Modul [Train Model][train-model] trainieren und mit dem Modul [Score Model][score-model] Vorhersagen für ein Dataset treffen müssen, bevor Sie die Ergebnisse auswerten können. Die Auswertung basiert auf den bewerteten Bezeichnern/Wahrscheinlichkeiten sowie den tatsächlichen Bezeichnern, die alle vom Modul [Score Model][score-model] ausgegeben werden.

Alternativ können Sie mithilfe der Kreuzvalidierung automatisch mehrere Trainings-/Bewertungs-/Auswertungsvorgänge (Aufteilung in 10 Teildatensätze) für verschiedene Teilmengen der Eingabedaten durchführen. Die Eingabedaten werden in 10 Teilmengen aufgeteilt, wobei eine Teilmenge zum Testen und die anderen 9 zum Trainieren vorgesehen sind. Dieser Vorgang wird 10-mal wiederholt, und die Auswertungskennzahlen werden gemittelt. Damit lässt sich feststellen, wie gut ein Modell neue Datasets verallgemeinert. Das Modul [Cross Validate Model][cross-validate-model] erfasst ein untrainiertes Modell und ein Dataset mit Bezeichnern und gibt die Auswertungsergebnisse aller 10 Aufteilungen sowie die gemittelten Ergebnisse aus.

In den folgenden Abschnitten werden einfache Regressions- und Klassifizierungsmodelle erstellt und deren Leistung evaluiert. Dazu werden die beiden Module [Evaluate Model][evaluate-model] und [Cross Validate Model][cross-validate-model] verwendet.

##Auswerten eines Regressionsmodells##
Angenommen, wir möchten den Preis eines Fahrzeugs anhand seiner Eigenschaften wie z. B. Abmessungen, Pferdestärke, Motorisierung usw. vorhersagen. Dies ist ein typischer Regressionsfall, bei dem die Zielvariable (*Preis*) ein beständiger Zahlenwert ist. Dazu kann ein einfaches lineares Regressionsmodell angepasst werden, das mit den Eigenschaftenwerten eines bestimmten Fahrzeugs den Preis des Fahrzeugs vorhersagen kann. Mit diesem Regressionsmodell kann das gleiche Dataset bewertet werden, das für das Training verwendet wurde. Wenn die vorhergesagten Preise für alle Fahrzeuge vorliegen, kann die Leistung des Modells ausgewertet werden, indem wir uns ansehen, inwieweit die Vorhersagen im Durchschnitt von den tatsächlichen Preisen abweichen. Zur Veranschaulichung verwenden wir das im Abschnitt **Saved Datasets** in Azure Machine Learning Studio bereitgestellte Dataset *Automobile price data (Raw)*.
 
###Erstellen des Experiments###
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio die folgenden Module hinzu:

- Automobile price data (Raw)
- [Linear Regression][linear-regression]
- [Train Model][train-model]
- [Score Model][score-model]
- [Evaluate Model][evaluate-model]


Verbinden Sie die Ports wie unten in Abbildung 1 dargestellt, und legen Sie für die Spalte "Label" des Moduls [Train Model][train-model] den Wert *price* fest.
 
![Auswerten eines Regressionsmodells](media/machine-learning-evaluate-model-performance/1.png)

Abbildung 1. Auswerten eines Regressionsmodells.

###Überprüfen der Auswertungsergebnisse###
Nach dem Ausführen des Experiments können Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model] klicken und *Visualize* auswählen, um die Auswertungsergebnisse anzuzeigen. Für Regressionsmodelle stehen folgende Auswertungskennzahlen zur Verfügung: *Mean Absolute Error*, *Root Mean Absolute Error*, *Relative Absolute Error*, *Relative Squared Error* und *Coefficient of Determination*.

Der Begriff "Fehler" gibt hier die Differenz zwischen dem vorhergesagten und dem tatsächlichen Wert an. Normalerweise wird der absolute Wert oder das Quadrat dieser Differenz berechnet, um die gesamte Fehlerabweichung in allen Fällen zu erfassen, da die Differenz zwischen dem vorhergesagten und dem tatsächlichen Wert in einigen Fällen auch negativ sein kann. Die Fehlerkennzahlen messen die Vorhersageleistung eines Regressionsmodells im Hinblick auf die mittlere Abweichung seiner Vorhersagen von den tatsächlichen Werten. Kleinere Fehlerwerte bedeuten, dass das Modell bei Vorhersagen genauer ist. Die Fehlerkennzahl "0" bedeutet, dass das Modell perfekt mit den Daten übereinstimmt.

Das Bestimmtheitsmaß, das auch als "R-Quadrat" notiert wird, ist ebenfalls eine Standardmethode für die Berechnung, wie gut das Vorhersagemodell mit den Daten übereinstimmt. Es kann als der Anteil der Abweichung interpretiert werden, der durch das Modell beschrieben wird. In diesem Fall ist ein höherer Anteil besser, wobei "1" eine perfekte Übereinstimmung angibt.
 
![Auswertungskennzahlen bei der linearen Regression.](media/machine-learning-evaluate-model-performance/2.png)

Abbildung 2. Auswertungskennzahlen bei der linearen Regression.

###Verwenden der Kreuzvalidierung###
Wie bereits zuvor erwähnt, können Sie mit dem Modul [Cross Validate Model][cross-validate-model] automatisch wiederholte Trainings-, Bewertungs- und Auswertungsvorgänge durchführen. In diesem Fall benötigen Sie lediglich ein Dataset, ein untrainiertes Modell und das Modul [Cross Validate Model][cross-validate-model] (siehe Abbildung unten). Beachten Sie, dass Sie in den Eigenschaften des Moduls [Cross Validate Model][cross-validate-model] für die Spalte "Label" den Wert *price* festlegen müssen.

![Kreuzvalidierung eines Regressionsmodells.](media/machine-learning-evaluate-model-performance/3.png)

Abbildung 3. Kreuzvalidierung eines Regressionsmodells.

Nach dem Ausführen des Experiments können Sie die Auswertungsergebnisse prüfen, indem Sie auf den rechten Ausgangsport des Moduls [Cross Validate Model][cross-validate-model] klicken. Dadurch werden eine Detailansicht der Kennzahlen für jede Iteration (Aufteilung) und die gemittelten Ergebnisse aller Kennzahlen angezeigt (Abbildung 4).
 
![Ergebnisse der Kreuzvalidierung eines Regressionsmodells.](media/machine-learning-evaluate-model-performance/4.png)

Abbildung 4. Ergebnisse der Kreuzvalidierung eines Regressionsmodells.

##Auswerten eines binären Klassifizierungsmodells##
Bei der binären Klassifizierung hat die Zielvariable nur zwei mögliche Ergebnisse, z. B.: {0, 1} oder {falsch, wahrt}, {negativ, positiv}. Angenommen, Sie erhalten ein Dataset mit Mitarbeiterdaten, das verschiedene Variablen zu Demografie und Beschäftigung enthält, und Sie werden gebeten, das Einkommensniveau vorherzusagen, eine binäre Variable mit den Werten {"<=50K", ">50K"}. Mit anderen Worten: Die negative Klasse gibt die Mitarbeiter an, deren Einkommen pro Jahr kleiner oder gleich 50K ist, und die positive Klasse alle anderen Mitarbeiter. Wie beim Regressionsszenario werden ein Modell trainiert, einige Daten bewertet und die Ergebnisse ausgewertet. Der hauptsächliche Unterschied besteht hier in der Auswahl der Kennzahlen, die in Azure Machine Learning berechnet und ausgegeben werden. Zur Veranschaulichung des Vorhersageszenarios für das Einkommensniveau wird mit dem Dataset [Adult](http://archive.ics.uci.edu/ml/datasets/Adult) ein Azure Machine Learning-Experiment erstellt und anschließend die Leistung eines logistischen Zwei-Klassen-Regressionsmodells ausgewertet, einem häufig eingesetzten binärer Klassifikator.

###Erstellen des Experiments###
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio die folgenden Module hinzu:

- Dataset "Adult Census Income Binary Classification"
- [Two-Class Logistic Regression][two-class-logistic-regression]
- [Train Model][train-model]
- [Score Model][score-model]
- [Evaluate Model][evaluate-model]

Verbinden Sie die Ports wie unten in Abbildung 5 dargestellt, und legen Sie für die Spalte "Label" des Moduls [Train Model][train-model] den Wert *income* fest.

![Auswerten eines Modells für die binäre Klassifizierung](media/machine-learning-evaluate-model-performance/5.png)

Abbildung 5. Auswerten eines binären Klassifizierungsmodells.

###Überprüfen der Auswertungsergebnisse###
Nach dem Ausführen des Experiments können Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model] klicken und *Visualize* auswählen, um die Auswertungsergebnisse anzuzeigen (Abbildung 7). Für binäre Klassifizierungsmodelle stehen folgende Auswertungskennzahlen zur Verfügung: *Accuracy*, *Precision*, *Recall*, *F1 Score* und *AUC*. Darüber hinaus gibt das Modul eine Wahrheitsmatrix mit der Anzahl der Fälle "richtig positiv", "falsch negativ", "falsch positiv" und "richtig negativ" sowie den Kurven *ROC*, *Precision/Recall* und *Lift* aus.

Genauigkeit (Accuracy) ist dabei der Anteil der richtig klassifizierten Fälle. Sie ist normalerweise die erste Kennzahl, die Sie sich bei der Auswertung eines Klassifikators ansehen. Wenn die Testdaten jedoch unausgeglichen sind (die meisten Fälle einer der Klassen angehören) oder Sie überwiegend an der Leistung einer der beiden Klassen interessiert sind, wird mit der Genauigkeit die Effektivität eines Klassifikators nicht wirklich erfasst. Angenommen, beim Klassifizierungsszenario für das Einkommensniveau testen Sie Daten, bei denen 99 % der Fälle Mitarbeiter angeben, deren Einkommen pro Jahr kleiner oder gleich 50K ist. Bei der Vorhersage der Klasse "<=50K" für alle Fälle kann eine Genauigkeit von 0,99 erreicht werden. Der Klassifikator scheint in diesem Fall eine durchweg solide Leistung zu liefern, in Wirklichkeit werden aber alle Personen mit höherem Einkommen (die 1 %) nicht richtig klassifiziert.

Aus diesem Grund ist es nützlich, weitere Kennzahlen zu berechnen, die die spezifischeren Aspekte der Auswertung erfassen. Bevor die Einzelheiten dieser Kennzahlen besprochen werden, ist es wichtig, die Wahrheitsmatrix der Auswertung einer binären Klassifizierung zu verstehen. Die Klassen in den Trainingsdaten können nur zwei mögliche Werte annehmen, die normalerweise positiv oder negativ ausfallen. Die positiven und negativen Fälle, die ein Klassifikator richtig vorhersagt, werden als "richtig positiv" (RP) bzw. "richtig negativ" (RN) bezeichnet. Dementsprechend werden die falsch klassifizierten Fälle als "falsch positiv" (FP) und "falsch negativ" (FN) bezeichnet. Bei der Wahrheitsmatrix handelt es sich um eine tabellarische Aufstellung der Anzahl der Fälle, die sich in eine dieser vier Kategorien einordnen lassen. In Azure Machine Learning wird automatisch festgelegt, welche der beiden Klassen im Dataset als positive Klasse eingeordnet wird. Wenn es sich bei den Klassenwerten um boolesche Werte oder ganze Zahlen handelt, werden die mit "richtig" oder "1" kategorisierten Fälle der positiven Klasse zugewiesen. Wenn die Bezeichner Zeichenfolgen sind, wie hier im Beispiel zum Dataset zur Einkommenserhebung, werden sie alphabetisch sortiert. Die erste Ebene wird als negative Klasse eingestuft und die zweite Ebene als positive Klasse.

![Wahrheitsmatrix der binären Klassifizierung.](media/machine-learning-evaluate-model-performance/6a.png)

Abbildung 6. Wahrheitsmatrix der binären Klassifizierung.

Im Hinblick auf die Klassifizierung des Einkommens in unserem Beispiel sollen mehrere Auswertungsfragen gestellt werden, mit denen die Leistung des verwendeten Klassifikators bewertet werden kann. Eine sehr nahe liegende Frage ist: "Wie viele der Personen, für die im Modell vorhergesagt wurde, dass ihr Einkommen >50K beträgt (RP+FP), wurden richtig klassifiziert (RP)?" Diese Frage kann durch Prüfen der Genauigkeit (**Precision**) des Modells beantwortet werden, die den Anteil der positiven Fälle angibt, die richtig klassifiziert wurden: RP/(RP+FP). Eine weitere Frage lautet: "Wie viele aller besserverdienenden Mitarbeiter mit einem Einkommen von >50K (RP+FN) wurden mit dem Klassifikator richtig klassifiziert (RP)?" Dies ist die Sensitivität (**Recall**) oder die Richtig-Positiv-Rate: RP/(RP+FN) des Klassifikators. Sie können feststellen, dass ein offensichtlicher Zusammenhang zwischen Präzision und Sensitivität besteht. Ein relativ ausgeglichenes Dataset vorausgesetzt, weist ein Klassifikator, der überwiegend positive Fälle vorhersagt, eine hohe Sensitivität, jedoch eine eher geringe Präzision auf, da viele der negativen Fälle falsch klassifiziert werden, was zu einer hohen Anzahl falsch-negativer Fälle führt. Um grafisch darzustellen, wie diese beiden Kennzahlen variieren, können Sie in der Ausgabeseite der Auswertungsergebnisse auf die Kurve für Präzision/Sensitivität ("PRECISION/RECALL") klicken (links oben in Abbildung 7).

![Auswertungsergebnisse der binären Klassifizierung.](media/machine-learning-evaluate-model-performance/7.png) Abbildung 7. Auswertungsergebnisse der binären Klassifizierung.

Eine weitere zugehörige, häufig verwendete Kennzahl ist das sogenannte F-Maß (**F1 Score**), das sowohl Genauigkeit als auch Sensitivität berücksichtigt. Es handelt sich um das harmonische Mittel dieser 2 Kennzahlen, das wie folgt berechnet wird: F1 = 2 (Genauigkeit x Sensitivität) / (Genauigkeit + Sensitivität). Das F-Maß ist eine gute Möglichkeit, die Auswertung in einer Zahl zusammenzufassen. Es empfiehlt sich jedoch auch immer, die Präzision und Sensitivität zusammen zu betrachten, um besser einschätzen zu können, wie sich ein Klassifikator verhält.

Darüber hinaus können Sie mit der Grenzwertoptimierungskurve **(Receiver Operating Characteristic, ROC)** und dem entsprechenden Wert für die Fläche unter der Kurve **(Area Under the Curve, AUC)** die Richtig-Positiv-Rate im Vergleich zur Falsch-Positiv-Rate überprüfen. Je mehr sich diese Kurve der linken oberen Ecke nähert, desto höher ist die Leistung des Klassifikators (d. h., die Richtig-Positiv-Rate wird maximiert, während die Falsch-Positiv-Rate minimiert wird). Kurven, die sich der Diagonale in der Darstellung nähern, sind auf Klassifikatoren zurückzuführen, deren Vorhersagen auf bloßes Raten hinauslaufen.

###Verwenden der Kreuzvalidierung###
Wie im Regressionsbeispiel kann die Kreuzvalidierung durchgeführt werden, um verschiedene Teilmengen der Daten automatisch mehrmals zu trainieren, zu bewerten und auszuwerten. In ähnlicher Weise kann das Modul [Cross Validate Model][cross-validate-model], ein untrainiertes logistisches Regressionsmodell, und ein Dataset verwendet werden. Für die Spalte "Label" muss in den Eigenschaften des Moduls [Cross Validate Model][cross-validate-model] der Wert *income* festgelegt werden. Nach dem Ausführen des Experiments und dem Klicken auf den rechten Ausgabeport des Moduls [Cross Validate Model][cross-validate-model] werden die Kennzahlwerte der binären Klassifizierung sowie der Mittelwert und die Standardabweichung für jede Aufteilung angezeigt.
 
![Kreuzvalidierung eines binären Klassifizierungsmodells.](media/machine-learning-evaluate-model-performance/8.png)

Abbildung 8. Kreuzvalidierung eines binären Klassifizierungsmodells.

![Ergebnisse der Kreuzvalidierung eines binären Klassifikators.](media/machine-learning-evaluate-model-performance/9.png)

Abbildung 9. Ergebnisse der Kreuzvalidierung eines binären Klassifikators.

##Auswerten eines Modells für die Multiklassenklassifizierung##
In diesem Experiment wird das beliebte Dataset [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris") verwendet, das Fälle der drei verschiedenen Typen (Klassen) der Iris enthält. Für jeden Fall sind vier Funktionswerte (Länge/Breite des Kelchblatts und Länge/Breite des Blütenblatts) definiert. In den vorhergehenden Experimenten wurden die Modelle mit den gleichen Datasets trainiert und getestet. Hier werden nun mithilfe des Moduls [Split][split] zwei Teilmengen der Daten erstellt, das Modell mit der ersten Teilmenge trainiert und anschließend mit der zweiten Teilmenge bewertet und ausgewertet. Das Iris-Dataset wird im [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/index.html) öffentlich zur Verfügung gestellt und kann mit dem [Reader][reader]-Modul heruntergeladen werden.

###Erstellen des Experiments###
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio die folgenden Module hinzu:

- [Reader][reader]
- [Multiclass Decision Forest][multiclass-decision-forest]
- [Split][split]
- [Train Model][train-model]
- [Score Model][score-model]
- [Evaluate Model][evaluate-model]

Verbinden Sie die Ports wie unten in Abbildung 10 dargestellt.

Setzen Sie den Index der Spalte "Label" des Moduls [Train Model][train-model] auf 5. Das Dataset verfügt über keinen Header, wir wissen jedoch, dass sich die Klassenbezeichner in der fünften Spalte befinden.

Klicken Sie auf das [Reader][reader]-Modul, und legen Sie die *Data source*-Eigenschaft auf *Web URL via HTTP* und die *URL* auf http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data fest.

Legen Sie im Modul [Split][split] den Anteil der Fälle fest, die für das Training verwendet werden sollen (z. B. "0.7").
 
![Auswerten eines Multiklassenklassifikators](media/machine-learning-evaluate-model-performance/10.png)

Abbildung 10. Auswerten eines Multiklassenklassifikators

###Überprüfen der Auswertungsergebnisse###
Führen Sie das Experiment aus, und klicken Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model]. Die Auswertungsergebnisse werden in diesem Beispiel in Form einer Wahrheitsmatrix angezeigt. In der Matrix sind die tatsächlichen und vorhergesagten Fälle für alle drei Klassen aufgeführt.
 
![Auswertungsergebnisse der Multiklassenklassifizierung.](media/machine-learning-evaluate-model-performance/11.png)

Abbildung 11. Auswertungsergebnisse der Multiklassenklassifizierung.

###Verwenden der Kreuzvalidierung###
Wie bereits zuvor erwähnt, können Sie mit dem Modul [Cross Validate Model][cross-validate-model] automatisch wiederholte Trainings-, Bewertungs- und Auswertungsvorgänge durchführen. Sie benötigen ein Dataset, ein untrainiertes Modell und das Modul [Cross Validate Model][cross-validate-model] (siehe Abbildung unten). Und wieder müssen Sie die Spalte "Label" des Moduls [Cross Evaluate Model][cross-validate-model] angeben (in diesem Fall Spaltenindex "5"). Nach dem Ausführen des Experiments und dem Klicken auf den rechten Ausgabeport des Moduls [Cross Validate Model][cross-validate-model] können Sie die Kennzahlwerte sowie den Mittelwert und die Standardabweichung für die einzelnen Aufteilungen überprüfen. Die hier aufgeführten Kennzahlen sind denen sehr ähnlich, die zuvor im Beispiel für die binäre Klassifizierung erörtert wurden. Beachten Sie jedoch, dass bei der Multiklassenklassifizierung die Berechnung der Fälle "Richtig positiv/negativ" und "Falsch positiv/negativ" jeweils pro Klasse erfolgt und dass keine allgemeine positive oder negative Klasse vorhanden ist. Wenn beispielsweise die Präzision oder die Sensitivität der Klasse "Iris-setosa" berechnet wird, wird davon ausgegangen, dass es sich dabei um die positive Klasse handelt und dass alle anderen Klassen negativ sind.
 
![Kreuzvalidierung eines Modells für die Multiklassenklassifizierung.](media/machine-learning-evaluate-model-performance/12.png)

Abbildung 12: Kreuzvalidierung eines Modells für die Multiklassenklassifizierung


![Ergebnisse der Kreuzvalidierung eines Modells für die Multiklassenklassifizierung.](media/machine-learning-evaluate-model-performance/13.png)

Abbildung 13: Ergebnisse der Kreuzvalidierung eines Modells für die Multiklassenklassifizierung.


<!-- Module References -->
[cross-validate-model]: https://msdn.microsoft.com/library/azure/75fb875d-6b86-4d46-8bcc-74261ade5826/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[multiclass-decision-forest]: https://msdn.microsoft.com/library/azure/5e70108d-2e44-45d9-86e8-94f37c68fe86/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-logistic-regression]: https://msdn.microsoft.com/library/azure/b0fd7660-eeed-43c5-9487-20d9cc79ed5d/
 

<!---HONumber=August15_HO6-->