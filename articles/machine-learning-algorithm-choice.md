<properties 
	title="" 
	pageTitle="Auswählen eines Algorithmus in Azure Machine Learning | Azure" 
	description="Erfahren Sie, wie Sie einen Algorithmus in Azure Machine Learning auswählen." 
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
	ms.date="02/9/2015" 
	ms.author="bradsev"/>


# Auswählen eines Algorithmus in Azure Machine Learning

In diesem Thema werden einige Aspekte von Machine Learning erläutert, insbesondere zum Auswählen eines geeigneten Algorithmus für die Analyse bestimmter Datentypen, um eine gestellte Frage zu beantworten, eine Aufgabe zu lösen oder Kriterien für die Entscheidungsfindung vorzugeben. Bei der Verwendung von Machine Learning für Analysen stellen sich i. d. R. zwei Fragen: 

* Mit welcher Art von Analyse erreichen wir unsere Ziele mit den verfügbaren Daten? 
* Was ist der am besten geeignete Algorithmus oder das ideale Modell für diese Analyse?

Es werden drei Arten der Analyse und deren Einsatzgebiete behandelt: 

* Klassifizierung 
* Regression 
* Clustering

Die verschiedenen Algorithmen für jeden dieser Analysetypen und die Module, in denen sie in Azure Machine Learning enthalten sind, werden ebenfalls behandelt. 


<a name="anchor-1"></a>
##**Maschinelles Lernen**

Maschinelles Lernen ist eine Disziplin, die eine Klasse von Algorithmen untersucht, die in dem Sinne datengesteuert sind, als sie dazu entwickelt wurden, aus Daten zu lernen und nicht ein bestimmtes, vordefiniertes Modell auf die Daten anzuwenden. Die Idee besteht darin, Wissen durch das Untersuchen von Mustern in einem DataSet zu gewinnen, anstatt wie gewöhnlich mit der als hypothetisch-deduktiv bezeichneten Methode, bei der Sie zuerst das richtige Modell für das gesamte DataSet erraten müssen, um dann empirische Tests durchzuführen. Für diese Art des Lernens aus Daten gibt es zwei Möglichkeiten: beaufsichtigtes und unbeaufsichtigtes Lernen. 

<a name="anchor-2"></a>
##**Beaufsichtigtes Lernen**  

Für das beaufsichtigte Lernen ist es erforderlich, dass die Zielvariable definiert ist und eine ausreichende Anzahl von Werten angegeben wird. 

Das beaufsichtigte Lernen ist die Art des Lernens, die angewendet wird, wenn die korrekten Ausgabeergebnisse (oder Zielvariablen) für die einzugebenden Lerninstanzen bekannt sind. Ziel des Trainings eines Algorithmus für das maschinelle Lernen ist die Ermittlung eines Modells (also einer Regel oder Funktion), das Eingaben bekannten Ausgabewerten zuordnet. Dies ist vergleichbar mit einem Supervisor, der dem Algorithmus-Agent sagen kann, ob die Eingaben richtig den Ausgaben zugeordnet wurden. Sobald der Lernvorgang abgeschlossen ist und es ein funktionierendes Modell gibt, kann es auf neue Eingabedaten angewendet werden, um Vorhersagen über die erwartete Ausgabe zu treffen, wenn anders als beim Trainings-DataSet der Zielwert nicht im Voraus bekannt ist.

Die Art der Zielvariablen bestimmt also den Modelltyp.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help9.png)

Es gibt zwei allgemeine Kategorien der Analyse beim beaufsichtigten Lernen: Klassifizierung und Regression. Beaufsichtigtes Lernen ist besonders bei *classification problems* häufig anzutreffen, da das Ziel häufig ist, dass der Computer ein Klassifizierungssystem erlernt, das wir erstellt haben. Die Antworten sind i. d. R. nur einige bekannte Werte (Bezeichner) wie 'true' oder 'false'. Klassifikationsalgorithmen gelten für nominale, nicht ordinale Antwortwerte. Die Ziffernerkennung ist ein typisches Beispiel für das Klassifizierungs-Lernen. Allgemeiner gilt, dass sich das Klassifizierungs-Lernen besonders für Probleme eignet, bei denen das Ableiten einer Klassifizierung nützlich und die Klassifizierung leicht zu ermitteln ist.

