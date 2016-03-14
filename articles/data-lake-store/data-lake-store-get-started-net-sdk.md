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
   ms.date="02/29/2016"
   ms.author="nitinme"/>

# Erste Schritte mit Azure Data Lake-Speicher mithilfe des .NET SDK

> [AZURE.SELECTOR]
- [Verwenden des Portals](data-lake-store-get-started-portal.md)
- [Mithilfe von PowerShell](data-lake-store-get-started-powershell.md)
- [Verwenden des .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli.md)
- [Verwenden von Node.js](data-lake-store-manage-use-nodejs.md)

Erfahren Sie, wie Sie mithilfe des .NET SDK für Azure Data Lake-Speicher ein Azure Data Lake-Konto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen über Data Lake finden Sie unter [Übersicht über Azure Data Lake-Speicher](data-lake-store-overview.md).

## Voraussetzungen

* Visual Studio 2013 oder 2015 Die folgenden Anweisungen verwenden Visual Studio 2015.
* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Aktiviertes Azure-Abonnement** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup).
* Erstellen Sie eine AAD-Anwendung (Azure Active Directory), und rufen Sie deren **Client-ID** und den **Antwort-URI** ab. Weitere Informationen über AAD-Anwendungen und Anweisungen zum Abrufen einer Client-ID finden Sie unter [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mithilfe des Portals](../resource-group-create-service-principal-portal.md). Der Antwort-URI steht auch über das Portal zur Verfügung, sobald Sie die Anwendung erstellt haben.

## Wie authentifiziere ich mich mithilfe von Azure Active Directory?

Der folgende Codeausschnitt stellt zwei Methoden für die Authentifizierung bereit:

* **Interaktiv**: Ein Benutzer meldet sich über die Anwendung an. Diese Option wird in der Methode `AuthenticateUser` im folgenden Codeausschnitt implementiert.

* **Nicht interaktiv**: Die Anwendung stellt eigene Anmeldeinformationen bereit. Diese Option wird in der Methode `AuthenticateAppliaction` im folgenden Codeausschnitt implementiert.

Auch wenn der folgende Codeausschnitt Methoden für beide Ansätze zur Verfügung stellt, wird in diesem Artikel die Methode `AuthenticateUser` verwendet. Für diese Methode müssen Sie die Client-ID und den Antwort-URI der AAD-Anwendung angeben. Im Link unter „Voraussetzungen“ finden Sie Anweisungen, wie Sie diese Informationen erhalten können.

