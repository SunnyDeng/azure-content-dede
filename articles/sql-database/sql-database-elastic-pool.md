<properties
	pageTitle="Pool für elastische Datenbanken für SQL-Datenbanken | Microsoft Azure"
	description="Erfahren Sie, wie Sie das explosionsartige Wachstum von SQL-Datenbanken mit Pools für elastische Datenbanken verwalten können, sodass die verfügbaren Ressourcen von vielen Datenbanken gemeinsam genutzt werden."
	keywords="elastische Datenbank, SQL-Datenbanken"	
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/11/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Verwalten des explosionsartigen Wachstums von SQL-Datenbanken durch die Verwendung elastischer Datenbanken zur gemeinsamen Ressourcennutzung

Ein SaaS-Entwickler muss Dutzende, Hunderte oder sogar Tausende von SQL-Datenbanken erstellen und verwalten. Elastische Pools vereinfachen die Erstellung, Wartung und Leistungsverwaltung über diese Datenbanken hinweg innerhalb eines von Ihnen kontrollierten Budgets. Fügen Sie dem Pool je nach Bedarf Datenbanken hinzu, oder entfernen Sie diese. Sie können innerhalb weniger Minuten über das Microsoft Azure-Portal oder mit [PowerShell](sql-database-elastic-pool-powershell.md) oder [C#](sql-database-elastic-pool-csharp.md) für Ihre SQL-Datenbanken einen [Pool für elastische Datenbanken erstellen](sql-database-elastic-pool-portal.md).

Weitere Informationen zur API und zu Fehlern finden Sie unter [Referenz für Pools für elastische Datenbanken](sql-database-elastic-pool-reference.md).

## So funktioniert's

Ein gängiges SaaS-Anwendungsmuster besteht darin, dass jedem Kunden eine Datenbank zugeordnet wird. Jeder Kunde (jede Datenbank) verfügt über nicht vorhersagbare Ressourcenanforderungen (CPU/E/A/Arbeitsspeicher als DTU zusammengefasst). Wie ordnen Sie bei einer schwankenden Nutzung mit Spitzen- und Tiefstwerten also Ressourcen zu? Zwei Optionen: (1) Sie ordnen basierend auf der Nutzung zu Spitzenzeiten zu viele Ressourcen zu – und zahlen zu viel. (2) Oder Sie planen die Bereitstellung unterdimensioniert zur Kosteneinsparung – und nehmen Einbußen bei der Leistung und Kundenzufriedenheit während der Spitzenzeiten in Kauf.

Dieses Problem wird mit Pools für elastische Datenbanken gelöst.

> [AZURE.VIDEO elastic-databases-helps-saas-developers-tame-explosive-growth]

Mit Pools für elastische Datenbanken wird sichergestellt, dass Datenbanken über die erforderlichen Leistungsressourcen zum benötigten Zeitpunkt verfügen, wobei gleichzeitig ein einfacher Mechanismus für die Ressourcenzuordnung und ein vorhersagbares Budget geboten werden. Ein Pool erhält eine festgelegte Anzahl von eDTUs zu einem festen Preis. Im Pool können einzelne Datenbanken die automatische Skalierung innerhalb der angegebenen Parameter flexibel automatisch skalieren. Bei hoher Auslastung kann eine Datenbank mehr eDTUs nutzen, um den Bedarf zu befriedigen. Datenbanken verbrauchen bei geringerer Auslastung weniger Ressourcen, und Datenbanken ohne Auslastung verbrauchen gar keine eDTUs. Durch die Bereitstellung von Ressourcen für den gesamten Pool und nicht nur für einzelne Datenbanken vereinfachen Sie Ihre Verwaltungsaufgaben. Außerdem verfügen Sie über ein vorhersagbares Budget für den Pool.

Einem vorhandenen Pool können zusätzliche eDTUs hinzugefügt werden, ohne dass es zu Datenbankausfällen oder einer negativen Auswirkung auf die Datenbanken kommt. Ebenso können zusätzliche eDTUs, die nicht mehr benötigt werden, zu jedem beliebigen Zeitpunkt aus einem vorhandenen Pool entfernt werden.

Und Sie können dem Pool Datenbanken hinzufügen oder Datenbanken aus dem Pool entfernen. Wenn die Ressourcen für eine Datenbank voraussichtlich nicht ausgeschöpft werden, sollten Sie sie entfernen.

## Welche Datenbanken werden in einem Pool angeordnet?

![SQL-Datenbanken, die eDTUs in einem Pool für elastische Datenbanken gemeinsam nutzen][1]

Datenbanken, die für Pools für elastische Datenbanken besonders geeignet sind, weisen in der Regel Zeiträume der Aktivität sowie Zeiträume der Inaktivität auf. Im obigen Beispiel können Sie die Aktivität einer einzelnen Datenbank, von vier Datenbanken und schließlich eines Pools für elastische Datenbanken mit 20 Datenbanken sehen. Datenbanken mit veränderlicher Aktivität im Laufe der Zeit eignen sich besonders für elastische Pools, da sie nicht alle zur gleichen Zeit aktiv sind und eDTUs gemeinsam nutzen können. Nicht alle Datenbanken entsprechen jedoch diesem Muster. Datenbanken mit einem konstanteren Ressourcenbedarf eignen sich besser für die Basic-, Standard- und Premium-Dienstebenen, bei denen Ressourcen einzeln zugewiesen werden.

[Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md)


> [AZURE.NOTE] Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

## Aufträge für die elastische Datenbank

Mit einem Pool werden die Verwaltungsaufgaben vereinfacht, indem Skripts in **[elastischen Aufträgen](sql-database-elastic-jobs-overview.md)** ausgeführt werden. Durch einen Auftrag für die elastische Datenbank werden die meisten aufwändigen Schritte beseitigt, die bei der Verwendung einer großen Zahl von Datenbanken anfallen. Lesen Sie sich [Erste Schritte mit Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-getting-started.md) durch, um zu beginnen.

Weitere Informationen zu anderen Tools finden Sie im [Lernplan für Tools für elastische Datenbanken](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/).

## Funktionen der Geschäftskontinuität für Datenbanken in einem Pool

Derzeit unterstützen elastische Datenbanken in der Vorschau die meisten [Funktionen der Geschäftskontinuität](sql-database-business-continuity.md), die auch für Einzeldatenbanken auf V12-Servern zur Verfügung stehen.

### Point-in-Time-Wiederherstellung

Datenbanken in einem Pool für elastische Datenbanken werden automatisch vom System gesichert, und die Aufbewahrungsrichtlinie für die Sicherung stimmt mit der entsprechenden Dienstebene für einzelne Datenbanken überein. Die Datenbanken der einzelnen Ebenen haben also jeweils einen anderen Wiederherstellungsbereich:

* **Basic-Pool**: Die Wiederherstellung ist für jeden Zeitpunkt innerhalb der letzten sieben Tage möglich. 
* **Standard-Pool**: Die Wiederherstellung ist für jeden Zeitpunkt innerhalb der letzten 14 Tage möglich.
* **Premium-Pool**: Die Wiederherstellung ist für jeden Zeitpunkt innerhalb der letzten 35 Tage möglich. 

Während der Vorschau werden Datenbanken in einem Pool als eine neue Datenbank im gleichen Pool wiederhergestellt. Gelöschte Datenbanken werden immer als eigenständige Datenbank außerhalb des Pools in der niedrigsten Leistungsstufe für diese Dienstebene wiederhergestellt. Beispielsweise wird eine elastische Datenbank in einem Standard-Pool, die gelöscht wird, als S0-Datenbank wiederhergestellt. Sie können Datenbank-Wiederherstellungsvorgänge über das Azure-Verwaltungsportal oder programmgesteuert mithilfe der REST-API ausführen. PowerShell-Cmdlet-Unterstützung wird demnächst zur Verfügung stehen.

### Geografische Wiederherstellung

Mit der geografischen Wiederherstellung können Sie eine Datenbank in einem Pool auf einem Server an einem anderen Standort wiederherstellen. Bei der Vorschau muss zum Wiederherstellen einer Datenbank in einem Pool auf einem anderen Server der Pool auf dem Zielserver über den gleichen Namen wie der Quellpool verfügen. Bei Bedarf erstellen Sie einen neuen Pool auf dem Zielserver und geben ihm den gleichen Namen wie vor dem Wiederherstellen der Datenbank. Die geografische Wiederherstellung schlägt fehl, wenn kein Pool mit dem gleichen Namen auf dem Zielserver vorhanden ist. Informationen finden Sie unter [Wiederherstellen mit geografischer Wiederherstellung](sql-database-disaster-recovery.md#recover-using-geo-restore).


### Georeplikation

Georeplikation ist für jede Datenbank in einem Standard- oder Premiumpool mit elastischen Datenbanken verfügbar. Eine oder alle Datenbanken in einer Georeplikationspartnerschaft können in einem Pool mit elastischen Datenbanken enthalten sein, sofern die Dienstebenen identisch sind. Sie können die Georeplikation für Pools mit elastischen Datenbanken mit dem [Azure-Portal](sql-database-geo-replication-portal.md), [PowerShell](sql-database-geo-replication-powershell.md) oder [Transact-SQL](sql-database-geo-replication-transact-sql.md) konfigurieren.

### Import und Export

Das Exportieren einer Datenbank aus einem Pool wird unterstützt. Derzeit wird das direkte Importieren einer Datenbank in einen Pool nicht unterstützt, aber Sie können Daten in eine Einzeldatenbank importieren und die Datenbank anschließend in einen Pool verschieben.


<!--Image references-->
[1]: ./media/sql-database-elastic-pool/databases.png

<!---HONumber=AcomDC_0218_2016-->