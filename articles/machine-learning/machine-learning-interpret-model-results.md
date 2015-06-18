<properties 
	pageTitle="Gewusst wie: Interpretieren von Modellergebnissen in Azure Machine Learning | Azure" 
	description="Gewusst wie: Auswählen des optimalen Parametersatzes für einen Algorithmus unter Verwendung und mit Visualisierung von Ausgabedaten aus Ergebnismodellen" 
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
	ms.date="04/21/2015" 
	ms.author="bradsev" />


# Gewusst wie: Interpretieren von Modellergebnissen in Azure Machine Learning 
 
**Verstehen und Visualisieren von  'Score Model'-Ausgabedateien**
In diesem Thema wird die Visualisierung und das Interpretieren der Ergebnisse in Azure Machine Learning Studio erläutert. Nachdem Sie ein Modell trainiert und Vorhersagen basierend darauf erstellt (das Modell ausgewertet) haben, müssen Sie das erhaltene Vorhersageergebnis verstehen und interpretieren.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

Es gibt vier Hauptarten von Machine Learning-Modellen in Azure Machine Learning: 

* Klassifizierung
* Clustering
* Regression
* Empfehlungssysteme

Die Module, mit denen Vorhersagen auf Basis dieser Module erstellt werden, führen eine "Bewertung" mit einigen Testdaten durch. Es sind Folgende:

* [Modell bewerten][score-model] ist ein Modul für Klassifizierung und Regression 
* [Clustern zuweisen][assign-to-clusters] ist ein Modul für Clustering 
* [Matchbox-Empfehlungssysteme bewerten][score-matchbox-recommender] ist ein Modul für Empfehlungssysteme 
 
Dieses Dokument erläutert, wie Sie die Vorhersageergebnisse für jedes dieser Module interpretieren können. Einen Überblick über diese Arten von Modellen finden Sie unter [Auswählen eines Algorithmus in Azure Machine Learning](machine-learning-algorithm-parameters-optimize.md).

Dieses Thema behandelt die Interpretation der Vorhersage, aber nicht die Modellevaluierung. Weitere Informationen zum Evaluieren eines Modells finden Sie unter [Evaluieren der Modellleistung in Azure Machine Learning](machine-learning-evaluate-model-performance.md).

Wenn Sie gerade erst in Azure Machine Learning einsteigen und Hilfe zum Erstellen eines einfachen Experiments benötigen, finden Sie diese unter [Erstellen eines einfachen Experiments in Azure Machine Learning Studio](machine-learning-create-experiment.md) in Azure Machine Learning Studio. 

##Klassifizierung
Es gibt zwei Unterkategorien der Klassifizierungsprobleme: 

* Probleme mit nur zwei Klassen (zwei-Klassen oder binäre Klassifikation) 
* Probleme mit mehr als zwei Klassen (Klassifizierung mit mehreren Klassen) 

Azure Machine Learning verfügt über verschiedene Module für den Umgang mit jedem dieser Klassifizierungstypen. Doch die Art, ihre Vorhersageergebnisse zu interpretieren ist sehr ähnlich. Zuerst werden Klassifizierungsprobleme mit zwei und anschließend Probleme mit mehreren Klassen erläutert.

###Klassifizierung mit zwei Klassen
**Beispielexperiment**

Ein Beispiel für ein Klassifizierungsproblem mit zwei Klassen ist die Klassifizierung von Schwertlilien (Iris): bei dieser Aufgabe werden Schwertliliend auf Basis ihrer Merkmale klassifiziert. Beachten Sie, dass das in Azure Machine Learning bereitgestellte Iris-Dataset eine Teilmenge des beliebten [Iris-Dataset](http://en.wikipedia.org/wiki/Iris_flower_data_set) ist und nur zwei Blumenarten (Klasse 0 und 1) enthält. Es gibt vier Merkmale für jede Blume (Länge und Breite des Kelchblatts sowie Länge und Breite des Kronblatts).

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/1.png)

Abbildung 1: Experiment mit Schwertlilien für ein Klassifizierungsproblem mit zwei Klassen

