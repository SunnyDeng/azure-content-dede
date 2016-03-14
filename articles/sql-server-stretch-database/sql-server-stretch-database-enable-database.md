<properties
	pageTitle="Aktivieren von Stretch-Datenbank für eine Datenbank | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine Datenbank für Stretch-Datenbank konfigurieren."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Aktivieren von Stretch-Datenbank für eine Datenbank

Um eine Datenbank für Stretch-Datenbank zu konfigurieren, wählen Sie **Aufgaben | Stretch | Aktivieren** für eine Datenbank in SQL Server Management Studio, um den Assistenten zum **Aktivieren einer Datenbank für Stretch** zu öffnen. Sie können auch Transact-SQL verwenden, um Stretch-Datenbank für eine Datenbank zu aktivieren.

Wenn Sie **Aufgaben | Stretch | Aktivieren** für eine Tabelle auswählen und Sie die Datenbank noch nicht für Stretch-Datenbank aktiviert haben, konfiguriert der Assistent die Datenbank für Stretch-Datenbank und ermöglicht das Konfigurieren von Tabellen als Teil des Prozesses. Führen Sie die Schritte in diesem Thema anstelle der Schritte in [Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-database.md) aus.

Für das Aktivieren von Stretch-Datenbank für eine Datenbank oder eine Tabelle sind db\_owner-Berechtigungen erforderlich. Für das Aktivieren von Stretch-Datenbank für eine Datenbank sind ebenfalls CONTROL DATABASE-Berechtigungen erforderlich.

## Bevor Sie beginnen

-   Bevor Sie eine Datenbank für Stretch konfigurieren, empfiehlt sich das Ausführen von Stretch Database Advisor, um Datenbanken und Tabellen zu identifizieren, die für Stretch berechtigt sind. Der Stretch Database Advisor erkennt auch Hindernisse. Weitere Informationen finden Sie unter [Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank](sql-server-stretch-database-identify-databases.md).

-   Überprüfen Sie [Oberflächeneinschränkungen und Hindernisse für Stretch-Datenbank](sql-server-stretch-database-limitations.md).

