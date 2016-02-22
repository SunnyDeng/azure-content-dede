<properties 
	pageTitle="Erweiterte Ereignisse in Azure SQL-Datenbank | Microsoft Azure" 
	description="Beschreibt erweiterte Ereignisse (XEvents) in Azure SQL-Datenbank und wie sich Ereignissitzungen im Vergleich zu Microsoft SQL Server geringfügig unterscheiden." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard" 
	editor="" 
	tags=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="genemi"/>


# Erweiterte Ereignisse in Azure SQL-Datenbank


In diesem Thema wird erläutert, wie sich die Implementierung erweiterter Ereignisse in Azure SQL-Datenbank im Vergleich zu erweiterten Ereignissen in Microsoft SQL Server geringfügig unterscheidet.


- SQL-Datenbank V12 wurde das Feature der erweiterte Ereignisse in der zweiten Hälfte des Jahres 2015 hinzugefügt.
- SQL Server bietet erweiterte Ereignisse seit 2008.
- Der Funktionsumfang erweiterter Ereignisse für SQL-Datenbank ist eine stabile Teilmenge der Funktionen in SQL Server. 


*XEvents* ist ein informeller Spitzname, der in Blogs und anderen informellen Quellen mitunter für "erweiterte Ereignisse" verwendet wird.


