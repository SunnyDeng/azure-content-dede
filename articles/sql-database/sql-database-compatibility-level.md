<properties 
	pageTitle="Kompatibilitätsgrade in SQL-Datenbank | Microsoft Azure" 
	description="Es wird beschrieben, wie Sie den Kompatibilitätsgrad für die Datenbank von Azure SQL-Datenbank festlegen, und es wird auf die betroffenen Features eingegangen."
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="genemi"/>


# Kompatibilitätsgrade in SQL-Datenbank


In diesem Thema werden für Azure SQL-Datenbank die Features beschrieben, die Sie mit der Transact-SQL-Anweisung **ALTER DATABASE** auswählen können. Das Konzept der Kompatibilitätsgrade dient zur Reduzierung von Upgraderisiken.


Die meisten Features, die über den Kompatibilitätsgrad aktiviert oder deaktiviert werden, sind Teil des Abfrageoptimierers. Microsoft macht die Aktivierung eines neuen Abfrageoptimierers vom Kompatibilitätsgrad abhängig, wenn Folgendes gilt:


- Der aktualisierte Kontext ändert die Semantik eines früheren Features.
- Für die Abfrageoptimierung besteht eine plausible Chance, dass die Leistung bestimmter SQL-Abfragen (ungewöhnlich, aber legitim) beeinträchtigt wird.


Falls Abfragen nach einem Versionsupgrade Ihres Servers oder der Datenbank von Azure SQL-Datenbank eine schlechtere Leistung aufweisen, haben Sie die Möglichkeit, den Kompatibilitätsgrad zu reduzieren. Mit der niedrigeren Einstellung kann eine enge Gruppe von Verbesserungen des Optimierers deaktiviert werden. Darin ist dann wahrscheinlich auch diejenige enthalten, die sich negativ auf Ihre Abfrage auswirkt.


## Funktionsweise von Kompatibilitätsgraden


Für Microsoft SQL Server werden schon seit Jahren einstellbare Kompatibilitätsgrade verwendet. Ab Version V12 werden auch für Azure SQL-Datenbank Kompatibilitätsgrade eingeführt. Die Aktivierung einiger neuer Features wird jeweils über die Kompatibilitätsgradeinstellung in der Datenbank von SQL-Datenbank gesteuert. Die möglichen Einstellungen lauten:


- 110: Die kleinstmögliche Einstellung und somit auch die Einstellung, bei der die meisten Features ausgeschlossen werden.
- 120: Entspricht etwa SQL-Datenbank V11 (`SELECT @@version;` gibt also **11.**0.0.0 zurück).
 - Die Version V11 wurde auch als „SAWA V2“ von SQL-Datenbank bezeichnet.
 - 120 ist der höchste Wert in Microsoft SQL Server 2014.
- 130: Entspricht etwa V12 (`SELECT @@version;` gibt also **12.**0.0.0 zurück).
 - 130 ist der höchste Wert in Microsoft SQL Server 2016.


Die Einstellung 120 bedeutet beispielsweise, dass die Datenbank Zugriff auf die Teilmenge der Features hat, die bei Kompatibilitätsgrad 120 aktiviert werden, *plus* die Features, die bei niedrigeren Einstellungen als 110 aktiviert werden. Bei der Einstellung 120 hat die Datenbank keinen Zugriff auf die Features von Kompatibilitätsgrad 130.


## Lesen oder Festlegen des Kompatibilitätsgrads


### Lesen des Kompatibilitätsgrads


```
SELECT d.name, d.compatibility_level
	FROM sys.databases AS d
	WHERE d.name = db_name();
```


### Festlegen des Kompatibilitätsgrads


```
ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL = 130;
```


Weitere Informationen finden Sie unter:


- [ALTER TABLE-Kompatibilitätsgrad (Transact-SQL)](http://msdn.microsoft.com/library/bb510680.aspx)
- [sys.databases (Transact-SQL)](http://msdn.microsoft.com/library/ms178534.aspx)


## Tabellen im Arbeitsspeicher und Columnstore-Indizes


Ein Großteil der Kompatibilitätsgradfeatures, die bei den Einstellungen 120 und 130 aktiv werden, bezieht sich auf Tabellen und Indizes *im Arbeitsspeicher*. Folgende Elemente sind daher wichtig:


- Speicheroptimierte Tabellen
- ColumnStore-Indizes


Wenn es um Elemente im Arbeitsspeicher geht, hat Kompatibilitätsgrad 130 folgende Vorteile:


- Für den Abfrageoptimierer ist das Verarbeiten einer größeren Zahl von Abfragen im *Batch*modus aktiviert
 - Der Batchmodus ist schneller als der *Zeilen*modus, wenn sehr viele Zeilen verarbeitet werden müssen
- Verwendung des **SORT**-Operators
- Verwendung von Window-Aggregaten


Weitere Informationen zu Tabellen im Arbeitsspeicher und Columnstore-Indizes finden Sie unter:


- [Speicheroptimierte Tabellen](http://msdn.microsoft.com/library/dn133165.aspx)
- [CREATE CLUSTERED COLUMNSTORE INDEX (Transact-SQL)](http://msdn.microsoft.com/library/dn511016.aspx)
- [Beschreibung von Columnstore-Indizes](http://msdn.microsoft.com/library/gg492088.aspx)


## Allgemeine Features, die mindestens Kompatibilitätsgrad 130 erfordern


Bei der Kardinalitätsschätzung (Cardinality Estimator, CE) werden Statistiken zur ungefähren Anzahl von Zeilen einer Tabelle generiert und gespeichert. Kardinalitätsinformationen werden in vielen Bereichen des Abfrageoptimierers verwendet.


Die vom CE verwendeten Algorithmen wurden verbessert. Verbesserungen führen zu Veränderungen, und Veränderungen können für ungewöhnliche Spezialfälle einen Rückschritt bedeuten.


| 130 ist die mindestens<br/>erforderliche<br/>Einstellung | Abfragebereich,<br/>Allgemein | Details zur<br/>Verbesserung<br/>des Abfrageplans |
| :-- | :-- | :-- |
| 130 | Kardinalitätsschätzung (Cardinality Estimator, CE) | Anpassungen der Kardinalitätsschätzung gegenüber der früheren Version bei Einstellung 120.<br/><br/>Im Abfrageplan sehen Sie ggf. Folgendes: **CardinalityEstimationModelVersion="130"**<br/><br/>** Ablaufverfolgungsflag ** [**9481**](http://www.sqlservergeeks.com/sql-server-2014-trace-flags-9481/) kann aktiviert werden, um den CE von Einstellung 120 zu verwenden, wenn Ihre Datenbank über die Einstellung 130 verfügt.<br/><br/>**Ablaufverfolgungsflag** [**4199**](http://support.microsoft.com/kb/974006) kann – bei Einstellung der Datenbank auf Kompatibilitätsgrad 130 – auf „Off“ festgelegt werden, um Hotfixes für den Abfrageoptimierer zu deaktivieren. Das Flag gilt nur für Hotfixes, die implementiert werden, nachdem die Preview-Phase für Kompatibilitätsgrad 130 vollständig abgeschlossen ist und den Status „Allgemeine Verfügbarkeit“ (General Availability, GA) erreicht hat. Ausführliche Informationen finden Sie unter:<br/><br/>● [DBCC TRACEON](http://msdn.microsoft.com/library/ms187329.aspx) |
| 130 | Parallele Abfragepläne für Tabellen im Arbeitsspeicher | Für Abfragen können mehrere Threads verwendet werden, und sie können parallel ausgeführt werden, wenn sie für eine Tabelle im Arbeitsspeicher vorliegen. Dies ist eine Tabelle, die mit der Klausel **MEMORY\_OPTIMIZED = YES** erstellt wurde. Dank der Parallelität können Abfragen schneller ausgeführt werden.<br/><br/>Diese Verbesserung wird für normale Transact-SQL-Verfahren und vom Benutzer gespeicherte Prozeduren unterstützt. Sie wird aber nicht für systemeigene gespeicherte Prozeduren unterstützt, die zu einer DLL kompiliert wurden. |


## Columnstore-Indexfeatures, die mindestens Kompatibilitätsgrad 130 erfordern


Bei Kompatibilitätsgrad 130 können die Verbesserungen des Abfrageoptimierers zu einem neuen Abfrageplan führen. Für geänderte Pläne, die einen Columnstore-Index umfassen, ist die Änderung in der Regel mit einer der folgenden Auswirkungen verbunden:


- Reduzierung in den Fällen, in denen Daten für einen weiteren untergeordneten Vorgang kopiert werden müssen
- Wechsel von der *Zeilen*verarbeitung zur ***Batch***verarbeitung für Vorgänge wie die Sortierung


In den meisten Fällen verbessert die Änderung des Plans die Abfragenleistung mit folgenden Mitteln:


- Parallele Einfügevorgänge in einen Columnstore-Index
 - Kompatibilitätsgrad 130 bietet kein paralleles Scannen von nicht gruppierten Indizes
- Vermehrte Verwendung der **tempdb**-Datenbank


| 130 ist die mindestens<br/>erforderliche<br/>Einstellung | Abfragebereich,<br/>Columnstore-Index | Details zur<br/>Verbesserung<br/>des Abfrageplans |
| :-- | :-- | :-- |
| 130 | Funktionsabfragen | In den folgenden Fällen wird durch den Wechsel in den Batchmodus die Leistung verbessert:<br/><br/>• Sortierung ist enthalten.<br/><br/>• Aggregate mit *mehreren* einzelnen Funktionen<br/>(eine Funktion aus jeweils zwei unterschiedlichen Aufzählungspunkten der folgenden Liste):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT** *oder* **COUNT\_BIG**<br/>&nbsp;&nbsp;&nbsp;▫ **AVG** *oder* **SUM**<br/>&nbsp;&nbsp;&nbsp;▫ **CHECKSUM\_AGG**<br/>&nbsp;&nbsp;&nbsp;▫ **STDEV** *oder* **STDEVP**<br/><br/>• Window-Aggregatfunktionen<br/>(Beschreibung [hier bei MSDN](http://msdn.microsoft.com/library/ms189461.aspx) und [hier von Kathi Kellenberger](http://www.bidn.com/blogs/KathiKellenberger/sql-server/4397/what-is-a-window-aggregate-function)):<br/>&nbsp;&nbsp;&nbsp;▫ **COUNT**, **COUNT\_BIG**, **SUM**, **AVG**, **MIN**, **MAX**, **CLR**<br/><br/>• [Benutzerdefinierte](http://msdn.microsoft.com/library/ms131057.aspx) Window-Aggregate:<br/>&nbsp;&nbsp;&nbsp;▫ [**CHECKSUM\_AGG**](http://msdn.microsoft.com/library/ms188920.aspx), [**STDEV**](http://msdn.microsoft.com/library/ms190474.aspx), [**STDEVP**](http://msdn.microsoft.com/library/ms176080.aspx), [**VAR**](http://msdn.microsoft.com/library/ms186290.aspx), [**VARP**](http://msdn.microsoft.com/library/ms188735.aspx), [**GROUPING**](http://msdn.microsoft.com/library/ms178544.aspx)<br/><br/>• Analysefunktionen für Window-Aggregate:<br/>&nbsp;&nbsp;&nbsp;▫ [**LAG**](http://msdn.microsoft.com/library/hh231256.aspx), [**LEAD**](http://msdn.microsoft.com/library/hh213125.aspx), [**FIRST\_VALUE**](http://msdn.microsoft.com/library/hh213018.aspx), [**LAST\_VALUE**](http://msdn.microsoft.com/library/hh231517.aspx), [**PERCENTILE\_CONT**](http://msdn.microsoft.com/library/hh231473.aspx), [**PERCENTILE\_DISC**](http://msdn.microsoft.com/library/hh231327.aspx), [**CUME\_DIST**](http://msdn.microsoft.com/library/hh231078.aspx), [**PERCENT\_RANK**](http://msdn.microsoft.com/library/hh213573.aspx) |
| 130 | Serieller Singlethread-Abfrageplan | Eine Abfrage, die über einen einzelnen Thread ausgeführt wird, kann im Batchmodus ausgeführt werden. Dadurch kann die Abfrage schneller werden.<br/><br/>Ein Abfrageplan kann als Singlethread-Abfrage entworfen werden, oder eine Abfrage kann unter **MAXDOP 1** ausgeführt werden. |
| 130 | Paralleles Einfügen | Der Abfrageplan kann mehrere Einfügevorgänge parallel durchführen.<br/<br/>Im [Beispiel](#ExampleQueryParallelCciByCompatLevel) weiter unten in diesem Thema wird diese Parallelität veranschaulicht. |
| 130 | Anti-Semi Join | Dieser Operator kann nun im Batchmodus ausgeführt werden. |


## Allgemeine Features, die mindestens Kompatibilitätsgrad 120 erfordern


| 120 ist die mindestens<br/>erforderliche<br/>Einstellung | Abfragebereich,<br/>Allgemein | Details zur<br/>Verbesserung<br/>des Abfrageplans |
| :-- | :-- | :-- |
| 120 | [Ändern von speicheroptimierten Tabellen](http://msdn.microsoft.com/library/dn269114.aspx) | Ermöglicht die Ausführung von **ALTER TABLE**-Transact-SQL-Vorgängen für Tabellen, für die **MEMORY\_OPTIMIZED = YES** gilt.<br/><br/>Die Datenbankanwendung kann weiter ausgeführt werden, aber Vorgänge, bei denen auf die Tabelle zugegriffen wird, werden blockiert, bis der **ALTER TABLE**-Vorgang abgeschlossen ist. |
| 120 | [Erstellen und Verwalten von Speicher für speicheroptimierte Objekte](http://msdn.microsoft.com/library/dn133174.aspx) | Das im Arbeitsspeicher befindliche OLTP-Modul ist in SQL Server integriert. Auf diese Weise können Sie sowohl speicheroptimierte Tabellen (**MEMORY\_OPTIMIZED**) als auch herkömmliche festplattenbasierte Tabellen in derselben Datenbank verwenden. |
| 120 | [Transact-SQL-Unterstützung für OLTP im Arbeitsspeicher](http://msdn.microsoft.com/library/dn133180.aspx) | Einige Transact-SQL-Befehle wurden verbessert, damit sie die Onlinetransaktionsverarbeitung (OLTP) im Arbeitsspeicher unterstützen.<br/><br/>Ein Beispiel hierfür ist das neue **NATIVE\_COMPILATION**-Schlüsselwort des Befehls [CREATE PROCEDURE](http://msdn.microsoft.com/library/ms187926.aspx). |
| 120 | Kardinalitätsschätzung (Cardinality Estimator, CE) | Anpassungen der Kardinalitätsschätzung gegenüber der früheren Version bei Einstellung 110.<br/><br/>Im Abfrageplan sehen Sie ggf. Folgendes: **CardinalityEstimationModelVersion="120"**<br/><br/>Ausführliche Informationen zur Interaktion von **Ablaufverfolgungsflag 4199** mit dem Kompatibilitätsgradwert finden Sie unter [KB 974006](http://support.microsoft.com/kb/974006).|


## Columnstore-Indexfeatures, die mindestens Kompatibilitätsgrad 120 erfordern


In diesem Abschnitt werden die [Features der Columnstore-Indizierung](http://msdn.microsoft.com/library/dn934994.aspx) beschrieben, die bei Kompatibilitätsgrad 120 oder höher aktiviert werden.


| 120 ist der Mindestwert für den<br/>Kompatibilitätsgrad<br/> | Columnstore-Index<br/>Featurename | Columnstore-Index<br/>Featurebeschreibung |
| :-- | :-- | :-- |
| 120 | Momentaufnahme-Isolationsstufe (SI) und<br/><br/>Read Commited-Momentaufnahme-Isolationsstufe (RCSI). | Wenn der Abfrageplan über einen Columnstore-Index verfügt, wird durch SI und RCSI verhindert, dass Daten aus teilweise abgeschlossenen Transaktionen in die Abfrageergebnisse einbezogen werden, ohne das übermäßig viele Sperren erforderlich sind. |
| 120 | Indexdefragmentierung | Gelöschte Zeilen werden ohne explizite Neuerstellung des Index entfernt.<br/><br/>** ALTER INDEX... REORGANIZE** entfernt gelöschte Zeilen aus dem Columnstore-Index, während die Tabelle und der Index online betriebsbereit bleiben. |
| 120 | Verfügbar auf einem [lesbaren sekundären AlwaysOn-Replikat](http://msdn.microsoft.com/library/ff878253.aspx) | Sie können die Leistung für die Betriebsanalyse verbessern, indem Sie Analyseabfragen auf ein sekundäres AlwaysOn-Replikat verlagern. |
| 120 | Aggregat-Pushdown,<br/>während der Tabellenscanphase von Aggregatfunktionen | Verbessert die Leistung, indem Zwischenberechnungen im Abfrageplan früher abgeschlossen werden, sodass weniger Daten in spätere Phasen kopiert werden müssen.<br/><br/>Gilt für **MIN**, **MAX**, **SUM**, **COUNT**, **AVG**.<br/><br/>Gilt nur, wenn der Datentyp über acht Byte oder weniger verfügt (nicht für Zeichenfolgen). |
| 120 | Pushdown von zeichenfolgenbasierten **WHERE**-Klauseln | Die Optimierung des Prädikat-Pushdowns kann zur Beschleunigung von Abfragen führen, bei denen Zeichenfolgendaten vom Typ &#x5b;var&#x5d;char oder n&#x5b;var&#x5d;char verglichen werden. Bei dieser Optimierung:<br/><br/>• Gilt für die allgemeinen Vergleichsoperatoren wie **LIKE**, bei denen Bitmapfilter verwendet werden.<br/><br/>• Funktioniert nur, wenn ein Zeichenfolgenprädikat vorhanden ist.<br/><br/>• Funktioniert mit allen Sortierungen, die vom Produkt unterstützt werden.<br/><br/>Weitere Informationen zu Bitmapfiltern finden Sie in diesem Blog-Beitrag: [Einführung in Bitmapfilter für die Abfragenausführung](http://blogs.msdn.com/b/sqlqueryprocessing/archive/2006/10/27/query-execution-bitmaps.aspx) (in englischer Sprache). |



<a id="ExampleQueryParallelCciByCompatLevel" name="ExampleQueryParallelCciByCompatLevel"></a>


&nbsp;


## Beispiel für einen Wechsel des Abfrageplans nach Kompatibilitätsgrad


Für die **INSERT...SELECT**-Transact-SQL-Anweisung wird in diesem Abschnitt der Wechsel im Abfrageplan von Kompatibilitätsgrad 120 zu 130 veranschaulicht.


#### Quelltabellenschema


Die folgende Tabelle enthält mindestens 300.000 Zeilen. Der erweiterte Abfrageplan entscheidet sich unter Umständen gegen die Parallelität, wenn zu wenig Daten vorhanden sind und sich dies nicht lohnt.


```
CREATE TABLE [dbo].[ccitestoriginal](
	[FinanceKey] [int] NOT NULL,
	[DateKey] [int] NOT NULL,
	[OrganizationKey] [int] NOT NULL,
	[DepartmentGroupKey] [int] NOT NULL,
	[ScenarioKey] [int] NOT NULL,
	[AccountKey] [int] NOT NULL,
	[Amount] [float] NOT NULL,
	[Date] [datetime] NULL
) ON [PRIMARY];
GO

CREATE CLUSTERED COLUMNSTORE INDEX [cci_ccitestoriginal]
	ON [dbo].[ccitestoriginal]
	WITH (DROP_EXISTING = OFF) ON [PRIMARY];
GO
```


#### Skript zum Generieren des Abfrageplans


Wenden Sie diese Schritte wie folgt auf das Transact-SQL-Skript an:


1. Stellen Sie in **Ssms.exe** eine Verbindung mit Ihrer Datenbank her.
2. Fügen Sie das Transact-SQL-Skript in ein **Ssms.exe**-Abfragefenster ein.
3. Bearbeiten Sie das Skript, um sicherzustellen, dass **120** der Wert in der **ALTER DATABASE**-Anweisung ist.
4. Führen Sie nur den Teil des Skripts aus, der sich *vor* der **INSERT INTO**-Anweisung befindet.
5. Aktivieren Sie die Menüoption: **Abfrage** > **Tatsächlichen Ausführungsplan einschließen (STRG+M)**.
6. Führen Sie nur die **INSERT INTO**-Anweisung aus.<br/><br/>
7. Deaktivieren Sie die Menüoption: **Abfrage** > **Tatsächlichen Ausführungsplan einschließen (STRG+M)**.
8. Führen Sie zuletzt nur den Teil des Skripts aus, der *nach* der **INSERT INTO**-Anweisung folgt.
9. Beachten Sie den Abfrageplan für **120** auf der Registerkarte **Ausführungsplan**, die in der Nähe der Registerkarte **Ergebnisse** angeordnet ist.<br/>-- -- -- -- -- --
10. Bearbeiten Sie das Skript so, dass **130** der Wert unter **ALTER DATABASE** ist.
11. Führen Sie das Skript erneut aus, wie dies in den obigen Schritten beschrieben ist.
12. Beachten Sie, dass sich der Abfrageplan für **130** hiervon unterscheidet und Parallelität umfasst.


&nbsp;


```
go
SET NOCOUNT ON;
go

ALTER DATABASE YourDatabaseName SET COMPATIBILITY_LEVEL =
	120
	--130
;
go

DROP TABLE ccitest_into_work;
go

SELECT *
	INTO ccitest_into_work  -- Create an empty copy of the table.
	FROM ccitestoriginal
	WHERE 1=2;
go

CREATE CLUSTERED COLUMNSTORE INDEX ccitest_into_work_cci
	ON ccitest_into_work;
go

SET NOCOUNT OFF;
go
	
	-- Run this INSERT statement alone!
	--
	-- First run all the preceding Transact-SQL statements.
	-- Then run this one INSERT statement alone.
	-- Then run all remaining Transact-SQL statements.

INSERT INTO ccitest_into_work WITH (TABLOCK)
	SELECT TOP 300000 *
		FROM ccitestoriginal;
go

SET NOCOUNT ON;
go

DROP TABLE ccitest_into_work;
go
```


#### Anzeigen der beiden Pläne



<br/>**120:** Dies ist der Abfrageplan, wenn der Kompatibilitätsgrad **120** beträgt.


![Plan bei Kompatibilitätsgrad 120][1-Plan-at-level-120]


<br/>**130:** Dies ist der Abfrageplan, wenn der Kompatibilitätsgrad **130** beträgt.


Der Plan mit **130** verfügt über *Parallelität*, während dies für den Plan mit **120** nicht der Fall ist.


Die Anzeige dieses Plans in **Ssms.exe** ist relativ breit. Aus Gründen der besseren Darstellung ist der Screenshot hier in zwei Teile aufgeteilt. Der zweite Teil ist die Fortsetzung des ersten Teils.


![Plan bei Kompatibilitätsgrad 130][2-Plan-at-level-130]


## Standardmäßiger Kompatibilitätsgrad in der Datenbank


Wenn Sie Ihren Azure SQL-Datenbank-Server aktualisieren, z. B. von Version V11 auf V12, bleibt der Kompatibilitätsgrad in jeder Datenbank unverändert. Nach dem Upgrade können Sie den Kompatibilitätsgrad in einer Datenbank erhöhen, indem Sie die **ALTER DATABASE**-Anweisung verwenden.


Alle neu erstellten Datenbanken weisen den maximalen Kompatibilitätsgrad auf, der gemäß SQL-Datenbank-Version zulässig ist.



<!-- Image references. -->

[1-Plan-at-level-120]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b-120.png

[2-Plan-at-level-130]: ./media/sql-database-compatibility-level/sql-db-compat-level-query-plan-b12-130.png

<!---HONumber=Oct15_HO3-->