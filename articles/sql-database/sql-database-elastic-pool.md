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
	ms.date="09/23/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Verwalten des explosionsartigen Wachstums elastischer Datenbanken

Für SaaS-Entwickler, die Dutzende, Hunderte oder sogar Tausende von Datenbanken haben, vereinfacht ein Pool für elastische Datenbanken das Erstellen, Warten und Verwalten der Leistung und der Kosten der gesamten Datenbankgruppe, sodass das geplante Budget eingehalten werden kann.

Ein gängiges Muster bei SaaS-Anwendungen ist, für jede Kunden eine andere Datenbank zu implementieren, jeweils mit unterschiedlicher und unvorhersehbarer Nutzung von Systemressourcen (CPU, E/A, Speicher zusammengefasst mit DTU). Mit diesen Spitzen- und Tiefstwerten beim Bedarf der einzelnen Datenbanken können Prognosen schwierig sein, sodass die Bereitstellung von Ressourcen erschwert wird. Es gibt zwei Möglichkeiten: entweder planen Sie Datenbankressourcen überdimensioniert für Spitzenauslastungen – und zahlen dann auch mehr. Oder Sie planen die Bereitstellung unterdimensioniert zur Kosteneinsparung und nehmen Einbußen bei der Leistung und Kundenzufriedenheit während Spitzenzeiten in Kauf.

Microsoft hat Pools für elastische Datenbanken speziell zur Behebung dieses Problems entwickelt.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]


Pools für elastische Datenbanken bieten eine Lösung für Kunden, die sicherstellen müssen, dass ihre Datenbanken über die erforderlichen Leistungsressourcen zum benötigten Zeitpunkt verfügen, wobei gleichzeitig ein einfacher Mechanismus für die Ressourcenzuordnung und ein vorhersagbares Budget geboten werden. Die bedarfsgesteuerte Leistungsskalierung einzelner Datenbanken innerhalb eines Pools für elastische Datenbanken ist möglich, da jede Datenbank innerhalb eines Pools eDTUs aus einer gemeinsam genutzten Gruppe verwendet, die dem Pool zugeordnet ist. Dadurch können Datenbanken mit hoher Auslastung mehr eDTUs nutzen, um die Anforderungen zu erfüllen, während Datenbanken mit geringer Auslastung weniger und Datenbanken ohne Auslastung gar keine eDTUs nutzen. Indem Ressourcen dem Pool und nicht einzelnen Datenbanken bereitgestellt werden, wird nicht nur die Verwaltung mehrerer Datenbanken vereinfacht, sondern auch ein vorhersagbares Budget für eine andernfalls nicht vorhersagbare Workload ermöglicht.

Wenn weitere eDTUs zum Erfüllen der Anforderungen eines Pools erforderlich sind (zusätzliche Datenbanken werden einem Pool hinzugefügt oder die vorhandenen Datenbanken verwenden mehr eDTUs), können einem vorhandenen Pool zusätzliche eDTUs ohne Datenbankausfall oder negative Auswirkung auf die Datenbanken hinzugefügt werden. Ebenso können zusätzliche eDTUs, die nicht mehr benötigt werden, zu jedem beliebigen Zeitpunkt aus einem vorhandenen Pool entfernt werden.

![Datenbanken mit gemeinsamer Nutzung von eDTUs][1]

Datenbanken, die für Pools für elastische Datenbanken besonders geeignet sind, weisen in der Regel Zeiträume der Aktivität sowie Zeiträume der Inaktivität auf. Sehen Sie sich das Beispiel oben an. Hier können Sie die Aktivität einer einzelnen Datenbank, von vier Datenbanken und schließlich eines Pools für elastische Datenbanken mit 20 Datenbanken sehen. Diese Datenbanken mit veränderlicher Aktivität im Laufe der Zeit eignen sich besonders für Pools für elastische Datenbanken, da sie nicht alle zur gleichen Zeit aktiv sind und eDTUs gemeinsam nutzen können. Nicht alle Datenbanken entsprechen jedoch diesem Muster. Es gibt Datenbanken mit einem konstanteren Ressourcenbedarf. Diese Datenbanken eignen sich besser für die Basic-, Standard- und Premium-Dienstebenen, bei denen Ressourcen einzeln zugewiesen werden. Hilfe beim Ermitteln, ob Ihre Datenbanken von einem Pool für elastische Datenbanken profitieren würden, finden Sie unter [Überlegungen zum Preis und zur Leistung eines elastischen Datenbankpools](sql-database-elastic-pool-guidance.md).

Sie können einen Pool für elastische Datenbanken innerhalb weniger Minuten über das Microsoft Azure-Portal oder mit PowerShell oder C# erstellen. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md). Ausführliche Informationen zu Pools für elastische Datenbanken, einschließlich API- und Fehlerinformationen, finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).


> [AZURE.NOTE]Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

## Einfaches Verwalten einer großen Anzahl von Datenbanken mit elastischen Datenbanktools

