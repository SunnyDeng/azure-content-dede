<properties 
	pageTitle="Interpretieren von Modellergebnissen beim maschinellen Lernen | Microsoft Azure" 
	description="Auswahl des optimalen Parametersatzes für einen Algorithmus mit Verwendung und Visualisierung von Bewertungsmodellausgaben." 
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
	ms.date="12/04/2015" 
	ms.author="bradsev" />


# Interpretieren von Modellergebnissen in Azure Machine Learning 
 
**Verstehen und Visualisieren der "Score Model"-Ausgabe** In diesem Thema werden die Visualisierung und das Interpretieren der Vorhersageergebnisse in Azure Machine Learning Studio erläutert. Nachdem Sie ein Modell trainiert und darauf basierend Vorhersagen erstellt (das Modell ausgewertet) haben, müssen Sie das erhaltene Vorhersageergebnis verstehen und interpretieren.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Es gibt vier Hauptarten von Machine Learning-Modellen in Azure Machine Learning:

* Klassifizierung
* Clustering
* Regression
* Empfehlungssysteme

Folgende Module, mit denen Vorhersagen auf Basis dieser Module erstellt werden, führen eine "Bewertung" mit einigen Testdaten durch:

* [Score Model][score-model] ist ein Modul für Klassifizierung und Regression; 
* [Assign to Clusters][assign-to-clusters] ist ein Modul für das Clustering 
* [Score Matchbox Recommender][score-matchbox-recommender] ist ein Modul für Empfehlungssysteme 
 
Dieses Dokument erläutert, wie Sie die Vorhersageergebnisse für jedes dieser Module interpretieren können. Einen Überblick über diese Modellarten finden Sie unter [Auswählen von Parametern zum Optimieren Ihres Algorithmus in Azure Machine Learning](machine-learning-algorithm-parameters-optimize.md).

Dieses Thema behandelt die Interpretation der Vorhersage, aber nicht die Modellauswertung. Weitere Informationen zum Auswerten Ihres Modells finden Sie unter [Auswerten der Modellleistung in Azure Machine Learning](machine-learning-evaluate-model-performance.md).

Wenn Sie gerade erst in Azure Machine Learning einsteigen und Hilfe beim Erstellen eines einfachen Experiments benötigen, finden Sie diese in Azure Machine Learning Studio unter [Erstellen eines einfachen Experiments in Azure Machine Learning Studio](machine-learning-create-experiment.md).

##Klassifizierung
Es gibt zwei Unterkategorien von Klassifizierungsproblemen:

* Probleme mit nur zwei Klassen (Zwei-Klassen- oder binäre Klassifizierung) 
* Probleme mit mehr als zwei Klassen (Multiklassenklassifizierung) 

Azure Machine Learning verfügt über verschiedene Module für den Umgang mit jedem dieser Klassifizierungstypen. Doch ihre Vorhersageergebnisse werden in sehr ähnlicher Weise interpretiert. Zuerst werden Klassifizierungsprobleme mit zwei und anschließend Probleme mit mehreren Klassen erläutert.

###Klassifizierung mit zwei Klassen
**Beispielexperiment**

Ein Beispiel für ein Klassifizierungsproblem mit zwei Klassen ist die Klassifizierung von Schwertlilien (Iris): bei dieser Aufgabe werden Schwertlilien auf Basis ihrer Features klassifiziert. Beachten Sie, dass das in Azure Machine Learning bereitgestellte Iris-Dataset eine Teilmenge des beliebten [Iris-Dataset](http://en.wikipedia.org/wiki/Iris_flower_data_set) ist und nur Instanzen von zwei Blumenarten (Klasse 0 und 1) enthält. Es gibt vier Features für jede Blume (Länge und Breite des Kelchblatts sowie Länge und Breite des Kronblatts).

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1.png)

Abbildung 1: Experiment mit Schwertlilien für ein Klassifizierungsproblem mit zwei Klassen

Es wurde ein Experiment ausgeführt, um dieses Problem zu beheben, wie in Abbildung 1 dargestellt. Ein zweiklassiges Modell mit gewichtetem Entscheidungsbaum wurde trainiert und bewertet. Jetzt können Sie die Vorhersageergebnisse aus dem Modul [Score Model][score-model] grafisch darstellen, indem Sie auf den Ausgabeport des Moduls [Score Model][score-model] und dann im angezeigten Menü auf **Visualize** klicken. Hierdurch werden die Bewertungsergebnisse angezeigt, wie in Abbildung 2 dargestellt.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/2.png)

