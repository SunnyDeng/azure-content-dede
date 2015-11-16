<properties
    pageTitle="Azure SQL-Datenbank – Clientbibliothek"
    description="Erstellen von skalierbaren .NET-Datenbank-Apps"
    services="sql-database"
    documentationCenter=""
    manager="jeffreyg"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/04/2015"
    ms.author="ddove;sidneyh"/>

# Übersicht über die Clientbibliothek für elastische Datenbanken

Mit der **Clientbibliothek für elastische Datenbanken** können Sie mithilfe hunderter oder sogar tausender in Microsoft Azure gehosteter Azure SQL-Datenbanken problemlos Sharding-Anwendungen entwickeln. Solche Entwürfe werden häufig für SaaS (Software as a Service)-Anwendungen verwendet, bei denen es sich in der Regel um Einzelinstanzarchitekturen handelt. Dabei wird für jede Instanz eine Datenbank bereitgestellt. Das Ziel der Bibliothek ist die Erstellung und Verwaltung einer solchen Anwendung.

Informationen zum Installieren der Bibliothek finden Sie unter [Microsoft Azure SQL-Datenbank: Elastische Skalierung](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Die Clientbibliothek ist Teil der Tools für elastische Datenbanken, ein spezielles [Feature für elastische Datenbanken](sql-database-elastic-scale-introduction.md).

## Clientfunktionen

Das Entwickeln, Skalieren und Verwalten von skalierten Applikationen mit *Sharding* (siehe unten) stellt sowohl Entwickler als auch Administratoren vor Probleme. Die Clientbibliothek vereinfacht die Arbeit für diese beiden Rollen. Die Zahlen in der Abbildung verweisen auf die Hauptfunktionen der Clientbibliothek für elastische Datenbanken. Die Abbildung zeigt eine Umgebung mit vielen Datenbanken, bei der jede Datenbank einem Shard entspricht. In diesem Beispiel befinden sich viele Kunden mithilfe einer Bereichszuordnung in der gleichen Datenbank, obwohl dasselbe gilt, wenn es pro Kunde (Mandant) eine Datenbank gibt. Die Tools vereinfachen die Entwicklung von Anwendungen mit Sharding für Azure SQL-Datenbank über die folgenden spezifischen Funktionen:

Definitionen von hier verwendeten Begriffen finden Sie unter [Tools für elastische Datenbanken – Glossar](sql-database-elastic-scale-glossary.md).

![Funktionen zur elastischen Skalierung][1]

1.  **Shard-Zuordnungsverwaltung**: Für die Verwaltung einer Shardsammlung wird eine spezielle Datenbank, der sogenannte Shard-Zuordnungs-Manager, erstellt. Die Verwaltung von Shard Maps ist die Fähigkeit einer Anwendung, verschiedene Metadaten über die Shards zu verwalten. Entwickler können diese Funktion verwenden, um Datenbanken als Shards zu registrieren, Zuordnungen einzelner Sharding-Schlüssel oder -Schlüsselbereiche zu diesen Datenbanken zu beschreiben und diese Metadaten zu verwalten, während sich die Anzahl und Zusammensetzung der Datenbanken gemäß den Kapazitätsänderungen weiter entwickelt. Ohne die Clientbibliothek für elastische Datenbanken ist das Schreiben des Verwaltungscodes beim Implementieren von Sharding sehr zeitaufwendig. Weitere Informationen finden Sie unter [Shard-Zuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md).

* **Datenabhängiges Routing**: Stellen Sie sich vor, dass eine Anforderung bei der Anwendung eingeht. Anhand des Shardingschlüsselwerts der Anforderung muss die Anwendung die richtige Datenbank bestimmen, die die Daten für diesen Schlüsselwert enthält, und eine Verbindung mit dieser zum Verarbeiten der Anforderung herstellen. Das datenabhängige Routing bietet die Möglichkeit, Verbindungen durch einen einfachen Aufruf über die Shard-Zuordnung der Anwendung zu öffnen. Das datenabhängige Routing ist weiterer Bereich des Infrastrukturcodes, der jetzt durch Funktionen der Clientbibliothek für elastische Datenbanken abgedeckt wird. Weitere Informationen finden Sie unter [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md).

* **Abfragen von mehreren Shards (MSQ)**: Abfragen von mehreren Shards funktioniert dann, wenn eine Anforderung mehrere (oder alle) Shards umfasst. In einer Abfrage mehrerer Shards wird derselbe T-SQL-Code für alle Shards oder eine Gruppe von Shards ausgeführt. Die Ergebnisse der beteiligten Shards werden unter Verwendung der UNION ALL-Semantik in einem Gesamtergebnis zusammengeführt. Die Funktionalität wird über die Clientbibliothek verfügbar gemacht. Sie verarbeitet u. a. Aufgaben wie Verbindungsverwaltung, Threadverwaltung, Fehlerbehandlung und Verarbeiten von Zwischenergebnissen. MSQ kann Hunderte von Shards abfragen. Weitere Informationen finden Sie unter [Abfragen von mehreren Shards](sql-database-elastic-scale-multishard-querying.md).

Im Allgemeinen steht den Endkunden mit den Tools für elastische Datenbanken bei der Übermittlung lokaler Shard-Vorgänge die volle T-SQL-Funktionalität zur Verfügung, anders als bei Shard-übergreifenden Vorgängen, die eine eigene Semantik haben.

## Nächste Schritte

Testen Sie die [Beispielapp](sql-database-elastic-scale-get-started.md), die die Clientfunktionen verdeutlicht.

Gehen Sie zum Installieren der Bibliothek zur [Clientbibliothek für elastische Datenbanken](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Informationen zur Verwendung des Split-Merge-Tools finden Sie unter [Übersicht über das Split-Merge-Tool](sql-database-elastic-scale-overview-split-and-merge.md).

[Die Clientbibliothek für elastische Datenbanken liegt nun als Open Source vor!](http://azure.microsoft.com/blog/elastic-database-client-library-is-now-open-sourced/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]: ./media/sql-database-elastic-database-client-library/glossary.png

<!---HONumber=Nov15_HO2-->