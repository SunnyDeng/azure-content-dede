<properties 
	pageTitle="Machine Learning-App: Anomaly Detection Service | Microsoft Azure" 
	description="Die Anomaly Detection-API ist ein mit Microsoft Azure Machine Learning erstelltes Beispiel, das Anomalien in Zeitreihendaten erkennt, wenn die numerischen Daten zeitlich gleich verteilt sind." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm"
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/28/2015" 
	ms.author="luisca"/>


# Machine Learning Anomaly Detection Service#

##Übersicht

Die Anomaly Detection-API ist ein mit Azure Machine Learning erstelltes Beispiel, das Anomalien in Zeitreihendaten erkennt, wenn die numerischen Daten zeitlich gleich verteilt sind.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Dieser Dienst zur Erkennung von Anomalien erkennt die folgenden unterschiedlichen Typen von Anomalien in Zeitreihendaten:

1. Positive und negative Trends: Beim Überwachen der Speicherauslastung in der Computerverarbeitung weist ein Aufwärtstrend z. B. auf einen Speicherverlust hin.

2. Anstieg im dynamischen Wertebereich: Bei der Überwachung der durch einen Dienst ausgelösten Ausnahmen kann ein Anstieg im dynamischen Wertebereich z. B. auf eine Instabilität der Dienstintegrität hinweisen.

3. Spitzen und Abfälle: Beim Überwachen der Anzahl von fehlerhaften Anmeldeversuchen bei einem Dienst oder der Anzahl der Auscheckvorgänge in einer E-Commerce-Website könnten Spitzen oder Abfälle z. B. auf anormales Verhalten hinweisen.


Diese Erkennungsprogramme dienen zum Nachverfolgen von Änderungen an Werten im Zeitverlauf und melden laufende Änderungen in ihren Werten. Sie benötigen keine Ad-hoc-Schwellenwertanpassung, und ihre Bewertungen können verwendet werden, um falsch positive Raten zu steuern. Die Anomaly Detection-API ist nützlich in Szenarios wie Dienstüberwachung durch Nachverfolgen von KPIs über die Zeit, oder von Nutzungsdaten wie der Anzahl von Suchvorgängen, der Anzahl von Klicks, Leistungsindikatoren wie Arbeitsspeicher, CPU, Dateilesevorgängen usw. über die Zeit.

##API-Definition

Der Dienst stellt eine REST-basierte API über HTTPS bereit, die auf unterschiedliche Weise genutzt werden kann, beispielsweise durch eine Web- oder mobile Anwendung, T, Python, Excel usw. Wir bieten eine [Azure-Webanwendung](http://anomalydetection-aml.azurewebsites.net/), mit der Sie den Webdienst zur Erkennung von Anomalien für Ihre Daten ausführen und die Ergebnisse visualisieren können.

Sie können auch Zeitreihendaten über einen REST-API-Aufruf an diesen Dienst senden, dann wird eine Kombination der oben beschriebenen drei Anomaliearten ausgeführt. Der Dienst wird auf der Machine Learning-Plattform Azure ML ausgeführt, die nahtlos für Ihre geschäftlichen Anforderungen skaliert werden kann und SLAs von 99,95 % bietet.

Die folgende Abbildung zeigt ein Beispiel für erkannte Anomalien in einer Zeitreihe mit dem oben genannten Framework. Die Zeitreihe weist zwei unterschiedliche Pegeländerungen und drei Spitzen auf. Die roten Punkte zeigen die Zeit an, zu der die Pegeländerung erkannt wird, während die roten nach oben weisenden Pfeile die erkannten Spitzen anzeigen.


![][1]

##Eingabe

Die API akzeptiert zwei Eingabeparameter.

1. "data" stellt die eingegebene Zeitreihe im Format: t1=v1;t2=v2;... dar. 

 
	Beispieldaten:
		
		"9/21/2014 11:05:00 AM=3;9/21/2014 11:10:00 AM=9.09;9/21/2014 11:15:00 AM=0;"

2. "params" ist auf "SpikeDetector.TukeyThresh=3; SpikeDetector.ZscoreThresh=3" festgelegt und steuert somit die Empfindlichkeit der Spitzenerkennungsprogramme. Bei höheren Werten werden höhere Spitzen erkannt und umgekehrt.

	Beispiel-URL mit den oben genannten Eingabeparametern:

		https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v1/Score?data=%279%2F21%2F2014%2011%3A05%3A00%20AM%3D3%3B9%2F21%2F2014%2011%3A10%3A00%20AM%3D9.09%3B9%2F21%2F2014%2011%3A15%3A00%20AM%3D0%3B%27&params=%27SpikeDetector.TukeyThresh%3D3%3B%20SpikeDetector.ZscoreThresh%3D3%27



###Ausgabe###

Die API führt diese Erkennungsprogramme für Zeitreihendaten aus und gibt die Anomaliebewertungen für jeden Zeitpunkt zurück. Diese Ausgabe kann mit einem einfachen Parser analysiert werden, siehe [https://adresultparser.codeplex.com/](https://adresultparser.codeplex.com/). Dort finden Sie Beispielcode, der das Verbinden mit der API und das Analysieren der Ausgabe zeigt. Die generierten Warnungen können auf einem Dashboard visualisiert oder an menschliche Experten weitergegeben werden, die dann Korrekturmaßnahmen ergreifen können.

Beispielausgabe für die oben stehende Beispieleingabe:

	"Time,Data,TSpike,ZSpike,Martingale values,Alert indicator,Martingale values(2),Alert indicator(2),;9/21/2014 11:05:00 AM,3,0,0,-0.687952590518378,0,-0.687952590518378,0,;9/21/2014 11:10:00 AM,9.09,0,0,-1.07030497733224,0,-0.884548154298423,0,;9/21/2014 11:15:00 AM,0,0,0,-1.05186237440962,0,-1.173800281031,0,;"

Dabei handelt es sich um eine Darstellung der folgenden Tabelle:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Time</th>
   <th align="left" valign="middle">Data</th>
   <th align="left" valign="middle">Tspike</th>
   <th align="left" valign="middle">Zspike</th>
   <th align="left" valign="middle">Martingale values</th>
   <th align="left" valign="middle">Alert indicator</th>
   <th align="left" valign="middle">Martingale values (2)</th>
   <th align="left" valign="middle">Alert indicator (2)</th>
   </tr>
<tr>
   <td valign="middle">9/21/2014 11:05</td>
   <td valign="middle">3</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.687952591</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.687952591</td>
   <td valign="middle">0</td>
   </tr>
<tr>
<td valign="middle">9/21/2014 11:10</td>
   <td valign="middle">9.09</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.070304977</td>
   <td valign="middle">0</td>
   <td valign="middle">-0.884548154</td>
   <td valign="middle">0</td>
    </tr>
<tr>
<td valign="middle">9/21/2014 11:15</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.051862374</td>
   <td valign="middle">0</td>
   <td valign="middle">-1.1738002814</td>
   <td valign="middle">0</td>
   </tr>
   </table>

[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection.jpg

 

 

<!---HONumber=July15_HO5-->