>[AZURE.NOTE] Wenn Sie den Codeausschnitt ändern und stattdessen die Methode `AuthenticateApplication` verwenden möchten, müssen Sie zusätzlich zu Client-ID und Antwort-URI auch den Clientauthentifizierungsschlüssel als Eingabe für die Methode angeben. Im Artikel [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mithilfe des Portals](../resource-group-create-service-principal-portal.md) finden Sie auch Informationen zum Generieren und Abrufen des Clientauthentifizierungsschlüssels.



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
	
		* `Microsoft.Azure.Management.DataLake.Store`
		* `Microsoft.Azure.Management.DataLake.StoreUploader`

		![Hinzufügen einer Nuget-Quelle](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Erstellen eines neuen Azure Data Lake-Kontos")

	4. Installieren Sie außerdem das Paket `Microsoft.IdentityModel.Clients.ActiveDirectory` für die Authentifizierung in Azure Active Directory.

		![Hinzufügen einer Nuget-Quelle](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Erstellen eines neuen Azure Data Lake-Kontos")


	5. Schließen Sie den **NuGet-Paket-Manager**.

7. Öffnen Sie die Datei **Program.cs**, und ersetzen Sie den vorhandenen Codeblock durch folgenden Code. Geben Sie zudem die im Codeausschnitt genannten Werte für Parameter an, z. B. **\_adlsAccountName** und **\_resourceGroupName**, und ersetzen Sie die Platzhalter für **APPLICATION-CLIENT-ID**, **APPLICATION-REPLY-URI** und **SUBSCRIPTION-ID**.

	Dieser Code erstellt zunächst ein Konto im Data Lake-Speicher, erstellt dort Ordner, lädt Dateien hoch und dann wieder herunter und löscht schließlich das Konto. Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) herunterladen.
	
        using System;
        using System.IO;
        using System.Security;
        using System.Text;
        using System.Collections.Generic;
        using System.Linq;

        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value. This resource group should already exist.
                    _location = "East US 2";
                    
                    string localFolderPath = @"C:\local_path"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = @"C:\local_path\file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                    
                    // Authenticate the user
                    var tokenCreds = AuthenticateUser("common", "https://management.core.windows.net/",
                        "<APPLICATION-CLIENT-ID>", new Uri("https://<APPLICATION-REPLY-URI>")); // TODO: Replace bracketed values.
                    
                    SetupClients(tokenCreds, "<SUBSCRIPTION-ID>"); // TODO: Replace bracketed value.

                    // Run sample scenarios
                    WaitForNewline("Authenticated.", "Creating NEW account.");
                    CreateAccount();
                    WaitForNewline("Account created.", "Creating a directory.");

                    // Create a directory in the Data Lake Store
                    CreateDirectory(remoteFolderPath);
                    WaitForNewline("Directory created.", "Showing directory info.");

                    // Get info about the directory in the Data Lake Store
                    var itemInfo = GetItemInfo(remoteFolderPath);
                    Console.WriteLine("Type: " + itemInfo.Type);
                    Console.WriteLine("Last modified (UTC): " +
                                      new DateTime(1970, 1, 1, 0, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(
                                          itemInfo.ModificationTime.Value));
                    WaitForNewline("Directory info shown.", "Uploading a file.");

                    // Upload a file to the Data Lake Store
                    UploadFile(localFilePath, remoteFilePath);
                    WaitForNewline("File uploaded.", "Listing files and directories.");

                    // List the files in the Data Lake Store
                    var itemList = ListItems(remoteFolderPath);
                    var fileMenuItems = itemList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix));
                    Console.WriteLine(String.Join("\r\n", fileMenuItems));
                    WaitForNewline("Files and directories listed.", "Appending content to a file.");

                    // Append to a file in the Data Lake Store
                    AppendToFile(remoteFilePath, "123");
                    WaitForNewline("Content appended.", "Downloading a file.");

                    // Download a file from the Data Lake Store
                    DownloadFile(remoteFilePath, localFilePath);
                    WaitForNewline("File downloaded.", "Deleting account.");

                    // Delete account
                    DeleteAccount();
                    WaitForNewline("Account deleted. You can now exit.");
                }

                // Helper function to show status and wait for user input
                public static void WaitForNewline(string reason, string nextAction = "")
                {
                    if (!String.IsNullOrWhiteSpace(nextAction))
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                        Console.WriteLine(nextAction);
                    }
                    else
                    {
                        Console.WriteLine(reason + "\r\nPress ENTER to continue...");
                        Console.ReadLine();
                    }
                }

                // Authenticate the user with AAD through an interactive popup.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/de-DE/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateUser(string tenantId, string resource, string appClientId, Uri appRedirectUri, string userId = "")
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);

                    var tokenAuthResult = authContext.AcquireToken(resource, appClientId, appRedirectUri,
                        PromptBehavior.Auto, UserIdentifier.AnyUser);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                // Authenticate the application with AAD through the application's secret key.
                // You need to have an application registered with AAD in order to authenticate.
                //   For more information and instructions on how to register your application with AAD, see: 
                //   https://azure.microsoft.com/de-DE/documentation/articles/resource-group-create-service-principal-portal/
                public static TokenCredentials AuthenticateApplication(string tenantId, string resource, string appClientId, Uri appRedirectUri, SecureString clientSecret)
                {
                    var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenantId);
                    var credential = new ClientCredential(appClientId, clientSecret);

                    var tokenAuthResult = authContext.AcquireToken(resource, credential);

                    return new TokenCredentials(tokenAuthResult.AccessToken);
                }

                //Set up clients
                public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
                {
                    _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
                    _adlsClient.SubscriptionId = subscriptionId;

                    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
                    _adlsFileSystemClient.SubscriptionId = subscriptionId;
                }

                // Create account
                public static void CreateAccount()
                {
                    // Create ADLS account
                    var adlsParameters = new DataLakeStoreAccount(location: _location);
                    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
                }

                // Delete account
                public static void DeleteAccount()
                {
                    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
                }

                // List all ADLS accounts within the subscription
                public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
                {
                    var response = _adlsClient.Account.List(_adlsAccountName);
                    var accounts = new List<DataLakeStoreAccount>(response);
                    
                    while (response.NextPageLink != null)
                    {
                        response = _adlsClient.Account.ListNext(response.NextPageLink);
                        accounts.AddRange(response);
                    }

                    return accounts;
                }

                // Upload a file
                public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
                {
                    var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                    var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                    var uploader = new DataLakeStoreUploader(parameters, frontend);
                    uploader.Execute();
                }

                // Concatenate files
                public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
                {
                    _adlsFileSystemClient.FileSystem.Concat(destFilePath, srcFilePaths, _adlsAccountName);
                }

                // Get file or directory info
                public static FileStatusProperties GetItemInfo(string path)
                {
                    return _adlsFileSystemClient.FileSystem.GetFileStatus(path, _adlsAccountName).FileStatus;
                }

                // List files and directories
                public static List<FileStatusProperties> ListItems(string directoryPath)
                {
                    return _adlsFileSystemClient.FileSystem.ListFileStatus(directoryPath, _adlsAccountName).FileStatuses.FileStatus.ToList();
                }

                // Download file
                public static void DownloadFile(string srcPath, string destPath)
                {
                    var stream = _adlsFileSystemClient.FileSystem.Open(srcPath, _adlsAccountName);
                    var fileStream = new FileStream(destPath, FileMode.Create);

                    stream.CopyTo(fileStream);
                    fileStream.Close();
                    stream.Close();
                }

                // Append to file
                public static void AppendToFile(string path, string content)
                {
                    var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

                    _adlsFileSystemClient.FileSystem.Append(path, stream, _adlsAccountName);
                }

                // Create a directory
                public static void CreateDirectory(string path)
                {
                    _adlsFileSystemClient.FileSystem.Mkdirs(path, _adlsAccountName);
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
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0302_2016-->