Es wurde ein Experiment durchgeführt, um dieses Problem zu beheben, wie in Abbildung 1 dargestellt. Ein Modell mit zweiklassigem Boosted Decision Tree wurde trainiert und bewertet. Jetzt können Sie die Vorhersageergebnisse aus dem Modul [Modell bewerten][score-model] grafisch darstellen, indem Sie auf dem Ausgabeport des Moduls [Modell bewerten][score-model] und dann im angezeigten Menü auf **Visualisieren** klicken. Hierdurch werden die Bewertungsergebnisse angezeigt, wie in Abbildung 2 dargestellt.

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/1_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/2.png)

Abbildung 2: Visualisieren der Ergebnismodellergebnisse bei Zwei-Klassen-Klassifizierung

**Interpretation von Ergebnissen**

Es gibt sechs Spalten in der Ergebnistabelle. Die vier linken Spalten sind die vier Merkmale. Die zwei rechten Spalten Ausgewertete Bezeichnungen und Ausgewertete Wahrscheinlichkeiten beinhalten die Voerhersageergebnisse. Die Spalte Ausgewertete Wahrscheinlichkeiten zeigt die Wahrscheinlichkeit, dass eine Blume zur positiven Klasse (Klasse 1) gehört. Zum Beispiel bedeutet die erste Zahl 0,028571 in der Spalte also, dass eine Wahrscheinlichkeit von 0,028571 besteht, dass die erste Blume der Klasse 1 angehört. Die Spalte Ausgewertete Bezeichnungen zeigt die vorhergesagte Klasse für jede Blume. Dies basiert auf der Spalte Ausgewertete Wahrscheinlichkeiten. Wenn die ausgewertete Wahrscheinlichkeit einer Blume größer als 0,5 ist, wird sie als Klasse 1 vorhergesagt, andernfalls als Klasse 0. 

**Webdienst-Veröffentlichung**

Sobald die Vorhersageergebnisse verstanden und als solide eingestuft wurden, kann das Experiment als Webdienst veröffentlicht werden, damit Sie es in verschiedenen Anwendungen bereitstellen können und aufgerufen werden, um Klassenvorhersagen zu allen neuen Schwertlilien zu erhalten. Informationen dazu, wie Sie ein Trainingsexperiment in ein Bewertungsexperiment ändern und als Webdienst veröffentlichen können finden Sie unter [Veröffentlichen des Azure Machine Learning-Webdiensts](machine-learning-walkthrough-5-publish-web-service.md). Dieses Verfahren liefert Ihnen ein Bewertungsexperiment wie in Abbildung 3 dargestellt.

![Screenshot_Experiment](./media/machine-learning-interpret-model-results/3.png)

Abbildung 3: Bewertungsexperiment mit Schwertlilien für ein Klassifizierungsproblem mit zwei Klassen

Jetzt müssen Sie die Eingabe und Ausgabe für den Webdienst festlegen. Natürlich ist die Eingabe der korrekte Eingangs-Port von [Modell bewerten][score-model], also die Eingabe der Schwertlilienmerkmale. Die Wahl der Ausgabe hängt davon ab, ob Sie an der vorhergesagten Klasse (Ausgewertete Bezeichnung) und/oder der ausgewerteten Wahrscheinlichkeit interessiert sind. Hier wird davon ausgegangen, dass Sie an beidem interessiert sind. Um die gewünschten Ausgabespalten auszuwählen, müssen Sie das Modul [Projektspalten][project-columns] verwenden. Klicken Sie auf das Modul [Projektspalten][project-columns], klicken Sie im rechten Bereich auf **Spaltenauswahl starten**, und wählen Sie **Ausgewertete Bezeichnungen** und **Ausgewertete Wahrscheinlichkeiten** aus. Nach dem Festlegen des Ausgangs-Ports für das Modul [Projektspalten][project-columns] und erneutem Ausführen, können Sie das Bewertungsexperiment als Webdienst veröffentlichen, indem Sie unten auf **WEBDIENST VERÖFFENTLICHEN** klicken. Das letzte Experiment sieht aus wie in Abbildung 4.
 
