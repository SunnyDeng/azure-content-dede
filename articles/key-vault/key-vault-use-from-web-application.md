<properties 
	pageTitle="Verwenden des Azure-Schlüsseltresors aus einer Webanwendung | Übersicht" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie den Azure-Schlüsseltresor aus einer Webanwendung verwenden." 
	services="key-vault" 
	documentationCenter="" 
	authors="adamhurwitz" 
	manager=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/24/2015" 
	ms.author="adhurwit"/>

# Verwenden des Azure-Schlüsseltresors aus einer Webanwendung #

## Einführung  
In diesem Lernprogramm erfahren Sie, wie Sie den Azure-Schlüsseltresor aus einer Webanwendung in Azure verwenden. Sie werden durch den Prozess des Zugriffs auf einen geheimen Schlüssel aus dem Azure-Schlüsseltresor geführt, um diesen in der Webanwendung verwenden zu können.

**Geschätzter Zeitaufwand**: 15 Minuten.


Eine Übersicht über den Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](key-vault-whatis.md)

## Voraussetzungen 

Für dieses Lernprogramm benötigen Sie Folgendes:

- Einen URI eines geheimen Schlüssels im Azure-Schlüsseltresor
- Eine Client-ID und einen geheimen Schlüssel für den Client für eine Webanwendung, die in Azure Active Directory registriert ist und Zugriff auf Ihren Schlüsseltresor hat
- Eine Webanwendung. Es werden die Schritte für eine ASP.NET MVC-Anwendung gezeigt, die in Azure als Web-App bereitgestellt wurde. 

> [AZURE.NOTE]Es ist für dieses Lernprogramm wichtig, dass Sie die Schritte unter [Erste Schritte mit dem Azure-Schlüsseltresor](key-vault-get-started.md) abgeschlossen haben , damit Sie über den URI zu einem geheimen Schlüssel und die Client-ID sowie den geheimen Schlüssel für den Client einer Webanwendung verfügen.

Die Webanwendung, die auf den Schlüsseltresor zugreift, ist die in Azure Active Directory registrierte. Ihr wurde Zugriff auf den Schlüsseltresor gewährt. Wenn dies nicht der Fall ist, kehren Sie zur Registrierung der Anwendung im Lernprogramm "Erste Schritte" zurück, und wiederholen Sie die dort aufgeführten Schritte.

Dieses Lernprogramm richtet sich an Webentwickler, die die Grundlagen zum Erstellen von Webanwendungen in Azure kennen. Weitere Informationen zu Azure-Web-Apps finden Sie unter [Web-Apps – Übersicht](../app-service-web-overview.md).



## <a id="packages"></a>Hinzufügen von NuGet-Paketen ##
Drei Pakete müssen für Ihre Webanwendung installiert sein.

- Active Directory-Authentifizierungsbibliothek: enthält Methoden für die Interaktion mit Azure Active Directory und das Verwalten der Benutzeridentität
- Azure-Schlüsseltresorbibliothek: enthält Methoden für die Interaktion mit dem Azure-Schlüsseltresor


Alle drei Pakete können mithilfe der Paket-Manager-Konsole mit dem Befehl "Install-Package" installiert werden.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	//this is a preview version of the Key Vault Library
	Install-Package Microsoft.Azure.KeyVault -Pre


## <a id="webconfig"></a>Ändern der Datei "web.config" ##
Es gibt drei Anwendungseinstellungen, die wie folgt der Datei "web.config" hinzugefügt werden müssen.

	<!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

	<!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Wenn Sie die Anwendung nicht als Azure-Web-App hosten möchten, sollten Sie in der Datei "web.config" die tatsächlichen Werte für Client-ID, geheimen Schlüssel des Clients und URI angeben. Andernfalls belassen Sie diese Platzhalterwerte, da wir die tatsächlichen Werte im Azure-Portal hinzufügen, um die Sicherheit zu erhöhen.


## <a id="gettoken"></a>Hinzufügen einer Methode zum Abrufen eines Zugriffstokens ##
Sie benötigen für die Verwendung der Schlüsseltresor-API ein Zugriffstoken. Der Schlüsseltresor-Client behandelt die Aufrufe an die Schlüsseltresor-API. Sie müssen dafür jedoch eine Funktion angeben, die das Zugriffstoken übernimmt.

Im Folgenden finden Sie den Code für das Abrufen eines Zugriffstokens von Azure Active Directory. Dieser Code kann überall in der Anwendung eingefügt werden. Ich füge dafür i. d. R. eine "Utils"- oder "EncryptionHelper"-Klasse hinzu.

	//add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Web.Configuration;
	
	//this is an optional property to hold the secret after it is retrieved
	public static string EncryptSecret { get; set; }

	//the method that will be provided to the KeyVaultClient
	public async static Task<string> GetToken(string authority, string resource, string scope)
    {
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
	    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	    
	    if (result == null)
	    	throw new InvalidOperationException("Failed to obtain the JWT token");
	    
	    return result.AccessToken;
    }

## <a id="appstart"></a>Abrufen des geheimen Schlüssel beim Anwendungsstart ##
Sie benötigen nun Code zum Aufrufen der Schlüsseltresor-API, um den geheimen Schlüssel abzurufen. Der folgende Code kann an einer beliebigen Stelle eingefügt werden, sofern er aufgerufen wird, bevor er verwendet werden muss. Ich haben diesen Code im "Application Start"-Ereignis in der Datei "Global.asax" eingefügt, sodass es einmal beim Start ausgeführt wird und den geheimen Schlüssel für die Anwendung verfügbar macht.

	//add these using statements
	using Microsoft.Azure.KeyVault;
	using System.Web.Configuration;

	// I put my GetToken method in a Utils class. Change for wherever you placed your method. 
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

	var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;
	
	//I put a variable in a Utils class to hold the secret for general  application use. 
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Hinzufügen von App-Einstellungen im Azure-Portal (optional) ##
Wenn Sie über eine Azure-Web-App verfügen, können Sie jetzt die tatsächlichen Werte für die App-Einstellungen im Azure-Portal hinzufügen. Damit befinden sich die tatsächlichen Werte nicht in der Datei "web.config", sondern sie werden über das Portal geschützt, für das Sie eigene Zugriffssteuerungsfunktionen verwenden können. Diese Werte ersetzen die Werte, die Sie in der Datei "web.config" eingegeben haben. Stellen Sie sicher, dass die Namen übereinstimmen.

![Anwendungseinstellungen im Azure-Portal][1]



## <a id="next"></a>Nächste Schritte ##


Eine Referenz zur Programmierung finden Sie unter [C#-Client-API-Referenz für den Schlüsseltresor](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
 

<!---HONumber=July15_HO2-->