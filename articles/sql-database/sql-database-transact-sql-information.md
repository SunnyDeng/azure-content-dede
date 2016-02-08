<properties
   pageTitle="Nicht unterstützte T-SQL-Funktionen in Azure SQL-Datenbank | Microsoft Azure"
   description="Transact-SQL-Anweisungen, die in Azure SQL-Datenbank nicht vollständig unterstützt werden"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="01/21/2016"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL-Datenbank – Abweichungen bei Transact-SQL


Die meisten Transact-SQL-Funktionen, von denen Anwendungen abhängen, werden in Microsoft SQL Server und Azure SQL-Datenbank unterstützt. Hier eine unvollständige Liste der für Anwendungen unterstützten Funktionen:

- Datentypen
- Operatoren
- Zeichenfolgen-, arithmetische, logische und Cursorfunktionen

Azure SQL-Datenbank ist jedoch so konzipiert, dass Funktionen von der Abhängigkeit von der **master**-Datenbank isoliert werden. Daher sind viele Aktivitäten auf Serverebene für SQL-Datenbank nicht geeignet und werden nicht unterstützt. In diesem Thema werden die Funktionen erläutert, die in SQL-Datenbank nicht vollständig unterstützt werden.

Auch Funktionen, die in SQL Server veraltet sind, werden im Allgemeinen in Azure SQL-Datenbank nicht unterstützt.

## Upgrade auf Azure SQL-Datenbank V12

In diesem Thema werden die Features behandelt, die mit Azure SQL-Datenbank bei einem Upgrade auf die kostenlose Azure SQL-Datenbank V12 verfügbar sind. Weitere Informationen zu V12 finden Sie unter [SQL-Datenbank V12 – Neuerungen](sql-database-v12-whats-new.md). Azure SQL-Datenbank V12 bietet Leistungs- und Verwaltungsverbesserungen sowie Unterstützung für zusätzliche Features. Die zusätzlichen Features sind nachstehend getrennt nach teilweise und vollständig unterstützten Features aufgeführt.

## In Azure SQL-Datenbank V12 teilweise unterstützte Funktionen

Azure SQL-Datenbank V12 unterstützt einige, aber nicht alle Argumente, die in den entsprechenden SQL Server 2016-Transact-SQL-Anweisungen vorhanden sind. Beispielsweise steht die CREATE PROCEDURE-Anweisung zur Verfügung, jedoch ohne die Option WITH ENCRYPTION. In den verlinkten Syntaxthemen finden Sie Einzelheiten zu den unterstützten Bereichen jeder Anweisung.

- Datenbanken: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- Dynamische Verwaltungssichten stehen im Allgemeinen für verfügbare Features zur Verfügung.
- Funktionen: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- Anmeldungen: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Gespeicherte Prozeduren: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabellen: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Typen (benutzerdefiniert): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Benutzer: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- Sichten: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## In SQL-Datenbank nicht unterstützte Funktionen

