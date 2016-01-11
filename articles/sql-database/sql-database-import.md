<properties
	pageTitle="Importieren einer BACPAC-Datei zum Erstellen einer neuen Azure SQL-Datenbank"
	description="Erstellen Sie eine neue Azure SQL-Datenbank, indem Sie eine vorhandene BACPAC-Datei importieren."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/17/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Importieren einer BACPAC-Datei zum Erstellen einer neuen Azure SQL-Datenbank


**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Dieser Artikel enthält Anweisungen zum Erstellen einer neuen Azure SQL-Datenbank aus einer BACPAC-Datei mithilfe des [Azure-Portals](https://portal.azure.com).

Ein „BACPAC“ ist eine BACPAC-Datei, die ein Datenbankschema und Daten enthält. Weitere Informationen finden Sie unter „Sicherungspaket (.bacpac)“ in [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx).

Die Datenbank wird aus einer BACPAC-Datei erstellt, die aus einem Azure-Speicherblobcontainer importiert wurde. Wenn im Azure-Speicher keine BACPAC-Datei vorhanden ist, können Sie eine erstellen, indem Sie die in [Erstellen und Exportieren der BACPAC-Datei einer Azure SQL-Datenbank](sql-database-export.md) beschriebenen Schritte ausführen.


> [AZURE.NOTE]Azure SQL-Datenbank erstellt und verwaltet automatisch Sicherungen für jede Benutzerdatenbank, die Sie wiederherstellen können. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).


Zum Importieren einer SQL-­Datenbank aus einer BACPAC-Datei benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Einen Azure SQL-Datenbank V12-Server Wenn Sie nicht über einen Server der Version 12 verfügen, erstellen Sie ihn anhand der Schritte in dem Artikel [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).
- Eine BACPAC-Datei der Datenbank, die Sie in einen [Azure Storage-Konto (klassischc)](storage-create-storage-account.md)-Blobcontainer importieren möchten.


## Auswählen des Servers, der die Datenbank enthalten soll

Öffnen Sie das SQL Server-Blatt für die Datenbank, die Sie importieren möchten:

1.	Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Server**.
2.	Klicken Sie auf den Server, um die Datenbank darauf wiederherzustellen.
3.	Klicken Sie im SQL Server-Blatt auf **Datenbank importieren**, um das Blatt **Datenbank importieren** zu öffnen:

    ![Datenbank importieren][1]

1.  Klicken Sie auf **Storage**, wählen Sie Ihr Speicherkonto, den Blobcontainer und die BACPAC-Datei aus, und klicken Sie auf **OK**.

    ![Speicheroptionen konfigurieren][2]

1.  Wählen Sie den Tarif für die neue Datenbank aus, und klicken Sie auf **Auswählen**.

    ![Tarif auswählen][3]

1.  Geben Sie einen **DATENBANKNAMEN** ein.
2.  Geben Sie die **Serveradministratoranmeldung** und das **Kennwort** für den SQL Azure-Server ein, auf den Sie die Datenbank importieren.
1.  Klicken Sie auf **Erstellen**, um die Datenbank aus der BACPAC-Datei zu erstellen.

    ![Datenbank erstellen][4]

Durch Klicken auf **Erstellen** wird eine Anforderung zum Importieren der Datenbank an den Dienst gesendet. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Importvorgang abgeschlossen ist.

## Überwachen des Fortschritts des Importvorgangs

2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Server**.
2.	Klicken Sie auf dem Server, auf dem die Wiederherstellung erfolgt.
3.	Klicken Sie im Blatt „SQL Server“ auf **Import/Export-Verlauf**:

    ![Import/Export-Verlauf][5] ![Import/Export-Verlauf][6]





## Sicherstellen, dass die Datenbank auf dem Server aktiv ist

2.	Klicken Sie auf **ALLE DURCHSUCHEN**.
3.	Klicken Sie auf **SQL-Datenbanken**, und überprüfen Sie, ob die neue Datenbank **online** ist.



## Nächste Schritte

- [Herstellen einer Verbindung mit SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)



## Zusätzliche Ressourcen

- [SQL-Datenbankdokumentation](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=AcomDC_1223_2015-->