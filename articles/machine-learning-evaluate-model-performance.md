<properties 
	pageTitle="Evaluieren der Modellleistung in Azure Machine Learning | Azure" 
	description="Erläutert, wie die Modellleistung in Azure Machine Learning evaluiert wird." 
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
	ms.date="03/11/2015" 
	ms.author="bradsev" />


# Evaluieren der Modellleistung in Azure Machine Learning

In diesem Thema wird veranschaulicht, wie die Leistung eines Modells in Azure Machine Learning Studio evaluiert wird. Zudem werden die für diese Aufgabe verfügbaren Kennzahlen kurz erläutert. Es werden drei häufig eingesetzte Szenarios des beaufsichtigten Lernens vorgestellt: 

* Regression
* Binäre Klassifikation 
* Multi-Klassen-Klassifikation


Die Evaluierung der Leistung eines Modells ist eine der wesentlichen Phasen im Datenanalyseprozess. Sie gibt an, wie erfolgreich die Bewertung (die Vorhersagen) eines DataSets eines trainierten Modells war. 

In Azure Machine Learning wird die Evaluierung von Modellen durch zwei der wichtigsten Machine Learning-Module unterstützt: **Evaluate Model** und **Cross Validate Model**. Mit diesen Modulen können Sie die Leistung Ihres Modells im Hinblick auf verschiedene Kennzahlen anzeigen, die beim maschinellen Lernen und in der Statistik häufig verwendet werden.

##Evaluierung und Kreuzvalidierung im Vergleich##
Die Evaluierung und die Kreuzvalidierung sind Standardmethoden zum Messen der Leistung von Modellen. Bei beiden Methoden werden Evaluierungskennzahlen generiert, die mit denen anderer Modelle geprüft oder verglichen werden können.

Beim **Evaluate Model** wird ein bewertetes DataSet als Eingabe erwartet (oder zwei DataSets, wenn Sie die Leistung zweier verschiedener Modelle vergleichen möchten). Das bedeutet, dass Sie Ihr Modell mit dem Modul **Train Model** trainieren und mit dem Modul **Score Model** Vorhersagen für ein DataSet treffen müssen, bevor Sie die Ergebnisse evaluieren können. Die Evaluierung basiert auf den bewerteten Bezeichnern/Wahrscheinlichkeiten sowie den tatsächlichen Bezeichnern, die alle vom Modul **Score Model** ausgegeben werden.

Alternativ können Sie mithilfe der Kreuzvalidierung automatisch mehrere Training-Bewertung-Evaluierung-Vorgänge (Aufteilung in 10 Teildatensätze) für verschiedene Teilmengen der Eingabedaten durchführen. Die Eingabedaten werden in 10 Teilmengen aufgeteilt, wobei eine Teilmenge zum Testen und die anderen 9 zum Trainieren vorgesehen sind. Dieser Vorgang wird 10-mal wiederholt, und die Evaluierungskennzahlen werden gemittelt. Damit lässt sich feststellen, wie gut ein Modell neue DataSets verallgemeinert. Das Modul **Cross Validate Model** erfasst ein untrainiertes Modell und ein DataSet mit Bezeichnern und gibt die Evaluierungsergebnisse aller 10 Aufteilungen sowie die gemittelten Ergebnisse aus.

In den folgenden Abschnitten werden einfache Regressions- und Klassifikationsmodelle erstellt und deren Leistung evaluiert. Dazu werden die beiden Module **Evaluate Model** und **Cross Validate Model** verwendet.

##Evaluieren eines Regressionsmodells##
Angenommen, wir möchten den Preis eines Fahrzeugs anhand seiner Eigenschaften wie z. B. Abmessungen, Pferdestärke, Motorisierung usw. vorhersagen. Dies ist ein typischer Regressionsfall, bei dem die Zielvariable (*Preis*) ein stetiger Zahlenwert ist. Dazu kann ein einfaches lineares Regressionsmodell angepasst werden, das mit den Eigenschaftenwerten eines bestimmten Fahrzeugs den Preis des Fahrzeugs vorhersagen kann. Mit diesem Regressionsmodell kann das gleiche DataSet bewertet werden, das für das Training verwendet wurde. Wenn die vorhergesagten Preise für alle Fahrzeuge vorliegen, kann die Leistung des Modells evaluiert werden, indem wir uns ansehen, inwieweit die Vorhersagen im Durchschnitt von den tatsächlichen Preisen abweichen. Zur Veranschaulichung verwenden wir das im Abschnitt **Saved Datasets** in Azure Machine Learning Studio bereitgestellte *Automobile price data (Raw) dataset*.
 
