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

Das Lernprogramm in diesem Artikel hilft Ihnen, schnell mit der Service Factory der Azure-Daten beginnen. In diesem Lernprogramm Sie erstellen eine Factory für Azure-Daten und erstellen Sie eine Pipeline in der Factory Daten zum Kopieren von Daten aus einem Azure-Blob-Speicher mit einer Azure SQL-Datenbank.

> [AZURE.NOTE]Einen ausführlichen Überblick über die Service Factory von Daten finden Sie unter der [Einführung in Azure Data Factory][data-factory-introduction] Artikel.

##Voraussetzungen für das Lernprogramm
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie in wenigen Minuten ein kostenloses Testkonto erstellen. Siehe die [kostenlose Testversion][azure-free-trial] Artikel.
- **Speicherkonto**. Verwenden Sie den Blob-Speicher als eine **Quelle** Datenspeicher in diesem Lernprogramm. Wenn Sie Azure-Speicherkonten, finden Sie unter besitzen die [erstellen ein Speicherkonto][data-factory-create-storage] Artikel die Schritte zu erstellen.
- **Azure SQL-Datenbank**. Verwenden Sie eine Azure SQL-Datenbank als eine **Ziel** Speichern von Daten in diesem Lernprogramm. Wenn Sie mit eine Azure SQL-Datenbank verfügen, mit denen Sie in diesem Lernprogramm finden Sie unter [zum Erstellen und Konfigurieren einer Azure SQL-Datenbank][data-factory-create-sql-database] zu erstellen.
- **SQL Server 2012/2014 oder Visual Studio 2013**. Sie verwenden SQL Server Management Studio oder Visual Studio zum Erstellen einer Beispieldatenbank und zum Anzeigen der Daten in der Datenbank.  

### Sammeln von Kontonamen und Kontoschlüssel für Azure-Speicherkonto
Sie benötigen den Kontonamen und Kontoschlüssel von Azure-Speicherkonto in diesem Lernprogramm zu tun. Notieren Sie sich die **Kontoname** und **Kontoschlüssel** für Azure-Speicherkonto anhand der nachfolgenden Anweisungen:

1. Melden Sie sich die [Azure Preview Portal][azure-preview-portal].
2. Klicken Sie auf **Durchsuchen** -Hub auf der linken Seite und wählen Sie **Speicherkonten**.
3. In der **Speicherkonten** Blade, wählen Sie die **Speicherkonto** die Sie in diesem Lernprogramm verwenden möchten.
4. In der **Speicher** Blade, klicken Sie auf **Schlüssel** Kachel.
5. In der **Schlüssel verwalten** Blade, klicken Sie auf **Kopie** (Bild) Schaltfläche neben **SPEICHERKONTONAME** Text Feld und speichern und Einfügen es irgendwo (z. B.: in einer Textdatei).  
6. Wiederholen Sie den vorherigen Schritt kopieren oder notieren Sie sich die **PRIMARY ACCESS KEY**.
7. Schließen Sie alle Blades, indem Sie auf **X**.

### Sammeln von Servername, Datenbankname und Benutzerkonto für Azure SQL-Datenbank
Sie benötigen die Namen der Azure SQL Server-, Datenbank- und Benutzer in diesem Lernprogramm. Notieren Sie den Namen der **Server**, **Datenbank**, und **Benutzer** für Azure SQL-Datenbank anhand der nachfolgenden Anweisungen:

1. In der **Azure Preview Portal**, klicken Sie auf **Durchsuchen** auf der linken Seite und wählen Sie **SQL-Datenbanken**.
2. In der **SQL-Datenbanken Blade**, wählen Sie die **Datenbank** die Sie in diesem Lernprogramm verwenden möchten. Notieren Sie sich die **Datenbankname**.  
3. In der **SQL-Datenbank** Blade, klicken Sie auf **Eigenschaften** Kachel.
4. Notieren Sie sich die Werte für **Servername** und **SERVER ADMIN-Benutzername**.
5. Schließen Sie alle Blades, indem Sie auf **X**.