![Screenshot_Experiment](./media/machine-learning-interpret-model-results/4.png)

Abbildung 4: Letztes Bewertungsexperiment mit Schwertlilien für ein Klassifizierungsproblem mit zwei Klassen

Nachdem der Webdienst ausgeführt wird und Sie einige Maerkmalwerte eine Testinstanz eingegeben haben, gibt das zurückgegebene Ergebnis zwei Zahlen zurück. Die erste Zahl ist die ausgewertete Bezeichnung, die zweite ist die ausgewertete Wahrscheinlichkeit. Diese Blume wird als Klasse 1 mit einer Wahrscheinlichkeit von 0,9655 vorhergesagt. 
 
![Screenshot_Experiment](./media/machine-learning-interpret-model-results/4_1.png)

![Screenshot_Experiment](./media/machine-learning-interpret-model-results/5.png)

Abbildung 5: Webdienstergebnis der Klassifizierung der Schwertlilien mit zwei Klassen

###Mehrklassenklassifizierung
**Beispielexperiment**

In dieses Experiment führen Sie eine Buchstabenerkennung als Beispiel der Mehrklassenklassifizierung aus. Der Klassifikator versucht, einem bestimmten Buchstaben (Klasse) vorherzusagen, und zwar anhand angegebener von Hand geschriebene Attributwerte, die aus handgeschriebenen Bildern extrahiert werden. In den Trainingsdaten es gibt sechzehn Merkmale, die aus handgeschriebenen Bildern extrahiert wurden. Die 26 Buchstaben bilden die 26 Klassen. Ein Experiment wurde zum Trainieren eines Klassifizierungsmodells mit mehreren Klasse für die Erkennung von Buchstaben und die Vorhersage der gleichen Merkmale wie in einem Testdataset erstellt, wie in Abbildung 6 dargestellt.

![Screenshot_Experiment](./media/machine-learning-interpret-model-results/5_1.png)
 
![Screenshot_Experiment](./media/machine-learning-interpret-model-results/6.png)

Abbildung 6: Experiment zur Buchstabenerkennung bei einem Klassifizierungsproblem mit mehreren Klassen

Visualisieren Sie die Ergebnisse aus dem Modul [Modell bewerten][score-model], indem Sie mit rechts/links auf den Ausgabeport des Moduls [Modell bewerten][score-model] klicken. Klicken Sie dann auf **Visualisieren**. Es sollte ein Fenster wie in Abbildung 7 angezeigt werden.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/7.png)

Abbildung 7: Visualisieren der Ergebnismodellergebnisse bei Multiklassenklassifizierung

**Interpretieren der Ergebnisse**

Die linken sechzehn Spalten stellen die Merkmalwerte des Testsatzes dar. Die Spalten mit den Namen Ausgewertete Wahrscheinlichkeiten für Klasse "XX" sind ebenso wie die Spalten Ausgewertete Wahrscheinlichkeiten im Fall mit zwei Klassen. Sie zeigen die Wahrscheinlichkeit, dass der entsprechende Eintrag in einer bestimmten Klasse liegt. Für den ersten Eintrag ist die Wahrscheinlichkeit z. B. 0,003571, dass es sich um ein "A" handelt, 0,000451, dass es ein "B" handelt usw. Die letzte Spalte Ausgewertete Bezeichnungen entspricht Ausgewertete Bezeichnungen im Fall mit zwei Klassen. Die Klasse mit der größten ausgewerteten Wahrscheinlichkeit wird als die vorhergesagte Klasse des entsprechenden Eintrags ausgewählt. Beispielsweise ist für den ersten Eintrag die ausgewertete Bezeichnung "F", da es mit größter Wahrscheinlichkeit ein "F" (0,916995) ist.

**Veröffentlichen des Webdienstes**

