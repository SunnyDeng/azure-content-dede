<properties 
	pageTitle="Was ist der Cortana-Analyseprozess (CAP)? | Microsoft Azure" 
	description="Der Cortana-Analyseprozess ist eine systematische Datenwissenschaftsmethode zum Entwickeln intelligenter Anwendungen, die erweiterte Analysen nutzen." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/05/2015" 
	ms.author="bradsev;gopitk" />


# Was ist der Cortana-Analyseprozess (CAP)?

Der Cortana-Analyseprozess (CAP) ist eine systematische Datenwissenschaftsmethode, die eine Folge von Schritten beschreibt, bei denen auf erweiterte Analysen zurückgegriffen wird, um intelligente Anwendungen zu entwickeln. Die CAP-Schritte bieten einen **Leitfaden** zum Definieren des Problems, Analysieren relevanter Daten, Erstellen und Auswerten von Vorhersagemodellen und anschließenden Bereitstellen dieser Modelle in intelligenten Anwendungen.

Es folgen die Schritte im **Cortana-Analyseprozess**:

![CAP-Workflow](./media/machine-learning-data-science-the-cortana-analytics-process/CAP-workflow.png)

Der Prozess ist **iterativ**: das Verständnis neuer und vorhandener Optimierungen im Modell entwickelt sich weiter und erfordert das Überarbeiten von zuvor in der Folge abgeschlossenen Schritten. Vorhandene Organisationsentwicklungs- und Projektplanungsprozesse können **einfach** an die im CAP definierte Schrittfolge angepasst werden.

Die Schritte in diesem Prozess werden nachstehend beschrieben.

## Vorbereitungsschritte 

## P1. Planen des Analyseprojekts 

Starten Sie ein Analyseprojekt, indem Sie geschäftliche Ziele und Probleme bestimmen, die als **Geschäftsanforderungen** ausgedrückt werden. Ein zentrales Ziel dieses Schritts ist das Bestimmen der wesentlichen geschäftlichen Variablen (z. B. Umsatzprognose oder Wahrscheinlichkeit eines betrügerischen Auftrags), die die Analyse vorherzusagen hat, um diese Anforderungen zu erfüllen. Zusätzliche Planung ist dann in der Regel wichtig, um sich einen Überblick über die **Datenquellen** zu verschaffen, die benötigt werden, um die Ziel des Projekts aus Analysesicht zu erreichen. Es ist z. B. nicht ungewöhnlich festzustellen, dass vorhandene Systeme zusätzliche Arten von Daten sammeln und protokollieren müssen, um das Problem anzugehen und die Projektziele zu erreichen.

## P2. Einrichten der Analyse-Umgebung 

Eine Analyse-Umgebung für den Cortana-Analyseprozess umfasst mehrere Komponenten:

- **Datenarbeitsbereiche**, in denen die Daten für die Analyse und Modellierung bereitgestellt werden 
- eine **Verarbeitungsinfrastruktur** für die Vorverarbeitung, Untersuchung und Modellierung von Daten
- eine **Laufzeitinfrastruktur** zum Operationalisieren der Analysemodelle und Ausführen intelligenter Clientanwendungen, die die Modelle nutzen.  

Die einzurichtende Analyse-Infrastruktur ist häufig Teil einer Umgebung, die von den Hauptsystemen getrennt ist. Doch in der Regel nutzt sie Daten aus mehreren Systemen innerhalb des Unternehmens sowie aus Quellen außerhalb des Unternehmens. Die Analyse-Infrastruktur kann rein cloudbasiert, lokal oder eine Kombination aus beidem sein.

## Analyseschritte:  

## 1\. Erfassen von Daten in der Analyse-Umgebung 

Der erste Schritt ist das Erfassen der relevanten Daten aus verschiedenen Quellen, entweder innerhalb oder außerhalb des Unternehmens, in den Analyse-Umgebungen, in denen die Daten verarbeitet werden sollen. Das **Format** der Daten in der Quelle kann sich von dem vom Ziel geforderten Format unterscheiden. Daher müssen möglicherweise verschiedene Datentransformationen mithilfe der Erfassungstools erfolgen.

Zusätzlich zur ersten Erfassung von Daten sind viele intelligente Anwendungen erforderlich, um die Daten im Rahmen eines laufenden Lernprozesses zu aktualisieren. Dies kann durch Einrichten einer **Datenpipeline** oder eines Workflows erfolgen. Das gehört zum iterativen Teil des Prozesses, der das Neuerstellen und Neuauswerten der Analysemodelle umfasst, die von der intelligenten Anwendung verwendet werden, die die Lösung bereitstellt.


## 2\. Untersuchen und Vorverarbeiten von Daten 

