<properties 
   pageTitle="Verwenden des .NET SDK für Data Lake-Speicher zur Anwendungsentwicklung | Azure" 
   description="Verwenden des .NET SDK für Azure Data Lake-Speicher zur Anwendungsentwicklung | Azure" 
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
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Erste Schritte mit Azure Data Lake-Speicher mithilfe des .NET SDK

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Erfahren Sie, wie Sie mithilfe des .NET SDK für Azure Data Lake-Speicher ein Azure Data Lake-Konto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen über Data Lake finden Sie unter [Übersicht über Azure Data Lake-Speicher](data-lake-store-overview.md).

## Voraussetzungen

* Visual Studio 2013 oder 2015 Die folgenden Anweisungen verwenden Visual Studio 2015.
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Aktiviertes Azure-Abonnement** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup).

## Erstellen einer .NET-Anwendung

1. Öffnen Sie Visual Studio, und erstellen Sie eine Konsolenanwendung.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Unter **Neues Projekt** können Sie die folgenden Werte eingeben bzw. auswählen:

	| Eigenschaft | Wert |
	|----------|-----------------------------|
	| Kategorie | Vorlagen/Visual C#/Windows |
	| Vorlage | Konsolenanwendung |
	| Name | CreateADLApplication |

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Fügen Sie Ihrem Projekt die Nuget-Pakete hinzu.

	1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **NuGet-Pakete verwalten**.
	2. Stellen Sie auf der Registerkarte **Nuget-Paket-Manager** sicher, dass **Paketquelle** auf **nuget.org** festgelegt und das Kontrollkästchen **Include Prerelease** aktiviert ist.
	3. Suchen und installieren Sie folgende Pakete für den Data Lake-Speicher:
	
		* Microsoft.Azure.Management.DataLake.Store
		* Microsoft.Azure.Management.DataLake.StoreFileSystem
		* Microsoft.Azure.Management.DataLake.StoreUploader

		![Hinzufügen einer Nuget-Quelle](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Erstellen eines neuen Azure Data Lake-Kontos")

	4. Installieren Sie außerdem das Paket **Microsoft.Azure.Common.Authentication**. Auch hierbei handelt es sich um ein Vorabpaket („Prerelease“). Es wird für die Authentifizierung beim Azure Data Lake-Speicher benötigt.

		![Hinzufügen einer Nuget-Quelle](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Erstellen eines neuen Azure Data Lake-Kontos")

	4. Schließen Sie den **NuGet-Paket-Manager**.

7. Öffnen Sie die Datei **Program.cs**, und ersetzen Sie den vorhandenen Codeblock durch folgenden Code. Geben Sie außerdem Werte für die im Codeausschnitt vorhandenen Parameter an.

	Dieser Code erstellt zunächst einen Data Lake-Speicher, erstellt dort Ordner, lädt Dateien hoch und dann wieder herunter, und löscht schließlich das Konto. Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) herunterladen.
	
		using System;
		using System.Collections.Generic;
		using System.Linq;
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
		
		
		namespace CreateADLApplication
		{
		    class CreateADLApplication
		    {
		        private static DataLakeStoreManagementClient _dataLakeStoreClient;
		        private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
		        private const string ResourceGroupName = "<resource_grp_name>"; //THIS SHOULD ALREADY EXIST
		
		        static void Main(string[] args)
		        {
		            var subscriptionId = new Guid("<subscription_ID>");
		            var _credentials = GetAccessToken();
		            string dataLakeAccountName = "<data_lake_store_name>";
		            string location = "East US 2";
		
		            _credentials = GetCloudCredentials(_credentials, subscriptionId);
		            _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
		            _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
		
		            var parameters = new DataLakeStoreAccountCreateOrUpdateParameters();
		            parameters.DataLakeStoreAccount = new DataLakeStoreAccount
		            {
		                Name = dataLakeAccountName,
		                Location = location
		            };
		
		            // Create a Data Lake Store account
		            Console.WriteLine("Creating an Azure Data Lake Store account ...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, parameters);
		
		            Console.WriteLine("Account created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Create a directory called MYTEMPDIR in the store
		            Console.WriteLine("Creating a directory under the Azure Data Lake Store account");
		            CreateDir(_dataLakeStoreFileSystemClient, "/mytempdir", dataLakeAccountName, "777");
		            Console.WriteLine("Directory created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Upload a file under MYTEMPDIR
		            Console.WriteLine("Uploading a file to the Azure Data Lake Store account");
		            bool force = false; //Set this to true if you want to overwrite existing data
		            UploadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, "C:\\users\\nitinme\\desktop\\vehicle1_09142014.csv", "/mytempdir/vehicle1_09142014.csv", force);
		            Console.WriteLine("File uploaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // List the files in the Data Lake Store
		            Console.WriteLine("Listing all files in the Azure Data Lake Store account");
		            var fileList = ListItems(_dataLakeStoreFileSystemClient, dataLakeAccountName, "/mytempdir");
		            var fileMenuItems = fileList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix)).ToList();
		            foreach (var filename in fileMenuItems)
		            {
		                Console.WriteLine(filename);
		
		            }
		            Console.WriteLine("Files listed. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Download the files from the Data Lake Store
		            Console.WriteLine("Downloading files from an Azure Data Lake Store account");
		            DownloadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, "/mytempdir/vehicle1_09142014.csv", "C:\\users\\nitinme\\desktop\\vehicle1_09142014_copy.csv", force);
		            Console.WriteLine("Files downloaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Delete the Data Lake Store account
		            Console.WriteLine("Azure Data Lake Store account will be deleted. Press ENTER to continue...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Delete(ResourceGroupName, dataLakeAccountName);
		            Console.ReadLine();
		            Console.WriteLine("Account deleted. Press ENTER to exit...");
		            Console.ReadLine();
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
		
		        public static bool CreateDir(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string path, string permission)
		        {
		            dataLakeStoreFileSystemClient.FileSystem.Mkdirs(path, dlAccountName, permission);
		            return true;
		        }
		
		        public static bool UploadFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string srcPath, string destPath, bool force = false)
		        {
		            var parameters = new UploadParameters(srcPath, destPath, dlAccountName, isOverwrite: true);
		            var frontend = new DataLakeStoreFrontEndAdapter(dlAccountName, dataLakeStoreFileSystemClient);
		            var uploader = new DataLakeStoreUploader(parameters, frontend);
		            uploader.Execute();
		            return true;
		        }
		        
		        public static bool AppendToFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string path, string content)
		        {
		            var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
		            
		            dataLakeStoreFileSystemClient.FileSystem.DirectAppend(filePath, accountName, stream);
		            return true;
		        }
		
		        public static void DownloadFile(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient,
		        string dataLakeAccountName, string srcPath, string destPath, bool force)
		        {
		            var beginOpenResponse = dataLakeFileSystemClient.FileSystem.BeginOpen(srcPath, dataLakeAccountName,
		                new FileOpenParameters());
		            var openResponse = dataLakeFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
		            if (force || !File.Exists(destPath))
		                File.WriteAllBytes(destPath, openResponse.FileContents);
		        }
		
		        public static List<FileStatusProperties> ListItems(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient, string dataLakeAccountName, string path)
		        {
		            var response = dataLakeFileSystemClient.FileSystem.ListFileStatus(path, dataLakeAccountName, new DataLakeStoreFileSystemListParameters());
		            return response.FileStatuses.FileStatus.ToList();
		        }
		    }
		}


8. Erstellen Sie die Anwendung, und führen Sie sie aus. Befolgen Sie die Anweisungen zum Ausführen und Fertigstellen der Anwendung.

## Weitere Methoden zum Erstellen eines Data Lake-Speicherkontos

- [Erste Schritte mit Data Lake-Speicher mithilfe des Portals](data-lake-store-get-started-portal.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe von PowerShell](data-lake-store-get-started-powershell.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe von Azure CLI](data-lake-store-get-started-cli.md)


## Nächste Schritte

- [Sichern von Daten in Data Lake-Speicher](data-lake-store-secure-data.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0128_2016-->