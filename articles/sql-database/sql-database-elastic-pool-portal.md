<properties 
	pageTitle="Erstellen und Verwalten eines elastischen SQL-Datenbankpools (Vorschau)" 
	description="Erstellen Sie einen einzelnen Pool von Ressourcen, den Sie in einer Gruppe von Azure SQL-Datenbanken übergreifend nutzen können." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/24/2015" 
	ms.author="sstein" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA"/>


# Erstellen und Verwalten eines elastischen SQL-Datenbankpools (Vorschau)

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Dieser Artikel beschreibt, wie Sie einen elastischen Pool mit dem [Azure-Portal](https://portal.azure.com) erstellen.

Elastische Pools vereinfachen die Erstellung, Wartung und Verwaltung von sowohl der Leistung als auch der Kosten für eine große Anzahl von Datenbanken.
 

> [AZURE.NOTE]Elastische Pools sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.

 


## Voraussetzungen

Zur Erstellung eines elastischen Pools benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link "Kostenlose Testversion" klicken. Lesen Sie anschließend den Artikel weiter.
- Ein Server mit Azure SQL-Datenbank V12. Wenn Sie nicht über einen Server der Version 12 verfügen, erstellen Sie ihn anhand der Schritte unter [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).



## Erstellen eines elastischen Pools

Erstellen Sie einen elastischen Pool, indem Sie einen neuen Pool zu einem Server hinzufügen. Sie können mehrere Pools zu einem Server hinzufügen, aber jedem Pool kann jeweils nur ein Server zugeordnet werden. Darüber hinaus können alle oder einige der Datenbanken auf einem Server zu einem Pool hinzugefügt werden.


1.	Wählen Sie den Server mit SQL-Datenbank V12 mit den Datenbanken, die Sie dem Pool hinzufügen möchten.
2.	Erstellen Sie den Pool durch Auswählen von **Add pool** am oberen Rand des Blatts **SQL Server**.

   ![Erstellen eines elastischen Pools][1]

## Konfigurieren eines elastischen Pools

Konfigurieren Sie den Pool, indem Sie den Tarif festlegen, Datenbanken hinzufügen und die Leistungsmerkmale des Pools einrichten.

*Bei der Auswahl des Befehls **Add pool** müssen Sie den Bedingungen der Vorschau zustimmen, indem Sie **PREVIEW TERMS** auswählen und das Blatt **Preview Terms** ausfüllen. Sie müssen diesen Bedingungen nur einmal für jedes Abonnement zustimmen.*

   ![Konfigurieren eines elastischen Pools][2]


### Preisstufe

Die Preisgestaltung für elastische Pools ähnelt im Prinzip der für die SQL-Datenbank-Dienstebene. Der Tarif legt die Features für die elastischen Datenbanken im Pool, die maximale Anzahl von DTUs (DTU MAX) und den für jede Datenbank verfügbaren Speicher (GB) fest.

> [AZURE.NOTE]Die Vorschau ist zurzeit auf den Tarif **Standard** beschränkt.

| Preisstufe | DTU MAX pro Datenbank |
| :--- | :--- |
| Standard | 100 MAX DTUs pro Datenbank |

### Hinzufügen von Datenbanken

Sie können zu jedem Zeitpunkt bestimmte Datenbanken auswählen, die in den Pool aufgenommen werden sollen. Wenn Sie einen neuen Pool erstellen, empfiehlt Azure die Datenbanken, die von einem Pool profitieren, und kennzeichnet diese für die Aufnahme. Sie können alle auf dem Server verfügbaren Datenbanken hinzufügen, oder Sie aktivieren oder deaktivieren Datenbanken aus der ursprünglichen Liste nach Bedarf.

   ![Hinzufügen von Datenbanken][5]

Bei Auswahl einer Datenbank für einen Pool müssen die folgenden Bedingungen erfüllt sein:

- Der Pool muss über ausreichend Speicherplatz für die Datenbank verfügen (darf nicht bereits die maximale Anzahl von Datenbanken enthalten). Genauer gesagt muss der Pool über genügend verfügbare DTUs zur Abdeckung der zugesagten DTUs pro Datenbank verfügen. (Wenn beispielsweise 400 DTUs für die Gruppe und für jede Datenbank 10 DTUs garantiert sind, sind für den Pool maximal 40 Datenbanken erlaubt (400 DTUs/10 DTUs pro DB garantiert = max. 40 Datenbanken.)
- Die aktuellen Features, die von der Datenbank verwendet werden, müssen im Pool verfügbar sein. 


### Konfigurieren der Leistung

Sie konfigurieren die Leistung des Pools, indem Sie die Leistungsparameter für den Pool und die elastischen Datenbanken im Pool einrichten. Denken Sie daran, dass die **elastischen Datenbankeinstellungen** für alle Datenbanken im Pool gelten.

   ![Konfigurieren eines elastischen Pools][3]

Es gibt drei Parameter, die Sie festlegen können und die die Leistung des Pools definieren: die DTU-Garantie für den Pool sowie die DTU MIN- und DTU MAX-Werte für die elastischen Datenbanken im Pool. In der folgenden Tabelle werden die drei Parameter beschrieben und einige Anleitungen zu deren Einrichtung bereitgestellt.

| Leistungsparameter | Beschreibung |
| :--- | :--- |
| **POOL DTU/GB** – DTU-Garantie für den Pool | Die DTU-Garantie für den Pool ist die zugesagte Anzahl verfügbarer DTUs, die für alle Datenbanken im Pool freigegeben sind. <br> Derzeit können Sie 100, 200, 400, 800 oder 1.200 festlegen. <br> Die spezifische Größe der DTU-Garantie für eine Gruppe sollte unter Berücksichtigung des DTU-Nutzungsverlaufs der Gruppe bereitgestellt werden. Wahlweise kann diese Größe durch die gewünschte DTU-Garantie pro Datenbank und Nutzung gleichzeitig aktiver Datenbanken festgelegt werden. Die DTU-Garantie für den Pool korreliert auch mit dem verfügbaren Speicher für den Pool. Für jede DTU, die Sie dem Pool zuordnen, erhalten Sie 1 GB Datenbankspeicher (1 DTU = 1 GB Speicher). <br> **Wie sollte die DTU-Garantie des Pools festgelegt werden?** <br>Sie sollten die DTU-Garantie des Pools mindestens auf das Ergebnis der Formel ([Anzahl Datenbanken] x [durchschnittliche DTU-Auslastung pro Datenbank]) festlegen. |
| **DTU MIN**: DTU-Garantie für jede Datenbank | Die DTU-Garantie pro Datenbank ist die minimale Anzahl von DTUs, die einer einzelnen Datenbank im Pool garantiert wird. Derzeit können Sie diese Garantie auf 0, 10, 20 oder 50 DTUs festlegen, oder Sie können auch keine Garantie auf Datenbanken in der Gruppe (DTU MIN = 0) bereitstellen. <br> **Auf welchen Wert sollte die DTU-Garantie pro Datenbank festgelegt werden?** <br> Sie sollten die DTU-Garantie pro Datenbank (DTU MIN) mindestens auf die ([durchschnittliche DTU-Auslastung pro Datenbank]) festlegen. Die DTU-Garantie pro Datenbank ist eine globale Einstellung, die die DTU-Garantie für alle Datenbanken im Pool festlegt. |
| **DTU MAX**: DTU-Höchstanzahl pro Datenbank | DTU MAX pro Datenbank ist die maximale Anzahl von DTUs, die von einer einzelnen Datenbank im Pool verwendet werden können. Legen Sie die DTU-Grenze pro Datenbank hoch genug fest, um maximale Aktivitätsspitzen, die für Ihre Datenbanken auftreten können, verarbeiten zu können. Sie können diese Obergrenze bis auf den Systembegrenzungswert festlegen, der vom Tarif des Pools (100 DTUs beim Standard-Tarif) abhängig ist. Die spezifische Größe dieser Obergrenze sollte an maximalen Auslastungsmustern der Datenbanken innerhalb der Gruppe ausgerichtet sein. Sie sollten ein gewisses Maß an Mehrlast für die Gruppe einplanen, da für den Pool i. Allg. von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der jedoch nicht alle Datenbanken gleichzeitig stark ausgelastet sind.<br> **Auf welchen Wert sollte die DTU-Obergrenze pro Datenbank festgelegt werden?** <br> Legen Sie DTU MAX oder die DTU-Obergrenze pro Datenbank auf die ([Spitzenauslastung der Datenbank]) fest. Angenommen, die Spitzenauslastung pro Datenbank beträgt 50 DTUs und nur 20 % der 100 Datenbanken in der Gruppe springen gleichzeitig auf die Spitzenauslastung. Wenn die DTU-Obergrenze pro Datenbank auf 50 DTUs festgelegt ist, ist es sinnvoll, die Gruppe 5mal größer zu dimensionieren und die DTU-Garantie für die Gruppe auf 1.000 DTUs festzulegen. Es ist außerdem erwähnenswert, dass die DTU-Obergrenze keine Ressourcengewährleitung für eine Datenbank darstellt, sondern ein DTU-Grenzwert ist, der ggf. erreicht werden kann. |


## Hinzufügen von Datenbanken zu einem elastischen Pool

Nachdem der Pool erstellt wurde, können Sie Datenbanken zum Pool hinzufügen oder aus diesem entfernen.


## Überwachen und Verwalten elastischer Pools

Nach dem Erstellen eines elastischen Pools können Sie den Pool im Portal überwachen und verwalten, indem Sie zur Liste der vorhandenen Pools wechseln und den gewünschten Pool auswählen.

Nach dem Erstellen eines Pools können Sie folgende Aktionen ausführen:

- Wählen Sie **Pool konfigurieren**, um die Einstellungen für Pool-DTU und DTU pro Datenbank zu ändern.
- Wählen Sie **Auftrag erstellen**, und verwalten Sie die Datenbanken im Pool durch elastische Aufträge. Elastische Aufträge erleichtern die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool. Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).
- Wählen Sie **Aufträge verwalten**, um vorhandene elastische Aufträge zu verwalten.



![Überwachen eines elastischen Pools][8]




![Überwachen eines elastischen Pools][4]

Wenn Sie einen vorhandenen Pool auswählen, können Sie die Ressourcenverwendung des Pools sehen. Klicken Sie auf das Diagramm **Ressourcenverwendung**, um das Blatt **Metrik** zu öffnen, auf dem Sie die Diagrammanzeige anpassen und Warnungen einrichten können.


![Ressourcenverwendung][6]

Klicken Sie auf **Diagramm bearbeiten**, um Parameter hinzuzufügen, mit denen Sie Telemetriedaten für den Pool problemlos anzeigen können.


![Bearbeiten eines Diagramms][7]







## Nächste Schritte
Nach dem Erstellen eines elastischen Pools können Sie die Datenbanken im Pool mit elastischen Aufträgen verwalten. Elastische Aufträge erleichtern die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool.

Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).



## Verwandte Themen

- [Elastische SQL-Datenbankpools](sql-database-elastic-pool.md)
- [Erstellen eines elastischen SQL-Datenbankpools mit PowerShell](sql-database-elastic-pool-powershell.md)
- [Referenz für elastische Datenbanken](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-portal/new-elastic-pool.png
[2]: ./media/sql-database-elastic-pool-portal/configure-elastic-pool.png
[3]: ./media/sql-database-elastic-pool-portal/configure-performance.png
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[5]: ./media/sql-database-elastic-pool-portal/add-databases.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[8]: ./media/sql-database-elastic-pool-portal/configure-pool.png

<!---HONumber=July15_HO3-->