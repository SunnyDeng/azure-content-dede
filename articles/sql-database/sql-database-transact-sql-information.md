<properties
   pageTitle="Azure SQL-Datenbank – Transact-SQL-Informationen | Microsoft Azure"
   description="Transact-SQL-Anweisungen in Azure SQL-Datenbank"
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
   ms.date="08/07/2015"
   ms.author="rick.byham@microsoft.com"/>

# Azure SQL-Datenbank – Transact-SQL-Informationen

Die meisten SQL Server 2016 Transact-SQL-Anweisungen werden in Microsoft Azure SQL-Datenbank vollständig unterstützt. Dies umfasst die Datentypen, Operatoren, Zeichenfolgen-, arithmetischen, logischen und Cursorfunktionen sowie die anderen Transact-SQL-Elemente von SQL Server, mit denen die meisten Programme arbeiten. Teilweise oder nicht unterstützte Funktionen steht meist in Zusammenhang mit den Unterschieden bei der Verwaltung der Datenbank durch Azure SQL-Datenbank \(z. B. Datei-, Hochverfügbarkeits- und Sicherheitsfunktionen\) und bei speziellen Features wie dem Service Broker. Da Azure SQL-Datenbank viele Features von der Abhängigkeit von der master-datenbank isoliert, sind viele Aktivitäten auf Serverebene ungeeignet und werden nicht unterstützt. Features, die in SQL Server veraltet sind, werden im Allgemeinen von Azure SQL-Datenbank nicht unterstützt.

## Upgrade auf Azure SQL-Datenbank V12

In diesem Thema werden die Features behandelt, die mit Azure SQL-Datenbank bei einem Upgrade auf die kostenlose Azure SQL-Datenbank V12 verfügbar sind. Weitere Informationen zu V12 finden Sie unter [SQL-Datenbank V12 – Neuerungen](sql-database-v12-whats-new.md). Azure SQL-Datenbank V12 bietet Leistungs- und Verwaltungsverbesserungen sowie Unterstützung für zusätzliche Features. Die zusätzlichen Features sind nachstehend getrennt nach teilweise und vollständig unterstützten Features aufgeführt.

## In Azure SQL-Datenbank V12 teilweise unterstützte Features

Azure SQL-Datenbank V12 unterstützt einige, aber nicht alle Argumente, die in den entsprechenden SQL Server 2016-Transact-SQL-Anweisungen vorhanden sind. Beispielsweise steht die CREATE PROCEDURE-Anweisung zur Verfügung, jedoch ohne die Option WITH ENCRYPTION. In den verlinkten Syntaxthemen finden Sie Einzelheiten zu den unterstützten Bereichen jeder Anweisung.

- CLR-Assemblys: [CREATE ASSEMBLY](https://msdn.microsoft.com/library/ms189524.aspx)
- Datenbanken: [CREATE](https://msdn.microsoft.com/library/dn268335.aspx)/[ALTER](https://msdn.microsoft.com/library/ms174269.aspx)
- Dynamische Verwaltungssichten stehen im Allgemeinen für verfügbare Features zur Verfügung.
- Funktionen: [CREATE](https://msdn.microsoft.com/library/ms186755.aspx)/[ALTER FUNCTION](https://msdn.microsoft.com/library/ms186967.aspx)
- [KILL](https://msdn.microsoft.com/library/ms173730.aspx) 
- Anmeldungen: [CREATE](https://msdn.microsoft.com/library/ms189751.aspx)/[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)
- Gespeicherte Prozeduren: [CREATE](https://msdn.microsoft.com/library/ms187926.aspx)/[ALTER PROCEDURE](https://msdn.microsoft.com/library/ms189762.aspx)
- Tabellen: [CREATE](https://msdn.microsoft.com/library/dn305849.aspx)/[ALTER](https://msdn.microsoft.com/library/ms190273.aspx)
- Typen \(benutzerdefiniert\): [CREATE TYPE](https://msdn.microsoft.com/library/ms175007.aspx)
- Benutzer: [CREATE](https://msdn.microsoft.com/library/ms173463.aspx)/[ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)
- Sichten: [CREATE](https://msdn.microsoft.com/library/ms187956.aspx)/[ALTER VIEW](https://msdn.microsoft.com/library/ms173846.aspx)

## In Azure SQL-Datenbank V12 nicht unterstützte Features

- Sortierung von Systemobjekten
- Verbindungsbezogen: Endpunktanweisungen, ORIGINAL\_DB\_NAME. Die Windows-Authentifizierung ist für Anmeldungen oder eigenständige Datenbanken nicht verfügbar.
- Datenbankübergreifende Abfragen und Datenbankbesitzverkettung, Einstellung TRUSTWORTHY
- Datensammler
- Datenbankdiagramme
- Datenbank-E-Mail
- DATABASEPROPERTY \(verwenden Sie stattdessen DATABASEPROPERTYEX\)
- Verteilte Transaktionen
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
- SQL Server-Überwachung \(verwenden Sie stattdessen die SQL-Datenbank-Überwachung\)
- SQL Server Profiler
- SQL Server-Ablaufverfolgung
- Ablaufverfolgungskennzeichen
- Transact-SQL-Debugging
- Trigger: Auf Server begrenzt oder Anmeldetrigger
- USE-Anweisung

## Vollständige Transact-SQL-Referenz

Weitere Informationen zu Transact-SQL-Grammatik und -Syntax sowie Beispiele finden Sie unter [Transact-SQL-Referenz \(Datenbankmodul\)](https://msdn.microsoft.com/library/bb510741.aspx) in der SQL Server-Onlinedokumentation.

### Informationen zu Tags vom Typ "Gilt für"

Die Transact-SQL-Referenz enthält Themen im Zusammenhang mit SQL Server 2008, SQL Server 2008 R2, SQL Server 2012, SQL Server 2014 und Microsoft Azure SQL-Datenbank. Am oberen Rand jedes Themas ist ein Abschnitt, der angibt, welche Produkte den Betreff des Themas unterstützen. Wenn ein Produkt fehlt, ist das im Thema beschriebene Feature in diesem Produkt nicht verfügbar. Beispielsweise wurden Verfügbarkeitsgruppen in SQL Server 2012 eingeführt. Das Thema **VERFÜGBARKEITSGRUPPE ERSTELLEN** gibt an, dass es für **SQL Server \(SQL Server 2012 bis zur aktuellen Version\)** gilt. Es gilt nicht für SQL Server 2008, SQL Server 2008 R2 oder Microsoft Azure SQL-Datenbank.

In einigen Fällen kann der allgemeine Betreff eines Themas in einem Produkt verwendet werden, ohne dass alle Argumente unterstützt werden. Eigenständige Datenbanken wurden z. B. in SQL Server 2012 eingeführt. Die **CREATE USER**-Anweisung kann in allen SQL Server-Produkten, die **WITH PASSWORD**-Syntax jedoch nicht in älteren Versionen verwendet werden. In diesem Fall wurden zusätzliche **Gilt für**-Abschnitte in die entsprechenden Argumentbeschreibungen im Text des Themas eingefügt.

<!---HONumber=August15_HO7-->