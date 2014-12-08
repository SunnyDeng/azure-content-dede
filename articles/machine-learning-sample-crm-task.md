<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning-Beispiel: CRM-Aufgabe | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure Machine Learning-Beispiel: CRM-Aufgabe

>[AZURE.NOTE]
>Das [Beispielexperiment] und die [Beispieldatasets] im Zusammenhang mit diesem Modell sind in ML Studio verfügbar. Weitere Details erhalten Sie unten.
[Beispielexperiment]: #sample-experiment
[Beispieldatasets]: #sample-datasets

<!--
- [Problem Description]
- [Data]
- [Model]
- [Results]
- [API]
- [Sample Experiment]
-->
[Problembeschreibung]: #problem-description
[Daten]: #data
[Modell]: #model
[Ergebnisse]: #results
[API]: #api

## Problembeschreibung ##

Vorhersage von Kundenabwanderung (Anbieterwechsel), Upselling (Kauf von Upgrades oder Add-Ons) und Neigung zum Kauf eines neuen Produkts (Kaufwunsch) 

## Daten ##

Dataset mit 50.000 Kunden des französischen Telekommunikationsunternehmens Orange. Jeder Kundendatensatz besteht aus 230 anonymisierten Merkmalen. Diese Daten stammen aus dem KDD Cup 2009. 

Es handelt sich um numerische und Zeichenfolgenmerkmale. Die Merkmale sind sehr karg.
 
## Modell ##

Die einzige Vorverarbeitung in diesem Fall ist die Behandlung fehlender Werte. Wir haben "0" anstatt der fehlenden Werte in Zeichenfolgenmerkmalen eingefügt. Beinahe alle numerischen Merkmale haben nicht-negative Werte. Wir haben 1 zu den vorhandenen Werten der numerischen Merkmale addiert und fehlende Werte durch Nullwerte ersetzt. Auf diese Weise können wir unterscheiden zwischen tatsächlichen Nullwerten und solchen, die auf fehlende Werte hinweisen. Hierzu haben wir zunächst eine mathematische Operation (+1) auf die numerischen Merkmale ausgeführt. Anschließend haben wir alle fehlenden Werte durch 0 (bzw. "0" für Zeichenfolgen) ersetzt. 

Da wir mit numerischen und kategorischen Merkmalen arbeiten, verwenden wir einen Boosted Decision Tree-Klassifikator. Alle 3 Probleme sind unausgewogen, und positive Beispiele sind in der Minderheit. In manchen Fällen führt dies dazu, dass der Klassifikator Regionen im Merkmalsraum mit einer kleinen Anzahl positiver Proben ignoriert. Um dies zu testen, haben wir zwei Modelle für jedes Problem trainiert: Ein Modell verwendet die Rohdaten, und für das andere Modell wurden positive Beispiele repliziert, sodass die Anzahl der positiven Beispiele grob mit der Anzahl der negativen Testbeispiele übereinstimmt. Hierzu haben wir ein einfaches R-Skript verwendet, das positive und negative Beispiele aufteilt und anschließend 13 Kopien des positiven Ergebnissatzes an den negativen Ergebnissatz anhängt. 

## Ergebnisse ##

Die Ausgabe der Modelle enthält eine Bewertung für die Wahrscheinlichkeit eines positiven Ausgangs für jede der Aufgaben. Da wir für die Einordnung der Bezeichnungen als positiv einen beliebigen Schwellenwert verwenden können, messen wir die Leistung anhand der Fläche unterhalb der Grenzwertoptimierungskurve (Receiver Operating Characteristic ROC). Für die Fälle Kundenabwanderung und Upselling wurde die Leistung des Modells durch die Replikation der positiven Beispiele zur Erzeugung eines ausgeglicheneren Datensatzes leicht verbessert.  

<table border="1">
<tr><td>Codeänderung</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.711</td></tr>
<tr><td>replizierte positiven </td><td>0,728</td></tr>
</table>


<table border="0">
<tr><td>Upsell</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.853</td></tr>
<tr><td>replizierte positiven </td><td>0.865</td></tr>
</table>


<table border="0">
<tr><td>Appetenz</td><td>AUC</td></tr>
<tr style="background-color: #fff"><td>no replication</td><td>0.805</td></tr>
<tr><td>replizierte positiven </td><td>0.8</td></tr>
</table>

## API ##

Wir haben das Modell nicht operationalisiert, da es anonyme Merkmale enthält.



## Beispielexperiment

Das nachfolgende Beispielexperiment im Zusammenhang mit diesem Modellist in ML Studio im Abschnitt **EXPERIMENTE** auf der Registerkarte **BEISPIELE** verfügbar.

> **Beispielexperimente - CRM - Entwicklung**

## Beispieldatasets

Die folgenden Beispieldatasets, die von diesem Experiment verwendet werden, finden Sie in der Modulpalette in ML Studio unter **Gespeicherte Datasets**.

###CRM-Dataset gemeinsam genutzt
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]

###CRM Appetency Bezeichnungen freigegeben
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]

###CRM Codeänderung Bezeichnungen freigegeben
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]

###CRM Upselling Bezeichnungen freigegeben
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
