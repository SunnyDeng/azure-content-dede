<properties
	pageTitle="Ausführen des Assistenten zum Aktivieren einer Datenbank für Stretch | Microsoft Azure"
	description="Erfahren Sie, wie eine Datenbank für Stretch-Datenbank konfigurieren, indem Sie den Assistenten zum Aktivieren einer Datenbank für Stretch ausführen."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Ausführen des Assistenten zum Aktivieren einer Datenbank für Stretch

Führen Sie zum Konfigurieren einer Datenbank für Stretch-Datenbank den Assistenten zum Aktivieren einer Datenbank für Stretch aus. In diesem Thema werden die Informationen beschrieben, die Sie eingeben, und die Optionen, die Sie im Assistenten festlegen müssen.

Weitere Informationen zu Stretch-Datenbank finden Sie unter [Stretch-Datenbank](sql-server-stretch-database-overview.md).

## Starten des Assistenten

1.  Wählen Sie in SQL Server Management Studio im Objekt-Explorer die Datenbank aus, für die Stretch aktiviert werden soll.

2.  Klicken Sie mit der rechten Maustaste, und wählen Sie **Aufgaben** und dann **Stretch** aus. Wählen Sie dann **Aktivieren**, um den Assistenten zu starten.

## <a name="Intro"></a>Einführung
Überprüfen Sie den Zweck des Assistenten und die Voraussetzungen.

![Einführungsseite des Assistenten der Stretch-Datenbank][StretchWizardImage1]

## <a name="Tables"></a>Auswählen von Tabellen
Wählen Sie die Tabellen, die Sie für Stretch aktivieren möchten.

![Auswahl der Tabellenseite des Assistenten der Stretch-Datenbank][StretchWizardImage2]

|Column|Beschreibung|
|----------|---------------|
|(ohne Titel)|Aktivieren Sie das Kontrollkästchen in dieser Spalte, um die ausgewählte Tabelle für Stretch zu aktivieren.|
|**Name**|Gibt den Namen der Spalte in der Tabelle an.|
|(ohne Titel)|Ein Symbol in dieser Spalte gibt normalerweise an, dass Sie die ausgewählte Tabelle aufgrund eines Hindernisses nicht für Stretch aktivieren können. Möglicherweise verwendet die Tabelle einen nicht unterstützten Datentyp. Zeigen Sie auf das Symbol, um weitere Informationen in einer QuickInfo anzuzeigen. Weitere Informationen finden Sie unter [Oberflächeneinschränkungen und Hindernisse für Stretch-Datenbank](sql-server-stretch-database-limitations.md).|
|**Gestreckt**|Gibt an, ob die Tabelle bereits aktiviert ist.|
|**beibehalten**|Gibt die Anzahl der Zeilen in der Tabelle an|
|**Größe (KB)**|Gibt die Größe der Tabelle in KB an.|
|**Migrieren**|In CTP 3.1 bis RC0 können Sie nur eine gesamte Tabelle mithilfe des Assistenten migrieren. Wenn Sie ein Prädikat für die Auswahl von Zeilen zur Migration aus einer Tabelle angeben möchten, die historische und aktuelle Daten enthält, führen Sie die ALTER TABLE-Anweisung aus, um ein Prädikat anzugeben, nachdem Sie den Assistenten beendet haben. Weitere Informationen finden Sie unter [Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md) oder [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|

## <a name="Configure"></a>Konfigurieren der Azure-Bereitstellung

1.  Melden Sie sich bei Microsoft Azure mit einem Microsoft-Konto an.

    ![Anmelden bei Azure – Assistent der Stretch-Datenbank][StretchWizardImage3]

2.  Wählen Sie das Azure-Abonnement für Stretch-Datenbank aus.

3.  Wählen Sie eine Azure-Region. Wenn Sie einen neuen Server erstellen, wird der Server in dieser Region erstellt.

    Um Latenz zu minimieren, wählen Sie die Azure-Region, in der sich Ihr SQL Server befindet. Weitere Informationen zu Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/).

