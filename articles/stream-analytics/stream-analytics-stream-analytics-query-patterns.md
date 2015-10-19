<properties
	pageTitle="Abfragemuster in Azure Stream Analytics | Microsoft Azure"
	description="Allgemeine Abfragemuster in Azure Stream Analytics"
	keywords="stream analytics, sample, query, language, guide, patterns"
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
	ms.date="10/05/2015"
	ms.author="jeffstok"/>


# Allgemeine Abfragemuster in Azure Stream Analytics  #

## Einführung ##
Abfragen in Azure Stream Analytics werden in einer SQL-ähnlichen Abfragesprache formuliert, die [hier](https://msdn.microsoft.com/library/azure/dn834998.aspx) dokumentiert ist. In diesem Dokument werden anhand von Praxisbeispielen Lösungen für mehrere allgemeine Abfragemuster veranschaulicht. Das Dokument wird nach und nach mit weiteren Mustern aktualisiert.

## Grundlagen ##

## Konvertierung von Datentypen ##
**Beschreibung**: Definieren der Arten von Eigenschaften im Eingabestream. Beispiel: Das Fahrzeuggewicht ist im Eingabestream als Zeichenfolge angegeben und muss zur Summenbildung in INT konvertiert werden.

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

**Erläuterung**: Verwenden Sie eine CAST-Anweisung für das Gewichtsfeld, um dessen Typ anzugeben. (Eine Liste mit unterstützten Datentypen finden Sie [hier](https://msdn.microsoft.com/library/azure/dn835065.aspx).)

## Musterabgleich mithilfe von „Like“/„Not like“ ##
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

## Logik für verschiedene Fälle/Werte (CASE-Anweisungen) ##
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

## Senden von Daten an mehrere Ausgaben ##
**Beschreibung**: Senden von Daten an mehrere Ausgabeziele über einen einzelnen Auftrag. Beispiel: Analysieren von Daten für eine schwellenwertbasierte Warnung und Archivieren aller Ereignisse im BLOB-Speicher.

**Eingabe**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe 1**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe 2**:

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

**Erläuterung**: Mit der INTO-Klausel wird Stream Analytics mitgeteilt, in welche Ausgabe die Daten aus dieser Anweisung geschrieben werden sollen. Bei der ersten Abfrage werden die eingegangenen Daten an eine Ausgabe namens „ArchiveOutput“ weitergereicht. Bei der zweiten Abfrage werden die Daten nach einer einfachen Aggregierung und Filterung an ein nachgelagertes Warnsystem gesendet. *Hinweis*: Sie können auch die Ergebnisse von CTEs (WITH-Anweisungen) in mehreren Ausgabeanweisungen wiederverwenden. Dies hat zudem den Vorteil, dass weniger Leser für die Eingabequelle geöffnet werden. Beispiel:

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

## Muster ##

## Zählen eindeutiger Werte
**Beschreibung**: Zählen der Anzahl eindeutiger Feldwerte, die im Datenstrom innerhalb eines bestimmten Zeitraums vorkommen. Beispiel: Wie viele individuelle Fahrzeugmarken passieren in einem Zeitraum von zwei Sekunden die Mautstation?

**Eingabe**:

| Make | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Ausgabe**:

| Count | Time |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Lösung**:

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

## Ermitteln, ob ein Wert geändert wurde ##
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

**Erläuterung**: Verwenden Sie „LAG“, um im Eingabestream einen Blick auf das vorherige Ereignis zu werfen und den Make-Wert zu ermitteln. Vergleichen Sie ihn dann mit dem Make-Wert des aktuellen Ereignisses, und geben Sie das Ereignis aus, falls sich die Werte unterscheiden.

## Ermitteln des ersten Ereignisses in einem Zeitraum ##
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

## Ermitteln des letzten Ereignisses in einem Zeitraum ##
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

## Erkennen der Abwesenheit von Ereignissen ##
**Beschreibung**: Überprüfen, ob der Datenstrom einen Wert enthält, der einem bestimmten Kriterium entspricht. Beispiel: Wurde die mautpflichtige Straße in einem Zeitraum von 90 Sekunden von zwei aufeinanderfolgenden Fahrzeugen der gleichen Marke befahren?

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

**Erläuterung**: Verwenden Sie „LAG“, um im Eingabestream einen Blick auf das vorherige Ereignis zu werfen und den Make-Wert zu ermitteln. Vergleichen Sie ihn dann mit dem Make-Wert des aktuellen Ereignisses, geben Sie das Ereignis aus, falls die Werte identisch sind, und rufen Sie mithilfe von „LAG“ Daten zum vorherigen Fahrzeug ab.

## Ermitteln der Dauer einer Bedingung ##
**Beschreibung**: Ermitteln, wie lange eine Bedingung angedauert hat. Beispiel: Aufgrund eines Fehlers wurde für alle Fahrzeuge ein Gewicht von über 20.000 Pfund erfasst, und wir möchten nun ermitteln, wie lange dieser Fehler aufgetreten ist.

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

	SELECT
	    PrevGood.Time AS StartFault,
	    ThisGood.Time AS Endfault,
	    DATEDIFF(second, PrevGood.Time, ThisGood.Time) AS FaultDuraitonSeconds
	FROM
	    Input AS ThisGood TIMESTAMP BY Time
	    INNER JOIN Input AS PrevGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, ThisGood) BETWEEN 1 AND 3600
	    AND PrevGood.Weight < 20000
	    INNER JOIN Input AS Bad TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, Bad) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, Bad, ThisGood) BETWEEN 1 AND 3600
	    AND Bad.Weight >= 20000
	    LEFT JOIN Input AS MidGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, MidGood) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, MidGood, ThisGood) BETWEEN 1 AND 3600
	    AND MidGood.Weight < 20000
	WHERE
	    ThisGood.Weight < 20000
	    AND MidGood.Weight IS NULL

**Erläuterung**: Wir suchen nach zwei gültigen Ereignissen, zwischen denen sich ein ungültiges und kein weiteres gültiges Ereignis befindet. Mit anderen Worten: Bei den beiden Ereignissen handelt es sich jeweils um das erste Ereignis vor bzw. nach mindestens einem ungültigen Ereignis. Zwei gültige Ereignisse mit einem ungültigen Ereignis dazwischen lassen sich problemlos mit zwei JOIN-Klauseln und einer Suche nach dem Muster „gültig -> ungültig -> gültig“ ermitteln. Zu diesem Zweck überprüfen wir das Gewicht und vergleichen die Zeitstempel.

Dank der Erkenntnisse, die wir bei der Verwendung der linken äußeren Verknüpfung zur Ermittlung von NULL-Werten oder zur Prüfung auf nicht vorhandene Ereignisse gewonnen haben, wissen wir, wie wir uns vergewissern können, dass zwischen den beiden gültigen Ereignissen kein gültiges Ereignis aufgetreten ist.

Zusammengenommen erhalten wir „gültig -> ungültig -> gültig“ ohne anderes gültiges Ereignis dazwischen. Nun können wir die Dauer zwischen Beginn und Ende der gültigen Ereignisse berechnen und so die Dauer des Fehlers ermitteln.

## Hier erhalten Sie Hilfe
Um Hilfe zu erhalten, besuchen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).

## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](../stream.analytics.get.started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=Oct15_HO2-->