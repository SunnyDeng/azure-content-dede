<properties 
   pageTitle="Erstellen einer Azure SQL-Datenbank mit C#" 
   description="In diesem Artikel wird gezeigt, wie Sie eine Azure SQL-Datenbank mithilfe von C# und der Azure SQL-Datenbankbibliothek für .NET erstellen." 
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
   ms.date="09/01/2015"
   ms.author="sstein"/>

# Erstellen einer SQL-Datenbank mit C&#x23;

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Dieser Artikel enthält Befehle zum Erstellen einer Azure SQL-Datenbank mithilfe von C# und der [Azure SQL-Datenbankbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

In diesem Artikel wird erläutert, wie Sie eine Einzeldatenbank erstellen. Informationen zum Erstellen elastischer Datenbanken finden Sie unter [Erstellen eines Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md).

Aus Gründen der Übersichtlichkeit beschränken sich die Beispiele auf einzelne Codeausschnitte. In dem Abschnitt am Ende dieses Artikels sind alle Befehle in einer Beispielkonsolenanwendung zusammengeführt.

Die Azure SQL-Datenbankbibliothek für .NET bietet eine [Azure-Ressourcen-Manager](resource-group-overview.md)-basierte API, die die [Ressourcen-Manager-basierte REST-API für die SQL-Datenbank](https://msdn.microsoft.com/library/azure/mt163571.aspx) umfasst. Diese Clientbibliothek folgt dem allgemeinen Muster für Ressourcen-Manager-basierte Clientbibliotheken. Der Ressourcen-Manager erfordert Ressourcengruppen und die Authentifizierung mit [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]Die SQL-Datenbankbibliothek für .NET befindet sich derzeit in der Vorschauphase.

<br>

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Visual Studio. Eine kostenlose Version von Visual Studio finden Sie auf der Seite für [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installieren der erforderlichen Bibliotheken

Rufen Sie die erforderlichen Verwaltungsbibliotheken ab, indem Sie die folgenden Pakete mithilfe der [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) installieren:

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Konfigurieren der Authentifizierung mit Azure Active Directory

Zuerst müssen Sie der Clientanwendung den Zugriff auf die REST-API ermöglichen, indem Sie die erforderliche Authentifizierung einrichten.

Die [APIs des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn948464.aspx) nutzen Azure Active Directory für die Authentifizierung.

Zur Authentifizierung der Clientanwendung basierend auf dem aktuellen Benutzer, müssen Sie die Anwendung zuerst in der AAD-Domäne registrieren, die dem Abonnement zugeordnet ist, unter dem die Azure-Ressourcen erstellt wurden. Wenn das Azure-Abonnement mit einem Microsoft-Konto anstelle eines Geschäfts-, Schul- oder Unikontos erstellt wurde, verfügen Sie bereits über eine AAD-Standarddomäne. Die Anwendung kann im [Azure-Portal](https://manage.windowsazure.com/) registriert werden.

Um eine neue Anwendung zu erstellen und im richtigen Active Directory zu registrieren, führen Sie die folgenden Schritte aus:

1. Führen Sie im Menü auf der linken Seite einen Bildlauf durch, um den Dienst **Active Directory** zu suchen und zu öffnen.

    ![AAD][1]

2. Wählen Sie das Verzeichnis zum Authentifizieren Ihrer Anwendung aus, und klicken Sie auf seinen **Namen**.

    ![Verzeichnisse][4]

3. Klicken Sie auf der Verzeichnisseite auf **ANWENDUNGEN**.

    ![Anwendungen][5]

4. Klicken Sie auf **HINZUFÜGEN**, um eine neue Anwendung zu erstellen.

    ![Anwendung hinzufügen][6]

5. Wählen Sie **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen** aus.

5. Geben Sie im Feld **NAME** einen Namen für die App ein, und wählen Sie **SYSTEMEIGENE CLIENTANWENDUNG** aus.

    ![Anwendung hinzufügen][7]

6. Geben Sie einen **UMLEITUNGS-URI** an. Es muss kein tatsächlicher Endpunkt sein, lediglich ein gültiger URI.

    ![Anwendung hinzufügen][8]

7. Beenden Sie das Erstellen der App, klicken Sie auf **KONFIGURIEREN**, und kopieren Sie die **CLIENT-ID** (Sie benötigen die Client-ID in Ihrem Code).

    ![Client-ID abrufen][9]


1. Klicken Sie unten auf der Seite auf **Anwendung hinzufügen**.
1. Wählen Sie **Microsoft-Apps** aus.
1. Wählen Sie **Azure-Dienstverwaltungs-API** aus, und schließen Sie dann den Assistenten ab.
2. Nun müssen Sie bei ausgewählter API die jeweiligen Berechtigungen für den Zugriff auf diese API erteilen, indem Sie **Auf Azure-Dienstverwaltung (Vorschauversion) zugreifen** auswählen.

    ![Berechtigungen][2]

2. Klicken Sie auf **SPEICHERN**.



### Ermitteln des Domänennamens

Der Domänenname ist für den Code erforderlich. Es folgt eine einfache Möglichkeit zum Ermitteln des richtigen Domänennamens:

1. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com).
2. Zeigen Sie auf Ihren Namen in der oberen rechten Ecke, und notieren Sie sich die Domäne, die im Popupfenster angezeigt wird.

    ![Domänenname ermitteln][3]





**Zusätzliche AAD-Ressourcen**

Weitere Informationen zur Verwendung von Azure Active Directory zur Authentifizierung finden Sie in [diesem nützlichen Blogbeitrag](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Abrufen des Zugriffstokens für den aktuellen Benutzer 

Die Clientanwendung muss das Zugriffstoken der Anwendung für den aktuellen Benutzer abrufen. Wenn der Code zum ersten Mal von einem Benutzer ausgeführt wird, wird er aufgefordert, seine Benutzeranmeldeinformationen einzugeben, und das resultierende Token wird lokal im Cache gespeichert. Bei nachfolgenden Ausführungen wird das Token aus dem Cache abgerufen, und der Benutzer wird nur zur Anmeldung aufgefordert, wenn das Token abgelaufen ist.

    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]Bei den Beispielen in diesem Artikel wird eine synchrone Form der einzelnen API-Anforderungen und -Blöcke bis zur Beendigung des REST-Aufrufs für den zugrunde liegenden Dienst verwendet. Es stehen asynchrone Methoden zur Verfügung.



## Erstellen einer Ressourcengruppe

Beim Ressourcen-Manager müssen alle Ressourcen in einer Ressourcengruppe erstellt werden. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Bei einer Azure SQL-Datenbank muss der Datenbankserver zuerst in einer vorhandenen Ressourcengruppe und die Datenbank dann auf dem Server erstellt werden. Bevor eine Anwendung eine Verbindung mit dem Server oder der Datenbank herstellen kann, müssen Sie auch eine Firewallregel auf dem Server für den Zugriff von der Client-IP-Adresse erstellen.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



## Erstellen eines Servers 

SQL-Datenbanken befinden sich auf Servern. Der Servername muss global für alle Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## Erstellen einer Serverfirewallregel für den Zugriff auf den Server

Standardmäßig kann nicht von jedem Standort aus eine Verbindung mit einem Server hergestellt werden. Zum Herstellen einer Verbindung mit einem Server oder mit Datenbanken auf dem Server muss eine [Firewallregel](https://msdn.microsoft.com/library/azure/ee621782.aspx) definiert werden, die den Zugriff von der Client-IP-Adresse zulässt.

Im folgenden Beispiel wird eine Regel erstellt, die den Zugriff auf den Server von einer beliebigen IP-Adresse zulässt. Es wird empfohlen, geeignete SQL-Anmeldungen und Kennwörter zum Sichern Ihrer Datenbank zu erstellen und sich nicht auf Firewallregeln als primäre Verteidigung gegen Angriffe zu verlassen.


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




Damit andere Azure-Dienste auf einen Server zugreifen können, fügen Sie eine Firewallregel hinzu, und setzen Sie sowohl "StartIpAddress" als auch "EndIpAddress" auf "0.0.0.0". Beachten Sie, dass dadurch Azure-Datenverkehr aus *jedem* Azure-Abonnement auf den Server zugreifen kann.


## Erstellen einer Datenbank

Mit dem folgenden Befehl wird eine neue Basic-Datenbank erstellt, wenn keine Datenbank mit demselben Namen auf dem Server vorhanden ist. Ist eine Datenbank mit demselben Namen vorhanden, wird diese aktualisiert.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## Beispielkonsolenanwendung


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## Nächste Schritte

- [Verbinden mit und Abfragen der SQL-Datenbank mit C#](sql-database-connect-query.md)
- [Herstellen einer Verbindung mit SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)

## Zusätzliche Ressourcen

- [SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=Oct15_HO3-->