Abbildung 2: Visualisieren der Ergebnismodellergebnisse bei der Zwei-Klassen-Klassifizierung

**Ergebnisinterpretation**

Es gibt in der Ergebnistabelle sechs Spalten. Die vier linken Spalten sind die vier Features. Die zwei rechten Spalten, "Scored Labels" und "Scored Probabilities", beinhalten die Vorhersageergebnisse. Die Spalte "Scored Probabilities" zeigt die Wahrscheinlichkeit, dass eine Blume zur positiven Klasse (Klasse 1) gehört. Zum Beispiel bedeutet die erste Zahl 0,028571 in der Spalte, dass eine Wahrscheinlichkeit von 0,028571 besteht, dass die erste Blume der Klasse 1 angehört. Die Spalte "Scored Labels" zeigt die vorhergesagte Klasse für jede Blume. Dies basiert auf der Spalte "Scored Probabilities". Wenn die ausgewertete Wahrscheinlichkeit einer Blume größer als 0,5 ist, wird sie als Klasse 1 vorhergesagt, andernfalls als Klasse 0.

**Webdienstveröffentlichung**

Sobald Sie die Vorhersageergebnisse verstanden und als solide eingestuft haben, kann das Experiment als Webdienst veröffentlicht werden, damit es in verschiedenen Anwendungen bereitgestellt und aufgerufen werden kann, um Klassenvorhersagen zu allen neuen Schwertlilien abzurufen. Informationen dazu, wie Sie ein Trainingsexperiment in ein Bewertungsexperiment ändern und als Webdienst veröffentlichen können, finden Sie unter [Veröffentlichen des Azure Machine Learning-Webdiensts](machine-learning-walkthrough-5-publish-web-service.md). Dieses Verfahren liefert Ihnen ein Bewertungsexperiment wie in Abbildung 3 dargestellt.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/3.png)

Abbildung 3: Bewertungsexperiment mit Schwertlilien für ein Klassifizierungsproblem mit zwei Klassen

Jetzt müssen Sie die Eingabe und Ausgabe für den Webdienst festlegen. Natürlich erfolgt die Eingabe über den rechten Eingangsport von [Score Model][score-model], also die Eingabe der Schwertlilienfeatures. Die Wahl der Ausgabe hängt davon ab, ob Sie an der vorhergesagten Klasse (ausgewerteter Bezeichner), der ausgewerteten Wahrscheinlichkeit oder an beiden Werten interessiert sind. Hier wird vorausgesetzt, dass Sie an beiden Werten interessiert sind. Um die gewünschten Ausgabespalten auszuwählen, müssen Sie das Modul [Project Columns][project-columns] verwenden. Klicken Sie auf das Modul [Project Columns][project-columns], klicken Sie im rechten Bereich auf **Launch column selector**, und wählen Sie **Scored Labels** und **Scored Probabilities**. Nach dem Festlegen des Ausgangsports für das Modul [Project Columns][project-columns] und erneutem Ausführen sollten Sie das Bewertungsexperiment als Webdienst veröffentlichen können, indem Sie unten auf die Schaltfläche **PUBLISH WEB SERVICE** klicken. Das letzte Experiment sieht aus wie in Abbildung 4.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4.png)

Abbildung 4: Letztes Bewertungsexperiment mit Schwertlilien für ein Klassifizierungsproblem mit zwei Klassen

Nach Ausführung des Webdiensts und Eingabe einiger Featurewerte einer Testinstanz werden zwei Zahlen als Ergebnis zurückgegeben. Die erste Zahl ist der ausgewertete Bezeichner, die zweite ist die ausgewertete Wahrscheinlichkeit. Diese Blume wird mit einer Wahrscheinlichkeit von 0,9655 als Klasse 1 vorhergesagt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/4_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5.png)

Abbildung 5: Webdienstergebnis der Klassifizierung der Schwertlilien mit zwei Klassen

###Klassifizierung mit mehreren Klassen
**Beispielexperiment**

