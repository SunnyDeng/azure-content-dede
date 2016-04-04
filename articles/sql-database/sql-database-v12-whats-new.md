<properties 
	pageTitle="Neuerungen in SQL-Datenbank V12 | Microsoft Azure" 
	description="Beschreibt, warum Geschäftssysteme, die eine Azure SQL-Datenbank in der Cloud verwenden von einem Upgrade auf Version V12 profitieren." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jhubbard" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="genemi"/>


# Neuerungen in SQL-Datenbank V12


In diesem Thema werden die zahlreichen Vorteile beschrieben, die die neue Version V12 von Azure SQL-Datenbank gegenüber Version V11 bietet.


Es werden auch weiterhin neue Funktionen zu V12 hinzugefügt. Daher empfehlen wir Ihnen, die Webseite "Dienstupdates" für Azure zu besuchen und diese Filter zu verwenden:


- Gefiltert nach [SQL-Datenbank](https://azure.microsoft.com/updates/?service=sql-database).
- Gefiltert nach allgemeiner Verfügbarkeit [Ankündigungen](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) für SQL-Datenbankfunktionen.


Die neueste Informationen zu Ressourceneinschränkungen für SQL-Datenbank finden Sie unter:<br/>[Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md).


## Höhere Anwendungskompatibilität mit SQL Server


Ein wichtiges Ziel für SQL-Datenbank V12 war, die Kompatibilität mit Microsoft SQL Server 2014 zu verbessern und die Kompatibilität zu gewährleisten, wenn neue Versionen von SQL Server veröffentlicht werden. Unter anderem ist V12 jetzt mit SQL Server im wichtigen Bereich der Programmierung gleichauf. Beispiel:


- [Fensterfunktionen](http://msdn.microsoft.com/library/ms189798.aspx), mit [OVER](http://msdn.microsoft.com/library/ms189461.aspx) 

- [XML-Indizes](http://msdn.microsoft.com/library/bb934097.aspx) und [selektive XML-Indizes](http://msdn.microsoft.com/library/jj670104.aspx)

- [Änderungsnachverfolgung](http://msdn.microsoft.com/library/bb933875.aspx)

- [INTO-Klausel](http://msdn.microsoft.com/library/ms188029.aspx)

- [Volltextsuche](http://msdn.microsoft.com/library/ms142571.aspx)

- [ALTER DATABASE SCOPED CONFIGURATION (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Informationen zu den wenigen noch nicht unterstützten SQL-Datenbankfunktionen finden Sie [hier](sql-database-transact-sql-information.md).


## Mehr Premium-Performance, neue Leistungsstufen


In V12 haben wir die Datenbanktransaktionseinheiten (DTUs) aller Premium-Leistungsstufen ohne zusätzliche Kosten um 25 % erhöht. Durch neue Features, wie die im Folgenden aufgeführten, können noch größere Leistungsvorteile erzielt werden:


- Unterstützung für speicherinterne [Columnstore-Indizes](http://msdn.microsoft.com/library/gg492153.aspx).
- [Tabellenpartitionierung nach Zeilen](http://msdn.microsoft.com/library/ms187802.aspx) und damit verbundene Verbesserungen bei [TRUNCATE TABLE](http://msdn.microsoft.com/library/ms177570.aspx).
- Verfügbarkeit der dynamischen Verwaltungssichten [(DMVs)](http://msdn.microsoft.com/library/ms188754.aspx) zur besseren Überwachung und Optimierung der Leistung.


### Zuverlässige Leistung


Wenn Ihr Clientprogramm eine Verbindung mit SQL-Datenbank V12 herstellt, wobei der Client auf einem virtuellen Azure-Computer ausgeführt wird, müssen Sie die folgenden Portbereiche auf dem virtuellen Computer öffnen:

- 11000-11999
- 14000-14999


Klicken Sie auf [hier](sql-database-develop-direct-route-ports-adonet-v12.md), um ausführliche Informationen über die Ports für die SQL-Datenbank V12 zu erhalten. Die Ports werden für Leistungssteigerungen in der SQL-Datenbank V12 benötigt.


## Bessere Unterstützung für Cloud-SaaS-Anbieter


Nur in V12 verfügbar ist die neue Standardleistungsstufe S3 sowie die öffentliche Vorschau des [elastischen Datenbankpools](sql-database-elastic-pool.md). Dies ist eine Lösung, die speziell für Cloud-SaaS-Anbieter entwickelt wurde. Elastische Datenbankpools bieten die folgenden Möglichkeiten:


- Gemeinsame Nutzung von DTUs mit anderen Datenbanken zur Kostensenkung für eine große Anzahl von Datenbanken
- Ausführen [elastischer Datenbankaufträge](sql-database-elastic-jobs-overview.md), um Datenbanken angemessen zu verwalten


## Verbesserungen bei der Sicherheit


Sicherheit ist eines der Hauptprobleme für alle Benutzer, die für Unternehmenszwecke auf die Cloud angewiesen sind. Zu den aktuellen Sicherheitsfeatures in V12 gehören die folgenden:


- [Zeilenbasierte Sicherheit](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Dynamische Datenmaskierung](sql-database-dynamic-data-masking-get-started.md)
- [Eigenständige Datenbanken](http://msdn.microsoft.com/library/ff929188.aspx)
- [Anwendungsrollen](http://msdn.microsoft.com/library/ms190998.aspx), die mit GRANT, DENY oder REVOKE verwaltet werden
- [Transparente Datenverschlüsselung](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung der Azure Active Directory-Authentifizierung](sql-database-aad-authentication.md)
 - SQL-Datenbank unterstützt nun die Azure Active Directory-Authentifizierung, ein Mechanismus zum Herstellen einer Verbindung mit SQL-Datenbank unter Verwendung von Identitäten in Azure Active Directory (Azure AD). Mithilfe der Azure Active Directory-Authentifizierung ist es möglich, die Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten an einer zentralen Stelle zu verwalten.
- [Immer verschlüsselt](https://msdn.microsoft.com/library/mt163865.aspx) (in der Vorschau) macht die Verschlüsselung für Anwendungen erkennbar und ermöglicht es Clients, sensible Daten in Clientanwendungen zu verschlüsseln, ohne die Verschlüsselungsschlüssel für die SQL-Datenbank freizugeben.


## Erhöhte Geschäftskontinuität, wenn eine Wiederherstellung erforderlich ist


V12 bietet wesentlich bessere Recovery Point Objectives (RPO) und geschätzte Wiederherstellungszeiten (ERTs):


| Funktionen der Geschäftskontinuität | Frühere Version | V12 |
| :-- | :-- | :-- |
| Geografische Wiederherstellung | • RPO < 24 Stunden.<br/>• ERT < 12 Stunden. | • RPO < 1 Stunde.<br/>• ERT < 12 Stunden. |
| Standardgeoreplikation | • RPO < 30 Minuten.<br/>• ERT < 2 Stunden. | • RPO < 5 Sekunden.<br/>• ERT < 30 Sekunden. |
| Aktive Georeplikation | • RPO < 5 Minuten.<br/>• ERT < 1 Stunde. | • RPO < 5 Sekunden.<br/>• ERT < 30 Sekunden. |


Weitere Informationen finden Sie unter [Azure SQL-Datenbank – Geschäftskontinuität](sql-database-business-continuity.md).


## Weitere Gründe für ein Upgrade


Es gibt viele gute Gründe, warum Kunden jetzt ein Upgrade von V11 auf Azure SQL-Datenbank V12 ausführen sollten:


- SQL-Datenbank V12 bietet eine lange Liste von Features, die über Version V11 hinausgehen.
- Wir fügen weiterhin neue Features zu V12 hinzu, aber es werden keine neuen Features mehr zu V11 hinzugefügt.
- Die meisten neuen Features werden in SQL-Datenbank V12 veröffentlicht, bevor sie für Microsoft SQL Server veröffentlicht werden.


## Verwenden Sie bereits V12?


Eine einfache Möglichkeit, um festzustellen, ob eine Datenbank oder ein logischer Server einer älteren Version des SQL-Datenbankdiensts ausgeführt wird, besteht in folgendem Verfahren:


1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).
2. Klicken Sie auf **Durchsuchen**.
3. Klicken Sie auf **SQL-Server**.
4. Das Symbol neben dem Server oder der Datenbank enthält die Informationen:
 - ![Symbol für einen V12-Server](./media/sql-database-v12-whats-new/v12_icon.png) **Logischer V12-Server**
 - ![Symbol für einen Server älterer Version](./media/sql-database-v12-whats-new/earlier_icon.png) **Logischer Server einer früheren Version**


Ein anderes Verfahren zum Ermitteln der Version besteht in der Ausführung der `SELECT @@version;`-Anweisung in der Datenbank zur Anzeige von Versionsinformationen, die den folgenden ähneln:


- **12**.0.2000.10 &nbsp; *(Version V12)*
- **11**.0.9228.18 &nbsp; *(Version V11)*


Eine V12-Datenbank kann nur auf einem logischen V12-Server gehostet werden. Und ein V12-Server kann nur V12 Datenbanken hosten.


Wenn Sie noch nicht V12 ausführen, können Sie ein Upgrade des logischen Servers ausführen, indem Sie die Schritte unter [Upgrade auf SQL-Datenbank V12 vorhanden](sql-database-v12-plan-prepare-upgrade.md) befolgen.


## <a name="V12AzureSqlDbPreviewGaTable"></a> Regionen mit allgemeiner Verfügbarkeit


- Am 31. Juli 2015 wurden alle Regionen auf die "Allgemeine Verfügbarkeit" heraufgestuft.
- V12 wurde im Dezember 2014 freigegeben, zunächst jedoch nur mit dem Status "Vorschau".

[Ergänzende Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

<!---HONumber=AcomDC_0323_2016-->