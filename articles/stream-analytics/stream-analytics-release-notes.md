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
	ms.date="07/09/2015" 
	ms.author="jeffstok"/>

#Microsoft Stream Analytic Versionshinweise

## Hinweise zur Version von Stream Analytics vom 07/09/2015 ##

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
</tr>

<tr>
<td>Benutzerdefinierte Blob-Ausgabepartitionierung</td>
<td>Blob-Speicherausgaben erlauben einer Option nun, die Häufigkeit anzugeben, mit der Ausgabe-Blobs geschrieben werden und die Struktur und das Format der Ausgabepfad-Ordnerstruktur zu bestimmen. </td>
</tr>
</table>

## Hinweise zur Version von Stream Analytics vom 05/03/2015 ##

Diese Version enthält die folgenden Updates.

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
</tr>

<tr>
<td>Erhöhter maximaler Wert des Toleranzfensters für falsche Reihenfolge</td>
<td>Die maximale Größe für das Toleranzfenster für falsche Reihenfolge ist jetzt 59:59 (Min:Sek)</td>
</tr>

<tr>
<td>JSON-Ausgabeformat: Trennung durch Zeile oder Array</td>
<td>Es gibt jetzt die Option der Ausgabe an einen Blob-Speicher oder Event Hub als Array von JSON-Objekten oder durch die Trennung von JSON-Objekten mit einer neuen Zeile. </td>
</tr>
</table>

## Hinweise zur Version von Stream Analytics vom 04/16/2015 ##

<table border="1">
<tr>
<th>Titel</th>
<th>Beschreibung</th>
</tr>

<tr>
<td>Verzögerung bei der Azure-Speicherkontokonfiguration</td>
<td>Wenn Sie einen Stream Analytics-Auftrag zum ersten Mal in einer Region erstellen, werden Sie aufgefordert, ein neues Speicherkonto zu erstellen oder ein vorhandenes Konto anzugeben, um Stream Analytics-Aufträge in dieser Region zu überwachen. Aufgrund der Wartezeit bei der Konfigurationsüberwachung werden Sie beim Erstellen eines anderen Stream Analytics-Auftrags in derselben Region innerhalb von 30 Minuten aufgefordert, ein zweites Speicherkonto anzugeben, anstatt das zuletzt konfigurierte in der Überwachungsspeicherkonto-Dropdownliste anzuzeigen. Um die Erstellung eines unnötigen Speicherkontos zu vermeiden, warten Sie 30&#160;Minuten nach der erstmaligen Erstellung eines Auftrags in einer Region, bis Sie zusätzliche Aufträge in dieser Region bereitstellen.</td>
</tr>

<tr>
<td>Auftrags-Upgrade</td>
<td>Zum aktuellen Zeitpunkt unterstützt Stream Analytics keine Echtzeitbearbeitungen der Definition oder Konfiguration eines laufenden Auftrags. Um die Eingabe, Ausgabe, Abfrage, Skalierung oder Konfiguration eines laufenden Auftrags zu ändern, müssen Sie den Auftrag zunächst beenden.</td>
</tr>

<tr>
<td>Von der Eingabequelle abgeleitete Datentypen</td>
<td>Wird nicht die Anweisung CREATE TABLE verwendet, so wird der Eingabetyp vom Eingabeformat abgeleitet (Beispiel: Alle Felder aus CSV sind Zeichenfolgen). Um Typkonflikte zu vermeiden, müssen die Felder mithilfe der CAST-Funktion explizit in den richtigen Typ konvertiert werden.</td>
</tr>

<tr>
<td>Fehlende Felder werden als Nullwerte ausgegeben.</td>
<td>Der Verweis auf ein Feld, das in der Eingabequelle nicht vorhanden ist, führt zu Nullwerten im Ausgabeereignis.</td>
</tr>

<tr>
<td>WITH-Anweisungen müssen SELECT-Anweisungen vorausgehen.</td>
<td>In der Abfrage müssen SELECT-Anweisungen in WITH-Anweisungen definierten Unterabfragen folgen.</td>
</tr>

<tr>
<td>Arbeitsspeicherproblem</td>
<td>Stream Analytics-Aufträge mit großer Toleranz für unsortierte Ereignisse und/oder komplexe Abfragen, die eine große Statusmenge verwalten, führen dazu, dass nicht genügend Arbeitsspeicher verfügbar ist, wodurch der Auftrag neu gestartet wird. Die Start- und Beendigungsvorgänge sind in den Vorgangsprotokollen des Auftrags sichtbar. Um dieses Verhalten zu vermeiden, skalieren Sie die Abfrage auf mehrere Partitionen. In zukünftigen Versionen wird diese Einschränkung behoben werden, indem die Leistung von betroffenen Aufträgen verringert wird, anstatt sie neu zu starten.</td>
</tr>

<tr>
<td>Große Blob-Eingaben ohne Nutzlastzeitstempel können Arbeitsspeicherprobleme verursachen.</td>
<td>Große Dateien aus dem Blob-Speicher können den Absturz von Stream Analytics-Aufträgen verursachen, wenn über TIMESTAMP BY kein Zeitstempel-Feld angegeben wird. Um dieses Problem zu vermeiden, sorgen Sie dafür, dass jedes Blob kleiner als 10&#160;MB ist.</td>
</tr>

<tr>
<td>Einschränkungen im Hinblick auf die SQL-Datenbank-Ereignismenge</td>
<td>Wenn Sie SQL-Datenbank als Ausgabeziel verwenden, kann es für einen Stream Analytics-Auftrag bei einer sehr großen Ausgabedatenmenge zu einem Timeout kommen. Reduzieren Sie zum Beheben dieses Problems entweder die Ausgabemenge mithilfe von Aggregaten oder Filter-Operatoren, oder wählen Sie stattdessen Azure-Blobspeicher oder den Event Hub als Ausgabeziel aus.</td>
</tr>

<tr>
<td>PowerBI-Datasets können nur eine Tabelle enthalten.</td>
<td>PowerBI unterstützt nicht mehr als eine Tabelle in einem bestimmten Dataset.</td>
</tr>
</table>

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](../stream.analytics.get.started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=July15_HO2-->