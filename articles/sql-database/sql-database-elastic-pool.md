<properties 
	pageTitle="Verwalten des explosionsartigen Wachstums elastischer Datenbanken" 
	description="Ein Pool für elastische Datenbanken in Azure SQL-Datenbank stellt eine Sammlung der verfügbaren Ressourcen dar, die von einer Gruppe von elastischen Datenbanken gemeinsam verwendet werden." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="08/12/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Verwalten des explosionsartigen Wachstums elastischer Datenbanken

Für SaaS-Entwickler, die Dutzende, Hunderte oder sogar Tausende von Datenbanken haben, vereinfacht ein Pool für elastische Datenbanken das Erstellen, Warten und Verwalten der Leistung und der Kosten der gesamten Datenbankgruppe, sodass das geplante Budget eingehalten werden kann.

Ein gängiges Muster bei SaaS-Anwendungen ist, für jede Kunden eine andere Datenbank zu implementieren, jeweils mit unterschiedlicher und unvorhersehbarer Nutzung von Systemressourcen \(CPU, E/A, Speicher zusammengefasst mit DTU\). Mit diesen Spitzen- und Tiefstwerten beim Bedarf der einzelnen Datenbanken können Prognosen schwierig sein, sodass die Bereitstellung von Ressourcen erschwert wird. Es gibt zwei Möglichkeiten: entweder planen Sie Datenbankressourcen überdimensioniert für Spitzenauslastungen – und zahlen dann auch mehr. Oder Sie planen die Bereitstellung unterdimensioniert zur Kosteneinsparung und nehmen Einbußen bei der Leistung und Kundenzufriedenheit während Spitzenzeiten in Kauf.

Microsoft hat Pools für elastische Datenbanken speziell zur Behebung dieses Problems entwickelt.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Ein Pool für elastische Datenbanken stellt eine Sammlung der verfügbaren Ressourcen dar, die von den elastischen Datenbanken im Pool gemeinsam verwendet werden. Sie können Datenbanken zu einem beliebigen Zeitpunkt zum Pool hinzufügen oder daraus entfernen. Diese Datenbanken nutzen die Ressourcen \(in Form von elastischen Datenbank-Durchsatzeinheiten oder eDTUs\) und Speicherkapazitäten des Pools gemeinsam. Jede Datenbank verwendet jedoch nur die Ressourcen, die zu einem bestimmten Zeitpunkt erforderlich sind. Dadurch sind die Ressourcen für andere Datenbanken verfügbar, wenn diese benötigt werden. Anstelle einer Überdimensionierung einzelner Datenbanken mit daraus resultierenden Kosten für nicht genutzte Ressourcen, reservieren Sie diese nach Bedarf und zahlen einen vorhersagbaren Preis für zusammengefasste Ressourcen des Pools. Dadurch werden die Kosten verteilt, wodurch Sie ein konkurrenzfähiges Geschäftsmodell erhalten. Außerdem bietet jede Datenbank Anpassungsmöglichkeiten bei der Leistung.

Datenbanken, die sich gut für Pools für elastische Datenbanken eignen, sind i. d. R. weniger als 50 % der Zeit aktiv. Ein typisches Aktivitätsmuster ist, dass Datenbanken einige Zeit inaktiv bleiben, einige Zeit mit wenig Ressourcenanforderungen aktiv und dann mit hohen Ressourcenanforderungen aktiv sind. Nicht alle Datenbanken entsprechen jedoch diesem Muster. Es gibt Datenbanken mit einem konstanteren Ressourcenbedarf. Diese Datenbanken eignen sich besser für die Basic-, Standard- und Premium-Dienstebenen, bei denen Ressourcen einzelnen Datenbanken zugewiesen werden. Hilfe beim Ermitteln, ob Ihre Datenbanken von einem Pool für elastische Datenbanken profitieren würden, finden Sie unter[Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md).

Sie können einen Pool für elastische Datenbanken innerhalb weniger Minuten über das Microsoft Azure-Portal oder mit PowerShell oder C\# erstellen. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md). Weitere Informationen zu Pools für elastische Datenbanken, einschließlich API- und Fehlerinformationen, finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

