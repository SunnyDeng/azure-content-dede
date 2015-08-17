<properties 
	pageTitle="Machine Learning Cheat Sheet für Algorithmen | Microsoft Azure" 
	description="Eine druckbare Version des Machine Learning Cheat Sheet für Algorithmen, mit dem Sie den richtigen Algorithmus für Ihr Vorhersagemodell in Azure Machine Learning Studio auswählen können."	
	services="machine-learning"
	documentationCenter="" 
	authors="brohrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="brohrer;garye" />


# Machine Learning – Cheat Sheet für Algorithmen für Microsoft Azure Machine Learning Studio

Das **Microsoft Azure Machine Learning – Cheat Sheet für Algorithmen** erleichtert Ihnen die Auswahl des richtigen Lernalgorithmus für Vorhersageanalyselösungen aus der Microsoft Azure Machine Learning-Algorithmusbibliothek. Das Cheat Sheet stellt Fragen über die Art der Daten und das Problem, das Sie lösen möchten, und schlägt dann einen Algorithmus vor, den Sie ausprobieren können.

[Azure Machine Learning Studio](https://studio.azureml.net/) verfügt über eine große Anzahl von Algorithmen für maschinelles Lernen für Ihre Vorhersageanalyselösungen. Diese Algorithmen sind in die allgemeinen Kategorien für maschinelles Lernen ***Regression***, ***Klassifizierung***, ***Clustering*** und ***Anomalienerkennung*** unterteilt. Jede dieser Kategorien gilt für eine andere Art von Problemen beim maschinellen Lernen.



## Machine Learning Cheat Sheet für Algorithmen herunterladen

Laden Sie das Cheat Sheet für Algorithmen für maschinelles Lernen herunter, mit dem Sie einen Algorithmus für maschinelles Lernen auswählen können. Drucken Sie das Cheat Sheet in Tabloid-Größe (11 x 17 Zoll) aus, um schnell darauf zugreifen zu können.

**Sie können das Cheat Sheet hier herunterladen: [Microsoft Azure Machine Learning Algorithm Cheat Sheet](http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet-v2.pdf)** (in englischer Sprache).

![Machine Learning Cheat Sheet für Algorithmen: Wie wähle ich einen Algorithmus für maschinelles Lernen aus?][cheat-sheet]

[cheat-sheet]: ./media/machine-learning-algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet-microsoft-azure.png



<!-- This is now covered in the first footnote below
[Azure Machine Learning Studio](https://studio.azureml.net/) gives you the flexibility to experiment - 
try one algorithm, and if you're not satisfied with the results, try another.
Here's an example from the [Azure Machine Learning Gallery](http://gallery.azureml.net) of an experiment that tries several algorithms against the same data and compares the results:
[Compare Multi-class Classifiers: Letter recognition](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).
-->

## Weitere Hilfe zu Algorithmen 

* Eine eingehendere Erläuterung der verschiedenen Typen von Algorithmen für maschinelles Lernen und ihre Verwendung finden Sie unter [Auswählen eines Algorithmus in Azure Machine Learning](machine-learning-algorithm-choice.md).
* Eine in Kategorien unterteilte Liste aller in Machine Learning verfügbaren Algorithmen für maschinelles Lernen finden Sie in der Hilfe zu den Algorithmen und Modulen in Machine Learning unter [Modell initialisieren][initialize-model].
* Eine vollständige Liste aller in Machine Learning Studio verfügbaren Algorithmen und Module finden Sie in der Hilfe zu den Algorithmen und Modulen in Machine Learning Studio unter [Modulliste von A bis Z für Machine Learning Studio](https://msdn.microsoft.com/library/azure/dn906033.aspx).

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Hinweise und Terminologiedefinitionen für das Machine Learning Cheat Sheet für Algorithmen

* Die Vorschläge in diesem Cheat Sheet für Algorithmen stellen nur grobe Richtlinien dar. Einige können leicht abgeändert werden und andere sogar stark überarbeitet. Sie dienen lediglich als Ausgangspunkt und Anregung. Sie sollten auch direkte Vergleiche zwischen verschiedenen Algorithmen mit Ihren Daten durchführen. Es gibt keinen Ersatz für das grundsätzliche Verständnis der einzelnen Algorithmen und des Systems, das Ihre Daten generiert hat. 

* Jeder Algorithmus für maschinelles Lernen hat seinen eigenen Stil oder *induktiven Bias*. Für ein bestimmtes Problem können verschiedene Algorithmen geeignet sein, aber ein Algorithmus passt möglicherweise besser als andere. Es ist jedoch nicht immer schon im Vorfeld klar, welches der beste Algorithmus ist. In diesen Fällen werden im Cheat Sheet mehrere Algorithmen zusammen angegeben. Eine geeignete Strategie bestünde darin, einen Algorithmus zu testen und bei nicht zufriedenstellenden Ergebnissen einen anderen zu versuchen. Es folgt ein Beispiel aus dem [Azure Machine Learning-Katalog](http://gallery.azureml.net/). Dabei handelt es sich um ein Experiment, mit dem mehrere Algorithmen auf dieselben Daten angewendet und die Ergebnisse verglichen werden: [Vergleichen von Multiklassen-Klassifizierungen: Buchstabenerkennung](http://gallery.azureml.net/Details/a635502fc98b402a890efe21cec65b92).

* Es gibt drei Arten von maschinellem Lernen: **beaufsichtigtes Lernen**, **unbeaufsichtigtes Lernen** und **vertiefendes Lernen**.

  * Beim **beaufsichtigten Lernen** wird jeder Datenpunkt bezeichnet oder einer Kategorie oder einem Wert zugeordnet. Ein Beispiel für eine Kategoriebezeichnung ist die Zuordnung eines Bildes zu "Katze" oder "Hund". Ein Beispiel für eine Wertbezeichnung ist der Verkaufspreis für ein gebrauchtes Auto. Ziel beim beaufsichtigten Lernen ist es, viele bezeichnete Beispiele wie diese zu untersuchen und dann Vorhersagen zu zukünftigen Datenpunkten zu treffen– z. B., um neue Fotos mit dem richtigen Tier zu identifizieren oder präzise Verkaufspreise für gebrauchte PKW festzulegen. Dies ist eine häufige und nützliche Verwendung für das maschinelle Lernen. Alle Module in Azure Machine Learning sind Algorithmen zum beaufsichtigten Lernen, mit Ausnahme von [K-Means-Clustering][k-means-clustering].

  * Beim **unbeaufsichtigten Lernen** werden den Datenpunkten keine Bezeichnungen zugeordnet. Stattdessen besteht das Ziel von Algorithmen zum unbeaufsichtigten Lernen im Organisieren der Daten in einer bestimmten Form oder in der Beschreibung ihrer Struktur. Dies kann das Gruppieren in Clustern bedeuten, wie bei K-Means, oder das Suchen nach unterschiedlichen Möglichkeiten zur Darstellung komplexer Daten in einfacherer Form.

  * Beim **vertiefenden Lernen** wählt der Algorithmus eine Aktion als Reaktion auf jeden Datenpunkt aus. Diese Vorgehensweise wird häufig in der Robotik angewendet. Dabei ist der Satz der Sensorenwerte zu einem bestimmten Zeitpunkt ein Datenpunkt, und der Algorithmus muss dann die nächste Aktion des Roboters auswählen. Diese Methode eignet sich auch für Anwendungen im Zusammenhang mit dem Internet der Dinge. Der Lernalgorithmus erhält außerdem kurz danach ein Erfolgssignal, das angibt, wie gut die Entscheidung war. Auf dieser Grundlage ändert der Algorithmus die Strategie zum Erreichen des bestmöglichen Ergebnisses. Derzeit bietet Azure ML keine Algorithmen zum vertiefenden Lernen.

* **Bayessche Methoden** machen Annahmen auf der Grundlage statistisch unabhängiger Datenpunkte. Dies bedeutet, dass die nicht modellierte Variabilität bei einem Datenpunkt nicht mit anderen in Zusammenhang gesetzt wird, d. h., es ist keine Vorhersage möglich. Wenn die aufgezeichneten Daten z. B. die Anzahl der Minuten bis zur Ankunft der nächsten U-Bahn ist, sind zwei Messungen im Abstand von einem Tag statistisch unabhängig voneinander. Allerdings sind zwei Messungen im Abstand von einer Minute nicht statistisch unabhängig – der eine Wert ist mithilfe des anderen Werts hochgradig vorhersagbar.

* Die **Boosted Decision Tree-Regression** nutzt die Vorteile der Funktionenüberlappung oder der Interaktion zwischen den Funktionen. Das bedeutet, dass an einen bestimmten Datenpunkt der Wert einer Funktion mithilfe des Werts einer anderen eingeschränkt vorhersagbar ist. Beispielsweise kann bei Daten zu den täglichen Tiefst- und Höchsttemperaturen mithilfe der Tiefsttemperatur für den Tag eine sinnvolle Prognose für die Höchsttemperatur getroffen werden. Die Informationen in den beiden Funktionen sind auf gewisse Weise redundant.

* Das Klassifizieren von Daten in mehr als zwei Kategorien kann entweder mithilfe einer inhärenten Multiklassen-Klassifizierung oder durch Kombination eines Satzes von Zweiklassen-Klassifizierungen in einem **Ensemble** durchgeführt werden. Bei der Vorgehensweise mithilfe eines Ensembles gibt es für jede Klasse eine separate Zweiklassen-Klassifizierung, die jeweils die Daten in zwei Kategorien unterteilen: "diese Klasse" und "nicht diese Klasse". Diese Klassifizierungen stimmen dann praktisch für die richtige Zuordnung des Datenpunkts ab. Dies ist das Prinzip hinter [One-vs-All Multiclass][one-vs-all-multiclass].

* Bei mehreren Methoden, einschließlich logistischer Regression und Bayes Point Machines, wird von **linearen Klassengrenzen** ausgegangen, d. h., dass die Grenzen zwischen den Klassen annähernd gerade Linien (oder Hyperebenen im allgemeineren Fall) sind. Oftmals kennen Sie Ihre Daten erst, nachdem Sie versucht haben, sie aufzuteilen, dies kann aber i. d. R. gut mit einer Visualisierung im Voraus erlernt werden. Wenn die Klassengrenzen sehr unregelmäßig sind, sollten Sie weiterhin Entscheidungsstrukturen, Decision Jungles, Support Vector Machines (SVM, Stützvektormethode) oder neuronale Netze verwenden.

* Sie können neuronale Netze mit kategorischen Variablen verwenden, indem Sie eine **Platzhaltervariable** für jede Kategorie erstellen und auf "1" festlegen, wenn die Kategorie gilt, bzw. auf "0", wenn sie nicht zutrifft.


<!-- This is how you can add a link to the image in HTML. Don't know how to do this in markdown.
<a href="http://download.microsoft.com/download/A/6/1/A613E11E-8F9C-424A-B99D-65344785C288/microsoft-machine-learning-algorithm-cheat-sheet.pdf">
<img src="C:\Users\garye\azure-content-pr\articles\media\machine-learning-algorithm-cheat-sheet\cheat-sheet-small.png">
</a>
-->

<!-- Module References -->
[initialize-model]: https://msdn.microsoft.com/library/azure/0c67013c-bfbc-428b-87f3-f552d8dd41f6/
[k-means-clustering]: https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/
[one-vs-all-multiclass]: https://msdn.microsoft.com/library/azure/7191efae-b4b1-4d03-a6f8-7205f87be664/
 

<!---HONumber=August15_HO6-->