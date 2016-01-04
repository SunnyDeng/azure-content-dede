<properties 
   pageTitle="Erste Schritte mit Azure Data Lake Analytics mithilfe von Azure PowerShell | Azure" 
   description="Erfahren Sie, wie Sie Azure PowerShell zum Erstellen eines Data Lake-Speicherkontos, Erstellen eines Data Lake Analytics-Auftrags mit U-SQL und Senden des Auftrags verwenden." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# Lernprogramm: Erste Schritte mit Azure Data Lake Analytics mithilfe von Azure PowerShell

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Enthält Informationen zum Verwenden von Azure PowerShell zum Erstellen von Azure Data Lake Analytics-Konten, zum Definieren von Data Lake Analytics-Aufträgen in [U-SQL](data-lake-analytics-u-sql-get-started.md) und zum Übermitteln von Aufträgen an Data Lake Analytics-Konten. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

In diesem Tutorial entwickeln Sie einen Auftrag, bei dem eine Datei mit tabulatorgetrennten Werten (TSV) gelesen und in eine Datei mit kommagetrennten Werten (CSV) konvertiert wird. Um das gleiche Lernprogramm unter Verwendung anderer unterstützter Tools zu durchlaufen, klicken Sie auf die Registerkarten oben in diesem Abschnitt.

**Der grundlegende Data Lake Analytics-Prozess:**

![Azure Data Lake Analytics-Prozessflussdiagramm](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Erstellen Sie ein Data Lake Analytics-Konto.
2. Vorbereiten der Quelldaten. Data Lake Analytics-Aufträge können Daten entweder von Azure Data Lake-Speicherkonten oder von Azure Blob-Speicherkonten lesen.   
3. Entwickeln Sie ein U-SQL-Skript.
4. Übermitteln Sie einen Auftrag (U-SQL-Skript) an das Data Lake Analytics-Konto. Für den Auftrag werden die Quelldaten gelesen, die Daten werden gemäß Anweisung im U-SQL-Skript verarbeitet, und anschließend wird die Ausgabe entweder in einem Data Lake-Speicherkonto oder einem BLOB-Speicherkonto gespeichert.


**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren von Azure PowerShell 1.0 und höher](data-lake-analytics-manage-use-powershell.md#install-azure-powershell-10-and-greater).

##Erstellen eines Data Lake Analytics-Kontos

Zum Ausführen von Aufträgen ist ein Data Lake Analytics-Konto erforderlich. Zum Erstellen eines Data Lake Analytics-Kontos müssen Sie Folgendes angeben:

- **Azure-Ressourcengruppe**: Ein Data Lake Analytics-Konto muss in einer Azure-Ressourcengruppe erstellt werden. Mit dem [Azure-Ressourcen-Manager](resource-group-overview.md) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen.  

	So listen Sie die Ressourcengruppen in Ihrem Abonnement auf:
    
    	Get-AzureRmResourceGroup
    
	So erstellen Sie eine neue Ressourcengruppe:

    	New-AzureRmResourceGroup `
			-Name "<Your resource group name>" `
			-Location "<Azure Data Center>" # For example, "East US 2"

- **Name des Data Lake Analytics-Kontos**
- **Standort**: eines der Azure-Rechenzentren, die Data Lake Analytics unterstützen.
- **Data Lake-Standardkonto**: Jedes Data Lake Analytics-Konto verfügt über ein Data Lake-Standardkonto.

	So erstellen Sie ein neues Data Lake-Konto

	    New-AzureRmDataLakeStoreAccount `
	        -ResourceGroupName "<Your Azure resource group name>" `
	        -Name "<Your Data Lake account name>" `
	        -Location "<Azure Data Center>"  # For example, "East US 2"

	> [AZURE.NOTE]Der Data Lake-Kontoname darf nur Kleinbuchstaben und Zahlen enthalten.



**So erstellen Sie ein Data Lake Analytics-Konto**

1. Öffnen Sie PowerShell ISE auf der Windows-Arbeitsstation.
2. Führen Sie das folgende Skript aus:

		$resourceGroupName = "<ResourceGroupName>"
		$dataLakeStoreName = "<DataLakeAccountName>"
		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$location = "East US 2"
		
		Write-Host "Create a resource group ..." -ForegroundColor Green
		New-AzureRmResourceGroup `
			-Name  $resourceGroupName `
			-Location $location
		
		Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
		New-AzureRmDataLakeStoreAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeStoreName `
			-Location $location 
		
		Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
		New-AzureRmDataLakeAnalyticsAccount `
			-Name $dataLakeAnalyticsName `
			-ResourceGroupName $resourceGroupName `
			-Location $location `
			-DefaultDataLake $dataLakeStoreName
		
		Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
		Get-AzureRmDataLakeAnalyticsAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeAnalyticsName  

##Hochladen von Daten in Data Lake

In diesem Tutorial verarbeiten Sie einige Suchprotokolle. Das Suchprotokoll kann entweder in einem Data Lake-Speicher oder einem Azure-BLOB-Speicher gespeichert werden.

Eine Beispiel-Suchprotokolldatei wurde in einen öffentlichen Azure-BLOB-Container kopiert. Verwenden Sie das folgende PowerShell-Skript, um die Datei auf die Arbeitsstation herunterzuladen, und laden Sie dann die Datei in das Data Lake-Standardspeicherkonto Ihres Data Lake Analytics-Kontos.

	$dataLakeStoreName = "<The default Data Lake Store account name>"
	
	$localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
	$storageAccount = "adltutorials"  # Don't modify this value.
	$container = "adls-sample-data"  #Don't modify this value.

	# Create the temp location	
	New-Item -Path $localFolder -ItemType Directory -Force 

	# Download the sample file from Azure Blob storage
	$context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
	$blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
	$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

	# Upload the file to the default Data Lake Store account	
	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Das folgende PowerShell-Skript veranschaulicht das Abrufen des Data Lake-Standardspeichernamens für ein Data Lake Analytics-Konto:


	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE]Das Azure-Portal bietet eine Benutzeroberfläche zum Kopieren der Beispieldatendateien in das Data Lake-Standardspeicherkonto. Anweisungen finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Data Lake Analytics hat auch Zugriff auf den Azure-Blob-Speicher. Informationen zum Hochladen von Daten in den Azure-BLOB-Speicher finden Sie unter [Verwenden von Azure PowerShell mit Azure Storage](storage-powershell-guide-full.md).