Beim beaufsichtigten Lernen können die untersuchten Variablen in zwei Gruppen aufgeteilt werden: erklärende Variablen (auch Indikatoren genannt) und abhängige Variablen (oder Antwortvariablen). Das Ziel der Analyse ist die Angabe einer Beziehung zwischen den erklärenden Variablen und den abhängigen Variablen, wie bei der *regression analysis*. Die Werte der abhängigen Variablen müssen für einen ausreichend großen Teil des DataSets bekannt sein. Bei der Regression übernehmen die Antworten oder Ausgabevariablen kontinuierliche Werte, wie z. B. Kilometer pro Liter für ein bestimmtes Auto, das Alter einer Person usw.

Das beaufsichtigte Lernen ist auch die gängigste Methode zum Trainieren neuronaler Netzwerke und Entscheidungsstrukturen. Beide Verfahren sind stark abhängig von den Angaben der vordefinierten Klassifizierungen. 

* Bei neuronalen Netzwerken wird die Klassifizierung angewendet, um den Fehler im Netzwerk zu bestimmen und das Netzwerk anzupassen, um diesen Fehler zu minimieren. 
* Im Fall von Entscheidungsstrukturen werden die Klassifizierungen verwendet, um zu bestimmen, welche Attribute möglichst viele Informationen bereitstellen, die zum Lösen der Klassifizierungsaufgabe verwendet werden können.  

In beiden Beispielen ist eine "Beaufsichtigung" in dem Sinne erforderlich, dass sie abhängig von den bereitgestellten vordefinierten Klassifizierungen sind. 

Die Spracherkennung mithilfe von versteckten Markov-Modellen und Bayesschen Netzen ist ebenfalls von einer gewissen Beaufsichtigung abhängig, da nämlich die Parameter angepasst werden müssen, um den Fehler für die angegebenen Eingaben zu minimieren. 

<a name="anchor-3"></a>
##**Unbeaufsichtigtes Lernen**

Das Problem beim maschinellen Lernen mit dem unbeaufsichtigten Verfahren besteht in der Ermittlung von Mustern oder versteckten Strukturen in nicht bezeichneten Daten. Das Modell wird nicht mit den "richtigen Ergebnissen" für ein DataSet bereitgestellt, mit denen das Lernen durchgeführt werden kann. Da die Beispiele für den Lernenden keine Bezeichnung haben, gibt es kein Fehler- oder Belohnungssignal für eine potenzielle Lösung. Das Ziel ist es, dass der Computer lernt, wie etwas funktioniert, von dem wir ihm nicht mitteilen, wie es geht! Alle Variablen werden beim unbeaufsichtigten Lernen auf die gleiche Weise behandelt. Es gibt keinen Unterschied zwischen erklärenden und abhängigen Variablen. Es gibt jedoch immer noch einige Ziele zu erreichen. Diese Ziele sind möglicherweise sehr allgemein wie eine Reduzierung der Datenmenge oder spezifischer wie das Suchen von Clustern. 

In Azure Machine Learning können wir sowohl das unbeaufsichtigte als auch das beaufsichtigte Lernen über **Klassifizierung**, **Clustering** und **Regression** durchführen.

   ![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help2.png)

<a name="anchor-4"></a>
##**Clustering**  
Clustering ist ein Beispiel für unbeaufsichtigtes Lernen. Bei diesem Lernen ist das Ziel die Suche nach Ähnlichkeiten in den Trainingsdaten und das Aufteilen des DataSets in Teilmengen, die von diesen Ähnlichkeiten abgegrenzt werden können. Die Annahme, dass die wichtigsten Cluster, die bei dieser datengesteuerten Vorgehensweise ermittelt werden, mit unserer intuitiven Klassifizierung übereinstimmen, wird häufig, aber nicht immer erfüllt. 

