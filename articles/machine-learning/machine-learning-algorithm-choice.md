<properties 
	pageTitle="Auswählen von Algorithmen für das maschinelle Lernen | Microsoft Azure" 
	description="Auswahl eines Azure Machine Learning-Algorithmus für überwachtes und unüberwachtes Lernen bei Clustering-, Klassifizierungs- oder Regressionsexperimenten." 
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
	ms.date="06/01/2015" 
	ms.author="bradsev;garye" />


# Auswählen von Azure Machine Learning-Algorithmen für Clustering, Klassifizierung oder Regression

Dieses Thema erläutert einige grundlegende Aspekte zum Ansatz des maschinellen Lernens. Insbesondere erfahren Sie, wie Sie geeignete maschinelle Lernalgorithmen zur Analyse von gegebenen Datentypen auswählen, wie aufkommende Fragen beantwortet werden, wie Sie bestimmte Aufgaben erledigen und wie Sie Kriterien für die Entscheidungsfindung bereitstellen.

> [AZURE.TIP]Das [Microsoft Azure Machine Learning – Cheat Sheet für Algorithmen](machine-learning-algorithm-cheat-sheet.md) ist eine praktische Referenz zu diesem Artikel.

Bei der Verwendung von Machine Learning für Analysen stellen sich i. d. R. zwei Fragen:

* Mit welcher Art von Analyse erreichen wir unsere Ziele mit den verfügbaren Daten? 
* Was ist der am besten geeignete Algorithmus oder das ideale Modell für diese Analyse?

Es werden drei Arten der Analyse für maschinelles Lernen und deren Einsatzgebiete behandelt:

* **Clustering**
* **Klassifizierung** 
* **Regression** 

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]


<a name="anchor-1"></a>
## Maschinelle Lernalgorithmen lernen aus Daten

Maschinelles Lernen ist eine Disziplin, in der eine Klasse von Algorithmen untersucht werden, die dazu entwickelt wurden, aus Daten zu lernen und nicht ein bestimmtes, vordefiniertes Modell auf die Daten anzuwenden. Die Idee besteht darin, Wissen durch das Untersuchen von Mustern in einem DataSet zu gewinnen, anstatt wie gewöhnlich mit der *hypothetisch-deduktiven* Methode, bei der Sie zuerst das richtige Modell für das gesamte DataSet erraten müssen, um dann empirische Tests durchzuführen.

Maschinelles Lernen – auch als Lernen aus Daten bezeichnet – gibt es in zwei Formen: *beaufsichtigtes* und *unbeaufsichtigtes* Lernen.

<a name="anchor-2"></a>
## Beaufsichtigtes Lernen  

Für das beaufsichtigte Lernen ist es erforderlich, dass die Zielvariable definiert ist und eine ausreichende Anzahl von Werten angegeben wird.

Das beaufsichtigte Lernen ist die Art des maschinellen Lernens, die angewendet wird, wenn die korrekten Ausgabeergebnisse (oder Zielvariablen) für die einzugebenden Lerninstanzen bekannt sind. Ziel des Trainings eines Algorithmus für das maschinelle Lernen ist die Ermittlung eines Modells (also einer Regel oder Funktion), das Eingaben bekannten Ausgabewerten zuordnet. Dies ist vergleichbar mit einem Supervisor, der dem Algorithmus-Agent sagen kann, ob die Eingaben richtig den Ausgaben zugeordnet wurden. Sobald der Lernvorgang abgeschlossen ist und es ein funktionierendes Modell gibt, kann es auf neue Eingabedaten angewendet werden, um Vorhersagen über die erwartete Ausgabe zu treffen, wenn anders als beim Trainings-DataSet der Zielwert nicht im Voraus bekannt ist.

Der Modelltyp wird also durch die Art der Zielvariablen bestimmt.

![Diagramm für überwachtes Lernen: Modellierung aus bezeichneten Daten und der Verwendung zur Vorhersage von Ergebnissen aus neuen Daten](./media/machine-learning-algorithm-choice/supervised-learning-using-known-data-to-model-solution.png)

Es gibt zwei allgemeine Kategorien der Analyse beim beaufsichtigten Lernen: *Klassifizierung* und *Regression*. Beaufsichtigtes Lernen ist besonders bei Klassifizierungsproblemen häufig anzutreffen, da das Ziel häufig ist, dass der Computer ein von uns erstelltes Klassifizierungssystem erlernt. Die Antworten sind i. d. R. nur einige bekannte Werte (Bezeichner) wie "Wahr", "Falsch" oder "Hoch", "Mittel", "Niedrig". Klassifizierungsalgorithmen gelten für nominale Werte, nicht ordinale Antwortwerte. Die Ziffernerkennung ist ein typisches Beispiel für das Klassifizierungs-Lernen. Allgemeiner gilt, dass sich das Klassifizierungslernen besonders für Probleme eignet, bei denen es sowohl nützlich als auch leicht ist, die Klassifizierung zu ermitteln.