##Übermitteln von Data Lake Analytics-Aufträgen

Die Data Lake Analytics-Aufträge werden in der Sprache U-SQL geschrieben. Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit der U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md) und in der [U-SQL Language Reference](http://go.microsoft.com/fwlink/?LinkId=691348) (in englischer Sprache).

**So erstellen Sie ein Skript für Data Lake Analytics-Aufträge**

- Erstellen Sie mit dem folgenden U-SQL-Skript eine Textdatei, und speichern Sie die Textdatei auf der Arbeitsstation:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Mit diesem U-SQL-Skript wird die Quelldatei mithilfe von **Extractors.Tsv()** gelesen, und anschließend wird eine CSV-Datei mithilfe von **Outputters.Csv()** erstellt.
    
    Ändern Sie die beiden Pfade nur, wenn Sie die Quelldatei an einen anderen Speicherort kopieren. Data Lake Analytics erstellt den Ausgabeordner, falls er nicht vorhanden ist.
	
	Es ist einfacher, für Dateien, die unter Data Lake-Standardkonten gespeichert sind, relative Pfade zu verwenden. Sie können aber auch absolute Pfade verwenden. Beispiel:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Sie müssen absolute Pfade verwenden, um auf Dateien in verknüpften Speicherkonten zuzugreifen. Die Syntax für Dateien, die unter dem verknüpften Azure-Speicherkonto gespeichert werden, lautet wie folgt:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Azure-BLOB-Container mit öffentlichen Blobs oder Zugriffsberechtigungen für öffentliche Container werden derzeit nicht unterstützt.
    
	
**So übermitteln Sie den Auftrag**

1. Öffnen Sie PowerShell ISE auf der Windows-Arbeitsstation.
2. Führen Sie das folgende Skript aus:

		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
		
		Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
		                
		While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
			Write-Host "Job status: "$t.State"..."
			Start-Sleep -seconds 5
		}
		
		Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

	Im Skript wird die U-SQL-Skriptdatei unter "c:\\tutorials\\data-lake-analytics\\copyFile.usql" gespeichert. Aktualisieren Sie den Dateipfad entsprechend.
 
Nachdem der Auftrag abgeschlossen wurde, können Sie die folgenden Cmdlets verwenden, um die Datei anzugeben und herunterzuladen:
	
	$resourceGroupName = "<Resource Group Name>"
	$dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
	$destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
	
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
	
	Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
	
	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## Weitere Informationen

- Wenn Sie dasselbe Tutorial mit anderen Tools verwenden möchten, klicken Sie oben auf der Seite auf die Registerkartenauswahl.
- Eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Informationen zu den ersten Schritten der Entwicklung von U-SQL-Anwendungen finden Sie unter [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Informationen zum Erlernen von U-SQL finden Sie unter [Erste Schritte mit der Sprache U-SQL für Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Informationen zu Verwaltungsaufgaben finden Sie unter [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md).
- Eine Übersicht über Data Lake Analytics finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_1203_2015-->