-   Stretch-Datenbank migriert Daten in Azure. Daher benötigen Sie für die Abrechnung ein Azure-Konto und ein Abonnement. [Klicken Sie hier](http://azure.microsoft.com/pricing/free-trial/), um ein Azure-Konto anzulegen.

-   Halten Sie die Informationen bereit, die Sie benötigen, um eine neue Remotedatenbank zu erstellen oder eine vorhandene Remotedatenbank auszuwählen sowie um eine Firewallregel zu erstellen, die die Kommunikation des lokalen Servers mit dem Remoteserver ermöglicht.

## <a name="EnableTSQLServer"></a>Voraussetzung: Berechtigung zum Aktivieren von Stretch-Datenbank auf dem Server
Bevor Sie Stretch-Datenbank für eine Datenbank oder eine Tabelle aktivieren können, müssen Sie sie auf dem lokalen Server aktivieren. Für diesen Vorgang sind sysadmin- oder serveradmin-Berechtigungen erforderlich.

-   Wenn Sie über die erforderlichen Administratorberechtigungen verfügen, konfiguriert der Assistent zum **Aktivieren einer Datenbank für Stretch** den Server für Stretch.

-   Wenn Sie nicht über die erforderlichen Berechtigungen verfügen, muss ein Administrator die Option manuell aktivieren, indem **sp\_configure** vor dem Ausführen des Assistenten ausgeführt wird. Alternativ muss der Assistent von einem Administrator ausgeführt werden.

Um Stretch-Datenbank auf dem Server manuell zu aktivieren, führen Sie **sp\_configure** aus, und schalten Sie die Option für die **Remotedatenarchivierung** ein. Im folgenden Beispiel wird die Option zur **Remotedatenarchivierung** durch Festlegen des Werts 1 aktiviert.

```
EXEC sp_configure 'remote data archive' , '1';
GO
RECONFIGURE;
GO
```
Weitere Informationen finden Sie unter [Konfigurieren der Serverkonfigurationsoption für die Remotedatenarchivierung](https://msdn.microsoft.com/library/mt143175.aspx) und [sp\_configure (Transact-SQL)](https://msdn.microsoft.com/library/ms188787.aspx).

## <a name="Wizard"></a>Verwenden des Assistenten zum Aktivieren von Stretch-Datenbank für eine Datenbank
Informationen über den Assistenten zum Aktivieren einer Datenbank für Stretch, einschließlich der von Ihnen einzugebenden Informationen und der auszuwählenden Angaben, finden Sie unter [Assistent zum Aktivieren einer Datenbank für Stretch](sql-server-stretch-database-wizard.md).

## <a name="EnableTSQLDatabase"></a>Verwenden von Transact-SQL zum Aktivieren von Stretch-Datenbank für eine Datenbank
Bevor Sie Stretch-Datenbank für einzelne Tabellen aktivieren können, müssen Sie sie auf dem lokalen Server aktivieren.

Für das Aktivieren von Stretch-Datenbank für eine Datenbank oder eine Tabelle sind db\_owner-Berechtigungen erforderlich. Für das Aktivieren von Stretch-Datenbank für eine Datenbank sind ebenfalls CONTROL DATABASE-Berechtigungen erforderlich.

1.  Wählen Sie vor Beginn einen vorhandenen Azure-Server für die Daten aus, die Stretch-Datenbank migriert, oder erstellen Sie einen neuen Server.

2.  Erstellen Sie auf dem Azure-Server eine Firewallregel mit der IP-Adresse (oder dem IP-Adressbereich) von SQL Server, sodass SQL Server mit dem Remoteserver kommunizieren kann.

3.  Zum Konfigurieren einer SQL Server-Datenbank für Stretch-Datenbank muss die Datenbank über einen Datenbankhauptschlüssel verfügen. Der Datenbankhauptschlüssel sichert die Anmeldeinformationen, die Stretch-Datenbank für die Verbindung mit der Remotedatenbank verwendet. Informationen zum manuellen Erstellen des Datenbankhauptschlüssels finden Sie unter [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) und [Erstellen eines Datenbankhauptschlüssels](https://msdn.microsoft.com/library/aa337551.aspx).

    ```tsql
    USE <database>
    GO

    CREATE MASTER KEY ENCRYPTION BY PASSWORD='<password>'
    ```

4.  Wenn Sie eine Datenbank für Stretch-Datenbank konfigurieren, müssen Sie die Anmeldeinformationen für Stretch-Datenbank angeben, um die Kommunikation zwischen dem lokalen SQL Server und dem Azure-Remoteserver zu ermöglichen. Sie haben zwei Möglichkeiten.

    -   Sie können die Anmeldeinformationen des Administrators angeben.

        -   Wenn Sie Stretch-Datenbank durch Ausführen des Assistenten aktivieren, können Sie die Anmeldeinformationen zu diesem Zeitpunkt erstellen.

        -   Wenn Sie Stretch-Datenbank durch Ausführen von **ALTER DATABASE** aktivieren, müssen Sie die Anmeldeinformationen manuell erstellen, bevor Sie Stretch-Datenbank aktivieren.

        Informationen zum manuellen Erstellen der Anmeldeinformationen finden Sie unter [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx). Zum Erstellen der Anmeldeinformationen sind die ALTER ANY CREDENTIAL-Berechtigungen erforderlich.

        ```tsql
        CREATE DATABASE SCOPED CREDENTIAL <db_scoped_credential_name>
            WITH IDENTITY = '<identity>' , SECRET = '<secret>'
        GO
        ```

    -   Sie können ein Verbunddienstkonto für den SQL Server verwenden, um mit dem Azure-Remoteserver zu kommunizieren, wenn alle der folgenden Bedingungen zutreffen.

        -   Das Dienstkonto, unter dem die Instanz von SQL Server ausgeführt wird, ist ein Domänenkonto.

        -   Das Domänenkonto gehört zu einer Domäne, deren Active Directory mit Azure Active Directory verbunden ist.

        -   Der Azure-Remoteserver wird für die Unterstützung der Azure Active Directory-Authentifizierung konfiguriert.

        -   Das Dienstkonto, unter dem die Instanz von SQL Server ausgeführt wird, muss als dbmanager- oder sysadmin-Konto auf dem Azure-Remoteserver konfiguriert werden.

5.  Um eine Datenbank für Stretch-Datenbank zu konfigurieren, führen Sie den Befehl ALTER DATABASE aus.

    1.  Geben Sie für das SERVER-Argument den Namen eines vorhandenen Azure-Servers ein, einschließlich des `.database.windows.net`-Teils des Namens, z. B. `MyStretchDatabaseServer.database.windows.net`.

    2.  Stellen Sie vorhandene Administratoranmeldeinformationen mit dem CREDENTIAL-Argument bereit, oder geben Sie FEDERATED\_SERVICE\_ACCOUNT \\= ON an. Das folgenden Beispiel enthält vorhandene Anmeldeinformationen:

    ```tsql
    ALTER DATABASE <database name>
        SET REMOTE_DATA_ARCHIVE = ON
            (
                SERVER = <server_name> ,
                CREDENTIAL = <db_scoped_credential_name>
            ) ;
    GO;
    ```

## Nächste Schritte
Aktivieren von zusätzlichen Tabellen für Stretch-Datenbank Überwachen der Datenmigration und Verwalten von Stretch-fähigen Datenbanken und Tabellen

-   [Aktivieren von Stretch-Datenbank für eine Tabelle](sql-server-stretch-database-enable-table.md) zum Aktivieren zusätzlicher Tabellen

-   [Überwachen von Stretch-Datenbank](sql-server-stretch-database-monitor.md) zum Anzeigen des Status der Datenmigration

-   [Anhalten und Fortsetzen von Stretch-Datenbank](sql-server-stretch-database-pause.md)

-   [Verwalten von Stretch-Datenbank und Behandeln von Problemen ](sql-server-stretch-database-manage.md)

-   [Sichern und Wiederherstellen von Stretch-fähigen Datenbanken](sql-server-stretch-database-backup.md)

## Weitere Informationen
[Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank](sql-server-stretch-database-identify-databases.md) [ALTER DATABASE SET-Optionen (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

<!---HONumber=AcomDC_0302_2016-->