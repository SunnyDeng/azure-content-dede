<properties
   pageTitle="Verschlüsseln und Entschlüsseln von BLOBs in Microsoft Azure Storage per Azure-Schlüsseltresor | Microsoft Azure"
   description="In diesem Lernprogramm werden Sie durch die Schritte zum Verschlüsseln und Entschlüsseln eines BLOBs mithilfe der clientseitigen Verschlüsselung für Microsoft Azure Storage mit Azure Key Vault geführt."
   services="storage"
   documentationCenter=""
   authors="adhurwit"
   manager=""
   editor=""/>

<tags
   ms.service="storage"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/17/2015"
   ms.author="adhurwit"/>

# Verschlüsseln und Entschlüsseln von Blobs in Microsoft Azure Storage per Azure-Schlüsseltresor

## Einführung
 
In diesem Lernprogramm erfahren Sie, wie Sie die clientseitige Speicherverschlüsselung mit Azure Key Vault verwenden. Sie werden durch die Schritte zum Verschlüsseln und Entschlüsseln eines Blobs in einer Konsolenanwendung mit dieser Technologie geführt.

**Geschätzter Zeitaufwand:** 20 Minuten

Eine Übersicht über den Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](key-vault/key-vault-whatis.md)

Übersichtsinformationen zur clientseitigen Verschlüsselung für Azure Storage finden Sie unter [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](storage-client-side-encryption.md).


## Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

- Ein Azure-Speicherkonto
- Visual Studio 2013 oder höher
- Windows Azure PowerShell 


## Übersicht über die clientseitige Verschlüsselung

Eine Übersicht über die clientseitige Verschlüsselung für Azure Storage finden Sie unter [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](storage-client-side-encryption.md).

Hier eine kurze Beschreibung zur Funktionsweise der clientseitigen Verschlüsselung:

1. Das Azure Storage-Client-SDK generiert einen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK), bei dem es sich um einen einmalig verwendeten symmetrischen Schlüssel handelt.
2. Die Kundendaten werden mit diesem CEK verschlüsselt.
3. Der CEK wird dann mit dem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) umschlossen (verschlüsselt). Der KEK wird anhand eines Schlüsselbezeichners identifiziert und kann ein asymmetrisches Schlüsselpaar oder ein symmetrischer Schlüssel sein. Er kann lokal verwaltet oder in Azure Key Vault gespeichert werden. Der Storage-Client hat selbst niemals Zugriff auf den KEK. Er ruft lediglich den Algorithmus für das Umschließen des Schlüssels aus, der vom Schlüsseltresor bereitgestellt wird. Kunden können bei Bedarf benutzerdefinierte Anbieter für das Umschließen von Schlüsseln bzw. das Aufheben dieses Zustands verwenden.
4. Die verschlüsselten Daten werden dann in den Azure Storage-Dienst hochgeladen.


## Einrichten des Azure-Schlüsseltresors
Zum Fortsetzen dieses Lernprogramms müssen Sie die folgenden Schritte ausführen, die im Lernprogramm [Erste Schritte mit dem Azure-Schlüsseltresor](key-vault/key-vault-get-started.md) beschrieben sind.

- Erstellen eines Schlüsseltresors
- Hinzufügen eines Schlüssels oder geheimen Schlüssels zum Schlüsseltresor
- Registrieren einer Anwendung mit Azure Active Directory
- Autorisieren der Anwendung zum Verwenden des Schlüssels oder geheimen Schlüssels

Notieren Sie ClientID und ClientSecret. Diese Angaben werden generiert, wenn Sie eine Anwendung mit Azure Active Directory registrieren.

Erstellen Sie beide Schlüssel im Schlüsseltresor. Im weiteren Verlauf des Lernprogramms setzen wir voraus, dass Sie die folgenden Namen verwendet haben: ContosoKeyVault und TestRSAKey1.


## Erstellen einer Konsolenanwendung mit Paketen und AppSettings

Erstellen Sie in Visual Studio eine neue Konsolenanwendung.

Fügen Sie die erforderlichen NuGet-Pakete in der Paket-Manager-Konsole hinzu.

	Install-Package WindowsAzure.Storage 

	// This is the latest stable release for ADAL.
	Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

	Install-Package Microsoft.Azure.KeyVault 
	Install-Package Microsoft.Azure.KeyVault.Extensions 


Fügen Sie der Datei „App.Config“ AppSettings hinzu.

	<appSettings>
	    <add key="accountName" value="myaccount"/>
	    <add key="accountKey" value="theaccountkey"/>
	    <add key="clientId" value="theclientid"/>
	    <add key="clientSecret" value="theclientsecret"/>
    	<add key="container" value="stuff"/>
	</appSettings>

