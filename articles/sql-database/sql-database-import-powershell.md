<properties 
    pageTitle="Importieren einer BACPAC-Datei in eine Azure SQL-­Datenbank mithilfe von PowerShell" 
    description="Importieren einer BACPAC-Datei in eine Azure SQL-­Datenbank mithilfe von PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jeffreyg" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="09/05/2015"
    ms.author="sstein"/>

# Importieren einer BACPAC-Datei in eine SQL-­Datenbank mithilfe von PowerShell

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)


In diesem Artikel wird das Erstellen einer SQL-Datenbank durch das Importieren einer BACPAC-Datei mit PowerShell veranschaulicht.

Ein „BACPAC“ ist eine BACPAC-Datei, die ein Datenbankschema und Daten enthält. Weitere Informationen finden Sie unter „Sicherungspaket (.bacpac)“ in [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx).

Die Datenbank wird aus einer BACPAC-Datei erstellt, die aus einem Azure-Speicherblobcontainer importiert wurde. Wenn im Azure-Speicher keine BACPAC-Datei vorhanden ist, können Sie eine erstellen, indem Sie in [Erstellen und Exportieren der BACPAC-Datei einer Azure SQL-Datenbank](sql-database-backup.md) beschriebenen Schritte ausführen.

> [AZURE.NOTE]Azure SQL-Datenbank erstellt und verwaltet automatisch Sicherungen für jede Benutzerdatenbank, die Sie wiederherstellen können. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).


Zum Importieren einer SQL-­Datenbank benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Eine BACPAC-Datei (.bacpac) der Datenbank, die Sie wiederherstellen möchten. Die BACPAC-Datei muss sich in einem [Azure Storage-Konto (klassisch)](storage-create-storage-account.md)-Blobcontainer befinden.
- Azure PowerShell. Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) ausführen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).



## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Starten Sie also PowerShell, und führen Sie dann das folgende Cmdlet aus. Geben Sie auf dem Anmeldebildschirm die E-Mail-Adresse und das Kennwort wie für die Anmeldung beim Azure-Portal ein.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zum Auswählen des Abonnements benötigen Sie Ihre Abonnement-ID. Sie können die Abonnement-ID aus den Informationen im vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen und mehr Details benötigen, können Sie auch das **Get-AzureSubscription**-Cmdlet ausführen und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Sobald Sie über Ihre Abonnement-ID verfügen, führen Sie das folgende Cmdlet aus:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Nach dem erfolgreichen Ausführen von **Select-AzureSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück. Wenn Sie über mehrere Abonnements verfügen, können Sie **Get-AzureSubscription** ausführen und überprüfen, ob das ausgewählte Abonnement den Wert **IsCurrent: True** aufweist.


## Die Variablen für Ihre Umgebung einrichten

Es gibt einige Variablen, bei denen Sie die Beispielwerte durch die speziellen Werte für Ihre Datenbank und Ihr Speicherkonto ersetzen müssen.

Der Servername muss ein Server sein, der derzeit in dem im vorherigen Schritt ausgewählten Abonnement vorhanden ist, und auf dem Sie die Datenbank erstellen möchten.

Der Datenbankname ist der gewünschte Name für die neue Datenbank.

    $ServerName = "servername"
    $DatabaseName = "databasename"


Die folgenden Variablen stammen aus dem Speicherkonto, in dem sich die BACPAC-Datei befindet. Navigieren Sie im [Azure-Vorschauportal](https://portal.azure.com) zu Ihrem Speicherkonto, um diese Werte zu erhalten. Sie können auf den primären Zugriffsschlüssel zugreifen, indem Sie auf dem Blatt Ihres Speicherkontos auf **Alle Einstellungen** und dann auf **Schlüssel** klicken.

Der Blobname ist der Name einer vorhandenen BACPAC-Datei, aus der Sie die Datenbank erstellen möchten. Sie müssen die Erweiterung „.bacpac“ angeben.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"

## Erstellen eines Zeigers auf Ihren Server und Ihr Speicherkonto

Wenn Sie das **Get-Credential**-Cmdlet ausführen, werden Sie in einem Fenster zum Eingeben des Benutzernamens und Kennworts aufgefordert. Geben Sie den Admin-Benutzernamen und das Kennwort für den SQL Server ein, auf dem Sie die Datenbank erstellen möchten ($ServerName von oben), nicht den Benutzernamen und das Kennwort für Ihr Azure-Konto.

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Importieren der Datenbank

Mit diesem Befehl wird eine Anforderung zum Importieren der Datenbank an den Dienst gesendet. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Importvorgang abgeschlossen ist.

    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Überwachen des Fortschritts des Vorgangs

Nach dem Ausführen von **Start-AzureSqlDatabaseImport** können Sie den Status der Anforderung überprüfen.

Eine Statusüberprüfung direkt nach der Anforderung gibt in der Regel den Status **Ausstehend** oder **Wird ausgeführt** zurück. Außerdem wird der Fortschritt in Prozent angegeben, d. h. Sie können die Überprüfung mehrmals durchführen, bis in der Ausgabe **Status: Abgeschlossen** angezeigt wird.

Beim Ausführen dieses Befehls werden Sie zur Eingabe eines Kennworts aufgefordert. Geben Sie den Administratorbenutzernamen und das -kennwort für Ihren SQL Server ein.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
 


## SQL-Datenbank-PowerShell-Skript zur Wiederherstellung


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "nameofnewdatabase"

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $BlobName = "filename.bacpac"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $ImportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $ImportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    

## Nächste Schritte

- [Herstellen einer Verbindung mit SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)




## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)
- [SQL-Datenbankdokumentation](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Sept15_HO2-->