Obwohl der Algorithmus diesen Clustern keine geeigneten Namen zuweist, kann er sie erstellen und dann zum Ermitteln von Ähnlichkeiten verwenden, die in neuen Beispielen zu erwarten sind, indem er sie in die wahrscheinlichsten Cluster klassifiziert. Dies ist eine datengesteuerte Vorgehensweise, die bei ausreichenden Datenmengen sehr gut funktioniert. So werden z. B. Algorithmen zum Filtern sozialer Informationen wie der von Amazon.com verwendet, um Bücher auf der Grundlage ähnlicher Personengruppen zu empfehlen und neue Benutzer diesen Gruppen zum Zweck von Empfehlungen zuzuordnen.

Der Clustering-Algorithmus in Azure Machine Learning ist k-Means.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help10.png)

k-Means ist einer der einfachsten Algorithmen für das unbeaufsichtigte Lernen zum Lösen bekannter Clustering-Probleme. Der k-Means-Algorithmus fasst Daten in Clustern zusammen, indem er versucht, die Daten in N-Gruppen mit gleicher Varianz aufzuteilen und damit ein Kriterium zu minimieren, das als Trägheit oder Summe der quadrierten Abweichungen von den Cluster-Schwerpunkten bezeichnet wird. Dieser Algorithmus erfordert die Angabe der Anzahl von Clustern. Er kann sehr gut auf große Datenmengen skaliert werden und kommt daher bei einer Vielzahl von Anwendungsbereichen in vielen verschiedenen Bereichen zum Einsatz.

Das Modul **k-Means Clustering**, das den k-Means-Algorithmus implementiert, gibt ein untrainiertes k-Means-Clustermodell zurück, das zu Lernzwecken an das Modul **Train Clustering Model** übergeben werden kann.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/k4.png)

Diese Abbildung zeigt, dass bei Verwendung von "K-Means Clustering" Optionen konfiguriert werden können. Die K-Means-Methode findet eine angegebene Anzahl von Clustern für einen Satz von D-dimensionalen Datenpunkten. Beginnend mit einem *initial set of K centroids* verwendet die Methode den Lloyd-Algorithmus, um die Orte von Schwerpunkten iterativ zu verfeinern. Der Algorithmus wird beendet, wenn sich die Schwerpunkte stabilisieren oder eine *specified number of iterations* abgeschlossen wurde.
Das Modul initialisiert ein K-mal-D-Array mit den endgültigen Schwerpunkten, die die K-Cluster in den N Datenpunkten definieren. Der Algorithmus verwendet einen Vektor der Länge N, bei dem jeder Datenpunkt einem K-Cluster zugeordnet ist.
Wenn eine bestimmte Anzahl von Clustern (K) für die Suche angegeben wurde, weist das Modul die ersten K Datenpunkte in der Reihenfolge den K-Clustern zu.
Dieses Modul akzeptiert oder generiert auch Anfangspunkte zum Definieren der anfänglichen Clusterkonfiguration.
Die *Kennzahl* definiert die Methode, die zum Messen des Abstands zwischen einem Datenpunkt und dem Schwerpunkt verwendet wird.
Jeder Datenpunkt wird dem Cluster zugeordnet, dessen Schwerpunkt dem Datenpunkt am nächsten liegt. Standardmäßig verwendet die Methode die *Euclidean metric*. Sie können als eine alternative Kennzahl die *cosine metric* für die Methode angeben.
Beachten Sie, dass die K-Means-Methode eventuell nur eine lokale optimale Clusterkonfiguration für ein DataSet findet. Die Methode könnte mit einer anderen Anfangskonfiguration möglicherweise eine andere, eventuell bessere Konfiguration finden.

<a name="anchor-5"></a>
##**Klassifizierung**  
Bei der Klassifizierungsanalyse unterteilen wir die Daten in Klassen und verwenden dann einen trainierten Teil mit zuvor bezeichneten Daten. Das Verfahren dient zur Vorhersage der Gruppenmitgliedschaft für Dateninstanzen. 
In Azure Machine Learning stehen die folgenden Algorithmen für die Klassifizierung zur Verfügung.

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help3.png)

Die *Two-Class algorithms* werden für binäre Antwortvariablen verwendet (Ja oder Nein, 0 oder 1, wahr oder falsch usw.), während *Multiclass algorithms* für alle nominalen Antwortvariablen verwendet werden, die Instanzen in mehr als zwei Klassen unterteilen.