Fügen Sie die folgenden `using`-Anweisungen hinzu, und stellen Sie sicher, dass Sie dem Projekt einen Verweis auf "System.Configuration" hinzufügen.

	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using System.Configuration;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using Microsoft.Azure.KeyVault;
	using System.Threading;		
	using System.IO;


## Hinzufügen einer Methode zum Abrufen eines Tokens für die Konsolenanwendung

Die folgende Methode wird von Schlüsseltresorklassen verwendet, für die eine Authentifizierung des Zugriffs auf den Schlüsseltresor erforderlich ist.

	private async static Task<string> GetToken(string authority, string resource, string scope)
	{
	    var authContext = new AuthenticationContext(authority);
	    ClientCredential clientCred = new ClientCredential(
	        ConfigurationManager.AppSettings["clientId"], 
	        ConfigurationManager.AppSettings["clientSecret"]);
		AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);
	
	    if (result == null)
	        throw new InvalidOperationException("Failed to obtain the JWT token");
	
	    return result.AccessToken;
	}

## Zugreifen auf den Speicher und den Schlüsseltresor in Ihrem Programm

Fügen Sie in der Main-Funktion den folgenden Code hinzu.

	// This is standard code to interact with Blob storage.
	StorageCredentials creds = new StorageCredentials(
		ConfigurationManager.AppSettings["accountName"],
       	ConfigurationManager.AppSettings["accountKey"]);
	CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
	CloudBlobClient client = account.CreateCloudBlobClient();
	CloudBlobContainer contain = client.GetContainerReference(ConfigurationManager.AppSettings["container"]);
	contain.CreateIfNotExists();

	// The Resolver object is used to interact with Key Vault for Azure Storage.
	// This is where the GetToken method from above is used.
	KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);


> [AZURE.NOTE]Schlüsseltresor-Objektmodelle
>
>Es ist wichtig zu verstehen, dass eigentlich zwei Schlüsseltresor-Objektmodelle vorhanden sind: eins basiert auf der REST-API (KeyVault-Namespace), und das andere ist eine Erweiterung für die clientseitige Verschlüsselung.

> Der Schlüsseltresor-Client interagiert mit der REST-API und erkennt JSON-Webschlüssel und geheime Schlüssel für die beiden Arten von Elementen, die im Schlüsseltresor enthalten sind.

> Die Schlüsseltresor-Erweiterungen sind Klassen, die scheinbar speziell für die clientseitige Verschlüsselung in Azure Storage erstellt wurden. Sie enthalten eine Schnittstelle für Schlüssel (IKey) und Klassen, die auf dem Konzept eines "Key Resolver" basieren. Es gibt zwei Implementierungen von IKey, die Sie kennen müssen: RSAKey und SymmetricKey. Sie kollidieren mit den Elementen, die im Schlüsseltresor enthalten sind, aber an diesem Punkt sind es unabhängige Klassen (Schlüssel und geheimer Schlüssel, die vom Schlüsseltresor-Client abgerufen werden, implementieren IKey also nicht).


## Verschlüsseln des Blobs und Hochladen
Fügen Sie den folgenden Code hinzu, um ein BLOB zu verschlüsseln und in Ihr Azure-Speicherkonto hochzuladen. Die **ResolveKeyAsync**-Methode, die verwendet wird, gibt einen IKey zurück.

	
	// Retrieve the key that you created previously.
	// The IKey that is returned here is an RsaKey.
	// Remember that we used the names contosokeyvault and testrsakey1.
    var rsa = cloudResolver.ResolveKeyAsync("https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", CancellationToken.None).GetAwaiter().GetResult();


	// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy. 
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

	// Reference a block blob.
	CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

	// Upload using the UploadFromStream method.
	using (var stream = System.IO.File.OpenRead(@"C:\data\MyFile.txt"))
		blob.UploadFromStream(stream, stream.Length, null, options, null);


Unten sehen Sie einen Screenshot aus dem aktuellen Azure-Portal für ein BLOB, das per clientseitiger Verschlüsselung mit einem im Schlüsselspeicher gespeicherten Schlüssel verschlüsselt wurde. Die **KeyId**-Eigenschaft ist der URI für den Schlüssel im Schlüsseltresor, der als KEK fungiert. Die **EncryptedKey**-Eigenschaft enthält die verschlüsselte Version des CEK.

![Screenshot mit Blob-Metadaten, die Verschlüsselungsmetadaten enthalten][1]

