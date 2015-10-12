<properties 
   pageTitle="Häufig gestellte Fragen zur Einstellung von Web Edition und Business Edition | Microsoft Azure"
   description="Erfahren Sie, wann die Web- und Business-Datenbanken in Azure SQL-Datenbank eingestellt werden und welche Features und Funktionen die neuen Dienstebenen bieten."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jeffreyg"
   editor="monicar" />
<tags 
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/30/2015"
   ms.author="sstein" />

# Häufig gestellte Fragen zur Einstellung von Web Edition und Business Edition

Azure SQL Web und Business-Datenbanken wurden eingestellt. Die Dienstebenen Basic, Standard, Premium und Elastic ersetzen die eingestellten Web- und Business-Datenbanken.

Um Sie bei der Aktualisierung von Web- und Business-Datenbanken zu unterstützen, empfiehlt der SQL-Datenbank-Dienst eine geeignete Dienstebene und Leistungsstufe (Tarif) für jede Datenbank, die auf der bisherigen Workload basieren sollten.

**Abrufen von Tarifempfehlungen**:

- [Upgrade auf SQL-Datenbank V12 mithilfe des Azure-Portals](sql-database-v12-upgrade.md)
- [Upgrade auf SQL-Datenbank V12 mithilfe von PowerShell](sql-database-upgrade-server.md)
- [Ändern des Tarifs einer Web- oder Business-Datenbank](sql-database-service-tier-advisor.md)
 


## Warum werden meine Web und Business Edition-Datenbanken im Azure-Verwaltungsportal als zurückgezogen angezeigt?

Da Datenbanken der Web und Business Editionen nach dem September 2015 nicht mehr verfügbar sind, werden Web- und Business-Datenbanken im Verwaltungsportal als zurückgezogen gekennzeichnet. Web- und Business-Datenbanken können noch wie gewohnt bereitgestellt und verwaltet werden, aber die Kennzeichnung "Zurückgezogen" dient als Erinnerung, dass für neue Datenbanken die Dienstebenen Basic, Standard oder Premium verwendet werden sollten. Die Bezeichnung "Zurückgezogen" soll auch daran erinnern, dass vorhandene Web- und Business-Datenbanken auf Standard, Basic oder Premium aktualisiert werden sollten. Ausführliche Informationen zum Aktualisieren vorhandener Web- oder Business-Datenbanken auf die neuen Dienstebenen finden Sie unter [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](sql-database-upgrade-new-service-tiers.md).

## Welche neue Dienstebene ist die beste Wahl für die Aktualisierung meiner vorhandenen Web- oder Business-Datenbank?

Die Auswahl einer geeigneten neuen Dienstebene und Leistungsstufe für vorhandene Web- oder Business-Datenbanken hängt von den spezifischen Feature- und Leistungsanforderungen der Anwendung ab.

Nutzen Sie die Tarifempfehlungen oben, oder lesen Sie ausführliche Informationen zur Auswahl einer geeigneten neue Dienstebene unter [Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](sql-database-upgrade-new-service-tiers.md).

## Warum führt Microsoft neue Dienstebenen ein?

Aufgrund von Kundenfeedback führt Azure SQL-Datenbank neue Dienstebenen ein, damit Kunden Workloads für relationale Datenbanken noch einfacher unterstützen können. Die neuen Dienstebenen sind darauf ausgelegt, eine vorhersagbare Leistung über ein Spektrum von sieben Ebenen für geringe bis sehr hohe Transaktionsanforderungen von Anwendung zu bieten. Darüber hinaus bieten die neuen Dienstebenen eine Reihe von Business Continuity-Features, eine strengere SLA für die Betriebszeit, größere Datenbanken für weniger Geld und ein verbessertes Abrechnungssystem.

## Wo kann ich mehr über die neuen Dienstebenen erfahren?

Ausführliche Informationen zu den neuen Dienstebenen und dem Leistungsmodell finden Sie unter [Dienstebenen](sql-database-service-tiers.md). Ausführliche Preisinformationen zu den neuen Dienstebenen finden Sie unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/).

## Welche Features oder Funktionen werden in Basic, Standard und Premium nicht verfügbar sein?