### Richtlinien zum Auswählen eines Klassifikationsalgorithmus
Diese lange Liste von Algorithmen führt zu einer Reihe von Fragen: 

* Wie wissen Sie, welche dieser vielen Klassifizierungen am besten für ein bestimmtes DataSet verwendet wird? 
* Gibt es Fälle, in denen es eine "natürliche" Wahl gibt? 
* Was sind die Prinzipien für diese Wahl?

Eine empfohlene Vorgehensweise besteht darin, mehrere unterschiedliche Klassifizierungen sowie verschiedene Parametersätze innerhalb jedes Algorithmus zu testen und dann die beste Version mithilfe der Kreuzvalidierung auszuwählen. Hier sind einige allgemeine Richtlinien als Ansatz für die Überlegung. Berücksichtigen Sie bei der Auswahl des zu verwendenden Algorithmus Folgendes:

**Wie umfangreich sind Ihre Trainingsdaten?**
Wenn der Trainingssatz klein ist und Sie eine beaufsichtigte Klassifizierung trainieren möchten, empfiehlt die Theorie zum maschinellen Lernen, dass Sie eine Klassifizierung mit hoher Erwartungstreue und geringer Varianz wie den Naiven Bayes-Klassifikator verwenden sollten. Sie verfügen über einen Vorteil gegenüber Klassifizierungen mit geringer Erwartungstreue und hoher Varianz wie "kNN", da letztere zur Überanpassung neigt. Klassifizierungen mit geringer Erwartungstreue und hoher Varianz sind jedoch besser geeignet, wenn der Trainingssatz größer wird (sie haben einen kleineren asymptotischen Fehler), da Klassifizierungen mit hoher Erwartungstreue nicht leistungsfähig genug sind, um genaue Modelle bereitzustellen. Es gibt theoretische und empirische Ergebnisse, die darauf hinweisen, dass der Naive Bayes-Klassifikator auch in solchen Fällen geeignet ist. Beachten Sie jedoch, dass gute Daten i. d. R. besser als gute Algorithmen sind und dass der Entwurf guter Funktionen entscheidende Vorteile bietet. Bei einem sehr großen DataSet hat der gewählte Klassifikationsalgorithmus möglicherweise keine Bedeutung für die Leistung der Klassifizierung. Daher ist es am besten, den Algorithmus auf Grundlage von  guter Skalierbarkeit, Geschwindigkeit und Benutzerfreundlichkeit zu wählen.

**Müssen Sie inkrementell oder stapelweise trainieren?** 
Wenn Sie die Klassifizierung häufig mit neuen Daten aktualisieren müssen (oder große Datenmengen verwenden), dann sollten Sie vermutlich Bayes-Algorithmen verwenden, die gut aktualisiert werden können. Neuronale Netze und SVM müssen die Trainingsdaten stapelweise verarbeiten.

**Sind Ihre Daten ausschließlich kategorisch oder ausschließlich numerisch oder eine Mischung aus beiden?** 
Bayes funktioniert am besten mit kategorischen/binomialen Daten. Entscheidungsstrukturen können numerische Werte nicht vorhersagen.

**Müssen Sie oder Ihre Benutzer verstehen, wie die Klassifizierung funktioniert?**  Verwenden Sie Bayes oder Entscheidungsstrukturen, da diese den meisten Benutzern einfach erklärt werden können. Neuronale Netzwerke und SVM sind "Black Boxes" in dem Sinne, dass Sie nicht wirklich sehen können, wie die Daten klassifiziert werden.

**Wie schnell muss die Klassifizierung generiert werden?** 
SVMs sind schnell, wenn es um Klassifizierung geht, da sie nur bestimmen müssen, auf welche Seite der "Linie" Ihre Daten gehören. Entscheidungsstrukturen können sehr langsam sein, wenn sie komplex sind (z. B. mit vielen Verzweigungen).

**Wie viel Komplexität besitzt oder erfordert das Problem?** Neuronale Netzwerke und SVMs können mit komplexen, nicht linearen Klassifizierungen umgehen.

