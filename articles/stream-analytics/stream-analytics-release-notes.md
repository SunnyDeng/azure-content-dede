<properties 
	pageTitle="Stream Analytics – Anmerkungen zu dieser Version | Microsoft Azure" 
	description="Stream Analytics GA – Anmerkungen zu dieser Version" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="01/13/2015" 
	ms.author="jeffstok"/>

#Versionshinweise zu Stream Analytics

## Hinweise zur Stream Analytics-Version vom 10.12.2015 ##

Diese Version enthält das folgende Update.

Titel | Beschreibung
---|---
REST-API-Versionsupdate | Die REST-API-Version wurde auf 2015-10-01 aktualisiert. Details finden Sie auf MSDN unter [Stream Analytics-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx) und [Machine Learning-Integration in Stream Analytics](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Azure Machine Learning-Integration | Mit dieser Version wird die Unterstützung für benutzerdefinierte Azure Machine Learning-Funktionen eingeführt. Sie finden [hier](stream-analytics-machine-learning-integration-tutorial.md) ein Tutorial und [hier](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx) die allgemeine Blog-Ankündigung.

## Hinweise zur Version von Stream Analytics vom 12.11.2015 ##

Diese Version enthält das folgende Update.

Titel | Beschreibung
---|---
Neues Verhalten der SELECT-Anweisung | Die SELECT-Anweisung in Stream Analytics wurde erweitert, um * als Eigenschaftenaccessor eines verschachtelten Datensatzes zuzulassen. Weitere Informationen finden Sie unter [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Komplexe Datentypen").

## Hinweise zur Version von Stream Analytics vom 22.10.2015 ##

Diese Version enthält die folgenden Updates.

Titel | Beschreibung
---|---
Zusätzliche Features für die Abfragesprache | Stream Analytics hat die Abfragesprache um die folgenden Features erweitert: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx), [SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx) und [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Aggregatbeschränkungen aufgehoben | Mit dieser Version wird die Beschränkung auf 15 Aggregate pro Abfrage aufgehoben. Es gibt jetzt keine Beschränkung für die Anzahl der Aggregate pro Abfrage mehr.
Feature „GROUP BY System.Timestamp“ hinzugefügt | Die [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx)-Funktion ermöglicht jetzt entweder „window\_type“ oder [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
OFFSET für Tumbling- und Hopping-Fenster hinzugefügt | [Tumbling](https://msdn.microsoft.com/library/azure/dn835055.aspx)- und [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx)-Fenster sind standardmäßig an der Nullzeit ausgerichtet (1.1.0001 0:00:00 UTC). Der neue (optionale) Parameter „offsetsize“ ermöglicht das Angeben eines benutzerdefinierten Versatzes (oder einer Ausrichtung).


## Hinweise zur Version von Stream Analytics vom 29.09.2015 ##

Diese Version enthält die folgenden Updates.

Titel | Beschreibung
---|---
Öffentliche Vorschau zu Azure IoT Suite | Stream Analytics ist in der öffentlichen Vorschau von Azure IoT Suite enthalten.
Azure-Vorschauportal, Integration | Stream Analytics ist nicht nur weiterhin im Azure-Verwaltungsportal enthalten, sondern jetzt auch im [Azure-Vorschauportal](http://azure.microsoft.com/overview/preview-portal/) integriert. Beachten Sie, dass im Vorschauportal derzeit nur ein Teil der im Azure-Verwaltungsportal bereitgestellten Stream Analytics-Funktionalität verfügbar ist. Nicht unterstützt werden Funktionen wir das Testen von Abfragen im Browser, das Konfigurieren der Power BI-Ausgabe und das Durchsuchen oder Erstellen neuer Eingabe- und Ausgaberessourcen in Abonnements, auf die Sie zugreifen können.
Unterstützung für DocumentDB-Ausgabe | Stream Analytics-Aufträge können jetzt Ausgaben an [DocumentDB](http://azure.microsoft.com/services/documentdb/) senden.
Unterstützung für IoT Hub-Eingabe | Stream Analytics-Aufträge können jetzt Daten von IoT Hubs erfassen.
TIMESTAMP BY für heterogene Ereignisse | Wenn ein einzelner Datenstrom mehrere Ereignistypen mit Zeitstempeln in verschiedenen Feldern enthält, können Sie nun [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) mit Ausdrücken verwenden, um für jeden Fall unterschiedliche Zeitstempelfelder anzugeben.

## Hinweise zur Version von Stream Analytics vom 10.09.2015 ##

Diese Version enthält die folgenden Updates.

Titel|Beschreibung
---|---
Unterstützung für Power BI-Gruppen|Zum Aktivieren der Freigabe von Daten mit anderen Power BI-Benutzern können Stream Analytics-Aufträge jetzt in [PowerBI-Gruppen](stream-analytics-define-outputs.md#power-bi) innerhalb Ihres Power BI-Kontos schreiben.

## Hinweise zur Stream Analytics-Version vom 20.08.2015 ##

Diese Version enthält die folgenden Updates.

Titel|Beschreibung
---|---
Zusätzliche LAST-Funktion |Die [LAST](http://msdn.microsoft.com/library/mt421186.aspx)-Funktion steht nun in Stream Analytics-Aufträgen zur Verfügung und ermöglicht Ihnen, das letzte Ereignis in einem Ereignisdatenstrom innerhalb eines bestimmten Zeitrahmens abrufen.
Neue Arrayfunktionen|Die Arrayfunktionen [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) und [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) sind jetzt verfügbar.
Neue Datensatzfunktionen|Die Datensatzfunktionen [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) und [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sind jetzt verfügbar.

## Hinweise zur Stream Analytics-Version vom 30.07.2015 ##

Diese Version enthält die folgenden Updates.

Titel|Beschreibung
---|---
Die Power BI-Organisations-ID wurde von der Azure-ID entkoppelt.|Dieses Feature ermöglicht die [Power BI-Ausgabe](stream-analytics-power-bi-dashboard.md) für ASA-Aufträge unter einem beliebigen Azure-Kontotyp (Live-ID oder Organisations-ID). Darüber hinaus können Sie eine Organisations-ID für Ihre Azure-Konto einrichten und eine andere ID für die Autorisierung der Power BI-Ausgabe verwenden.
Unterstützung für die Ausgabe von Service Bus-Warteschlangen|Ausgaben von [Service Bus-Warteschlangen](stream-analytics-connect-data-event-outputs.md#service-bus-queues) stehen nun in Stream Analytics-Aufträgen zur Verfügung.
Unterstützung für die Ausgabe von Service Bus-Themen|Ausgaben von [Service Bus-Themen](stream-analytics-connect-data-event-outputs.md#service-bus-topics) stehen nun in Stream Analytics-Aufträgen zur Verfügung.

## Hinweise zur Version von Stream Analytics vom 09.07.2015 ##

Diese Version enthält die folgenden Updates.


Titel|Beschreibung
---|---
Benutzerdefinierte Blob-Ausgabepartitionierung|Blob-Speicherausgaben erlauben einer Option nun, die Häufigkeit anzugeben, mit der Ausgabe-Blobs geschrieben werden und die Struktur und das Format der Ausgabepfad-Ordnerstruktur zu bestimmen. 

## Hinweise zur Version von Stream Analytics vom 03.05.2015 ##

Diese Version enthält die folgenden Updates.


Titel|Beschreibung
---|---
Erhöhter maximaler Wert des Toleranzfensters für falsche Reihenfolge|Die maximale Größe für das Toleranzfenster für falsche Reihenfolge ist jetzt 59:59 (Min:Sek)
JSON-Ausgabeformat: Trennung durch Zeile oder Array|Es gibt jetzt die Option der Ausgabe an einen Blob-Speicher oder Event Hub als Array von JSON-Objekten oder durch die Trennung von JSON-Objekten mit einer neuen Zeile. 

## Hinweise zur Version von Stream Analytics vom 16.04.2015 ##


Titel|Beschreibung
---|---
Verzögerung bei der Azure-Speicherkontokonfiguration|Wenn Sie einen Stream Analytics-Auftrag zum ersten Mal in einer Region erstellen, werden Sie aufgefordert, ein neues Speicherkonto zu erstellen oder ein vorhandenes Konto anzugeben, um Stream Analytics-Aufträge in dieser Region zu überwachen. Aufgrund der Wartezeit bei der Konfigurationsüberwachung werden Sie beim Erstellen eines anderen Stream Analytics-Auftrags in derselben Region innerhalb von 30 Minuten aufgefordert, ein zweites Speicherkonto anzugeben, anstatt das zuletzt konfigurierte in der Überwachungsspeicherkonto-Dropdownliste anzuzeigen. Um die Erstellung eines unnötigen Speicherkontos zu vermeiden, warten Sie 30 Minuten nach der erstmaligen Erstellung eines Auftrags in einer Region, bis Sie zusätzliche Aufträge in dieser Region bereitstellen.
Auftrags-Upgrade|Zum aktuellen Zeitpunkt unterstützt Stream Analytics keine Echtzeitbearbeitungen der Definition oder Konfiguration eines laufenden Auftrags. Um die Eingabe, Ausgabe, Abfrage, Skalierung oder Konfiguration eines laufenden Auftrags zu ändern, müssen Sie den Auftrag zunächst beenden.
Von der Eingabequelle abgeleitete Datentypen|Wird nicht die Anweisung CREATE TABLE verwendet, so wird der Eingabetyp vom Eingabeformat abgeleitet (Beispiel: Alle Felder aus CSV sind Zeichenfolgen). Um Typkonflikte zu vermeiden, müssen die Felder mithilfe der CAST-Funktion explizit in den richtigen Typ konvertiert werden.
Fehlende Felder werden als Nullwerte ausgegeben.|Der Verweis auf ein Feld, das in der Eingabequelle nicht vorhanden ist, führt zu Nullwerten im Ausgabeereignis.
WITH-Anweisungen müssen SELECT-Anweisungen vorausgehen.|In der Abfrage müssen SELECT-Anweisungen in WITH-Anweisungen definierten Unterabfragen folgen.
Arbeitsspeicherproblem|Stream Analytics-Aufträge mit großer Toleranz für unsortierte Ereignisse und/oder komplexe Abfragen, die eine große Statusmenge verwalten, führen dazu, dass nicht genügend Arbeitsspeicher verfügbar ist, wodurch der Auftrag neu gestartet wird. Die Start- und Beendigungsvorgänge sind in den Vorgangsprotokollen des Auftrags sichtbar. Um dieses Verhalten zu vermeiden, skalieren Sie die Abfrage auf mehrere Partitionen. In zukünftigen Versionen wird diese Einschränkung behoben werden, indem die Leistung von betroffenen Aufträgen verringert wird, anstatt sie neu zu starten.
Große Blob-Eingaben ohne Nutzlastzeitstempel können Arbeitsspeicherprobleme verursachen.|Große Dateien aus dem Blob-Speicher können den Absturz von Stream Analytics-Aufträgen verursachen, wenn über TIMESTAMP BY kein Zeitstempel-Feld angegeben wird. Um dieses Problem zu vermeiden, sorgen Sie dafür, dass jedes Blob kleiner als 10 MB ist.
Einschränkungen im Hinblick auf die SQL-Datenbank-Ereignismenge|Wenn Sie SQL-Datenbank als Ausgabeziel verwenden, kann es für einen Stream Analytics-Auftrag bei einer sehr großen Ausgabedatenmenge zu einem Timeout kommen. Reduzieren Sie zum Beheben dieses Problems entweder die Ausgabemenge mithilfe von Aggregaten oder Filter-Operatoren, oder wählen Sie stattdessen Azure-Blobspeicher oder den Event Hub als Ausgabeziel aus.
PowerBI-Datasets können nur eine Tabelle enthalten.|PowerBI unterstützt nicht mehr als eine Tabelle in einem bestimmten Dataset.

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](../stream.analytics.get.started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0121_2016-->