<properties
	pageTitle="Abfragebeispiele für gängige Verwendungsmuster in Stream Analytics | Microsoft Azure"
	description="Allgemeine Abfragemuster in Azure Stream Analytics"
	keywords="Abfragebeispiele"
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
	ms.workload="big-data"
	ms.date="02/17/2016"
	ms.author="jeffstok"/>


# Abfragebeispiele für gängige Stream Analytics-Verwendungsmuster

## Einführung

Abfragen in Azure Stream Analytics werden in einer SQL-ähnlichen Abfragesprache gestellt, die im [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) dokumentiert ist. Dieser Artikel zeigt anhand von Praxisbeispielen Lösungen für mehrere weit verbreitete Abfragemuster. Das Dokument wird nach und nach mit weiteren Mustern aktualisiert.

## Abfragebeispiel: Konvertierungen von Datentypen
**Beschreibung**: Definieren der Arten von Eigenschaften im Eingabedatenstrom. Beispiel: Das Fahrzeuggewicht ist im Eingabedatenstrom als Zeichenfolge angegeben und muss zur Summenbildung in INT konvertiert werden.

**Eingabe**:

| Make | Time | Weight |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**Ausgabe**:

| Make | Weight |
| --- | --- |
| Honda | 3000 |

**Lösung**:

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**Erläuterung**: Verwenden Sie eine CAST-Anweisung für das Gewichtsfeld (Weight), um dessen Typ anzugeben. (Eine Liste mit unterstützten Datentypen finden Sie [hier](https://msdn.microsoft.com/library/azure/dn835065.aspx).)


## Abfragebeispiel: Musterabgleich mithilfe von „Like“/„Not like“
**Beschreibung**: Sicherstellen, dass ein Feldwert des Ereignisses einem bestimmten Muster entspricht. Beispiel: Zurückgeben von Nummernschildern, die mit „A“ beginnen und mit „9“ enden.

**Eingabe**:

| Make | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Ausgabe**:

| Make | LicensePlate | Time |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC-369 | 2015-01-01T00:00:03.0000000Z |

**Lösung**:

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**Erläuterung**: Verwenden Sie die LIKE-Anweisung, um sicherzustellen, dass der Wert im Feld „LicensePlate“ mit „A“ beginnt, anschließend eine beliebige Zeichenfolge mit null oder mehr Zeichen enthält und dann mit „9“ endet.

## Abfragebeispiel: Logik für verschiedene Fälle/Werte (CASE-Anweisungen)
**Beschreibung**: Angeben einer kriterienbasierten Berechnung für ein Feld. Beispiel: Bereitstellen einer Zeichenfolge, die beschreibt, wie viele Fahrzeuge der gleichen Marke vorbeigefahren sind (mit einem Sonderfall für „1“).

**Eingabe**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Lösung**:

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**Erläuterung**: Mit der CASE-Klausel kann auf der Grundlage einiger Kriterien (in unserem Fall die Fahrzeuganzahl im Aggregierungszeitraum) eine abweichende Berechnung angegeben werden.

## Abfragebeispiel: Senden von Daten an mehrere Ausgaben
**Beschreibung**: Senden von Daten an mehrere Ausgabeziele über einen einzelnen Auftrag. Beispiel: Analysieren von Daten für eine schwellenwertbasierte Warnung und Archivieren aller Ereignisse im BLOB-Speicher.

**Eingabe**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe 1**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe 2**:

| Make | Time | Count |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Lösung**:

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**Erläuterung**: Mit der INTO-Klausel wird Stream Analytics mitgeteilt, in welche Ausgabe die Daten aus dieser Anweisung geschrieben werden sollen. Bei der ersten Abfrage werden die eingegangenen Daten an eine Ausgabe namens „ArchiveOutput“ weitergereicht. Bei der zweiten Abfrage werden die Daten nach einer einfachen Aggregierung und Filterung an ein nachgelagertes Warnsystem gesendet. *Hinweis:* Sie können auch die Ergebnisse von CTEs (WITH-Anweisungen) in mehreren Ausgabeanweisungen wiederverwenden. Dies hat zudem den Vorteil, dass weniger Leser für die Eingabequelle geöffnet werden. Beispiel:

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## Abfragebeispiel: Zählen eindeutiger Werte
**Beschreibung**: Zählen der Anzahl eindeutiger Feldwerte, die im Datenstrom innerhalb eines bestimmten Zeitraums vorkommen. Beispiel: Wie viele individuelle Fahrzeugmarken passieren in einem Zeitraum von zwei Sekunden die Mautstation?

**Eingabe**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe:**

| Count | Time |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Lösung:**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**Erläuterung**: Bei einer ersten Aggregierung werden individuelle Marken innerhalb des Zeitraums ermittelt. Anschließend wird mithilfe einer Aggregierung deren Anzahl ermittelt. Wenn alle eindeutigen Werte in einem Zeitraum den gleichen Zeitstempel erhalten, muss der Zeitraum für die zweite Aggregierung sehr klein sein, damit nicht zwei Zeiträume aus dem ersten Schritt aggregiert werden.

## Abfragebeispiel: Ermitteln, ob ein Wert geändert wurde#
**Beschreibung**: Betrachten eines vorherigen Werts, um zu ermitteln, ob er sich vom aktuellen Wert unterscheidet. Beispiel: Handelt es sich beim aktuellen Fahrzeug auf der mautpflichtigen Straße um die gleiche Marke wie beim vorherigen Fahrzeug?

**Eingabe**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Ausgabe**:

| Make | Time |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Lösung**:

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Erläuterung**: Verwenden Sie „LAG“, um im Eingabedatenstrom einen Blick auf das vorherige Ereignis zu werfen und den Make-Wert zu ermitteln. Vergleichen Sie ihn dann mit dem Make-Wert des aktuellen Ereignisses, und geben Sie das Ereignis aus, falls sich die Werte unterscheiden.

## Abfragebeispiel: Ermitteln des ersten Ereignisses in einem Zeitraum
**Beschreibung**: Ermitteln des jeweils ersten Fahrzeugs in einem Zehn-Minuten-Intervall.

**Eingabe**:

| LicensePlate | Stellen | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Ausgabe**:

| LicensePlate | Stellen | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Lösung**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

Ändern wir nun die Aufgabe, und ermitteln wir jeweils das erste Fahrzeug einer bestimmten Marke in einem Zehn-Minuten-Intervall.

| LicensePlate | Stellen | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Lösung**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## Abfragebeispiel: Ermitteln des letzten Ereignisses in einem Zeitraum
**Beschreibung**: Ermitteln des jeweils letzten Fahrzeugs in einem Zehn-Minuten-Intervall.

**Eingabe**:

| LicensePlate | Stellen | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Ausgabe**:

| LicensePlate | Stellen | Time |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Lösung**:

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**Erläuterung**: Die Abfrage umfasst zwei Schritte: Im ersten Schritt wird der neueste Zeitstempel des jeweiligen Zehn-Minuten-Intervalls gesucht. Im zweiten Schritt werden die Ergebnisse der ersten Abfrage mit dem ursprünglichen Datenstrom zusammengeführt, um nach Ereignissen zu suchen, die dem letzten Zeitstempel des jeweiligen Intervalls entsprechen.

## Abfragebeispiel: Erkennen der Abwesenheit von Ereignissen
**Beschreibung**: Überprüfen, ob der Datenstrom einen Wert enthält, der einem bestimmten Kriterium entspricht. Beispiel: Wurde die mautpflichtige Straße in einem Zeitraum von 90 Sekunden von zwei aufeinanderfolgenden Fahrzeugen der gleichen Marke befahren?

**Eingabe**:

| Make | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF-987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI-345 | 2015-01-01T00:00:04.0000000Z |

**Ausgabe**:

| Make | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Lösung**:

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Erläuterung**: Verwenden Sie „LAG“, um im Eingabedatenstrom einen Blick auf das vorherige Ereignis zu werfen und den Make-Wert zu ermitteln. Vergleichen Sie ihn dann mit dem Make-Wert des aktuellen Ereignisses, geben Sie das Ereignis aus, falls die Werte identisch sind, und rufen Sie mithilfe von „LAG“ Daten zum vorherigen Fahrzeug ab.

## Abfragebeispiel: Ermitteln der Dauer zwischen Ereignissen
**Beschreibung**: Ermitteln der Dauer eines bestimmten Ereignisses beispielsweise mit einem gegebenen Webclickstream, der auf eine Funktion verwendete Zeit ermitteln.

**Eingabe**:
  
| Benutzer | Funktion | Ereignis | Time |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Starten | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | End | 2015-01-01T00:00:08.0000000Z |
  
**Ausgabe**:
  
| Benutzer | Funktion | Dauer |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Lösung**

````
    SELECT
    	[user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
    	Event = 'end'
````

**Erläuterung**: Verwenden Sie die „LAST“-Funktion, um den letzten Zeitpunkt (Time value) mit dem Ereignistyp „Start“ zu ermittelt. Beachten Sie, dass die LAST-Funktion PARTITION BY [user] verwendet, um anzuzeigen, dass das Ergebnis einzeln pro Benutzer berechnet wird. Die Abfrage hat einen maximalen Schwellenwert von einer Stunde für die Zeitdifferenz zwischen Start- und Stoppereignissen, ist aber nach Bedarf konfigurierbar (LIMIT DURATION(hour, 1).

## Abfragebeispiel: Ermitteln der Dauer einer Bedingung
**Beschreibung:** Ermitteln, wie lange eine Bedingung angedauert hat. Beispiel: Aufgrund eines Fehlers wurde für alle Fahrzeuge ein Gewicht von über 20.000 Pfund erfasst, und wir möchten nun ermitteln, wie lange dieser Fehler aufgetreten ist.

**Eingabe**:

| Make | Time | Weight |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Ausgabe**:

| StartFault | EndFault | FaultDurationSeconds |
| --- | --- | --- |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |

**Lösung**:

````
SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN weight < 20000 ) [StartFault],
    [time] [EndFault]
FROM input
WHERE
    [weight] < 20000
    AND LAG(weight) OVER (LIMIT DURATION(hour, 24)) > 20000
````

**Erläuterung**: Verwenden Sie LAG, um den Eingabedatenstrom für 24 Stunden anzusehen und suchen Sie nach Instanzen, bei denen „StartFault“ und „StopFault“ von Gewichten unter 20.000 umgeben sind.

## Beispiel für eine Abfrage: Ausfüllen der fehlenden Werte
**Beschreibung**: Für den Ereignisdatenstrom mit fehlenden Werten erzeugen Sie einen Ereignisdatenstrom mit regelmäßigen Intervallen. Generieren Sie z. B. alle 5 Sekunden ein Ereignis, das den zuletzt angezeigten Datenpunkt meldet.

**Eingabe**:

| t | value |
|--------------------------|-------|
| "2014-01-01T06:01:00" | 1 |
| "2014-01-01T06:01:05" | 2 |
| "2014-01-01T06:01:10" | 3 |
| "2014-01-01T06:01:15" | 4 |
| "2014-01-01T06:01:30" | 5 |
| "2014-01-01T06:01:35" | 6 |

**Ausgabe (erste 10 Zeilen)**:

| windowend | lastevent.t | lastevent.value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Lösung**:

    SELECT
    	System.Timestamp AS windowEnd,
    	TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
    	input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Erklärung**: Diese Abfrage generiert Ereignisse alle 5 Sekunden und gibt das letzte Ereignis aus, das zuvor empfangen wurde. Die Dauer des [springenden Fensters](https://msdn.microsoft.com/library/dn835041.aspx "Springendes Fenster – Azure Stream Analytics") legt fest, wie weit die Abfrage zurückreicht, um das letzte Ereignis zu suchen (in diesem Beispiel 300 Sekunden).


## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0224_2016-->