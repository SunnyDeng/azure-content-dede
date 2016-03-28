<properties
	pageTitle="Einen Pool für elastische Datenbanken überwachen, verwalten und skalieren"
	description="Erfahren Sie, wie Sie mithilfe der im Azure-Portal und in der SQL-Datenbank integrierten Intelligenz einen skalierbaren Pool für elastische Datenbanken verwalten, überwachen und skalieren, um die Leistung der Datenbank und die Kostenverwaltung zu optimieren."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/16/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Einen Pool für elastische Datenbanken mit dem Azure-Portal überwachen, verwalten und skalieren

> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)


Dieser Artikel beschreibt, wie Sie das Azure-Portal dazu nutzen, einen Pool für elastische Datenbanken und die dazugehörigen Datenbanken zu überwachen, zu verwalten und zu skalieren. Die SQL-Datenbank verfügt über eine integrierte Logik, die die historische Nutzungstelemetrie analysiert und eigenständig einen Pool für Datenbanken empfiehlt, wenn dies die kostengünstigere Lösung ist. Sie können auch Datenbanken hinzufügen und entfernen, bevor Sie Änderungen committen und die Auswirkungen auf die Leistung des Pools und den Speicher beobachten können.

Um diese Anleitung durchzuarbeiten, brauchen Sie Datenbanken und einen Pool. Besuchen Sie [Erstellen eines Pools](sql-database-elastic-pool-create-portal.md), wenn Sie bereits Datenbanken haben. Falls nicht, verwenden Sie das [SQL-Datenbank-Tutorial](sql-database-get-started).

**So wählen Sie den Pool aus, mit dem Sie arbeiten möchten:**