### Vor- und Nachteile der Klassifizierungsalgorithmen
Jeder dieser Klassifizierungsalgorithmen hat einige Vorteile und  einige Nachteile:

<a name="anchor-5a"></a>
**Vor- und Nachteile der logistischen Regression:**   
Die logistische Regressionsanalyse basiert auf der Berechnung der Wahrscheinlichkeit des Ergebnisses als das Verhältnis zwischen der Wahrscheinlichkeit für das Ergebnis geteilt durch die Wahrscheinlichkeit des Nichteintreffens des Ergebnisses. Das logistische Modell  ist parametrisch und hat daher den Vorteil, dass es einen Einblick in die Auswirkungen der einzelnen Indikatorvariablen auf die Antwortvariable bietet. Mit den verfügbaren natürlich probabilistischen Interpretationen (im Gegensatz zu Entscheidungsstrukturen oder SVMs) können wir das Modell ganz einfach für neue Daten aktualisieren. Es gibt viele Möglichkeiten zur Regulierung des Modells. Sie müssen sich auch keine Gedanken über die Korrelation Ihrer Funktionen wie beim Naiven Bayes-Verfahren machen. Die logistische Regression ist nützlich, wenn Sie 

* einen probabilistischen Rahmen benötigen, der die Anpassung der Klassifizierungsschwellenwerte erleichtert, wenn Sie sich nicht sicher sind oder Konfidenzintervalle benötigen, 
* in der Zukunft weitere Trainingsdaten erwarten, die schnell in das Modell integriert werden sollen. 

Logistische Regression funktioniert bei Daten mit vielen Dimensionen besser als Entscheidungsstrukturen. Bei Textklassifizierungen haben Sie z. B. möglicherweise mehr als 100.000 Dokumente mit 500.000 einzelnen Wörtern (Funktionen). Eine einfache Regel wie das Lernen einer linearen Hyperebene ist besser, da Entscheidungsstrukturen viel zu viele Freiheiten bieten und anfällig für eine Überanpassung sind. Sie könnten über die Funktionsauswahl eine Entscheidungsstruktur auf Textdaten anwenden, dabei gehen jedoch viele wertvolle Informationen für die Textklassifizierung verloren, da eine erheblich reduzierte Teilmenge von Funktionen ausgewählt wird. Wenn Lernmodelle mit Daten mit vielen Dimensionen angewendet werden, vergrößern sich varianzbasierte Fehler sehr leicht. Deshalb sind einfache Modelle mit höherer Erwartungstreue die bessere Wahl. 

Ein Nachteil der logistischen Regression besteht darin, dass sie instabil ist, wenn ein Indikator fast die Antwortvariable erklären kann, da der Koeffizient dieser Variable sehr umfangreich wird.

