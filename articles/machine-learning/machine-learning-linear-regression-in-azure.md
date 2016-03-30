<properties 
	pageTitle="Verwenden der linearen Regression in Machine Learning | Microsoft Azure" 
	description="Ein Vergleich von Modellen zur linearen Regression in Excel und in Azure Machine Learning Studio" 
	metaKeywords="" 
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="kbaroni;garye" />

# Verwenden der linearen Regression in Azure Machine Learning

> *Kate Baroni* und *Ben Boatman* sind Enterprise Solution Architects im Microsoft Data Insights Center of Excellence. In diesem Artikel beschreiben die beiden ihre Erfahrungen beim Migrieren einer vorhandenen Regressionsanalysesuite zu einer cloudbasierten Lösung mit Azure Machine Learning.
 
&nbsp;
  
[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## Zielsetzung

Unser Projekt begann zunächst mit zwei Zielsetzungen:

1. Durch den Einsatz von Vorhersageanalysen die Genauigkeit der Umsatzprognosen unserer Organisation zu verbessern  
2. Mithilfe von Azure ML eine Bestätigung, Optimierung, Beschleunigung und Skalierung unserer Ergebnisse herbeizuführen.  

Wie viele andere Unternehmen durchläuft auch unsere Organisation einen monatlichen Prozess zur Erlösprognose. Unser kleines Analystenteam wurde beauftragt, diesen Prozess mithilfe von Machine Learning zu unterstützen und die Prognosegenauigkeit zu verbessern. Das Team sammelte im Verlauf einiger Monate Daten aus mehreren Quellen und ermittelte in dieser Zeit mit statistischen Hilfsmitteln die wichtigsten Attribute für Vertriebsprognosen im Bereich Services. Im nächsten Schritt wurden verschiedene Prototypen für die Regressionsmodelle in Excel erstellt und mit den vorhandenen Daten getestet. Innerhalb einiger Wochen hatten wir ein Regressionsmodell in Excel erstellt, das besser war als unsere derzeitigen Prognoseverfahren aus dem Finanzbereich. Dieses Modell wurde unser Prognose-Benchmark.


Weiter ging es mit dem Versuch, unsere Vorhersageanalyse in Azure ML zu implementieren, um herauszufinden, wie Azure ML die Vorhersageleistung weiter verbessern würde.


## Erreichen eines Leistungsgleichstands

Unsere oberste Priorität bestand darin, ein zu unserem Excel-Regressionsmodell gleichwertiges Modell in Azure ML zu schaffen. Wir wollten mit exakt denselben Daten und derselben Aufteilung von Trainings- und Testdaten einen Gleichstand der Vorhersageleistung bei Excel und Azure ML herstellen. Zunächst scheiterten wir dabei. Das Excel-Modell übertraf das Azure-ML-Modell. Grund für das Scheitern waren unsere fehlenden Kenntnisse über die Basiseinstellung in Azure ML. Nachdem wir uns mit dem Azure ML-Produktteam kurzgeschlossen hatten, erhielten wir ein besseres Verständnis der erforderlichen Basiseinstellung für unsere DataSets und erzielten schließlich einen Leistungsgleichstand der beiden Modelle.

### Erstellen eines Regressionsmodells in Excel
Unsere Excel-Regression verwendete das Standardmodell für lineare Regression aus dem Excel-Analysis-ToolPak.

Wir berechneten den *Mean Absolute % Error* und verwendeten diesen Wert als Leistungsmaß für unser Modell. Die Entwicklung des funktionierenden Excel-Modells dauerte 3 Monate. Viele Aspekte des Lernmodells konnten wir für das Azure ML-Experiment übernehmen, was letztlich vorteilhaft für unser Verständnis der Anforderungen war.

### Erstellen eines vergleichbaren Experiments in Azure Machine Learning  
Das Erstellen unseres Experiments in Azure ML verlief in folgenden Schritten:

1.	Hochladen des DataSets als CSV-Datei in Azure ML (sehr kleine Datei)
2.	Erstellen eines neuen Experiments und Verwenden des [Project Columns][project-columns]-Moduls, um dieselben Datenfunktionen wie in Excel auszuwählen.   
3.	Verwenden des [Split Data][split]-Moduls (im Modus *Relative Expression*), um die Daten in ebenso große Trainingssätze wie in Excel aufzugliedern  
4.	Experimentieren mit dem [Linear Regression][linear-regression]-Modul (nur Standardoptionen), Dokumentieren und Vergleichen mit den Ergebnissen unseres Excel-Regressionsmodells

### Beurteilung erster Ergebnisse
Zunächst war das Excel-Modell dem Azure-ML-Modell deutlich überlegen.

| |Excel|Azure ML|
|---|:---:|:---:|
|Leistung| | |
|<ul style="list-style-type: none;"><li>Adjusted R Square</li></ul>| 0,96 |–|
|<ul style="list-style-type: none;"><li>Coefficient of <br />Determination</li></ul>|–|	0,78<br />(geringe Genauigkeit)|
|Mittlerer absoluter Fehler |	9,5 Mio. $|	19,4 Mio. $|
|Mean Absolute Error (%)|	6,03 %|	12,2 %

Als wir unser Verfahren und die Ergebnisse den Entwicklern und Data Scientists des Azure ML-Teams zeigten, konnte man uns dort ein paar rasche Tipps geben.

* Bei Verwendung des [Linear Regression][linear-regression]-Moduls in Azure ML werden zwei Methoden bereitgestellt:
	*  Online Gradient Descent: erscheint eher geeignet für umfangreichere Problemstellungen
	*  Ordinary Least Squares: Diese Methode wird allgemein am häufigsten mit dem Stichwort lineare Regression assoziiert. Für kleine DataSets ist die "Ordinary Least Squares"-Methode möglicherweise besser geeignet.
*  Denken Sie an die Möglichkeit, den "L2 Regularization Weight"-Parameter anzupassen, um die Leistung zu verbessern. Standardmäßig ist er auf 0,001 eingestellt, aber für unser kleines DataSet haben wir durch Ändern des Werts auf 0,005 eine bessere Leistung erzielt.    

### Rätsel gelöst!
Als wir die Empfehlungen umgesetzt hatten, konnten wir in Azure ML denselben Benchmark erzielen wie mit Excel.

|| Excel|Azure ML (Start)|Azure ML mit der Methode der kleinsten Quadrate|
|---|:---:|:---:|:---:|
|Bezeichneter Wert |Ist-Werte (numerisch)|identisch|identisch|
|Lernender |Excel -> Data Analysis -> Regression|Lineare Regression|Linear Regression|
|Lernmoduloptionen|N/V|Standardeinstellungen|Methode der kleinsten Quadrate<br />L2 = 0,005|
|Dataset|26 Zeilen, 3 Funktionen, 1 Bezeichnung Alle numerisch|identisch|identisch|
|Split: trainieren|Excel mithilfe der ersten 18 Zeilen trainiert, mit den letzten 8 Zeilen getestet|identisch|identisch|
|Split: Test|Excels Regressionsformel auf die letzten 8 Zeilen angewendet|identisch|identisch|
|**Leistung**||||
|Adjusted R Square|0,96|–||
|Coefficient of Determination|–|0,78|0,952049|
|Mittlerer absoluter Fehler |9,5 Mio. $|19,4 Mio. $|9,5 Mio. $|
|Mean Absolute Error (%)|<span style="background-color: 00FF00;"> 6,03%</span>|12,2 %|<span style="background-color: 00FF00;"> 6,03%</span>|

Zudem gab es eine gute Übereinstimmung der Excel-Koeffizienten mit den Funktionsgewichten im trainierten Azure-Modell.

||Excel-Koeffizienten|Stärken der Azure-Funktion|
|---|:---:|:---:|
|Schnittpunkt/Verschiebung|19470209,88|19328500|
|Funktion A|0,832653063|0,834156|
|Funktion B|11071967,08|11007300|
|Funktion C|25383318,09|25140800|

## Nächste Schritte

Wir wollten innerhalb von Excel einen Azure-Webdienst aufrufen. Unsere Analysten sind auf Excel angewiesen, daher war es wichtig, den Azure ML-Webdienst mit einer Zeile Excel-Daten aufrufen und den Prognosewert in Excel zurückliefern zu können.

Wir wollten unser Modell auch mithilfe der in Azure ML verfügbaren Optionen und Algorithmen optimieren.

### Integrieren in Excel
Unsere Lösung bestand darin, das Azure ML-Regressionsmodell in Form eines von uns aus dem Trainingsmodell erstellten Webdiensts in Betrieb zu nehmen. Der Webdienst war in wenigen Minuten fertiggestellt. Wir konnten ihn direkt in Excel aufrufen und bekamen einen Wert für die Erlösprognose zurück.

Der Abschnitt *Web Services-Dashboard* enthält eine Excel-Arbeitsmappe als Download. Die Arbeitsmappe ist bereits vorformatiert und enthält die Webdienst-API und die Schema-Informationen. Die Arbeitsmappe wird durch Klicken auf *Download Excel Workbook* geöffnet, und Sie können sie auf Ihrem lokalen Computer speichern.

![][1]
 
Kopieren Sie bei geöffnetem Arbeitsblatt Ihre vordefinierten Parameter in den blauen Parameterabschnitt, wie unten gezeigt. Sobald die Parameter eingegeben werden, ruft Excel den AzureML-Webdienst auf, und die prognostizierten Werte werden im grünen Abschnitt "Predicted Values" angezeigt. Das Arbeitsblatt erstellt anhand Ihres trainierten Modells fortlaufend Prognosen für alle unter Parameter eingegebenen Zeilenelemente. Weitere Informationen zum Verwenden dieser Funktion finden Sie unter [Verwenden eines Azure Machine Learning-Webdiensts aus Excel](machine-learning-consuming-from-excel.md).

![][2]
 
### Optimieren und weitere Untersuchungen
Nun, da wir mit dem Excel-Modell unseren Benchmark erreicht hatten, versuchten wir uns daran, unser Modell der linearen Regression in Azure ML zu optimieren. Wir verwendeten das Modul [Filter-Based Feature Selection][filter-based-feature-selection], um unsere Auswahl der anfänglichen Datenelemente zu verbessern, womit wir eine Leistungsverbesserung von 4,6 % (mittlerer absoluter Fehler) erreichen konnten. Für zukünftige Projekte werden wir diese Funktion verwenden, die uns möglicherweise wochenlanges Iterieren von Datenattributen zum Herausfinden des richtigen Funktionssatzes für das Modell erspart.

Als Nächstes planen wir die Einbeziehung zusätzlicher Algorithmen wie [Bayes][bayesian-linear-regression] oder [Boosted Decision Trees][boosted-decision-tree-regression] in unser Experiment, um deren Leistung vergleichen zu können.

Ein gutes DataSet für eigene Versuche mit Regressions-Modellen ist das Beispiel-DataSet "Energy Efficiency Regression" mit einer Vielzahl numerischer Attribute. Dieses DataSet wird als Teil der Beispiel-DataSets in ML Studio bereitgestellt. Zur Vorhersage der Kühl- bzw. Wärmelast lässt sich eine Vielzahl von Lernmodulen verwenden. Das nachstehende Diagramm zeigt einen Leistungsvergleich verschiedener Regressions-Lernmodelle im Vergleich zum Energieeffizienz-DataSet für die Zielvariable Kühllast.

|Modell|Mittlerer absoluter Fehler|Root Mean Squared Error|Relative Absolute Error|Relative Squared Error|Coefficient of Determination
|---|---|---|---|---|---
|Boosted Decision Tree|0,930113|1,4239|0,106647|0,021662|0,978338
|Linear Regression (Gradient Descent)|2,035693|2,98006|0,233414|0,094881|0,905119
|Regression mit neuronalen Netzwerken|1,548195|2,114617|0,177517|0,047774|0,952226
|Linear Regression (Ordinary Least Squares)|1,428273|1,984461|0,163767|0,042074|0,957926  

## Wesentliche Punkte 

Wir haben durch das parallele Weiterführen von Excel-Regressionen und Azure Machine Learning-Experimenten viel gelernt. Durch das Erstellen des Benchmark-Modells in Excel und anschließendes Vergleichen mit Modellen, die Azure ML [Linear Regression][linear-regression] verwenden, konnten wir Azure ML erlernen und Möglichkeiten entdecken, die Datenauswahl und die Modellleistung zu verbessern.

Wir haben auch festgestellt, dass es ratsam ist, [Filter-Based Feature Selection][filter-based-feature-selection] zu verwenden, um zukünftige Vorhersageprojekte zu beschleunigen. Durch Anwenden der Funktionsauswahl auf Ihre Daten lässt sich ein verbessertes Modell in Azure ML mit insgesamt besserer Leistung erstellen.

Die Möglichkeit, die Vorhersageanalytik für Prognosen aus Azure ML auch in Excel nutzen zu können, sorgt durch die Verfügbarkeit auf verschiedenen Systemen für einen erheblich breiteren Nutzerkreis im Unternehmen.


## Ressourcen
Im Folgenden finden Sie einige Ressourcen für Ihre Arbeit mit Regression:

* Regression in Excel. Falls Sie in Excel noch nicht mit Regressionstechniken gearbeitet haben, erleichtert folgende Anleitung den Einstieg: [http://www.excel-easy.com/examples/regression.html](http://www.excel-easy.com/examples/regression.html)
* Regression im Vergleich zu Vorhersagen. Tyler Chessman hat einen Blog-Artikel mit einer leicht verständlichen Beschreibung von linearer Regression für Anfänger verfasst, in dem er den Gebrauch von Zeitreihen zu Prognosezwecken erläutert. [http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts](http://sqlmag.com/sql-server-analysis-services/understanding-time-series-forecasting-concepts)  
* 	Ordinary Least Squares Linear Regression: Schwachstellen, Probleme und Stolperfallen. Eine Einführung und Erörterung der Regression: [http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/ ](http://www.clockbackward.com/2009/06/18/ordinary-least-squares-linear-regression-flaws-problems-and-pitfalls/)

[1]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-1.png
[2]: ./media/machine-learning-linear-regression-in-azure/machine-learning-linear-regression-in-azure-2.png


<!-- Module References -->
[bayesian-linear-regression]: https://msdn.microsoft.com/library/azure/ee12de50-2b34-4145-aec0-23e0485da308/
[boosted-decision-tree-regression]: https://msdn.microsoft.com/library/azure/0207d252-6c41-4c77-84c3-73bdf1ac5960/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 

<!------HONumber=AcomDC_0309_2016-->