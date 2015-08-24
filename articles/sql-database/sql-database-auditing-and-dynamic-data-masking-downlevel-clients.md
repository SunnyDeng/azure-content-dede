<properties 
	pageTitle="SQL-Datenbank – Unterstützung von Vorgängerversionsclients für Überwachung und dynamische Datenmaskierung | Microsoft Azure" 
	description="SQL-Datenbank – Unterstützung von Vorgängerversionsclients für Überwachung und dynamische Datenmaskierung" 
	services="sql-database" 
	documentationCenter="" 
	authors="nadavhelfman" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="nadavhelfman"/>
 
# SQL-Datenbank – Unterstützung von Vorgängerversionsclients für Überwachung und dynamische Datenmaskierung 


[Überwachung](sql-database-auditing-get-started.md) und [dynamische Datenmaskierung](sql-database-dynamic-data-masking-get-started.md) können mit SQL-Clients genutzt werden, die die TDS-Umleitung unterstützen.

Jeder Client, der TDS 7.4 implementiert, sollte auch die Umleitung unterstützen. Zu den Ausnahmen gehören JDBC 4.0, in dem die Umleitungsfunktion nicht vollständig unterstützt wird, und Tedious for Node.JSS, in dem die Umleitungsfunktion nicht implementiert wurde.

Für "Vorgängerversionsclients", d. h. Clients, die TDS 7.3 und ältere Versionen unterstützen, sollte der vollqualifizierte Domänenname des Servers in der Verbindungszeichenfolge geändert werden:

Ursprünglicher vollqualifizierter Domänenname des Servers in der Verbindungszeichenfolge: \<*Servername*\>.database.windows.net

Geänderter vollqualifizierter Domänenname des Servers in der Verbindungszeichenfolge: \<*Servername*\>.database.**secure**.windows.net

Eine unvollständige Liste der "Vorgängerversionsclients":

- .NET 4.0 und ältere Versionen
- ODBC 10.0 und ältere Versionen
- JDBC 4.0 und ältere Versionen \(JDBC 4.0 unterstützt zwar TDS 7.4, aber die TDS-Umleitungsfunktion wird nicht vollständig unterstützt\)
- Tedious \(for Node.JS\)

**Anmerkung:** Die oben genannte Änderung des vollqualifizierten Domänennamens des Servers kann sich auch als nützlich erweisen bei der Anwendung einer Richtlinie für die Überwachung auf SQL Server-Ebene, ohne dass ein Konfigurationsschritt in jeder Datenbank ausgeführt werden muss \(temporäre Minderung\).

 

<!---HONumber=August15_HO7-->