Statt [Projektspalten][project-columns] zu verwenden, um einige Spalten als Ausgabe des Webdiensts auszuwählen, rufen Sie diesmal die ausgewertete Bezeichnung für jeden Eintrag und die Wahrscheinlichkeit der ausgewertete Bezeichnung ab. Die grundlegende Logik ist, die größte Wahrscheinlichkeit zwischen allen ausgewerteten Wahrscheinlichkeiten zu suchen. Zu diesem Zweck müssen Sie das Modul [R-Skript ausführen][execute-r-script] verwenden. Der R-Code ist in Abbildung 8 dargestellt, und das Experiment ist wie in Abbildung 9.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/8.png)

Abbildung 8: R-Code für das Extrahieren von Ausgewertete Bezeichnungen und die zugehörigen Wahrscheinlichkeiten der Bezeichnungen
  
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/9.png)

Abbildung 9: Letztes Bewertungsexperiment zur Buchstabenerkennung bei einem Klassifizierungsproblem mit mehreren Klassen

Nach dem Veröffentlichen und Ausführen des Webdiensts geben Sie einige Merkmaleingabewerte ein. Das ausgegebene Ergebnis entspricht dann Abbildung 10. Dieser handgeschriebene Buchstabe mit den extrahierten sechzehn Merkmalen wird mit einer Wahrscheinlichkeit von 0,9715 als "T" vorhergesagt. 
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/9_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/10.png)

Abbildung 10: Webdienstergebnis der Klassifizierung der Schwertlilien mit zwei Klassen

##Regression

Regressionsprobleme unterscheiden sich von Klassifizierungsproblemen. Bei einem Klassifizierungsproblem versuchen Sie, diskrete Klassen vorherzusagen, zum Beispiel welcher Klasse eine Schwertlilie angehört. Aber bei einem Regressionsproblem wird versucht, eine kontinuierliche Variable, z. B. den Preis eines Autos, vorherzusagen, wie im folgenden Beispiel ersichtlich.

**Beispielexperiment**

Die Automobilpreisvorhersage wird hier für Regression als Beispiel verwendet. Es wird versucht, den Preis für ein Auto basierend auf seinen Merkmalen, einschließlich Marke, Treibstofftyp, Karosserietyp, Antrieb usw. vorherzusagen. Das Experiment ist in Abbildung 11 dargestellt.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/11.png)

Abbildung 11: Experiment zum Regressionsproblem Automobilpreis

Visualisieren des Moduls [Modell bewerten][score-model], das Ergebnis sieht wie in Abbildung 12 aus.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/12.png)

Abbildung 12: Visualisieren des Bewertungsergebnisses für das Problem der Automobilpreisvorhersage

**Interpretieren der Ergebnisse**

In diesem Bewertungsergebnis ist Ausgewertete Bezeichnungen die Ergebnisspalte. Die Zahlen sind der vorhergesagte Preis für jedes Auto.

**Veröffentlichen des Webdienstes**

Sie können das Regressionsexperiment in einen Webdienst veröffentlichen und für die Vorhersage von Autopreisen verwenden, auf die gleiche Weise wie im Fall der Klassifizierung mit zwei Klassen.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/13.png)

Abbildung 13: Bewertungsexperiment zum Regressionsproblem Automobilpreis

Wenn der Webdienst ausgeführt wird, sieht das zurückgegebene Ergebnis wie in Abbildung 14 aus. Der vorhergesagte Preis für dieses Fahrzeug ist 15.085,52. 
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/13_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/14.png)

Abbildung 14: Webdienstergebnis zum Regressionsproblem Automobilpreis

##Clustering

**Beispielexperiment**

Verwenden Sie das Iris-Dataset erneut, um ein Clusterexperiment zu erstellen. Hier filtern Sie die Klassenbezeichnungen aus dem Dataset heraus, sodass es nur Merkmale enthält und für das Clustering verwendet werden kann. Legen Sie in diesem Iris-Fall die Anzahl der Cluster während des Trainingsprozesses auf zwei fest, was bedeutet, dass die Blumen in zwei Klassen geclustert werden sollen. Das Experiment ist in Abbildung 15 dargestellt.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/15.png)

Abbildung 15: Experiment zum Iris-Clusteringproblem

