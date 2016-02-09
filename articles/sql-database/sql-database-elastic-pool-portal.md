<properties
	pageTitle="Hinzufügen skalierbarer Pools elastischer Datenbanken | Microsoft Azure"
	description="Informationen zum Hinzufügen eines skalierbaren Pools elastischer Datenbanken zu Ihrer SQL-Datenbankkonfiguration für eine einfachere Verwaltung und Ressourcenfreigabe zwischen zahlreichen Datenbanken."
	keywords="Skalierbare Datenbank, Datenbankkonfiguration"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Erstellen eines skalierbaren Pools für elastische SQL-Datenbanken im Azure-Portal

> [AZURE.SELECTOR]
- [Azure portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)

Dieser Artikel beschreibt, wie Sie im Azure-Portal einen skalierbaren [Pool für elastische Datenbanken](sql-database-elastic-pool.md) erstellen. Eine SQL-Datenbankkonfiguration mit Pools für elastische Datenbanken vereinfacht die Verwaltung und Ressourcenfreigabe zwischen mehreren Datenbanken.

> [AZURE.NOTE] Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar. Wenn Sie über einen SQL-Datenbank V11-Server verfügen, können Sie in einem Schritt [mithilfe von PowerShell auf V12 aktualisieren und einen Pool erstellen](sql-database-upgrade-server-powershell.md).


Bevor Sie beginnen, benötigen Sie eine Datenbank auf einem SQL-Datenbank V12-Server. Wenn Sie keine besitzen, finden Sie Informationen zum Erstellen einer Datenbank in weniger als fünf Minuten unter [Erstellen Sie Ihre erste Azure SQL-Datenbank](sql-database-get-started.md). Oder wenn Sie bereits über einen SQL-Datenbank V11-Server verfügen, können Sie [im Portal auf V12 aktualisieren](sql-database-v12-upgrade.md), dann zurückkommen, und diese Anleitung zum Erstellen eines Pools befolgen.


## Schritt 1: Erstellen eines neuen Pools

Erstellen Sie einen elastischen Datenbankpool, indem Sie einen neuen Pool zu einem Server hinzufügen. Sie können einem Server mehrere Pools hinzufügen, aber es ist nicht möglich, Datenbanken von verschiedenen Servern in demselben Pool zusammenzufassen.