> [AZURE.NOTE]Wenn Sie sich den BlobEncryptionPolicy-Konstruktor ansehen, wird Ihnen auffallen, dass dafür ein Schlüssel und/oder ein Konfliktlöser (Resolver) verwendet werden kann. Denken Sie daran, dass Sie an diesem Punkt keinen Konfliktlöser für die Verschlüsselung verwenden können, weil dafür derzeit kein Standardschlüssel unterstützt wird.



## Entschlüsseln des Blobs und Herunterladen
Eigentlich findet Entschlüsselung statt, wenn die Verwendung der Resolver-Klassen sinnvoll ist. Die ID des Schlüssels, der für die Verschlüsselung verwendet wird, wird dem BLOB in seinen Metadaten zugeordnet. Es gibt für Sie also keinen Grund, den Schlüssel abzurufen und sich die Zuordnung zwischen Schlüssel und BLOB zu merken. Sie müssen lediglich sicherstellen, dass der Schlüssel im Schlüsseltresor erhalten bleibt.

Der private Schlüssel eines RSA-Schlüssels verbleibt im Schlüsseltresor. Damit die Entschlüsselung durchgeführt werden kann, wird der verschlüsselte Schlüssel aus den BLOB-Metadaten, der den CEK enthält, zur Entschlüsselung an den Schlüsseltresor gesendet.

Fügen Sie Folgendes hinzu, um das Blob zu entschlüsseln, das Sie gerade hochgeladen haben.

	// In this case, we will not pass a key and only pass the resolver because
	// this policy will only be used for downloading / decrypting.
	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

    using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
	    blob.DownloadToStream(np, null, options, null);


> [AZURE.NOTE]Es sind einige andere Arten von Konfliktlösern (Resolvers) vorhanden, um die Schlüsselverwaltung zu vereinfachen, z. B. AggregateKeyResolver und CachingKeyResolver.


## Verwenden von geheimen Schlüsseltresor-Schlüsseln
Für die Verwendung eines geheimen Schlüssels mit clientseitiger Verschlüsselung wird die SymmetricKey-Klasse genutzt, da ein geheimer Schlüssel im Wesentlichen ein symmetrischer Schlüssel ist. Wie oben erwähnt, wird ein geheimer Schlüssel im Schlüsseltresor nicht exakt einem "SymmetricKey" zugeordnet. Hierbei sind einige Dinge zu beachten:


- Der Schlüssel in einem "SymmetricKey" muss eine feste Länge haben: 128, 192, 256, 384 oder 512 Bit.
- Der Schlüssel in einem "SymmetricKey" sollte über eine Base64-Codierung verfügen.
- Ein geheimer Schlüsseltresor-Schlüssel, der als "SymmetricKey" verwendet wird, muss im Schlüsseltresor den Inhaltstyp "application/octet-stream" aufweisen.

Hier ist ein Beispiel in PowerShell angegeben, bei dem im Schlüsseltresor ein geheimer Schlüssel erstellt wird, der als "SymmetricKey" verwendet werden kann.

	// Here we are making a 128-bit key so we have 16 characters. 
	// 	The characters are in the ASCII range of UTF8 so they are
	//	each 1 byte. 16 x 8 = 128.
	$key = "qwertyuiopasdfgh"
	$b = [System.Text.Encoding]::UTF8.GetBytes($key)
	$enc = [System.Convert]::ToBase64String($b)
	$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

	// Substitute the VaultName and Name in this command.
	$secret = Set-AzureKeyVaultSecret -VaultName 'ContoseKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"

In Ihrer Konsolenanwendung können Sie den gleichen Aufruf wie vorher verwenden, um diesen geheimen Schlüssel als "SymmetricKey" abzurufen.

	SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    	"https://contosokeyvault.vault.azure.net/secrets/TestSecret2/", 
        CancellationToken.None).GetAwaiter().GetResult();

Das ist alles. Viel Spaß!

## Nächste Schritte

Weitere Informationen zur Verwendung von Microsoft Azure Storage mit C# finden Sie unter [Microsoft Azure-Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

Weitere Informationen zur BLOB-REST-API finden Sie unter [REST-API des Blob-Diensts](https://msdn.microsoft.com/library/azure/dd135733.aspx).

Aktuelle Informationen zu Microsoft Azure Storage finden Sie im [Microsoft Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/) (in englischer Sprache).


<!--Image references-->
[1]: ./media/storage-encrypt-decrypt-blobs-key-vault/blobmetadata.png

<!---HONumber=AcomDC_1125_2015-->