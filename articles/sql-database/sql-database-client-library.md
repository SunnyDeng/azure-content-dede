<properties 
   pageTitle="Erstellen und Verwalten von Azure SQL-Datenbanken mit C#" 
   description="In diesem Artikel wird gezeigt, wie Sie eine Azure SQL-Datenbank mithilfe von C# und der Azure SQL-Datenbankbibliothek für .NET erstellen und verwalten." 
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
   ms.date="12/01/2015"
   ms.author="sstein"/>

# Erstellen und Verwalten von SQL-Datenbanken mit C&#x23;

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## Übersicht

Dieser Artikel enthält Befehle zum Ausführen einer Vielzahl von Verwaltungsaufgaben für die Azure SQL-Datenbank mithilfe von C# und der [Azure SQL-Datenbankbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

Aus Gründen der Übersichtlichkeit beschränken sich die Beispiele auf einzelne Codeausschnitte. In dem Abschnitt am Ende dieses Artikels sind alle Befehle in einer Beispielkonsolenanwendung zusammengeführt.

Die Azure SQL-Datenbankbibliothek für .NET bietet eine [Azure-Ressourcen-Manager](resource-group-overview.md)-basierte API, die die [Ressourcen-Manager-basierte REST-API für die SQL-Datenbank](https://msdn.microsoft.com/library/azure/mt163571.aspx) umfasst. Diese Clientbibliothek folgt dem allgemeinen Muster für Ressourcen-Manager-basierte Clientbibliotheken. Der Ressourcen-Manager erfordert Ressourcengruppen und die Authentifizierung mit [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]Die SQL-Datenbankbibliothek für .NET befindet sich derzeit in der Vorschauphase.

<br>

Wenn Sie kein Azure-Abonnement haben, klicken Sie einfach oben auf dieser Seite auf den Link **Kostenlose Testversion**, und kehren Sie dann zu diesem Artikel zurück. Eine kostenlose Version von Visual Studio finden Sie auf der Seite für [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Installieren der erforderlichen Bibliotheken

Rufen Sie die erforderlichen Verwaltungsbibliotheken ab, indem Sie die folgenden Pakete mithilfe der [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) installieren:

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Konfigurieren der Authentifizierung mit Azure Active Directory

Zuerst müssen Sie der Anwendung den Zugriff auf die REST-API ermöglichen, indem Sie die erforderliche Authentifizierung einrichten.

Die [REST-APIs des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn948464.aspx) verwenden Azure Active Directory zur Authentifizierung und nicht die Zertifikate, die von den früheren REST-APIs für die Azure-Dienstverwaltung verwendet wurden.

Zur Authentifizierung der Clientanwendung basierend auf dem aktuellen Benutzer, müssen Sie die Anwendung zuerst in der AAD-Domäne registrieren, die dem Abonnement zugeordnet ist, unter dem die Azure-Ressourcen erstellt wurden. Wenn das Azure-Abonnement mit einem Microsoft-Konto anstelle eines Geschäfts-, Schul- oder Unikontos erstellt wurde, verfügen Sie bereits über eine AAD-Standarddomäne. Die Anwendung kann im [klassischen Portal](https://manage.windowsazure.com/) registriert werden.

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
1. Wählen Sie **Azure Service-Verwaltungs-API** aus, und schließen Sie dann den Assistenten ab.
2. Nun müssen Sie bei ausgewählter API die jeweiligen Berechtigungen für den Zugriff auf diese API erteilen, indem Sie **Auf Azure-Dienstverwaltung (Vorschauversion) zugreifen** auswählen.

    ![Berechtigungen][2]

2. Klicken Sie auf **SPEICHERN**.



### Ermitteln des Domänennamens

Der Domänenname ist für den Code erforderlich. Es folgt eine einfache Möglichkeit zum Ermitteln des richtigen Domänennamens:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
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
            ("https://login.windows.net/" /* AAD URI */ 
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */, 
                "aa00a0a0-a0a0-0000-0a00-a0a00000a0aa" /* application client ID from AAD*/, 
        new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */, 
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }

Zum Erstellen automatisierter Skripts, bei denen keine Benutzerinteraktion erforderlich ist, kann die Authentifizierung basierend auf einem Dienstprinzipal anstelle eines Benutzers erfolgen. Für diesen Ansatz ist das Erstellen und Übermitteln eines Anmeldeinformationsobjekts erforderlich.




> [AZURE.NOTE]Bei den Beispielen in diesem Artikel wird eine synchrone Form der einzelnen API-Anforderungen und -Blöcke bis zur Beendigung des REST-Aufrufs für den zugrunde liegenden Dienst verwendet. Es stehen asynchrone Methoden zur Verfügung.



## Erstellen einer Ressourcengruppe

Beim Ressourcen-Manager müssen alle Ressourcen in einer Ressourcengruppe erstellt werden. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Bei einer Azure SQL-Datenbank muss der Datenbankserver zuerst in einer vorhandenen Ressourcengruppe und dann die Datenbank auf dem Server erstellt werden. Bevor eine Anwendung eine Verbindung mit dem Server oder der Datenbank mithilfe von TDS zum Übermitteln von T-SQL herstellen kann, müssen Sie auch eine Firewallregel auf dem Server für den Zugriff von der Client-IP-Adresse erstellen.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



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
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## Erstellen einer Serverfirewallregel für den Zugriff auf den Server

Standardmäßig kann nicht von jedem Standort aus eine Verbindung mit einem Server hergestellt werden. Zum Herstellen einer Verbindung mit einem Server mithilfe von TDS und Übermitteln von T-SQL an den Server oder an Datenbanken auf dem Server muss eine [Firewallregel](https://msdn.microsoft.com/library/azure/ee621782.aspx) definiert werden, die den Zugriff von der Client-IP-Adresse zulässt.

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

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




Damit andere Azure-Dienste auf einen Server zugreifen können, fügen Sie eine Firewallregel hinzu, und setzen Sie sowohl "StartIpAddress" als auch "EndIpAddress" auf "0.0.0.0". Beachten Sie, dass dadurch Azure-Datenverkehr aus *jedem* Azure-Abonnement auf den Server zugreifen kann.


## Erstellen einer Datenbank

Mit dem folgenden Befehl wird eine neue Basic-Datenbank erstellt, wenn keine Datenbank mit demselben Namen auf dem Server vorhanden ist. Ist eine Datenbank mit demselben Namen vorhanden, wird diese aktualisiert.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## Aktualisieren einer Datenbank 

Zum Aktualisieren einer Datenbank (z. B. Ändern der Dienst- und Leistungsebene) rufen Sie die Methode **Databases.CreateOrUpdate** wie beim oben gezeigten Erstellen bzw. Aktualisieren einer Datenbank auf. Legen Sie die Eigenschaften **Edition** und **RequestedServiceObjectiveName** auf die gewünschte Dienst- und Leistungsebene fest. Beachten Sie, dass beim Ändern der Eigenschaft "Edition" von oder in **Premium** das Update je nach Größe der Datenbank eine Weile dauern kann.

Im folgenden Beispiel wird eine SQL-Datenbank auf die Ebene "Standard" (S0) aktualisiert:

    // Retrieve current database properties 
    var currentDatabase = sqlClient.Databases.Get("resourecegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation
        }
     };

    // Update the database
    dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);


## Auflisten aller Datenbanken auf einem Server

Zum Auflisten aller Datenbanken auf einem Server übergeben Sie den Namen des Servers und der Ressourcengruppe an die Methode "Databases.List":

    // List databases on the server
    DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
    Console.WriteLine("Databases on Server {0}", "server-name");
    foreach (Database db in dbListOnServer)
    {
        Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
    }



## Erstellen eines elastischen Datenbankpools

So erstellen Sie einen neuen Pool auf einem Server:



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## Aktualisieren eines elastischen Datenbankpools

    // Retrieve existing pool properties
    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);



## Verschieben einer vorhandenen Datenbank in einen Pool für elastische Datenbanken

*Nach dem Erstellen eines Pools können Sie Transact-SQL auch zum Verschieben vorhandener Datenbanken in und aus Pools verwenden. Weitere Informationen finden Sie unter [Referenz für Pools für elastische Datenbanken – Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

So verschieben Sie eine vorhandene Datenbank in einen Pool:

    
    // Update database service objective to add the database to a pool
    
    // Retrieve current database properties 
    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;
    
    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };
    
    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);
    
    


