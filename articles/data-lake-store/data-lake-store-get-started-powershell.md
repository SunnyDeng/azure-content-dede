<properties 
   pageTitle="Erste Schritte mit Data Lake-Speicher | Azure" 
   description="Verwenden von Azure PowerShell zum Erstellen eines Data Lake-Speicherkontos und Ausführen grundlegender Vorgänge" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/04/2015"
   ms.author="nitinme"/>

# Erste Schritte mit Azure Data Lake-Speicher mithilfe von Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)

Erfahren Sie, wie Sie mit Azure PowerShell ein Azure Data Lake-Speicherkonto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen zu Data Lake-Speicher finden Sie unter [Übersicht über Data Lake-Speicher](data-lake-store-overview.md).

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivieren Ihres Azure-Abonnements** für die öffentliche Vorschauversion von Data Lake-Speicher. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup).


##Installieren von Azure PowerShell 1.0 und höher

Zunächst müssen Sie die 0.9x-Versionen von Azure PowerShell deinstallieren. Um die installierte PowerShell-Version zu überprüfen, führen Sie den folgenden Befehl in einem PowerShell-Fenster aus:

	Get-Module *azure*
	
Um die ältere Version zu deinstallieren, führen Sie **Programme und Funktionen** in der Systemsteuerung aus und entfernen die installierte Version, sofern diese niedriger ist als PowerShell 1.0.

Es gibt zwei Hauptoptionen für die Installation von Azure PowerShell.

- [PowerShell-Katalog](https://www.powershellgallery.com/). Führen Sie die folgenden Befehle in einer erweiterten PowerShell ISE oder einer Windows PowerShell-Konsole mit erhöhten Rechten aus:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Weitere Informationen finden Sie unter [PowerShell Gallery](https://www.powershellgallery.com/) (in englischer Sprache).

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps) (in englischer Sprache). Falls Sie Azure PowerShell 0.9.x installiert haben, werden Sie aufgefordert, diese Version zu deinstallieren. Wenn Sie Azure PowerShell-Module aus dem PowerShell-Katalog installiert haben, erfordert das Installationsprogramm, dass diese Module vor der Installation deinstalliert werden, um eine konsistente Azure PowerShell-Umgebung herzustellen. Anweisungen hierzu finden Sie unter [Installing Azure PowerShell 1.0 via WebPI](https://azure.microsoft.com/blog/azps-1-0/) (in englischer Sprache).

WebPI wird monatlich aktualisiert. Der PowerShell-Katalog wird fortlaufend aktualisiert. Wenn Sie mit der Installation aus dem PowerShell-Katalog erst einmal vertraut sind, wird der Katalog sicherlich Ihre erste Anlaufstelle für die neusten und beliebtesten Cmdlets in Azure PowerShell.

## Erstellen eines Azure Data Lake-Speicherkontos

1. Öffnen Sie vom Desktop aus ein neues Azure PowerShell-Fenster, und geben Sie den folgenden Ausschnitt ein, um sich bei Ihrem Azure-Konto anzumelden, das Abonnement einzurichten und den Data Lake-Speicheranbieter zu registrieren. Stellen Sie bei der Aufforderung zum Anmelden sicher, dass Sie sich als einer der Administratoren bzw. Besitzer des Abonnements anmelden:

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>
        
		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore" 


2. Ein Azure Data Lake-Speicherkonto wird einer Azure-Ressourcengruppe zugeordnet. Erstellen Sie zunächst eine Azure-Ressourcengruppe.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Erstellen einer Azure-Ressourcengruppe](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Erstellen einer Azure-Ressourcengruppe")

2. Erstellen Sie ein Azure Data Lake-Speicherkonto. Der angegebene Name darf nur Kleinbuchstaben und Zahlen enthalten.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Erstellen eines Azure Data Lake-Speicherkontos](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Erstellen eines Azure Data Lake-Speicherkontos")

3. Stellen Sie sicher, dass das Konto erfolgreich erstellt wurde.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	Die Ausgabe sollte **True** lauten.

## Erstellen von Verzeichnisstrukturen im Azure Data Lake-Speicher

Sie können in Ihrem Azure Data Lake-Speicherkonto Verzeichnisse zum Verwalten und Speichern von Daten erstellen.

1. Legen Sie ein Stammverzeichnis fest.

		$myrootdir = "/"

2. Erstellen Sie ein neues Verzeichnis namens **mynewdirectory** unter dem festgelegten Stammverzeichnis.

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Stellen Sie sicher, dass das neue Verzeichnis erfolgreich erstellt wurde.

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	Eine Ausgabe ähnlich der folgenden sollte angezeigt werden:

	![Überprüfen des Verzeichnisses](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Überprüfen des Verzeichnisses")


## Hochladen von Daten in den Azure Data Lake-Speicher

Sie können Ihre Daten direkt auf die Stammebene eines Data Lake-Speichers oder in ein im Konto erstelltes Verzeichnis hochladen. Die folgenden Codeausschnitte veranschaulichen das Hochladen von Beispieldaten in das im vorigen Abschnitt erstellte Verzeichnis (**mynewdirectory**).

Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData) herunterladen. Laden Sie die Datei herunter, und speichern Sie sie in ein lokales Verzeichnis auf dem Computer, z. B. „C:\\sampledata“.

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## Umbenennen, Herunterladen und Löschen von Daten im Data Lake-Speicher

Verwenden Sie zum Umbenennen einer Datei den folgenden Befehl:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Verwenden Sie zum Downloaden einer Datei den folgenden Befehl:

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Verwenden Sie zum Löschen einer Datei den folgenden Befehl:

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv 
	
Geben Sie nach entsprechender Aufforderung **Y** ein, um das Element zu löschen. Wenn mehrere Dateien gelöscht werden sollen, können Sie die betreffenden Pfade durch Kommas getrennt bereitstellen.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Löschen eines Azure Data Lake-Speicherkontos

Verwenden Sie den folgenden Befehl zum Löschen Ihres Data Lake-Speicherkontos.

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Geben Sie nach entsprechender Aufforderung **Y** ein, um das Konto zu löschen.


## Weitere Methoden zum Erstellen eines Data Lake-Speicherkontos

- [Erste Schritte mit Data Lake-Speicher mithilfe des Portals](data-lake-store-get-started-portal.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe des .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe von Azure CLI](data-lake-store-get-started-cli.md)


## Nächste Schritte

- [Sichern von Daten in Data Lake-Speicher](data-lake-store-secure-data.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_1210_2015-->