In diesem Experiment führen Sie als Beispiel der Klassifizierung mit mehreren Klassen eine Buchstabenerkennung durch. Die Klassifizierung versucht, einen bestimmten Buchstaben (Klasse) anhand von Handschriftattributwerten vorherzusagen, die aus Handschriftbildern extrahiert werden. In den Trainingsdaten gibt es sechzehn Features, die aus Handschriftbildern extrahiert wurden. Die 26 Buchstaben bilden die 26 Klassen. Zum Trainieren eines Klassifizierungsmodells mit mehreren Klasse zur Buchstabenerkennung und Vorhersage auf Basis derselben Featuregruppe bei einem Testdataset wurde ein Experiment erstellt, wie in Abbildung 6 gezeigt.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/6.png)

Abbildung 6: Experiment zur Buchstabenerkennung bei einem Klassifizierungsproblem mit mehreren Klassen

Visualisieren Sie die Ergebnisse aus dem Modul [Score Model][score-model], indem Sie mit der rechten/linken Maustaste auf den Ausgabeport des Moduls [Score Model][score-model] und dann auf **Visualize** klicken. Es sollte ein Fenster wie in Abbildung 7 angezeigt werden.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/7.png)

Abbildung 7: Visualisieren des Ergebnisses der Modellbewertung bei der Multiklassenklassifizierung

**Ergebnisinterpretation**

Die linken sechzehn Spalten stellen die Featurewerte des Testsatzes dar. Die Spalten mit den Namen "Scored Probabilities for Class 'XX'" entsprechen der Spalte "Scored Probabilities" bei zwei Klassen. Sie zeigen die Wahrscheinlichkeit, dass der entsprechende Eintrag in einer bestimmten Klasse liegt. Für den ersten Eintrag beträgt die Wahrscheinlichkeit z. B. 0,003571, dass es sich um ein "A" handelt, 0,000451, dass es sich um ein "B" handelt usw. Die letzte Spalte "Scored Labels" entspricht "Scored Labels" bei zwei Klassen. Die Klasse mit der größten ausgewerteten Wahrscheinlichkeit wird als die vorhergesagte Klasse des entsprechenden Eintrags ausgewählt. Beispielsweise ist "F" der ausgewertete Bezeichner für den ersten Eintrag, da es mit größter Wahrscheinlichkeit (0,916995) ein "F" ist.

**Webdienstveröffentlichung**

Statt [Project Columns][project-columns] zu verwenden, um einige Spalten als Ausgabe des Webdiensts auszuwählen, rufen Sie diesmal den ausgewerteten Bezeichner für jeden Eintrag und die Wahrscheinlichkeit des ausgewerteten Bezeichners ab. Die grundlegende Logik ist, die größte Wahrscheinlichkeit unter allen ausgewerteten Wahrscheinlichkeiten zu finden. Zu diesem Zweck müssen Sie das [Execute R Script][execute-r-script]-Modul verwenden. Der R-Code ist in Abbildung 8 dargestellt, das Experiment in Abbildung 9.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/8.png)

Abbildung 8: R-Code für das Extrahieren von "Scored Labels" und der zugehörigen Wahrscheinlichkeiten der Bezeichner
  
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9.png)

Abbildung 9: Endgültiges Bewertungsexperiment zur Buchstabenerkennung bei einem Klassifizierungsproblem mit mehreren Klassen

Wenn Sie nach dem Veröffentlichen und Ausführen des Webdiensts einige Featurewerte eingeben, entspricht das ausgegebene Ergebnis Abbildung 10. Dieser handgeschriebene Buchstabe mit den extrahierten sechzehn Features wird mit einer Wahrscheinlichkeit von 0,9715 als "T" vorhergesagt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/10.png)

Abbildung 10: Webdienstergebnis der Schwertlilienklassifizierung mit zwei Klassen

##Regression

Regressionsprobleme unterscheiden sich von Klassifizierungsproblemen. Bei einem Klassifizierungsproblem versuchen Sie, diskrete Klassen vorherzusagen, zum Beispiel die Klasse, der eine Schwertlilie angehört. Aber bei einem Regressionsproblem versuchen Sie, eine kontinuierliche Variable, z. B. den Preis eines Autos, vorherzusagen, wie im folgenden Beispiel gezeigt wird.

**Beispielexperiment**

Die Automobilpreisvorhersage ist hier das Beispiel für Regression. Der Preis für ein Auto soll auf Basis seiner Features einschließlich Marke, Treibstofftyp, Karosserietyp, Antriebsart usw. vorhergesagt werden. Das Experiment ist in Abbildung 11 dargestellt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/11.png)

