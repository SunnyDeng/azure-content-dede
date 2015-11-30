<properties 
	pageTitle="Shared Access Signatures: Grundlagen zum SAS-Modell | Microsoft Azure" 
	description="Erfahren Sie mehr zum Delegieren des Zugriffs auf Azure-Speicherressourcen, einschließlich Blobs, Warteschlangen, Tabellen und Dateien mithilfe von Shared Access Signatures (SAS). Mit SAS können Sie Ihren Speicherkontoschlüssel schützen, während Sie anderen Benutzern Zugriff auf Ressourcen in Ihrem Konto gewähren. Sie können die Berechtigungen, die Sie gewähren, und das für das SAS gültige Intervall steuern. Wenn Sie auch eine gespeicherte Zugriffsrichtlinie einrichten, können Sie die SAS widerrufen, sollten Sie sich um die Sicherheit Ihres Kontos fürchten." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="tamram"/>



# Shared Access Signatures, Teil 1: Grundlagen zum SAS-Modell

## Übersicht

Shared Access Signatures (SAS) sind eine leistungsstarke Methode, um anderen Clients eingeschränkten Zugriff auf Objekte in Ihrem Speicherkonto zu bieten, ohne Ihren Kontoschlüssel weitergeben zu müssen. Teil 1 dieses Lernprogramms zu Shared Access Signatures bietet eine Übersicht über das SAS-Modell und bewährte Methoden für SAS. [Teil 2](storage-dotnet-shared-access-signature-part-2.md) des Lernprogramms beschreibt die Erstellung von Shared Access Signatures mit dem Blob-Dienst.

## Was ist eine Shared Access Signature?

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihren Speicherkonto. Sie haben die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen. Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Für den Zugriff auf Speicherressourcen mit der SAS braucht der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode zu übergeben.

## Wann sollten Sie eine Shared Access Signature verwenden?

Sie können eine SAS verwenden, um einem Client Zugriff auf Ressourcen in Ihrem Speicherkonto zu bieten, dem Sie Ihren Kontoschlüssel nicht anvertrauen möchten. Ihre Speicherkontoschlüssel bestehen aus Primär- und Sekundärschlüssel. Beide bieten Administratorzugriff auf Ihr Konto und alle enthaltenen Ressourcen. Wenn Sie Ihre Kontoschlüssel weitergeben, besteht die Gefahr von böswilliger oder fahrlässiger Nutzung. Shared Access Signatures bieten eine sichere alternative, um anderen Clients Lese-, Schreib- und Löschzugriff auf Daten in Ihrem Speicherkonto gemäß der von Ihnen definierten Berechtigungen zu bieten, ohne den Kontoschlüssel weitergeben zu müssen.

SAS sind zum Beispiel dann hilfreich, wenn Benutzer ihre eigenen Daten in Ihrem Speicherkonto ablegen und von dort abrufen. Für den Fall, dass ein Speicherkonto Benutzerdaten enthält, existieren zwei typische Designmuster:


1\. Clients laden Daten über einen Front-End-Proxydienst hoch und herunter, der die Authentifizierung übernimmt. Dieser Front-End-Proxydienst hat den Vorteil, dass auch Geschäftsregeln validiert werden können. Allerdings kann die Erstellung eines skalierbaren Dienstes für große Datenmengen oder Transaktionen mit großem Volumen teuer und aufwändig sein.

![sas-storage-fe-proxy-service][sas-storage-fe-proxy-service]

2\. Ein vereinfachter Dienst authentifiziert den Client bei Bedarf und generiert anschließend die SAS. Sobald der Client die SAS erhält, kann er direkt gemäß der in der SAS definierten Berechtigungen für den definierten Zeitraum auf die Speicherkonto-Ressourcen zugreifen. Dank der SAS müssen nicht mehr alle Daten durch einen Front-End-Proxydienst geleitet werden.

![sas-storage-provider-service][sas-storage-provider-service]

Viele tatsächliche Dienste verwenden je nach Szenario eine Mischung aus beiden Ansätzen, verarbeiten und validieren einige Daten im Front-End-Proxy und speichern und/oder lesen andere Daten direkt per SAS.

