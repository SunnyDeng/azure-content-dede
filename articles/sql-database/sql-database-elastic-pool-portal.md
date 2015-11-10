<properties
	pageTitle="Erstellen und Verwalten eines elastischen SQL-Datenbankpools | Microsoft Azure"
	description="Erstellen Sie einen einzelnen Pool von Ressourcen, den Sie in einer Gruppe von Azure SQL-Datenbanken übergreifend nutzen können."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/29/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Erstellen eines elastischen Datenbankpools

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

In diesem Artikel wird das Erstellen eines elastischen Datenbankpools veranschaulicht, der den Prozess des Erstellens, Wartens und Verwaltens von Leistung und Kosten mehrerer Datenbanken vereinfacht. Bevor Sie beginnen, benötigen Sie mindestens eine Datenbank auf einem SQL-Datenbankserver V12. Wenn Sie keine besitzen, finden Sie Informationen zum Erstellen einer Datenbank in weniger als fünf Minuten unter [Erstellen Sie Ihre erste Azure SQL-Datenbank](sql-database-get-started.md).


> [AZURE.NOTE]Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar.


## Schritt 1: Hinzufügen eines Pools zu einem Server

Erstellen Sie einen elastischen Datenbankpool, indem Sie einen neuen Pool zu einem Server hinzufügen. Sie können mehrere Pools zu einem Server hinzufügen, aber jedem Pool kann jeweils nur ein (1) Server zugeordnet werden. Darüber hinaus können alle oder einige der Datenbanken auf einem Server zu einem Pool hinzugefügt werden.