> [AZURE.NOTE] Ab Oktober 2015 ist das Sitzungsfeature "Erweiterte Ereignisse" in Azure SQL-Datenbank auf Preview-Ebene aktiviert. Das Datum der allgemeinen Verfügbarkeit steht noch nicht fest.
> 
> Auf der Azure-Seite [Dienstupdates](https://azure.microsoft.com/updates/?service=sql-database) finden Sie Beiträge mit Ankündigungen zur allgemeinen Verfügbarkeit.


## Voraussetzungen


Dieses Thema setzt voraus, dass Sie mit Folgendem grundlegend vertraut sind:


- [Azure SQL-Datenbankdienst](https://azure.microsoft.com/services/sql-database/).


- [Erweiterte Ereignisse](http://msdn.microsoft.com/library/bb630282.aspx) in Microsoft SQL Server.
 - Der größte Teil unserer Dokumentation zu erweiterten Ereignissen gilt für SQL Server und SQL-Datenbank.


Vorherige Kenntnisse in Bezug auf die folgenden Elemente ist hilfreich bei der Auswahl der Ereignisdatei als [Ziel](#AzureXEventsTargets):


- [Azure Storage-Dienst](https://azure.microsoft.com/services/storage/)


- PowerShell
 - [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md) – Bietet umfassende Informationen zu PowerShell und zum Azure Storage-Dienst.


## Codebeispiele


In verwandten Themen werden zwei Codebeispiele geboten:


- [Code des Ringpufferziels für erweiterte Ereignisse in SQL-Datenbank](sql-database-xevent-code-ring-buffer.md)
 - Kurzes einfaches Transact-SQL-Skript.
 - Wir betonen im Codebeispielthema, dass wenn Sie mit dem Ringpufferziel fertig sind, Sie dessen Ressourcen durch Ausführen der Anweisung "alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;`" freigeben müssen. Später können Sie eine weitere Ringpufferinstanz durch `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` hinzufügen.


- [Code des Ziels "Ereignisdatei" für erweiterte Ereignisse in SQL-Datenbank](sql-database-xevent-code-event-file.md)
 - Phase 1 ist PowerShell zum Erstellen eines Azure-Speichercontainers.
 - Phase 2 ist Transact-SQL-Code, der den Azure Storage-Container verwendet.


## Transact-SQL-Unterschiede


- Beim Ausführen des [CREATE EVENT SESSION](http://msdn.microsoft.com/library/bb677289.aspx)-Befehls für SQL Server verwenden Sie die **ON SERVER**-Klausel. Doch bei SQL-Datenbank verwenden Sie stattdessen die **ON DATABASE**-Klausel.


- Die **ON DATABASE** -Klausel gilt auch für die Transact-SQL-Befehle [ALTER EVENT SESSION](http://msdn.microsoft.com/library/bb630368.aspx) und [DROP EVENT SESSION](http://msdn.microsoft.com/library/bb630257.aspx).


- Eine bewährte Methode ist das Einschließen der Ereignissitzungsoption von **STARTUP\_STATE = ON** in Ihre Anweisungen **CREATE EVENT SESSION** oder **ALTER EVENT SESSION**.
 - Der Wert **= ON** unterstützt einen automatischen Neustart nach einer Neukonfiguration der logischen Datenbank aufgrund eines Failovers.


## Neue Katalogsichten


Das Feature "Erweiterte Ereignisse" wird von mehreren [Katalogsichten](http://msdn.microsoft.com/library/ms174365.aspx) unterstützt. Katalogsichten informieren Sie über *Metadaten oder Definitionen* von vom Benutzer erstellten Ereignissitzungen in der aktuellen Datenbank. Die Sichten geben keine Informationen zu Instanzen aktiver Ereignissitzungen zurück.


| Name der<br/>Katalogsicht | Beschreibung |
| :-- | :-- |
| **sys.database\_event\_session\_actions** | Gibt eine Zeile für jede Aktion in jedem Ereignis einer Ereignissitzung zurück. |
| **sys.database\_event\_session\_events** | Gibt eine Zeile für jedes Ereignis in einer Ereignissitzung zurück. |
| **sys.database\_event\_session\_fields** | Gibt eine Zeile für jede anpassbare Spalte zurück, die für Ereignisse und Ziele explizit festgelegt wurde. |
| **sys.database\_event\_session\_targets** | Gibt eine Zeile für jedes Ereignisziel einer Ereignissitzung zurück. |
| **sys.database\_event\_sessions** | Gibt eine Zeile für jede Ereignissitzung in der SQL-Datenbank zurück. |


In Microsoft SQL Server haben ähnliche Katalogsichten Namen, die *.server\_* anstelle von *.database\_* enthalten. Das Namensmuster entspricht **sys.server\_event\_%**.


## Neue dynamische Verwaltungssichten [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx)


Azure SQL-Datenbank hat [dynamische Verwaltungsansichten (DMVs)](http://msdn.microsoft.com/library/bb677293.aspx), die erweiterte Ereignisse unterstützen. DMVs informieren Sie über *aktive* Ereignissitzungen.


| DMV-Name | Beschreibung |
| :-- | :-- |
| **sys.dm\_xe\_database\_session\_event\_actions** | Gibt Informationen zu Ereignissitzungsaktionen zurück. |
| **sys.dm\_xe\_database\_session\_events** | Gibt Informationen zu Sitzungsereignissen zurück. |
| **sys.dm\_xe\_database\_session\_object\_columns** | Zeigt die Konfigurationswerte für Objekte, die an eine Sitzung gebunden sind. |
| **sys.dm\_xe\_database\_session\_targets** | Gibt Informationen zu Sitzungszielen zurück. |
| **sys.dm\_xe\_database\_sessions** | Gibt eine Zeile für jede Ereignissitzung zurück, die auf die aktuelle Datenbank begrenzt ist. |


In Microsoft SQL Server werden ähnliche Katalogsichten ohne den Teil *\_database* des Namens benannt, wie z. B.:


- **sys.dm\_xe\_sessions** anstatt <br/>**sys.dm\_xe\_database\_sessions**.


### DMVs, die Azure SQL-Datenbank und Microsoft SQL Server gemeinsam haben


Für erweiterte Ereignisse gibt es zusätzliche DMVs, die Azure SQL-Datenbank und Microsoft SQL Server gemeinsam haben:


- **sys.dm\_xe\_map\_values**
- **sys.dm\_xe\_object\_columns**
- **sys.dm\_xe\_objects**
- **sys.dm\_xe\_packages**



 <a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## Suchen der verfügbaren erweiterten Ereignisse, Aktionen und Ziele


Sie können mithilfe einer einfachen SQL **SELECT**-Anweisung eine Liste der verfügbaren Ereignisse, Aktionen und Ziele abrufen.


```
SELECT
		o.object_type,
		p.name         AS [package_name],
		o.name         AS [db_object_name],
		o.description  AS [db_obj_description]
	FROM
		           sys.dm_xe_objects  AS o
		INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
	WHERE
		o.object_type in
			(
			'action',  'event',  'target'
			)
	ORDER BY
		o.object_type,
		p.name,
		o.name;
```



<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a>

&nbsp;

## Ziele für SQL-Datenbank-Ereignissitzungen


Es folgen Ziele, die Ergebnisse aus Ihren Ereignissitzungen für SQL-Datenbank erfassen können:


- [Ringpuffer](http://msdn.microsoft.com/library/ff878182.aspx): Enthält für kurze Zeit Ereignisdaten im Arbeitsspeicher.
- [Ereigniszähler](http://msdn.microsoft.com/library/ff878025.aspx): Zählt alle Ereignisse, die während einer Sitzung mit erweiterter Ereignissen auftreten.
- [Ereignisdatei](http://msdn.microsoft.com/library/ff878115.aspx): Schreibt vollständige Puffer in einen Azure Storage-Container.


Die [ETW](http://msdn.microsoft.com/library/ms751538.aspx)-API (Event Tracing for Windows) ist für erweiterte Ereignisse in SQL-Datenbank nicht verfügbar.


## Einschränkungen


Es gibt einige sicherheitsbezogene Unterschiede, die für die Cloudumgebung von SQL-Datenbank angemessen sind:


- Erweiterte Ereignisse beruhen auf dem Modell der Isolation einzelner Mandanten. Eine Ereignissitzung in einer Datenbank kann nicht auf Daten oder Ereignisse in einer anderen Datenbank zugreifen.

- Sie können keine **CREATE EVENT SESSION**-Anweisung im Kontext der **master**-Datenbank angeben.


## Berechtigungsmodell


Sie benötigen die Berechtigung **Control** für die Datenbank, um eine **CREATE EVENT SESSION**-Anweisung angeben zu können. Der Datenbankbesitzer (dbo) hat die Berechtigung **Control**.


### Speichercontainerautorisierungen


Das SAS-Token, das Sie für Ihren Azure Storage-Container generieren, muss **rwl** für die Berechtigungen angeben. Dies bietet die folgenden Berechtigungen:


- Lesen
- Schreiben
- Auflisten


## Überlegungen zur Leistung


Es gibt Szenarien, in denen durch die intensive Nutzung erweiterter Ereignisse mehr aktiver Arbeitsspeicher kumuliert wird, als für das System vorteilhaft ist. Deshalb sorgt das Azure SQL-Datenbanksystem für eine dynamische Festlegung und Anpassung von Grenzwerten für die Menge an aktivem Arbeitsspeicher, die von einer Ereignissitzung kumuliert werden kann. Viele Faktoren fließen in die dynamische Berechnung ein.


Wenn Sie eine Fehlermeldung erhalten, die besagt, dass ein Arbeitsspeichermaximum erzwungen wurde, können Sie z. B. die folgenden Maßnahmen ergreifen:


- Führen Sie weniger gleichzeitige Ereignissitzungen durch.


- Verringern Sie über die Anweisungen **CREATE** und **ALTER** für Ereignissitzungen die Arbeitsspeichermenge, die Sie für die **MAX\_MEMORY**-Klausel angeben.


### Netzwerklatenz


Beim Ziel **Ereignisdatei** können Netzwerklatenz oder -fehler auftreten, während Daten in Azure Storage-Blobs dauerhaft gespeichert werden. Andere Ereignisse in der SQL-Datenbank können möglicherweise verzögert werden, während auf den Abschluss der Netzwerkkommunikation gewartet wird. Diese Verzögerung kann Ihren Workload verlangsamen.

- Um dieses Leistungsrisiko zu vermeiden, legen Sie die Option **EVENT\_RETENTION\_MODE** in Ihren Ereignissitzungsdefinitionen nicht auf **NO\_EVENT\_LOSS** fest.


## Verwandte Links


- [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md)
- [Azure Storage-Cmdlets](http://msdn.microsoft.com/library/dn806401.aspx)


- [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md) – Bietet umfassende Informationen zu PowerShell und zum Azure Storage-Dienst.
- [Verwenden des Blob-Speichers mit .NET](storage-dotnet-how-to-use-blobs.md)


- [CREATE CREDENTIAL (Transact-SQL)](http://msdn.microsoft.com/library/ms189522.aspx)
- [CREATE EVENT SESSION (Transact-SQL)](http://msdn.microsoft.com/library/bb677289.aspx)


- [Jonathan Kehayias' Blogbeiträge zu erweiterten Ereignissen in Microsoft SQL Server](http://www.sqlskills.com/blogs/jonathan/category/extended-events/)


Anderen Codebeispielthemen für erweiterte Ereignisse finden Sie unter den folgenden Links. Allerdings müssen Sie routinemäßig alle Beispiele dahingehend prüfen, ob das Beispiel für Microsoft SQL Server oder Azure SQL-Datenbank gilt. Dann können Sie entscheiden, ob kleinere Änderungen erforderlich sind, um das Beispiel auszuführen.


<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](http://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](http://msdn.microsoft.com/library/bb630355.aspx)
-->

<!---HONumber=AcomDC_0211_2016-->