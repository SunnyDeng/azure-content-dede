<properties title="Azure Machine Learning Sample: CRM task" pageTitle="Machine Learning Sample: CRM task | Azure" description="A sample Azure Machine Learning experiment to develop multiple models that predict customer churn, upsell, and propensity to buy a new product." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Azure Machine Learning-Beispiel: CRM-Aufgabe

*Sie finden das Beispielexperiment für dieses Modell in ML Studio im Bereich **EXPERIMENTE** unter der Registerkarte **BEISPIELE**. Der Name des Experiments lautet:*

	Sample Experiments - CRM - Development

## Problembeschreibung
Vorhersage von Kundenabwanderung (Anbieterwechsel), Upselling (Kauf von Upgrades oder Add-Ons) und Neigung zum Kauf eines neuen Produkts (Kaufwunsch)
## Daten
Dataset mit 50.000 Kunden des französischen Telekommunikationsunternehmens Orange. Jeder Kundendatensatz besteht aus 230 anonymisierten Merkmalen. Diese Daten stammen aus dem KDD Cup 2009.

Es handelt sich um numerische und Zeichenfolgenmerkmale. Die Merkmale sind sehr karg.
## Modell
Die einzige Vorverarbeitung in diesem Fall ist die Behandlung fehlender Werte. Wir haben "0" anstatt der fehlenden Werte in Zeichenfolgenmerkmalen eingefügt. Beinahe alle numerischen Merkmale haben nicht-negative Werte. Wir haben 1 zu den vorhandenen Werten der numerischen Merkmale addiert und fehlende Werte durch Nullwerte ersetzt. Auf diese Weise können wir unterscheiden zwischen tatsächlichen Nullwerten und solchen, die auf fehlende Werte hinweisen. Hierzu haben wir zunächst eine mathematische Operation (+1) auf die numerischen Merkmale ausgeführt. Anschließend haben wir alle fehlenden Werte durch 0 (bzw. "0" für Zeichenfolgen) ersetzt.

Da wir mit numerischen und kategorischen Merkmalen arbeiten, verwenden wir einen Boosted Decision Tree-Klassifikator. Alle 3 Probleme sind unausgewogen, und positive Beispiele sind in der Minderheit. In manchen Fällen führt dies dazu, dass der Klassifikator Regionen im Merkmalsraum mit einer kleinen Anzahl positiver Proben ignoriert. Um dies zu testen, haben wir zwei Modelle für jedes Problem trainiert: Ein Modell verwendet die Rohdaten, und für das andere Modell wurden positive Beispiele repliziert, sodass die Anzahl der positiven Beispiele grob mit der Anzahl der negativen Testbeispiele übereinstimmt. Hierzu haben wir ein einfaches R-Skript verwendet, das positive und negative Beispiele aufteilt und anschließend 13 Kopien des positiven Ergebnissatzes an den negativen Ergebnissatz anhängt.
## Ergebnisse
Die Ausgabe der Modelle enthält eine Bewertung für die Wahrscheinlichkeit eines positiven Ausgangs für jede der Aufgaben. Da wir für die Einordnung der Bezeichnungen als positiv einen beliebigen Schwellenwert verwenden können, messen wir die Leistung anhand der Fläche unterhalb der Grenzwertoptimierungskurve (Receiver Operating Characteristic ROC). Für die Fälle Kundenabwanderung und Upselling wurde die Leistung des Modells durch die Replikation der positiven Beispiele zur Erzeugung eines ausgeglicheneren Datensatzes leicht verbessert.

<table border="1">
<tr><td>Abwanderung</td><td>Fläche unterhalb der Kurve</td></tr>
<tr style="background-color: #fff"><td>Keine Replikation</td><td>0,711</td></tr>
<tr><td>Positivbeispiele repliziert</td><td>0,728</td></tr>
</table>


<table border="0">
<tr><td>Upselling</td><td>Fläche unterhalb der Kurve</td></tr>
<tr style="background-color: #fff"><td>Keine Replikation</td><td>0,853</td></tr>
<tr><td>Positivbeispiele repliziert</td><td>0.865</td></tr>
</table>


<table border="0">
<tr><td>Kaufwunsch</td><td>Fläche unterhalb der Kurve</td></tr>
<tr style="background-color: #fff"><td>Keine Replikation</td><td>0.805</td></tr>
<tr><td>Positivbeispiele repliziert</td><td>0.8</td></tr>
</table>

## API
Wir haben das Modell nicht operationalisiert, da es anonyme Merkmale enthält.