Zusätzlich zur Bereitstellung von effizienter Ressourcennutzung und vorhersagbarer Leistung vereinfachen Pools für elastische Datenbanken die SaaS-Anwendungsentwicklung mit Tools, die die Erstellung und Verwaltung der Datenebene erleichtern. Das Ausführen von Wartungsaufgaben und die Implementierung von Änderungen für eine große Gruppe von Datenbanken war in der Vergangenheit ein zeitaufwändiger und komplizierter Prozess, der auf die Ausführung von Skripts in elastischen Aufträgen reduziert wurde. Die Möglichkeit zum Erstellen und Ausführen eines elastischen Datenbankauftrags beseitigt nahezu alle schwierigen Aufgaben im Zusammenhang mit der Verwaltung von Hunderten oder gar Tausenden von Datenbanken. Eine Übersicht über den Dienst für elastische Datenbankaufträge, der die Ausführung von Transact-SQL-Skripts in allen elastischen Datenbanken in einem Pool ermöglicht, finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

Außerdem ist ein umfassender und leistungsfähiger Satz von Entwicklertools zum Implementieren von Anwendungsmustern für elastische Datenbanken verfügbar. Bei Sharding-Datenbanken können Sie mit anderen Tools, wie z. B. dem Split-Merge-Dienstprogramm, Daten aus einem Shard unterteilen und in einem anderen zusammenführen. Dies sorgt für eine erhebliche Verringerung des Arbeitsaufwands bei der Verwaltung von umfangreichen Sharding-Datenbanken. Weitere Informationen finden Sie unter der [Themenübersicht für elastische Datenbanktools](sql-database-elastic-scale-documentation-map.md).

## Funktionen der Geschäftskontinuität für Datenbanken in einem Pool

Derzeit unterstützen elastische Datenbanken in der Vorschau die meisten [Funktionen der Geschäftskontinuität](https://msdn.microsoft.com/library/azure/hh852669.aspx), die auch für Einzeldatenbanken auf V12-Servern zur Verfügung stehen.

### Sichern und Wiederherstellen von Datenbanken ([Zeitpunktwiederherstellung](https://msdn.microsoft.com/library/azure/hh852669.aspx#BKMK_PITR))

Datenbanken in einem Pool für elastische Datenbanken werden automatisch vom System gesichert, und die Aufbewahrungsrichtlinie für die Sicherung stimmt mit der entsprechenden Dienstebene für einzelne Datenbanken überein. Genauer gesagt kann eine elastische Datenbank in einem Basic-Pool mit einem beliebigen Wiederherstellungspunkt innerhalb der letzten sieben Tage wiederhergestellt werden, eine elastische Datenbank in einem Standard-Pool kann mit einem beliebigen Wiederherstellungspunkt innerhalb der letzten 14 Tage wiederhergestellt werden, und eine elastische Datenbank in einem Premium-Pool kann mit einem beliebigen Wiederherstellungspunkt innerhalb der letzten 35 Tage wiederhergestellt werden. Während der Vorschau werden Datenbanken in einem Pool als eine neue Datenbank im gleichen Pool wiederhergestellt. Gelöschte Datenbanken werden immer als eigenständige Datenbank außerhalb des Pools in der niedrigsten Leistungsstufe für diese Dienstebene wiederhergestellt. Beispielsweise wird eine elastische Datenbank in einem Standard-Pool, die gelöscht wird, als S0-Datenbank wiederhergestellt. Sie können Datenbank-Wiederherstellungsvorgänge über das Azure-Verwaltungsportal oder programmgesteuert mithilfe der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.

### [Geografische Wiederherstellung](https://msdn.microsoft.com/library/azure/hh852669.aspx#BKMK_GEO)

Mit der geografischen Wiederherstellung können Sie eine Datenbank in einem Pool auf einem Server an einem anderen Standort wiederherstellen. Bei der Vorschau muss zum Wiederherstellen einer Datenbank in einem Pool auf einem anderen Server der Pool auf dem Zielserver über den gleichen Namen wie der Quellpool verfügen. Bei Bedarf erstellen Sie einen neuen Pool auf dem Zielserver und geben ihm den gleichen Namen wie vor dem Wiederherstellen der Datenbank. Die geografische Wiederherstellung schlägt fehl, wenn kein Pool mit dem gleichen Namen auf dem Zielserver vorhanden ist. Sie können Vorgänge zur geografischen Wiederherstellung mithilfe des Azure-Portals oder der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.


### [Georeplikation](https://msdn.microsoft.com/library/azure/dn783447.aspx)

Datenbanken, für die bereits die Georeplikation aktiviert wurde, können in und aus einem Pool für elastische Datenbanken verschoben werden, und die Replikation wird weiterhin wie gewohnt funktionieren. Sie können für eine Datenbank, die bereits in einem Pool ist, die Georeplikation aktivieren, wenn der angegebene Zielserver einen Pool mit dem gleichen Namen wie der Quellpool hat.

### Import und Export

Das Exportieren einer Datenbank aus einem Pool wird unterstützt. Derzeit wird das direkte Importieren einer Datenbank in einen Pool nicht unterstützt, aber Sie können Daten in eine Einzeldatenbank importieren und die Datenbank anschließend in einen Pool verschieben.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=Oct15_HO2-->