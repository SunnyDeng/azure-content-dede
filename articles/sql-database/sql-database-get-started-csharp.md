<properties 
	pageTitle="Ausprobieren von SQL-Datenbank: Erstellen einer SQL-Datenbank mit C# | Microsoft Azure" 
	description="Probieren Sie SQL-Datenbank für das Entwickeln von SQL- und C#-Apps aus, und erstellen Sie eine Azure SQL-Datenbank mithilfe der SQL-Datenbankbibliothek für .NET." 
	keywords="Ausprobieren von SQL, SQL C#"   
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="01/22/2016"
   ms.author="sstein"/>

# Ausprobieren von SQL-Datenbank: Verwenden von C# zum Erstellen einer SQL-Datenbank mithilfe der SQL-Datenbankbibliothek für .NET 

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Erfahren Sie, wie Sie C#-Befehle zum Erstellen einer Azure SQL-Datenbank mithilfe der [Azure SQL-Datenbankbibliothek für .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql) erstellen.

Sie können SQL-Datenbank ausprobieren, indem Sie mit SQL und C# eine Einzeldatenbank erstellen. Informationen zum Erstellen elastischer Datenbanken finden Sie unter [Erstellen eines Pools für elastische Datenbanken](sql-database-elastic-pool-portal.md).

Aus Gründen der Übersichtlichkeit beschränken sich die Beispiele auf einzelne Codeausschnitte. In dem Abschnitt am Ende dieses Artikels sind alle Befehle in einer Beispielkonsolenanwendung zusammengeführt.