- Sortierung von Systemobjekten
- Verbindungsbezogen: Endpunktanweisungen, ORIGINAL\_DB\_NAME. Die Windows-Authentifizierung ist für Anmeldungen oder eigenständige Datenbanken nicht verfügbar.
- Datenbankübergreifende Abfragen für INSERT-, UPDATE- und DELETE-Vorgänge. (Schreibgeschützte datenbankübergreifende Abfragen werden unterstützt.)
- Datenbankübergreifende Besitzverkettung, TRUSTWORTHY-Einstellung
- Datensammler
- Datenbankdiagramme
- Datenbank-E-Mail
- DATABASEPROPERTY (verwenden Sie stattdessen DATABASEPROPERTYEX)
- EXECUTE AS-Anmeldungen
- Verschlüsselung: erweiterbare Schlüsselverwaltung
- Ereignisse: Ereignisse, Ereignisbenachrichtigungen, Abfragebenachrichtigungen
- Features im Zusammenhang mit der Ablage von Datenbankdateien, Größe und Datenbankdateien, die automatisch von Microsoft Azure verwaltet werden.
- Features im Zusammenhang mit hoher Verfügbarkeit, die über Ihr Microsoft Azure-Konto verwaltet werden: Sicherung, Wiederherstellung, AlwaysOn, Datenbankspiegelung, Protokollversand, Wiederherstellungsmodi. Weitere Informationen finden Sie unter "Sichern und Wiederherstellen der Azure SQL-Datenbank".
- Features, die vom Protokollleser abhängig sind: Replikation, Erfassung geänderter Daten.
- Features, die vom SQL Server-Agent oder der MSDB-Datenbank abhängen: Aufträge, Warnungen, Operatoren, richtlinienbasierte Verwaltung, Datenbank-E-Mail, zentrale Verwaltungsserver.
- FILESTREAM
- Funktionen: fn\_get\_sql, fn\_virtualfilestats, fn\_virtualservernodes
- Globale temporäre Tabellen
- Hardwarebezogene Servereinstellungen: Arbeitsspeicher, Worker-Threads, CPU-Affinität, Ablaufverfolgungskennzeichen usw. Verwenden Sie stattdessen Dienstebenen.
- HAS\_DBACCESS
- KILL STATS JOB
- Verknüpfte Server, OPENQUERY, OPENROWSET, OPENDATASOURCE, BULK INSERT, Namen mit 3 oder 4 Teilen
- Master-/Zielserver
- .NET Framework: [CLR-Integration in SQL Server](http://msdn.microsoft.com/library/ms254963.aspx)
- Ressourcenkontrolle
- Semantische Suche
- Serveranmeldeinformationen
- Elemente auf Serverebene: Serverrollen, IS\_SRVROLEMEMBER, sys.login\_token. Berechtigungen auf Serverebene sind nicht verfügbar, obwohl einige durch Berechtigungen auf Datenbankebene ersetzt wurden. Einige dynamische Verwaltungssichten auf Serverebene sind nicht verfügbar, obwohl einige durch dynamische Verwaltungssichten auf Datenbankebene ersetzt wurden.
- Serverloses Express: localdb, Benutzerinstanzen
- Service Broker
- SET REMOTE\_PROC\_TRANSACTIONS
- SHUTDOWN
- sp\_addmessage
- sp\_configure-Optionen und die RECONFIGURE
- sp\_helpuser
- sp\_migrate\_user\_to\_contained
- SQL Server-Überwachung (verwenden Sie stattdessen die SQL-Datenbank-Überwachung)
- SQL Server Profiler
- SQL Server-Ablaufverfolgung
- Ablaufverfolgungskennzeichen
- Transact-SQL-Debugging
- Trigger: Auf Server begrenzt oder Anmeldetrigger
- USE-Anweisung

## Vollständige Transact-SQL-Referenz

Weitere Informationen zu Transact-SQL-Grammatik und -Syntax sowie Beispiele finden Sie unter [Transact-SQL-Referenz (Datenbankmodul)](https://msdn.microsoft.com/library/bb510741.aspx) in der SQL Server-Onlinedokumentation.

### Informationen zu Tags vom Typ "Gilt für"

Die Transact-SQL-Referenz umfasst Themen zu SQL Server-Versionen ab 2008. Unter der Themenüberschrift sind normalerweise unter „Gilt für“ die SQL Server-Versionen und möglicherweise auch andere Produktnamen aufgeführt. Häufig ist dort auch Azure SQL-Datenbank aufgeführt. Wenn Azure SQL-Datenbank nicht angegeben ist, gilt der Inhalt des Themas nicht für Azure SQL-Datenbank. Wenn unter „Gilt für“ mehrere Produkte aufgeführt sind, wird mit einem kleinen Symbol angegeben, ob das Thema jeweils für die einzelnen Produkte gilt.

 Beispielsweise wurden Verfügbarkeitsgruppen in SQL Server 2012 eingeführt. Das Thema **VERFÜGBARKEITSGRUPPE ERSTELLEN** gibt an, dass es für **SQL Server (SQL Server 2012 bis zur aktuellen Version)** gilt. Es gilt nicht für SQL Server 2008, SQL Server 2008 R2 oder Azure SQL-Datenbank.

In einigen Fällen kann der allgemeine Gegenstand eines Themas in einem Produkt verwendet werden, es liegen jedoch kleine Unterschiede im Hinblick auf die verschiedenen Produkte vor. Die Unterschiede werden dann im Thema entsprechend angegeben.

<!---HONumber=AcomDC_0128_2016-->