- Im [Azure-Portal](https://portal.azure.com) klicken Sie auf **Durchsuchen**, dann auf **elastische SQL-Pools** und wählen dann den Pool in der Liste aus, mit dem Sie arbeiten möchten.

##Überwachen der Ressourcenverwendung eines Pools
Nachdem Sie einen Pool ausgewählt haben, zeigen Ihnen unter **Überwachung des elastischen Pools** ein Diagramm und Live-Kacheln wichtige Auslastungsdaten zu Ihrem Pool an.

![Überwachen eines elastischen Pools](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**So ändern Sie den Datumsbereich, den Diagrammtyp (Balken- oder Liniendiagramm) oder die angezeigten Ressourcen:**

- Klicken Sie auf **Bearbeiten**, wählen Sie die gewünschten Einstellungen, und klicken Sie dann auf **Speichern**, um das Diagramm zu aktualisieren.

**So ändern Sie die Live-Kacheln:**

- Klicken Sie auf **Kacheln hinzufügen**, und wählen Sie dann die Kacheln aus dem Kachel-Katalog, der auf der linken Seite angezeigt wird.

##Eine Warnung zu einer Poolressource hinzufügen
Sie können Regeln zu Ressourcen hinzufügen, die E-Mails an Personen verschicken, oder Warnzeichenfolgen zu einem URL-Endpunkt, wenn die Ressource einen Verwendungsschwellenwert erreicht, den Sie eingerichtet haben.

**So fügen Sie eine Warnung zu einer Ressource hinzu:**

1. Klicken Sie auf das Diagramm **Ressourcenverwendung**, um das Blatt **Metrik** zu öffnen. Klicken Sie auf **Warnung hinzufügen**, und füllen Sie dann die Informationen auf dem Blatt **Warnungsregel hinzufügen** aus (**Ressource** wird automatisch auf den Pool festgelegt, mit dem Sie arbeiten).
2. Geben Sie einen **Namen** und eine **Beschreibung** ein, die die Warnung Ihnen und anderen Empfängern gegenüber identifiziert.
3. Wählen Sie eine **Metrik** aus, für die aus der Liste eine Warnung ausgegeben werden soll.

    Das Diagramm zeigt dynamisch die Ressourcenverwendung für diese Metrik an, um Ihnen die Festlegung eines Schwellenwerts zu erleichtern.

4. Wählen Sie eine **Bedingung** (größer als, kleiner als usw.) und einen **Schwellenwert**.
5. Klicken Sie auf **OK**.

##Ändern der eDTUs pro Pool und pro Datenbank-eDTU
Wenn Sie die Ressourcenverwendung eines Pools näher betrachten, werden Sie möglicherweise feststellen, dass der Pool oder einzelne Datenbanken im Pool andere eDTU-Einstellungen brauchen. Sie können das Setup des Pools jederzeit ändern, um die beste Leistungsverteilung und Kosteneffizienz zu erzielen. Weitere Informationen finden Sie unter [Überlegungen zum Preis und zur Leistung eines elastischen Datenbankpools](sql-database-elastic-pool-guidance.md).

**So ändern Sie die Pool-eDTU und eDTU pro Datenbank:**

1. Klicken Sie auf **Pool konfigurieren**, um das Blatt **Leistung konfigurieren** zu öffnen.

    In den **Einstellungen des Pools für elastische Datenbanken** zeigt Ihnen ein Diagramm den aktuellen Trend der eDTU-Nutzung und Speicherauslastung (prozentualer Anteil der Poolkapazität).

    ![Ressourcenverwendung eines elastischen Pools](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Klicken Sie auf eine andere **Pool-eDTU**, und es werden Ihnen die geschätzten Kosten angezeigt, die mit der gewünschten Umstellung verbunden sind. Das Diagramm aktualisiert sich und zeigt die vorhergesagte Auslastung mit der maximalen eDTU an, die Sie ausgewählt haben.

    ![Einen Pool aktualisieren und neue monatliche Kosten](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. In der **Einstellung für elastische Datenbank** zeigt ein Balkendiagramm die eDTU-Nutzung der einzelnen Datenbanken im Pool an.

4. Klicken Sie auf **eDTU maximal**, um das Maximum festzulegen, und auf **eDTU mininmal**, um die minimale Anzahl von eDTUs für die Datenbanken im Pool festzulegen.

    ![Die minimalen und maximalen eDTU-Einstellungen einer elastischen Datenbank aktualisieren](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##Hinzufügen und Entfernen von Datenbanken

Nachdem Sie einen Pool erstellt haben, können Sie Datenbanken zum Pool hinzufügen oder daraus entfernen. Sie können nur Datenbanken auf dem gleichen SQL-Server hinzufügen.

**Hinzufügen von Datenbanken:**

1. Klicken sie im Blatt für den Pool unter **elastische Datenbanken** auf den Link, der die Anzahl der Datenbanken im Pool anzeigt.

    ![Auflisten von Datenbanken](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Klicken Sie im Blatt **elastische Datenbanken** auf **Datenbank hinzufügen**. Klicken Sie auf die Datenbanken, die Sie hinzufügen wollen, und klicken Sie dann auf die Schaltfläche **Auswählen**.

    Auf dem Blatt **elastische Datenbanken** wird jetzt die Datenbank aufgeführt, die Sie gerade hinzugefügt haben, zusammen mit **Mittelwert DTU**, der Speicherplatzauslastung als **Größe (GB)** und dem Status **Ausstehend**. Die Poolauslastungswerte zeigen Ihnen nun an, wie die Werte **Neu** wären, wenn Sie die Änderungen speichern.

    ![Empfohlener Pool](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. Klicken Sie auf **Speichern**, und klicken Sie dann auf **OK**, wenn das Portal meldet, dass die Anforderung übermittelt wurde. Die Anzahl der Datenbanken im Pool wird auf dem Blatt für den Pool angezeigt, wenn der Vorgang abgeschlossen ist.

**Entfernen von Datenbanken:**

1. Klicken sie im Blatt für den Pool unter **elastische Datenbanken** auf den Link, der die Anzahl der Datenbanken im Pool anzeigt.

    ![Auflisten von Datenbanken](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Im Blatt **elastische Datenbanken** klicken Sie in der Liste der Datenbanken im Pool auf die Datenbanken, die Sie entfernen möchten, und klicken Sie dann auf **Datenbanken entfernen**.

    Die Poolauslastungswerte zeigen Ihnen nun an, wie die Werte **Neu** wären, wenn Sie die Änderungen speichern.

3. Klicken Sie auf **Speichern**, und klicken Sie dann auf **OK**, wenn das Portal meldet, dass die Anforderung übermittelt wurde. Die Anzahl der Datenbanken im Pool wird auf dem Blatt für den Pool angezeigt, wenn der Vorgang abgeschlossen ist.

## Erstellen einer neuen Datenbank in einem Pool

Klicken Sie im Blatt für die Datenbank einfach auf **Datenbank erstellen**. Die SQL-Datenbank ist bereits für den richtigen Server und Pool konfiguriert. Gehen Sie also zu den weiteren Einstellungen über und klicken Sie dann auf **OK**, um die neue Datenbank im Pool zu erstellen:

   ![Erstellen einer elastischen Datenbank](./media/sql-database-elastic-pool-portal/create-database.png)

##Erstellen und Verwalten von elastischen Aufträgen

Elastische Aufträge ermöglichen die Ausführung von Transact-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool. Vor der Verwendung von Aufträgen installieren Sie die Komponenten für elastische Aufträge, und geben Sie Ihre Anmeldeinformationen an. Weitere Informationen finden Sie unter [Übersicht über elastische Datenbankaufträge](sql-database-elastic-jobs-overview.md).

## Zusätzliche Ressourcen

- [Referenz für elastische Datenbanken](sql-database-elastic-pool-reference.md)
- [Elastische SQL-Datenbankpools](sql-database-elastic-pool.md)
- [Erstellen eines Pools für elastische Datenbanken mit dem Portal](sql-database-elastic-pool-create-csharp.md)
- [Erstellen eines Pools für elastische Datenbanken mit PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Erstellen eines Pools für elastische Datenbanken mit C#](sql-database-elastic-pool-create-csharp.md)
- [Überlegungen zum Preis und zur Leistung eines Pools für elastische Datenbanken](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0316_2016-->