Darüber hinaus müssen Sie eine SAS verwenden, um das Quellobjekt in einen Kopiervorgang in bestimmten Szenarien zu authentifizieren:

- Beim Kopieren eines Blobs in ein anderes Blob, das sich in einem anderen Speicherkonto befindet, müssen Sie eine SAS verwenden, um das Quell-Blob zu authentifizieren. Mit Version 2015-04-05 können Sie optional eine SAS verwenden, um auch das Ziel-Blob zu authentifizieren.
- Beim Kopieren einer Datei in eine andere Datei, die sich in einem anderen Speicherkonto befindet, müssen Sie eine SAS verwenden, um die Quelldatei zu authentifizieren. Mit Version 2015-04-05 können Sie optional eine SAS verwenden, um auch die Zieldatei zu authentifizieren.
- Wenn Sie ein Blob in eine Datei oder eine Datei in ein Blob kopieren, müssen Sie eine SAS verwenden, um das Quellobjekt zu authentifizieren. Dies gilt selbst dann, wenn sich die Quell- und Zielobjekte innerhalb desselben Speicherkontos befinden.

>[AZURE.NOTE]Die Konto-SAS wird derzeit nur für den Blob- und Dateidienst unterstützt. In naher Zukunft wird sie auch für den Tabellenspeicher- und Warteschlangendienst unterstützt.

## Arten von Shared Access Signatures

Mit Version 2015-04-05 von Azure Storage wird eine neue Art von Shared Access Signature eingeführt: die Konto-SAS. Sie können jetzt zwei Arten von Shared Access Signatures erstellen:

- **Konto-SAS:** Die Konto-SAS delegiert den Zugriff auf Ressourcen in einem oder mehreren Speicherdiensten. Alle Vorgänge, die über eine Dienst-SAS verfügbar sind, sind auch über eine Konto-SAS verfügbar. Mit der Konto-SAS können Sie auch den Zugriff auf Vorgänge delegieren, die für einen bestimmten Dienst gelten, z. B. **Get/Set Service Properties** und **Get Service Stats**. Sie können auch den Zugriff auf Lese-, Schreib- und Löschvorgänge in Blob-Containern, Tabellen, Warteschlangen und Dateifreigaben delegieren, die mit einer Dienst-SAS nicht zulässig sind. Ausführliche Informationen zur Erstellung des Konto-SAS-Tokens finden Sie unter [Erstellen einer Konto-SAS](https://msdn.microsoft.com/library/mt584140.aspx).

- **Dienst-SAS:** Die Dienst-SAS delegiert den Zugriff auf eine Ressource in nur einem der Speicherdienste: Blob-, Warteschlangen-, Tabellen- oder Dateidienst. Ausführliche Informationen zur Erstellung des Dienst-SAS-Tokens finden Sie unter [Erstellen einer Dienst-SAS](https://msdn.microsoft.com/library/dn140255.aspx) und [Dienst-SAS-Beispiele](https://msdn.microsoft.com/library/dn140256.aspx).

## Funktionsweise von Shared Access Signatures

Eine Shared Access Signature ist ein URI, der auf eine oder mehrere Speicherressourcen verweist und über ein Token verfügt, das einen speziellen Satz von Abfrageparametern enthält. Das Token gibt an, wie der Client auf die Ressourcen zugreifen kann. Einer der Abfrageparameter ist die Signatur. Sie besteht aus den SAS-Parametern und wird mit dem Kontoschlüssel signiert. Der Azure-Speicher verwendet die Signatur, um die SAS zu authentifizieren.

Die Konto-SAS- und Dienst-SAS-Token enthalten einige gemeinsame Parameter sowie einige Parameter, die unterschiedlich sind.

### Gemeinsame Parameter von Konto-SAS- und Dienst-SAS-Token

- **API-Version:** Ein optionaler Parameter zum Angeben der Speicherdienstversion, die zum Ausführen der Anforderung verwendet wird. 
- **Dienstversion:** Ein erforderlicher Parameter zum Angeben der Speicherdienstversion, die zum Authentifizieren der Anforderung verwendet wird.
- **Startzeit.** Dies ist der Zeitpunkt, ab dem die SAS gültig ist. Die Startzeit für eine Shared Access Signature ist optional. Wenn die Startzeit nicht angegeben wird, ist die SAS sofort gültig. 
- **Ablaufzeit.** Dies ist der Zeitpunkt, ab dem die SAS nicht mehr gültig ist. Sie sollten nach Möglichkeit entweder eine Ablaufzeit für die SAS angeben oder diese mit einer gespeicherten Zugriffsrichtlinie verknüpfen (siehe unten).
- **Berechtigungen.** Die in der SAS angegebenen Berechtigungen geben an, welche Operationen der Client mit der SAS auf der Speicherressource ausführen kann. Die verfügbaren Berechtigungen unterscheiden sich für eine Konto-SAS und eine Dienst-SAS.
- **IP:** Ein optionaler Parameter, der eine IP-Adresse oder einen Bereich von IP-Adressen außerhalb von Azure angibt (siehe Abschnitt [Konfigurationszustand der Routingsitzung](../expressroute/expressroute-workflows.md#routing-session-configuration-state) für Express Route), aus dem Anforderungen angenommen werden. 
- **Protokoll:** Ein optionaler Parameter zum Angeben des Protokolls, das für eine Anforderung zulässig ist. Mögliche Werte sind HTTPS und HTTP (https,http), der Standardwert, oder nur HTTPS (https). Beachten Sie, dass HTTP allein kein zulässiger Wert ist.
- **Signatur:** Die Signatur wird aus den anderen Parametern erstellt, die als Teiltoken angegeben sind, und dann verschlüsselt. Sie wird zum Authentifizieren der SAS verwendet.

### Parameter für ein Konto-SAS-Token

- **Dienst oder Dienste:** Eine Konto-SAS kann den Zugriff auf einen oder mehrere Speicherdienste delegieren. Sie können beispielsweise eine Konto-SAS erstellen, mit der der Zugriff auf den Blob- und Dateidienst delegiert wird. Oder Sie können eine SAS erstellen, mit der der Zugriff auf alle vier Dienste delegiert wird (Blob, Warteschlange, Tabelle und Datei).
- **Speicherressourcenarten:** Eine Konto-SAS gilt für eine oder mehrere Klassen von Speicherressourcen und nicht für eine bestimmte Ressource. Sie können eine Konto-SAS erstellen, um den Zugriff für Folgendes zu delegieren:
	- APIs auf Dienstebene, die für die Speicherkontoressource aufgerufen werden. Beispiele hierfür sind **Get/Set Service Properties**, **Get Service Stats** und **List Containers/Queues/Tables/Shares**.
	- APIs auf Containerebene, die für die Containerobjekte für jeden Dienst aufgerufen werden: Blob-Container, Warteschlangen, Tabellen und Dateifreigaben. Beispiele hierfür sind **Create/Delete Container**, **Create/Delete Queue**, **Create/Delete Table**, **Create/Delete Share** und **List Blobs/Files and Directories**.
	- APIs auf Objektebene, die für Blobs, Warteschlangennachrichten, Tabellenentitäten und Dateien aufgerufen werden. Beispiele hierfür sind **Put Blob**, **Query Entity**, **Get Messages** und **Create File**.

### Parameter für ein Dienst-SAS-Token

- **Speicherressource:** Speicherressourcen, für die Sie den Zugriff mit einer Dienst-SAS delegieren können, umfassen:
	- Container und Blobs
	- Dateifreigaben und Dateien
	- Warteschlangen
	- Tabellen und Bereiche von Tabellenentitäten.

## Beispiele für SAS-URIs

Dies ist ein Beispiel für einen Dienst-SAS-URI, der Lese- und Schreibzugriff für einen Blob gewährt. Die Tabelle analysiert die einzelnen Teile des URI, um dessen Zusammenhang mit der SAS zu erläutern:

	https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2015-04-05&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

Name|SAS-Teil|Beschreibung
---|---|---
Blob-URI|https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt |Die Blob-Adresse. Sie sollten unbedingt HTTPS verwenden.
Version des Speicherdienstes|sv=2015-04-05|Für Speicherdienste der Version 2012-02-12 und später gibt dieser Parameter die zu verwendende Version an.
Startzeit|st=2015-04-29T22%3A18%3A26Z|Angegeben im ISO 8601-Format. Lassen Sie diesen Parameter aus, wenn die SAS sofort gültig sein soll.
Ablaufzeit|se=2015-04-30T02%3A23%3A26Z|Angegeben im ISO 8601-Format.
Ressource|sr=b|Die Ressource ist ein Blob.
Berechtigungen|sp=rw|Die SAS verleiht die Berechtigungen zum Lesen (r) und Schreiben (w).
IP-Bereich|sip=168.1.5.60-168.1.5.70|Der Bereich der IP-Adressen, von denen eine Anforderung akzeptiert wird.
Protokoll|spr=https|Nur Anforderungen per HTTPS sind zulässig.
Signatur|sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D|Wird zur Zugriffsauthentifizierung für den Blob verwendet. Die Signatur ist ein HMAC, der mithilfe des SHA256-Algorithmus über StringToSign-Zeichenfolge und Schlüssel erstellt und anschließend mit Base64 codiert wird.

Hier ist ein Beispiel für eine Konto-SAS angegeben, bei der die gleichen allgemeinen Parameter im Token verwendet werden. Da diese Parameter oben beschrieben sind, werden Sie hier nicht noch einmal beschrieben. Lediglich die Parameter, die speziell für Konto-SAS gelten, werden unten in der Tabelle erläutert.

	https://myaccount.blob.core.windows.net/?restype=service&comp=properties&sv=2015-04-05&ss=bf&srt=s&st=2015-04-29T22%3A18%3A26Z&se=2015-04-30T02%3A23%3A26Z&sr=b&sp=rw&sip=168.1.5.60-168.1.5.70&spr=https&sig=F%6GRVAZ5Cdj2Pw4tgU7IlSTkWgn7bUkkAg8P6HESXwmf%4B

Name|SAS-Teil|Beschreibung
---|---|---
Ressourcen-URI|https://myaccount.blob.core.windows.net/?restype=service&comp=properties|The Blob-Dienstendpunkt mit Parametern zum Abrufen von Diensteigenschaften (bei Aufruf mit GET) oder Festlegen von Diensteigenschaften (bei Aufruf mit SET).
Dienste|ss=bf|Die SAS gilt für die Blob- und Dateidienste.
Ressourcentypen|srt=s|Die SAS gilt für Vorgänge auf Dienstebene.
Berechtigungen|sp=rw|Die Berechtigungen gewähren Zugriff auf Lese- und Schreibvorgänge.  

Da die Berechtigungen auf die Dienstebene beschränkt sind, sind mit dieser SAS die Vorgänge **Get Blob Service Properties** (Lesen) und **Set Blob Service Properties** (Schreiben) zugänglich. Mit einem anderen Ressourcen-URI kann das gleiche SAS-Token aber auch verwendet werden, um den Zugriff auf **Get Blob Service Stats** (Lesen) zu delegieren.

## Steuern einer SAS mit einer gespeicherten Zugriffsrichtlinie ##

Shared Access Signatures können zwei unterschiedliche Formen haben:

- **Ad-Hoc-SAS:** Beim Erstellen von Ad-Hoc-SAS werden Startzeit, Ablaufzeit und Berechtigungen für die SAS direkt im SAS-URI angegeben (bzw. impliziert, falls die Startzeit ausgelassen wird). Diese Art von SAS kann als Konto-SAS oder Dienst-SAS erstellt werden. 

- **SAS mit gespeicherter Zugriffsrichtlinie:** Gespeicherte Zugriffsrichtlinien werden für Ressourcencontainer – Blob-Container, Tabellen, Warteschlangen oder Dateifreigaben – definiert und dienen zur Verwaltung von Einschränkungen für eine oder mehrere Shared Access Signatures. Wenn Sie eine SAS mit einer gespeicherten Zugriffsrichtlinie verknüpfen, erbt die SAS die Einschränkungen (Startzeit, Ablaufzeit und Berechtigungen) dieser gespeicherten Zugriffsrichtlinie.

>[AZURE.NOTE]Derzeit muss eine Konto-SAS eine Ad-hoc-SAS sein. Gespeicherte Zugriffsrichtlinien werden für Konto-SAS noch nicht unterstützt.

Der Unterschied zwischen diesen beiden Formen ist wichtig für ein Schlüsselszenario: Widerruf. Eine SAS ist eine URL und kann daher von beliebiger Stelle verwendet werden, unabhängig davon, wer die SAS ursprünglich angefordert hatte. Wenn eine SAS veröffentlicht wird, kann diese von beliebiger Stelle weltweit verwendet werden. Auf diese Weise verteilte SAS sind gültig, bis eines von vier Ereignissen eintritt:

1.	Die Ablaufzeit der SAS wird erreicht.
2.	Die Ablaufzeit der von der SAS referenzierten gespeicherten Zugriffsrichtlinie wird erreicht (falls eine gespeicherte Zugriffsrichtlinie referenziert wurde und diese eine Ablaufzeit definiert). Dies geschieht entweder, wenn das Zeitintervall abläuft, oder wenn Sie die Ablaufzeit der gespeicherten Zugriffsrichtlinie auf einen Zeitpunkt in der Vergangenheit gesetzt haben, um die SAS zu widerrufen.
3.	Die von der SAS referenzierte gespeicherte Zugriffsrichtlinie wird gelöscht, wodurch die SAS ebenfalls widerrufen wird. Wenn Sie die gespeicherte Zugriffsrichtlinie mit demselben Namen erneut erstellen, sind alle existierenden SAS-Tokens gemäß der Berechtigungen der gespeicherten Zugriffsrichtlinie wieder gültig (sofern die Ablaufzeit der SAS noch nicht erreicht wurde). Falls Sie die SAS widerrufen möchten, müssen Sie einen anderen Namen verwenden, wenn Sie die gespeicherte Zugriffsrichtlinie mit einer Ablaufzeit in der Zukunft erneut erstellen.
4.	Der Kontoschlüssel, mit dem die SAS erstellt wurde, wird erneut generiert. Dies führt dazu, dass die Authentifikation aller Anwendungskomponenten mit diesem Kontoschlüssel fehlschlägt, bis diese entweder mit dem anderen gültigen Kontoschlüssel oder dem neu generierten Kontoschlüssel aktualisiert werden.

>[AZURE.IMPORTANT]Ein SAS-URI wird dem Kontoschlüssel, mit dem die Signatur erstellt wurde, und der zugehörigen gespeicherten Zugriffsrichtlinie (sofern vorhanden) zugeordnet. Wenn keine gespeicherte Zugriffsrichtlinie angegeben wird, kann eine SAS nur durch Änderung des Kontoschlüssels aufgehoben werden.

## Beispiele: Erstellen und Verwenden von SAS (Shared Access Signatures)

Unten sind einige Beispiele für beide Arten von Shared Access Signatures aufgeführt: Konto-SAS und Dienst-SAS.

Zum Ausführen dieser Beispiele müssen Sie diese Pakete herunterladen und darauf verweisen:

- [Azure Storage-Clientbibliothek für .NET](http://www.nuget.org/packages/WindowsAzure.Storage), Version 6.x oder höher (zur Verwendung von Konto-SAS).
- [Azure-Konfigurations-Manager](http://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) 

### Beispiel: Konto-SAS

Im folgenden Codebeispiel wird eine Konto-SAS erstellt, die für die Blob- und Dateidienste gültig ist und dem Client die Berechtigungen Lesen, Schreiben und Auflisten für den Zugriff auf APIs auf Dienstebene gewährt. Bei der Konto-SAS ist das Protokoll auf HTTPS beschränkt, sodass die Anforderung mit HTTPS durchgeführt werden muss.

    static string GetAccountSASToken()
    {
        // To create the account SAS, you need to use your shared key credentials. Modify for your account.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create a new access policy for the account.
        SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
            {
                Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
                Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
                ResourceTypes = SharedAccessAccountResourceTypes.Service,
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
                Protocols = SharedAccessProtocol.HttpsOnly
            };

        // Return the SAS token.
        return storageAccount.GetSharedAccessSignature(policy);
    }

Um die Konto-SAS zum Zugreifen auf APIs auf Dienstebene für den Blob-Dienst zu verwenden, erstellen Sie ein Blob-Clientobjekt, indem Sie die SAS und den Blob-Speicherendpunkt für Ihr Speicherkonto verwenden.

    static void UseAccountSAS(string sasToken)
    {
        // In this case, we have access to the shared key credentials, so we'll use them
        // to get the Blob service endpoint.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create new storage credentials using the SAS token.
        StorageCredentials accountSAS = new StorageCredentials(sasToken);
        // Use these credentials and the Blob storage endpoint to create a new Blob service client.
        CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, blobClient.StorageUri, null, null, null);
        CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

        // Now set the service properties for the Blob client created with the SAS.
        blobClientWithSAS.SetServiceProperties(new ServiceProperties()
        {
            HourMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            MinuteMetrics = new MetricsProperties()
            {
                MetricsLevel = MetricsLevel.ServiceAndApi,
                RetentionDays = 7,
                Version = "1.0"
            },
            Logging = new LoggingProperties()
            {
                LoggingOperations = LoggingOperations.All,
                RetentionDays = 14,
                Version = "1.0"
            }
        });

        // The permissions granted by the account SAS also permit you to retrieve service properties.
        ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
        Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
        Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
        Console.WriteLine(serviceProperties.HourMetrics.Version);
    }

### Beispiel: Dienst-SAS mit gespeicherter Zugriffsrichtlinie

Im folgenden Codebeispiel wird eine gespeicherte Zugriffsrichtlinie für einen Container erstellt. Anschließend wird eine Dienst-SAS für den Container generiert. Diese SAS kann dann an Clients weitergegeben werden, um sie mit Lese-/Schreibberechtigungen für den Container auszustatten. Ändern Sie den Code, um Ihren eigenen Kontonamen zu verwenden:

    // Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the storage account with the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
       
    // Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
    // Get a reference to the container for which shared access signature will be created.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    container.CreateIfNotExists();
    
    // Get the current permissions for the blob container.
    BlobContainerPermissions blobPermissions = container.GetPermissions();

    // Clear the container's shared access policies to avoid naming conflicts.
    blobPermissions.SharedAccessPolicies.Clear();
    
    // The new shared access policy provides read/write access to the container for 24 hours.
    blobPermissions.SharedAccessPolicies.Add("mypolicy", new SharedAccessBlobPolicy()
    {
       // To ensure SAS is valid immediately, don’t set the start time.
       // This way, you can avoid failures caused by small clock differences.
       SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
       Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Add
    });
    
    // The public access setting explicitly specifies that 
    // the container is private, so that it can't be accessed anonymously.
    blobPermissions.PublicAccess = BlobContainerPublicAccessType.Off;
    
    // Set the new stored access policy on the container.
    container.SetPermissions(blobPermissions);
    
    // Get the shared access signature token to share with users.
    string sasToken =
       container.GetSharedAccessSignature(new SharedAccessBlobPolicy(), "mypolicy");

Ein Client, der im Besitz der Dienst-SAS ist, kann diese über seinen Code verwenden, um eine Lese- oder Schreibanforderung für ein Blob im Container zu authentifizieren. Im folgenden Code wird das SAS-Token genutzt, um ein neues Blockblob im Container zu erstellen. Ändern Sie den Code, um Ihren eigenen Kontonamen zu verwenden:

    Uri blobUri = new Uri("https://<myaccount>.blob.core.windows.net/mycontainer/myblob.txt");
    
    // Create credentials with the SAS token. The SAS token was created in previous example.
    StorageCredentials credentials = new StorageCredentials(sasToken);
    
    // Create a new blob.
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, credentials);
    
    // Upload the blob. 
    // If the blob does not yet exist, it will be created. 
    // If the blob does exist, its existing content will be overwritten.
    using (var fileStream = System.IO.File.OpenRead(@"c:\Temp\myblob.txt"))
    {
    	blob.UploadFromStream(fileStream);
    }


## Bewährte Methoden für die Verwendung von Shared Access Signatures

Wenn Sie Shared Access Signatures in Ihren Anwendungen verwenden, müssen Sie sich der potenziellen Risiken bewusst sein:

- Weitergegebene SAS können von beliebiger Stelle verwendet werden, was die Sicherheit Ihres Speicherkontos gefährden kann.
- Wenn die SAS einer Clientanwendung abläuft und die Anwendung keine neue SAS von Ihrem Dienst abrufen kann, wird die Funktionsweise der Anwendung möglicherweise eingeschränkt.  

Mit den folgenden Empfehlungen für die Verwendung von Shared Access Signatures können Sie diese Risiken mindern:

1. **Verwenden Sie immer HTTPS** für die Erstellung und Verteilung von SAS. Wenn eine SAS über HTTP weitergegeben und abgefangen wird, kann ein Angreifer mit einem Man-in-the-Middle-Angriff diese auslesen, anschließend im Namen des Benutzers verwenden und somit möglicherweise sensible Daten gefährden oder böswillig beschädigen.
2. **Verweisen Sie nach Möglichkeit auf gespeicherte Zugriffsrichtlinien.** Mit gespeicherten Zugriffsrichtlinien können Sie Berechtigungen widerrufen, ohne die Schlüssel für das Speicherkonto neu generieren zu müssen. Setzen Sie das Ablaufdatum dieser Richtlinien weit in die Zukunft (oder unendlich) und stellen Sie sicher, dass das Datum regelmäßig aktualisiert und weiter in die Zukunft verschoben wird.
3. **Verwenden Sie Ablaufdaten in naher Zukunft für Ad-Hoc-SAS.** Auf diese Weise sind SAS nur für kurze Zeit gültig, selbst wenn ihre Sicherheit auf unbekannte Weise gefährdet ist. Dies ist besonders dann wichtig, wenn Sie nicht auf eine gespeicherte Zugriffsrichtlinie verweisen können. Auf diese Weise schränken Sie auch die Datenmenge ein, die in einen Blob geschrieben werden kann, indem Sie die verfügbare Uploadzeit verkürzen.
4. **Sorgen Sie dafür, dass die Clients die SAS bei Bedarf automatisch erneuern müssen.** Die Clients sollten ihre SAS rechtzeitig vor der erwarteten Ablaufzeit erneuern, um Zeit für Wiederholungsversuche zu bieten, falls der entsprechende Dienst nicht verfügbar sein sollte. Falls Ihre SAS für eine kleine Anzahl sofortiger und kurzfristiger Operationen gilt, die normalerweise innerhalb der gegebenen Ablaufzeit abgeschlossen werden, ist dies möglicherweise nicht notwendig, da die SAS nicht erneuert werden müssen. Wenn Ihre Clients jedoch immer wieder Anfragen über die SAS stellen, müssen Sie sich mit dem Ablaufmechanismus auseinander setzen. Dabei müssen Sie einen Ausgleich zwischen der Notwendigkeit einer kurzlebigen SAS (wie oben beschrieben) und dem Bedarf des Clients schaffen, diese rechtzeitig zu erneuern, um zu verhindern, dass die SAS vor der erfolgreichen Erneuerung abläuft.
5. **Seien Sie vorsichtig mit der SAS-Startzeit.** Wenn Sie die Startzeit einer SAS auf **jetzt** setzen, können aufgrund von Zeitunterschieden zwischen unterschiedlichen Computern in den ersten Minuten Probleme auftreten. Daher sollten Sie die Startzeit mindestens 15 Minuten in der Vergangenheit setzen oder auch auslassen, damit diese in allen Fällen sofort gültig ist. Dasselbe gilt für die Ablaufzeit. Rechnen Sie immer mit Zeitunterschieden von bis zu 15 Minuten in beide Richtungen. Für Clients mit einer REST-Version vor 2012-02-12 ist die maximale Dauer von SAS, die keine gespeicherte Zugriffsrichtlinie referenzieren, eine Stunde. Alle Richtlinien, die längere Zeiträume verwenden, schlagen fehl.
6.	**Geben Sie die freigegebene Ressource exakt an.** Normalerweise sollten Sie Benutzern immer ein Minimum an benötigten Privilegien erteilen. Wenn ein Benutzer nur Lesezugriff auf eine einzige Entität benötigt, dann geben Sie auch nur Lesezugriff auf diese Entität, und nicht Lese-/Schreib-/Löschzugriff auf alle Entitäten. Auf diese Weise mindern Sie auch die Missbrauchsgefahr für Ihre SAS, da diese in den Händen eines Angreifers weniger mächtig ist.
7.	**Beachten Sie, dass Ihnen jegliche Nutzung Ihres Kontos berechnet wird, inklusive der Nutzung über SAS.** Wenn Sie Schreibzugriff für einen Blob vergeben, können Benutzer Blobs mit bis zu 200GB hochladen. Falls Sie außerdem noch Lesezugriff vergeben, können die Benutzer die Daten bis zu 10 mal herunterladen und Gebühren für den Datenausgang von bis zu 2TB verursachen. Vergeben Sie also eingeschränkte Berechtigungen, um das Schadenspotenzial böswilliger Benutzer einzugrenzen. Verwenden Sie kurzlebige SAS, um diese Bedrohung zu mindern (beachten Sie jedoch mögliche Zeitunterschiede bei der Ablaufzeit).
8.	**Überprüfen Sie Daten, die per SAS geschrieben wurden.** Wenn Clientanwendungen Daten in Ihr Speicherkonto schreiben, müssen Sie stets beachten, dass diese Daten problembehaftet sein können. Wenn Ihre Anwendung diese Daten vor der Verwendung validieren oder autorisieren muss, sollten Sie diese Validierung durchführen, nachdem die Daten geschrieben und bevor sie von Ihrer Anwendung verwendet werden. Auf diese Weise schützen Sie Ihr Konto auch vor beschädigten oder bösartigen Daten, sowohl von tatsächlich berechtigten SAS-Benutzern als auch von Angreifern, die eine abgefangene SAS verwenden.
9. **Verwenden Sie SAS nicht in jedem Fall.** Manchmal überwiegen die Risiken für eine bestimmte Operation auf Ihrem Speicherkonto den Nutzen von SAS. Für solche Operation sollten Sie einen Dienst auf der mittleren Ebene erstellen, der zunächst Geschäftsregeln validiert sowie Authentifizierung und Überwachung durchführt und die Daten anschließend in Ihr Speicherkonto schreibt. Manchmal gibt es auch einfachere Möglichkeiten der Zugriffsverwaltung. Wenn Sie z. B. alle Blobs in einem Container öffentlich lesbar machen möchten, können Sie auch den Container öffentlich machen, anstatt jedem Client für den Zugriff eine SAS zu geben.
10.	**Überwachen Sie Ihrer Anwendung mithilfe der Speicheranalyse.** Sie können Häufungen von Authentifizierungsfehlern aufgrund von Ausfällen Ihres SAS-Dienstes oder einer unbeabsichtigt gelöschten gespeicherten Zugriffsrichtlinie mithilfe von Protokollierung und Metriken beobachten. Weitere Informationen finden Sie im [Blog des Azure-Speicherteams](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx).

## Zusammenfassung ##

Shared Access Signatures sind nützlich für die Vergabe eingeschränkter Berechtigungen für Ihr Speicherkonto an Clients, die keinen Zugriff auf Ihren Kontoschlüssel haben dürfen. Somit sind sie ein wichtiger Teil des Sicherheitsmodells für alle Anwendungen, die den Azure-Speicher verwenden. Wenn Sie den hier genannten Empfehlungen folgen, können Sie mit SAS eine größere Flexibilität für den Zugriff auf Ressourcen in Ihrem Speicherkonto erreichen, ohne die Sicherheit Ihrer Anwendung zu gefährden.

## Nächste Schritte ##

- [Shared Access Signatures, Teil 2: Erstellen und Verwenden einer SAS mit dem Blobdienst](storage-dotnet-shared-access-signature-part-2.md)
- [Verwenden des Azure-Dateispeichers unter Windows](storage-dotnet-how-to-use-files.md)
- [Verwalten des Zugriffs auf Azure Storage-Ressourcen](storage-manage-access-to-resources.md)
- [Delegieren des Zugriffs mit einer Shared Access Signature](http://msdn.microsoft.com/library/azure/ee395415.aspx)
- [Einführung in Tabellen- und Warteschlangen-SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
[sas-storage-fe-proxy-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-fe-proxy-service.png
[sas-storage-provider-service]: ./media/storage-dotnet-shared-access-signature-part-1/sas-storage-provider-service.png


 

<!---HONumber=Nov15_HO4-->