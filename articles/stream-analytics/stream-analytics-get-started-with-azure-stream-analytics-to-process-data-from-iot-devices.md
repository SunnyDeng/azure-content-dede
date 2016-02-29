<properties
	pageTitle="Erste Schritte mit Azure Stream Analytics zum Verarbeiten der Daten von IoT-Geräten | Stream Analytics"
	description="IoT-Sensortags und -Datenströme mit Stream Analytics und Echtzeit-Datenverarbeitung"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/11/2016"
	ms.author="jeffstok"
/>

# Erste Schritte mit Azure Stream Analytics zum Verarbeiten der Daten von IoT-Geräten

In diesem Tutorial erfahren Sie, wie Sie Datenstrom-Verarbeitungslogik erstellen, um Daten von IoT-Geräten (Internet of Things) zu erfassen. Wir verwenden einen echten IoT-Anwendungsfall aus der Praxis, um zu zeigen, wie Sie Ihre Lösung schnell und wirtschaftlich erstellen.

## Voraussetzungen

-   [Azure-Abonnement](https://azure.microsoft.com/pricing/free-trial/)
-   Beispielabfragen und Datendateien per Download von [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted)

## Szenario

Contoso ist ein Fertigungsunternehmen im Bereich der industriellen Automation, das seinen Fertigungsprozess vollständig automatisiert hat. Die Maschinen im Werk verfügen über Sensoren, die in Echtzeit Datenströme ausgeben. In diesem Szenario möchte ein Production Floor Manager in Echtzeit Einblick in die Sensordaten erhalten, um darin nach Mustern zu suchen und entsprechend darauf zu reagieren. Wir verwenden die Stream Analytics Query Language (SAQL) für die Sensordaten, um für den eingehenden Datenstrom interessante Muster zu ermitteln.

In diesem Fall werden die Daten von einem Texas Instruments SensorTag-Gerät generiert.

![Texas Instruments SensorTag](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-01.jpg)

Die Nutzlast der Daten liegt im JSON-Format vor und sieht wie folgt aus:

    
	{
    	"time": "2016-01-26T20:47:53.0000000",  
	    "dspl": "sensorE",  
    	"temp": 123,  
	    "hmdt": 34  
	}  
    
In einem echten Fall generieren Hunderte dieser Sensoren Ereignisse als Datenstrom. Idealerweise ist dann ein Gatewaygerät vorhanden, auf dem Code ausgeführt wird, um diese Ereignisse an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) zu übertragen. Ihr Stream Analytics-Auftrag würde diese Ereignisse von den Event Hubs nutzen und Echtzeitanalysen ausführen, die als Abfragen ausgedrückt werden, und die Ergebnisse an die gewünschten Ausgaben senden.

In dieser Anleitung für die ersten Schritte stellen wir Ihnen eine Beispieldatendatei bereit, deren Daten von echten SensorTag-Geräten erfasst wurden. Sie können hierfür verschiedene Abfragen ausführen und die Ergebnisse anzeigen. In den nachfolgenden Tutorials erfahren Sie, wie Sie Ihren Auftrag mit Eingaben und Ausgaben verbinden und für den Azure-Dienst bereitstellen.

## Erstellen eines Stream Analytics-Auftrags

Öffnen Sie Stream Analytics im [Azure-Portal](http://manage.windowsazure.com), und klicken Sie in der unteren linken Ecke der Seite auf **Neu**, um einen neuen Analytics-Auftrag zu erstellen.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

Klicken Sie auf **Schnellerfassung**.

Wählen Sie **Neues Speicherkonto erstellen** für die Einstellung **Regionales Überwachungskonto für Speicher**, und geben Sie diesem einen eindeutigen Namen. Azure Stream Analytics verwendet dieses Konto, um Überwachungsinformationen für alle Ihre zukünftigen Aufträge zu speichern.

> [AZURE.NOTE] Sie sollten dieses Speicherkonto nur einmal pro Region erstellen. Dieser Speicher wird dann für alle Stream Analytics-Aufträge freigegeben, die in dieser Region erstellt werden.

Klicken Sie am unteren Ende der Seite auf **Stream Analytics-Auftrag erstellen**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.jpg)

## Azure Stream Analytics-Abfrage

Klicken Sie auf die Registerkarte „Abfrage“, um zum Abfrage-Editor zu wechseln. Die Registerkarte „Abfrage“ enthält eine SQL-Abfrage, die die Transformation der eingehenden Daten ausführt.

## Archivieren von Rohdaten

Die einfachste Form einer Abfrage ist ein Passthrough-Vorgang, bei dem alle Eingabedaten unter dem angegebenen Ausgabeort archiviert werden.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-04.png)

