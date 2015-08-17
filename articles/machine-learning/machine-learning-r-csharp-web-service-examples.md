<properties 
	pageTitle="Beispiele für mit R entwickelte Machine Learning-Webdienste | Microsoft Azure" 
	description="Hier finden Sie einige nützliche Beispiele für Machine Learning-Webdienste, die mit R-Code erstellt und im Azure Marketplace veröffentlicht wurden." 
	services="machine-learning" 
	documentationCenter="" 
	authors="jaymathe" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="jaymathe"/>


#Beispiele für Webdienste mit R-Code, die Azure Machine Learning erstellt und im Microsoft Azure Marketplace veröffentlicht wurden

Die Beispielwebdienste in diesem Artikel wurden mit Azure Machine Learning erstellt und im Azure Marketplace veröffentlicht. Jedem Webdienstbeispiel ist ein umfassendes Dokument angefügt, in das Beispieldatasets zum Testen des Diensts integriert sind und in dem erläutert wird, wie Benutzer ähnliche Dienste selber erstellen können.

Benutzer können in Azure Machine Learning Studio R-Code schreiben und diesen mit nur wenigen Mausklicks als Webdienst für Anwendungen und Geräte veröffentlichen, der auf der ganzen Welt genutzt werden kann.


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Unabhängig davon, ob ein einfacher Rechner mit statistischen Funktionen oder ein auf Text Mining basierender Stimmungsanalysenprädiktor erstellt werden soll – sowohl neue als auch erfahrene R-Programmierer können von der einfachen Operationalisierung von R-Code in Azure Machine Learning profitieren. Diese Webdienste können von Benutzern verwendet werden – beispielsweise direkt im Marketplace, über eine mobile App, eine Website oder sogar über einen lokalen Computer. Hauptsächlich sollen sie jedoch zeigen, wie Machine Learning R-Skripts für Analysezwecke operationalisieren und zum Erstellen von Webdiensten basierend auf R-Code verwendet werden kann.

Jedes Beispiel enthält ein C#-Beispiel zur Webdienstnutzung.


![Diagramm des R-Codes in Azure Machine Learning: R-Lösungen für die eigene Nutzung oder zur Veröffentlichung im Azure Marketplace.][1]

Betrachten Sie folgende Szenarien:

##Szenario 1: Generisches Modell 
Ein Benutzer arbeitet mit einem generischen Modell, das basierend auf neuen Benutzerdaten, wie z. B. Zeitdaten oder einer benutzerdefinierten R-Methode mit erweiterten Analysen angewendet werden kann. Dieser Benutzer veröffentlicht das Modell als Webdienst für andere Benutzer mit ihren Daten.



* [Binäre Klassifizierung](machine-learning-r-csharp-binary-classifier.md)
* [Clustermodell](machine-learning-r-csharp-cluster-model.md)
* [Multivariate lineare Regression](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Prognose mit exponentieller Glättung](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [Prognose – ETS und STL](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Prognose – ARIMA (Autoregressive Integrated Moving Average, autoregressiver integrierter gleitender Mittelwert)](machine-learning-r-csharp-arima.md)
* [Lebensdaueranalysen](machine-learning-r-csharp-survival-analysis.md)


##Szenario 2: Trainiertes Modell – spezifische Daten 
Ein Benutzer könnte beispielsweise über Daten verfügen, die mithilfe von R-Code nützliche Vorhersagen liefern, wie z. B. eine umfangreiche Stichprobe von Fragebögen mit Angaben zur Persönlichkeit, die über einen K-Means-Algorithmus gruppiert werden, um Persönlichkeitstyp vorherzusagen. Oder es könnte sich um Daten einer Umfrage zum Thema Gesundheit handeln, mit deren Hilfe das Lungenkrebsrisiko einer Person mit einem R-Paket für die Lebensdaueranalyse vorhergesagt wird. Der Benutzer veröffentlicht die Daten über einen Webdienst, der das Ergebnis des neuen Benutzers vorhersagt.

##Szenario 3: Trainiertes Modell – generische Daten 
Ein Benutzer verfügt über generische Daten (z. B. einen Textkorpus), mit denen ein Webdienst entwickelt und generisch auf verschiedene Arten von Anwendungsfällen und Szenarien angewendet werden kann.

* [Lexikonbasierte Stimmungsanalyse](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##Szenario 4: Erweiterter Rechner 
Ein Benutzer bietet erweiterte Berechnungen oder Simulationen an, die keine trainierten Modelle oder Anpassungen eines Modells an die Daten des Benutzers erfordern.

* [Testen des Unterschieds zwischen Anteilen](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Normal Distribution Suite](machine-learning-r-csharp-normal-distribution.md)
* [Binomial Distribution Suite](machine-learning-r-csharp-binomial-distribution.md)

##Häufig gestellte Fragen
Häufig gestellte Fragen zur Nutzung des Webdiensts und zum Veröffentlichen im Marketplace finden Sie [hier](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 

<!---HONumber=August15_HO6-->