Clustering unterscheidet sich von der Klassifizierung, da das Trainingsdataset nicht selbst über Ground-Truth-Bezeichnungen verfügt. Vielmehr sollen die Trainingsdatasetinstanzen in unterschiedliche Cluster gruppiert werden. Während des Trainingsprozesses versieht das Modell die Einträge mit Bezeichnungen, indem es die Unterschiede zwischen deren Merkmalen lernt. Danach kann das trainierte Modell weiter zum Klassifizieren von zukünftigen Einträge verwendet werden. Es gibt zwei Teile des Ergebnisses, die in einem Clusteringproblem relevant sind. Der erste Teil ist, wie das Trainingsdataset bezeichnet werden soll,und der zweite Teil, wie ein neues Dataset mit dem trainierten Modell klassifiziert werden kann.

Der erste Teil des Ergebnisses kann durch Klicken auf den linken Ausgabeport des Moduls [Clusteringmodell trainieren][train-clustering-model] und durch Klicken auf **Visualisieren** danach visualisiert werden. Das Fenster "Visualisierung" wird in Abbildung 16 dargestellt.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/16.png)

Abbildung 16: Visualisieren des Clusteringergebnisses für das Trainingsdataset

Das Ergebnis des zweiten Teils, die Gruppierung neuer Einträge mit dem trainierten Clusteringmodell, ist in Abbildung 17 dargestellt.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/17.png)

Abbildung 17: Visualisieren des Clusteringergebnisses für ein neues Dataset

**Interpretieren der Ergebnisse**

Obwohl die Ergebnisse der beiden Teile aus verschiedenen Experimentphasen stammen, sehen sie genau gleich aus und werden auf dieselbe Weise interpretiert. Die ersten vier Spalten sind Merkmale. Die letzte Spalte Zuordnungen ist das Vorhersageergebnis. Die Einträge, denen die gleiche Zahl zugewiesen wurde, werden im selben Cluster vorhergesagt, d. h. beide weisen Ähnlichkeiten in irgendeiner Form auf. (In diesem Experiment wurde die standardmäßige euklidische Abstandsmetrik verwendet.) Denken Sie daran, dass Sie die Anzahl der Cluster mit 2 angegeben haben. In der Spalte Zuordnungen sind also die Einträge entweder mit 0 oder 1 bezeichnet.

**Veröffentlichen des Webdienstes**

Sie können das Clusteringsexperiment in einen Webdienst veröffentlichen und für Clusteringvorhersagen verwenden, auf die gleiche Weise wie im Fall der Klassifizierung mit zwei Klassen.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/18.png)

Abbildung 18: Bewertungsexperiment zum Iris-Clusteringproblem

Nach dem Ausführen des Webdiensts sieht das zurückgegebene Ergebnis wie in Abbildung 19 aus. Für diese Blume wird der Cluster 0 vorhergesagt. 
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/18_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/19.png)

Abbildung 19: Webdienstergebnis der Klassifizierung der Schwertlilien mit zwei Klassen

##Empfehlungssystem
**Beispielexperiment**

Für Empfehlungssysteme verwenden Sie das Restaurantempfehlungsproblem als Beispiel: Kunden auf Grundlage ihrer Bewertungsgeschichte Restaurants empfehlen. Die Eingabedaten besteht aus drei Teilen: 

* Restaurantbewertungen von Kunden 
* Kundenmerkmaldaten 
* Restaurantmerkmaldaten 

Einiges können Sie mit dem integrierten Modul [Matchbox-Empfehlungssysteme trainieren][train-matchbox-recommender] von Azure Machine Learning durchführen: 

- Vorhersagen von Bewertungen für einen bestimmten Benutzer und ein gegebenes Element
- Empfehlen von Elementen für einen bestimmten Benutzer
- Benutzer suchen, die im Zusammenhang mit einem bestimmten Benutzer stehen
- Suchen von Elementen, die im Zusammenhang mit einem bestimmten Element stehen