## Einfaches Verwalten einer großen Anzahl von Datenbanken mit elastischen Datenbanktools

Zusätzlich zur Bereitstellung von effizienter Ressourcennutzung und vorhersagbarer Leistung vereinfachen Pools für elastische Datenbanken die SaaS-Anwendungsentwicklung mit Tools, die die Erstellung und Verwaltung der Datenebene erleichtern. Das Ausführen von Wartungsaufgaben und die Implementierung von Änderungen für eine große Gruppe von Datenbanken war in der Vergangenheit ein zeitaufwändiger und komplizierter Prozess, der auf die Ausführung von Skripts in elastischen Aufträgen reduziert wurde. Die Möglichkeit zum Erstellen und Ausführen eines elastischen Datenbankauftrags beseitigt nahezu alle schwierigen Aufgaben im Zusammenhang mit der Verwaltung von Hunderten oder gar Tausenden von Datenbanken. Eine Übersicht über den Dienst für elastische Datenbankaufträge, der die Ausführung von T-SQL-Skripts in allen elastischen Datenbanken in einem Pool ermöglicht, finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

Außerdem ist ein umfassender und leistungsfähiger Satz von Entwicklertools zum Implementieren von Anwendungsmustern für elastische Datenbanken verfügbar. Bei Sharding-Datenbanken können Sie mit anderen Tools, wie z. B. dem Split-Merge-Dienstprogramm, Daten aus einem Shard unterteilen und in einem anderen zusammenführen. Dies sorgt für eine erhebliche Verringerung des Arbeitsaufwands bei der Verwaltung von umfangreichen Sharding-Datenbanken. Weitere Informationen finden Sie unter der [Themenübersicht für elastische Datenbanktools](sql-database-elastic-scale-documentation-map.md).

## Funktionen der Geschäftskontinuität für Datenbanken in einem Pool

Derzeit unterstützen elastische Datenbanken in der Vorschau die meisten Funktionen, die auch für Einzeldatenbanken zur Verfügung stehen.

### Sichern und Wiederherstellen von Datenbanken \(Zeitpunktwiederherstellung\)

Datenbanken in einem Pool für elastische Datenbanken werden automatisch vom System gesichert und die Aufbewahrungsrichtlinie für die Sicherung entspricht der einzelner Datenbanken. Während der Vorschau werden die Datenbanken in einem Pool als neue Datenbanken im gleichen Pool wiederhergestellt. Gelöschte Datenbanken werden immer als eigenständige Standard-S0-Datenbank außerhalb des Pools wiederhergestellt. Sie können Datenbank-Wiederherstellungsvorgänge über das Azure-Verwaltungsportal oder programmgesteuert mithilfe der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.

### Geografische Wiederherstellung

Mit der geografischen Wiederherstellung können Sie eine Datenbank in einem Pool auf einem Server an einem anderen Standort wiederherstellen. Bei der Vorschau muss zum Wiederherstellen einer Datenbank in einem Pool auf einem anderen Server der Pool auf dem Zielserver über den gleichen Namen wie der Quellpool verfügen. Bei Bedarf erstellen Sie einen neuen Pool auf dem Zielserver und geben ihm den gleichen Namen wie vor dem Wiederherstellen der Datenbank. Die geografische Wiederherstellung schlägt fehl, wenn kein Pool mit dem gleichen Namen auf dem Zielserver vorhanden ist. Sie können Vorgänge zur geografischen Wiederherstellung mithilfe des Azure-Portals oder der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.


### Georeplikation

Datenbanken, für die bereits die Georeplikation aktiviert wurde, können in und aus einem Pool für elastische Datenbanken verschoben werden, und die Replikation wird weiterhin wie gewohnt funktionieren. Sie können für eine Datenbank, die bereits in einem Pool ist, die Georeplikation aktivieren, wenn der angegebene Zielserver einen Pool mit dem gleichen Namen wie der Quellpool hat. Derzeit können Sie in der Vorschau die Georeplikation nicht für eine Datenbank aktivieren, die bereits in einem Pool zu einem Pool mit einem anderen Namen oder zu einer sekundären Singleton-Datenbank ist.



 

<!---HONumber=August15_HO7-->