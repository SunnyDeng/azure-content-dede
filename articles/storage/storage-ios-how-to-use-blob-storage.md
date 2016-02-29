<properties
    pageTitle="Verwenden des Azure-Blobspeichers mit iOS | Microsoft Azure"
    description="Erfahren Sie, wie Sie Azure Blob-Speicher zum Hochladen, Herunterladen, Auflisten und Löschen von Blob-Inhalten verwenden. Die Codebeispiele wurden in Objective-C geschrieben."
    services="storage"
    documentationCenter="ios"
    authors="micurd"
    manager="jahogg"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Objective-C"
    ms.topic="article"
    ms.date="01/05/2016"
    ms.author="micurd"/>

# Verwenden des Blobspeichers mit iOS

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Artikel wird die Durchführung gängiger Szenarien mit Microsoft Azure-Blobspeicher demonstriert. Die Beispiele sind in Objective-C geschrieben und greifen auf die [Azure-Speicherclientbibliothek für iOS](https://github.com/Azure/azure-storage-ios) zurück. Die hier beschriebenen Szenarien umfassen das **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs. Weitere Informationen zu Blobs finden Sie im Abschnitt [Nächste Schritte](#next-steps). Sie können auch die [Beispielapp](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) herunterladen, um eine schnelle Demonstration der Verwendung von Azure Storage in einer iOS-Anwendung zu sehen.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Erstellen der Frameworkdatei
Um die Azure Storage-iOS-Bibliothek zu verwenden, müssen Sie zunächst die Frameworkdatei erstellen.

1. Laden Sie zuerst das [azure-storage-ios-repo](https://github.com/azure/azure-storage-ios) herunter, oder klonen Sie es.

2. Wechseln Sie zu *azure-storage-ios* -> *Lib* -> *Azure Storage-Client-Bibliothek*, und öffnen Sie `Azure Storage Client Library.xcodeproj` in Xcode.

3. Ändern Sie oben links in Xcode das aktive Schema von "Azure Storage-Client Library" in "Framework".

4. Erstellen Sie das Projekt (⌘+B). Dadurch wird eine `Azure Storage Client Library.framework`-Datei auf Ihrem Desktop erstellt.

## Importieren der Azure Storage-iOS-Bibliothek in Ihre Anwendung

Sie können die Azure Storage-iOS-Bibliothek folgendermaßen in Ihre Anwendung importieren:

1. Erstellen Sie ein neues Projekt, oder öffnen Sie das vorhandene Projekt in Xcode.

2. Klicken Sie im linken Navigationsbereich auf Ihr Projekt, und klicken Sie auf die Registerkarte *General* (Allgemein) am oberen Rand des Projekteditors.

3. Klicken Sie unter dem Abschnitt *Linked Frameworks und Libraries* (Verknüpfte Frameworks und Bibliotheken) auf die Schaltfläche zum Hinzufügen (+).

4. Klicken Sie auf *Add Other...* (Weitere hinzufügen). Navigieren Sie zur gerade erstellten `Azure Storage Client Library.framework`-Datei, und fügen Sie sie hinzu.

5. Klicken Sie unter dem Abschnitt *Linked Frameworks und Libraries* (Verknüpfte Frameworks und Bibliotheken) erneut auf die Schaltfläche zum Hinzufügen (+).

6. Suchen Sie in der Liste der bereits bereitgestellten Bibliotheken nach `libxml2.2.dylib`, und fügen Sie es dem Projekt hinzu.

##Import-Anweisung
Sie müssen die folgende Import-Anweisung in die Datei einschließen, in der Sie die Azure Storage-API aufrufen möchten.

    // Include the following import statement to use blob APIs.
    #import <Azure Storage Client Library/Azure_Storage_Client_Library.h>

## Konfigurieren der Anwendung für den Zugriff auf Blob-Speicher

Es gibt zwei Möglichkeiten, um Ihre Anwendung für den Zugriff auf die Storage-Dienste zu authentifizieren:

- Gemeinsam verwendeter Schlüssel: Verwenden Sie den gemeinsam verwendeten Schlüssel nur für Testzwecke.
- Shared Access Signature (SAS): Verwenden Sie SAS für Produktionsanwendungen.

###Gemeinsam verwendeter Schlüssel
Bei der Authentifizierung mit gemeinsam verwendetem Schlüssel nutzt die Anwendung Ihren Kontonamen und Kontoschlüssel, um auf die Storage-Dienste zuzugreifen. Für eine schnelle Demonstration, wie Sie Blobspeicher mit iOS verwenden können, wird in diesen ersten Schritten die Authentifizierung mit gemeinsam verwendetem Schlüssel verwendet.

> [AZURE.WARNING (Only use Shared Key authentication for testing purposes!) ] Ihr Kontoname und Kontoschlüssel, mit denen Sie vollständigen Lese-/Schreibzugriff auf das zugehörige Storage-Konto erhalten, werden an alle Personen verteilt, die Ihre App herunterladen. Diese Vorgehensweise wird **nicht** empfohlen, da das Risiko besteht, dass Ihr Schlüssel durch nicht vertrauenswürdige Clients beeinträchtigt werden kann.

Wenn Sie die Authentifizierung mit gemeinsam verwendetem Schlüssel nutzen, erstellen Sie eine Verbindungszeichenfolge. Die Verbindungszeichenfolge besteht aus:

- dem **DefaultEndpointsProtocol** – Sie können zwischen HTTP oder HTTPS wählen. Die Verwendung von HTTPS wird jedoch unbedingt empfohlen.
- dem **Kontonamen** – der Name Ihres Storage-Kontos
- dem** Kontoschlüssel** – Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto, und klicken Sie auf das Symbol **Schlüssel**, um diese Informationen zu erhalten. Wenn Sie das [klassische Azure-Portal](https://manage.windowsazure.com) verwenden, navigieren Sie zu Ihrem Speicherkonto im Portal und klicken Sie auf **Zugriffsschlüssel verwalten**. 

Und so sieht es in Ihrer Anwendung aus:

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=account_name;AccountKey=account_key"];

###Shared Access Signatures (SAS)
Die empfohlene Methode für eine iOS-Anwendung zum Authentifizieren einer Anforderung durch einen Client an den Blobspeicher ist die Nutzung einer Shared Access Signature (SAS). SAS ermöglicht es Ihnen, dem Client mit einem angegebenen Satz von Berechtigungen für einen bestimmten Zeitraum Zugriff auf eine Ressource zu gewähren. Als Speicherkontobesitzer müssen Sie eine SAS für Ihre iOS-Clients erstellen. Zum Generieren der SAS sollten Sie wahrscheinlich einen separaten Dienst schreiben, der die SAS generiert, die an Ihre Clients verteilt werden. Zu Testzwecken können Sie auch die Azure-Befehlszeilenschnittstellen verwenden, um eine SAS zu erstellen. Beachten Sie, dass die Anmeldeinformationen des gemeinsam verwendeten Schlüssels zum Generieren einer SAS verwendet werden, aber Ihre Clients können anschließend die SAS unter Verwendung der Authentifizierungsinformationen nutzen, die in der SAS-URL gekapselt sind. Bei der Erstellung der SAS können Sie das Zeitintervall angeben, in dem die SAS gültig ist, sowie die Berechtigungen, die die SAS dem Client erteilt. Für einen Blobcontainer kann die SAS beispielsweise Lese-, Schreib- oder Löschberechtigungen für einen Blob im Container gewähren und Berechtigungen zum Auflisten der Blobs im Container auflisten.

Das folgende Beispiel zeigt, wie Sie die Azure-CLI verwenden, um ein SAS-Token zu generieren, das bis 12:00 Uhr (UTC) am 5. September 2015 Lese- und Schreibberechtigungen für den Container *sascontainer* gewährt.

1. Lesen Sie zunächst unter [Installieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) nach, um zu erfahren, wie Sie die Azure-CLI installieren und eine Verbindung mit Ihrem Azure-Abonnement herstellen können.

2. Geben Sie anschließend den folgenden Befehl in Azure-CLI ein, um die Verbindungszeichenfolge für Ihr Konto abzurufen:

		azure storage account connectionstring show youraccountname

3. Erstellen Sie eine Umgebungsvariable mit der Verbindungszeichenfolge, die Sie soeben generiert haben:

		export AZURE_STORAGE_CONNECTION_STRING=”your connection string”

4. Generieren Sie die SAS-URL:

		azure storage container sas create --container sascontainer --permissions rw --expiry 2015-09-05T00:00:00

5. Die SAS-URL sollte in etwa wie folgt aussehen:

		https://myaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-29T22%3A18%3A26Z&se=2013-04-30T02%3A23%3A26Z&sr=b&sp=rw&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D

6. In der iOS-Anwendung erhalten Sie jetzt einen Verweis auf den Container, indem Sie die SAS-URL folgendermaßen verwenden:

		// Get a reference to a container in your Storage account
    	AZSCloudBlobContainer *blobContainer = [[AZSCloudBlobContainer alloc] initWithUrl:[NSURL URLWithString:@" your SAS URL"]];

Wie Sie sehen, machen Sie den Kontonamen und Kontoschlüssel in der iOS-Anwendung nicht verfügbar, wenn Sie ein SAS-Token verwenden. Weitere Informationen zu SAS erhalten Sie unter [Shared Access Signatures: Grundlagen zum SAS-Modell](../storage-dotnet-shared-access-signature-part-1).

##Asynchrone Vorgänge
> [AZURE.NOTE] Alle Methoden, die eine Anforderung für den Dienst ausführen, sind asynchrone Vorgänge. In den Codebeispielen werden Sie feststellen, dass diese Methoden einen Abschlusshandler haben. Der Code im Abschlusshandler wird **nach** Abschluss der Anforderung ausgeführt. Der Code nach dem Abschlusshandler wird **während** der Anforderung ausgeführt.

## Erstellen eines Containers
Jeder Blob in Azure Storage muss sich in einem Container befinden. Das folgende Beispiel zeigt, wie Sie einen Container namens *newcontainer* in Ihrem Storage-Konto erstellen, wenn er noch nicht vorhanden ist. Wenn Sie einen Namen für den Container auswählen, achten Sie auf die oben genannten Benennungsregeln.

     -(void)createContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

Sie können überprüfen, ob dies funktioniert, indem Sie im [Azure-Portal](https://portal.azure.com) oder in einem [Speicher-Explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) überprüfen, ob *newcontainer* sich in der Liste der Container für das Storage-Konto befindet.

## Festlegen von Containerberechtigungen
Die Berechtigungen eines Containers werden standardmäßig für den **Privatzugriff** konfiguriert. Container bieten jedoch einige unterschiedliche Optionen für den Containerzugriff:

- **Privat**: Container- und Blobdaten können nur vom Kontobesitzer gelesen werden.

- **Blob**: Blobdaten innerhalb dieses Containers können über anonyme Anforderungen gelesen werden, aber die Containerdaten sind nicht verfügbar. Clients können keine Blobs innerhalb des Containers über anonyme Anforderungen aufzählen.

- **Container**: Container- und Blobdaten können über anonyme Anforderungen gelesen werden. Clients können Blobs innerhalb des Containers über eine anonyme Anforderung aufzählen, können aber keine Container innerhalb des Speicherkontos aufzählen.

Das folgende Beispiel veranschaulicht das Erstellen eines Containers mit **Container**-Zugriffsberechtigungen, die allen Benutzern im Internet den öffentlichen, schreibgeschützten Zugriff gewähren:

     -(void)createContainerWithPublicAccess{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create container in your Storage account if the container doesn't already exist
        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
            if (error){
                NSLog(@"Error in creating container.");
            }
        }];
    }

## Hochladen eines Blobs in einen Container
Wie im Abschnitt [Blobdienstkonzepte](#blob-service-concepts) erwähnt, bietet der Blobspeicher drei verschiedene Arten von Blobs: Blockblobs, Anfügeblobs und Seitenblobs. Zu diesem Zeitpunkt unterstützt die Azure Storage-iOS-Bibliothek nur Blockblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Das folgende Beispiel zeigt den Upload eines Blockblobs aus einer NSString. Wenn in diesem Container bereits ein Blob mit demselben Namen vorhanden ist, wird der Inhalt dieses Blobs überschrieben.

     -(void)uploadBlobToContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
         {
             if (error){
                 NSLog(@"Error in creating container.");
             }
         	 else{
				 // Create a local blob object
             	 AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

             	 // Upload blob to Storage
             	 [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                 	if (error){
						NSLog(@"Error in creating blob.");
                 	}             
             	 }];
			 }
         }];
     }

