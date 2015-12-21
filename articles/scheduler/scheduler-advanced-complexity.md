<properties 
 pageTitle="Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>

# Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler  

## Übersicht

Das Herzstück eines Azure Scheduler-Auftrags ist der *Zeitplan*. Der Zeitplan bestimmt, wann und wie Scheduler den Auftrag ausführt.

Mit Azure Scheduler können Sie verschiedene einmalige Zeitpläne und Wiederholungszeitpläne für einen Auftrag angeben. *Einmalige* Zeitpläne werden einmal zu einem bestimmten Zeitpunkt ausgelöst und sind damit im Prinzip *Wiederholungszeitpläne*, die nur einmal ausgeführt werden. Wiederholungszeitpläne werden gemäß einer vorgegebenen Häufigkeit ausgelöst.

Dadurch bietet Azure Scheduler ein hohes Maß an Flexibilität für verschiedenste geschäftliche Szenarien:

-	Regelmäßige Datenbereinigung (etwa zur täglichen Löschung aller Tweets, die älter als drei Monate sind)
-	Archivierung (etwa zur monatlichen Weiterleitung des Rechnungsverlaufs an den Sicherungsdienst)
-	Anforderung externer Daten (etwa zum Abrufen des aktuellen Skiwetterberichts im 15-Minuten-Takt)
-	Bildverarbeitung (etwa zur regelmäßigen Komprimierung der hochgeladenen Bilder des aktuellen Tages mittels Cloud-Computing an jedem Wochentag außerhalb der Spitzenzeiten)


