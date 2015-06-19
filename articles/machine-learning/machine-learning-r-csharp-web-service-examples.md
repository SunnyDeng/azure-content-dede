<properties 
	pageTitle="Beispiele für Webdienste, die mit R auf Azure ML erstellt und auf Marketplace veröffentlicht wurden | Azure" 
	description="Beispiele für Webdienste, die mit R auf Azure ML und Marketplace veröffentlicht wurden"
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
	ms.date="02/11/2015" 
	ms.author="jaymathe"/> 


#Beispiele für Webdienste, die mit R auf Azure ML und Marketplace veröffentlicht wurden




Benutzer können mit Azure Machine Learning Studio R Code schreiben und mit wenigen Mausklicks einer Schaltfläche als Webdienst veröffentlichen, um dann von anderen Personen und Geräten auf der ganzen Welt genutzt zu werden. Von einfachen Rechner, die zum Erstellen von statistischen Funktionalitäten verwendet werden bis hin beispielsweise zum Erstellen von benutzerdefinierten Stimmungsanalysen können sowohl neue wie auch erfahrene R-Nutzer von der Einfachheit profitieren, mit der Azure ML R-Codes durchsetzen kann. Während diese Webdienste von Benutzern möglicherweise über eine mobile Anwendung oder eine Website verwendet werden, ist der Zweck dieser Webdienste, als Beispiel dafür genutzt zu werden, wie Azure ML R-Skripts für analytische Zwecke operationalisieren kann und zum Erstellen von Webdiensten zusätzlich zum R-Code verwenden kann.

Auf dieser Seite finden eine breite Palette von Beispiel-Webdiensten, die mithilfe von Azure ML erstellt und auf dem Microsoft Azure Marketplace veröffentlicht wurden. Jeder Webdienst hat ein umfassendes Dokument angefügt, das Beispieldatensätzen zum Testen des Dienstes eingebettet hat und erklärt, wie der Benutzer ähnliche Dienste selber erstellen kann. 

![Workflow][1]

Stellen Sie sich die folgenden Szenarien vor:

####Szenario 1: Generisches Modell 
Ein Benutzer arbeitet mit einem generischen Modell, das basierend auf neuen Benutzerdaten, wie z. B. Zeitdaten oder einer benutzerdefinierten R-Methode mit erweiterten Analysen angewendet werden kann. Dieser Benutzer veröffentlicht das Modell als Webdienst für andere Benutzer mit ihren Daten.

* [Binäre Klassifizierung](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binary-classifier/)
* [Cluster-Modell](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-cluster-model/)
* [Multivariate lineare Regression](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-multivariate-linear-regression/)
* [Prognose mit exponentieller Glättung](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-forecasting-exponential-smoothing/)
* [ETS + STL-Planung](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-retail-demand-forecasting/)
* [Prognose-Autoregressiver, integrierter gleitender Durchschnitt (ARIMA)](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-arima/)
* [Lebensdaueranalysen](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-survival-analysis/)

####Szenario 2: Trainiertes Modell - Spezifische Daten 
Ein Benutzer hat die Daten, die nützliche Vorhersagen über R-Code bereitstellen, wie z. B. ein ausführliches Beispiel von Persönlichkeits- Fragebögen, die über einen K-Means-Algorithmus den Persönlichkeitstyp oder Gesundheitsdaten voraussagen und die dazu verwendet werden können, individuelle Risiken für Lungenkrebs mittels einem Überlebens-Analyse-R-Paket vorherzuzsagen. Der Benutzer veröffentlicht die Daten über einen Webdienst, der das Ergebnis des neuen Benutzers vorhersagt.

####Szenario 3: Trainiertes Modell - Generische Daten 
Ein Benutzer hat die generischen Daten (z. B. einen Text-Körper), die es einem Webdienst ermöglichen, generische Daten von verschiedenen Arten von Anwendungsfällen und Szenarien zu verwenden.

* [Lexikonbasierte Stimmungsanalyse](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-lexicon-based-sentiment-analysis/)

####Szenario 4: Erweiterter Rechner 
Ein Benutzer bietet erweiterte Berechnungen oder Simulationen an, die keine trainierten Modelle oder Anpassungen eines Modells an die Daten des Benutzers erfordern.

* [Unterschied im Anteilstest](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-difference-in-two-proportions/)
* [Normal Distribution Suite](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-normal-distribution/)
* [Binomial Distribution Suite](http://azure.microsoft.com/documentation/articles/machine-learning-r-csharp-binomial-distribution/)

##Häufig gestellte Fragen
Häufig gestellte Fragen zum Gebrauch des Webdienstes oder zum Veröffentlichen in Marketplace finden Sie [hier](http://azure.microsoft.com/documentation/articles/machine-learning-marketplace-faq).

[1]: ./media/machine-learning-r-csharp-web-service-examples/base1.png



<!--HONumber=46--> 
 