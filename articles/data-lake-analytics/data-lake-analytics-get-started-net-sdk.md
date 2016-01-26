<properties 
   pageTitle="Erste Schritte mit Azure Data Lake Analytics mithilfe des .NET SDK | Azure" 
   description="Enthält Informationen zur Verwendung des .NET SDK zum Erstellen von Data Lake-Speicherkonten und Data Lake Analytics-Aufträgen sowie zum Übermitteln von Aufträgen, die in U-SQL geschrieben sind." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/14/2016"
   ms.author="edmaca"/>

# Lernprogramm: Erste Schritte mit Azure Data Lake Analytics mithilfe des .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Enthält Informationen zum Verwenden des Azure .NET SDK zum Erstellen von Azure Data Lake Analytics-Konten, zum Definieren von Data Lake Analytics-Aufträgen in [U-SQL](data-lake-analytics-u-sql-get-started.md) und zum Übermitteln von Aufträgen an Data Lake Analytics-Konten. Weitere Informationen zu Data Lake Analytics finden Sie unter [Übersicht über Azure Data Lake Analytics](data-lake-analytics-overview.md).

In diesem Lernprogramm wird eine C#-Konsolenanwendung entwickelt, die ein U-SQL-Skript enthält, das eine durch Tabulatorzeichen getrennte Datei (TSV) einliest und sie in eine kommagetrennte Datei (CSV) umwandelt. Um das gleiche Lernprogramm unter Verwendung anderer unterstützter Tools zu durchlaufen, klicken Sie auf die Registerkarten oben in diesem Abschnitt.

**Der grundlegende Data Lake Analytics-Prozess:**