Abbildung 11: Experiment zum Regressionsproblem Automobilpreis

Eine Visualisierung des Moduls [Score Model][score-model] liefert das in Abbildung 12 gezeigte Ergebnis.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/12.png)

Abbildung 12: Visualisieren des Bewertungsergebnisses für das Problem der Automobilpreisvorhersage

**Ergebnisinterpretation**

In diesem Bewertungsergebnis ist "Scored Labels" die Ergebnisspalte. Die Zahlen sind der vorhergesagte Preis für jedes Auto.

**Webdienstveröffentlichung**

Sie können das Regressionsexperiment wie im Fall der Klassifizierung mit zwei Klassen in einem Webdienst veröffentlichen und für die Vorhersage von Autopreisen verwenden.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13.png)

Abbildung 13: Bewertungsexperiment zum Regressionsproblem Automobilpreis

Wenn der Webdienst ausgeführt wird, wird das in Abbildung 14 gezeigte Ergebnis zurückgegeben. Der vorhergesagte Preis für dieses Auto ist 15.085,52.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/14.png)

Abbildung 14: Webdienstergebnis zum Regressionsproblem Automobilpreis

##Clustering

**Beispielexperiment**

Verwenden Sie das Iris-Dataset erneut, um ein Clusteringexperiment zu erstellen. Hier filtern Sie die Klassenbezeichner aus dem Dataset heraus, sodass es nur Features enthält und für das Clustering verwendet werden kann. Legen Sie in diesem Iris-Anwendungsfall die Anzahl der Cluster während des Trainingsprozesses auf 2 fest, d. h. die Blumen sollen in zwei Klassen zusammengefasst werden. Das Experiment ist in Abbildung 15 dargestellt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/15.png)

Abbildung 15: Experiment zum Iris-Clusteringproblem

Das Clustering unterscheidet sich von der Klassifizierung darin, dass das Trainingsdataset nicht selbst über Ground-Truth-Bezeichner verfügt. Vielmehr sollen die Trainingsdatasetinstanzen in unterschiedliche Cluster gruppiert werden. Während des Trainingsprozesses versieht das Modell die Einträge mit Bezeichnern, indem es die Unterschiede zwischen deren Features lernt. Danach kann das trainierte Modell weiter zum Klassifizieren von zukünftigen Einträge verwendet werden. Zwei Teile des Ergebnisses sind in einem Clusteringproblem relevant. Der erste Teil betrifft die Frage, wie das Trainingsdataset bezeichnet werden soll, und der zweite Teil, wie ein neues Dataset mit dem trainierten Modell klassifiziert werden kann.

Der erste Teil des Ergebnisses kann durch Klicken auf den linken Ausgabeport des Moduls [Train Clustering Model][train-clustering-model] und anschließendes Klicken auf **Visualize** visualisiert werden. Das Visualisierungsfenster wird in Abbildung 16 dargestellt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/16.png)

Abbildung 16: Visualisieren des Clusteringergebnisses für das Trainingsdataset

Das Ergebnis des zweiten Teils, das Clustering neuer Einträge mit dem trainierten Clusteringmodell, ist in Abbildung 17 dargestellt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/17.png)

Abbildung 17: Visualisieren des Clusteringergebnisses für ein neues Dataset

**Ergebnisinterpretation**

Obwohl die Ergebnisse der beiden Teile aus verschiedenen Experimentphasen stammen, sehen sie genau gleich aus und werden auf dieselbe Weise interpretiert. Die ersten vier Spalten sind Features. Die letzte Spalte "Assignments" ist das Vorhersageergebnis. Die Einträge, denen die gleiche Zahl zugewiesen wurde, werden als im selben Cluster befindlich vorhergesagt, d. h. beide weisen in bestimmter Form Ähnlichkeiten auf (in diesem Experiment wurde die standardmäßige euklidische Abstandsmetrik verwendet). Denken Sie daran, dass Sie die Anzahl von Clustern mit 2 angegeben haben. In der Spalte "Assignments" sind also die Einträge entweder mit 0 oder 1 bezeichnet.

**Webdienstveröffentlichung**

Sie können das Clusteringexperiment wie im Fall der Klassifizierung mit zwei Klassen in einem Webdienst veröffentlichen und für Clusteringvorhersagen verwenden.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18.png)