<a name="anchor-5b"></a>
**Vor- und Nachteile von Entscheidungsstrukturen:**   
[Entscheidungsstrukturen](http://research.microsoft.com/pubs/155552/decisionForests_MSR_TR_2011_114.pdf) sind leicht zu interpretieren und zu erläutern. Sie können sehr gut mit Funktionsinteraktionen umgehen und sind nicht parametrisch, sodass Sie sich keine Sorgen um Ausreißer machen müssen oder darüber, ob die Daten linear trennbar sind (Entscheidungsstrukturen können z. B. problemlos für Fälle verwendet werden, bei denen Sie die Klasse A am unteren Ende der Funktion x, Klasse B in der Mitte von Feature x und dann erneut A im hohen Bereich haben). Entscheidungsstrukturen generieren die Ausgabe als Regeln zusammen mit Kennzahlen wie Support, Confidence und Lift. 

Ein Nachteil von Entscheidungsstrukturen ist, dass sie kein Onlinetraining unterstützen, sodass Sie Ihre Struktur neu erstellen müssen, wenn neue Daten eintreffen. Ein weiterer Nachteil ist, die sie zu Überanpassung neigen - in solchen Fällen können jedoch Zusammenfassungsmethoden wie Random Forests (oder Boosted Trees) helfen. Random Forests sind auch bei vielen Problemen bei der Klassifizierung nützlich. Sie wirken i. d. R. etwas besser als SVMs: Sie sind schnell und skalierbar, und Sie müssen nicht so viele Parameter wie bei SVMs anpassen.


<a name="anchor-5c"></a>
**Vor- und Nachteile von SVMs:**   
Support Vector Machines (SVMs) sind sehr effektiv in Räumen mit sehr vielen Dimensionen. Auch in Fällen, in denen die Anzahl der Dimensionen größer als die Anzahl der Proben ist, sind sie weiter effektiv. Ist die Anzahl der Funktionen jedoch wesentlich größer als die Anzahl der Proben, ist dieses Verfahren wahrscheinlich ineffizient. SVMs sind auch speichereffizient, da sie eine Teilmenge der Trainingspunkte in der Entscheidungsfunktion (so genannte Support-Vektoren) verwenden.  Sie sind sehr vielseitig: Es können verschiedene Kernel-Funktionen für die Entscheidungsfunktion angegeben werden. Es werden zwar gemeinsame Kernel bereitgestellt, aber es ist auch möglich, benutzerdefinierte Kernel anzugeben. Die Kernel-Funktion dient zum Umwandeln niedrig-dimensionaler Trainingsproben in höhere Dimensionen. Dies ist besonders für das Problem der linearen Separierbarkeit hilfreich. 

SVMs bieten jedoch keine direkten Wahrscheinlichkeitsschätzungen. Diese werden mit einer aufwendigen fünffachen Kreuzvalidierung berechnet. Mit hoher Genauigkeit, einer geringen Wahrscheinlichkeit für Überanpassung und einem geeigneten Kernel funktionieren sie auch dann gut, wenn die Daten im Basisfunktionsbereich nicht linear separierbar sind. Sie sind speziell bei Textklassifizierungsproblemen beliebt, bei denen Räume mit vielen Dimensionen die Norm sind. Im Gegensatz zu Gesamtstrukturen sind SVMs ursprünglich Zwei-Klassen-Klassifikatoren. Mittlerweile wurden sie aber auch für die Arbeit mit mehreren Klassen angepasst. Mit einem Training wie "One-vs-Rest" können Sie einen Multi-Klassen-Klassifikator erstellen, der aber möglicherweise nicht optimal arbeitet. Da SVMs nur Zwei-Klassen-Ausgaben behandeln können (d. h. eine kategorische Ausgabevariable mit der Varianz 2), lernt er mit N Klassen auch N SVMs (SVM 1 lernt "Ausgabe == 1" vs. "Ausgabe != 1", SVM 2 lernt "Ausgabe == 2" vs. "Ausgabe != 2", ... , SVM N lernt "Ausgabe == N" vs. "Ausgabe != N"). Um dann die Ausgabe für eine neue Eingabe vorherzusagen, führt er einfach eine Vorhersage mit jeder SVM durch und ermittelt so, welche die Vorhersage trifft, die am weitesten in den positiven Bereich reicht.

<a name="anchor-5d"></a>
**Vor- und Nachteile von Naiven Bayes-Klassifikatoren:**   
[Naive Bayes](http://www.aaai.org/Papers/FLAIRS/2004/Flairs04-097.pdf)-Klassifikatoren (NB) werden häufig für Klassifizierungsprobleme verwendet. Sie gehen von unabhängigen Funktionen aus - weshalb dieses Verfahren als 'naive' bezeichnet wird. Wenn die Annahme des NB-Klassifikators zur bedingten Unabhängigkeit der Überprüfung standhält, konvergiert der Naive Bayes-Klassifikator schneller als diskriminative Modelle wie die logistische Regression. In solchen Fällen benötigen Sie also weniger Trainingsdaten. 

Auch wenn die NB-Annahme nicht standhält, ist ein NB-Klassifikator in der Praxis häufig hervorragend geeignet.  Bayes ist aber nicht nur gut, wenn die Funktionen unabhängig sind, sondern auch, wenn die Abhängigkeiten der Funktionen untereinander ähnlich sind. Daher ist NB die richtige Wahl für eine schnelle und einfache Operation mit guter Leistung.

Der Hauptnachteil ist, dass kein Lernen zu den Interaktionen zwischen den Funktionen stattfindet (NB wird z. B. nicht lernen, dass Sie, obwohl Sie Filme mit Brad Pitt und Tom Cruise lieben, Filme hassen, in denen sie zusammen spielen).


<a name="anchor-5e"></a>
**One-vs-All:**  
One-vs-All ist eine Strategie zur Reduzierung des Problems der Multi-Klassen-Klassifizierung bei einem Satz mit mehreren binären Klassifizierungsproblemen. Die Strategie umfasst eine einzelne Klassifizierung pro Klasse, wobei Proben dieser Klasse die Positivproben und alle anderen Proben die Negativproben sind. Für diese Strategie müssen die Basisklassifikatoren für ihre Entscheidung einen Echtwert-Confidence-Wert und nicht einfach einen Klassenbezeichner verwenden. Diskrete Klassenbezeichner allein können zu Mehrdeutigkeiten führen, bei denen mehrere Klassen für eine einzelne Probe vorhergesagt werden.	


<a name="anchor-6"></a>
##**Regression**  
Bei einer Regressionsanalyse erfolgen die Vorhersagen anhand der früheren Inferenz. Die neuen Werte für eine abhängige Variable werden basierend auf dem Wert eines oder mehrerer gemessener Attribute berechnet. Folgende Regressionsalgorithmen sind in Azure Machine Learning verfügbar:

![screenshot_of_experiment](./media/machine-learning-algorithm-choice/help4.png)

Je nach dem Anwendungsfall und den verfügbaren Daten bieten einige Algorithmen/Module Vorteile gegenüber anderen. Im Folgenden werden einige Regressionsalgorithmen und ihre wichtigsten Anwendungsfälle beschrieben.

<a name="anchor-6b"></a>
**Bayessche lineare Regression**                      
Die Bayessche lineare Regression ist ein Verfahren für die lineare Regression, bei dem die statistische Analyse im Kontext der Bayesschen Inferenz durchgeführt wird. Explizite Ergebnisse stehen für die A-posteriori-Wahrscheinlichkeitsverteilung der Modellparameter zur Verfügung, wenn das Regressionsmodell normal verteilte Fehler besitzt und eine bestimmte Form der Vorverteilung angenommen werden kann.

<a name="anchor-6f"></a>
**Boosted Decision Tree-Regression**  
Bei dieser Regression wird eine Beziehung zwischen Indikator und Antwortvariablen berechnet. Die Regressionsstruktur ähnelt einer Klassifizierungsstruktur. Die Endknoten sind die vorhergesagten Funktionswerte (Modellwerte). Die Vorhersagewerte sind auf die Werte in den Endknoten beschränkt. Einige der Vorteile der Verwendung von Entscheidungsstrukturen sind: 

* Einfach zu interpretierende Entscheidungsregeln 
* Sie sind nicht parametrisch, sodass auf einfache Weise numerische oder kategorische Datenebenen integriert werden können und keine unimodalen Trainingsdaten ausgewählt werden müssen 
* Sie sind im Hinblick auf Ausreißer in den Trainingsdaten stabil 
* Die Klassifizierung ist nach der Erstellung der Regeln sehr schnell 

Ein kleiner Nachteil bei der Verwendung von Entscheidungsstrukturen ist, dass sie zu Überanpassung bei Trainingsdaten neigen. Dies kann zu schlechten Ergebnissen führen, wenn sie auf das vollständige DataSet angewendet werden und es nicht möglich ist, über die Grenzen der Mindest- und Höchstgrenzen der Antwortvariablen in den Trainingsdaten hinaus eine Vorhersage zu treffen.

<a name="anchor-6g"></a>
**Decision Forest-Regression**  
Decision Forests können für Klassifizierungs- (kategorische Variablen) oder Regressionsanwendungen (kontinuierliche Variablen) verwendet werden. Regression Forests können für die nicht lineare Regression abhängiger Variablen bei unabhängigen Eingaben verwendet werden. Eingaben und Ausgaben können mehrdimensional sein. Regression Forests werden nicht so häufig verwendet wie ihre Klassifizierungsentsprechungen. Der Hauptunterschied besteht darin, dass der Ausgabebezeichner der Decision Forests einem Eingabedatum zugeordnet wird, sodass die Trainingsbezeichner fortlaufend sein müssen. Daher muss die Zielfunktion entsprechend angepasst werden. Regression Forests besitzen viele der Vorteile von Klassifizierungs-Gesamtstrukturen wie die Effizienz und die Flexibilität.

<a name="anchor-6a"></a>
**Lineare Regression**  
Die lineare Regression wird häufig zum Modellieren der Beziehungen zwischen einer skalar abhängigen Variable Y und einer oder mehreren erläuternden Variablen X verwendet. Sie wird häufig für die Vorhersage, Prognose oder Reduzierung angewendet. Sie kann auch verwendet werden, um ein Vorhersagemodell in ein untersuchtes DataSet von X- und Y-Werten einzupassen. Bei der linearen Regression wird davon ausgegangen, dass die zugrunde liegende Struktur von Y eine lineare Kombination der X-Variablen ist. Wird dann ein zusätzlicher Wert von X ohne zugehörigen Y-Wert angegeben, kann das eingepasste lineare Regressionsmodell verwendet werden, um den Y-Wert vorherzusagen. Lineare Regressionsmodelle werden i. d. R. gemäß der Methode der kleinsten Quadrate eingepasst. Es gibt aber auch andere Optionen zum Messen der besten Eignung.

<a name="anchor-6c"></a>
**Regression mit neuronalen Netzwerken**  
Neuronale Netzwerke sind ein nützliches statistisches Tool für die nicht parametrische Regression. Die nicht parametrische Regression behebt das Problem, dass versucht wird, ein Modell für eine Variable Y in einen Satz möglicher erläuternder Variablen X1; : ab: ab: ;Xp einzupassen, wobei die Beziehung zwischen X und Y komplexer als eine einfache lineare Beziehung sein könnte.

<a name="anchor-6d"></a>
**Ordinal-Regression**   
Die Ordinal-Regression ist eine Regressionsanalyse zum Modellieren oder Vorhersagen einer ordinal abhängigen Variable. Für ordinal abhängige Variablen können Sie die Werte ordnen, der tatsächliche Abstand zwischen den Kategorien ist dabei aber unbekannt. Nur die relative Reihenfolge zwischen den verschiedenen Werten ist wichtig. Da die Bezeichner oder Zielwerte eine natürliche Reihenfolge oder Rangfolge aufweisen, kann jede numerische Spalte als Ordinal-Ziel verwendet werden. Die natürliche Reihenfolge der Zahlen wird für ihre Rangfolge verwendet. Krankheiten werden auf Skalen von der leichtesten zur schwerwiegendsten bewertet. Bei Umfragen wählen die Befragten ihre Antworten auf einer Skala von "Stimme komplett zu" bis "Stimme überhaupt nicht zu" aus. Lernende werden auf einer Skala von 1 bis 6 eingestuft. Im Wesentlichen ist die Ordinal-Regression jedoch eine Erweiterung der logistischen Regression, die auf einem proportionalen Wahrscheinlichkeitsmodell basiert.


<a name="anchor-6e"></a>
**Poisson-Regression**  
Die Poisson-Regression wird häufig zum Modellieren von Zählungsdaten verwendet. Bei der Poisson-Regression wird davon ausgegangen, dass die Antwortvariable über eine Poisson-Verteilung verfügt. Daten in einer Poisson-Verteilung sind ganzzahlig (diskret und positiv), was für Zählungsdaten besonders sinnvoll ist. Wird ein Trainings-DataSet angegeben, versucht die Poisson-Regression die optimalen Werte durch Maximierung der logarithmischen Wahrscheinlichkeit der Parameter in den Eingaben zu finden. Die Wahrscheinlichkeit der Parameter ist die Wahrscheinlichkeit, dass die Trainingsdaten aus einer Verteilung mit diesen Parametern extrahiert wurden. Die Poisson-Regression wäre z. B. für Folgendes hilfreich: 

* Modellierung der Anzahl von Erkältungen in Zusammenhang mit Flugreisen 
* Schätzung der Anzahl von Aufrufen im Zusammenhang mit einer Veranstaltung oder Promotion 
* Erstellung von Kontingenztafeln



<!--HONumber=49-->