1. Klicken Sie im [Azure-Portal](http://portal.azure.com/) auf **SQL-Server**.
2. Wählen Sie den Server mit den Datenbanken, die Sie dem Pool hinzufügen möchten.
3. Klicken Sie auf **Neuer Pool**. (Wenn eine Meldung mit dem Hinweis angezeigt wird, dass ein empfohlener Pool vorhanden ist, können Sie wie folgt vorgehen: Klicken Sie darauf, um auf einfache Weise einen Pool zu überprüfen und zu erstellen, der für die Datenbanken Ihres Servers optimiert ist.) Unten sind weitere Informationen zu Empfehlungen angegeben.


    ![Hinzufügen eines Pools zu einem Server](./media/sql-database-elastic-pool-portal/new-pool.png)


4. Auf dem Blatt **Pool für elastische Datenbanken** können Sie den Standardnamen übernehmen oder einen Namen für den neuen Pool eingeben.

    ![Konfigurieren eines elastischen Pools](./media/sql-database-elastic-pool-portal/configure-elastic-pool.png)


### Empfohlene Pools für elastische Datenbanken

Navigieren Sie zu einem SQL-Datenbankserver. Unter Umständen wird dann eine Meldung angezeigt, dass empfohlene Pools für elastische Datenbanken für den Server vorhanden sind (nur V12).

### Warum erhalte ich eine Empfehlung?

Der SQL-Datenbankdienst bewertet den Nutzungsverlauf und empfiehlt einen oder mehrere elastische Datenbankpools, wenn dies kostengünstiger als die Verwendung einzelner Datenbanken ist.

![Empfohlener Pool](./media/sql-database-elastic-pool-portal/recommended-pool.png)

Jede Empfehlung ist mit einer eindeutigen Teilmenge der Datenbanken des Servers konfiguriert, die optimal in den Pool passen.

Jede Poolempfehlung enthält Folgendes:

- Tarif für den Pool (Basic, Standard oder Premium).
- Angemessenes Niveau der Pool-eDTUs.
- Die Minimal-/Maximalwerte der elastischen Datenbank-eDTU-Einstellungen.
- Eine Liste der empfohlenen Datenbanken.

Der Dienst berücksichtigt bei der Empfehlung von elastischen Datenbankpools die Telemetrie der letzten 30 Tage. Damit eine Datenbank als Kandidat für einen elastischen Datenbankpool berücksichtigt wird, muss sie seit mindestens 7 Tagen existieren. Datenbanken, die sich bereits in einem elastischen Datenbankpool befinden, werden nicht als Empfehlungskandidaten für elastische Datenbankpools angesehen.

Der Dienst bewertet den Ressourcenbedarf und die Kosteneffizienz beim Verschieben der Einzeldatenbanken auf jeder Dienstebene in elastische Datenbankpools auf der gleichen Ebene. Beispielsweise werden alle Datenbanken der Standard-Edition auf einem Server entsprechend ihrer Eignung für einen elastischen Pool der Standard-Edition bewertet. Dies bedeutet, dass der Dienst keine tarifübergreifenden Empfehlungen trifft, also etwa eine Standard-Datenbank nicht in einen Premium-Pool verschiebt.


### Erstellen eines empfohlenen Pools

1. Klicken Sie auf die Meldung, um eine Liste mit den empfohlenen Pools anzuzeigen.
1. Klicken Sie auf einen Pool, um die Details der Empfehlung anzuzeigen.
2. Überprüfen Sie alle Einstellungen der Empfehlung. Übernehmen Sie diese unverändert, oder bearbeiten Sie die Empfehlung gemäß Ihren geschäftlichen Anforderungen.


## Schritt 2: Auswählen eines Tarifs

Der Tarif für den Pool legt die Features für die elastischen Datenbanken im Pool, die maximale Anzahl von eDTUs (eDTU MAX) und den für jede Datenbank verfügbaren Speicher (GB) fest. Weitere Informationen finden Sie unter [Tarife](sql-database-service-tiers.md#Service-tiers-for-elastic-database-pools).

>[AZURE.NOTE] Aktuell können Sie in der Vorschau den Tarif eines elastischen Datenbankpools nach dessen Erstellung nicht mehr ändern. Um den Tarif für einen vorhandenen elastischen Pool zu ändern, erstellen Sie einen neuen elastischen Pool im gewünschten Tarif, und migrieren Sie dann die elastischen Datenbanken zu diesem neuen Pool.

   ![Auswählen eines Tarifs](./media/sql-database-elastic-pool-portal/pricing-tier.png)

### Konfigurieren des Pools

Klicken Sie nach dem Festlegen des Tarifs auf „Pool konfigurieren“. Hier fügen Sie Datenbanken hinzu, legen Pool-eDTUs und den Speicher (GB für Pool) fest und geben die eDTU-Minimalwerte und -Maximalwerte für die elastischen Datenbanken im Pool an.

## Schritt 3: Hinzufügen von Datenbanken zum Pool

Sie können Datenbanken jederzeit einem Pool hinzufügen oder daraus entfernen.

1. Klicken Sie während der Erstellung des Pools auf dem Blatt **Pool konfigurieren** auf **Datenbanken hinzufügen**.
2. Wählen Sie die Datenbanken aus, die Sie dem Pool hinzufügen möchten:

    ![Hinzufügen von Datenbanken](./media/sql-database-elastic-pool-portal/add-databases.png)

    Bei Auswahl einer Datenbank für einen Pool müssen die folgenden Bedingungen erfüllt sein:

    - Der Pool muss über ausreichend Speicherplatz für die Datenbank verfügen (darf nicht bereits die maximale Anzahl von Datenbanken enthalten). Genauer gesagt muss der Pool über genügend verfügbare eDTUs zur Abdeckung der zugesagten eDTUs pro Datenbank verfügen. (Wenn beispielsweise 400 eDTUs für die Gruppe und für jede Datenbank 10 eDTUs garantiert sind, sind für den Pool maximal 40 Datenbanken erlaubt (400 eDTUs/10 eDTUs pro DB garantiert = max. 40 Datenbanken.)
    - Die aktuellen Features, die von der Datenbank verwendet werden, müssen im Pool verfügbar sein.

### Dynamische Empfehlungen

Nach dem Hinzufügen von Datenbanken zum Pool werden Empfehlungen basierend auf dem Nutzungsverlauf der von Ihnen ausgewählten Datenbanken dynamisch generiert. Diese Empfehlungen werden im Diagramm zur eDTU- und GB-Nutzung sowie im Empfehlungsbanner oben auf dem Blatt **Pool konfigurieren** angezeigt. Diese Empfehlungen sollen Ihnen als Hilfe beim Erstellen eines Pools dienen, der für Ihre speziellen Datenbanken optimiert ist.


 ![Dynamische Empfehlungen](./media/sql-database-elastic-pool-portal/dynamic-recommendation.png)


## Schritt 4: Festlegen von Leistungsmerkmalen des Pools

Sie konfigurieren die Leistung des Pools, indem Sie die Leistungsparameter für den Pool und die elastischen Datenbanken im Pool einrichten. Denken Sie daran, dass die **elastischen Datenbankeinstellungen** für alle Datenbanken im Pool gelten.

 ![Konfigurieren eines elastischen Pools](./media/sql-database-elastic-pool-portal/configure-performance.png)

Es gibt drei Parameter, die Sie festlegen können und die die Leistung des Pools definieren: die eDTU-Garantie für den Pool sowie die eDTU MIN- und eDTU MAX-Werte für die elastischen Datenbanken im Pool. In der folgenden Tabelle werden die drei Parameter beschrieben und einige Anleitungen zu deren Einrichtung bereitgestellt. Informationen zu den im Einzelnen verfügbaren Werteinstellungen finden Sie unter [Referenz für elastische Datenbankpools](sql-database-elastic-pool-reference.md).

| Leistungsparameter | Beschreibung |
| :--- | :--- |
| **POOL eDTU** – eDTU-Garantie für den Pool | Die eDTU-Garantie für den Pool ist die zugesagte Anzahl verfügbarer eDTUs, die für alle Datenbanken im Pool freigegeben sind. <br> Die spezifische Größe der eDTU-Garantie für eine Gruppe sollte unter Berücksichtigung des eDTU-Nutzungsverlaufs der Gruppe bereitgestellt werden. Wahlweise kann diese Größe durch die gewünschte eDTU-Garantie pro Datenbank und Nutzung gleichzeitig aktiver Datenbanken festgelegt werden. Die eDTU-Garantie für den Pool korreliert auch mit dem verfügbaren Speicher für den Pool. Für jede eDTU, die Sie dem Pool zuordnen, erhalten Sie eine feste Menge Datenbankspeicher. <br> **Wie sollte die eDTU-Garantie des Pools festgelegt werden?** <br>Sie sollten die eDTU-Garantie des Pools mindestens auf das Ergebnis der Formel ([Anzahl von Datenbanken] x [durchschnittliche DTU-Auslastung pro Datenbank]) festlegen. |
| **eDTU MIN**: eDTU-Garantie für jede Datenbank | Die eDTU-Garantie pro Datenbank ist die minimale Anzahl von eDTUs, die einer einzelnen Datenbank im Pool garantiert wird. <br> **Auf welchen Wert sollte die eDTU-Garantie pro Datenbank festgelegt werden?** <br> Normalerweise wird die eDTU-Garantie pro Datenbank (eDTU MIN) auf einen Wert zwischen 0 und ([durchschnittliche Nutzung pro Datenbank]) festgelegt. Die eDTU-Garantie pro Datenbank ist eine globale Einstellung, die die eDTU-Garantie für alle Datenbanken im Pool festlegt. |
| **eDTU MAX**: eDTU-Obergrenze pro Datenbank | eDTU MAX pro Datenbank ist die maximale Anzahl von eDTUs, die von einer einzelnen Datenbank im Pool verwendet werden können. Legen Sie die eDTU-Obergrenze pro Datenbank hoch genug fest, um maximale Aktivitätsspitzen, die für Ihre Datenbanken auftreten können, verarbeiten zu können. Sie können diese Obergrenze bis auf den Systembegrenzungswert festlegen, der vom Tarif des Pools (1000 eDTUs beim Premium-Tarif) abhängig ist. Die spezifische Größe dieser Obergrenze sollte an maximalen Auslastungsmustern der Datenbanken innerhalb der Gruppe ausgerichtet sein. Sie sollten ein gewisses Maß an Mehrlast für die Gruppe einplanen, da für den Pool i. Allg. von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der jedoch nicht alle Datenbanken gleichzeitig stark ausgelastet sind.<br> **Auf welchen Wert sollte die eDTU-Obergrenze pro Datenbank festgelegt werden?** <br> Legen Sie eDTU MAX oder die eDTU-Obergrenze pro Datenbank auf die ([Spitzenauslastung der Datenbank]) fest. Angenommen, die Spitzenauslastung pro Datenbank beträgt 50 DTUs und nur 20 % der 100 Datenbanken in der Gruppe springen gleichzeitig auf die Spitzenauslastung. Wenn die eDTU-Obergrenze pro Datenbank auf 50 eDTUs festgelegt ist, ist es sinnvoll, den Pool 5mal größer zu dimensionieren und die eDTU-Garantie für die Gruppe (POOL-eDTU) auf 1.000 eDTUs festzulegen. Es ist außerdem erwähnenswert, dass die eDTU-Obergrenze keine Ressourcengewährleitung für eine Datenbank darstellt, sondern ein eDTU-Grenzwert ist, der ggf. erreicht werden kann. |


## Hinzufügen von Datenbanken zu einem Pool und Entfernen von Datenbanken aus einem Pool

Nachdem der Pool erstellt wurde, können Sie vorhandene Datenbanken zum Pool hinzufügen oder daraus entfernen, indem Sie Datenbanken auf der Seite **Elastische Datenbanken** hinzufügen oder entfernen (wechseln Sie in den Pool, und klicken Sie unter **Zusammenfassung** auf **Elastische Datenbanken**).

- Klicken Sie auf **Datenbanken hinzufügen**, um die Liste mit den Datenbanken zu öffnen, die Sie dem Pool hinzufügen können.

    Oder

- Wählen Sie die Datenbanken aus, die Sie aus dem Pool entfernen möchten, und klicken Sie auf **Datenbanken entfernen**.

![Empfohlener Pool](./media/sql-database-elastic-pool-portal/add-remove-databases.png)

## Erstellen einer neuen Datenbank in einem Pool

Erstellen Sie eine neue Datenbank in einem Pool, indem Sie zum gewünschten Pool navigieren und auf **Datenbank erstellen** klicken.

Die SQL-Datenbank ist bereits für den richtigen Server und Pool konfiguriert. Geben Sie einen Namen ein, und wählen Sie die Datenbankoptionen aus. Klicken Sie dann auf **OK**, um die neue Datenbank zu erstellen:


   ![Erstellen einer elastischen Datenbank](./media/sql-database-elastic-pool-portal/create-database.png)



## Überwachen und Verwalten elastischer Datenbankpools

Nach dem Erstellen eines elastischen Datenbankpools können Sie den Pool im Portal überwachen und verwalten, indem Sie zur Liste der vorhandenen Pools wechseln und den gewünschten Pool auswählen.

Nach dem Erstellen eines Pools können Sie folgende Aktionen ausführen:

- Wählen Sie **Pool konfigurieren**, um die Einstellungen für Pool-eDTU und eDTU pro Datenbank zu ändern.
- Wählen Sie **Auftrag erstellen**, und verwalten Sie die Datenbanken im Pool durch elastische Aufträge. Elastische Aufträge ermöglichen die Ausführung von Transact-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool. Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).
- Wählen Sie **Aufträge verwalten**, um vorhandene elastische Aufträge zu verwalten.
- Klicken Sie auf **Datenbank erstellen**, um im Pool eine neue Datenbank zu erstellen.
- Klicken Sie auf den Link zu den Einstellungen des Pools, um die Pool-eDTUs und die GB-Vorgabe pro Pool anzupassen.
- Klicken Sie auf den Link „Datenbanken“, um dem Pool Datenbanken hinzuzufügen oder diese daraus zu entfernen.
- Klicken Sie auf den Link „Datenbankeinstellungen“, um die eDTU-Minimalwerte und -Maximalwerte für die elastischen Datenbanken im Pool anzupassen. 

![Überwachen eines elastischen Pools](./media/sql-database-elastic-pool-portal/pool-tasks.png)


Wenn Sie einen vorhandenen Pool auswählen, können Sie die Ressourcenverwendung des Pools sehen. Klicken Sie auf das Diagramm **Ressourcenverwendung**, um das Blatt **Metrik** zu öffnen, auf dem Sie die Diagrammanzeige anpassen und Warnungen einrichten können.

![Überwachen eines elastischen Pools][4] ![Ressourcenverwendung][6]

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
[4]: ./media/sql-database-elastic-pool-portal/monitor-elastic-pool.png
[6]: ./media/sql-database-elastic-pool-portal/metric.png
[7]: ./media/sql-database-elastic-pool-portal/edit-chart.png
[10]: ./media/sql-database-elastic-pool-portal/star.png

<!---HONumber=AcomDC_0204_2016-->