![Azure Data Lake Analytics-Prozessflussdiagramm](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Erstellen Sie ein Data Lake Analytics-Konto.
2. Vorbereiten der Quelldaten. Data Lake Analytics-Aufträge können Daten entweder von Azure Data Lake-Speicherkonten oder von Azure Blob-Speicherkonten lesen.   
3. Entwickeln Sie ein U-SQL-Skript.
4. Übermitteln Sie einen Auftrag (U-SQL-Skript) an das Data Lake Analytics-Konto. Für den Auftrag werden die Quelldaten gelesen, die Daten werden gemäß Anweisung im U-SQL-Skript verarbeitet, und anschließend wird die Ausgabe entweder in einem Data Lake-Speicherkonto oder einem BLOB-Speicherkonto gespeichert.

##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Visual Studio 2015, Visual Studio 2013 Update 4 oder Visual Studio 2012 mit Installation von Visual C++**.
- **Microsoft Azure SDK für .NET-Version 2.5 oder höher**. Führen Sie die Installation mit dem [Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) durch.
- **[Data Lake-Tools für Visual Studio](http://aka.ms/adltoolsvs)**. 
- **Data Lake Analytics-Konto**. Weitere Informationen finden Sie unter [Erstellen eines Azure Data Lake Analytics-Kontos](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

	Für die Data Lake-Tools wird das Erstellen von Data Lake Analytics-Konten nicht unterstützt. Sie müssen sie also mit dem Azure-Portal oder über Azure PowerShell, das .NET SDK oder die Azure-Befehlszeilenschnittstelle erstellen.

##Erstellen einer Konsolenanwendung

In diesem Tutorial verarbeiten Sie einige Suchprotokolle. Das Suchprotokoll kann entweder in einem Data Lake-Speicher oder einem Azure-BLOB-Speicher gespeichert werden.

Ein Beispielsuchprotokoll wurde in einen öffentlichen Azure-Blob-Container kopiert. In der Anwendung downloaden Sie die Datei auf Ihren Arbeitsplatzrechner und laden sie dann in Ihr Data Lake-Standardspeicherkonto hoch.

**So erstellen Sie eine Anwendung**

1. Öffnen Sie Visual Studio.
2. Erstellen Sie eine C#-Konsolenanwendung.
3. Öffnen Sie die Nuget-Paketverwaltungskonsole, und führen Sie folgende Befehle aus:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsCatalog -Pre
        Install-Package Microsoft.Azure.Management.DataLake.AnalyticsJob -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreFileSystem -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package WindowsAzure.Storage

4. Fügen Sie dem Projekt eine neue Datei mit dem Namen **SampleUSQLScript.txt** hinzu, und fügen Sie als Inhalt das folgende U-SQL-Skript ein. Die Data Lake Analytics-Aufträge werden in der Sprache U-SQL geschrieben. Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit der Sprache U-SQL](data-lake-analytics-u-sql-get-started.md) und in der [U-SQL language reference](http://go.microsoft.com/fwlink/?LinkId=691348) (in englischer Sprache).

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
    
    Im C#-Programm müssen Sie die Datei **/Samples/Data/SearchLog.tsv** und den Ordner **/Output/** vorbereiten.
	
	Es ist einfacher, für Dateien, die unter Data Lake-Standardkonten gespeichert sind, relative Pfade zu verwenden. Sie können aber auch absolute Pfade verwenden. Beispiel:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Sie müssen absolute Pfade verwenden, um auf Dateien in verknüpften Speicherkonten zuzugreifen. Die Syntax für Dateien, die unter dem verknüpften Azure-Speicherkonto gespeichert werden, lautet wie folgt:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Azure-BLOB-Container mit öffentlichen Blobs oder Zugriffsberechtigungen für öffentliche Container werden derzeit nicht unterstützt.
       
       
5. Fügen Sie in „Program.cs“ den folgenden Code ein:

        using System;
        using System.Security;
        using System.IO;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem;
        using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Common.Authentication.Factories;
        
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob;
        using Microsoft.Azure.Management.DataLake.AnalyticsJob.Models;
        
        using Microsoft.WindowsAzure.Storage.Blob;
        
        namespace data_lake_analytics_get_started
        {
            class Program
            {
        
                private const string AzureSubscriptionID = "<Your Azure Subscription ID>";
        
                private const string ResourceGroupName = "<Existing Azure Resource Group Name>"; //See the Get started using portal article
                private const string Location = "<Azure Data Center>";  //For example, EAST US 2
                private const string DataLakeStoreAccountName = "<Data Lake Store Account Name>"; // The application will create this account.
                private const string DataLakeAnalyticsAccountName = "<Data Lake Analytics Account Name>"; //The application will create this account.
        
                private const string LocalFolder = @"C:\tutorials\downloads\";  //local folder with write permission for file transferring.
        
                private static DataLakeStoreManagementClient _dataLakeStoreClient;
                private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
        
                private static DataLakeAnalyticsManagementClient _dataLakeAnalyticsClient;
                private static DataLakeAnalyticsJobManagementClient _dataLakeAnalyticsJobClient;
        
                static void Main(string[] args)
                {
                    var subscriptionId = new Guid(AzureSubscriptionID);
                    var _credentials = GetAccessToken();
        
                    _credentials = GetCloudCredentials(_credentials, subscriptionId);
                    _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
                    _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
                    _dataLakeAnalyticsClient = new DataLakeAnalyticsManagementClient(_credentials);
                    _dataLakeAnalyticsJobClient = new DataLakeAnalyticsJobManagementClient(_credentials);
        
                    //=========================
                    Console.WriteLine("Creating the Azure Data Lake Store account ...");
                    var storeAccountParameters = new DataLakeStoreAccountCreateOrUpdateParameters();
                    storeAccountParameters.DataLakeStoreAccount = new Microsoft.Azure.Management.DataLake.Store.Models.DataLakeStoreAccount
                    {
                        Name = DataLakeStoreAccountName,
                        Location = Location
                    };
                    _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, storeAccountParameters);
        
                    //=========================
                    Console.WriteLine("Preparing the source data file ...");
        
                    // Download the source file from a public Azure Blob container.
                    CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                    blob.DownloadToFile(LocalFolder + "SearchLog.tsv", System.IO.FileMode.Create);
        
                    // Create a folder in the default Data Lake Store account.
                    _dataLakeStoreFileSystemClient.FileSystem.Mkdirs("/Samples/Data/", DataLakeStoreAccountName, "777");
        
                    // Upload the source file to the default Data Lake Store account
                    var uploadParameters = new UploadParameters(LocalFolder + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv", DataLakeStoreAccountName, isOverwrite: true);
                    var uploader = new DataLakeStoreUploader(uploadParameters, new DataLakeStoreFrontEndAdapter(DataLakeStoreAccountName, _dataLakeStoreFileSystemClient));
                    uploader.Execute();
        
                    //=========================
                    Console.WriteLine("Creating the Data Lake Analytics account ...");
                    var analyticsAccountParameters = new DataLakeAnalyticsAccountCreateOrUpdateParameters();
                    analyticsAccountParameters.DataLakeAnalyticsAccount = new DataLakeAnalyticsAccount
                    {
                        Name = DataLakeAnalyticsAccountName,
                        Location = Location,
                        Properties = new DataLakeAnalyticsAccountProperties()
                    };
        
                    //create a DataLakeStoreAccount object, add it to the analytics client and then set it as the default ADL Store account
                    Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount storeAccountObject = new Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeStoreAccount();
                    storeAccountObject.Name = DataLakeStoreAccountName;
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DataLakeStoreAccounts.Add(storeAccountObject);
                    analyticsAccountParameters.DataLakeAnalyticsAccount.Properties.DefaultDataLakeStoreAccount = DataLakeStoreAccountName;
        
                    _dataLakeAnalyticsClient.DataLakeAnalyticsAccount.Create(ResourceGroupName, analyticsAccountParameters);
        
                    //=========================
                    Console.WriteLine("Submitting the job ...");
        
                    USqlProperties uSQLProperties = new USqlProperties
                    {
                        Type = JobType.USql,
                        Script = File.ReadAllText("SampleUSQLScript.txt")
                    };
        
                    JobInformation jobParameters = new JobInformation
                    {
                        JobId = Guid.NewGuid(),
                        Name = "myfirstdatalakeanalyticsjob",
                        Properties = uSQLProperties,
                        Type = JobType.USql,
                        DegreeOfParallelism = 1,
                        Priority = 1
                    };
        
                    _dataLakeAnalyticsJobClient.Jobs.Create(ResourceGroupName, DataLakeAnalyticsAccountName, new JobInfoBuildOrCreateParameters { Job = jobParameters });
        
                    Console.WriteLine(" Downloading results ...");
                    FileCreateOpenAndAppendResponse beginOpenResponse = _dataLakeStoreFileSystemClient.FileSystem.BeginOpen("/Output/SearchLog-from-Data-Lake.csv", DataLakeStoreAccountName, new FileOpenParameters());
                    FileOpenResponse openResponse = _dataLakeStoreFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
                    System.IO.File.WriteAllBytes(LocalFolder + "SearchLog-from-Data-Lake.csv", openResponse.FileContents);
        
                    Console.WriteLine("Done");
                }
        
                public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();
        
                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };
        
                    if (username != null && password != null)
                        account.Id = username;
        
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
                }
        
                public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

7. Drücken Sie **F5**, um die Anwendung auszuführen.

## Weitere Informationen

- Wenn Sie dasselbe Tutorial mit anderen Tools verwenden möchten, klicken Sie oben auf der Seite auf die Registerkartenauswahl.
- Eine komplexere Abfrage finden Sie unter [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Informationen zu den ersten Schritten der Entwicklung von U-SQL-Anwendungen finden Sie unter [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Weitere Informationen zu U-SQL finden Sie unter [Erste Schritte mit Azure Data Lake Analytics-U-SQL-Sprache](data-lake-analytics-u-sql-get-started.md) und in der [Referenz zur U-SQL-Sprache](http://go.microsoft.com/fwlink/?LinkId=691348).
- Informationen zu Verwaltungsaufgaben finden Sie unter [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md).
- Eine Übersicht über Data Lake Analytics finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0121_2016-->