Die Azure SQL-Datenbankbibliothek für .NET bietet eine [Azure-Ressourcen-Manager](resource-group-overview.md)-basierte API, die die [Ressourcen-Manager-basierte REST-API für die SQL-Datenbank](https://msdn.microsoft.com/library/azure/mt163571.aspx) umfasst. Diese Clientbibliothek folgt dem allgemeinen Muster für Ressourcen-Manager-basierte Clientbibliotheken. Der Ressourcen-Manager erfordert Ressourcengruppen und die Authentifizierung mit [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD).

<br>

> [AZURE.NOTE] Die SQL-Datenbankbibliothek für .NET befindet sich derzeit in der Vorschauphase.

<br>

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Visual Studio. Eine kostenlose Version von Visual Studio finden Sie auf der Seite für [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Installieren der erforderlichen Bibliotheken

Zum Einrichten einer SQL-Datenbank mit C# erhalten Sie die erforderlichen Verwaltungsbibliotheken, indem Sie über die [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console) in Visual Studio (**Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**) die folgenden Pakete installieren:

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Konfigurieren der Authentifizierung mit Azure Active Directory

Zuerst müssen Sie der Clientanwendung den Zugriff auf den SQL-Datenbankdienst ermöglichen, indem Sie die erforderliche Authentifizierung einrichten.

Zur Authentifizierung der Clientanwendung basierend auf dem aktuellen Benutzer, müssen Sie die Anwendung zuerst in der AAD-Domäne registrieren, die dem Abonnement zugeordnet ist, unter dem die Azure-Ressourcen erstellt wurden. Wenn das Azure-Abonnement mit einem Microsoft-Konto anstelle eines Geschäfts-, Schul- oder Unikontos erstellt wurde, verfügen Sie bereits über eine AAD-Standarddomäne.

Um eine neue Anwendung zu erstellen und im richtigen Active Directory zu registrieren, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com/) an.
1. Wählen Sie auf der linken Seite den Dienst **Active Directory** und dann das Verzeichnis zum Authentifizieren Ihrer Anwendung aus, und klicken Sie auf den **Namen**.

    ![Ausprobieren von SQL-Datenbank: Einrichten von Azure Active Directory (AAD).][1]

2. Klicken Sie auf der Verzeichnisseite auf **ANWENDUNGEN**.

    ![Die Verzeichnisseite mit Anwendungen.][5]

4. Klicken Sie auf **HINZUFÜGEN**, um eine neue Anwendung zu erstellen.

5. Wählen Sie **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen** aus.

5. Geben Sie im Feld **NAME** einen Namen für die App ein, und wählen Sie **SYSTEMEIGENE CLIENTANWENDUNG** aus.

    ![Geben Sie Informationen zu Ihrer SQL C#-Anwendung an.][7]

6. Geben Sie einen **UMLEITUNGS-URI** an. Es muss kein tatsächlicher Endpunkt sein, lediglich ein gültiger URI.

    ![Fügen Sie eine Umleitungs-URL für Ihre SQL-C#-Anwendung hinzu.][8]

7. Beenden Sie das Erstellen der App, klicken Sie auf **KONFIGURIEREN**, und kopieren Sie die **CLIENT-ID** (Sie benötigen die Client-ID später in Ihrem Code).

    ![Rufen Sie die Client-ID Ihrer Anwendung ab.][9]


1. Klicken Sie unten auf der Seite auf **Anwendung hinzufügen**.
1. Wählen Sie **Microsoft-Apps** aus.
1. Wählen Sie **Azure Service-Verwaltungs-API** aus, und schließen Sie dann den Assistenten ab.
2. Nun müssen Sie bei ausgewählter API die jeweiligen Berechtigungen für den Zugriff auf diese API erteilen, indem Sie **Auf Azure-Dienstverwaltung (Vorschauversion) zugreifen** auswählen.

    ![Legen Sie Berechtigungen fest.][2]

2. Klicken Sie auf **SPEICHERN**.



### Ermitteln des Domänennamens

Der Domänenname ist für den Code erforderlich. Es folgt eine einfache Möglichkeit zum Ermitteln des richtigen Domänennamens:

1. Öffnen Sie das [Azure-Portal](http://portal.azure.com).
2. Zeigen Sie auf Ihren Namen in der oberen rechten Ecke, und notieren Sie sich die Domäne, die im Popupfenster angezeigt wird.

    ![Ermitteln Sie den Domänennamen.][3]





**Zusätzliche AAD-Ressourcen**

Weitere Informationen zur Verwendung von Azure Active Directory zur Authentifizierung finden Sie in [diesem nützlichen Blogbeitrag](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Abrufen des Zugriffstokens für den aktuellen Benutzer 

Die Clientanwendung muss das Zugriffstoken der Anwendung für den aktuellen Benutzer abrufen. Wenn der Code zum ersten Mal von einem Benutzer ausgeführt wird, wird er aufgefordert, seine Benutzeranmeldeinformationen einzugeben, und das resultierende Token wird lokal im Cache gespeichert. Bei nachfolgenden Ausführungen wird das Token aus dem Cache abgerufen, und der Benutzer wird nur zur Anmeldung aufgefordert, wenn das Token abgelaufen ist.

Dieser Code gibt ein Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult-Element zurück, das Sie in den anderen Codeausschnitten weiter unten benötigen.

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



> [AZURE.NOTE] Bei den Beispielen in diesem Artikel wird eine synchrone Form der einzelnen API-Anforderungen und -Blöcke bis zur Beendigung des REST-Aufrufs für den zugrunde liegenden Dienst verwendet. Es stehen asynchrone Methoden zur Verfügung.



## Erstellen einer Ressourcengruppe

Beim Ressourcen-Manager müssen alle Ressourcen in einer Ressourcengruppe erstellt werden. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Bei Azure SQL-Datenbank muss der Datenbankserver in einer vorhandenen Ressourcengruppe erstellt werden.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Erstellen eines Servers 

SQL-Datenbanken befinden sich auf Servern. Der Servername muss global für alle Azure SQL-Server eindeutig sein. Wenn der Servername bereits vergeben ist, wird ein Fehler ausgeben. Sie sollten auch berücksichtigen, dass dieser Befehl mehrere Minuten in Anspruch nehmen kann.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Erstellen eines externen Serveradministrators


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Erstellen einer Serverfirewallregel für den Zugriff auf den Server

Standardmäßig kann nicht von jedem Standort aus eine Verbindung mit einem Server hergestellt werden. Zum Herstellen einer Verbindung mit einem Server oder mit Datenbanken auf dem Server muss eine [Firewallregel](https://msdn.microsoft.com/library/azure/ee621782.aspx) definiert werden, die den Zugriff von der Client-IP-Adresse zulässt.

Im folgenden Beispiel wird eine Regel erstellt, die den Zugriff auf den Server von einer beliebigen IP-Adresse zulässt. Es wird empfohlen, geeignete SQL-Anmeldungen und Kennwörter zum Sichern Ihrer Datenbank zu erstellen und sich nicht auf Firewallregeln als primäre Verteidigung gegen Angriffe zu verlassen.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Damit andere Azure-Dienste auf einen Server zugreifen können, fügen Sie eine Firewallregel hinzu, und setzen Sie sowohl "StartIpAddress" als auch "EndIpAddress" auf "0.0.0.0". Beachten Sie, dass dadurch Azure-Datenverkehr aus *jedem* Azure-Abonnement auf den Server zugreifen kann.


## Verwenden von C&#x23; zum Erstellen einer SQL-Datenbank

Mit dem folgenden C#-Befehl wird eine neue SQL-Datenbank erstellt, sofern nicht bereits eine Datenbank mit demselben Namen auf dem Server vorhanden ist. Ist eine Datenbank mit demselben Namen vorhanden, wird diese aktualisiert.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## C#-Beispielkonsolenanwendung

Im folgenden Beispiel werden diese Komponenten erstellt: Ressourcengruppe, Server, Firewallregel und eine SQL-Datenbank. Im Abschnitt *Konfigurieren der Authentifizierung mit Azure Active Directory* am Anfang dieses Artikels wird gezeigt, wie Sie die Werte für die Variablen clientId, redirectUri und domainName abrufen.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    
    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values 
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";
        
        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString() 
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server 
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
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
Nachdem Sie SQL-Datenbank ausprobiert und mit C# eine Datenbank erstellt haben, bietet sich die folgenden Artikel an:

- [Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit SQL Server Management Studio und Ausführen einer T-SQL-Beispielabfrage](sql-database-connect-query-ssms.md)

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

<!---HONumber=AcomDC_0204_2016-->