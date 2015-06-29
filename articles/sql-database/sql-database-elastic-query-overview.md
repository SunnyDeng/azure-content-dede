<properties 
	title="Elastic database query – previewing May 2015" 
	pageTitle="Elastische Datenbankabfrage – Vorschau Mai 2015" 
	description="Ankündigungen zum Feature der elastischen Abfrage" 
	metaKeywords="azure sql database elastic global queries" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="sidneyh" />

# Elastische Datenbankabfrage – Vorschau Juni 2015 

Die elastische Datenbankabfrage ist ein neues Feature von Azure SQL-Datenbank, das Ende Juni 2015 in einer Vorschau vorgestellt wird. Sie bietet die Möglichkeit mit einer einzelnen Azure SQL-Datenbankverbindung eine T-SQL-Abfrage auszuführen, die mehrere Datenbanken umfasst. Dies ermöglicht einen Sharding-Datensatz, der mit einer elastischen Datenbank-Shard-Zuordnung so definiert wird, dass er als ein einziger integrierter Datenspeicher angezeigt wird. Die Verarbeitung von verteilten Abfragen wird für Berichterstellungs- und Integrationszwecke verwendet und im Hintergrund verarbeitet. Verbindungen werden unter Verwendung jedes Treibers unterstützt, der mit SQL-Datenbanken kommunizieren kann, einschließlich ADO.Net, ODBC, JDBC, Node.js, PHP usw.

## Szenarios mit elastischen Datenbankabfragen

Das Ziel der elastischen Datenbankabfrage ist es, Berichterstellungsszenarios über eine Sharding-Datenebene zu erleichtern, in der mehrere Datenbanken oder Shards Zeilen für ein einzelnes Gesamtergebnis einer T-SQL-Abfrage beitragen. Die T-SQL-Abfrage kann entweder vom Benutzer oder durch die Anwendung direkt oder indirekt über Tools erstellt werden, die mit der globalen Datenbank für elastische Abfragen verbunden sind. Dies ist besonders nützlich für kommerzielle BI, die Berichterstellung oder Datenintegrationstools sowie Softwarepakete, die direkt über elastische Skalierungsbibliotheken erweitert werden können. Außerdem ermöglicht sie einfachen Zugriff auf eine ganze Sammlung von Sharding-Daten, die von SQL Server Management Studio oder Visual Studio ausgegeben werden, und sie bietet Unterstützung für transparenten Datenzugriff mit mehreren Shards aus Entity Framework oder anderen ORM-Umgebungen. Abbildung 1 veranschaulicht dieses Szenario, bei dem globale elastische DB-Abfragen eine zweite und abweichende Möglichkeit zum Senden von Abfragen der Sharding-Datenebene zusätzlich zur vorhandenen Cloudanwendung (die die elastische Skalierbarkeitsbibliotheken verwenden kann) bieten.

![Abfragen von elastischen Datenbanken][1]

Die Szenarios lassen sich durch die folgenden Topologien beschreiben:

-	Sharding-Datenebene (Topologie 1): die Datenebene wurde für Sharding entwickelt. Das Sharding wird mit der (1) elastischen Datenbank-Clientbibliothek ausgeführt und verwaltet oder per (2) Selbst-Sharding. Der Berichterstellungsfall dient zum Berechnen von Berichten über mehrere Shards mit funktionalen oder Verbindungsanforderungen, die über die Möglichkeiten von Multi-Shard-Abfragen in der elastischen Datenbank-Clientbibliothek hinausgehen. 

-	Entwurf mit mehreren Datenbanken (Topologie 2): Hier wurde die Datenebene vertikal partitioniert, sodass sich unterschiedliche Arten von Daten in verschiedenen Datenbanken befinden. Der Berichterstellungsfall dient zum Berechnen von Berichten über Daten, die über mehrere Datenbanken verteilt sind, aber in ein Gesamtergebnis für den Bericht integriert werden müssen.

In der Vorschau der elastischen Datenbankabfragen werden beide Fälle behandelt. Kunden mit Topologie 1 können sich auf ihre vorhandene Shard-Zuordnung verlassen, wenn sie die elastische Datenbank-Clientbibliothek zum Verwalten von Shards verwenden. Benutzer mit Selbst-Sharding müssen wiederum zum Verwenden dieser Vorschaufunktion eine Shard-Zuordnung mithilfe von elastischen Datenbanktools für ihre Datenebenen erstellen. Kunden, die nach Topologie 2 vorgehen, müssen eine andere Shard-Zuordnung für jede ihrer Datenbanken erstellen. Diese Shard-Zuordnungen verweisen jeweils nur auf einen einzelnen Shard, der dann die Tabellen für Shard-übergreifende Abfragen verfügbar machen kann.

## Nächste Schritte
Die elastische Datenbankabfrage ist für die Vorschau Ende Juni 2015 geplant. Besuchen Sie diese Seite Ende Mai noch einmal, um weitere Informationen und schrittweise Anleitungen zur Verwendung der Funktion zu erhalten.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
<!--anchors-->

<!---HONumber=58_postMigration-->