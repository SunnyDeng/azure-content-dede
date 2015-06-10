<properties 
	pageTitle="Elastische Pools in Azure SQL-Datenbank (Vorschau)" 
	description="Ein elastischer Datenbankpool stellt eine Sammlung der verfügbaren Ressourcen dar, die von einer Gruppe von elastischen Datenbanken gemeinsam verwendet werden." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/11/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Elastische SQL-Datenbankpools (Vorschau)

Für SaaS-Entwickler, die Dutzende, Hunderte oder sogar Tausende von Datenbanken haben, vereinfacht ein elastischer Datenbankpool das Erstellen, Warten und Verwalten der Leistung und der Kosten der gesamten Datenbankgruppe, sodass das geplante Budget eingehalten werden kann.

Ein gängiges Muster bei SaaS-Anwendungen ist, für jede Datenbank einen anderen Kunden zu implementieren, jeweils mit unterschiedlichem und unvorhersehbarem Verbrauch von Systemressourcen (CPU, E/A, Speicher zusammengefasst mit eDTU). Mit diesen Spitzen- und Tiefstwerten beim Bedarf der einzelnen Datenbanken können Prognosen schwierig sein, sodass die Bereitstellung von Ressourcen erschwert wird. Es gibt zwei Möglichkeiten: entweder planen Sie Datenbankressourcen überdimensioniert für Spitzenauslastungen – und zahlen dann auch mehr. Oder Sie planen die Bereitstellung unterdimensioniert zur Kosteneinsparung und nehmen Einbußen bei der Leistung und Kundenzufriedenheit während Spitzenzeiten in Kauf.

Microsoft hat elastische Pools speziell zur Behebung dieses Problems entwickelt.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Ein elastischer Datenbankpool stellt eine Sammlung der verfügbaren Ressourcen dar, die von den elastischen Datenbanken im Pool gemeinsam verwendet werden. Sie können Datenbanken zu einem beliebigen Zeitpunkt zum Pool hinzufügen oder daraus entfernen. Diese elastischen Datenbanken nutzen die Ressourcen (in Form von elastischen Datenbank-Durchsatzeinheiten oder eDTUs) und Speicherkapazitäten des Pools gemeinsam. Jede elastische Datenbank verwendet jedoch nur die Ressourcen, die zu einem bestimmten Zeitpunkt erforderlich sind. Dadurch sind die Ressourcen für andere elastische Datenbanken verfügbar, wenn diese benötigt werden. Anstatt einer Überdimensionierung einzelner Datenbanken mit daraus resultierenden Kosten für nicht genutzte Ressourcen, reservieren Sie diese nach Bedarf und zahlen einen vorhersagbaren Preis für zusammengefasste Ressourcen des elastischen Pools. Dadurch werden die Kosten verteilt, wodurch Sie ein konkurrenzfähiges Geschäftsmodell erhalten. Außerdem bietet jede elastische Datenbank Anpassungsmöglichkeiten bei der Leistung.

Datenbanken, die sich gut für elastische Datenbankpools eignen, sind i. d. R. weniger als 50 % der Zeit aktiv. Ein typisches Aktivitätsmuster ist, dass Datenbanken einige Zeit inaktiv bleiben, einige Zeit mit wenig Ressourcenanforderungen aktiv und dann mit hohen Ressourcenanforderungen aktiv sind.

Nicht alle Datenbanken entsprechen jedoch diesem Muster. Es gibt Datenbanken mit einem konstanteren Ressourcenbedarf. Diese Datenbanken eignen sich besser für die Basic-, Standard- und Premium-Dienstebenen, bei denen Ressourcen einzeln zugewiesen werden.

