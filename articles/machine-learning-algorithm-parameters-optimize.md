<properties 
	pageTitle="Auswählen von Parametern zur Optimierung von Algorithmen in Azure Machine Learning | Azure" 
	description="Erläutert das Auswählen der optimalen Parametersätze für einen Algorithmus in Azure Machine Learning." 
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
	ms.date="02/14/2015" 
	ms.author="bradsev"/>


# Auswählen von Parametern zur Optimierung von Algorithmen in Azure Machine Learning

In diesem Thema wird das Auswählen des richtigen Hyperparametersatzes für einen Algorithmus in Azure Machine Learning beschrieben. Die meisten Algorithmen für maschinelles Lernen sind von verschiedenen Parametern abhängig. Wenn wir ein Modell trainieren, müssen wir Werte für diese Parameter bereitstellen. Die Wirksamkeit des trainierten Modells ist abhängig von den gewählten Parametern. Das Ermitteln des optimalen Parametersatzes wird als Modellauswahl bezeichnet. 

Es gibt verschiedene Möglichkeiten zum Durchführen der Modellauswahl. Beim maschinellen Lernen ist die Kreuzvalidierung eine der am häufigsten verwendeten Methoden für die Modellauswahl, und sie ist der Standardmechanismus für die Modellauswahl in Azure Machine Learning. Da sowohl R als auch Python Azure Machine Learning unterstützen, können Benutzer ihren eigenen Modellauswahlmechanismus in R oder Python implementieren. 

Das Ermitteln des besten Parametersatzes umfasst vier Schritte. 

1.	**Definieren des Parameterraums**: Für den Algorithmus entscheiden wir zunächst über die genauen Parameterwerte, die berücksichtigt werden sollen. 
2.	**Definieren der Einstellungen für die Kreuzvalidierung**: Für das DataSet müssen wir entscheiden, wie Teilmengen für die Kreuzvalidierung ausgewählt werden. 
3.	**Definieren der Kennzahl**: Anschließend entscheiden wir, welche Kennzahl zum Ermitteln des besten Parametersatzes verwendet werden soll, z. B. Genauigkeit, mittlere quadratische Abweichung, Genauigkeit, Ausfall oder f-Maß. 
4.	**Trainieren, Auswerten und Vergleichen**: Für jede eindeutige Kombination der Parameterwerte wird eine Kreuzvalidierung durchgeführt, und anhand der vom Benutzer definierten Fehlerkennzahl kann das Modell mit optimaler Leistung ausgewählt werden.

Das folgende Experiment zeigt, wie dies in Azure Machine Learning erzielt werden kann. 

![image1](./media/machine-learning-algorithm-parameters-optimize/fig1.png)
 
## Definieren des Parameterraums
Der Parametersatz kann bei der Initialisierung des Modells definiert werden. Der Parameterbereich aller Algorithmen für maschinelles Lernen weist zwei Trainingsmodi auf - **Single Parameter** und **Parameter Range**. Wir müssen den Modus **Parameter Range** (Abb. 1) auswählen. Hiermit können mehrere Werte für jeden Parameter eingegeben werden: Es können durch Trennzeichen getrennte Werte in das Textfeld eingegeben werden. Alternativ kann **Use Range Builder** verwendet werden, um die Maximum- und Minimumpunkte des Rasters und die Gesamtzahl der zu generierenden Punkte zu definieren. Standardmäßig werden die Parameterwerte auf einer linearen Skala generiert. Wenn jedoch das Kontrollkästchen **Log Scale** aktiviert ist, werden die Werte in logarithmischer Skalierung generiert, d. h., das Verhältnis zwischen den benachbarten Punkten ist konstant anstelle von ihrer Differenz. Für ganzzahlige Parameter kann mit einem Bindestrich "-" ein Bereich definiert werden, z. B. bedeutet "1-10", dass alle ganzen Zahlen zwischen 1 und 10 (beide einschließlich) den Parametersatz bilden. Ein gemischter Modus wird ebenfalls unterstützt, z. B. "1-10, 20, 50". In diesem Fall werden neben der ganzen Zahlen 1 bis 10 auch 20 und 50 dem Parametersatz hinzugefügt. 
  
![image2](./media/machine-learning-algorithm-parameters-optimize/fig2.png)
![image3](./media/machine-learning-algorithm-parameters-optimize/fig3.png)

## Definition der Teilmengen für die Kreuzvalidierung
Das **Partition and Sample**-Modul kann verwendet werden, um die Daten nach dem Zufallsprinzip Teilmengen zuzuweisen. In der folgenden Abbildung sehen wir eine Beispielkonfiguration für das Modul, in dem wir 5 Teilmengen definieren und die Teilmengennummern nach dem Zufallsprinzip den Beispielinstanzen zuweisen. 

![image4](./media/machine-learning-algorithm-parameters-optimize/fig4.png)


## Definieren der Kennzahl
Das **Sweep Parameters**-Modul bietet Unterstützung für die empirische Auswahl des besten Parametersatzes für einen bestimmten Algorithmus und ein bestimmtes DataSet. Der Eigenschaftenbereich dieses Moduls enthält, zusätzlich zu anderen Informationen zum Trainieren des Modells, die zu verwendende Kennzahl für die Ermittlung des besten Parametersatzes. Er verfügt über zwei verschiedene Dropdownlisten für Klassifizierungs- und Regressionsalgorithmen. Wenn der untersuchte Algorithmus ein Klassifizierungsalgorithmus ist, wird die Regressionskennzahl ignoriert und umgekehrt. In diesem speziellen Beispiel wählten wir **Accuracy** als Kennzahl.   
 
![image5](./media/machine-learning-algorithm-parameters-optimize/fig5.png)

## Trainieren, Evaluieren und Vergleichen:  
Ein und dasselbe Modul **Sweep Parameters** trainiert alle Modelle, die dem Parametersatz entsprechen, wertet verschiedene Kennzahlen aus und gibt dann das am besten trainierte Modell anhand der vom Benutzer ausgewählten Kennzahl aus. Dieses Modul hat zwei obligatorische Eingaben:

* den untrainierten Lernenden 
* das DataSet 

sowie eine optionale DataSet-Eingabe. Wir verknüpfen das DataSet mit Teilmengeninformationen mit der obligatorischen DataSet-Eingabe. Wenn dem DataSet keine Informationen für die Teilmenge zugewiesen ist, wird standardmäßig automatisch eine 10-fache Kreuzvalidierung ausgeführt. Wenn die Zuordnung für die Teilmenge nicht erfolgt und ein DataSet für die Überprüfung am optionalen DataSet-Port bereitgestellt wird, dann wird ein Trainings-Test-Modus ausgewählt, und mit dem ersten DataSet wird das Modell für jede Parameterkombination trainiert. Anschließend wird es für das Validierungs-DataSet ausgewertet. Der linke Ausgabeport des Moduls weist verschiedene Kennzahlen als Funktion der Parameterwerte auf. Der rechte Ausgabeport stellt das trainierte Modell anhand des Modells mit der besten Leistung gemäß der vom Benutzer ausgewählten Kennzahl (in diesem Fall Genauigkeit) bereit.  

![image6](./media/machine-learning-algorithm-parameters-optimize/fig6a.png)
![image7](./media/machine-learning-algorithm-parameters-optimize/fig6b.png)
 
Wir sehen die genauen gewählten Parameter in einer Visualisierung des rechten Ausgabeports. Dieses Modell kann nach dem Speichern als trainiertes Modell für die Bewertung von Testsätzen oder in einem operationalisierten Webdienst verwendet werden. 

<!--HONumber=49-->