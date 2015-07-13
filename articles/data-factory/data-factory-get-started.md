<properties
	pageTitle="Erste Schritte mit Azure Data Factory"
	description="In diesem Lernprogramm erfahren Sie, wie Sie eine Beispieldatenpipeline erstellen, die Daten von einem Blob an eine Azure SQL-Datenbankinstanz kopiert."
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
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Erste Schritte mit Azure Data Factory
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

Dieser Artikel hilft Ihnen beim raschen Einstieg in die Verwendung des Azure Data Factory-Diensts. Das Lernprogramm zeigt, wie Sie erst eine Azure Data Factory und dann eine Pipeline in der Data Factory erstellen, um Daten aus einem Azure-Blobspeicher in eine Azure SQL-Datenbank zu kopieren.

> [AZURE.NOTE]Eine ausführliche Übersicht über den Data Factory-Dienst finden Sie im Artikel [Einführung in Azure Data Factory][data-factory-introduction].

##Voraussetzungen für das Lernprogramm
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement**. Wenn Sie über kein Abonnement verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Im Artikel [Kostenlose Testversion][azure-free-trial] finden Sie Details.
- **Azure Storage-Konto**. In diesem Lernprogramm verwenden Sie den Blobspeicher als **Quelldatenspeicher**. Wenn Sie kein Azure-Speicherkonto haben, finden Sie im Artikel [Erstellen eines Speicherkontos][data-factory-create-storage] Schritte zum Erstellen eines Azure-Speicherkontos.
- **Azure SQL-Datenbank**. In diesem Lernprogramm verwenden Sie eine Azure SQL-Datenbank als **Zieldatenspeicher**. Wenn Sie keine Azure SQL-Datenbank haben, die Sie in diesem Lernprogramm verwenden können, finden Sie unter [Erstellen und Konfigurieren einer Azure SQL-Datenbank][data-factory-create-sql-database] entsprechende Anweisungen.
- **SQL Server 2012/2014 oder Visual Studio 2013**. Sie verwenden SQL Server Management Studio oder Visual Studio zum Erstellen einer Beispieldatenbank und zum Anzeigen der Ergebnisdaten in der Datenbank.  

### Notieren des Kontonamens und -schlüssels Ihres Azure-Speicherkontos
Sie benötigen in diesem Lernprogramm den Kontonamen und -schlüssel Ihres Azure-Speicherkontos. Notieren Sie sich den **Kontonamen** und **Kontoschlüssel** Ihres Azure-Speicherkontos, indem Sie die folgenden Anweisungen befolgen:

1. Melden Sie sich beim [Azure-Vorschauportal][azure-preview-portal] an.
2. Klicken Sie auf der linken Seite auf den Hub **Durchsuchen**, und wählen Sie **Speicherkonten** aus.
3. Wählen Sie auf dem Blatt **Speicherkonten** das **Azure-Speicherkonto** aus, das Sie in diesem Lernprogramm verwenden möchten.
4. Klicken Sie auf dem Blatt **SPEICHER** auf die Kachel **SCHLÜSSEL**.
5. Klicken Sie auf dem Blatt **Schlüssel verwalten** auf die (Bild-) Schaltfläche **Kopieren** neben dem Textfeld **SPEICHERKONTONAME**, und speichern Sie den Eintrag z. B. in einer Textdatei.  
6. Wiederholen Sie den vorherigen Schritt zum Kopieren oder Notieren des **PRIMÄREN ZUGRIFFSSCHLÜSSELS**.
7. Schließen Sie alle Blätter, indem Sie auf **X** klicken.

### Notieren des Servernamens, Datenbanknamens und Benutzerkontos Ihrer Azure SQL-Datenbank
Sie benötigen in diesem Lernprogramm die Namen von Azure SQL-Server, -Datenbank und -Benutzer. Notieren Sie den Namen des **Servers**, der **Datenbank** und des **Benutzers** Ihrer Azure SQL-Datenbank, indem Sie die folgenden Anweisungen befolgen:

1. Klicken Sie links im **Azure-Vorschauportal** auf **Durchsuchen**, und wählen Sie **SQL-Datenbanken** aus.
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

1. Öffnen Sie den Editor, fügen Sie den folgenden Text ein, und speichern Sie die Datei als **emp.txt** im Ordner **C:\ADFGetStarted** auf Ihrer Festplatte.

        John, Doe
		Jane, Doe

2. Verwenden Sie Tools wie [Azure Storage-Explorer](https://azurestorageexplorer.codeplex.com/) zum Erstellen des Containers **adftutorial** und zum Hochladen der Datei **emp.txt** in den Container.

    ![Azure-Speicher-Explorer][image-data-factory-get-started-storage-explorer]
3. Verwenden Sie das folgende SQL-Skript, um die Tabelle **emp** in der Azure SQL-Datenbank zu erstellen.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Wenn Sie SQL Server 2012/2014 auf Ihrem Computer installiert haben:** Befolgen Sie die Anweisungen unter "Schritt 2: Herstellen einer Verbindung mit SQL-Datenbank" im Artikel [Verwalten einer Azure SQL-Datenbank mit SQL Server Management Studio][sql-management-studio], um eine Verbindung mit Ihrem Azure SQL-Server herzustellen und das SQL-Skript auszuführen. Beachten Sie, dass in diesem Artikel das Versionsverwaltungsportal (http://manage.windowsazure.com)) und nicht das Vorschauportal (http://portal.azure.com)) verwendet wird, um die Firewall für einen Azure SQL-Server zu konfigurieren.

	**Wenn Sie Visual Studio 2013 auf Ihrem Computer installiert haben:** Klicken Sie im [Azure-Vorschauportal](http://portal.azure.com) links auf **Durchsuchen**, dann auf **SQL-Server**, wählen Sie Ihre Datenbank aus, und klicken Sie auf der Symbolleiste auf die Schaltfläche **In Visual Studio öffnen**, um eine Verbindung mit Ihrem Azure SQL-Server herzustellen und das SQL-Skript auszuführen. Wenn Ihr Client nicht auf den Azure SQL-Server zugreifen darf, müssen Sie die Firewall für Ihren Azure SQL-Server so konfigurieren, dass der Zugriff über Ihren Computer (bzw. dessen IP-Adresse) ermöglicht wird. Im vorherigen Artikel finden Sie Schritte zum Konfigurieren der Firewall für Ihren Azure SQL-Server.


Gehen Sie wie folgt vor:

- Klicken Sie oben auf den Link [Data Factory-Editor verwenden](data-factory-get-started-using-editor.md), um das Lernprogramm mithilfe des Data Factory-Editors auszuführen, der Teil des Azure-Portals ist.
- Klicken Sie oben auf den Link [PowerShell verwenden](data-factory-monitor-manage-using-powershell.md), um das Lernprogramm mit Azure PowerShell auszuführen.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
 

<!---HONumber=62-->