Klicken Sie im [Azure-Vorschauportal](https://ms.portal.azure.com/) auf **SQL-Server**, klicken Sie auf Server, die die Datenbanken hosten, die Sie zum Pool hinzufügen möchten, und klicken Sie dann auf **Pool hinzufügen**.

![Hinzufügen eines Pools zu einem Server](./media/sql-database-elastic-pool-portal/elastic-pool-add-pool.png)

Oder

Wird eine Meldung angezeigt, der zu entnehmen ist, dass es einen empfohlenen Pool für einen Server gibt, klicken Sie darauf, um auf einfache Weise einen Pool zu überprüfen und zu erstellen, der für die Datenbanken Ihres Servers optimiert ist. Ausführliche Informationen finden Sie unter [Empfohlene Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md#recommended-elastic-database-pools).
   
  
![Erstellen eines elastischen Pools][1]


Das Blatt **Pool für elastische Datenbanken** stellt Optionen zum Festlegen des Tarifs, Hinzufügen von Datenbanken und Konfigurieren der Leistungsmerkmale des Pools bereit.

> [AZURE.NOTE]Bei der ersten Auswahl des Befehls **Pool hinzufügen** müssen Sie den Bedingungen der Vorschau zustimmen, indem Sie **VORSCHAUBESTIMMUNGEN** auswählen und das Blatt **Vorschaubestimmungen** ausfüllen. Sie müssen dies nur einmal für jedes Abonnement tun.

   ![Konfigurieren eines elastischen Pools][2]

## Schritt 2: Auswählen eines Tarifs

Der Tarif für den Pool legt die Features für die elastischen Datenbanken im Pool, die maximale Anzahl von eDTUs (eDTU MAX) und den für jede Datenbank verfügbaren Speicher (GB) fest. Weitere Informationen finden Sie unter [Tarife](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE]Aktuell können Sie in der Vorschau den Tarif eines elastischen Datenbankpools nach dessen Erstellung nicht mehr ändern. Um den Tarif für einen vorhandenen elastischen Pool zu ändern, erstellen Sie einen neuen elastischen Pool im gewünschten Tarif, und migrieren Sie dann die elastischen Datenbanken zu diesem neuen Pool.

   ![Preisstufe][9]


### Tarifempfehlungen

Der SQL-Datenbankdienst bewertet den Nutzungsverlauf und empfiehlt einen oder mehrere elastische Datenbankpools, wenn dies kostengünstiger als die Verwendung einzelner Datenbanken ist.

Tarife mit einem Stern (![Stern][10]) stellen Empfehlungen auf der Grundlage Ihrer Datenbankworkloads dar.

Wenn mehrere Tarife empfohlen werden, bedeutet dies, dass mehrere elastische Datenbankpools erstellt werden sollen. Jede Empfehlung ist mit einer eindeutigen Teilmenge der Datenbanken des Servers konfiguriert, die optimal in den Pool passen.

Über den einfachen Tarifvorschlag für einen elastischen Datenbankpool hinaus enthält jede Poolempfehlung die folgenden Informationen:

- Tarif für den Pool (Basic, Standard oder Premium).
- Angemessenes Niveau der Pool-eDTUs.
- Die Minimal-/Maximalwerte der elastischen Datenbank-eDTU-Einstellungen.  
- Eine Liste der empfohlenen Datenbanken.

Der Dienst berücksichtigt bei der Empfehlung von elastischen Datenbankpools die Telemetrie der letzten 30 Tage. Damit eine Datenbank als Kandidat für einen elastischen Datenbankpool berücksichtigt wird, muss sie seit mindestens 7 Tagen existieren. Datenbanken, die sich bereits in einem elastischen Datenbankpool befinden, werden nicht als Empfehlungskandidaten für elastische Datenbankpools angesehen.

Der Dienst bewertet den Ressourcenbedarf und die Kosteneffizienz beim Verschieben der Einzeldatenbanken auf jeder Dienstebene in elastische Datenbankpools auf der gleichen Ebene. Beispielsweise werden alle Datenbanken der Standard-Edition auf einem Server entsprechend ihrer Eignung für einen elastischen Pool der Standard-Edition bewertet. Dies bedeutet, dass der Dienst keine tarifübergreifenden Empfehlungen trifft, also etwa eine Standard-Datenbank nicht in einen Premium-Pool verschiebt.

>[AZURE.NOTE]Web- und Geschäftsdatenbanken werden ausgehend von ihrem Nutzungsverlauf und der Datenbankgröße den neuen Basic-, Standard- und Premium-Tarifen zugeordnet. Die Zuordnung zu den neuen Tarifen stellt eine Empfehlung für Web- und Geschäftsdatenbanken für die passenden Pools dar.


## Schritt 3: Hinzufügen von Datenbanken zum Pool

Sie können zu jedem Zeitpunkt bestimmte Datenbanken auswählen, die in den Pool aufgenommen werden sollen. Wenn Sie einen neuen Pool erstellen, empfiehlt Azure die Datenbanken, die von einem Pool profitieren, und kennzeichnet diese für die Aufnahme. Sie können alle auf dem Server verfügbaren Datenbanken hinzufügen, oder Sie aktivieren oder deaktivieren Datenbanken aus der ursprünglichen Liste nach Bedarf.

   ![Hinzufügen von Datenbanken][5]

Bei Auswahl einer Datenbank für einen Pool müssen die folgenden Bedingungen erfüllt sein:

- Der Pool muss über ausreichend Speicherplatz für die Datenbank verfügen (darf nicht bereits die maximale Anzahl von Datenbanken enthalten). Genauer gesagt muss der Pool über genügend verfügbare eDTUs zur Abdeckung der zugesagten eDTUs pro Datenbank verfügen. (Wenn beispielsweise 400 eDTUs für die Gruppe und für jede Datenbank 10 eDTUs garantiert sind, sind für den Pool maximal 40 Datenbanken erlaubt (400 eDTUs/10 eDTUs pro DB garantiert = max. 40 Datenbanken.)
- Die aktuellen Features, die von der Datenbank verwendet werden, müssen im Pool verfügbar sein.


## Schritt 4: Anpassen der Leistungsmerkmale

Sie konfigurieren die Leistung des Pools, indem Sie die Leistungsparameter für den Pool und die elastischen Datenbanken im Pool einrichten. Denken Sie daran, dass die **elastischen Datenbankeinstellungen** für alle Datenbanken im Pool gelten.

   ![Konfigurieren eines elastischen Pools][3]

Es gibt drei Parameter, die Sie festlegen können und die die Leistung des Pools definieren: die eDTU-Garantie für den Pool sowie die eDTU MIN- und eDTU MAX-Werte für die elastischen Datenbanken im Pool. In der folgenden Tabelle werden die drei Parameter beschrieben und einige Anleitungen zu deren Einrichtung bereitgestellt. Informationen zu den im Einzelnen verfügbaren Werteinstellungen finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).

| Leistungsparameter | Beschreibung |
| :--- | :--- |
| **POOL eDTU** – eDTU-Garantie für den Pool | Die eDTU-Garantie für den Pool ist die zugesagte Anzahl verfügbarer eDTUs, die für alle Datenbanken im Pool freigegeben sind. <br> Die spezifische Größe der eDTU-Garantie für eine Gruppe sollte unter Berücksichtigung des eDTU-Nutzungsverlaufs der Gruppe bereitgestellt werden. Wahlweise kann diese Größe durch die gewünschte eDTU-Garantie pro Datenbank und Nutzung gleichzeitig aktiver Datenbanken festgelegt werden. Die eDTU-Garantie für den Pool korreliert auch mit dem verfügbaren Speicher für den Pool. Für jede eDTU, die Sie dem Pool zuordnen, erhalten Sie eine feste Menge Datenbankspeicher. <br> **Wie sollte die eDTU-Garantie des Pools festgelegt werden?** <br>Sie sollten die eDTU-Garantie des Pools mindestens auf das Ergebnis der Formel ([Anzahl von Datenbanken] x [durchschnittliche DTU-Auslastung pro Datenbank]) festlegen. |
| **eDTU MIN**: eDTU-Garantie für jede Datenbank | Die eDTU-Garantie pro Datenbank ist die minimale Anzahl von eDTUs, die einer einzelnen Datenbank im Pool garantiert wird. Beispielsweise können Sie diese Garantie in Standard-Pools auf 0, 10, 20, 50 oder e100 DTUs festlegen, oder Sie können auch keine Garantie auf Datenbanken in der Gruppe (eDTU MIN = 0) bereitstellen. <br> **Auf welchen Wert sollte die eDTU-Garantie pro Datenbank festgelegt werden?** <br> Normalerweise wird die eDTU-Garantie pro Datenbank (eDTU MIN) auf einen Wert zwischen 0 und ([durchschnittliche Nutzung pro Datenbank]) festgelegt. Die eDTU-Garantie pro Datenbank ist eine globale Einstellung, die die eDTU-Garantie für alle Datenbanken im Pool festlegt. |
| **eDTU MAX**: eDTU-Obergrenze pro Datenbank | eDTU MAX pro Datenbank ist die maximale Anzahl von eDTUs, die von einer einzelnen Datenbank im Pool verwendet werden können. Legen Sie die eDTU-Obergrenze pro Datenbank hoch genug fest, um maximale Aktivitätsspitzen, die für Ihre Datenbanken auftreten können, verarbeiten zu können. Sie können diese Obergrenze bis auf den Systembegrenzungswert festlegen, der vom Tarif des Pools (1000 eDTUs beim Premium-Tarif) abhängig ist. Die spezifische Größe dieser Obergrenze sollte an maximalen Auslastungsmustern der Datenbanken innerhalb der Gruppe ausgerichtet sein. Sie sollten ein gewisses Maß an Mehrlast für die Gruppe einplanen, da für den Pool i. Allg. von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der jedoch nicht alle Datenbanken gleichzeitig stark ausgelastet sind.<br> **Auf welchen Wert sollte die eDTU-Obergrenze pro Datenbank festgelegt werden?** <br> Legen Sie eDTU MAX oder die eDTU-Obergrenze pro Datenbank auf die ([Spitzenauslastung der Datenbank]) fest. Angenommen, die Spitzenauslastung pro Datenbank beträgt 50 DTUs und nur 20 % der 100 Datenbanken in der Gruppe springen gleichzeitig auf die Spitzenauslastung. Wenn die eDTU-Obergrenze pro Datenbank auf 50 eDTUs festgelegt ist, ist es sinnvoll, den Pool 5mal größer zu dimensionieren und die eDTU-Garantie für die Gruppe (POOL-eDTU) auf 1.000 eDTUs festzulegen. Es ist außerdem erwähnenswert, dass die eDTU-Obergrenze keine Ressourcengewährleitung für eine Datenbank darstellt, sondern ein eDTU-Grenzwert ist, der ggf. erreicht werden kann. |

## Empfohlene Pools für elastische Datenbanken

Navigieren Sie zu einem SQL-Datenbank V12-Server. Möglicherweise wird dann eine Meldung angezeigt, dass es empfohlene Pools für elastische Datenbanken für den Server gibt.

Ähnlich wie Tarifempfehlungen für einen Pool für elastische Datenbanken sind empfohlene Pools vorkonfiguriert, wobei folgende Optionen bereits festgelegt sind:

- Tarif für den Pool.
- Angemessenes Niveau der Pool-eDTUs.
- Die Minimal-/Maximalwerte der Datenbank-eDTU-Einstellungen.  
- Eine Liste der empfohlenen Datenbanken.

### Erstellen eines empfohlenen Pools

1. Klicken Sie auf die Meldung, um eine Liste mit den empfohlenen Pools anzuzeigen:
 
     ![Empfohlene Pools][12]
  
1. Klicken Sie auf einen Pool, um die ausführlichen Empfehlungseinstellungen anzuzeigen.
2. Bearbeiten Sie einfach den Poolnamen, und klicken Sie auf **OK**, um den Pool zu erstellen. (Empfohlene Pools können erst nach der Erstellung geändert werden.)

    ![Empfohlener Pool][11]


## Hinzufügen zum und Entfernen von Datenbanken aus dem Pool

Nachdem der Pool erstellt wurde, können Sie Datenbank zum Pool hinzufügen oder daraus entfernen, indem Sie Datenbanken auf der Seite **Datenbanken hinzufügen** aktivieren oder deaktivieren.

Nach dem Erstellen eines Pools können Sie Transact-SQL auch zum Erstellen von neuen elastischen Datenbanken im Pool verwenden und Datenbanken in und aus Pools verschieben. Weitere Informationen finden Sie unter [Referenz für Pools für elastische Datenbanken – Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*


## Überwachen und Verwalten elastischer Datenbankpools

Nach dem Erstellen eines elastischen Datenbankpools können Sie den Pool im Portal überwachen und verwalten, indem Sie zur Liste der vorhandenen Pools wechseln und den gewünschten Pool auswählen.

Nach dem Erstellen eines Pools können Sie folgende Aktionen ausführen:

- Wählen Sie **Pool konfigurieren**, um die Einstellungen für Pool-eDTU und eDTU pro Datenbank zu ändern.
- Wählen Sie **Auftrag erstellen**, und verwalten Sie die Datenbanken im Pool durch elastische Aufträge. Elastische Aufträge ermöglichen die Ausführung von Transact-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool. Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).
- Wählen Sie **Aufträge verwalten**, um vorhandene elastische Aufträge zu verwalten.



![Überwachen eines elastischen Pools][8]




![Überwachen eines elastischen Pools][4]

Wenn Sie einen vorhandenen Pool auswählen, können Sie die Ressourcenverwendung des Pools sehen. Klicken Sie auf das Diagramm **Ressourcenverwendung**, um das Blatt **Metrik** zu öffnen, auf dem Sie die Diagrammanzeige anpassen und Warnungen einrichten können.


![Ressourcenverwendung][6]

Klicken Sie auf **Diagramm bearbeiten**, um Parameter hinzuzufügen, mit denen Sie Telemetriedaten für den Pool problemlos anzeigen können.


![Bearbeiten eines Diagramms][7]




## Nächste Schritte
Nach dem Erstellen eines Pools für elastische Datenbanken können Sie die Datenbanken im Pool mit elastischen Aufträgen verwalten. Elastische Aufträge erleichtern die Ausführung von Transact-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool. Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).



## Zusätzliche Ressourcen

- [Elastische SQL-Datenbankpools](sql-database-elastic-pool.md)
- [Erstellen eines elastischen SQL-Datenbankpools mit PowerShell](sql-database-elastic-pool-powershell.md)
- [Erstellen und Verwalten von SQL-Datenbanken mit C#](sql-database-client-library.md)
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
[9]: ./media/sql-database-elastic-pool-portal/pricing-tier.png
[10]: ./media/sql-database-elastic-pool-portal/star.png
[11]: ./media/sql-database-elastic-pool-portal/recommended-pool.png
[12]: ./media/sql-database-elastic-pool-portal/pools-message.png

<!---HONumber=Nov15_HO2-->