Der nächste Schritte besteht darin, sich ein umfassenderes Verständnis der Daten zu verschaffen, indem **zusammenfassende Statistiken** und Beziehungen untersucht und Techniken wie die **Visualisierung** genutzt werden. In dieser Phase werden auch Probleme mit der **Datenqualität** und Integrität, wie z. B. fehlende Werte, Datentypenkonflikte und inkonsistente Datenbeziehungen, behandelt. Vorverarbeitungstransformationen erfolgen, um die Rohdaten zu bereinigen, bevor weitere Analysen und Modellierungen stattfinden.


## 3\. Entwickeln von Features 

Datenwissenschaftler muss in Zusammenarbeit mit Fachbereichsexperten die Features bestimmen, die die wesentlichen Eigenschaften des Datasets erfassen und am besten zum Vorhersagen der wichtigsten Geschäftsvariablen genutzt werden können, die während der Planung ausgemacht wurden. Diese neuen Features können von vorhandenen Daten abgeleitet werden oder ggf. das Erfassen zusätzlicher Daten erfordern. Dieser Prozess wird als **Feature-Engineering** bezeichnet und ist einer der wichtigsten Schritte beim Entwickeln eines effektiven Predictive Analytics-Systems. Dieser Schritt erfordert eine kreative Kombination von Sachkenntnis mit den im Datenuntersuchungsschritt gewonnenen Erkenntnissen.


## 4\. Erstellen von Vorhersagemodellen 

Datenwissenschaftler erstellen Analysemodelle zum Vorhersagen der Hauptvariablen, die von den Geschäftsanforderungen bestimmt werden. Diese werden wiederum im Planungsschritt anhand von Daten definiert, die bereinigt und Features zugeordnet wurden. Machine Learning-Systeme unterstützen mehrere **Modellierungsalgorithmen**, die für eine Vielzahl von Fällen geeignet sind.

Datenwissenschaftler müssen das am besten geeignete Modell für die jeweilige Vorhersageaufgabe wählen. Es ist nicht ungewöhnlich, dass Ergebnisse aus mehreren Modellen kombiniert werden müssen, um die besten Ergebnisse zu erzielen. Die Eingabedaten für die Modellierung sind in der Regel nach dem Zufallsprinzip in drei Teile unterteilt:

- ein Dataset für das Training 
- ein Dataset für die Überprüfung 
- ein Datasets für Tests 

Die Modelle werden mithilfe des **Datasets für das Training** erstellt. Die optimale Kombination von Modellen (mit optimierten Parametern) wird ausgewählt, indem die Modelle ausgeführt und die Vorhersagefehler für das **Dataset für die Überprüfung** gemessen werden. Schließlich wird das **Dataset für Tests** verwendet, um die Leistung des ausgewählten Modells für unabhängige Daten auswerten, die nicht zum Trainieren oder Überprüfen des Modells verwendet wurden.


## 5\. Bereitstellen und Nutzen von Modellen 

Sobald wir eine Gruppe von Modellen mit adäquater Leistung haben, können diese von anderen Anwendungen **operationalisiert** und genutzt werden. Abhängig von den Geschäftsanforderungen erfolgen Vorhersagen entweder in **Echtzeit** oder auf **Batchbasis**. Um operationalisiert zu werden, müssen die Modelle mit einer **offenen API-Schnittstelle** verfügbar gemacht werden, die von verschiedenen Anwendungen einfach genutzt werden kann, wie z. B. von Onlinewebsites, Kalkulationstabellen, Dashboards bzw. Branchen- oder Back-End-Anwendungen.

## Zusammenfassung und nächste Schritte

Der Cortana-Analyseprozess wird als eine Folge von zu durchlaufenden Schritten modelliert. Diese **bieten einen Leitfaden** für die Aufgaben, die zum Nutzen erweiterter Analysen für das Entwickeln intelligenter Anwendungen ausgeführt werden müssen. Jeder Schritt enthält Details zur Verwendung verschiedener Microsoft-Technologien zum Erledigen der beschriebenen Aufgaben.

Wenngleich der CAP nicht bestimmte Arten von **Dokumentationsinhalten** vorschreibt, es ist eine bewährte Methode, die Ergebnisse der Datenuntersuchung, Modellierung und Auswertung zu dokumentieren und den dazugehörigen Code zu speichern, damit die Analyse bei Bedarf wiederholt kann. Dies ermöglicht außerdem die Wiederverwendung der Analyseergebnisse bei der Arbeit mit anderen Anwendungen mit ähnlichen Daten und Vorhersageaufgaben.

Vollständige exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind ebenfalls verfügbar.

 

<!---HONumber=Oct15_HO3-->