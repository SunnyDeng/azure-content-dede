<properties 
    pageTitle="Erstellen und Exportieren eines BACPAC einer Azure SQL-Datenbank mit PowerShell" 
    description="Erstellen und Exportieren eines BACPAC einer Azure SQL-Datenbank mit PowerShell" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/23/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Erstellen und Exportieren eines BACPAC einer SQL-Datenbank mit PowerShell

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)


In diesem Artikel wird veranschaulicht, wie Sie ein „BACPAC“ Ihrer SQL-Datenbank mit PowerShell manuell exportieren.

Ein „BACPAC“ ist eine BACPAC-Datei, die ein Datenbankschema und Daten enthält. Weitere Informationen finden Sie unter „Sicherungspaket (.bacpac)“ in [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx).

> [AZURE.NOTE]Azure SQL-Datenbank erstellt für jede Benutzerdatenbank automatisch Sicherungen. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).

Das BACPAC wird in einen Azure-Speicherblobcontainer exportiert, den Sie nach dem erfolgreichen Abschluss des Vorgangs herunterladen können.


Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Azure SQL-Datenbank. Wenn Sie nicht über eine SQL-Datenbank verfügen, können Sie die Erstellung anhand der Schritte im folgenden Artikel durchführen: [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).
- Ein [Azure-Speicherkonto](storage-create-storage-account.md) mit einem BLOB-Container zum Speichern des BACPAC. Derzeit muss für das Speicherkonto das klassische Bereitstellungsmodell verwendet werden. Wählen Sie also **Klassisch**, wenn Sie ein Speicherkonto erstellen.
- Azure PowerShell. Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) ausführen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).



## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Starten Sie also PowerShell, und führen Sie dann das folgende Cmdlet aus. Geben Sie auf dem Anmeldebildschirm die E-Mail-Adresse und das Kennwort wie für die Anmeldung beim Azure-Portal ein.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Zur Auswahl des Abonnements benötigen Sie Ihre Abonnement-ID oder den Anmeldenamen für das Abonnement (**-SubscriptionName**). Sie können die Abonnement-ID aus den Informationen im vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen und mehr Details benötigen, können Sie auch das **Get-AzureSubscription**-Cmdlet ausführen und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Wenn Sie Ihre Abonnementinformationen haben, führen Sie das folgende Cmdlet aus:

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Nach dem erfolgreichen Ausführen von **Select-AzureSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück. Wenn Sie über mehrere Abonnements verfügen, können Sie **Get-AzureSubscription** ausführen und überprüfen, ob das gewünschte Abonnement den Wert **IsCurrent: True** aufweist.


## Einrichten der Variablen für Ihre jeweilige Umgebung

Es gibt einige Variablen, bei denen Sie die Beispielwerte durch die speziellen Werte für Ihre Datenbank und Ihr Speicherkonto ersetzen müssen.

Ersetzen Sie den Server- und Datenbanknamen durch den derzeitigen Server und die Datenbank in Ihrem Konto. Geben Sie als BLOB-Name den Namen der BACPAC-Datei ein, die erstellt werden soll. Geben Sie beliebigen Text ein, um die BACPAC-Datei zu benennen. Dabei ist nur die Erweiterung „.bacpac“ obligatorisch.

    $ServerName = "servername"
    $DatabaseName = "nameofdatabasetobackup"
    $BlobName = "filename.bacpac"

Navigieren Sie im [Azure-Vorschauportal](https://portal.azure.com) zu Ihrem Speicherkonto, um diese Werte zu erhalten. Sie können auf den primären Zugriffsschlüssel zugreifen, indem Sie auf dem Blatt Ihres Speicherkontos auf **Alle Einstellungen** und auf **Schlüssel** klicken.

    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"

## Erstellen eines Zeigers auf Ihren Server und Ihr Speicherkonto

Wenn Sie das **Get-Credential**-Cmdlet ausführen, werden Sie in einem Fenster zum Eingeben des Benutzernamens und Kennworts aufgefordert. Geben Sie den Administratorbenutzernamen und das dazugehörige Kennwort für Ihren SQL-Server ein (NICHT den Benutzernamen und das Kennwort für Ihr Azure-Konto).

    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential

    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx


## Exportieren der Datenbank

Mit diesem Befehl wird eine Anforderung zum Exportieren der Datenbank an den Dienst gesendet. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Exportvorgang abgeschlossen ist.

    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    

## Überwachen des Fortschritts des Exportvorgangs

Nach dem Ausführen von **Start-AzureSqlDatabaseExport** können Sie den Status der Anforderung überprüfen. Wenn die Ausführung sofort nach der Anforderung erfolgt, wird normalerweise **Status: Ausstehend** oder **Status: Wird ausgeführt** ausgegeben. Sie können die Ausführung also mehrmals durchführen, bis in der Ausgabe **Status: Abgeschlossen** angezeigt wird.

Beim Ausführen dieses Befehls werden Sie zur Eingabe eines Kennworts aufgefordert. Geben Sie das Administratorkennwort für Ihren SQL-Server ein.


    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Exportieren eines SQL-Datenbank-PowerShell-Skripts


    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ServerName = "servername"
    $DatabaseName = "databasename"
    $BlobName = "bacpacfilename"
    
    $StorageName = "storageaccountname"
    $ContainerName = "blobcontainername"
    $StorageKey = "primaryaccesskey"
    
    $credential = Get-Credential
    $SqlCtx = New-AzureSqlDatabaseServerContext -ServerName $ServerName -Credential $credential
    
    $StorageCtx = New-AzureStorageContext -StorageAccountName $StorageName -StorageAccountKey $StorageKey
    $Container = Get-AzureStorageContainer -Name $ContainerName -Context $StorageCtx
    
    $exportRequest = Start-AzureSqlDatabaseExport -SqlConnectionContext $SqlCtx -StorageContainer $Container -DatabaseName $DatabaseName -BlobName $BlobName
    
    Get-AzureSqlDatabaseImportExportStatus -RequestId $exportRequest.RequestGuid -ServerName $ServerName -Username $credential.UserName
    


## Nächste Schritte

- [Importieren einer Azure SQL-Datenbank](sql-database-import-powershell.md)


## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)
- [SQL-Datenbank-Dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO1-->