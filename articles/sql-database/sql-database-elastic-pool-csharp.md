<properties
    pageTitle="C#-Datenbankentwicklung: Pools für elastische Datenbanken | Microsoft Azure"
    description="Verwenden Sie C#-Datenbankentwicklungstechniken, um einen Pool für elastische Datenbanken in Azure SQL-Datenbank zu erstellen, damit Sie Ressourcen für zahlreiche Datenbanken freigeben können."
    services="sql-database"
    keywords="c#-Datenbank,SQL-Entwicklung"
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
    ms.date="11/06/2015"
    ms.author="sstein"/>

# C&#x23;-Datenbankentwicklung: Erstellen und Konfigurieren eines Pools für elastische Datenbanken für SQL-Datenbank

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


Dieser Artikel beschreibt, wie Sie einen [Pool für elastische Datenbanken](sql-database-elastic-pool.md) für SQL-Datenbanken aus einer Anwendung mithilfe von C#-Entwicklungstechniken erstellen.

> [AZURE.NOTE]Pools für elastische Datenbanken sind derzeit als Vorschauversion ausschließlich für Server mit SQL-Datenbank V12 verfügbar. Wenn Sie über einen SQL-Datenbank V11-Server verfügen, können Sie in einem Schritt [mithilfe von PowerShell auf V12 aktualisieren und einen Pool erstellen](sql-database-upgrade-server.md).

In diesem Beispiel wird die [Azure SQL-Datenbankbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) verwendet. Aus Gründen der Übersichtlichkeit beschränken sich die Beispiele auf einzelne Codeausschnitte. In dem Abschnitt am Ende dieses Artikels sind alle Befehle in einer Beispielkonsolenanwendung zusammengeführt.