Sie können überprüfen, ob dies funktioniert, indem Sie im [Azure-Portal](https://portal.azure.com) oder in einem [Speicher-Explorer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) überprüfen, ob der Container *containerpublic* das Blob *sampleblob* enthält. In diesem Beispiel haben wir einen öffentlichen Container verwendet, sodass Sie die Funktion auch durch das Aufrufen der Blob-URI überprüfen können:

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

Neben dem Hochladen eines Blockblobs aus einer NSString gibt es ähnliche Methoden für NSData, NSInputStream oder eine lokale Datei.

## Auflisten der Blobs in einem Container
Das folgende Beispiel zeigt, wie alle Blobs in einem Container aufgelistet werden. Achten Sie beim Durchführen dieses Vorgangs auf die folgenden Parameter:

- **ContinuationToken**: Das Fortsetzungstoken gibt an, wo der Auflistungsvorgang beginnen soll. Wenn kein Token angegeben wird, listet es Blobs vom Anfang auf. Es kann eine beliebige Anzahl von Blobs aufgeführt werden, von 0 (null) bis zu einem festgelegten Maximum. Auch wenn diese Methode keine Ergebnisse zurückgibt, wenn `results.continuationToken` nicht Null ist, gibt es möglicherweise mehr Blobs für den Dienst, die nicht aufgelistet wurden.
- **prefix** – Sie können das Präfix angeben, das für die Blobliste verwendet werden soll. Nur Blobs, die mit diesem Präfix beginnen, werden aufgelistet.
- **useFlatBlobListing** – Wie im Abschnitt [Benennen von Containern und Blobs und verweisen auf diese](#naming-and-referencing-containers-and-blobs) erwähnt, können Sie eine virtuelle Hierarchie durch Benennen von Blobs mit Pfadinformationen erstellen, selbst wenn der Blobdienst ein flaches Speicherschema ist. Allerdings wird derzeit eine nicht flache Auflistung nicht unterstützt. Dies ist in Kürze verfügbar. Bis dahin sollte der folgende Wert verwendet werden: `YES`.
- **blobListingDetails** – Sie können angeben, welche Elemente beim Auflisten der Blobs einbezogen werden sollen.
	- `AZSBlobListingDetailsNone`: Listen Sie nur zugesicherte Blobs auf, und geben Sie keine Blobmetadaten zurück.
	- `AZSBlobListingDetailsSnapshots`: Listen Sie zugesicherte Blobs und Blobmomentaufnahmen auf.
	- `AZSBlobListingDetailsMetadata`: Rufen Sie Blobmetadaten für jeden Blob auf, der in der Liste zurückgegeben wird.
	- `AZSBlobListingDetailsUncommittedBlobs`: Listen Sie zugesicherte und nicht zugesicherte Blobs auf.
	- `AZSBlobListingDetailsCopy`: Schließen Sie die Kopiereigenschaften in der Liste ein.
	- `AZSBlobListingDetailsAll`: Listen Sie alle verfügbaren zugesicherten Blobs, nicht zugesicherten Blobs und Momentaufnahmen auf, und geben Sie alle Metadaten und Kopierstatus für diese Blobs zurück.
- **maxResults** – Die maximale Anzahl von Ergebnissen, die für diesen Vorgang zurückgegeben werden. Verwenden Sie -1, um keinen Grenzwert festzulegen.
- **completionHandler** – Der auszuführende Codeblock mit den Ergebnissen des Auflistungsvorgangs.

In diesem Beispiel wird eine Hilfsmethode verwendet, um jedes Mal die Methode zum Auflisten von Blobs wiederholt aufzurufen, wenn ein Fortsetzungstoken zurückgegeben wird.

    -(void)listBlobsInContainer{
      // Create a storage account object from a connection string.
      AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

      // Create a blob service client object.
      AZSCloudBlobClient *blobClient = [account getBlobClient];

      // Create a local container object.
      AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

      //List all blobs in container
      [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
         if (error != nil){
             NSLog(@"Error in creating container.");
         }
      }];
    }

    //List blobs helper method
    -(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
    {
      [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
         if (error)
         {
             completionHandler(error);
         }
         else
         {
             for (int i = 0; i < results.blobs.count; i++) {
                 NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
             }
             if (results.continuationToken)
             {
                 [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
             }
             else
             {
                 completionHandler(nil);
             }
         }
      }];
    }

## Herunterladen eines Blobs

Das folgende Beispiel zeigt den Download eines Blobs in ein NSString-Objekt.

     -(void)downloadBlobToString{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

        // Download blob    
        [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
            if (error) {
                NSLog(@"Error in downloading blob");
            }
			else{
            	NSLog(@"%@",text);
			}
        }];
    }

## Löschen eines Blobs

Das folgende Beispiel zeigt den Löschvorgang eines Blobs.

     -(void)deleteBlob{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Create a local blob object
        AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

        // Delete blob
        [blockBlob deleteWithCompletionHandler:^(NSError *error) {
          if (error) {
            NSLog(@"Error in deleting blob.");
          }
        }];
     }

## Löschen eines Blob-Containers

Das folgende Beispiel zeigt den Löschvorgang eines Containers.

     -(void)deleteContainer{
        // Create a storage account object from a connection string.
        AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"];

        // Create a blob service client object.
        AZSCloudBlobClient *blobClient = [account getBlobClient];

        // Create a local container object.
        AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

        // Delete container
        [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
            if(error){
                NSLog(@"Error in deleting container");
            }
        }];
    }

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Blob-Speichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- [Azure Storage-Clientbibliothek für iOS](https://github.com/azure/azure-storage-ios)
- [REST-API für Azure-Speicherdienste](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Übertragen von Daten mit dem Befehlszeilenprogramm AzCopy](storage-use-azcopy.md)
- [Azure Storage-Teamblog](http://blogs.msdn.com/b/windowsazurestorage)

Stellen Sie Fragen zu dieser Bibliothek in unserem [MSDN Azure-Forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) oder unter [Stack Overflow](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files). Vorschläge für Azure Storage-Funktionen können Sie unter [Feedback zu Azure Storage](https://feedback.azure.com/forums/217298-storage/) machen.

<!---HONumber=AcomDC_0218_2016-->