Abbildung 18: Bewertungsexperiment zum Iris-Clusteringproblem

Abbildung 19 zeigt das nach dem Ausführen des Webdiensts zurückgegebene Ergebnis. Für diese Blume wird der Cluster 0 vorhergesagt.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19.png)

Abbildung 19: Webdienstergebnis der Klassifizierung der Schwertlilien mit zwei Klassen

##Empfehlungssystem
**Beispielexperiment**

Für Empfehlungssysteme verwenden Sie das Restaurantempfehlungsproblem als Beispiel: Kunden werden auf Grundlage eines Bewertungsverlaufs Restaurants empfohlen. Die Eingabedaten besteht aus drei Teilen:

* Restaurantbewertungen von Kunden 
* Kundenfeaturedaten 
* Restaurantfeaturedaten 

Mit dem integrierten Modul [Train Matchbox Recommender][train-matchbox-recommender] von Azure Machine Learning können Sie verschiedene Aufgaben durchführen:

- Vorhersagen von Bewertungen für einen bestimmten Benutzer und ein bestimmtes Element
- Empfehlen von Elementen für einen bestimmten Benutzer
- Suchen von Benutzern, die im Zusammenhang mit einem bestimmten Benutzer stehen
- Suchen von Elementen, die im Zusammenhang mit einem bestimmten Element stehen

Im Menü **Recommender prediction kind** auf der rechten Seite können Sie zwischen vier Optionen wählen. Hier werden alle vier Szenarien erläutert. Ein typisches Experiment für Empfehlungssysteme in Azure Machine Learning sieht wie in Abbildung 20 aus. Ausführliche Informationen zur Verwendung dieser Empfehlungssystemmodule finden Sie in der Hilfe zu [Train Matchbox Recommender][train-matchbox-recommender] und [Score Matchbox Recommender][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/20.png)

Abbildung 20: Empfehlungssystemexperiment

**Ergebnisinterpretation**

*Vorhersagen von Bewertungen für einen bestimmten Benutzer und ein bestimmtes Element*

Durch Auswahl von "Rating Prediction" im Menü **Recommender prediction kind** wird das Empfehlungssystem aufgefordert, die Bewertung für einen bestimmten Benutzer und ein bestimmtes Element vorauszusagen. Abbildung 21 zeigt die Visualisierung der Ausgabe von [Score Matchbox Recommender][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/21.png)

Abbildung 21: Visualisieren des Bewertungsergebnisses des Empfehlungssystems – "Rating Prediction"

Es gibt drei Spalten. Die ersten beiden Spalten sind die Benutzer-Element-Paare, die durch die eingegebenen Daten bereitgestellt werden. Die dritte Spalte ist die vorhergesagte Bewertung eines Benutzers für ein bestimmtes Element. In der ersten Zeile wird zum Beispiel vorhergesagt, dass Kunde U1048 das Restaurant 135026 mit 2 bewertet.

*Empfehlen von Elementen für einen bestimmten Benutzer*

Bei Auswahl von **Item Recommendation** im Menü **Recommender prediction kind** empfiehlt das System Elemente für einen bestimmten Benutzer. Es gibt einen weiteren Parameter, den Sie in diesem Szenario auswählen müssen: die Auswahl des empfohlenen Elements. Die Option **From Rated Items (for model evaluation)** wird in erster Linie für die Auswertung von Modellen während des Trainings verwendet. Wählen Sie für diese Vorhersagephase **From All Items**. Abbildung 22 zeigt die Visualisierung der Ausgabe von [Score Matchbox Recommender][score-matchbox-recommender].
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/22.png)

Abbildung 22: Visualisieren des Bewertungsergebnisses des Empfehlungssystems – Elementempfehlung

Es gibt sechs Spalten. Die erste Spalte stellt die angegebenen Benutzer-IDs dar, für die Elemente empfohlen werden sollen, die durch die Eingabedaten bereitgestellt werden. Die übrigen fünf Spalten stellen die dem Benutzer empfohlenen Elemente dar, in absteigender Reihenfolge nach ihrer Relevanz. In der ersten Zeile ist das am häufigsten empfohlene Restaurant für den Kunden U1048 beispielsweise 134986, gefolgt von 135018, 134975, 135021 und 132862.

