<properties 
   pageTitle="Verwenden von U-SQL-Fensterfunktionen für Azure Data Lake Analytics-Aufträge| Azure" 
   description="Erfahren Sie, wie Sie U-SQL-Fensterfunktionen verwenden." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>


# Verwenden von U-SQL-Funktionen für Azure Data Lake Analytics-Aufträge  

Fensterfunktionen wurden 2003 in den ISO/ANSI SQL-Standard eingeführt. U-SQL übernimmt eine Teilmenge der Fensterfunktionen gemäß der Definition durch den ANSI SQL-Standard.

Fensterfunktionen werden verwendet, um Berechnungen in Gruppen von Zeilen durchzuführen, die *Fenster* genannt werden. Fenster werden von der OVER-Klausel definiert. Fensterfunktionen bieten für verschiedene wichtige Szenarien Lösungen auf überaus effiziente Weise.

In diesem Tutorial werden zwei Beispieldatasets verwendet, mit deren Hilfe Sie ein Beispielszenario durchlaufen, in dem Sie Fensterfunktionen anwenden können. Weitere Informationen finden Sie in der [U-SQL-Referenz](http://go.microsoft.com/fwlink/p/?LinkId=691348).

Die Fensterfunktionen sind in die folgenden Kategorien unterteilt:

- [Berichtsaggregationsfunktionen](#reporting-aggregation-functions), wie z. B. SUM oder AVG
- [Rangfolgefunktionen](#ranking-functions), wie z. B. DENSE\_RANK, ROW\_NUMBER, NTILE und RANK
- [Analysefunktionen](#analytic-functions), wie z. B. Summenverteilung, Perzentile oder Zugriffe auf Daten in einer vorherigen Zeile im selben Resultset ohne Selbstverknüpfung

**Voraussetzungen:**

- Durchlaufen Sie die folgenden beiden Tutorials:

    - [Erste Schritte mit Azure Data Lake-Tools für Visual Studio](data-lake-analytics-use-data-lake-tools.md)
    - [Erste Schritte mit U-SQL für Azure Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-get-started.md).
- Erstellen Sie ein Data Lake Analytic-Konto gemäß den Anweisungen unter [Erste Schritte mit Azure Data Lake-Tools für Visual Studio](data-lake-analytics-use-data-lake-tools.md).
- Erstellen Sie ein Visual Studio-U-SQL-Projekt gemäß den Anweisungen unter [Erste Schritte mit U-SQL für Azure Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-get-started.md).

## Beispieldatasets

In diesem Tutorial werden zwei Datasets verwendet:

- QueryLog 

    „QueryLog“ enthält eine Übersicht, wonach Benutzer in der Suchmaschine gesucht haben. Jedes Abfrageprotokoll enthält Folgendes:
    
        - Query - What the user was searching for.
        - Latency - How fast the query came back to the user in milliseconds.
        - Vertical - What kind of content the user was interested in (Web links, Images, Videos).
    
    Kopieren Sie das folgende Skript, und fügen Sie es in Ihr U-SQL-Projekt ein, um das Rowset „QueryLog“ zu erstellen:
    
        @querylog = 
            SELECT * FROM ( VALUES
                ("Banana"  , 300, "Image" ),
                ("Cherry"  , 300, "Image" ),
                ("Durian"  , 500, "Image" ),
                ("Apple"   , 100, "Web"   ),
                ("Fig"     , 200, "Web"   ),
                ("Papaya"  , 200, "Web"   ),
                ("Avocado" , 300, "Web"   ),
                ("Cherry"  , 400, "Web"   ),
                ("Durian"  , 500, "Web"   ) )
            AS T(Query,Latency,Vertical);
    
    In der Praxis sind die Daten wahrscheinlich in einer Datendatei gespeichert. Auf diese Daten in einer Datei mit Tabstopptrennzeichen wird über den folgenden Code zugegriffen:
    
        @querylog = 
        EXTRACT 
            Query    string, 
            Latency  int, 
            Vertical string
        FROM "/Samples/QueryLog.tsv"
        USING Extractors.Tsv();

- Employees

    Das Dataset „Employees“ enthält die folgenden Felder:
   
        - EmpID - Employee ID.
        - EmpName  Employee name.
        - DeptName - Department name. 
        - DeptID - Deparment ID.
        - Salary - Employee salary.

    Kopieren Sie das folgende Skript, und fügen Sie es in Ihr U-SQL-Projekt ein, um das Rowset „Employees“ zu erstellen:

        @employees = 
            SELECT * FROM ( VALUES
                (1, "Noah",   "Engineering", 100, 10000),
                (2, "Sophia", "Engineering", 100, 20000),
                (3, "Liam",   "Engineering", 100, 30000),
                (4, "Emma",   "HR",          200, 10000),
                (5, "Jacob",  "HR",          200, 10000),
                (6, "Olivia", "HR",          200, 10000),
                (7, "Mason",  "Executive",   300, 50000),
                (8, "Ava",    "Marketing",   400, 15000),
                (9, "Ethan",  "Marketing",   400, 10000) )
            AS T(EmpID, EmpName, DeptName, DeptID, Salary);
    
    Die folgende Anweisung veranschaulicht das Erstellen des Rowsets, indem es aus einer Datendatei extrahiert wird.
    
        @employees = 
        EXTRACT 
            EmpID    int, 
            EmpName  string, 
            DeptName string, 
            DeptID   int, 
            Salary   int
        FROM "/Samples/Employees.tsv"
        USING Extractors.Tsv();

Wenn Sie die Beispiele im Tutorial testen, müssen Sie die Rowsetdefinitionen einschließen. U-SQL erfordert, dass Sie nur die Rowsets definieren, die verwendet werden. Einige Beispiele benötigen nur ein Rowset.

Außerdem müssen Sie die folgende Anweisung hinzufügen, um das Ergebnisrowset in einer Datendatei auszugeben:

    OUTPUT @result TO "/wfresult.csv" 
        USING Outputters.Csv();
 
 Die meisten Beispiele verwenden die Variable **@result** für die Ergebnisse.

## Vergleichen von Fensterfunktionen mit einer Gruppierung

Fensterfunktionen und Gruppierung sind zwar konzeptionell verwandt, aber auch unterschiedlich. Es empfiehlt sich, diese Beziehung zu verstehen.

### Verwenden von Aggregation und Gruppierung

Die folgende Abfrage verwendet eine Aggregation, um die Summe der Gehälter aller Mitarbeiter zu berechnen:

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;
    
>[AZURE.NOTE]Anweisungen zum Testen und Überprüfen der Ausgabe finden Sie unter [Erste Schritte mit U-SQL für Azure Data Lake Analytics-Aufträge](data-lake-analytics-u-sql-get-started.md).

Das Ergebnis ist eine einzelne Zeile mit einer einzelnen Spalte. $165000 ist die Summe der Gehaltswerte in der gesamten Tabelle.

|TotalSalary
|-----------
|165000

>[AZURE.NOTE]Wenn Sie noch nicht mit Fensterfunktionen vertraut sind, ist es hilfreich, die Werte in den Ausgaben zu speichern.

Die folgende Anweisung verwendet die GROUP BY-Klausel, um die gesamten Gehaltskosten für jede Abteilung zu berechnen:

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Die Ergebnisse sind wie folgt:

|DeptName|SalaryByDept
|--------|------------
|Engineering|60000
|HR|30000
|Executive|50000
|Marketing|25000

Die Summe der Spalte „SalaryByDept“ ist $165000, was dem Betrag im letzten Skript entspricht.
 
In beiden Fällen gibt es weniger Ausgabezeilen als Eingabezeilen:
 
- Ohne GROUP BY reduziert die Aggregation alle Zeilen zu einer einzelnen Zeile. 
- Mit GROUP BY gibt es N Ausgabezeilen, wobei N die Anzahl unterschiedliche Werte ist, die in den Daten enthalten sind. In diesem Fall sind 4 Zeilen in der Ausgabe enthalten.

###  Verwenden einer Fensterfunktion

Die OVER-Klausel im folgenden Beispiel ist leer. Dadurch wird das „Fenster“ so definiert, dass es alle Zeilen aufnehmen soll. SUM wird in diesem Beispiel auf die OVER-Klausel angewendet, die vorausgeht.

Sie können diese Abfrage so lesen: „Die Summe der Gehälter über ein Fenster aller Zeilen“.

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

Im Gegensatz zu GROUP BY ist die Anzahl von Aus- und Eingabezeilen identisch:

|EmpName|TotalAllDepts
|-------|--------------------
|Noah|165000
|Sophia|165000
|Liam|165000
|Emma|165000
|Jacob|165000
|Olivia|165000
|Mason|165000
|Ava|165000
|Ethan|165000


Der Wert 165000 (die Summe aller Gehälter) wird in jeder Ausgabezeile platziert. Die Summe stammt aus dem „Fenster“ aller Zeilen, weshalb sie alle Gehälter einschließt.

Das nächste Beispiel veranschaulicht das Optimieren des „Fensters“, um alle Mitarbeiter, die Abteilung und die Gesamtgehaltskosten der Abteilung aufzulisten. PARTITION BY wird der OVER-Klausel hinzugefügt.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Die Ergebnisse sind wie folgt:

|EmpName|DeptName|SalaryByDep
|-------|--------|-------------------
|Noah|Engineering|60000
|Sophia|Engineering|60000
|Liam|Engineering|60000
|Mason|Executive|50000
|Emma|HR|30000
|Jacob|HR|30000
|Olivia|HR|30000
|Ava|Marketing|25000
|Ethan|Marketing|25000

Wiederum entspricht die Anzahl der Eingabezeilen der Anzahl der Ausgabezeilen. Jede Zeile enthält das Gesamtgehalt der jeweiligen Abteilung.




## Berichtsaggrationsfunktionen

Fensterfunktionen unterstützen auch die folgenden Aggregate:

- COUNT
- SUM
- MIN
- MAX
- AVG
- STDEV
- VAR

Die Syntax ist:

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Hinweis:

- Standardmäßig werden NULL-Werte von allen Aggregatfunktionen außer COUNT ignoriert.
- Wenn Aggregatfunktionen zusammen mit der OVER-Klausel angegeben werden, ist die ORDER BY-Klausel in der OVER-Klausel nicht zulässig.

### Verwenden von SUM

Das folgende Beispiel fügt das Gesamtgehalt nach Abteilung den einzelnen Eingabezeilen hinzu:
 
    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Hier sehen Sie die Ausgabe:

|EmpID|EmpName|DeptName|DeptID|Salary|TotalByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Engineering|100|10000|60000
|2|Sophia|Engineering|100|20000|60000
|3|Liam|Engineering|100|30000|60000
|7|Mason|Executive|300|50000|50000
|4|Emma|HR|200|10000|30000
|5|Jacob|HR|200|10000|30000
|6|Olivia|HR|200|10000|30000
|8|Ava|Marketing|400|15000|25000
|9|Ethan|Marketing|400|10000|25000

### Verwenden von COUNT

Das folgende Beispiel fügt jeder Zeile ein zusätzliches Feld hinzu, um die Gesamtanzahl von Mitarbeitern in jeder Abteilung anzuzeigen.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Das Ergebnis:

|EmpID|EmpName|DeptName|DeptID|Salary|CountByDept
|-----|-------|--------|------|------|-----------
|1|Noah|Engineering|100|10000|3
|2|Sophia|Engineering|100|20000|3
|3|Liam|Engineering|100|30000|3
|7|Mason|Executive|300|50000|1
|4|Emma|HR|200|10000|3
|5|Jacob|HR|200|10000|3
|6|Olivia|HR|200|10000|3
|8|Ava|Marketing|400|15000|2
|9|Ethan|Marketing|400|10000|2


### Verwenden von MIN und MAX

Das folgende Beispiel fügt jeder Zeile ein zusätzliches Feld hinzu, um das niedrigste Gehalt in jeder Abteilung anzuzeigen:

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Die Ergebnisse:

|EmpID|EmpName|DeptName|DeptID|Salary|MinSalary
|-----|-------|--------|------|-------------|----------------
|1|Noah|Engineering|100|10000|10000
|2|Sophia|Engineering|100|20000|10000
|3|Liam|Engineering|100|30000|10000
|7|Mason|Executive|300|50000|50000
|4|Emma|HR|200|10000|10000
|5|Jacob|HR|200|10000|10000
|6|Olivia|HR|200|10000|10000
|8|Ava|Marketing|400|15000|10000
|9|Ethan|Marketing|400|10000|10000

Ersetzen Sie MIN durch MAX, und wiederholen Sie den Vorgang.


## Rangfolgefunktionen

Rangfolgefunktionen geben gemäß der Definition der Klauseln PARTITION BY und OVER für jede Zeile in jeder Partition einen Rangfolgewert (LONG) zurück. Die Reihenfolge der Ränge wird über ORDER BY in der OVER-Klausel gesteuert.

Die folgenden Rangfolgefunktionen werden unterstützt:

- RANK
- DENSE\_RANK 
- NTILE
- ROW\_NUMBER

**Syntax:**

	[ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
	    OVER (
	        [PARTITION BY <identifier, > …[n]]
	        [ORDER BY <identifier, > …[n] [ASC|DESC]] 
	) AS <alias>

- Die ORDER BY-Klausel ist für Rangfolgefunktionen optional. Falls ORDER BY angegeben ist, wird damit die Reihenfolge für die Rangfolge bestimmt. Falls ORDER BY nicht angegeben ist, werden Werte von U-SQL auf Grundlage der Lesereihenfolge von Datensätzen zugewiesen. Dies führt zu einem nicht deterministischen Wert für ROW\_NUMBER, RANK und DENSE\_RANK für den Fall, dass die ORDER BY-Klausel nicht angegeben wurde.
- NTILE erfordert einen Ausdruck, der in eine positive ganze Zahl ausgewertet wird. Diese Zahl gibt die Anzahl der Gruppen an, in die jede Partition unterteilt werden muss. Dieser Bezeichner wird nur bei der Rangfolgefunktion NTILE verwendet. 

Weitere Informationen zur OVER-Klausel finden Sie in der [U-SQL-Referenz]().

ROW\_NUMBER, RANK und DENSE\_RANK weisen Zeilen in einem Fenster Werte zu. Anstatt diese Funktionen separat zu behandeln, empfiehlt es sich zu prüfen, wie sie auf die gleiche Eingabe reagieren.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;
        
Beachten Sie, dass die OVER-Klauseln identisch sind. Das Ergebnis:

|Abfrage|Latency:int|Vertical|RowNumber|Rank|DenseRank
|-----|-----------|--------|--------------|---------|--------------
|Banana|300|Image|1|1|1
|Cherry|300|Image|2|1|1
|Durian|500|Image|3|3|2
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|3|2|2
|Fig|300|Web|4|4|3
|Cherry|400|Web|5|5|4
|Durian|500|Web|6|6|5

### ROW\_NUMBER

In jedem Fenster (Vertical, entweder „Image“ oder „Web“) wird der Zeilenwert nach „Latency“ sortiert um 1 erhöht.

![U-SQL-Fensterfunktion ROW\_NUMBER](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### RANK

Im Unterschied zu ROW\_NUMBER() berücksichtigt RANK() den Wert von „Latency“, der in der ORDER BY-Klausel für das Fenster angegeben ist.

RANK beginnt mit (1,1,3), da die beiden ersten Werte für „Latency“ identisch sind. Dann ist der nächste Wert 3, da der „Latency“-Wert in 500 geändert wurde. Wichtig ist der Hinweis, dass obwohl doppelte Werte denselben Rang erhalten, der RANK-Wert zum nächsten ROW\_NUMBER-Wert „springt“. Dieses Muster wird mit der Sequenz (2,2,4) für (Vertical, Web) wiederholt.

![U-SQL-Fensterfunktion RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### DENSE\_RANK
	
DENSE\_RANK entspricht nahezu RANK, außer dass kein „Sprung“ zur nächsten ROW\_NUMBER, sondern zum nächsten Wert in der Sequenz erfolgt. Sehen Sie sich die Sequenzen (1,1,2) und (2,2,3) im Beispiel an.

![U-SQL-Fensterfunktion DENSE\_RANK](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### Anmerkungen

- Wenn ORDER BY nicht angegeben ist, wird die Rangfolgefunktion ohne bestimmte Reihenfolge auf das Rowset angewendet. Dies führt zu einem nicht deterministischen Verhalten dahingehend, wie die Rangfolgefunktion angewendet wird.
- Es gibt keine Garantie, dass die von einer Abfrage mit ROW\_NUMBER zurückgegebenen Zeilen bei jeder Ausführung exakt gleich sortiert werden, es sei denn, die folgenden Bedingungen treffen zu.

	- Die Werte der partitionierten Spalte sind eindeutig.
	- Die Werte der ORDER BY-Spalten sind eindeutig.
	- Kombinationen von Werten in der Partitionsspalte und den ORDER BY-Spalten sind eindeutig.

### NTILE

Mit NTILE werden die Zeilen in einer sortierten Partition auf eine angegebene Anzahl von Gruppen verteilt. Die Gruppen sind beginnend mit 1 nummeriert.


Im folgenden Beispiel wird die Menge der Zeilen in jeder Partition (Vertical) in 4 Gruppen in der Reihenfolge der Abfragelatenz geteilt und die Gruppennummer für jede Zeile zurückgegeben.

(Image, Vertical) hat 3 Zeilen, weshalb es 3 Gruppen gibt.

(Web, Vertical) hat 6 Zeilen, wobei die zwei zusätzlichen Zeilen auf die ersten beiden Gruppen verteilt werden. Damit gibt es zwei Zeilen in Gruppe 1 und 2 und nur eine Zeile in Gruppe 3 und 4.

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;
		
Die Ergebnisse:

|Query|Latency|Vertical|Quartile
|-----|-----------|--------|-------------
|Banana|300|Image|1
|Cherry|300|Image|2
|Durian|500|Image|3
|Apple|100|Web|1
|Fig|200|Web|1
|Papaya|200|Web|2
|Fig|300|Web|2
|Cherry|400|Web|3
|Durian|500|Web|4

NTILE verwendet den „numgroups“-Parameter. „numgroups“ ist eine positive ganze Zahl (int) oder ein Konstantenausdruck vom Typ „long“, der die Anzahl der Gruppen angibt, in die jede Partition unterteilt werden muss.

- Wenn die Anzahl der Zeilen in der Partition von „numgroups“ gleichmäßig geteilt werden kann, haben die Gruppen dieselbe Größe. 
- Wenn die Anzahl der Zeilen in der Partition von „numgroups“ nicht gleichmäßig geteilt werden kann, sind das Ergebnis Gruppen mit zwei Größen, die sich in einem Element unterscheiden. Größere Gruppen stehen in der von der OVER-Klausel angegebenen Reihenfolge vor kleineren Gruppen. 

Beispiel:

- In 4 Gruppen unterteilte 100 Zeilen: [25, 25, 25, 25]
- In 4 Gruppen unterteilte 102 Zeilen: [26, 26, 25, 25]


### Top N Datensätze pro Partition über RANK, DENSE\_RANK oder ROW\_NUMBER

Viele Benutzer möchten nur die TOP N Zeilen pro Gruppe auswählen. Dies ist mit der herkömmlichen GROUP BY-Klausel nicht möglich.

Sie haben am Anfang des Abschnitts zu Rangfolgefunktionen das folgende Beispiel gesehen. Es zeigt nicht die TOP N Datensätze für jede Partition:

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Die Ergebnisse:

|Query|Latency|Vertical|Rank|DenseRank|RowNumber
|-----|-----------|--------|---------|--------------|--------------
|Banana|300|Image|1|1|1
|Cherry|300|Image|1|1|2
|Durian|500|Image|3|2|3
|Apple|100|Web|1|1|1
|Fig|200|Web|2|2|2
|Papaya|200|Web|2|2|3
|Fig|300|Web|4|3|4
|Cherry|400|Web|5|4|5
|Durian|500|Web|6|5|6

### TOP N mit DENSE RANK

Im folgenden Beispiel werden die TOP 3 Datensätze in jeder Gruppe ohne Lücken in der sequenziellen Rangnummerierung von Zeilen in jeder Fensterpartition zurückgegeben.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Die Ergebnisse:

|Query|Latency|Vertical|DenseRank
|-----|-----------|--------|--------------
|Banana|300|Image|1
|Cherry|300|Image|1
|Durian|500|Image|2
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2
|Fig|300|Web|3

### TOP N mit RANK

    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Die Ergebnisse:

|Query|Latency|Vertical|Rank
|-----|-----------|--------|---------
|Banana|300|Image|1
|Cherry|300|Image|1
|Durian|500|Image|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|2


### TOP N mit ROW\_NUMBER

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;
    
    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Die Ergebnisse:
    
|Query|Latency|Vertical|RowNumber
|-----|-----------|--------|--------------
|Banana|300|Image|1
|Cherry|300|Image|2
|Durian|500|Image|3
|Apple|100|Web|1
|Fig|200|Web|2
|Papaya|200|Web|3

### Zuweisen einer global eindeutigen Zeilennummer

Es ist häufig nützlich, jeder Zeile eine global eindeutige Nummer zuzuweisen. Dies ist bei den Rangfolgefunktionen einfach (und effizienter als die Verwendung eines Reducers).

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## Analysefunktionen

Analysefunktionen dienen zum Verstehen der Verteilung von Werten in Fenstern. Das häufigste Szenario für die Verwendung von Analysefunktionen ist die Berechnung von Perzentilen.

**Unterstützte Analysefunktionen für Fenster**

- CUME\_DIST 
- PERCENT\_RANK
- PERCENTILE\_CONT
- PERCENTILE\_DISC

### CUME\_DIST  

CUME\_DIST berechnet die relative Position eines angegebenen Werts in einer Gruppe von Werten. Berechnet wird den Prozentsatz der Abfragen mit einer Latenz kleiner gleich der aktuellen Abfragelatenz im selben „Vertical“. Für eine Zeile R ist bei angenommener aufsteigender Reihenfolge CUME\_DIST von R die Anzahl von Zeilen mit Werten kleiner gleich dem Wert von R dividiert durch die Anzahl von Zeilen, die im Resultset der Partition oder Abfrage ausgewertet werden. CUME\_DIST gibt Zahlen im Bereich 0 < x < = 1 zurück.

Syntax

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

Im folgenden Beispiel wird die CUME\_DIST-Funktion zum Berechnen des Latenzperzentils für jede Abfrage in einem „Vertical“ verwendet.

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Die Ergebnisse:
    
|Query|Latency|Vertical|CumeDist
|-----|-----------|--------|---------------
|Durian|500|Image|1
|Banana|300|Image|0,666666666666667
|Cherry|300|Image|0,666666666666667
|Durian|500|Web|1
|Cherry|400|Web|0,833333333333333
|Fig|300|Web|0,666666666666667
|Fig|200|Web|0,5
|Papaya|200|Web|0,5
|Apple|100|Web|0,166666666666667

Es gibt in der Partition 6 Zeilen mit dem Partitionsschlüssel „Web“ (ab der 4. Zeile nach unten):

- Es gibt 6 Zeilen mit einem Wert kleiner gleich 500, sodass der CUME\_DIST-Wert 6:6 = 1 entspricht.
- Es gibt 5 Zeilen mit einem Wert kleiner gleich 500, sodass der CUME\_DIST-Wert 5:6 = 0,83 entspricht.
- Es gibt 4 Zeilen mit einem Wert kleiner gleich 500, sodass der CUME\_DIST-Wert 5:6 = 0,66 entspricht.
- Es gibt 3 Zeilen mit einem Wert kleiner gleich 500, sodass der CUME\_DIST-Wert 3:6 = 0,5 entspricht. Es gibt zwei Zeilen mit demselben Latenzwert.
- Es gibt eine Zeile mit einem Wert kleiner gleich 500, sodass der CUME\_DIST-Wert 1:6 = 0,16 entspricht. 


**Hinweise zur Verwendung:**

- Gleichwertige Werte werden stets in denselben kumulativen Verteilungswert ausgewertet.
- NULL-Werte werden als die niedrigsten möglichen Werte behandelt.
- Sie müssen die ORDER BY-Klausel angeben, um CUME\_DIST zu berechnen.
- CUME\_DIST ist vergleichbar mit der PERCENT\_RANK-Funktion.

Hinweis: Die ORDER BY-Klausel ist nicht zulässig, wenn auf die SELECT-Anweisung nicht OUTPUT folgt. Daher bestimmt die ORDER BY-Klausel in der OUTPUT-Anweisung die Anzeigereihenfolge des zurückgegebenen Rowsets.


### PERCENT\_RANK

PERCENT\_RANK berechnet den relativen Rang einer Zeile innerhalb einer Gruppe von Zeilen. Mit PERCENT\_RANK können Sie den relativen Rang eines Werts innerhalb eines Resultsets oder einer Partition ermitteln. Der von PERCENT\_RANK zurückgegebene Wertebereich ist größer als 0 und kleiner gleich 1. Im Gegensatz zu CUME\_DIST ist PERCENT\_RANK für die erste Zeile immer 0.
	
Syntax

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Hinweise**

- Die erste Zeile in einer Menge hat den PERCENT\_RANK 0.
- NULL-Werte werden als die niedrigsten möglichen Werte behandelt.
- Sie müssen die ORDER BY-Klausel angeben, um PERCENT\_RANK zu berechnen.
- CUME\_DIST ist vergleichbar mit der PERCENT\_RANK-Funktion. 


Im folgenden Beispiel wird die PERCENT\_RANK-Funktion zum Berechnen des Latenzperzentils für jede Abfrage in einem „Vertical“ verwendet.

Die PARTITION BY-Klausel wird angegeben, um die Zeilen im Resultset gemäß dem „Vertical“ zu partitionieren. Die ORDER BY-Klausel in der OVER-Klausel sortiert die Zeilen in jeder Partition.

Der von der PERCENT\_RANK-Funktion zurückgegebene Wert stellt den Rang der Latenz der Abfragen in einem „Vertical“ als Prozentsatz dar.


    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Die Ergebnisse:

|Query|Latency:int|Vertical|PercentRank
|-----|-----------|--------|------------------
|Banana|300|Image|0
|Cherry|300|Image|0
|Durian|500|Image|1
|Apple|100|Web|0
|Fig|200|Web|0,2
|Papaya|200|Web|0,2
|Fig|300|Web|0,6
|Cherry|400|Web|0,8
|Durian|500|Web|1

### PERCENTILE\_CONT und PERCENTILE\_DISC

Diese beiden Funktionen berechnen ein Perzentil basierend auf einer kontinuierlichen oder diskreten Verteilung der Spaltenwerte.

**Syntax**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric\_literal**: Das zu berechnende Perzentil. Der Wert muss im Bereich von 0,0 bis 1,0 liegen.

WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ]): Gibt eine Liste von numerischen Werten für die Sortierung und Berechnung des Perzentils an. Nur ein Spaltenbezeichner ist zulässig. Der Ausdruck muss in einen numerischen Datentyp ausgewertet werden. Andere Datentypen sind nicht zulässig. Die Standardsortierreihenfolge ist aufsteigend.

OVER ([ PARTITION BY <identifier,>…[n] ] ): Teilt das Eingaberowset in Partitionen gemäß dem Partitionsschlüssel auf, für den die Perzentilfunktion gilt. Weitere Informationen finden Sie im Abschnitt RANGFOLGE dieses Dokuments. Hinweis: Alle NULL-Werte im Dataset werden ignoriert.

**PERCENTILE\_CONT** berechnet ein Perzentil basierend auf einer kontinuierlichen Verteilung der Spaltenwerte. Das Ergebnis wird interpoliert und entspricht ggf. keinem der spezifischen Werte in der Spalte.

**PERCENTILE\_DISC** berechnet ein Perzentil basierend auf einer diskreten Verteilung der Spaltenwerte. Das Ergebnis ist gleich einem bestimmten Wert in der Spalte. Mit anderen Worten gibt PERCENTILE\_DISC im Gegensatz zu PERCENTILE\_CONT stets einen tatsächlichen (ursprünglichen Eingabe-) Wert zurück.

Die Funktionsweise beider Funktionen zeigt das folgende Beispiel, bei dem der Medianwert (Perzentil=0,5) für „Latency“ in jedem „Vertical“ gesucht wird.

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 
        
        FROM @querylog;

Die Ergebnisse:

|Query|Latency:int|Vertical|PercentileCont50|PercentilDisc50
|-----|-----------|--------|-------------------|----------------
|Banana|300|Image|300|300
|Cherry|300|Image|300|300
|Durian|500|Image|300|300
|Apple|100|Web|250|200
|Fig|200|Web|250|200
|Papaya|200|Web|250|200
|Fig|300|Web|250|200
|Cherry|400|Web|250|200
|Durian|500|Web|250|200


Da die Werte für PERCENTILE\_CONT interpoliert werden können, ist der Median für „Web“ 250, obwohl keine Abfrage in diesem „Vertical“ die Latenz 250 hatte.

PERCENTILE\_DISC interpoliert keine Werte, weshalb der Median für „Web“ 200 ist und so tatsächlich einem Wert in den Eingabezeilen entspricht.











## Weitere Informationen

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
- [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Verwenden interaktiver Tutorials zu Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
- [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)
- [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)
- [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-use-portal.md)
- [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-use-powershell.md)
- [Überwachung und Problembehandlung von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Vorschauportals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=Nov15_HO1-->