###Erstellen des Experiments###
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio die folgenden Module hinzu:

- Fahrzeugpreisdaten (unformatiert)
- Linear Regression
- Train Model
- Bewertungsmodell
- Modell evaluieren


Verbinden Sie die Ports wie unten in Abbildung 1 dargestellt, und legen Sie für die Spalte "Label" des Moduls **Train Model** den Wert *price* fest.
 
![Evaluating a Regression Model](media/machine-learning-evaluate-model-performance/1.png)

Abbildung 1. Evaluieren eines Regressionsmodells.

###Überprüfen der Evaluierungsergebnisse###
Nach dem Ausführen des Experiments können Sie auf den Ausgabeport des Moduls **Evaluate Model** klicken und *Visualize* auswählen, um die Evaluierungsergebnisse anzuzeigen. Für Regressionsmodelle stehen folgende Evaluierungskennzahlen zur Verfügung: *Mean Absolute Error*, *Root Mean Absolute Error*, *Relative Absolute Error*, *Relative Squared Error* und *Coefficient of Determination*.

Der Begriff "Fehler" gibt hier die Differenz zwischen dem vorhergesagten und dem tatsächlichen Wert an. Normalerweise wird der absolute Wert oder das Quadrat dieser Differenz berechnet, um die gesamte Fehlerabweichung in allen Fällen zu erfassen, da die Differenz zwischen dem vorhergesagten und dem tatsächlichen Wert in einigen Fällen auch negativ sein kann. Die Fehlerkennzahlen messen die Vorhersageleistung eines Regressionsmodells im Hinblick auf die mittlere Abweichung seiner Vorhersagen von den tatsächlichen Werten. Kleinere Fehlerwerte bedeuten, dass das Modell bei Vorhersagen genauer ist. Die Fehlerkennzahl "0" bedeutet, dass das Modell perfekt mit den Daten übereinstimmt.

Das Bestimmtheitsmaß, das auch als "R-Quadrat" notiert wird, ist ebenfalls eine Standardmethode für die Berechnung, wie gut das Vorhersagemodell mit den Daten übereinstimmt. Es kann als der Anteil der Abweichung interpretiert werden, der durch das Modell beschrieben wird. In diesem Fall ist ein höherer Anteil besser, wobei "1" eine perfekte Übereinstimmung angibt.
 
![Linear Regression Evaluation Metrics](media/machine-learning-evaluate-model-performance/2.png)

Abbildung 2. Evaluierungskennzahlen bei der linearen Regression.

###Verwenden der Kreuzvalidierung###
Wie bereits zuvor erwähnt, können Sie mit dem Modul **Cross Validate Model** automatisch wiederholte Trainings-, Bewertungs- und Evaluierungsvorgänge durchführen. In diesem Fall benötigen Sie lediglich ein DataSet, ein untrainiertes Modell und das Modul **Cross Validate Model** (siehe Abbildung unten). Beachten Sie, dass Sie in den Eigenschaften des Moduls **Cross Validate Model** für die Spalte "Label" den Wert *price* festlegen müssen.

![Cross-Validating a Regression Model](media/machine-learning-evaluate-model-performance/3.png)

Abbildung 3. Kreuzvalidierung eines Regressionsmodells.

Nach dem Ausführen des Experiments können Sie die Evaluierungsergebnisse prüfen, indem Sie auf den rechten Ausgangsport des Moduls **Cross Validate Model** klicken. Dadurch werden eine Detailansicht der Kennzahlen für jede Iteration (Aufteilung) und die gemittelten Ergebnisse aller Kennzahlen angezeigt (Abbildung 4).
 
![Cross-Validation Results of a Regression Model](media/machine-learning-evaluate-model-performance/4.png)

Abbildung 4. Ergebnisse der Kreuzvalidierung eines Regressionsmodells.

