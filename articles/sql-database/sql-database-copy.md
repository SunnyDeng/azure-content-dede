<properties
	pageTitle="Kopieren einer Azure SQL-Datenbank"
	description="Erstellen der Kopie einer Azure SQL-Datenbank"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/16/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Kopieren einer Azure SQL-Datenbank

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

Die folgenden Schritte veranschaulichen, wie Sie eine SQL-Datenbank über das [Azure-Vorschauportal](https://portal.azure.com) kopieren. Mit dem Vorgang zum Kopieren einer Datenbank wird eine neue SQL-Datenbank erstellt. Die Kopie ist eine Snapshotsicherung Ihrer Datenbank, die Sie entweder auf demselben Server oder auf einem anderen Server erstellen.

> [AZURE.NOTE]Azure SQL-Datenbank erstellt und verwaltet automatisch Sicherungen für jede Benutzerdatenbank, die Sie wiederherstellen können. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).

Wenn der Kopiervorgang abgeschlossen ist, handelt es sich bei der neuen Datenbank um eine voll funktionsfähige Datenbank, die unabhängig von der Quelldatenbank ist. Die neue Datenbank entspricht in Bezug auf Transaktionen mit der Quelldatenbank für den Zeitpunkt überein, an dem die Erstellung der Kopie abgeschlossen ist. Die Dienstebene und die Leistungsebene (Tarif) der Datenbankkopie stimmen mit den Ebenen der Quelldatenbank überein. Nachdem der Kopiervorgang abgeschlossen ist, wird die Kopie zu einer voll funktionsfähigen, unabhängigen Datenbank. Anmeldungen, Benutzer und Berechtigungen können unabhängig verwaltet werden.


Wenn Sie eine Datenbank auf denselben logischen Server kopieren, können für beide Datenbanken die gleichen Anmeldedaten verwendet werden. Das Sicherheitsprinzipal, das Sie zum Kopieren der Datenbank verwenden, wird zum Datenbankbesitzer (DBO) der neuen Datenbank. Alle Datenbankbenutzer, ihre Berechtigungen und ihre Sicherheits-IDs (SIDs) werden in die Kopie der Datenbank kopiert.


Zum Kopieren einer SQL-Datenbank benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Eine zu kopierende SQL-Datenbank. Wenn Sie nicht über eine SQL-Datenbank verfügen, können Sie die Erstellung anhand der Schritte im folgenden Artikel durchführen: [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).



## Kopieren der SQL-Datenbank

Öffnen Sie das Blatt „SQL-Datenbank“ für die Datenbank, die Sie kopieren möchten:

1.	Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com).
2.	Navigieren Sie zu der Datenbank, die Sie kopieren möchten: Durchsuchen > SQL-Datenbanken.
3.	Klicken Sie auf dem Blatt „SQL-Datenbank“ auf **Kopieren**, um das Blatt **Kopieren** zu öffnen:

    ![Datenbank kopieren][1]

1.  Geben Sie einen Namen für die Kopie der Datenbank ein. Es wird ein Standardname angegeben, den Sie bei Bedarf ändern können.
2.  Wählen Sie einen **Zielserver** aus. Auf dem Zielserver wird die Kopie der Datenbank erstellt. Sie können einen neuen Server erstellen oder in der Liste einen vorhandenen Server auswählen.
3.  Klicken Sie auf **OK**, um den Kopiervorgang zu starten.

    ![Datenbankname und Server][2]




## Überwachen des Fortschritts des Kopiervorgangs

2.	Klicken Sie nach dem Starten des Kopiervorgangs auf die Portalbenachrichtigung, um Details anzuzeigen.


    ![Benachrichtigung][3]

 
    ![Überwachen][4]





## Sicherstellen, dass die Datenbank auf dem Server aktiv ist

- Klicken Sie auf **DURCHSUCHEN** > **SQL-Datenbanken**, und überprüfen Sie, ob die neue Datenbank **online** ist.



## Nächste Schritte

- [Herstellen einer Verbindung mit SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)
- [Exportieren der Datenbank in eine BACPAC-Datei](sql-database-export.md)



## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [SQL-Datenbank-Dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=Oct15_HO4-->