Sie können zwischen vier Optionen im Menü **Empfehlungsvorhersageart** auf der rechten Seite wählen. Hier werden alle vier Szenarien erläutert. Ein typisches Experiment für Empfehlungssysteme in Azure Machine Learning sieht wie in Abbildung 20 aus. Ausführliche Informationen zur Verwendung dieser Empfehlungssystemmodule finden Sie in der Hilfe für [Matchbox-Empfehlungssysteme trainieren][train-matchbox-recommender] und [Matchbox-Empfehlungssysteme bewerten][score-matchbox-recommender].
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/19_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/20.png)

Abbildung 20: Empfehlungssystemexperiment

**Interpretieren der Ergebnisse**

*Vorhersagen von Bewertungen für einen bestimmten Benutzer und ein gegebenes Element*

Durch Auswahl von Bewertungsvorhersage im Menü **Empfehlungsvorhersageart** sagt das Empfehlungssystem die Bewertung für einen bestimmten Benutzer und ein bestimmtes Element voraus. Die Visualisierung der Ausgabe von [Matchbox-Empfehlungssysteme bewerten][score-matchbox-recommender] sieht wie in Abbildung 21 aus.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/21.png)

Abbildung 21: Visualisieren des Bewertungsergebnisses des Empfehlungssystems - Bewertungsvorhersage

Es gibt drei Spalten. Die ersten beiden Spalten sind die Benutzer-Element-Paare, die durch die eingegebenen Daten gegeben sind. Die dritte Spalte ist die vorhergesagte Bewertung eines Benutzers für ein bestimmtes Element. In der ersten Zeile wird zum Beispiel vorhergesagt, dass Kunde U1048 das Restaurant 135026 mit 2 bewertet.

*Empfehlen von Elementen für einen bestimmten Benutzer*

Durch Auswahl von **Elementempfehlung** im Menü **Empfehlungsvorhersageart** empfiehlt das System Elemente für einen bestimmten Benutzer. Es gibt einen weiteren Parameter, den Sie in diesem Szenario auswählen müssen: Empfohlene Elementauswahl. Die Option **Aus bewerteten Elementen (für die Modellevaluierung)** wird in erster Linie für die Evaluierung von Modellen während des Trainings verwendet. Für diese Vorhersagephase wählen wir **Aus allen Elementen**. Die Visualisierung der Ausgabe von [Matchbox-Empfehlungssysteme bewerten][score-matchbox-recommender] sieht wie in Abbildung 22 aus.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/22.png)

Abbildung 22: Visualisieren des Bewertungsergebnisses des Empfehlungssystems - Elementempfehlung

Es gibt sechs Spalten. Die erste Spalte stellt die angegebenen Benutzer-IDs dar, für die Elemente empfohlen werden sollen, durch die Eingabedaten gegeben. Die übrigen fünf Spalten stellen die Elemente dar, die dem Benutzer in absteigender Reihenfolge in Bezug auf die Relevanz empfohlen werden. In der ersten Zeile ist das am häufigsten empfohlene Restaurant für den Kunden U1048 beispielsweise 134986, gefolgt von 135018, 134975, 135021 und 132862. 

*Benutzer suchen, die im Zusammenhang mit einem bestimmten Benutzer stehen*

Durch Auswahl von Verwandte Benutzer im Menü "Empfehlungsvorhersageart" sucht das Empfehlungssystem zugehörige Benutzer zu einem gegebenen Benutzer. Verwandte Benutzer sind Benutzer mit ähnlichen Einstellungen. Es gibt einen weiteren Parameter, den Sie in diesem Szenario auswählen müssen: die Auswahl des verwandten Benutzers. Die Option "Aus Benutzern, die Elemente bewertet haben (für die Modellevaluierung)" wird in erster Linie für die Evaluierung von Modellen während des Trainings verwendet. Wählen Sie "Aus allen Benutzern" für diese Vorhersagephase aus. Die Visualisierung der Ausgabe von [Matchbox-Empfehlungssysteme bewerten][score-matchbox-recommender] sieht wie in Abbildung 23 aus.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/23.png)