Beim beaufsichtigten Lernen können die untersuchten Variablen in zwei Gruppen aufgeteilt werden: erklärende Variablen (auch Indikatoren genannt) und abhängige Variablen (oder Antwortvariablen). Das Ziel der Analyse ist die Definition einer Beziehung zwischen den erklärenden Variablen und den abhängigen Variablen, wie in der *Regressionsanalyse* gezeigt. Die Werte der abhängigen Variablen müssen einem ausreichend großen Teil des DataSets bekannt sein. Bei der Regression übernehmen die Antworten oder Ausgabevariablen kontinuierliche Werte, wie z. B. Kilometer pro Liter für ein bestimmtes Auto, das Alter einer Person usw.

Das beaufsichtigte Lernen ist auch die gängigste Methode zum Trainieren neuronaler Netzwerke und Entscheidungsstrukturen.

> Beide Verfahren sind stark abhängig von den Angaben der vordefinierten Klassifizierungen. Bei neuronalen Netzwerken wird die Klassifizierung zum Ermitteln des Netzwerkfehlers und dann zum Anpassen des Netzwerks verwendet, um den Fehler zu minimieren. In Entscheidungsstrukturen wird mit den Klassifizierungen ermittelt, welche Attribute die meisten Informationen bereitstellen, die zum Lösen des Klassifizierungsrätsels verwendet werden können. Beide Beispiele hängen von einer "Überwachung" in Form von vordefinierten Klassifizierungen ab.

