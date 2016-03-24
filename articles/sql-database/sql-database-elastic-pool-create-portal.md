<properties
	pageTitle="Hinzufügen skalierbarer Pools elastischer Datenbanken | Microsoft Azure"
	description="Enthält Informationen zum Hinzufügen eines skalierbaren Pools für elastische Datenbanken zu Ihrer SQL-Datenbankkonfiguration, um eine einfachere Verwaltung und Ressourcenfreigabe zwischen zahlreichen Datenbanken zu ermöglichen."
	keywords="Skalierbare Datenbank, Datenbankkonfiguration"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Erstellen eines skalierbaren Pools für elastische SQL-Datenbanken im Azure-Portal

> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-elastic-pool-create-csharp.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)

In diesem Artikel wird beschrieben, wie Sie im [Azure-Portal](https://portal.azure.com/) einen skalierbaren [Pool für elastische Datenbanken](sql-database-elastic-pool.md) erstellen. Es gibt zwei Möglichkeiten, wie Sie einen Pool erstellen können. Sie können einen Pool von Grund auf neu erstellen, wenn Sie genau wissen, wie der Pool aussehen soll. Sie können aber auch mit einer Empfehlung des Diensts beginnen, der über ein integriertes Intelligence-Modul verfügt. Ein Pool-Setup wird empfohlen, wenn es für Sie anhand der Telemetriedaten zur bisherigen Datenbanknutzung kostengünstiger ist.

Sie können einem Server mehrere Pools hinzufügen, aber es ist nicht möglich, Datenbanken von verschiedenen Servern in demselben Pool zusammenzufassen. Zum Erstellen eines Pools benötigen Sie mindestens eine Datenbank auf einem V12-Server. Falls Sie keinen Server dieser Art besitzen, helfen Ihnen die Informationen unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md) weiter. Sie können einen Pool mit nur einer Datenbank erstellen, aber Pools sind erst mit mehreren Datenbanken kosteneffizient. Weitere Informationen finden Sie unter [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Pools sind nur mit SQL-Datenbank-V12-Servern verfügbar. Wenn Sie über eine frühere Serverversion verfügen, können Sie in einem Schritt [per PowerShell auf V12 aktualisieren und einen Pool erstellen](sql-database-upgrade-server-powershell.md).

##Erstellen eines neuen Pools
1. Klicken Sie im [Azure-Portal](http://portal.azure.com/) auf **SQL-Server** und dann auf den Server mit den Datenbanken, die Sie einem Pool hinzufügen möchten.
2. Klicken Sie auf **Neuer Pool**.

    ![Hinzufügen eines Pools zu einem Server](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **ODER**

    Klicken Sie auf die Meldung, um die Pools anzuzeigen, die basierend auf den Telemetriedaten zur bisherigen Datenbanknutzung empfohlen werden. Klicken Sie anschließend auf die Ebene, um weitere Details anzuzeigen und den Pool anzupassen. Informationen zur Bereitstellung der Empfehlung finden Sie weiter unten in diesem Thema unter [Grundlagen von Poolempfehlungen](#understand-pool-recommendations).

    ![Empfohlener Pool](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    Das Blatt **Pool für elastische Datenbanken**, in dem Sie den Pool einrichten, wird angezeigt. Wenn Sie im vorherigen Schritt auf **Neuer Pool** geklickt haben, werden vom Portal unter **Tarif** ein **Standard-Pool**, ein eindeutiger **Name** für den Pool und eine Standardkonfiguration für den Pool ausgewählt. Wenn Sie einen empfohlenen Pool ausgewählt haben, sind die empfohlene Ebene und die Konfiguration des Pools bereits ausgewählt, aber Sie können diese Angaben immer noch ändern.

    ![Konfigurieren eines elastischen Pools](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Um den Tarif für den Pool zu ändern, klicken Sie auf die Option **Tarif**, auf den gewünschten Tarif und dann auf **Auswählen**.

    > [AZURE.IMPORTANT] Nachdem Sie den Tarif ausgewählt und Ihre Änderungen übernommen haben, indem Sie im letzten Schritt auf **OK** geklickt haben, können Sie den Tarif des Pools nicht mehr ändern.

4. Klicken Sie auf **Pool konfigurieren**, um Datenbanken hinzuzufügen und Ressourceneinstellungen für den Pool auszuwählen.
5. Klicken Sie zum Hinzufügen von Datenbanken auf **Datenbank hinzufügen**, klicken Sie auf die Datenbanken, die Sie hinzufügen möchten, und klicken Sie dann auf die Schaltfläche **Auswählen**.

    ![Hinzufügen von Datenbanken](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Wenn die von Ihnen verwendeten Datenbanken über ausreichend Telemetriedaten zur bisherigen Nutzung verfügen, werden der Graph **Geschätzte eDTU- und GB-Nutzung** und das Balkendiagramm **Tatsächliche eDTU-Nutzung** aktualisiert, um für Sie als Hilfe beim Treffen von Konfigurationsentscheidungen zu dienen. Außerdem erhalten Sie vom Dienst unter Umständen eine Empfehlungsnachricht zur richtigen Dimensionierung des Pools. Weitere Informationen finden Sie unter [Grundlagen von Poolempfehlungen](#understand-pool-recommendations).

    ![Dynamische Empfehlungen](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. Verwenden Sie die Steuerelemente auf der Seite **Pool konfigurieren**, um die Einstellungen zu untersuchen und den Pool gemäß den folgenden Richtlinien zu konfigurieren:

    ![Konfigurieren eines elastischen Pools](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | Leistungseinstellung | Beschreibung |
    | :--- | :--- |
    | **POOL-eDTU** und **Pool GB** (pro Pooleinstellung)| Maximale eDTUs, die für alle Datenbanken im Pool verfügbar sind und von diesen gemeinsam verwendet werden. Wie viele eDTUs in einem Pool maximal verfügbar sind, richtet sich nach dem Tarif (Dienstebene). **Pool-eDTU** korreliert mit dem Speicher, der für den Pool verfügbar ist. Für jede eDTU, die Sie dem Pool zuordnen, erhalten Sie eine feste Menge an Datenbankspeicher (und umgekehrt). |
    | **eDTU minimal** (pro Datenbankeinstellung)| Die minimale Anzahl von eDTUs aus dem Pool, die für eine Einzeldatenbank jederzeit garantiert ist. **eDTU minimal** ist normalerweise auf einen Wert zwischen 0 und der durchschnittlichen bisherigen eDTU-Nutzung pro Datenbank festgelegt. Dies ist eine globale Einstellung, die für alle Datenbanken im Pool gilt. |
    | **eDTU maximal** (pro Datenbankeinstellung) | Die maximale Anzahl von eDTUs, die von einer Einzeldatenbank im Pool verwendet werden können. Sie können diese Obergrenze bis auf den Wert von **POOL-eDTU** festlegen. Legen Sie **eDTU maximal** pro Datenbank auf einen Wert fest, der hoch genug ist, sodass maximale Bursts oder Spitzen bis zur Spitzenauslastung der Datenbank verarbeitet werden können. Sie sollten ein gewisses Maß an Mehrlast für die Gruppe einplanen, da für den Pool im Allgemeinen von Nutzungsmustern starker und schwacher Auslastung ausgegangen wird, bei der aber nicht alle Datenbanken gleichzeitig stark ausgelastet sind. **Beispiel:** Angenommen, die Spitzenauslastung pro Datenbank beträgt 50 DTUs, und nur 20 % der 100 Datenbanken in der Gruppe springen gleichzeitig auf die Spitzenauslastung. Wenn die eDTU-Obergrenze pro Datenbank auf 50 eDTUs festgelegt ist, ist es sinnvoll, die fünffache Mehrlast für den Pool einzuplanen und den Wert für **POOL-eDTU** auf 1.000 festzulegen. **eDTU maximal** ist keine Ressourcengewährleistung für eine Datenbank, sondern ein eDTU-Grenzwert, der ggf. erreicht werden kann. |

    Weitere Details zu den Grenzwerten für die einzelnen Dienstebenen finden Sie unter [Referenz für Pools für elastische Datenbanken](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases), und eine ausführliche Anleitung zur richtigen Dimensionierung eines Pools finden Sie unter [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md).

7. Klicken Sie auf **Auswählen**, wenn Sie fertig sind, und klicken Sie dann auf **OK**, um den Pool zu erstellen.

##Grundlagen von Poolempfehlungen
Der SQL-Datenbankdienst bewertet den Nutzungsverlauf und empfiehlt einen oder mehrere Pools, wenn dies kostengünstiger als die Verwendung von Einzeldatenbanken ist. Jede Empfehlung ist mit einer eindeutigen Teilmenge der Datenbanken des Servers konfiguriert, die optimal zum Pool passen. Die Poolempfehlung umfasst Folgendes:

- Tarif für den Pool (Basic, Standard oder Premium)
- Passende **POOL-eDTUs** (auch als maximale eDTUs pro Pool bezeichnet)
- **eDTU maximal** und **eDTU minimal** pro Datenbank
- Liste der empfohlenen Datenbanken für den Pool

Der Dienst berücksichtigt bei der Empfehlung von Pools die Telemetrie der letzten 30 Tage. Damit eine Datenbank als Kandidat für einen elastischen Datenbankpool berücksichtigt wird, muss sie seit mindestens 7 Tagen existieren. Datenbanken, die sich bereits in einem elastischen Datenbankpool befinden, werden nicht als Empfehlungskandidaten für elastische Datenbankpools angesehen.

Der Dienst bewertet den Ressourcenbedarf und die Kosteneffizienz beim Verschieben der Einzeldatenbanken auf jeder Dienstebene in Pools auf der gleichen Ebene. Beispielsweise werden alle Datenbanken der Standard-Edition auf einem Server entsprechend ihrer Eignung für einen elastischen Pool der Standard-Edition bewertet. Dies bedeutet, dass der Dienst keine tarifübergreifenden Empfehlungen trifft, also etwa eine Standard-Datenbank nicht in einen Premium-Pool verschiebt.

## Zusätzliche Ressourcen

- [Verwalten eines elastischen SQL-Datenbankpools mit dem Portal](sql-database-elastic-pool-manage-portal.md)
- [Verwalten eines elastischen SQL-Datenbankpools mit PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Verwalten eines elastischen SQL-Datenbankpools mit C#](sql-database-client-library.md)
- [Referenz für elastische Datenbanken](sql-database-elastic-pool-reference.md)

<!---HONumber=AcomDC_0316_2016-->