<properties
	pageTitle="Oberflächeneinschränkungen und Hindernisse für Stretch-Datenbank | Microsoft Azure"
	description="Erfahren Sie mehr über Hindernisse, die Sie beheben müssen, bevor Sie Stretch-Datenbank aktivieren können."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Oberflächeneinschränkungen und Hindernisse für Stretch-Datenbank

Erfahren Sie mehr über Hindernisse, die Sie beheben müssen, bevor Sie Stretch-Datenbank aktivieren können.

## <a name="Limitations"></a>Hindernisse
In der aktuellen Vorversion von SQL Server 2016 sind Tabellen mit den folgenden Komponenten nicht für Stretch berechtigt.

**Tabelleneigenschaften**
-   Mehr als 1.023 Spalten

-   Mehr als 998 Indizes

-   Tabellen, die FILESTREAM-Daten enthalten

-   FileTables

-   Replizierte Tabellen

-   Tabellen, die aktiv Change Tracking oder Change Data Capture verwenden

-   Speicheroptimierte Tabellen

**Datentypen und Spalteneigenschaften**
-   timestamp

-   sql\_variant

-   XML

-   geometry

-   geography

-   hierarchyid

-   CLR-benutzerdefinierte Typen (UDTs)

**Spaltentypen**
-   COLUMN\_SET

-   Berechnete Spalten

**Einschränkungen**
-   CHECK-Einschränkungen

-   Standardeinschränkungen

-   Fremdschlüsseleinschränkungen, die auf die Tabelle verweisen

**Indexe**
-   Volltextindizes

-   XML-Indizes

-   Räumliche Indizes

-   Indizierte Sichten, die auf die Tabelle verweisen

## <a name="Caveats"></a>Einschränkungen und Vorbehalte für Stretch-fähige Tabellen
In der aktuellen Vorversion von SQL Server 2016 haben Stretch-fähige Tabellen die folgenden Einschränkungen oder Vorbehalte.

-   Eindeutigkeit wird für UNIQUE-Einschränkungen und PRIMARY KEY-Einschränkungen nicht für eine Stretch-fähige Tabelle erzwungen.

-   Sie können keine UPDATE- oder DELETE-Vorgänge für eine Stretch-fähige Tabelle ausführen.

-   Sie können nichts in die Azure SQL-Datenbank-Remotetabelle einfügen.

-   Sie können keinen Index für eine Sicht erstellen, die Stretch-fähige Tabellen enthält.

-   Sie können kein Update und keine Löschung aus einer Sicht ausführen, die Stretch-fähige Tabellen enthält. Sie können jedoch etwas in eine Sicht einfügen, die Stretch-fähige Tabellen enthält.

-   Filter für Indizes werden nicht an die Remotetabelle weitergegeben.

## Weitere Informationen
[Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank durch Ausführen von Stretch Database Advisor](sql-server-stretch-database-identify-databases.md) [Aktivieren von Stretch-Datenbank für eine Datenbank](sql-server-stretch-database-enable-database.md) [Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md)

<!---HONumber=AcomDC_0302_2016-->