Dieser Artikel enthält Beispielaufträge, die Sie mit Azure Scheduler erstellen können. Hier finden Sie auch die JSON-Daten zur Beschreibung der einzelnen Zeitpläne. Bei Verwendung der [Scheduler-REST-API](https://msdn.microsoft.com/library/azure/dn528946.aspx) können Sie diese JSON-Daten zum [Erstellen eines Azure Scheduler-Auftrags](https://msdn.microsoft.com/library/azure/dn528937.aspx) verwenden.

## Unterstützte Szenarien

Die vielen Beispielen in diesem Thema veranschaulichen die hohe Bandbreite der unterstützten Szenarien. Grob gesagt zeigen diese Beispiele, wie Sie Zeitpläne für verschiedenste Verhaltensmuster erstellen. Hierzu zählen unter anderem folgende:

-	Einmalige Ausführung zu einem bestimmten Zeitpunkt (Datum und Uhrzeit)
-	Ausführung mit einer bestimmten Anzahl von Wiederholungen
-	Sofortige Ausführung und Wiederholung 
-	Ausführung ab einem bestimmten Zeitpunkt und Wiederholung alle *n* Minuten, Stunden, Tage, Wochen oder Monate
-	Ausführung und wöchentliche oder monatliche Wiederholung, jedoch nur an bestimmten Tagen, Wochentagen oder Monatstagen
-	Ausführung und mehrmalige Wiederholung innerhalb eines Zeitraums (etwa jeweils am letzten Freitag und Montag eines Monats oder täglich um 5:15 Uhr und um 17:15 Uhr)

## Datums- und Datums-/Uhrzeitangaben

Datumsangaben in Azure Scheduler-Aufträgen entsprechen der [ISO-8601-Spezifikation](http://en.wikipedia.org/wiki/ISO_8601) und enthalten nur das Datum.

Datums-/Uhrzeitangaben in Azure Scheduler-Aufträgen entsprechen der [ISO-8601-Spezifikation](http://en.wikipedia.org/wiki/ISO_8601) und enthalten sowohl ein Datum als auch eine Uhrzeit. Eine Datums-/Uhrzeitangabe ohne UTC-Offset wird als UTC-Angabe interpretiert.

## Erstellen von Zeitplänen mithilfe von JSON und REST-API

Gehen Sie wie folgt vor, um einen einfachen Zeitplan mit den JSON-Beispielen in diesem Artikel und der Azure Scheduler-REST-API zu erstellen: [Erstellen Sie einen Clouddienst](https://msdn.microsoft.com/library/azure/dn528943.aspx), [erstellen Sie eine Auftragssammlung](https://msdn.microsoft.com/library/azure/dn528940.aspx), und [erstellen Sie anschließend einen Auftrag](https://msdn.microsoft.com/library/azure/dn528937.aspx). Bei der Auftragserstellung können Sie mithilfe von JSON den Zeitplan und die Wiederholung angeben, wie im folgenden Auszug zu sehen:

	{
	    "startTime": "2012-08-04T00:00Z", // optional
	     …
	    "recurrence":                     // optional
	    {
	        "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
	        "interval": 1,                // optional, how often to fire (default to 1)
	        "schedule":                   // optional (advanced scheduling specifics)
	        {
	            "weekDays": ["monday", "wednesday", "friday"],
	            "hours": [10, 22]                      
	        },
	        "count": 10,                  // optional (default to recur infinitely)
	        "endTime": "2012-11-04",      // optional (default to recur infinitely)
	    },
	    …
	}
	
## Übersicht: Grundlagen zum Auftragsschema

Die folgende Tabelle enthält eine allgemeine Übersicht über die wichtigsten Elemente für die Wiederholung und Zeitplanung in einem Auftrag:

|**JSON-Name**|**Beschreibung**|
|:--|:--|
|**_startTime_**|_startTime_ ist eine Datums-/Uhrzeitangabe. Bei einfachen Zeitpläne ist _startTime_ das erste Vorkommen. Bei komplexen Zeitplänen beginnt der Auftrag nicht vor _startTime_.|
|**_recurrence_**|Das Objekt _recurrence_ gibt die Wiederholungsregeln für den Auftrag sowie die Wiederholung an, mit der der Auftrag ausgeführt wird. Es unterstützt die Elemente _frequency, interval, endTime, count_ und _schedule_. Bei Angabe von _recurrence_ muss auch _frequency_ angegeben werden. Die anderen Elemente von _recurrence_ sind optional.|
|**_frequency_**|Die Zeichenfolge _frequency_ stellt die Häufigkeitseinheit für die Auftragswiederholung dar. Unterstützte Werte: _"minute", "hour", "day", "week"_ und _"month"_.|
|**_interval_**|_interval_ ist eine positive ganze Zahl und gibt das Intervall für _frequency_ an, das bestimmt, wie oft der Auftrag ausgeführt wird. Ist _interval_ also beispielsweise auf „3“ und _frequency_ auf „week“ festgelegt, wird der Auftrag alle drei Wochen ausgeführt. Azure Scheduler unterstützt für _interval_ maximal 18 Monate (bei monatlicher Häufigkeit), 78 Wochen (bei wöchentlicher Häufigkeit) bzw. 548 Tage (bei täglicher Häufigkeit). Bei Stunden und Minuten wird folgender Bereich unterstützt: 1 <= _interval_ <= 1000.|
|**_endTime_**|Die Zeichenfolge _endTime_ gibt an, ab welchem Datum und welcher Uhrzeit der Auftrag nicht mehr ausgeführt werden soll. _endTime_ darf nicht in der Vergangenheit liegen. Ohne Angabe von _endTime_ oder einer Anzahl wird der Auftrag unendlich ausgeführt. Für einen Auftrag kann sowohl _endTime_ als auch _count_ angegeben werden.|
|**_count_**|<p>_count_ ist eine positive ganze Zahl (größer Null) und gibt an, wie oft der Auftrag ausgeführt werden soll, bis er abgeschlossen ist.</p><p>Mit _count_ wird angegeben, wie oft der Auftrag ausgeführt wird, bevor er als abgeschlossen betrachtet wird. So ist beispielsweise ein täglich auszuführender Auftrag, für den _count_ auf „5“ und das Startdatum auf Montag festgelegt ist, nach der Ausführung am Freitag abgeschlossen. Liegt das Startdatum in der Vergangenheit, wird die erste Ausführung auf der Grundlage des Erstellungszeitpunkts berechnet.</p><p>Ohne Angabe von _endTime_ oder _count_ wird der Auftrag unendlich ausgeführt. Für einen Auftrag kann sowohl _endTime_ als auch _count_ angegeben werden.</p>|
|**_schedule_**|Die Wiederholung eines Auftrags mit einer bestimmten Häufigkeit wird auf der Grundlage eines Wiederholungszeitplans angepasst. _schedule_ enthält Anpassungen auf der Grundlage von Minuten, Stunden, Wochentagen, Monatstagen und Wochennummer.|

## Übersicht: Standardwerte, Einschränkungen und Beispiele für das Auftragsschema

In diesem Abschnitt gehen wir ausführlicher auf die einzelnen Elemente ein.

|**JSON-Name**|**Werttyp**|**Erforderlich?**|**Standardwert**|**Gültige Werte**|**Beispiel**|
|:---|:---|:---|:---|:---|:---|
|**_startTime_**|String|Nein|Keine|Datum/Uhrzeit (nach ISO 8601)|<code>"startTime" : "2013-01-09T09:30:00-08:00"</code>|
|**_recurrence_**|Object|Nein|Keine|Wiederholungsobjekt|<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code>|
|**_frequency_**|String|Ja|Keine|"minute", "hour", "day", "week", "month"|<code>"frequency" : "hour"</code> |
|**_interval_**|Number|Nein|1|1 bis 1000.|<code>"interval":10</code>|
|**_endTime_**|String|Nein|Keine|Datums-/Uhrzeitwert für einen Zeitpunkt in der Zukunft|<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
|**_count_**|Number|Nein|Keine|>= 1|<code>"count": 5</code>|
|**_schedule_**|Object|Nein|Keine|Zeitplanobjekt|<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code>|

## Ausführliche Betrachtung: _startTime_

Die folgende Tabelle verdeutlicht, wie _startTime_ die Ausführung eines Auftrags beeinflusst:

|**startTime-Wert**|**Ohne Wiederholung**|**Wiederholung, kein Zeitplan**|**Wiederholung mit Zeitplan**|
|:--|:--|:--|:--|
|**Keine Startzeit**|Sofortige einmalige Ausführung|Sofortige einmalige Ausführung. Berechnung weiterer Ausführungen auf der Grundlage der letzten Ausführungszeit|<p>Sofortige einmalige Ausführung</p><p>Weitere Ausführungen auf der Grundlage des Wiederholungszeitplans</p>|
|**Startzeit in der Vergangenheit**|Sofortige einmalige Ausführung|<p>Berechnung der ersten weiteren Ausführungszeit nach der Startzeit und Ausführung zu diesem Zeitpunkt</p><p>Berechnung weiterer Ausführungen auf der Grundlage der letzten Ausführungszeit</p><p>Weitere Informationen finden Sie im Beispiel im Anschluss an diese Tabelle.</p>|<p>Auftrag startet _frühestens_ zur angegebenen Startzeit. Das erste Vorkommen basiert auf dem Zeitplan, der auf der Grundlage der Startzeit berechnet wird.</p><p>Weitere Ausführungen basieren auf dem Wiederholungszeitplan.</p>|
|**Startzeit in der Zukunft oder Gegenwart**|Einmalige Ausführung zur angegebenen Startzeit|<p>Einmalige Ausführung zur angegebenen Startzeit</p><p>Berechnung weiterer Ausführungen auf der Grundlage der letzten Ausführungszeit</p>|<p>Auftrag startet _frühestens_ zur angegebenen Startzeit. Das erste Vorkommen basiert auf dem Zeitplan, der auf der Grundlage der Startzeit berechnet wird.</p><p>Weitere Ausführungen basieren auf dem Wiederholungszeitplan.</p>|

Das folgende Beispiel veranschaulicht, was passiert, wenn _startTime_ in der Vergangenheit liegt und nur _recurrence_, aber kein _schedule_ angegeben ist. In diesem Beispiel wird davon ausgegangen, dass die aktuelle Zeit „2015-04-08-13:00“ ist, _startTime_ auf „2015-04-07 14:00“ festgelegt ist und für _recurrence_ ein Zwei-Tages-Intervall (mit „_frequency_: day“ und „_interval_: 2“) angegeben wurde. Beachten Sie, dass _startTime_ in der Vergangenheit liegt.

Unter diesen Bedingungen findet die _erste Ausführung_ am 9.4.2015 um 14:00 Uhr statt. Das Scheduler-Modul berechnet die Ausführungen auf der Grundlage der Startzeit. In der Vergangenheit liegende Instanzen werden verworfen. Das Modul verwendet die nächste in der Zukunft liegende Instanz. In vorliegenden Fall ist _startTime_ auf 14:00 Uhr am 7.4.2015 festgelegt. Die nächste Instanz folgt zwei Tage nach diesem Zeitpunkt, also am 9.4.2015 um 14:00 Uhr.

Beachten Sie, dass die erste Ausführung auch dann zu diesem Zeitpunkt stattfinden würde, wenn als Startzeit der 5.4.2015 oder der 1.4.2015 (jeweils 14:00 Uhr) angegeben wäre. Nach der ersten Ausführung werden die nachfolgenden Ausführungen auf der Grundlage des Zeitplans berechnet und erfolgen jeweils um 14:00 Uhr am 11.4.2015, am 13.4.2015, am 15.4.2015 usw.

Wenn für den Auftrag ein Zeitplan, aber keine Stunden- und/oder Minutenangabe festgelegt ist, werden standardmäßig die Stunden und/oder Minuten der ersten Ausführung verwendet.

## Ausführliche Betrachtung: _schedule_

Mithilfe von _schedule_ lässt sich einerseits die Anzahl der Auftragsausführungen _begrenzen_. Beispiel: Wird für einen Auftrag mit einer monatlichen Häufigkeit ein Zeitplan angegeben, der nur am 31. Tag ausgeführt wird, wird der Auftrag nur in Monaten mit 31<sup></sup> Tagen ausgeführt.

Andererseits kann _schedule_ auch zum _Erweitern_ der Anzahl von Auftragsausführungen verwendet werden. Beispiel: Wird für einen Auftrag mit einer monatlichen Häufigkeit ein Zeitplan angegeben, der am ersten und zweiten Monatstag ausgeführt wird, wird der Auftrag nicht nur einmal im Monat, sondern jeweils am ersten<sup></sup> und zweiten<sup></sup> Tag des Monats ausgeführt.

Bei Angabe mehrerer Zeitplanelemente werden diese in absteigender Reihenfolge ausgewertet – also von der Wochennummer über Monatstag und Wochentag bis hin zu Stunde und Minute.

Die folgende Tabelle enthält eine ausführliche Beschreibung der Zeitplanelemente:

|**JSON-Name**|**Beschreibung**|**Gültige Werte**|
|:---|:---|:---|
|**minutes**|Minuten der Stunde, in der der Auftrag ausgeführt wird|<ul><li>Ganze Zahl oder</li><li>Array mit ganzen Zahlen</li></ul>|
|**hours**|Stunden des Tages, an dem der Auftrag ausgeführt wird|<ul><li>Ganze Zahl oder</li><li>Array mit ganzen Zahlen</li></ul>|
|**weekDays**|Die Wochentage, an denen der Auftrag ausgeführt wird. Kann nur bei wöchentlicher Häufigkeit angegeben werden.|<ul><li>"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" oder "Sunday"</li><li>Array mit beliebigen der oben angegebenen Werte (maximale Arraygröße: 7)</li></ul>Die Groß-/Kleinschreibung wird NICHT beachtet.|
|**monthlyOccurrences**|Bestimmt, an welchen Tagen im Monat der Auftrag ausgeführt wird. Kann nur bei monatlicher Häufigkeit angegeben werden.|<ul><li>Array mit MonthlyOccurence-Objekten:</li></ul> <pre>{ "day": _Tag_,<br /> "occurrence": _Vorkommen_<br />}</pre><p> _Tag_ ist der Wochentag, an dem der Auftrag ausgeführt wird. „{Sunday}“ steht beispielsweise für jeden Sonntag im Monat. (erforderlich)</p><p>_Vorkommen_ steht für den Tag innerhalb des Monats. Mit „{Sunday, -1}“ wird beispielsweise der letzte Sonntag des Monats angegeben. (optional)</p>|
|**monthDays**|Der Tag des Monats, an dem der Auftrag ausgeführt wird. Kann nur bei monatlicher Häufigkeit angegeben werden.|<ul><li>Beliebiger Wert, für den Folgendes gilt: <= -1 und >= -31</li><li>Beliebiger Wert, für den Folgendes gilt: >= 1 und <= 31</li><li>Array mit den oben genannten Werten</li></ul>|

## Beispiele: Wiederholungszeitpläne

Im Anschluss folgen verschiedene Beispiele für Wiederholungszeitpläne, bei denen jeweils das schedule-Objekt und die dazugehörigen Elemente im Mittelpunkt stehen.

Bei den folgenden Zeitplänen wird jeweils davon ausgegangen, dass _interval_ auf „1“ festgelegt ist. Darüber hinaus wird davon ausgegangen, dass für den Zeitplan jeweils eine passende Häufigkeit angegeben ist, da der Zeitplan bei Angabe einer täglichen Häufigkeit beispielsweise nicht mit "monthDays" angepasst werden kann. Eine Beschreibung dieser Einschränkungen finden Sie weiter oben.

|**Beispiel**|**Beschreibung**|
|:---|:---|
|<code>{"hours":[5]}</code>|Tägliche Ausführung um 5 Uhr. Azure Scheduler gleicht jeden Wert für "hours" nacheinander mit den Werten für "minutes" ab, um eine Liste mit allen Zeiten zu erstellen, zu denen der Auftrag ausgeführt werden soll.|
|<code>{"minutes":[15],"hours":[5]}</code>|Tägliche Ausführung um 5:15 Uhr|
|<code>{"minutes":[15],"hours":[5,17]}</code>|Tägliche Ausführung um 5:15 Uhr und um 17:15 Uhr|
|<code>{"minutes":[15,45],"hours":[5,17]}</code>|Tägliche Ausführung um 5:15 Uhr, 5:45 Uhr, 17:15 Uhr und um 17:45 Uhr|
|<code>{"minutes":[0,15,30,45]}</code>|Ausführung alle 15 Minuten|
|<code>{hours":[0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23]}</code>|Stündliche Ausführung. Dieser Auftrag wird einmal pro Stunde ausgeführt. Der Minutenwert wird von _startTime_ abgeleitet. Ist hier kein Minutenwert angegeben, wird der Minutenwert des Erstellungszeitpunkts verwendet. Lautet die Start- oder Erstellungszeit also beispielsweise 12:25 Uhr, wird der Auftrag um 00:25 Uhr, 01:25 Uhr, 02:25 Uhr, …, 23:25 Uhr ausgeführt. Der Zeitplan entspricht einem Auftrag mit _frequency_ = "hour", _interval_ = 1 und ohne Angabe von _schedule_. Der Unterschied besteht darin, dass dieser Zeitplan mit unterschiedlichen Angaben für _frequency_ und _interval_ zur Erstellung anderer Aufträge verwendet werden kann. Wird _frequency_ beispielsweise auf "month" festgelegt, wird der Auftrag nur einmal im Monat ausgeführt (und nicht täglich wie bei _frequency_ = "day").|
|<code>{minutes:[0]}</code>|Ausführung jeweils zur vollen Stunde. Dieser Auftrag wird ebenfalls stündlich ausgeführt, aber immer zur vollen Stunde (also beispielsweise um 12: 00 Uhr, 13:00 Uhr, 14:00 Uhr usw.). Dies entspricht einem Auftrag mit stündlicher Häufigkeit, einer Startzeit mit null Minuten und ohne Zeitplan. Bei wöchentlicher oder monatlicher Häufigkeit würde der Zeitplan dagegen nur einmal in der Woche bzw. nur einmal im Monat ausgeführt.|
|<code>{"minutes":[15]}</code>|Stündliche Ausführung jeweils 15 Minuten nach der vollen Stunde. Der Auftrag wird ab 00:15 Uhr stündlich (also um 1:15 Uhr, 2:15 Uhr usw.) bis 23:15 Uhr ausgeführt.|
|<code>{"hours":[17],"weekDays":["saturday"]}</code>|Wöchentliche Ausführung, immer samstags um 17:00 Uhr|
|<code>{hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 17:00 Uhr|
|<code>{"minutes":[15,45],"hours":[17],"weekDays":["monday","wednesday","friday"]}</code>|Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 17:15 Uhr und um 17:45 Uhr|
|<code>{"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 5:00 Uhr und um 17:00 Uhr|
|<code>{"minutes":[15,45],"hours":[5,17],"weekDays":["monday","wednesday","friday"]}</code>|Wöchentliche Ausführung am Montag, Mittwoch und Freitag, jeweils um 5:15 Uhr, 5:45 Uhr, 17:15 Uhr und um 17:45 Uhr|
|<code>{"minutes":[0,15,30,45], "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|Ausführung an Wochentagen im 15-Minuten-Takt|
|<code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday","tuesday","wednesday","thursday","friday"]}</code>|Ausführung an Wochentagen zwischen 9:00 Uhr und 16:45 Uhr im 15-Minuten-Takt|
|<code>{"weekDays":["sunday"]}</code>|Ausführung an Sonntagen zur Startzeit|
|<code>{"weekDays":["tuesday", "thursday"]}</code>|Ausführung jeweils dienstags und donnerstags zur Startzeit|
|<code>{"minutes":[0],"hours":[6],"monthDays":[28]}</code>|Ausführung um 6:00 Uhr am 28. Tag des Monats (bei monatlicher Häufigkeit)|
|<code>{"minutes":[0],"hours":[6],"monthDays":[-1]}</code>|Ausführung um 6:00 Uhr am letzten Tag des Monats. Wenn Sie einen Auftrag am letzten Tag eines Monats ausführen möchten, verwenden Sie „-1“ anstatt Tag 28, 29, 30 oder 31.|
|<code>{"minutes":[0],"hours":[6],"monthDays":[1,-1]}</code>|Ausführung jeweils am ersten und letzten Tag jedes Monats um 6:00 Uhr|
|<code>{monthDays":[1,-1]}</code>|Ausführung jeweils am ersten und letzten Tag jedes Monats zur Startzeit|
|<code>{monthDays":[1,14]}</code>|Ausführung jeweils am ersten und 14. Tag jedes Monats zur Startzeit|
|<code>{monthDays":[2]}</code>|Ausführung jeweils am zweiten Tag des Monats zur Startzeit|
|<code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|Ausführung am ersten Freitag jedes Monats um 5:00 Uhr|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1}]}</code>|Ausführung am ersten Freitag jedes Monats zur Startzeit|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":-3}]}</code>|Monatliche Ausführung am dritten Freitag vom Ende des Monats zur Startzeit|
|<code>{"minutes":[15],"hours":[5],"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|Ausführung am ersten und letzten Freitag jedes Monats um 5:15 Uhr|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":1},{"day":"friday","occurrence":-1}]}</code>|Ausführung am ersten und letzten Freitag jedes Monats zur Startzeit|
|<code>{"monthlyOccurrences":[{"day":"friday","occurrence":5}]}</code>|Ausführung am fünften Freitag jedes Monats zur Startzeit. Ist in einem Monat kein fünfter Freitag vorhanden, wird der Auftrag nicht ausgeführt, da die Ausführung ausschließlich für den fünften Freitag geplant ist. Soll der Auftrag am letzten Freitag des Monats ausgeführt werden, empfiehlt sich unter Umständen die Verwendung von „-1“ anstelle von „5“.|
|<code>{"minutes":[0,15,30,45],"monthlyOccurrences":[{"day":"friday","occurrence":-1}]}</code>|Ausführung im 15-Minuten-Takt am letzten Freitag des Monats|
|<code>{"minutes":[15,45],"hours":[5,17],"monthlyOccurrences":[{"day":"wednesday","occurrence":3}]}</code>|Ausführung um 5:15 Uhr, 5:45 Uhr, 17:15 Uhr und 17:45 Uhr am dritten Mittwoch jedes Monats|

## Siehe auch
 

 [Was ist Azure Scheduler?](scheduler-intro.md)
 
 [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)

 [Erste Schritte mit dem Scheduler im Azure-Portal](scheduler-get-started-portal.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Azure Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/dn528946)

 [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Hochverfügbarkeit und Zuverlässigkeit von Azure Scheduler](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Ausgehende Authentifizierung von Azure Scheduler](scheduler-outbound-authentication.md)
 
  

<!---HONumber=AcomDC_1210_2015-->