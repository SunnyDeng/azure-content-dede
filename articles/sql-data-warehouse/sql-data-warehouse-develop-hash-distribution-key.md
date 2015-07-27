<properties
   pageTitle="Hashverteilung und deren Auswirkung auf die Abfrageleistung in SQL Data Warehouse | Microsoft Azure"
   description="Sie erhalten Informationen zu Tabellen mit Hashverteilung und die damit verbundene Auswirkung auf die Abfrageleistung in Azure SQL Data Warehouse für die Entwicklung von Lösungen."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Hashverteilung und deren Auswirkung auf die Abfrageleistung in SQL Data Warehouse

Das Treffen von Entscheidungen zur intelligenten Hashverteilung ist eine der wichtigsten Möglichkeiten zur Verbesserung der Abfrageleistung.

Es gibt drei Hauptfaktoren:

1. Minimieren der Datenverschiebung
2. Vermeiden von Datenschiefe
3. Bereitstellen einer ausgewogenen Ausführung

## Minimieren der Datenverschiebung
Zu einer Datenverschiebung kommt es meist, wenn Tabellen verknüpft werden oder Aggregationen für Tabellen durchgeführt werden. Die Hashverteilung für Tabellen mit einem gemeinsam verwendeten Schlüssel ist eine der effektivsten Methoden zum Verringern dieser Verschiebungen.

Damit die Hashverteilung in Bezug auf die Verringerung von Verschiebungen effektiv ist, müssen die folgenden Kriterien erfüllt sein:

1. Für beide Tabellen muss die Hashverteilung durchgeführt werden, und sie müssen über den gemeinsamen Verteilungsschlüssel verknüpft sein.
2. Die Datentypen der beiden Spalten müssen übereinstimmen.
3. Für die Verknüpfungsspalten muss Folgendes gelten (equi-join): Die Werte in der Spalte der linken Tabelle müssen mit den Werten in der Spalte der rechten Tabelle identisch sein.
4. Die Verknüpfung (Join) ist **kein** `CROSS JOIN`.

> [AZURE.NOTE]Spalten, die in `JOIN`-, `GROUP BY`-, `DISTINCT`- und `HAVING`-Klauseln verwendet werden, sind gute HASH-Spaltenkandidaten. Andererseits eignen sich Spalten in der `WHERE`-Klausel **nicht** gut als HASH-Spaltenkandidaten. Informationen hierzu finden Sie unten im Abschnitt zur ausgewogenen Ausführung.

Datenverschiebungen können sich auch aus Abfragesyntax ergeben (`COUNT DISTINCT` und die `OVER`-Klausel sind gute Beispiele hierfür), wenn diese für Spalten verwendet wird, die keinen Hashverteilungsschlüssel enthalten.

> [AZURE.NOTE]Bei Roundrobin-Tabellen kommt es normalerweise zu Datenverschiebungen. Die Daten in der Tabelle werden auf nicht deterministische Weise zugeordnet und müssen daher zuerst verschoben werden, bevor die meisten Abfragen durchgeführt werden.

## Vermeiden von Datenschiefe
Damit die Hashverteilung effektiv ist, ist es wichtig, dass die gewählte Spalte die folgenden Eigenschaften aufweist:

1. Die Spalte enthält eine hohe Zahl unterschiedlicher Werte.
2. Für die Spalte liegt keine **Datenschiefe** (Data Skew) vor.

Jeder einzelne Wert wird einer Verteilung zugeordnet. Daher ist für die Daten eine angemessene Anzahl von unterschiedlichen Werten erforderlich, um sicherzustellen, dass genügend eindeutige Hashwerte generiert werden. Andernfalls fällt die Hashqualität unter Umständen schlecht aus. Wenn die Anzahl der Verteilungen beispielsweise die Anzahl von unterschiedlichen Werten übersteigt, bleiben einige Verteilungen leer. Dies würde die Leistung beeinträchtigen.

Falls alle Zeilen für die Hashspalte den gleichen Wert enthalten würden, wird dies als **Datenschiefe** bezeichnet. In diesem außergewöhnlichen Fall würde nur ein Hashwert erstellt werden, was dazu führt, dass alle Zeilen in einer einzigen Verteilung enthalten sind. Im Idealfall verfügt jeder unterschiedliche Wert in der Hashspalte über die gleiche Anzahl von Zeilen.

> [AZURE.NOTE]Roundrobin-Tabellen weisen keine Anzeichen von Datenschiefe auf. Dies liegt daran, dass die Daten gleichmäßig über die Verteilungen hinweg gespeichert werden.

