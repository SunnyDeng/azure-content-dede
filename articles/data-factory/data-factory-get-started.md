<properties
	pageTitle="Tutorial: Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank"
	description="Dieses Tutorial veranschaulicht das Verwenden der Kopieraktivität in einer Azure Data Factory-Pipeline, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="02/01/2016"
	ms.author="spelluru"/>

# Tutorial: Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank
> [AZURE.SELECTOR]
- [Übersicht über das Tutorial](data-factory-get-started.md)
- [Verwenden des Data Factory-Editors](data-factory-get-started-using-editor.md)
- [Mithilfe von PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Verwenden von Visual Studio](data-factory-get-started-using-vs.md)

In diesem Tutorial erstellen Sie zunächst eine Azure Data Factory und dann mit einer Kopieraktivität eine Pipeline, um Daten von einem Azure Blob Storage in eine Azure SQL-Datenbank zu kopieren.

Die Kopieraktivität führt die Datenverschiebung in Azure Data Factory durch, und die Aktivität wird von einem global verfügbaren Dienst gestützt, mit dem Daten zwischen verschiedenen Datenspeichern auf sichere, zuverlässige und skalierbare Weise kopiert werden können. Ausführliche Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

> [AZURE.NOTE] Eine ausführliche Übersicht über den Data Factory-Dienst finden Sie im Artikel [Einführung in Azure Data Factory][data-factory-introduction].

##Voraussetzungen für das Lernprogramm
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement**. Wenn Sie über kein Abonnement verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Im Artikel [Kostenlose Testversion][azure-free-trial] finden Sie Details.
- **Azure Storage-Konto**. In diesem Lernprogramm verwenden Sie den Blobspeicher als **Quelldatenspeicher**. Wenn Sie kein Azure-Speicherkonto haben, finden Sie im Artikel [Erstellen eines Speicherkontos][data-factory-create-storage] Schritte zum Erstellen eines Azure-Speicherkontos.
- **Azure SQL-Datenbank**. In diesem Lernprogramm verwenden Sie eine Azure SQL-Datenbank als **Zieldatenspeicher**. Wenn Sie keine Azure SQL-Datenbank haben, die Sie in diesem Lernprogramm verwenden können, finden Sie unter [Erstellen und Konfigurieren einer Azure SQL-Datenbank][data-factory-create-sql-database] entsprechende Anweisungen.
- **SQL Server 2012/2014 oder Visual Studio 2013**. Sie verwenden SQL Server Management Studio oder Visual Studio zum Erstellen einer Beispieldatenbank und zum Anzeigen der Ergebnisdaten in der Datenbank.  

### Notieren des Kontonamens und -schlüssels Ihres Azure-Speicherkontos
Sie benötigen in diesem Lernprogramm den Kontonamen und -schlüssel Ihres Azure-Speicherkontos. Notieren Sie sich den **Kontonamen** und **Kontoschlüssel** Ihres Azure-Speicherkontos, indem Sie die folgenden Anweisungen befolgen:

1. Melden Sie sich beim [Azure-Portal][azure-portal] an.
2. Klicken Sie auf der linken Seite auf den Hub **Durchsuchen**, und wählen Sie **Speicherkonten** aus.
3. Wählen Sie auf dem Blatt **Speicherkonten** das **Azure-Speicherkonto** aus, das Sie in diesem Lernprogramm verwenden möchten.
4. Klicken Sie auf dem Blatt **SPEICHER** auf die Kachel **SCHLÜSSEL**.
5. Klicken Sie auf dem Blatt **Schlüssel verwalten** auf die (Bild-) Schaltfläche **Kopieren** neben dem Textfeld **SPEICHERKONTONAME**, und speichern Sie den Eintrag z. B. in einer Textdatei.  
6. Wiederholen Sie den vorherigen Schritt zum Kopieren oder Notieren des **PRIMÄREN ZUGRIFFSSCHLÜSSELS**.
7. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

### Notieren des Servernamens, Datenbanknamens und Benutzerkontos Ihrer Azure SQL-Datenbank
Sie benötigen in diesem Lernprogramm die Namen von Azure SQL-Server, -Datenbank und -Benutzer. Notieren Sie den Namen des **Servers**, der **Datenbank** und des **Benutzers** Ihrer Azure SQL-Datenbank, indem Sie die folgenden Anweisungen befolgen:

