<properties pageTitle="Verwenden des Azure-Schlüsseltresors aus einer Webanwendung | Microsoft Azure" description="In diesem Lernprogramm erfahren Sie, wie Sie den Azure-Schlüsseltresor aus einer Webanwendung verwenden." services="key-vault" documentationCenter="" authors="adamhurwitz" manager="" tags="azure-resource-manager"//>

<tags 
	ms.service="key-vault" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
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
Zwei Pakete müssen für Ihre Webanwendung installiert sein.

- Active Directory-Authentifizierungsbibliothek: enthält Methoden für die Interaktion mit Azure Active Directory und das Verwalten der Benutzeridentität
- Azure-Schlüsseltresorbibliothek: enthält Methoden für die Interaktion mit dem Azure-Schlüsseltresor


Beide Pakete können mithilfe der Paket-Manager-Konsole mit dem Befehl "Install-Package" installiert werden.

	// this is currently the latest stable version of ADAL
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault 


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

> [AZURE.NOTE]Die Verwendung eines geheimen Clientschlüssels und einer Client-ID stellt einfachste Möglichkeit zur Authentifizierung einer Azure AD-Anwendung dar. Die Verwendung in Ihrer Webanwendung erlaubt auch die Trennung von Aufgaben und mehr Kontrolle über die Schlüsselverwaltung. Dafür muss jedoch der geheime Clientschlüssel in die Konfigurationseinstellungen eingefügt werden, was für einige als potenziell riskant angesehen wird. Nachfolgend finden Sie eine Erläuterung zur Verwendung einer Client-ID und eines Zertifikats anstelle von Client-ID und geheimem Clientschlüssel, um die Azure AD-Anwendung zu authentifizieren.



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


## Authentifizieren mit einem Zertifikat anstelle eines geheimen Clientschlüssels 
Eine weitere Möglichkeit zur Authentifizierung einer Azure AD-Anwendung bietet die Verwendung einer Client-ID und eines Zertifikats anstelle einer Client-ID mit dem geheimen Clientschlüssel. Mit den folgenden Schritten können Sie ein Zertifikat in einer Azure-Web-App verwenden:

1. Abrufen oder Erstellen eines Zertifikats
2. Zuordnen des Zertifikats zu einer Azure AD-Anwendung
3. Hinzufügen von Code zur Web-App für die Verwendung des Zertifikats
4. Hinzufügen eines Zertifikats zur Web-App


**Abrufen oder Erstellen eines Zertifikats** Für unsere Zwecke werden wir ein Testzertifikat erstellen. Die folgenden Befehle können Sie an einer Entwicklerbefehlszeile verwenden, um ein Zertifikat zu erstellen. Passen Sie das Verzeichnis an, um den Speicherort für die Zertifikatdateien festzulegen.

	makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
	pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Notieren Sie sich das Enddatum und das Kennwort für die PFX-Datei (in diesem Beispiel: 31.07.2016 und "test123"). Sie benötigen sie später.

Weitere Informationen zum Erstellen eines Testzertifikats finden Sie unter [How to: Create Your Own Test Certificate](https://msdn.microsoft.com/library/ff699202.aspx) (in englischer Sprache).


**Zuordnen des Zertifikats zu einer Azure AD-Anwendung** Nachdem Sie ein Zertifikat erstellt haben, müssen Sie es einer Azure AD-Anwendung zuordnen. Dies wird jedoch noch nicht im Azure-Verwaltungsportal unterstützt. Sie müssen stattdessen PowerShell verwenden. Im Folgenden finden Sie die Befehle, die Sie ausführen müssen:

	$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
	
	PS C:\> $x509.Import("C:\data\KVWebApp.cer")
	
	PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())
	
	PS C:\> $now = [System.DateTime]::Now
	
	# this is where the end date from the cert above is used
	PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31") 
	
	PS C:\> $adapp = New-AzureADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow
	
	PS C:\> $sp = New-AzureADServicePrincipal -ApplicationId $adapp.ApplicationId
	
	PS C:\> Set-AzureKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToKeys all -ResourceGroupName 'contosorg'
	
	# get the thumbprint to use in your app settings
	PS C:\>$x509.Thumbprint

Nachdem Sie diese Befehle ausgeführt haben, wird die Anwendung in Azure AD angezeigt. Wenn die Anwendung nicht sofort angezeigt wird, suchen Sie nach "Anwendungen im Besitz meines Unternehmens" anstelle von "Anwendungen, die mein Unternehmen verwendet".

Weitere Informationen zu Azure AD-Anwendungsobjekten und -Dienstprinzipalobjekten finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](../active-directory/active-directory-application-objects.md)



**Hinzufügen von Code zur Web-App für die Verwendung des Zertifikats** Nun fügen wir der Web-App Code hinzu, um auf das Zertifikat zuzugreifen und es für die Authentifizierung zu verwenden.

Zunächst erstellen wir den Code für den Zugriff auf das Zertifikat.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint, 
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Beachten Sie, dass "StoreLocation" den Wert "CurrentUser" anstelle von "LocalMachine" hat und dass wir "False" bereitstellen, um die Find-Methode zu suchen, da wir ein Testzertifikat verwenden.


Als Nächstes folgt Code, der "CertificateHelper" verwendet und das für die Authentifizierung benötigte "ClientAssertionCertificate" erstellt.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Dies ist der neue Code zum Abrufen des Zugriffstokens. Dieser ersetzt die GetToken-Methode von oben. Ich habe ihr der Einfachheit halber einen anderen Namen gegeben.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

Zur einfacheren Handhabung habe ich all diesen Code in die Utils-Klasse meines Web-App-Projekts eingefügt.

Die letzte Codeänderung betrifft die Application\_Start-Methode. Zunächst müssen wir die GetCert()-Methode aufrufen, um das "ClientAssertionCertificate" zu laden. Außerdem ändern wir die Callback-Methode, die wir beim Erstellen eines neues "KeyVaultClient" bereitstellen. Beachten Sie, dass dies den Code von oben ersetzt.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Hinzufügen eines Zertifikats zur Web-App** Das Hinzufügen eines Zertifikats zur Web-App ist ein einfacher Vorgang in zwei Schritten. Navigieren Sie zunächst im Azure-Portal zu Ihrer Web-App. Klicken Sie auf dem Blatt "Einstellungen" für Ihre Web-App auf den Eintrag "Benutzerdefinierte Domänen und SSL". Auf dem daraufhin geöffneten Blatt können Sie das oben erstellte Zertifikat "KVWebApp.pfx" hochladen. Sie müssen sich unbedingt das Kennwort für die PFX-Datei merken.

![Hinzufügen von Zertifikaten zu Web-Apps im Azure-Portal][2]


Abschließend müssen Sie Ihrer Web-App eine Anwendungseinstellung mit dem Namen "WEBSITE\_LOAD\_CERTIFICATES" und dem Wert "*" hinzufügen. Dadurch wird sichergestellt, dass alle Zertifikate geladen werden. Wenn Sie nur die von Ihnen hochgeladenen Zertifikate laden möchten, können Sie eine durch Kommas getrennte Liste ihrer Fingerabdrücke eingeben.

Weitere Informationen zum Hinzufügen von Zertifikaten zu Web-Apps finden Sie unter [Using Certificates in Azure Websites Applications](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/) (in englischer Sprache).



## <a id="next"></a>Nächste Schritte ##


Eine Referenz zur Programmierung finden Sie unter [C#-Client-API-Referenz für den Schlüsseltresor](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 

<!---HONumber=AcomDC_1223_2015-->