*Suchen von Benutzern, die im Zusammenhang mit einem bestimmten Benutzer stehen*

Bei Auswahl von "Related Users" im Menü "Recommender prediction kind" sucht das Empfehlungssystem zu einem bestimmten Benutzer in Beziehung stehende Benutzer. Diese verwandten Benutzer sind Benutzer mit ähnlichen Präferenzen. Es gibt einen weiteren Parameter, den Sie in diesem Szenario auswählen müssen: die Auswahl verwandter Benutzer. Die Option "From Users That Rated Items (for model evaluation)" wird in erster Linie für die Auswertung von Modellen während des Trainings verwendet. Wählen Sie für diese Vorhersagephase "From All Users" aus. Die Visualisierung der Ausgabe von [Score Matchbox Recommender][score-matchbox-recommender] sieht wie in Abbildung 23 aus.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/23.png)

Abbildung 23: Visualisieren des Bewertungsergebnisses des Empfehlungssystems – Verwandte Benutzer

Es gibt sechs Spalten. Die erste Spalte enthält die angegebenen Benutzer-IDs, für die verwandte Benutzer gefunden werden sollen, die durch die Eingabedaten bereitgestellt werden. Die übrigen fünf Spalten stellen die vorhergesagten verwandten Benutzer des Benutzers dar, in absteigender Reihenfolge nach ihrer Relevanz. So ist beispielsweise in der ersten Zeile der wichtigste Kunde für den Kunden U1048 der Kunde U1051, gefolgt von U1066, U1044, U1017 und U1072.

**Suchen von Elementen, die im Zusammenhang mit einem bestimmten Element stehen**

Bei Auswahl von **Related Items** im Menü **Recommender prediction kind** sucht das Empfehlungssystem zugehörige Elemente zu einem bestimmten Element. Verwandte Elemente sind die Elemente, für die es am wahrscheinlichsten ist, dass sie dem gleichen Benutzer gefallen. Es gibt einen weiteren Parameter, den Sie in diesem Szenario auswählen müssen: die Auswahl des verwandten Elements. Die Option **From Rated Items (for model evaluation)** wird in erster Linie für die Auswertung von Modellen während des Trainings verwendet. Wählen Sie **From All Items** für diese Vorhersagephase aus. Die Visualisierung der Ausgabe von [Score Matchbox Recommender][score-matchbox-recommender] sieht wie in Abbildung 24 aus.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/24.png)

Abbildung 24: Visualisieren des Bewertungsergebnisses des Empfehlungssystems – Verwandte Elemente

Es gibt sechs Spalten. Die erste Spalte stellt die angegebenen Element-IDs dar, für die verwandte Elemente gesucht werden sollen, die durch die Eingabedaten bereitgestellt werden. Die übrigen fünf Spalten stellen die vorhergesagten verwandten Elemente des Elements dar, in absteigender Reihenfolge nach ihrer Relevanz. In der ersten Zeile ist das Element mit der höchsten Relevanz für das Element 135026 beispielsweise 135074, gefolgt von 135035, 132875, 135055 und 134992. Webdienstveröffentlichung

Der Prozess der Veröffentlichung dieser Experimente als Webdienste zum Vorhersagen ist für jedes der vier Szenarien ähnlich. Hier wird als Beispiel das zweite Szenario verwendet, das Empfehlen von Elementen für einen bestimmten Benutzer. Sie können das gleiche Verfahren für die anderen drei Möglichkeiten nutzen.

Speichern Sie das trainierte Empfehlungssystem als ein trainiertes Modell, und filtern Sie die Eingabedaten für eine einzelne Benutzer-ID-Spalte wie erforderlich. Dann können Sie das Experiment wie in Abbildung 25 einbinden und als Webdienst veröffentlichen.

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25.png)
 
Abbildung 25: Bewertungsexperiment zum Restaurantempfehlungsproblem

Wenn der Webdienst ausgeführt wird, wird das in Abbildung 14 gezeigte Ergebnis zurückgegeben. Die fünf empfohlenen Restaurants für Benutzer U1048 sind 134986, 135018, 134975, 135021 und 132862.
 
![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot\_of\_experiment](./media/machine-learning-interpret-model-results/26.png)

Abbildung 26: Webdienstergebnis für das Restaurantempfehlungsproblem


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/
 

<!---HONumber=AcomDC_1210_2015-->