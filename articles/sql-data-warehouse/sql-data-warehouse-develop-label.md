<properties
   pageTitle="Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in SQL Data Warehouse | Microsoft Azure"
   description="Tipps zum Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in Azure SQL Data Warehouse für die Entwicklung von Lösungen"
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

# Verwenden von Bezeichnungen zum Instrumentieren von Abfragen in SQL Data Warehouse
In SQL Data Warehouse wird das Konzept der „Abfragebezeichnungen“ unterstützt. Bevor wir darauf näher eingehen, sehen wir uns ein Beispiel an:

	```
	SELECT *
	FROM sys.tables
	OPTION (LABEL = 'My Query Label')
	;
	```

Mit dieser letzten Zeile wird die Abfrage mit der Zeichenfolge „My Query Label“ versehen. Dies ist besonders hilfreich, da die Bezeichnung über die DMVs abgefragt werden kann. So erhalten wir ein Verfahren zum Ermitteln von Problemabfragen und können während einer ETL-Ausführung außerdem den Status abfragen.

Eine gute Benennungskonvention ist hierbei wirklich hilfreich. Beispielsweise ist etwas wie „PROJEKT : PROZEDUR : ANWEISUNG : KOMMENTAR“ hilfreich, um die Abfrage im Code der Quellcodeverwaltung eindeutig identifizieren zu können.

Um nach der Bezeichnung zu suchen, können Sie die folgende Abfrage nutzen, bei der die dynamischen Verwaltungssichten verwendet werden:

	```
	SELECT  *
	FROM    sys.dm_pdw_exec_requests r
	WHERE   r.[label] = 'My Query Label'
	;
	``` 

> [AZURE.NOTE]Es ist wichtig, dass Sie das Wort „label“ bei Abfragen in eckige Klammern oder doppelte Anführungszeichen setzen. „label“ ist ein reserviertes Wort, das zu einem Fehler führen kann, wenn es nicht entsprechend markiert wird.


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=August15_HO6-->