>  Spracherkennung mit versteckten Markov-Modellen und Bayes-Netzwerken basiert auf einigen Überwachungselementen, die auch zum Anpassen der Parameter zum Verringern der Fehler in gegebenen Eingaben dienen. [[Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizont](http://www.aihorizon.com/), in englischer Sprache]


<a name="anchor-3"></a>
##Unbeaufsichtigtes Lernen

Das Problem beim maschinellen Lernen mit dem unbeaufsichtigten Verfahren besteht in der Ermittlung von Mustern oder versteckten Strukturen in nicht bezeichneten Daten. Das Modell wird nicht mit den "richtigen Ergebnissen" für ein DataSet bereitgestellt, mit denen das Lernen durchgeführt werden kann. Da dem Lerner nicht bezeichnete Beispiele gegeben werden, erfolgt kein Feedback, um potenzielle Lösungen zu finden – weder über Fehler noch über Erfolge. Das Ziel ist, dass der Computer lernt, wie etwas funktioniert, auch wenn wir keine genauen Informationen darüber mitteilen, wie das geht. Alle Variablen werden beim unbeaufsichtigten Lernen auf die gleiche Weise behandelt. Es gibt keinen Unterschied zwischen erklärenden und abhängigen Variablen. Es gibt jedoch immer noch einige allgemeine Ziele, wie z. B. eine Reduzierung der Datenmenge, und spezifische Ziele, wie das Suchen von Clustern, zu erreichen.

In Azure Machine Learning können wir sowohl das unbeaufsichtigte als auch das beaufsichtigte Lernen über **Clustering**, **Klassifizierung** und **Regression** durchführen.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##Clustering
Clustering ist ein Beispiel für unbeaufsichtigtes Lernen. Bei diesem Lernen ist das Ziel die Suche nach Ähnlichkeiten in den Trainingsdaten und das Aufteilen des DataSets in Teilmengen, die von diesen Ähnlichkeiten abgegrenzt werden können. Die Annahme, dass die wichtigsten Cluster, die bei dieser datengesteuerten Vorgehensweise ermittelt werden, mit unserer intuitiven Klassifizierung übereinstimmen, wird häufig, aber nicht immer erfüllt.

Obwohl der Clustering-Algorithmus diesen Clustern keine geeigneten Namen zuweist, kann er sie erstellen und dann zum Ermitteln von Ähnlichkeiten verwenden, die in neuen Beispielen zu erwarten sind, indem er sie in die wahrscheinlichsten Cluster klassifiziert. Diese datengesteuerte Vorgehensweise kann bei ausreichenden Datenmengen sehr gut funktionieren. So werden z. B. Algorithmen zum Filtern sozialer Informationen wie der von Amazon.com verwendet, um Bücher auf der Grundlage ähnlicher Personengruppen zu empfehlen und neue Benutzer diesen Gruppen zum Zweck von Empfehlungen zuzuordnen.

Der Clustering-Algorithmus in Azure Machine Learning ist [K-Means][k-means-clustering].

![K-Means-Clustering-Algorithmus-Experiment: Screenshot](./media/machine-learning-algorithm-choice/k-means-clustering-algorithm-menu.png)

K-Means ist einer der einfachsten unbeaufsichtigten Algorithmen zum Lösen bekannter Clustering-Probleme. Der K-Means-Algorithmus fasst Daten in Clustern zusammen, indem er versucht, die Daten in N Gruppen mit gleicher Varianz aufzuteilen und somit das Kriterium der "Trägheit" oder "Summe der quadratischen Abweichungen von den Cluster-Schwerpunkten" zu minimieren. Dieser Algorithmus erfordert die Angabe der Anzahl von Clustern. K-Means kann sehr gut auf große Datenmengen skaliert werden und kommt daher bei einer Vielzahl von Anwendungsbereichen in vielen verschiedenen Bereichen zum Einsatz.

Das Algorithmusmodul [K-Means Clustering][k-means-clustering] gibt ein untrainiertes K-Means-Clustermodell zurück, das zu Lernzwecken an das Modul [Train Clustering Model][train-clustering-model] übergeben werden kann.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

Diese Abbildung zeigt, dass bei Verwendung von "K-Means Clustering" Optionen konfiguriert werden können. Die K-Means-Methode findet eine angegebene Anzahl von Clustern für einen Satz von D-dimensionalen Datenpunkten. Beginnend mit einem *ersten Satz von K-Schwerpunkten* verwendet die Methode den Lloyd-Algorithmus, um die Orte von Schwerpunkten iterativ zu verfeinern. Der Algorithmus wird beendet, wenn sich die Schwerpunkte stabilisieren oder eine *festgelegte Anzahl von Iterationen* abgeschlossen wurde. Das Modul initialisiert ein K-mal-D-Array mit den endgültigen Schwerpunkten, die die K-Cluster in den N Datenpunkten definieren. Der Algorithmus verwendet einen Vektor der Länge N, bei dem jeder Datenpunkt einem K-Cluster zugeordnet ist. Wenn eine bestimmte Anzahl von Clustern (K) für die Suche angegeben wurde, weist das Modul die ersten K Datenpunkte in der Reihenfolge den K-Clustern zu.


Dieses Modul akzeptiert oder generiert auch Anfangspunkte zum Definieren der anfänglichen Clusterkonfiguration. Die *Metrik* definiert die Methode, die zum Messen des Abstands zwischen einem Datenpunkt und dem Schwerpunkt verwendet wird. Jeder Datenpunkt wird dem Cluster zugeordnet, dessen Schwerpunkt dem Datenpunkt am nächsten liegt. Standardmäßig verwendet die Methode die *euklidische Metrik*. Sie können als eine alternative Metrik die *Kosinus-Metrik* für die Methode angeben. Beachten Sie, dass die K-Means-Methode eventuell nur eine lokale optimale Clusterkonfiguration für ein DataSet findet. Die Methode könnte mit einer anderen Anfangskonfiguration möglicherweise eine andere, eventuell bessere Konfiguration finden.

<a name="anchor-5"></a>
##Klassifizierung 
Bei der Klassifizierungsanalyse unterteilen wir die Daten in Klassen und verwenden dann einen trainierten Teil mit zuvor bezeichneten Daten. Das Verfahren dient zur Vorhersage der Gruppenmitgliedschaft für Dateninstanzen. In Azure Machine Learning stehen die folgenden Algorithmen für die Klassifizierung zur Verfügung.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Die *Zwei-Klassen-Algorithmen* werden für binäre Antwortvariablen verwendet (Ja oder Nein, 0 oder 1, wahr oder falsch usw.), während *Mehrklassen-Algorithmen* für alle nominalen Antwortvariablen verwendet werden, die Instanzen in mehr als zwei Klassen unterteilen.

### Richtlinien zum Auswählen eines Klassifikationsalgorithmus
Diese lange Liste von Algorithmen führt zu einer Reihe von Fragen:

* Wie können Sie wissen, welche dieser vielen Klassifizierungen am besten für ein bestimmtes DataSet verwendet wird? 
* Gibt es Fälle, in denen es eine "natürliche" Wahl der Klassifizierung gibt? 
* Was sind die Prinzipien für diese Wahl?

Eine empfohlene Vorgehensweise besteht darin, mehrere unterschiedliche Klassifizierungen sowie verschiedene Parametersätze innerhalb jedes Algorithmus zu testen und dann die beste Version mithilfe der Kreuzvalidierung auszuwählen.

> [AZURE.TIP] [Azure Machine Learning Studio](https://studio.azureml.net/) ermöglicht es Ihnen, mehrere Algorithmen für dieselben Daten zu testen und die Ergebnisse zu vergleichen. Es folgt ein Beispiel aus dem [Azure Machine Learning-Katalog](http://gallery.azureml.net/): [Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92) (Vergleichen von Mehrklassen-Klassifizierungen: Buchstabenerkennung, in englischer Sprache).

Hier sind einige allgemeine Richtlinien als Ansatz für die Überlegung. Berücksichtigen Sie die folgenden Probleme bei der Auswahl des zu verwendenden Algorithmus: [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Übersicht von Edwin Chen (in englischer Sprache)]

**Wie umfangreich sind Ihre Trainingsdaten?** 
Wenn der Trainingssatz klein ist und Sie eine beaufsichtigte Klassifizierung trainieren möchten, empfiehlt die Theorie zum maschinellen Lernen, dass Sie eine Klassifizierung mit hoher Erwartungstreue und geringer Varianz wie den naiven Bayes-Klassifikator verwenden sollten. Sie verfügen über einen Vorteil gegenüber Klassifizierungen mit geringer Erwartungstreue und hoher Varianz wie "kNN", da letztere zur Überanpassung neigt. Klassifizierungen mit geringer Erwartungstreue und hoher Varianz sind besser geeignet, wenn Sie einen größeren Trainingssatz festgelegt haben, da sie kleinere asymptotische Fehler aufweisen. In diesen Fällen ist eine Klassifizierung mit hoher Erwartungstreue nicht leistungsfähig genug, um ein genaues Modell zu bieten. Es gibt theoretische und empirische Ergebnisse, die darauf hinweisen, dass der Naive Bayes-Klassifikator auch in solchen Fällen geeignet ist. Beachten Sie jedoch, dass bessere Daten und gute Features in der Regel mehr Vorteile bieten als ein besserer Algorithmus. Darüber hinaus wird die Leistung der Klassifizierung bei sehr großen DataSets nicht so stark vom verwendeten Algorithmus beeinflusst, sodass Sie in diesem Fall den Algorithmus basierend auf Faktoren wie Skalierbarkeit, Geschwindigkeit und Benutzerfreundlichkeit auswählen sollten.

**Müssen Sie inkrementell oder stapelweise trainieren?** 
Bei großen Datenmengen oder häufigen Datenaktualisierungen, sollten Sie vermutlich Bayes-Algorithmen verwenden, die gut aktualisiert werden können. Neuronale Netze und SVM müssen die Trainingsdaten stapelweise verarbeiten.

**Sind Ihre Daten ausschließlich kategorisch oder ausschließlich numerisch oder eine Mischung aus beiden?** 
Bayes funktioniert am besten mit kategorischen/binomialen Daten. Entscheidungsstrukturen können numerische Werte nicht vorhersagen.

**Müssen Sie oder Ihre Benutzer verstehen, wie die Klassifizierung funktioniert?** Bayes oder Entscheidungsstrukturen sind leichter zu erläutern. Es ist viel schwieriger zu verstehen oder zu erklären, wie die Klassifizierung von Daten in neuronalen Netzwerken und SVMs erfolgt.

**Wie schnell muss die Klassifizierung generiert werden?** 
Entscheidungsstrukturen können langsam sein, wenn die Struktur sehr komplex ist. Demgegenüber sind SVMs schnell, wenn es um Klassifizierung geht, da sie nur bestimmen müssen, auf welche Seite der "Linie" Ihre Daten gehören.

**Wie hoch ist Komplexität des Problems oder wie viel Komplexität ist erforderlich?** Neuronale Netze und SVMs können komplexe nichtlineare Klassifizierung verarbeiten.

### Vor- und Nachteile der Klassifizierungsalgorithmen
Jeder dieser Klassifizierungsalgorithmen hat einige Vorteile und Nachteile:

<a name="anchor-5a"></a> **Vor- und Nachteile der logistischen Regression:** "Logistische Regressionsanalyse basiert auf der Berechnung der Wahrscheinlichkeit eines Ergebnisses anhand des Verhältnisses zwischen der Wahrscheinlichkeit des Auftretens des Ergebnisses geteilt durch die Wahrscheinlichkeit des Nichtauftretens." [[Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos et al. (International Journal of Pediatrics, 2009) Artikel-ID 952042, in englischer Sprache]
 
Das logistische Modell ist parametrisch und hat daher den Vorteil, dass es einen Einblick in die Auswirkungen der einzelnen Indikatorvariablen auf die Antwortvariable bietet.


Mit den verfügbaren natürlich probabilistischen Interpretationen (im Gegensatz zu Entscheidungsstrukturen oder SVMs) können wir das Modell ganz einfach zur Einbeziehung neuer Daten aktualisieren. Es gibt viele Möglichkeiten zur Regulierung des Modells, und im Gegensatz zum naiven Bayes-Klassifikator müssen Sie sich auch keine Gedanken über die Korrelation Ihrer Funktionen machen. Die logistische Regression ist nützlich, wenn Sie:

* ein probabilistisches Framework zur Anpassung von Klassifizierungsschwellenwerten benötigen
* schnell zusätzliche Trainingsdaten integrieren möchten  

Logistische Regression funktioniert bei Daten mit vielen Dimensionen besser als Entscheidungsstrukturen. Bei Textklassifizierungen haben Sie z. B. möglicherweise mehr als 100.000 Dokumente mit 500.000 einzelnen Wörtern (Funktionen). Eine einfache Regel wie das Lernen einer linearen Hyperebene ist besser, da Entscheidungsstrukturen viel zu viele Freiheiten bieten und anfällig für eine Überanpassung sind. Sie könnten über die Funktionsauswahl eine Entscheidungsstruktur auf Textdaten anwenden, dabei gehen jedoch viele wertvolle Informationen für die Textklassifizierung verloren, da eine erheblich reduzierte Teilmenge von Funktionen ausgewählt wird. Wenn Trainingsmodelle mit Daten mit vielen Dimensionen verwendet werden, nehmen Fehler durch Abweichungen schnell zu. In diesem Fall ist es besser, einfache Modelle mit höherer Erwartungstreue zu verwenden.

Ein Nachteil der logistischen Regression besteht darin, dass sie instabil ist, wenn ein Indikator fast die Antwortvariable erklären kann, da der Koeffizient dieser Variable sehr umfangreich wird.

<a name="anchor-5b"></a> **Vor- und Nachteile von Entscheidungsstrukturen:** [Entscheidungsstrukturen](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) sind einfach zu interpretieren und zu erläutern.

> [Entscheidungsstrukturen] können sehr gut mit Funktionsinteraktionen umgehen und sind nicht parametrisch, sodass Sie sich keine Sorgen um Ausreißer machen müssen oder darüber, ob die Daten linear trennbar sind. (Entscheidungsstrukturen können z. B. problemlos für Fälle verwendet werden, bei denen Sie die Klasse A am unteren Ende der Funktion x, Klasse B in der Mitte von Funktion x und dann erneut A im hohen Bereich haben.) Ein Nachteil von Entscheidungsstrukturen ist, dass sie kein Onlinetraining unterstützen, sodass Sie Ihre Struktur neu erstellen müssen, wenn neue Daten eintreffen. Ein weiterer Nachteil ist, dass sie zu Überanpassung neigen – in solchen Fällen können jedoch Zusammenfassungsmethoden wie Random Forests (oder Boosted Trees) helfen. Darüber hinaus sind Random Forests häufig für Probleme in der Klassifizierung besser geeignet (in der Regel etwas besser als SVMs, soweit ich weiß). Sie sind schnell und skalierbar, und Sie müssen sich keine Sorgen über die Anpassung vieler Parameter machen wie bei SVMs. [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen, in englischer Sprache]

Entscheidungsstrukturen generieren Ausgaben als Regeln zusammen mit Metriken wie *Support*, *Confidence* und *Lift*.


<a name="anchor-5c"></a> **Vor- und Nachteile von SVMs:** 
Support Vector Machines (SVMs) sind sehr effektiv in Räumen mit sehr vielen Dimensionen. Auch in Fällen, in denen die Anzahl der Dimensionen größer als die Anzahl der Proben ist, sind sie weiter effektiv. Ist die Anzahl der Funktionen jedoch wesentlich größer als die Anzahl der Proben, ist dieses Verfahren wahrscheinlich ineffizient. SVMs sind auch speichereffizient, da sie eine Teilmenge der Trainingspunkte in der Entscheidungsfunktion (sogenannte Supportvektoren) verwenden. Sie sind sehr vielseitig: Es können verschiedene allgemeine und spezifische Kernel-Funktionen für die Entscheidungsfunktion angegeben werden. Die Kernel-Funktionen dienen zum Umwandeln niedrigdimensionaler Trainingsproben in höhere Dimensionen. Dies ist besonders für Probleme der linearen Separierbarkeit hilfreich.

SVMs bieten jedoch keine direkten Wahrscheinlichkeitsschätzungen. Diese werden mit einer aufwendigen fünffachen Kreuzvalidierung berechnet.

>[Mit] hoher Genauigkeit, einer geringen Wahrscheinlichkeit für Überanpassung und einem geeigneten Kernel funktionieren sie auch dann gut, wenn die Daten im Basisfunktionsbereich nicht linear separierbar sind. [SVMs sind] insbesondere bei Textklassifizierungsproblemen beliebt, wo Räume mit sehr vielen Dimensionen die Norm sind. [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen, in englischer Sprache]

Im Gegensatz zu Gesamtstrukturen sind SVMs ursprünglich Zwei-Klassen-Klassifikatoren. Mittlerweile wurden sie aber auch für die Arbeit mit mehreren Klassen angepasst. Mit einem Training wie "One-vs-Rest" können Sie einen Multi-Klassen-Klassifikator erstellen, der aber möglicherweise nicht optimal arbeitet. Da SVMs nur Zwei-Klassen-Ausgaben behandeln können (d. h. eine kategorische Ausgabevariable mit der Varianz 2), lernt er mit N Klassen auch N SVMs (SVM 1 lernt "Ausgabe == 1" vs. "Ausgabe != 1", SVM 2 lernt "Ausgabe == 2" vs. "Ausgabe != 2", ... , SVM N lernt "Ausgabe == N" vs. "Ausgabe != N"). Um dann die Ausgabe für eine neue Eingabe vorherzusagen, führt er einfach eine Vorhersage mit jeder SVM durch und ermittelt so, welche die Vorhersage trifft, die am weitesten in den positiven Bereich reicht. [[Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore (Carnegie Mellon University 2001), in englischer Sprache]

<a name="anchor-5d"></a> **Vor- und Nachteile von naiven Bayes-Klassifikatoren:** [Naive Bayes (NB)](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf)-Klassifikatoren werden häufig für Klassifizierungsprobleme verwendet. Sie gehen von unabhängigen Funktionen aus, weshalb dieses Verfahren als "naiv" bezeichnet wird.

> Wenn die Annahme des NB-Klassifikators zur bedingten Unabhängigkeit der Überprüfung standhält, konvergiert der naive Bayes-Klassifikator schneller als diskriminative Modelle wie die logistische Regression, sodass weniger Training erforderlich ist. Auch wenn die NB-Annahme nicht standhält, ist ein NB-Klassifikator in der Praxis häufig hervorragend geeignet. ... Der Hauptnachteil ist, dass kein Lernen zu den Interaktionen zwischen den Funktionen stattfindet (NB wird z. B. nicht lernen, dass Sie, obwohl Sie Filme mit Brad Pitt und Tom Cruise lieben, Filme hassen, in denen sie zusammen spielen). [[Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen, in englischer Sprache]


<a name="anchor-5e"></a> **One-vs-All:** One-vs-All ist eine Strategie zur Reduzierung des Problems der Multi-Klassen-Klassifizierung bei einem Satz mit mehreren binären Klassifizierungsproblemen. Die Strategie umfasst eine einzelne Klassifizierung pro Klasse, wobei Proben dieser Klasse die Positivproben und alle anderen Proben die Negativproben sind. Für diese Strategie müssen die Basisklassifikatoren für ihre Entscheidung einen Echtwert-Confidence-Wert und nicht einfach einen Klassenbezeichner verwenden. Diskrete Klassenbezeichner allein können zu Mehrdeutigkeiten führen, bei denen mehrere Klassen für eine einzelne Probe vorhergesagt werden. [[Multiclass classification](http://en.wikipedia.org/wiki/Multiclass_classification) (Wikipedia 2006, in englischer Sprache)]


<a name="anchor-6"></a>
##Regression
 
Bei einer Regressionsanalyse erfolgen die Vorhersagen anhand der früheren Inferenz. Die neuen Werte für eine abhängige Variable werden basierend auf dem Wert eines oder mehrerer gemessener Attribute berechnet. Folgende Regressionsalgorithmen sind in Azure Machine Learning verfügbar:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Je nach dem Anwendungsfall und den verfügbaren Daten bieten einige Algorithmen Vorteile gegenüber anderen. Im Folgenden werden einige Regressionsalgorithmen und ihre wichtigsten Anwendungsfälle beschrieben.

<a name="anchor-6b"></a> **[Bayessche lineare Regression][bayesian-linear-regression]** 
Die Bayessche lineare Regression ist ein Verfahren für die lineare Regression, bei dem die statistische Analyse im Kontext der Bayesschen Inferenz durchgeführt wird. Explizite Ergebnisse stehen für die A-posteriori-Wahrscheinlichkeitsverteilung der Modellparameter zur Verfügung, wenn das Regressionsmodell normal verteilte Fehler besitzt und eine bestimmte Form der Vorverteilung angenommen werden kann. [[Linear regression](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org), in englischer Sprache)]

<a name="anchor-6f"></a> **[Boosted Decision Tree-Regression][boosted-decision-tree-regression]** 
Die Boosted Decision Tree-Regression berechnet eine Beziehung zwischen Indikator und Antwortvariablen. Die Regressionsstruktur ähnelt einer Klassifizierungsstruktur. Die Endknoten sind die vorhergesagten Funktionswerte (Modellwerte). Die Vorhersagewerte sind auf die Werte in den Endknoten beschränkt. Einige der Vorteile der Verwendung von Entscheidungsstrukturen sind:

* Einfach zu interpretierende Entscheidungsregeln 
* Sie sind nicht parametrisch, sodass auf einfache Weise numerische oder kategorische Datenebenen verwendet werden können und keine unimodalen Trainingsdaten erforderlich sind
* Sie sind im Hinblick auf Ausreißer in den Trainingsdaten stabil 
* Klassifizierung kann schnell ausgeführt werden, nachdem die Regeln entwickelt wurden 

Es gibt jedoch einige Nachteile bei der Verwendung von Entscheidungsstrukturen:

* Sie neigen zu einer Überanpassung der Trainingsdaten, wodurch schlechte Ergebnisse bei der Anwendung auf das vollständige DataSet erzielt werden
* Eine Vorhersage über die minimalen und maximalen Grenzen der Antwortvariablen in den Trainingsdaten hinaus ist nicht möglich


<a name="anchor-6g"></a> **[Decision Forest-Regression][decision-forest-regression]** 
Decision Forests können für Klassifizierungs- (kategorische Variablen) oder Regressionsanwendungen (kontinuierliche Variablen) verwendet werden. Regression Forests können für die nichtlineare Regression abhängiger Variablen bei unabhängigen Eingaben verwendet werden, und sowohl Eingaben als Ausgaben können mehrdimensional sein. Regression Forests werden nicht so häufig verwendet wie ihre Klassifizierungsentsprechungen. Der Hauptunterschied besteht darin, dass die Ausgabebezeichner der Decision Forests, die den Eingabedaten zugeordnet sind, und die Trainingsbezeichner fortlaufend sein müssen. Daher muss die Zielfunktion entsprechend angepasst werden. Regression Forests besitzen viele der Vorteile von Klassifizierungsgesamtstrukturen wie die Effizienz und die Flexibilität.

<a name="anchor-6a"></a> **[Lineare Regression][linear-regression]** 
Die lineare Regression wird häufig zum Modellieren der Beziehungen zwischen einer skalar abhängigen Variable Y und einer oder mehreren erläuternden Variablen X verwendet. Sie wird häufig für die Vorhersage, Prognose oder Reduzierung angewendet. Sie kann auch verwendet werden, um ein Vorhersagemodell in ein untersuchtes DataSet von X- und Y-Werten einzupassen. Bei der linearen Regression wird davon ausgegangen, dass die zugrunde liegende Struktur von Y eine lineare Kombination der X-Variablen ist. Wird dann ein zusätzlicher Wert von X ohne zugehörigen Y-Wert angegeben, kann das eingepasste lineare Regressionsmodell verwendet werden, um den Y-Wert vorherzusagen. Lineare Regressionsmodelle werden i. d. R. gemäß der Methode der kleinsten Quadrate eingepasst. Es gibt aber auch andere Optionen zum Messen der besten Eignung. [[Linear regression](http://en.wikipedia.org/wiki/Bayesian_linear_regression) ([Wikipedia](http://en.wikipedia.org), in englischer Sprache)]

<a name="anchor-6c"></a> **[Regression mit neuronalen Netzwerken][neural-network-regression]** 
Neuronale Netzwerke sind ein nützliches statistisches Tool für die nicht parametrische Regression. Die nicht parametrische Regression behebt das Problem, dass versucht wird, ein Modell für eine Variable Y in einen Satz möglicher erläuternder Variablen X1; : : : ;Xp einzupassen, wobei die Beziehung zwischen X und Y komplexer als eine einfache lineare Beziehung sein könnte. [[A Framework for Nonparametric Regression Using Neural Networks](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee, ISDS, Duke University, in englischer Sprache]

<a name="anchor-6d"></a> **[Ordinal-Regression][ordinal-regression]** 
Die Ordinal-Regression ist eine Regressionsanalyse zum Modellieren oder Vorhersagen einer ordinal abhängigen Variable. Für ordinal abhängige Variablen können Sie die Werte ordnen, der tatsächliche Abstand zwischen den Kategorien ist dabei aber unbekannt. Nur die relative Reihenfolge zwischen den verschiedenen Werten ist wichtig. Da die Bezeichner oder Zielwerte eine natürliche Reihenfolge oder Rangfolge aufweisen, kann jede numerische Spalte als Ordinal-Ziel verwendet werden. Die natürliche Reihenfolge der Zahlen wird für ihre Rangfolge verwendet. Krankheiten werden beispielsweise auf Skalen von der leichtesten zur schwerwiegendsten bewertet. Bei Umfragen wählen die Befragten ihre Antworten auf einer Skala von "Stimme komplett zu" bis "Stimme überhaupt nicht zu" aus. Lernende werden auf einer Skala von 1 bis 6 eingestuft. Im Wesentlichen ist die Ordinal-Regression jedoch eine Erweiterung der logistischen Regression, die auf einem *proportionalen Wahrscheinlichkeitsmodell* basiert.


<a name="anchor-6e"></a> **[Poisson-Regression][poisson-regression]** 
Die Poisson-Regression wird häufig zum Modellieren von Zählungsdaten verwendet. Bei der Poisson-Regression wird davon ausgegangen, dass die Antwortvariable über eine Poisson-Verteilung verfügt. Daten in einer Poisson-Verteilung sind ganzzahlig (diskret und positiv), was für Zählungsdaten besonders sinnvoll ist. Wird ein Trainings-DataSet angegeben, versucht die Poisson-Regression die optimalen Werte durch Maximierung der logarithmischen Wahrscheinlichkeit der Parameter in den Eingaben zu finden. Die Wahrscheinlichkeit der Parameter ist die Wahrscheinlichkeit, dass die Trainingsdaten aus einer Verteilung mit diesen Parametern extrahiert wurden. Die Poisson-Regression wäre z. B. für Folgendes hilfreich:

* Modellierung der Anzahl von Erkältungen in Zusammenhang mit Flugreisen 
* Schätzung der Anzahl von Aufrufen im Zusammenhang mit einer Veranstaltung oder Promotion 
* Erstellung von Kontingenztafeln

## Referenzen

Eine vollständige Liste aller in Machine Learning Studio verfügbaren Algorithmustypen für maschinelles Lernen finden Sie unter [Modell initialisieren](https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/) in der [Machine Learning Studio: Algorithmus und Modul-Hilfe](https://msdn.microsoft.com/library/azure/dn905974.aspx).

Ausführlichere Informationen über alle Arten von Algorithmen für maschinelles Lernen finden Sie in den folgenden Ressourcen, von denen viele bei der Erstellung des Artikels verwendet wurden.

* [Choosing a Machine Learning Classifier](http://blog.echen.me/2011/04/27/choosing-a-machine-learning-classifier/), Edwin Chen (in englischer Sprache).

* [Decision Forests for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf), A. Criminisi1, J. Shotton2 und E. Konukoglu, Microsoft Research, 2011, Technical Report TR-2011-114 (in englischer Sprache).

* [A Framework for Nonparametric Regression Using Neural Networks](http://ftp.isds.duke.edu/WorkingPapers/00-32.pdf), Herbert K. H. Lee, ISDS, Duke University (in englischer Sprache).

* Handbook of Computational Statistics: Concepts and Methods edited by James E. Gentle, Wolfgang Karl Härdle, Yuichi Mori, Springer-Verlag Berlin/Heidelberg/New York, 2004 (in englischer Sprache).

* [Logistic Regression and Linear Discriminant Analyses in Evaluating Factors Associated with Asthma Prevalence among 10- to 12-Years-Old Children: Divergence and Similarity of the Two Statistical Methods](http://www.hindawi.com/journals/ijpedi/2009/952042/), George Antonogeorgos, Demosthenes B. Panagiotakos, Kostas N. Priftis, Anastasia Tzonou, International Journal of Pediatrics, 2009, Artikel-ID 952042 (in englischer Sprache).

* [The Optimality of Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf), Harry Zhang, University of New Brunswick 2004 (in englischer Sprache).

* [Support Vector Machines](http://www.astro.caltech.edu/~george/aybi199/AMooreTutorials/svm.ppt), Andrew W. Moore, Carnegie Mellon University 2001 (in englischer Sprache).

* [Machine Learning, Part II: Supervised and Unsupervised Learning](http://www.aihorizon.com/essays/generalai/supervised_unsupervised_machine_learning.htm), [AI Horizon](http://www.aihorizon.com/) (in englischer Sprache).

* [What are the advantages of logistic regression over decision trees?](http://www.quora.com/What-are-the-advantages-of-logistic-regression-over-decision-trees) ([Quora](http://www.quora.com/), in englischer Sprache).

* [What is the difference between supervised learning and unsupervised learning?](http://stackoverflow.com/questions/1832076/what-is-the-difference-between-supervised-learning-and-unsupervised-learning) ([Stackoverflow](http://stackoverflow.com/), in englischer Sprache).

* [When to choose which machine learning classifier?](http://stackoverflow.com/questions/2595176/when-to-choose-which-machine-learning-classifier) ([Stackoverflow](http://stackoverflow.com/), in englischer Sprache).

* [Wikipedia](http://en.wikipedia.org):
	* [Bayesian linear regression](http://en.wikipedia.org/wiki/Bayesian_linear_regression) (in englischer Sprache)
	* [Linear regression](http://en.wikipedia.org/wiki/Linear_regression) (in englischer Sprache)
	* [Multiclass classification](http://en.wikipedia.org/wiki/Multiclass_classification) (in englischer Sprache)
	* [Unsupervised learning](http://en.wikipedia.org/wiki/Unsupervised_learning) (in englischer Sprache)

Weitere Informationen:

* [Microsoft Azure Machine Learning – Cheat Sheet für Algorithmen](machine-learning-algorithm-cheat-sheet.md) (Microsoft).

* [Choosing the right estimator](http://scikit-learn.org/stable/tutorial/machine_learning_map/) ([scikit-learn](http://scikit-learn.org/stable/index.html), in englischer Sprache).


<!-- Module References -->
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[train-clustering-model]: https://msdn.microsoft.com/library/azure/bb43c744-f7fa-41d0-ae67-74ae75da3ffd/
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[decision-forest-regression]: https://msdn.microsoft.com/library/azure/562988b2-e740-4e3a-8131-358391bad755/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[neural-network-regression]: https://msdn.microsoft.com/library/azure/d7ee222c-669f-4200-a576-a761a9c1a928/
[ordinal-regression]: https://msdn.microsoft.com/library/azure/ffb557f8-dc7f-44bd-8fd0-b25666dd23f1/
[poisson-regression]: https://msdn.microsoft.com/library/azure/80e21b9d-3827-40d8-b733-b53148becbc2/

 

<!---HONumber=August15_HO6-->