## Erstellen einer neuen Datenbank in einem elastischen Datenbankpool

*Nach dem Erstellen eines Pools können Sie Transact-SQL auch zum Erstellen von neuen elastischen Datenbanken im Pool verwenden, Weitere Informationen finden Sie unter [Referenz für Pools für elastische Datenbanken – Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

So erstellen Sie eine neue Datenbank direkt in einem Pool:

    
    // Create a new database in the pool
    
    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };
    
    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);



## Auflisten aller Datenbanken in einem elastischen Datenbankpool

So listen Sie alle Datenbanken in einem Pool auf:

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## Löschen eines Servers

Zum Löschen eines Servers (wodurch auch die Datenbanken und alle elastischen Datenbankpools auf dem Server gelöscht werden), führen Sie den folgenden Code aus:

    var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");


## Löschen einer Ressourcengruppe

So löschen Sie eine Ressourcengruppe:

    // Delete the resource group
    var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");



## Beispielkonsolenanwendung


    using Microsoft.Azure;
    using Microsoft.Azure.Insights;
    using Microsoft.Azure.Insights.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */ 
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */, 
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/, 
                new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */, 
                PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();
            
            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Basic",
                    RequestedServiceObjectiveName = "Basic",
                    MaxSizeBytes = 2147483648,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

            // ...
            // Update database: retrieve current database properties 
            var currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation
                }
            };

            // Update the database
            dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
                    DatabaseDtuMin = 0,
                    DatabaseDtuMax = 100
                }
            };

           // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
                    DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
                    Dtu = (int)currentPool.Properties.Dtu,
                    StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
                }
            };
            newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update database service objective to add the database to a pool

            // Update database: retrieve current database properties 
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };

            // Update the database
            var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", dbUpdateResponse.Database.Name, dbUpdateResponse.StatusCode, dbUpdateResponse.Database.Properties.ServiceObjective, dbUpdateResponse.Database.Properties.ElasticPoolName);

            // Create a new database in the pool

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = 268435456000, // 250 GB,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);

            // List databases on the server
            DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
            Console.WriteLine("Databases on Server {0}", "server-name");
            foreach (Database db in dbListOnServer)
            {
                Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
            }

            // List all servers, pools and databases in the resource group
            ServerListResponse serverList = new ServerListResponse();
            ElasticPoolListResponse poolList = new ElasticPoolListResponse();
            DatabaseListResponse dbListInPool = new DatabaseListResponse();

            Console.WriteLine("Servers in resource group {0}", "resourcegroup-name");
            serverList = sqlClient.Servers.List("resourcegroup-name");
            foreach (Server server in serverList)
            {
                Console.WriteLine("  Server '{0}' location: {1}", server.Name, server.Location);
                poolList = sqlClient.ElasticPools.List("resourcegroup-name", server.Name);
                foreach (ElasticPool pool in poolList)
                {
                    Console.WriteLine("    Elastic Pool '{0}' edition: {1} eDTU: {2} storage GB: {3} database eDTU min: {4} database eDTU max: {5}", pool.Name, pool.Properties.Edition, pool.Properties.Dtu, (pool.Properties.StorageMB/1024), pool.Properties.DatabaseDtuMin, pool.Properties.DatabaseDtuMax);
                    dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", server.Name, pool.Name);
                    foreach(Database db in dbListInPool)
                    {
                        Console.WriteLine("      Database '{0}'", db.Name);                       
                    }
                }
            }

            // Metrics

            var endTime = String.Format(DateTime.Now.ToUniversalTime().ToString("s")) + "Z"; // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ
            var duration = TimeSpan.FromHours(2);
            var startTime = String.Format(DateTime.Now.Subtract(duration).ToUniversalTime().ToString("s")) + "Z";  // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ

            Console.WriteLine("");
            Console.WriteLine("Elastic pool metrics for 'ElasticPool1'");

            ElasticPoolMetricDefinitions poolMetricDefinition = new ElasticPoolMetricDefinitions();
            poolMetricDefinition = sqlClient.ElasticPools.ListMetricDefinitions("resourcegroup-name", "server-name", "ElasticPool1");

            Console.WriteLine("  Metric definitions: ");
            foreach (Microsoft.Azure.Management.Sql.Models.MetricDefinition metricDefinition in poolMetricDefinition.MetricDefinitions)
            {
                Console.WriteLine("    '{0}' unit: {1} aggregation type: {2}", metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            Console.WriteLine("  Metric values: ");
            ElasticPoolMetrics elasticPoolMetrics = new ElasticPoolMetrics();
            elasticPoolMetrics = sqlClient.ElasticPools.ListMetrics("resourcegroup-name", "server-name", "ElasticPool1", "name.value eq 'dtu_consumption_percent'", "PT5M", startTime, endTime);
            foreach (Microsoft.Azure.Management.Sql.Models.Metric metric in elasticPoolMetrics.Metrics)
            {
                Console.WriteLine("    '{0}' unit: {1} time grain: {2} start time: {3} end time: {4} values: {5}", metric.Name.LocalizedValue, metric.Unit, metric.TimeGrain, metric.StartTime, metric.EndTime, metric.Values.Count);
                foreach (Value metricValue in metric.Values)
                {
                    Console.WriteLine("      Timestamp: {0} average: {1} minimum: {2} maximum {3} total {4}", metricValue.Timestamp, metricValue.Average, metricValue.Maximum, metricValue.Minimum, metricValue.Total);
                }
            }

            // List database metrics
            Console.WriteLine("");
            Console.WriteLine("Database metrics for 'Database1'");
            Console.WriteLine("  Metric definitions"); 

            Microsoft.Azure.Insights.InsightsClient insightsClient = new InsightsClient(tokenCredentials);

            Microsoft.Azure.Insights.Models.MetricDefinitionListResponse metricDefinitionListResponse = insightsClient.MetricDefinitionOperations.GetMetricDefinitions("subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/", "");
            foreach (Microsoft.Azure.Insights.Models.MetricDefinition metricDefinition in metricDefinitionListResponse.MetricDefinitionCollection.Value)
            {
                Console.WriteLine("    {0} unit: {1} aggregation: {2}" , metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            var resourceURI = "subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/";
            var filter = "(name.value eq 'dtu_consumption_percent') and startTime eq " + startTime + " and endTime eq " + endTime + " and timeGrain eq duration'PT5M'";

            Console.WriteLine("  Metric values");
            MetricListResponse mlr = insightsClient.MetricOperations.GetMetrics(resourceURI,filter);
            foreach (Microsoft.Azure.Insights.Models.Metric metric in mlr.MetricCollection.Value)
            {
                Console.WriteLine("    {0}", metric.Name.LocalizedValue);
                foreach (MetricValue metricValue in metric.MetricValues)
                {
                    Console.WriteLine("      Timestamp: {0} minimum: {1} maximum: {2} average: {3}", metricValue.Timestamp, metricValue.Minimum, metricValue.Maximum, metricValue.Average);
                }
            }

            Console.WriteLine("");
            Console.WriteLine("Press any key to delete the server and resource group, which will also delete the databases and the elastic pool.");
            Console.ReadKey();

            // Delete the server which deletes the databases and then the elastic pool
            var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");
            Console.WriteLine("");
            Console.WriteLine("Server {0} delete completed with status code {1}.", "server-name", serverOperationResponse.StatusCode);

            // Delete the resource group
            var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");
            Console.WriteLine("");
            Console.WriteLine("Resource {0} delete completed with status code {1}.", "resourcegroup-name", resourceOperationResponse.StatusCode);

            Console.WriteLine("");
            Console.WriteLine("Execution complete.  Press any key to continue.");
            Console.ReadKey();
        }
    }
    }





## Zusätzliche Ressourcen

[SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)

[APIs für Azure-Ressourcenverwaltung](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referenz für Pools für elastische Datenbanken](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-client-library/aad.png
[2]: ./media/sql-database-client-library/permissions.png
[3]: ./media/sql-database-client-library/getdomain.png
[4]: ./media/sql-database-client-library/aad2.png
[5]: ./media/sql-database-client-library/aad-applications.png
[6]: ./media/sql-database-client-library/add.png
[7]: ./media/sql-database-client-library/add-application.png
[8]: ./media/sql-database-client-library/add-application2.png
[9]: ./media/sql-database-client-library/clientid.png

<!---HONumber=AcomDC_1203_2015-->