Laden Sie jetzt die Beispieldatendatei von [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/GettingStarted) an einen Speicherort auf Ihrem Computer herunter. Kopieren Sie die Abfrage aus der Datei **PassThrough.txt**, und fügen Sie sie ein. Klicken Sie unten auf die Schaltfläche „Test“, und wählen Sie die Datendatei mit dem Namen **HelloWorldASA-InputStream.json** an Ihrem Downloadspeicherort aus.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-06.png)

Die Ergebnisse der Abfrage sind im unten dargestellten Browser zu sehen.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

## Bereinigen der Daten anhand einer Bedingung

Wir versuchen nun, die Ergebnisse anhand einer Bedingung zu filtern. Wir möchten nur Ergebnisse für die Ereignisse anzeigen, die von „SensorA“ stammen. Die Abfrage befindet sich in der Datei **Filtering.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

Beachten Sie Folgendes: Wir vergleichen hier einen Zeichenfolgenwert, und die Groß-/Kleinschreibung wird berücksichtigt. Klicken Sie auf die Schaltfläche **Erneut ausführen**, um die Abfrage auszuführen. Mit der Abfrage sollten nur 389 Zeilen für 1.860 Ereignisse zurückgegeben werden.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-09.png)

## Anzeigen von Warnungen zum Auslösen des Geschäftsworkflows

Wir gestalten die Abfrage jetzt etwas interessanter. Wenn wir für jeden Typ von Sensor die Durchschnittstemperatur für ein Zeitfenster von 30 Sekunden überwachen und Ergebnisse nur anzeigen möchten, wenn die Durchschnittstemperatur mehr als 100 Grad beträgt, schreiben wir die unten angegebene Abfrage und klicken dann auf „Erneut ausführen“, um die Ergebnisse anzuzeigen. Die Abfrage befindet sich in der Datei **ThresholdAlerting.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Sie sehen, dass die Ergebnisse jetzt nur 245 Zeilen mit den Sensoren enthalten, bei denen die Durchschnittstemperatur höher als 100 ist. In dieser Abfrage haben wir den Datenstrom mit den Ereignissen nach „dspl“ gruppiert. Dies ist der Sensorname. Es wird ein **rollierendes Fenster** von 30 Sekunden verwendet. Bei Zeitabfragen dieser Art ist es wichtig anzugeben, wie der Zeitablauf erfolgen soll. Mit der **TIMESTAMP BY**-Klausel haben wir die Spalte „time“ als Grundlage für den zeitlichen Ablauf aller Zeitberechnungen angegeben. Ausführliche Informationen finden Sie in den MSDN-Themen zum [Zeitmanagement](https://msdn.microsoft.com/library/azure/mt582045.aspx) und [„Windowing“](https://msdn.microsoft.com/library/azure/dn835019.aspx).

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

## Ermitteln von fehlenden Mustern

Wie kann ich eine Abfrage schreiben, um fehlende Muster zu ermitteln? Wir können beispielsweise ermitteln, wann ein Sensor zum letzten Mal Daten gesendet und danach eine Minute lang keine weiteren Ereignisse gesendet hat. Die Abfrage befindet sich in der Datei **AbsenseOfEvent.txt**.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-12.png)

Hier verwenden wir **LEFT OUTER JOIN** für denselben Datenstrom (Selbstverknüpfung). Für eine innere Verknüpfung wird nur dann ein Ergebnis zurückgegeben, wenn eine Übereinstimmung gefunden wird. Wenn ein Ereignis von der linken Seite der Verknüpfung keine Übereinstimmung aufweist, wird für eine **LEFT OUTER**-Verknüpfung aber eine Zeile mit NULL für alle Spalten der rechten Zeile zurückgegeben. Dieses Verfahren ist sehr nützlich, um das Nichtvorhandensein von Ereignissen zu ermitteln. Weitere Informationen zu [JOIN](https://msdn.microsoft.com/library/azure/dn835026.aspx) finden Sie in der MSDN-Dokumentation.

![](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-13.png)

## Zusammenfassung

In diesem Tutorial werden die Grundlagen dazu vermittelt, wie Sie verschiedene SAQL-Abfragen schreiben und die Ergebnisse im Browser in unterschiedlichen Mustern für die Daten anzeigen können. Dies ist aber erst der Anfang. Sie haben mit Stream Analytics noch viele weitere Möglichkeiten. Als Nächstes erfahren Sie, wie Sie den Stream Analytics-Auftrag mit Eingaben und Ausgaben verbinden und ihn unter Azure bereitstellen. Sie können sich weiter über Stream Analytics informieren, indem Sie den [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/stream-analytics/) verwenden. Weitere Informationen zum Schreiben von Abfragen erhalten Sie im Artikel [Gängige Abfragemuster](./stream-analytics-stream-analytics-query-patterns.md#query-example-detect-the-absence-of-events).

<!---HONumber=AcomDC_0218_2016-->