Die Azure SQL-Datenbankbibliothek für .NET bietet eine [Azure-Ressourcen-Manager](resource-group-overview.md)-basierte API, die die [Ressourcen-Manager-basierte REST-API für die SQL-Datenbank](https://msdn.microsoft.com/library/azure/mt163571.aspx) umfasst. Diese Clientbibliothek folgt dem allgemeinen Muster für Ressourcen-Manager-basierte Clientbibliotheken. Der Ressourcen-Manager erfordert Ressourcengruppen und die Authentifizierung mit [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE]Die SQL-Datenbankbibliothek für .NET befindet sich derzeit in der Vorschauphase.

<br>

Wenn Sie kein Azure-Abonnement haben, klicken Sie einfach oben auf dieser Seite auf den Link **Kostenlose Testversion**, und kehren Sie dann zu diesem Artikel zurück. Eine kostenlose Version von Visual Studio finden Sie auf der Seite für [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Installieren der erforderlichen Bibliotheken

Rufen Sie die erforderlichen Verwaltungsbibliotheken ab, indem Sie die folgenden Pakete mithilfe der [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) für die Entwicklung in SQL installieren:

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Konfigurieren der Authentifizierung mit Azure Active Directory

Bevor Sie die SQL-Entwicklung mit C# beginnen, müssen Sie einige Aufgaben im Azure-Portal ausführen. Ermöglichen Sie zunächst der Anwendung den Zugriff auf die REST-API, indem Sie die erforderliche Authentifizierung einrichten.

Die [REST-APIs des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn948464.aspx) verwenden Azure Active Directory zur Authentifizierung und nicht die Zertifikate, die von den früheren REST-APIs für die Azure-Dienstverwaltung verwendet wurden.

Zur Authentifizierung der Clientanwendung basierend auf dem aktuellen Benutzer, müssen Sie die Anwendung zuerst in der AAD-Domäne registrieren, die dem Abonnement zugeordnet ist, unter dem die Azure-Ressourcen erstellt wurden. Wenn das Azure-Abonnement mit einem Microsoft-Konto anstelle eines Geschäfts-, Schul- oder Unikontos erstellt wurde, verfügen Sie bereits über eine AAD-Standarddomäne. Das Registrieren der Anwendung kann im [Verwaltungsportal](https://manage.windowsazure.com/) erfolgen.

Um eine neue Anwendung zu erstellen und im richtigen Active Directory zu registrieren, führen Sie die folgenden Schritte aus:

1. Führen Sie im Menü auf der linken Seite einen Bildlauf durch, um den Dienst **Active Directory** zu suchen und zu öffnen.

    ![C#-SQL-Datenbankentwicklung: Active Directory-Einrichtung][1]

2. Wählen Sie das Verzeichnis zum Authentifizieren Ihrer Anwendung aus, und klicken Sie auf seinen **Namen**.

    ![Wählen Sie ein Verzeichnis aus.][4]

3. Klicken Sie auf der Verzeichnisseite auf **ANWENDUNGEN**.

    ![Klicken Sie auf „Anwendungen“.][5]

4. Klicken Sie auf **HINZUFÜGEN**, um eine neue Anwendung zu erstellen.

    ![Klicken Sie auf die Schaltfläche „Hinzufügen“: Erstellen Sie eine C#-Anwendung.][6]

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

    ![Berechtigungen festlegen][2]

2. Klicken Sie auf **SPEICHERN**.



### Ermitteln des Domänennamens

Der Domänenname ist für den Code erforderlich. Es folgt eine einfache Möglichkeit zum Ermitteln des richtigen Domänennamens:

1. Öffnen Sie das [Azure-Vorschauportal](https://portal.azure.com).
2. Zeigen Sie auf Ihren Namen in der oberen rechten Ecke, und notieren Sie sich die Domäne, die im Popupfenster angezeigt wird. Ersetzen Sie **domain.onmicrosoft.com** im folgenden Codeausschnitt durch den Wert für Ihr Konto.

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



> [AZURE.NOTE]Bei den Beispielen in diesem Artikel wird eine synchrone Form der einzelnen API-Anforderungen und -Blöcke bis zur Beendigung des REST-Aufrufs für den zugrunde liegenden Dienst verwendet. Es stehen asynchrone Methoden zur Verfügung.



## Erstellen einer Ressourcengruppe

Beim Ressourcen-Manager müssen alle Ressourcen in einer Ressourcengruppe erstellt werden. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Zum Erstellen einer Pools für elastische Datenbanken benötigen Sie einen Azure SQL-Datenbankserver in einer vorhandenen Ressourcengruppe. Führen Sie den folgenden C#-Befehl aus, um die Ressourcengruppe zu erstellen:


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

Pools für elastische Datenbanken sind in Azure SQL-Datenbankservern enthalten, daher muss im nächsten Schritt ein Server erstellt werden. Der Servername muss global für alle Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann. Damit eine Anwendung eine Verbindung mit dem Server herstellen kann, müssen Sie auch eine Firewallregel auf dem Server für den Zugriff von der Client-IP-Adresse erstellen.


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

Standardmäßig sind für einen Server keine Firewallregeln eingerichtet, daher kann nicht von jedem Standort aus eine Verbindung mit ihm hergestellt werden. Zum Herstellen einer Verbindung mit einem Server oder mit Datenbanken auf dem Server muss eine [Firewallregel](sql-database-firewall-configure.md) definiert werden, die den Zugriff von der Client-IP-Adresse zulässt.

Im folgenden Beispiel wird eine Serverfirewallregel erstellt, die den Zugriff auf den Server von einer beliebigen IP-Adresse zulässt. Es wird empfohlen, geeignete SQL-Anmeldungen und Kennwörter zum Sichern Ihrer Datenbank zu erstellen und sich nicht auf Firewallregeln als primäre Verteidigung gegen Angriffe zu verlassen. Ausführliche Informationen finden Sie unter [Verwalten von Datenbanken und Anmeldungen in Azure SQL-Datenbank](sql-database-manage-logins.md).


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

Das folgende Beispiel erstellt eine neue Basic-Datenbank. Wenn eine Datenbank mit demselben Namen auf dem Server vorhanden ist, wird die vorhandene Datenbank aktualisiert.

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



## Erstellen eines elastischen Datenbankpools

Im folgenden Beispiel wird ein neuer Pool für elastische Datenbanken erstellt:



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

Das folgende Beispiel aktualisiert die Leistungsmerkmale eines vorhandenen Pools für elastische Datenbanken:

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

Im folgenden Beispiel wird eine vorhandene Azure SQL-Datenbank in einen Pool verschoben:


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

Im folgenden Beispiel wird eine neue Datenbank direkt in einem Pool erstellt:


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

Das folgende Beispiel führt alle Datenbanken in einem Pool auf:

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## Beispielkonsolenanwendung


    using Microsoft.Azure;
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


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
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

            // Update a databases service objective to add the database to a pool

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
      }
    }







## Zusätzliche Ressourcen


[SQL-Datenbank](https://azure.microsoft.com/documentation/services/sql-database/)

[APIs für Azure-Ressourcenverwaltung](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referenz für Pools für elastische Datenbanken](sql-database-elastic-pool-reference.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png

<!---HONumber=Nov15_HO4-->