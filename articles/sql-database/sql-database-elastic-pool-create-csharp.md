<properties
    pageTitle="Erstellen eines Pools für elastische Datenbanken mit C# | Microsoft Azure"
    description="Verwenden Sie C#-Datenbankentwicklungstechniken, um einen skalierbaren Pool für elastische Datenbanken in Azure SQL-Datenbank zu erstellen, damit Sie Ressourcen für zahlreiche Datenbanken freigeben können."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="03/24/2016"
    ms.author="sstein"/>

# Erstellen eines Pools für elastische Datenbanken mit C&#x23;

> [AZURE.SELECTOR]
- [Azure-Portal](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Hier erfahren Sie, wie Sie mithilfe von (C&#x23;) einen [Pool für elastische Datenbanken](sql-database-elastic-pool.md) erstellen.

Häufige Fehlercodes finden Sie unter [SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen: Datenbankverbindungsfehler und andere Probleme](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar. Wenn Sie über einen SQL-Datenbank V11-Server verfügen, können Sie in einem Schritt [mithilfe von PowerShell auf V12 aktualisieren und einen Pool erstellen](sql-database-upgrade-server-portal.md).

In den Beispielen wird die [SQL-Datenbankbibliothek für .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx) verwendet. Sie müssen die Bibliothek daher installieren. Verwenden Sie den folgenden Befehl zur Installation über die [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre




## Erstellen eines Pools


    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Verschieben einer vorhandenen Datenbank in einen Pool


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




## Erstellen einer neuen Datenbank in einem Pool


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





## Erstellen eines Poolbeispiels mit C&#x23;


Die folgenden Bibliotheken sind erforderlich, um dieses Beispiel auszuführen. Verwenden Sie den folgenden Befehl zur Installation über die [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre

Erstellen Sie eine Konsolenanwendung, und ersetzen Sie den Inhalt von Program.cs durch den folgenden Code. Die erforderliche Client-ID und die verknüpften Werte finden Sie unter [Register your app and get the required client values for connecting your app to SQL Database](sql-database-client-id-keys.md) (Registrieren Ihrer App und Erhalt der erforderlichen Clientwerte für die Verbindung Ihrer App mit der SQL-Datenbank).

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }


        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

  

## Nächste Schritte

- [Verwalten Ihres Pools](sql-database-elastic-pool-manage-csharp.md)
- [Erstellen elastischer Aufträge:](sql-database-elastic-jobs-overview.md) Elastische Aufträge ermöglichen die Ausführung von T-SQL-Skripts für eine beliebige Anzahl von Datenbanken im Pool.


## Zusätzliche Ressourcen


- [SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs für Azure-Ressourcenverwaltung](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Referenz für Pools für elastische Datenbanken](sql-database-elastic-pool-reference.md)

<!---HONumber=AcomDC_0330_2016-->