Sie können einen elastischen Pool innerhalb weniger Minuten über das Microsoft Azure-Portal oder mit PowerShell erstellen. Weitere Informationen finden Sie unter [Erstellen und Verwalten elastischer Pools](sql-database-elastic-pool-portal.md). Weitere Informationen zu elastischen Datenbankpools, einschließlich API- und Fehlerinformationen, finden Sie unter [Referenz für elastische Datenbanken](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Elastische Pools sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

## Einfaches Verwalten einer großen Anzahl von Datenbanken mit elastischen Tools

Zusätzlich zur Bereitstellung von effizienter Ressourcennutzung und vorhersagbarer Leistung vereinfachen elastische Pools die SaaS-Anwendungsentwicklung mit Tools, die die Erstellung und Verwaltung der Datenebene erleichtern. Das Ausführen von Wartungsaufgaben und die Implementierung von Änderungen für eine große Gruppe von Datenbanken war in der Vergangenheit ein zeitaufwändiger und komplizierter Prozess, der auf die Ausführung von Skripts in elastischen Aufträgen reduziert wurde. Die Möglichkeit zum Erstellen und Ausführen eines elastischen Auftrags beseitigt nahezu alle schwierigen Aufgaben im Zusammenhang mit der Verwaltung von Hunderten oder gar Tausenden von Datenbanken. Einen Überblick über den Dienst für elastische Aufträge, der die Ausführung von T-SQL-Skripts in allen elastischen Datenbanken in einem Pool ermöglicht, finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

Außerdem ist ein umfassender und leistungsfähiger Satz von Entwicklertools zum Implementieren von Anwendungsmustern für elastische Datenbanken verfügbar. Bei Sharding-Datenbanken können Sie mit anderen Tools, wie z. B. dem Split-Merge-Dienstprogramm, Daten aus einem Shard unterteilen und in einem anderen zusammenführen. Dies sorgt für eine erhebliche Verringerung des Arbeitsaufwands bei der Verwaltung von umfangreichen Sharding-Datenbanken. Weitere Informationen finden Sie unter der [Themenübersicht für elastische Datenbanktools](sql-database-elastic-scale-documentation-map.md).

## Funktionen der Geschäftskontinuität für elastische Datenbanken

Derzeit unterstützen elastische Datenbanken (auf Standardebene für elastische Datenbanken) in der Vorschau die meisten Funktionen, die auch für die Standard-Datenbankebene zur Verfügung stehen.

### Sichern und Wiederherstellen von Datenbanken (Zeitpunktwiederherstellung)

Elastische Datenbanken werden automatisch vom System gesichert und die Aufbewahrungsrichtlinie für die Sicherung entspricht der Standard-Datenbankebene. Während der Vorschau werden die elastischen Datenbanken in einem Pool als neue elastische Datenbanken im gleichen Pool wiederhergestellt. Gelöschte elastische Datenbanken werden immer als eigenständige Standard-S0-Datenbank außerhalb des Pools wiederhergestellt. Sie können Datenbank-Wiederherstellungsvorgänge über das Azure-Verwaltungsportal oder programmgesteuert mithilfe der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.

### Geografische Wiederherstellung

Mit der geografischen Wiederherstellung können Sie eine Datenbank in einem Pool auf einem Server an einem anderen Standort wiederherstellen. Bei der Vorschau muss zum Wiederherstellen einer Datenbank in einem Pool auf einem anderen Server der Pool auf dem Zielserver über den gleichen Namen wie der Quellpool verfügen. Bei Bedarf erstellen Sie einen neuen Pool auf dem Zielserver und geben ihm den gleichen Namen wie vor dem Wiederherstellen der Datenbank. Die geografische Wiederherstellung schlägt fehl, wenn kein Pool mit dem gleichen Namen auf dem Zielserver vorhanden ist. Sie können Vorgänge zur geografischen Wiederherstellung mithilfe des Azure-Portals oder der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.


### Georeplikation

Datenbanken, für die bereits die Georeplikation aktiviert wurde, können in und aus einem elastischen Pool verschoben werden, und die Replikation wird weiterhin wie gewohnt funktionieren. In der Vorschau können Sie derzeit die Georeplikation nicht für elastische Datenbanken aktivieren, die bereits in einem Pool enthalten sind.

<!---HONumber=58-->