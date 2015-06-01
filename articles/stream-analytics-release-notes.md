<properties 
	pageTitle="Stream Analytics – Anmerkungen zu dieser Version | Azure" 
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
	ms.date="04/24/2015" 
	ms.author="jeffstok"/>

#Verzögerung bei der Azure-Speicherkontokonfiguration

Wenn Sie einen Stream Analytics-Auftrag zum ersten Mal in einer Region erstellen, werden Sie aufgefordert, ein neues Speicherkonto zu erstellen oder ein vorhandenes Konto anzugeben, um Stream Analytics-Aufträge in dieser Region zu überwachen. Aufgrund der Wartezeit bei der Konfigurationsüberwachung werden Sie beim Erstellen eines anderen Stream Analytics-Auftrags in derselben Region innerhalb von 30 Minuten aufgefordert, ein zweites Speicherkonto anzugeben, anstatt das zuletzt konfigurierte in der Überwachungsspeicherkonto-Dropdownliste anzuzeigen. Um die Erstellung eines unnötigen Speicherkontos zu vermeiden, warten Sie 30 Minuten nach der erstmaligen Erstellung eines Auftrags in einer Region, bis Sie zusätzliche Aufträge in dieser Region bereitstellen.

##Auftrags-Upgrade

Zu diesem Zeitpunkt unterstützt Stream Analytics keine Echtzeitbearbeitungen der Definition oder Konfiguration eines laufenden Auftrags. Um die Eingabe, Ausgabe, Abfrage, Skalierung oder Konfiguration eines laufenden Auftrags zu ändern, müssen Sie den Auftrag zunächst beenden.

##Von der Eingabequelle abgeleitete Datentypen

Wird nicht die Anweisung **CREATE TABLE** verwendet, so wird der Eingabetyp vom Eingabeformat abgeleitet (Beispiel: Alle Felder aus CSV sind Zeichenfolgen). Um Typkonflikte zu vermeiden, müssen die Felder mithilfe der CAST-Funktion explizit in den richtigen Typ konvertiert werden.

##Fehlende Felder werden als Nullwerte ausgegeben.

Der Verweis auf ein Feld, das in der Eingabequelle nicht vorhanden ist, führt zu Nullwerten im Ausgabeereignis.

##WITH-Anweisungen müssen SELECT-Anweisungen vorausgehen.

In der Abfrage müssen SELECT-Anweisungen in WITH-Anweisungen definierten Unterabfragen folgen.

##Arbeitsspeicherproblem

Stream Analytics-Aufträge mit großer Toleranz für unsortierte Ereignisse und/oder komplexe Abfragen, die eine große Statusmenge verwalten, führen dazu, dass nicht genügend Arbeitsspeicher verfügbar ist, wodurch der Auftrag neu gestartet wird. Die Start- und Beendigungsvorgänge sind in den Vorgangsprotokollen des Auftrags sichtbar. Um dieses Verhalten zu vermeiden, skalieren Sie die Abfrage auf mehrere Partitionen. In zukünftigen Versionen wird diese Einschränkung behoben werden, indem die Leistung von betroffenen Aufträgen verringert wird, anstatt sie neu zu starten.

##Große Blob-Eingaben ohne Nutzlastzeitstempel können Arbeitsspeicherprobleme verursachen.

Große Dateien aus dem Blob-Speicher können den Absturz von Stream Analytics-Aufträgen verursachen, wenn über TIMESTAMP BY kein Zeitstempel-Feld angegeben wird. Um dieses Problem zu vermeiden, sorgen Sie dafür, dass jedes Blob kleiner als 10 MB ist.

##Einschränkungen im Hinblick auf die SQL-Datenbank-Ereignismenge

Wenn Sie SQL-Datenbank als Ausgabeziel verwenden, kann es für einen Stream Analytics-Auftrag bei einer sehr großen Ausgabedatenmenge zu einem Timeout kommen. Reduzieren Sie zum Beheben dieses Problems entweder die Ausgabemenge mithilfe von Aggregaten oder Filter-Operatoren, oder wählen Sie stattdessen Azure-Blobspeicher oder den Event Hub als Ausgabeziel aus.

##PowerBI-Datasets können nur eine Tabelle enthalten.

PowerBI unterstützt nicht mehr als eine Tabelle in einem bestimmten Dataset.

##Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

##Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream.analytics.get.started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics-Abfragesprachreferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--HONumber=52-->