### Können Sie Azure-Diensten auf den Azure SQL-Server zugreifen
Sicherstellen, dass **ermöglichen den Zugriff auf Azure Services** Einstellung aktiviert **auf** für Ihren Azure SQL-Server, damit der Dienst Daten Factory Ihre Azure SQL-Server zugreifen kann. Um zu überprüfen, und diese Einstellung aktivieren, führen Sie folgende Schritte aus:

1. Klicken Sie auf **Durchsuchen** Hub auf der linken Seite und auf **SQL Server**.
2. Wählen Sie **Ihr Server**, und klicken Sie auf **Einstellungen** auf der **SQL SERVER** Blade.
3. In der **Einstellungen** Blade, klicken Sie auf **Firewall**.
4. In der **-Firewall-Einstellungen** Blade, klicken Sie auf **auf** für **ermöglichen den Zugriff auf Azure Services**.
5. Schließen Sie alle Blades, indem Sie auf **X**.

### Vorbereiten von Azure Blob-Speicher und Azure SQL-Datenbank für das Lernprogramm
Jetzt bereiten Sie Ihrer Azure-Blob-Speicher und Azure SQL-Datenbank für das Lernprogramm durch die folgenden Schritte ausführen vor:

1. Starten Sie den Editor, fügen Sie den folgenden Text, und speichern Sie sie als **emp.txt**  **C:\ADFGetStarted** Ordner auf Ihrer Festplatte.

        John, Doe
		Jane, Doe

2. Verwenden Sie Tools wie z. B. [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) zum Erstellen der **Adftutorial** Container und zum Hochladen der **emp.txt** Datei auf den Container.

    ![Azure-Speicher-Explorer][image-data-factory-get-started-storage-explorer]
3. Verwenden Sie das folgende SQL-Skript zum Erstellen der **emp** Tabelle in Azure SQL-Datenbank.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Wenn Sie SQL Server 2012/2014 auf Ihrem Computer installiert haben:** Anweisungen aus [Schritt2: Herstellen einer Verbindung mit SQL-Datenbank Verwalten von Azure SQL-Datenbank mit SQL Server Management Studio][sql-management-studio] Artikel Ihre Azure SQL-Server herstellen, und führen Sie das SQL-Skript. Beachten Sie, dass dieser Artikel das Release Management Portal verwendet (http://manage.windowsazure.com), nicht die vorschauportal (http://portal.azure.com), um die Firewall für eine Azure SQL-Server zu konfigurieren.

	**Wenn Sie Visual Studio 2013 auf Ihrem Computer installiert haben:** in den [Azure Preview Portal](http://portal.azure.com), klicken Sie auf **Durchsuchen** -Hub auf Links auf **SQL Server**, wählen Sie die Datenbank, und klicken Sie auf **in Visual Studio geöffnet** Schaltfläche auf der Symbolleiste der Azure SQL-Server herstellen, und führen Sie das Skript. Wenn der Client Zugriff auf den Azure SQL-Server nicht zulässig ist, müssen Sie die Firewall für den Azure SQL-Server ermöglicht den Zugriff auf Ihren Computer (IP-Adresse) konfigurieren. Finden Sie im Artikel über die Schritte zum Konfigurieren der Firewall für Ihren Azure SQL-Server aus.


Gehen Sie wie folgt vor:

- Klicken Sie auf [Data Factory-Editor mithilfe von](data-factory-get-started-using-editor.md) Link oben, um das Lernprogramm ausführen, indem Sie mit Factory, die Teil der Azure-Verwaltungsportal ist.
- Klicken Sie auf [mithilfe von PowerShell](data-factory-monitor-manage-using-powershell.md) Link oben, um das Lernprogramm mit Azure PowerShell ausführen.


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
[data-factory-create-sql-database]: sql-database-create-configure.md


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

<!---HONumber=GIT-SubDir-->