##Evaluieren eines binären Klassifikationsmodells##
Bei der binären Klassifikation hat die Zielvariable nur zwei mögliche Ergebnisse, z. B.: {0, 1} oder {falsch, wahr}, {negativ, positiv}. Angenommen, Sie erhalten ein DataSet für erwachsene Mitarbeiter mit einigen Variablen zur Demografie und Beschäftigung und werden gebeten, das Einkommensniveau, eine binäre Variable mit den Werten {"<=50K", ">50K"}, vorherzusagen. Mit anderen Worten: Die negative Klasse gibt die Mitarbeiter an, deren Einkommen pro Jahr kleiner oder gleich 50K ist, und die positive Klasse alle anderen Mitarbeiter. Wie beim Regressionsszenario werden ein Modell trainiert, einige Daten bewertet und die Ergebnisse evaluiert. Der hauptsächliche Unterschied besteht hier in der Auswahl der Kennzahlen, die in Azure Machine Learning berechnet und ausgegeben werden. Zur Veranschaulichung des Vorhersageszenarios für das Einkommensniveau wird mit dem [Adult](http://archive.ics.uci.edu/ml/datasets/Adult)-DataSet ein Azure Machine Learning-Experiment erstellt und anschließend die Leistung eines logistischen Zwei-Klassen-Regressionsmodells, ein häufig verwendeter binärer Klassifikator, evaluiert.

###Erstellen des Experiments###
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio die folgenden Module hinzu:

- Binäres Klassifizierungsdataset der Einkommenserhebung für Erwachsene
- Two-Class Logistic Regression
- Train Model
- Bewertungsmodell
- Modell evaluieren

Verbinden Sie die Ports wie unten in Abbildung 5 dargestellt, und legen Sie für die Spalte "Label" des Moduls **Train Model** den Wert *income* fest.

![Evaluating a Binary Classification Model](media/machine-learning-evaluate-model-performance/5.png)

Abbildung 5. Evaluieren eines binären Klassifikationsmodells.

###Überprüfen der Evaluierungsergebnisse###
Nach dem Ausführen des Experiments können Sie auf den Ausgabeport des Moduls **Evaluate Model** klicken und *Visualize* auswählen, um die Evaluierungsergebnisse anzuzeigen (Abbildung 7). Für binäre Klassifikationsmodelle stehen folgende Evaluierungskennzahlen zur Verfügung: *Accuracy*, *Precision*, *Recall*, *F1 Score* und *AUC*. Darüber hinaus gibt das Modul eine Wahrheitsmatrix mit der Anzahl der Fälle "richtig positiv", "falsch negativ", "falsch positiv" und "richtig negativ" sowie den Kurven *ROC*, *Precision/Recall* und *Lift* aus.

Genauigkeit (Accuracy) ist dabei der Anteil der richtig klassifizierten Fälle. Sie ist normalerweise die erste Kennzahl, die Sie sich bei der Evaluierung eines Klassifikators ansehen. Wenn die Testdaten jedoch unausgeglichen sind (die meisten Fälle einer der Klassen angehören) oder Sie überwiegend an der Leistung einer der beiden Klassen interessiert sind, wird mit der Genauigkeit die Effektivität eines Klassifikators nicht wirklich erfasst. Angenommen, beim Klassifikationsszenario für das Einkommensniveau testen Sie Daten, bei denen 99 % der Fälle Mitarbeiter angeben, deren Einkommen pro Jahr kleiner oder gleich 50K ist. Bei der Vorhersage der Klasse "<=50K" für alle Fälle kann eine Genauigkeit von 0,99 erreicht werden. Der Klassifikator scheint in diesem Fall eine durchweg solide Leistung zu liefern, in Wirklichkeit werden aber alle Personen mit höherem Einkommen (die 1 %) nicht richtig klassifiziert.

Aus diesem Grund ist es nützlich, weitere Kennzahlen zu berechnen, die die spezifischeren Aspekte der Evaluierung erfassen. Bevor die Einzelheiten dieser Kennzahlen besprochen werden, ist es wichtig, die Wahrheitsmatrix der Evaluierung einer binären Klassifikation zu verstehen. Die Klassen in den Trainingsdaten können nur zwei mögliche Werte annehmen, die normalerweise positiv oder negativ ausfallen. Die positiven und negativen Fälle, die ein Klassifikator richtig vorhersagt, werden als "richtig positiv" (RP) bzw. "richtig negativ" (RN) bezeichnet. Dementsprechend werden die falsch klassifizierten Fälle als "falsch positiv" (FP) und "falsch negativ" (FN) bezeichnet. Bei der Wahrheitsmatrix handelt es sich um eine tabellarische Aufstellung der Anzahl der Fälle, die sich in eine dieser vier Kategorien einordnen lassen. In Azure Machine Learning wird automatisch festgelegt, welche der beiden Klassen im DataSet als positive Klasse eingeordnet wird. Wenn es sich bei den Klassenwerten um boolesche Werte oder ganze Zahlen handelt, werden die mit "richtig" oder "1" kategorisierten Fälle der positiven Klasse zugewiesen. Wenn die Bezeichner Zeichenfolgen sind, wie hier im Beispiel zum DataSet zur Einkommenserhebung, werden sie alphabetisch sortiert. Die erste Ebene wird als negative Klasse eingestuft und die zweite Ebene als positive Klasse.

![Binary Classification Confusion Matrix](media/machine-learning-evaluate-model-performance/6a.png)

Abbildung 6. Wahrheitsmatrix der binären Klassifikation.

Im Hinblick auf die Klassifikation des Einkommens in unserem Beispiel sollen mehrere Evaluierungsfragen gestellt werden, mit denen die Leistung des verwendeten Klassifikators bewertet werden kann. Eine sehr nahe liegende Frage ist: "Wie viele der Personen, für die im Modell vorhergesagt wurde, dass ihr Einkommen >50K beträgt (RP+FP), wurden richtig klassifiziert (RP)?" Diese Frage kann durch Prüfen der **Präzision** (Precision) des Modells beantwortet werden, die den Anteil der positiven Fälle angibt, die richtig klassifiziert sind: RP/(RP+FP). Eine weitere Frage lautet: "Wie viele aller besserverdienenden Mitarbeiter mit einem Einkommen von >50K (RP+FN) wurden mit dem Klassifikator richtig klassifiziert (RP)?" Dies ist die **Sensitivität** (Recall) oder die Richtig-Positiv-Rate RP/(RP+FN) des Klassifikators. Sie können feststellen, dass ein offensichtlicher Zusammenhang zwischen Präzision und Sensitivität besteht. Ein relativ ausgeglichenes DataSet vorausgesetzt, weist ein Klassifikator, der überwiegend positive Fälle vorhersagt, eine hohe Sensitivität, jedoch eine eher geringe Präzision auf, da viele der negativen Fälle falsch klassifiziert werden, was zu einer hohen Anzahl falsch-negativer Fälle führt. Um grafisch darzustellen, wie diese beiden Kennzahlen variieren, können Sie in der Ausgabeseite der Evaluierungsergebnisse auf die Kurve für Präzision/Sensitivität ("PRECISION/RECALL") klicken (links oben in Abbildung 7).

![Binary Classification Evaluation Results](media/machine-learning-evaluate-model-performance/7.png)

Abbildung 7. Evaluierungsergebnisse der binären Klassifikation.

Eine weitere zugehörige, häufig verwendete Kennzahl ist das sogenannte **F-Maß** (F1 Score), das Präzision und Sensitivität vereint. Es handelt sich um das harmonische Mittel dieser 2 Kennzahlen, das wie folgt berechnet wird: F1 = 2 (Präzision x Sensitivität) / (Präzision + Sensitivität). Das F-Maß ist eine gute Möglichkeit, die Evaluierung in einer Zahl zusammenzufassen. Es empfiehlt sich jedoch auch immer, die Präzision und Sensitivität zusammen zu betrachten, um besser einschätzen zu können, wie sich ein Klassifikator verhält.

Darüber hinaus können Sie mit der **ROC-Kurve** (Receiver Operating Characteristic; Grenzwertoptimierungskurve) und dem entsprechenden Wert für die **Fläche unter der Kurve** (Area Under the Curve; AUC) die Richtig-Positiv-Rate im Vergleich zur Falsch-Positiv-Rate überprüfen. Je mehr sich diese Kurve der linken oberen Ecke nähert, desto höher ist die Leistung des Klassifikators (d. h., die Richtig-Positiv-Rate wird maximiert, während die Falsch-Positiv-Rate minimiert wird). Kurven, die sich der Diagonale in der Darstellung nähern, sind auf Klassifikatoren zurückzuführen, deren Vorhersagen auf bloßes Raten hinauslaufen.

###Verwenden der Kreuzvalidierung###
Wie im Regressionsbeispiel kann die Kreuzvalidierung durchgeführt werden, um verschiedene Teilmengen der Daten automatisch mehrmals zu trainieren, zu bewerten und zu evaluieren. Zu diesem Zweck können ganz ähnlich das Modul **Cross Validate Model**, ein untrainiertes logistisches Regressionsmodell und ein DataSet verwendet werden. Für die Spalte "Label" muss in den Eigenschaften des Moduls **Cross Validate Model** der Wert *income* festgelegt werden. Nach dem Ausführen des Experiments und dem Klicken auf den rechten Ausgabeport des Moduls **Cross Validate Model** werden die Kennzahlwerte der binären Klassifikation sowie der Mittelwert und die Standardabweichung für jede Aufteilung angezeigt. 
 
![Cross-Validating a Binary Classification Model](media/machine-learning-evaluate-model-performance/8.png)

Abbildung 8. Kreuzvalidierung eines binären Klassifikationsmodells.

![Cross-Validation Results of a Binary Classifier](media/machine-learning-evaluate-model-performance/9.png)

Abbildung 9. Ergebnisse der Kreuzvalidierung eines binären Klassifikators.

##Evaluieren eines Multi-Klassen-Klassifikationsmodells##
In diesem Experiment wird das bekannte [Iris](http://archive.ics.uci.edu/ml/datasets/Iris "Iris")-DataSet verwendet, das Fälle der drei verschiedenen Arten (Klassen) der Iris enthält. Für jeden Fall sind vier Funktionswerte (Länge/Breite des Kelchblatts und Länge/Breite des Blütenblatts) definiert. In den vorhergehenden Experimenten wurden die Modelle mit den gleichen DataSets trainiert und getestet. Hier werden nun mithilfe des Moduls "Split" zwei Teilmengen der Daten erstellt, das Modell mit der ersten Teilmenge trainiert und anschließend mit der zweiten Teilmenge bewertet und evaluiert. 
Das "Iris"-DataSet wird im [UCI Machine Learning Repository](http://archive.ics.uci.edu/ml/index.html) öffentlich zur Verfügung gestellt und kann mit dem Modul **Reader** heruntergeladen werden.

###Erstellen des Experiments###
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio die folgenden Module hinzu:

- Reader
- Multiclass Decision Forest
- Aufteilen
- Train Model
- Bewertungsmodell
- Modell evaluieren

Verbinden Sie die Ports wie unten in Abbildung 10 dargestellt.

Setzen Sie den Index der Spalte "Label" des Moduls "Train Model" auf "5". Das DataSet verfügt über keinen Header, wir wissen jedoch, dass sich die Klassenbezeichner in der fünften Spalte befinden.

Klicken Sie auf das Modul **Reader**, und setzen Sie die *Data source*-Eigenschaft auf *Web URL via HTTP* und *URL* auf "http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data".

Legen Sie im Modul **Split** den Anteil der Fälle fest, die für das Training verwendet werden sollen (z. B. "0.7").
 
![Evaluating a Multiclass Classifier](media/machine-learning-evaluate-model-performance/10.png)

Abbildung 10. Evaluieren eines Multi-Klassen-Klassifikators

###Überprüfen der Evaluierungsergebnisse###
Führen Sie das Experiment aus, und klicken Sie auf den Ausgabeport des Moduls **Evaluate Model**. Die Evaluierungsergebnisse werden in diesem Beispiel in Form einer Wahrheitsmatrix angezeigt. In der Matrix sind die tatsächlichen und vorhergesagten Fälle für alle drei Klassen aufgeführt.
 
![Multiclass Classification Evaluation Results](media/machine-learning-evaluate-model-performance/11.png)

Abbildung 11. Evaluierungsergebnisse der Multi-Klassen-Klassifikation.

###Verwenden der Kreuzvalidierung###
Wie bereits zuvor erwähnt, können Sie mit dem Modul **Cross Validate Model** automatisch wiederholte Trainings-, Bewertungs- und Evaluierungsvorgänge durchführen. Sie benötigen ein DataSet, ein untrainiertes Modell und das Modul **Cross Validate Model** (siehe Abbildung unten). Und wieder müssen Sie die Spalte "Label" des Moduls **Cross Evaluate Model** angeben (Spaltenindex "5" in diesem Beispiel). Nach dem Ausführen des Experiments und dem Klicken auf den rechten Ausgabeport des Moduls **Cross Validate Model** können Sie die Kennzahlwerte sowie den Mittelwert und die Standardabweichung für die einzelnen Aufteilungen überprüfen. Die hier aufgeführten Kennzahlen sind denen sehr ähnlich, die zuvor im Beispiel für die binäre Klassifikation erörtert wurden. Beachten Sie jedoch, dass bei der Multi-Klassen-Klassifikation die Berechnung der Fälle "Richtig positiv/negativ" und "Falsch positiv/negativ" jeweils pro Klasse erfolgt und dass keine allgemeine positive oder negative Klasse vorhanden ist. Wenn beispielsweise die Präzision oder die Sensitivität der Klasse "Iris-setosa" berechnet wird, wird davon ausgegangen, dass es sich dabei um die positive Klasse handelt und dass alle anderen Klassen negativ sind.
 
![Cross-Validating a Multiclass Classification Model](media/machine-learning-evaluate-model-performance/12.png)

Abbildung 12. Kreuzvalidierung eines Multi-Klassen-Klassifikationsmodells.


![Cross-Validation Results of a Multiclass Classification Model](media/machine-learning-evaluate-model-performance/13.png)

Abbildung 13. Ergebnisse der Kreuzvalidierung eines Multi-Klassen-Klassifikationsmodells.

<!--HONumber=49-->