## Bereitstellen einer ausgewogenen Ausführung
Eine ausgewogene Ausführung wird erreicht, wenn jede Verteilung die gleiche Menge an Arbeit ausführen muss. Massively Parallel Processing (MPP), also die parallele Massenverarbeitung, ist Teamarbeit. Alle Teilnehmer müssen die Ziellinie überschritten haben, bevor ein Gewinner gekürt werden kann. Wenn jede Verteilung über die gleiche Menge an Arbeit (also zu verarbeitende Daten) verfügt, werden alle Abfragen ungefähr zur selben Zeit abgeschlossen. Dies wird als ausgewogene Ausführung bezeichnet.

Wie wir gesehen haben, kann die Datenschiefe die ausgewogene Ausführung beeinträchtigen. Dies ist aber ebenso für die Auswahl des Hashverteilungsschlüssels der Fall. Wenn eine Spalte ausgewählt wurde, die in der `WHERE`-Klausel einer Abfrage enthalten ist, ist die Wahrscheinlichkeit hoch, dass die Abfrage nicht ausgewogen ist.

> [AZURE.NOTE]Die `WHERE`-Klausel ist normalerweise beim Identifizieren von Spalten hilfreich, die sich am besten für die Partitionierung eignen.

Ein gutes Beispiel für eine Spalte, die in der `WHERE`-Klausel verwendet wird, ist ein Datumsfeld. Datumsfelder sind klassische Beispiele für gut geeignete Partitionierungsspalten, aber es sind häufig auch schlechte Hashverteilungsspalten. Normalerweise gelten Data Warehouse-Abfragen für einen angegebenen Zeitraum, z. B. Tag, Woche oder Monat. Die Hashverteilung nach Datum kann sich unter Umständen negativ auf die Skalierbarkeit und die Leistung ausgewirkt haben. Wenn beispielsweise ein Datumsbereich von einer Woche (also sieben Tagen) angegeben wurde, beträgt die maximale Anzahl von Hashes 7 – einen pro Tag. Dies bedeutet, dass nur sieben unserer Verteilungen Daten enthalten würden. Die restlichen Verteilungen würden keine Daten enthalten. Diese Situation würde zu einer unausgewogenen Abfrageausführung führen, da nur sieben Verteilungen Daten verarbeiten.

> [AZURE.NOTE]Mit Roundrobin-Tabellen wird in der Regel eine ausgewogene Ausführung erzielt. Dies liegt daran, dass die Daten gleichmäßig über die Verteilungen hinweg gespeichert werden.

## Empfehlungen
Um die Leistung und den allgemeinen Abfragedurchsatz zu verbessern, sollten Sie sicherstellen, dass Sie für Ihre Tabellen mit Hashverteilung so weit wie möglich das folgende Muster einhalten:

Hashverteilungsschlüssel:

1. Wird in Ihren Abfragen in `JOIN`-, `GROUP BY`-, `DISTINCT`- oder `HAVING`-Klauseln verwendet.
2. Wird nicht in `WHERE`-Klauseln verwendet.
3. Verfügt über viele unterschiedliche Werte, mindestens 1.000.
4. Verfügt nicht über eine unverhältnismäßig große Zahl von Zeilen, für die sich per Hash eine geringe Zahl von Verteilungen ergibt.
5. Ist als NOT NULL definiert. NULL-Zeilen werden in einer Verteilung zusammengefasst.

## Zusammenfassung

Die Hashverteilung kann wie folgt zusammengefasst werden:

- Die Hashfunktion ist deterministisch. Derselbe Wert wird immer derselben Verteilung zugewiesen.
- Eine Spalte wird als Verteilungsspalte verwendet. Die Hashfunktion verwendet die nominierte Spalte, um die Zeilenzuweisungen für Verteilungen zu berechnen.
- Die Hashfunktion basiert auf dem Typ der Spalte, nicht auf den eigentlichen Werten.
- Die Hashverteilung kann für eine Tabelle in bestimmten Fällen zu einer Tabelle mit Datenschiefe führen.
- Tabellen mit Hashverteilung erfordern beim Auflösen von Abfragen im Allgemeinen weniger Datenverschiebungen, sodass die Abfrageleistung für große Faktentabellen verbessert wird.
- Beachten Sie die Empfehlungen zur Auswahl von Spalten mit Hashverteilung, um den Abfragedurchsatz zu optimieren.

> [AZURE.NOTE]In SQL Data Warehouse ist die Datentypkonsistenz sehr wichtig! Stellen Sie sicher, dass im vorhandenen Schema für eine Spalte einheitlich der gleiche Typ verwendet wird. Dies ist besonders für den Verteilungsschlüssel wichtig. Wenn die Verteilungsschlüssel-Datentypen nicht synchronisiert sind und die Tabellen verknüpft werden, kommt es zu unnötigen Datenverschiebungen. Dies kann teuer werden, wenn die Tabellen groß sind, und es würde zu einer Verschlechterung von Durchsatz und Leistung führen.


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->