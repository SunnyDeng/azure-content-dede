<properties
   pageTitle="Sichten in SQL Data Warehouse | Microsoft Azure"
   description="Tipps für die Verwendung von Transact-SQL-Sichten in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
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
   ms.date="01/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

 
# Sichten in SQL Data Warehouse

Sichten sind in SQL Data Warehouse besonders nützlich. Sie können auf verschiedene Weisen zur Verbesserung der Qualität der Lösung verwendet werden.

In diesem Artikel werden einige Beispiele zum Verbessern der Lösung mit einer Implementierung von Sichten dargestellt. Es gibt einige Einschränkungen, die ebenfalls berücksichtigt werden müssen.

## Architekturabstraktion
Ein häufig verwendetes Anwendungsmuster ist das erneute Erstellen von Tabellen mit CREATE TABLE AS SELECT (CTAS) gefolgt von einem Muster zur Objektumbenennung beim Laden von Daten.

Im folgenden Beispiel werden einer Datumsdimension neue Datumsdatensätze hinzugefügt. Beachten Sie, wie das neue Objekt "DimDate\_New" zuerst erstellt und dann umbenannt wird, um die ursprüngliche Version des Objekts zu ersetzen.

```
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS 
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Allerdings können dadurch Tabellenobjekte in der Benutzersicht im SSDT SQL Server-Objekt-Explorer auftauchen oder daraus verschwinden. Sichten können verwendet werden, um eine konsistente Darstellungsschicht für Warehouse-Datenconsumer bereitzustellen, während die zugrunde liegenden Objekte umbenannt werden. Beim Bereitstellen von Datenzugriff über eine Sicht benötigen Benutzer keine Einblicke in die zugrunde liegenden Tabellen. Dadurch wird eine konsistente Benutzererfahrung gewährleistet, während die Data Warehouse-Designer das Datenmodell weiterentwickeln und die Leistung maximieren können, indem sie beim Datenladevorgang CTAS verwenden.

## Leistungsoptimierung
Sichten bieten eine intelligente Möglichkeit, leistungsoptimierte Verknüpfungen zwischen Tabellen zu erzwingen. Beispielsweise kann die Sicht einen redundanten Verteilungsschlüssel als Teil des Verknüpfungskriteriums enthalten, um die Datenverschiebung zu minimieren. Ein weiterer Grund besteht im Erzwingen von bestimmten Abfragen oder Verknüpfungshinweisen. Dadurch wird sichergestellt, dass die Verknüpfung immer auf optimale Weise ausgeführt wird und nicht davon abhängt, dass sich der Benutzer an eine ordnungsgemäße Konstruktion der Verknüpfung erinnern muss.

## Einschränkungen
Sichten in SQL Data Warehouse bestehen nur aus Metadaten

Daher sind die folgenden Optionen nicht verfügbar: - Es ist keine Schemabindungsoption verfügbar - Basistabellen können nicht über die Sicht aktualisiert werden - Sichten können nicht auf temporären Tabellen erstellt werden - Es gibt keine Unterstützung für EXPAND/NOEXPAND-Hinweise - Es gibt keine indizierten Sichten in SQL Data Warehouse


## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [SQL Data Warehouse-Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse-Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0114_2016-->