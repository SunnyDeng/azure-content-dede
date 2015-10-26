<properties 
	pageTitle="SQL Data Warehouse – Unterstützung von Vorgängerversionsclients für die Datenüberwachung | Microsoft Azure" 
	description="Sie erhalten Informationen zur Unterstützung von Vorgängerversionsclients in SQL Data Warehouse für die Datenüberwachung." 
	services="sql-data-warehouse" 
	documentationCenter="" 
	authors="twounder" 
	manager="" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/06/2015" 
	ms.author="twounder"/>
 
# SQL Data Warehouse – Unterstützung von Vorgängerversionsclients für Überwachung und dynamische Datenmaskierung 


[Überwachung](sql-data-warehouse-auditing-get-started.md) kann mit SQL-Clients genutzt werden, die die TDS-Umleitung unterstützen.

Jeder Client, der TDS 7.4 implementiert, sollte auch die Umleitung unterstützen. Zu den Ausnahmen gehören JDBC 4.0, in dem die Umleitungsfunktion nicht vollständig unterstützt wird, und Tedious for Node.JSS, in dem die Umleitungsfunktion nicht implementiert wurde.

Für "Vorgängerversionsclients", d. h. Clients, die TDS 7.3 und ältere Versionen unterstützen, sollte der vollqualifizierte Domänenname des Servers in der Verbindungszeichenfolge geändert werden:

Ursprünglicher vollqualifizierter Domänenname des Servers in der Verbindungszeichenfolge: <*Servername*>.database.windows.net

Geänderter vollqualifizierter Domänenname des Servers in der Verbindungszeichenfolge: <*Servername*>.database.**secure**.windows.net

Eine unvollständige Liste der "Vorgängerversionsclients":

- .NET 4.0 und ältere Versionen
- ODBC 10.0 und ältere Versionen
- JDBC (JDBC unterstützt zwar TDS 7.4, aber die TDS-Umleitungsfunktion wird nicht vollständig unterstützt)
- Tedious (for Node.JS)

**Anmerkung:** Die oben genannte Änderung des vollqualifizierten Domänennamens des Servers kann sich auch als nützlich erweisen bei der Anwendung einer Richtlinie für die Überwachung auf SQL Server-Ebene, ohne dass ein Konfigurationsschritt in jeder Datenbank ausgeführt werden muss (temporäre Minderung).

 

<!---HONumber=Oct15_HO3-->