Das Verbundfeature wird mit den Web und Business Editionen eingestellt. Für Kunden, die ihre Datenbanken horizontal hochskalieren müssen, wird stattdessen die Verwendung von [elastischen Datenbanktools](sql-database-elastic-scale-get-started.md) oder Migration auf diese für [Azure SQL-Datenbank](sql-database-elastic-scale-get-started.md) empfohlen. Diese vereinfachen die Erstellung und Verwaltung einer Anwendung, die Sharding verwendet. Mithilfe einer .NET-Clientbibliothek können Anwendungen definieren, wie Daten Shards zugeordnet werden, und OLTP-Anfragen an die entsprechenden Datenbanken weiterleiten. Zur Unterstützung von Verwaltungsvorgängen, mit der die Datenaufteilung zwischen Shards neu konfiguriert wird, steht eine Azure Cloud Service-Vorlage zur Verfügung, die Sie in Ihrem eigenen Azure-Abonnement hosten können. Zusätzlich zu [elastischen Datenbanktools](sql-database-elastic-scale-get-started.md) erstellt und veröffentlicht Microsoft auch weiterhin [benutzerdefinierte Sharding-Muster und -Leitfäden](https://msdn.microsoft.com/library/azure/dn764977.aspx) basierend auf umfassenden Kundenprojekten. Neue Kunden, die Funktionen zum horizontalen Hochskalieren benötigen, sollten sich die [elastischen Datenbanktools](sql-database-elastic-scale-get-started.md) ansehen und/oder sich an den Microsoft-Support wenden, um alle Möglichkeiten zu erfahren.

Microsoft ändert auch die Funktionen zum Kopieren von Datenbanken bei Premium-Datenbanken. Da bisher das Datenbankkontingent begrenzt war, erstellte CREATE DATABASE … AS A COPY OF in T-SQL eine "Premium-Aussetzung"-Datenbank ohne reservierte Ressourcen, die wie eine Business-Datenbank abgerechnet wurde. Da nun Premium-Kontingente flexibler erhältlich sind, wird die Premium-Aussetzung nicht mehr unterstützt. Datenbankkopien werden nun mit derselben Edition und Leistungsstufe wie die Quelldatenbank erstellt und entsprechend abgerechnet. Kunden können kopierte Datenbanken auch auf eine andere Dienstebene oder Leistungsstufe herabstufen, um ihre Kosten zu senken. Vorhandene "Premium-Aussetzung"-Datenbanken werden in dieser Version in Business Edition konvertiert. Es ist davon auszugehen, dass die Einführung der Point-in-Time-Wiederherstellung die Notwendigkeit der Erstellung von Sicherungskopien von Datenbanken verringern wird.

## Wie verbessern die Tarife Basic, Standard und Premium das Abrechnungssystem?

Azure SQL-Datenbanken der Editionen Basic, Standard und Premium werden nach Stunden berechnet. Sie haben die Möglichkeit, jede Datenbank innerhalb eines 24-Stunden-Intervalls viermal auf eine höhere oder niedrigere Stufe zu skalieren. Die Abrechnung erfolgt mit einer festen Rate basierend auf der höchsten Dienstebene und Leistungsstufe, die Sie für jede Stunde gewählt haben. Darüber hinaus werden die Leistungsstufen (Beispiel: Basic, S1 und P2) in der Rechnung aufgeschlüsselt, um noch einfacher die Anzahl der Datenbanktage/-stunden zu erkennen, die im jeweiligen Monat pro Leistungsstufe entstanden sind. Web- und Business-Datenbanken werden weiterhin mithilfe von Datenbank-Einheiten abgerechnet, die auf der Größe der Datenbank basieren. Weitere Informationen zu den Preisen und den Unterschieden zwischen den neuen Dienstebenen finden Sie unter [SQL-Datenbank Preisseite](http://azure.microsoft.com/pricing/details/sql-database/).


## Weitere Informationen

[Azure SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)

[Dienstebenen](sql-database-service-tiers.md)

[Upgrade von Web-/Business-Datenbanken der SQL-Datenbank auf neue Dienstebenen](sql-database-upgrade-new-service-tiers.md)

<!---HONumber=Oct15_HO1-->