1. Klicken Sie links im **Azure-Portal** auf **Durchsuchen**, und wählen Sie **SQL-Datenbanken** aus.
2. Wählen Sie auf dem Blatt **SQL-Datenbanken** die **Datenbank**, die Sie in diesem Lernprogramm verwenden möchten. Notieren Sie sich den **Datenbanknamen**.  
3. Klicken Sie auf dem Blatt **SQL-DATENBANK** auf die Kachel **EIGENSCHAFTEN**.
4. Notieren Sie sich die Werte für **SERVERNAME** und **SERVER-ADMIN-ANMELDUNG**.
5. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

### Zulassen des Zugriffs von Azure-Diensten auf den Azure SQL-Server
Stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihren Azure SQL-Server **aktiviert** ist, damit der Data Factory-Dienst auf Ihren Azure SQL-Server zugreifen kann. Um diese Einstellung zu überprüfen und zu aktivieren, führen Sie folgende Schritte aus:

1. Klicken Sie links auf den Hub **DURCHSUCHEN** und dann auf **SQL-Server**.
2. Wählen Sie **Ihren Server** aus, und klicken Sie auf dem Blatt **SQL SERVER** auf **EINSTELLUNGEN**.
3. Klicken Sie auf dem Blatt **EINSTELLUNGEN** auf **Firewall**.
4. Klicken Sie auf dem Blatt **Firewalleinstellungen** für **Zugriff auf Azure-Dienste erlauben** auf **EIN**.
5. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

### Vorbereiten von Azure Blob-Speicher und Azure SQL-Datenbank für das Lernprogramm
Nun bereiten Sie Ihrer Azure-Blobspeicher und die Azure SQL-Datenbank durch Ausführen der folgenden Schritte auf das Lernprogramm vor:

1. Öffnen Sie den Editor, fügen Sie den folgenden Text ein, und speichern Sie die Datei als **emp.txt** im Ordner **C:\\ADFGetStarted** auf Ihrer Festplatte.

        John, Doe
		Jane, Doe

2. Verwenden Sie Tools wie [Azure Storage-Explorer](https://azurestorageexplorer.codeplex.com/) zum Erstellen des Containers **adftutorial** und zum Hochladen der Datei **emp.txt** in den Container.

    ![Azure-Speicher-Explorer](./media/data-factory-get-started/getstarted-storage-explorer.png)
3. Verwenden Sie das folgende SQL-Skript, um die Tabelle **emp** in der Azure SQL-Datenbank zu erstellen.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Wenn Sie SQL Server 2012/2014 auf Ihrem Computer installiert haben:** Befolgen Sie die Anweisungen unter "Schritt 2: Herstellen einer Verbindung mit SQL-Datenbank" im Artikel [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio][sql-management-studio], um eine Verbindung mit Ihrem Azure SQL-Server herzustellen und das SQL-Skript auszuführen. Beachten Sie, dass in diesem Artikel das [klassische Azure-Portal](http://manage.windowsazure.com) und nicht das [Azure-Portal](https://portal.azure.com) verwendet wird, um die Firewall für einen Azure SQL-Server zu konfigurieren.

	**Wenn Sie Visual Studio 2013 auf Ihrem Computer installiert haben:** Klicken Sie im [Azure-Portal](https://portal.azure.com) links auf **Durchsuchen**, dann auf **SQL-Server**, wählen Sie Ihre Datenbank aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **In Visual Studio öffnen**, um eine Verbindung mit Ihrem Azure SQL-Server herzustellen und das SQL-Skript auszuführen. Wenn Ihr Client nicht auf den Azure SQL-Server zugreifen darf, müssen Sie die Firewall für Ihren Azure SQL-Server so konfigurieren, dass der Zugriff über Ihren Computer (bzw. dessen IP-Adresse) ermöglicht wird. Im vorherigen Artikel finden Sie Schritte zum Konfigurieren der Firewall für Ihren Azure SQL-Server.


Gehen Sie wie folgt vor:

- Klicken Sie oben auf den Link [Data Factory-Editor verwenden](data-factory-get-started-using-editor.md), um das Tutorial mithilfe des Data Factory-Editors auszuführen, der Teil des klassischen Azure-Portals ist.
- Klicken Sie oben auf den Link [PowerShell verwenden](data-factory-monitor-manage-using-powershell.md), um das Lernprogramm mit Azure PowerShell auszuführen.
- Klicken Sie oben auf den Link [Mit Visual Studio](data-factory-get-started-using-vs.md), um das Lernprogramm mit Visual Studio 2013 auszuführen.

## Kopieraktivität
Ausführliche Informationen zur Kopieraktivität in Azure Data Factory finden Sie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0302_2016-->