Abbildung 23: Visualisieren des Bewertungsergebnisses des Empfehlungssystems - Verwandte Benutzer

Es gibt sechs Spalten. Die erste Spalte enthält die angegebenen Benutzer-IDs für die verwandte Benutzer gefunden werden sollen, durch die Eingabedaten gegeben. Die übrigen fünf Spalten stellen die vorhergesagten verwandten Benutzer des Benutzers dar, in absteigender Reihenfolge in Bezug auf die Relevanz. So ist beispielsweise in der ersten Zeile der wichtigste Kunde für den Kunden U1048 der Kunde U1051, gefolgt von U1066, U1044, U1017 und U1072. 

**Suchen von Elementen, die im Zusammenhang mit einem bestimmten Element stehen**

Durch Auswahl von **Verwandte Elemente** im Menü **Empfehlungsvorhersageart** sucht das Empfehlungssystem zugehörige Elemente zu einem gegebenen Element. Verwandte Elemente sind die Elemente, die am wahrscheinlichsten dem gleichen Benutzer gefallen. Es gibt einen weiteren Parameter, den Sie in diesem Szenario auswählen müssen: die Auswahl des verwandten Elements. Die Option **Aus bewerteten Elementen (für die Modellevaluierung)** wird in erster Linie für die Evaluierung von Modellen während des Trainings verwendet. Wählen Sie **Aus allen Elementen** für diese Vorhersagephase aus. Die Visualisierung der Ausgabe von [Matchbox-Empfehlungssysteme bewerten][score-matchbox-recommender] sieht wie in Abbildung 24 aus.
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/24.png)

Abbildung 24: Visualisieren des Bewertungsergebnisses des Empfehlungssystems - Verwandte Elemente

Es gibt sechs Spalten. Die erste Spalte stellt die angegebenen Element-IDs dar, für die verwandte Elemente gesucht werden sollen, durch die Eingabedaten gegeben. Die übrigen fünf Spalten stellen die vorhergesagten verwandten Elemente des Elements dar, in absteigender Reihenfolge in Bezug auf die Relevanz. In der ersten Zeile ist das am relevanteste Element für das Element 135026 beispielsweise 135074, gefolgt von 135035, 132875, 135055 und 134992. 
Veröffentlichen des Webdienstes

Der Prozess der Veröffentlichung dieser Experimente als Webdienste für Vorhersagen ist für jedes der vier Szenarien ähnlich. Hier wird als Beispiel das zweite Szenario verwendet, Empfehlen von Elementen für einen bestimmten Benutzer. Sie können das gleiche Verfahren für die anderen drei Möglichkeiten anwenden.

Speichern Sie das trainierte Empfehlungssystem als ein trainiertes Modell, und filtern Sie die Eingabedaten für eine einzelne Benutzer-ID-Spalte, wie erforderlich. Dann können Sie das Experiment wie in Abbildung 25 einbinden und als Webdienst veröffentlichen.

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/25.png)
 
Abbildung 25:  Bewertungsexperiment zum Restaurantempfehlungsproblem 

Wenn der Webdienst ausgeführt wird, sieht das zurückgegebene Ergebnis wie in Abbildung 14 aus. Die fünf empfohlenen Restaurants für Benutzer U1048 sind 134986, 135018, 134975, 135021 und 132862. 
 
![screenshot_of_experiment](./media/machine-learning-interpret-model-results/25_1.png)

![screenshot_of_experiment](./media/machine-learning-interpret-model-results/26.png)

Abbildung 26: Webdienstergebnis für das Restaurantempfehlungsproblem


<!-- Module References -->
[assign-to-clusters]: https://msdn.microsoft.com/library/azure/eed3ee76-e8aa-46e6-907c-9ca767f5c114/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-matchbox-recommender]: https://msdn.microsoft.com/library/azure/55544522-9a10-44bd-884f-9a91a9cec2cd/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[train-matchbox-recommender]: https://msdn.microsoft.com/library/azure/fa4aa69d-2f1c-4ba4-ad5f-90ea3a515b4c/

<!--HONumber=54--> 