4.  Geben Sie an, ob Sie einen vorhandenen Server verwenden oder einen neuen Azure-Server erstellen möchten.

    Wenn Active Directory auf dem SQL Server mit Azure Active Directory verbunden ist, können Sie optional ein Verbunddienstkonto für SQL Server für die Kommunikation mit dem Azure-Remoteserver verwenden. Weitere Informationen zu den Anforderungen für diese Option finden Sie unter [ALTER DATABASE SET-Optionen (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

	-   **Erstellen eines neuen Servers**

        1.  Erstellen Sie einen Benutzernamen und ein Kennwort für den Serveradministrator.

        2.  Wählen Sie optional ein Verbunddienstkonto für SQL Server für die Kommunikation mit dem Azure-Remoteserver aus.

		![Erstellen eines neuen Azure-Server – Assistent der Stretch-Datenbank][StretchWizardImage4]

    -   **Vorhandener Server**

        1.  Wählen Sie den Namen des vorhandenen Azure-Server aus, oder geben Sie ihn ein.

        2.  Wählen Sie die Authentifizierungsmethode aus.

            -   Wenn Sie die Option **SQL Server-Authentifizierung** auswählen, erstellen Sie einen neuen Benutzernamen und ein neues Kennwort.

            -   Wählen Sie **Integrierte Active Directory-Authentifizierung** aus, um ein Verbunddienstkonto für SQL Server für die Kommunikation mit dem Azure-Remoteserver zu verwenden.

		![Auswählen eines vorhandenen Azure-Servers – Assistent der Stretch-Datenbank][StretchWizardImage5]

## <a name="Credentials"></a>Sichere Anmeldeinformationen
Geben Sie ein sicheres Kennwort zum Erstellen eines Datenbankhauptschlüssels ein, oder geben Sie das Kennwort ein, wenn bereits ein Datenbankhauptschlüssel vorhanden ist.

Sie brauchen einen Datenbankhauptschlüssel, um die Anmeldeinformationen zu sichern, die Stretch-Datenbank für die Verbindung mit der Remotedatenbank verwendet.

![Seite mit den sicheren Anmeldeinformationen des Assistenten der Stretch-Datenbank][StretchWizardImage6]

Weitere Informationen zum Datenbankhauptschlüssel finden Sie unter [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) und [Erstellen eines Datenbankhauptschlüssels](https://msdn.microsoft.com/library/aa337551.aspx). Weitere Informationen über die Anmeldeinformationen, die der Assistent erstellt, finden Sie unter [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Auswählen der IP-Adresse
Verwenden Sie die öffentliche IP-Adresse von SQL Server, oder geben Sie einen IP-Adressbereich ein, um eine Firewallregel für Azure zu erstellen, damit SQL Server mit dem Azure-Remoteserver kommunizieren kann.

Die auf dieser Seite angegebenen IP-Adressen informieren den Azure-Server darüber, dass eingehende Daten, Abfragen und Verwaltungsvorgänge, die von SQL Server initiiert werden, die Azure-Firewall durchlaufen können. Der Assistent ändert keine Einstellungen der Firewall auf SQL Server.

![Auswählen der IP-Adressseite des Assistenten der Stretch-Datenbank][StretchWizardImage7]

## <a name="Summary"></a>Zusammenfassung
Überprüfen Sie die Werte, die Sie eingegeben haben, und die im Assistenten ausgewählten Optionen. Wählen Sie dann **Fertig stellen**, um Stretch zu aktivieren.

![Zusammenfassungsseite des Assistenten der Stretch-Datenbank][StretchWizardImage8]

## <a name="Results"></a>Ergebnisse
Überprüfen Sie die Ergebnisse.

Wählen Sie optional **Überwachen** aus, um den Status der Datenmigration im Stretch Database Monitor zu überwachen. Weitere Informationen finden Sie unter [Überwachen und Behandeln von Problemen der Datenmigration (Stretch-Datenbank)](sql-server-stretch-database-monitor.md).

## <a name="KnownIssues"></a>Problembehandlung für den Assistenten
**Der Assistent für Stretch-Datenbank ist fehlgeschlagen.** Wenn Stretch-Datenbank noch nicht auf Serverebene aktiviert ist und Sie den Assistenten ohne Systemadministratorberechtigungen zum Aktivieren ausführen, schlägt der Assistent fehl. Bitten Sie den Systemadministrator, Stretch-Datenbank auf der lokalen Serverinstanz zu aktivieren, und führen Sie den Assistenten dann erneut aus. Weitere Informationen finden Sie unter [Voraussetzung: Berechtigung zum Aktivieren von Stretch-Datenbank auf dem Server](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## Nächste Schritte
Aktivieren von zusätzlichen Tabellen für Stretch-Datenbank Überwachen der Datenmigration und Verwalten von Stretch-fähigen Datenbanken und Tabellen

-   [Aktivieren der Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md) zum Aktivieren zusätzlicher Tabellen

-   [Überwachen der Stretch-Datenbank](sql-server-stretch-database-monitor.md) zum Anzeigen des Status der Datenmigration

-   [Anhalten und Fortsetzen von Stretch-Datenbank](sql-server-stretch-database-pause.md)

-   [Verwalten von Stretch-Datenbank und Behandeln von Problemen ](sql-server-stretch-database-manage.md)

-   [Sichern und Wiederherstellen von Stretch-fähigen Datenbanken](sql-server-stretch-database-backup.md)

## Weitere Informationen

[Aktivieren von Stretch-Datenbank für eine Datenbank](sql-server-stretch-database-enable-database.md)

[Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png

<!---HONumber=AcomDC_0316_2016-->