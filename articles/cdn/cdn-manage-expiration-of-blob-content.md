<properties 
 pageTitle="Verwalten des Ablaufs von BLOB-Inhalten im Azure CDN (Content Delivery Network, Netzwerk für die Inhaltsübermittlung)" 
 description="" 
 services="cdn" 
 documentationCenter=".NET" 
 authors="zhangmanling" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="cdn" 
 ms.workload="media" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="08/01/2014" 
 ms.author="mazha"/>


# Verwalten des Ablaufs von BLOB-Inhalten im Azure CDN (Content Delivery Network, Netzwerk für die Inhaltsübermittlung)  

BLOBs, die am meisten vom Azure CDN-Caching profitieren, sind solche, auf die häufig während ihrer Gültigkeitsdauer zugegriffen wird. Ein BLOB bleibt für den Zeitraum seiner Gültigkeit im Cache und wird dann nach Ablauf dieser Zeit vom BLOB-Dienst aktualisiert. Anschließend wird der Prozess wiederholt.  

Sie haben zwei Möglichkeiten zur Steuerung der Gültigkeitsdauer.  

1.	Keine Cachewerte festlegen, sodass der Standardgültigkeitszeitraum von 7 Tagen verwendet wird. 
2.	Die  *x-ms-blob-cache-control*-Eigenschaft für eine Anforderung vom Typ **Put Blob**, **Put Block List** oder **Set Blob Properties** explizit festlegen, oder die verwaltete Bibliothek von Azure verwenden, um die [BlobProperties.CacheControl](http://msdn.microsoft.com/library/microsoft.windowsazure.storageclient.blobproperties.cachecontrol.aspx)-Eigenschaft festzulegen. Durch das Festlegen dieser Eigenschaft wird der Wert des  *Cache-Control*-Headers für das BLOB festgelegt. Der Wert des Headers bzw. der Eigenschaft sollte den entsprechenden Wert in Sekunden angeben. Wenn Sie beispielsweise den maximalen Zeitraum der Zwischenspeicherung auf ein Jahr festlegen, können Sie den Anforderungsheader mit `x-ms-blob-cache-control: public, max-age=31556926` angeben. Weitere Informationen zum Festlegen von Headern für die Zwischenspeicherung finden Sie unter [HTTP/1.1-Spezifikation](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).  

Alle Inhalte, die Sie mithilfe des CDN zwischenspeichern möchten, müssen im Azure-Speicherkonto als öffentlich zugängliche BLOBs gespeichert werden. Weitere Informationen zum Blob-Dienst von Azure finden Sie unter **Blob-Dienst-Konzepte**.  

Es gibt mehrere Möglichkeiten, Inhalte im Blob-Dienst zu verwenden:  

-	Verwenden der in der **Referenz zur verwalteten Azure-Bibliothek** bereitgestellten verwalteten API
-	Verwenden eines Speicherverwaltungstools von Drittanbietern
-	Verwenden der REST-API der Azure Storage-Dienste  

Das folgende Codebeispiel ist eine Konsolenanwendung, die die verwaltete Azure-Bibliothek verwendet, um einen Container zu erstellen, dessen Berechtigungen für den öffentlichen Zugriff festzulegen und ein BLOB im Container zu erstellen. Darüber hinaus wird explizit ein gewünschtes Aktualisierungsintervall angegeben, indem der "Cache-Control"-Header für das BLOB festgelegt wird.   

Wenn Sie das CDN wie oben gezeigt aktiviert haben, wird das erstellte BLOB vom CDN zwischengespeichert. Stellen Sie sicher, dass Ihre Kontoanmeldeinformationen mit Ihrem eigenen Speicherkonto und Ihrem Zugriffsschlüssel angegeben werden:  

	using System;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.StorageClient;
	
	namespace BlobsInCDN
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            //Specify storage credentials.
	            StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
	                "m4AHAkXjfhlt2rE2BN/hcUR4U2lkGdCmj2/1ISutZKl+OqlrZN98Mhzq/U2AHYJT992tLmrkFW+mQgw9loIVCg==");
	            
	            //Create a reference to your storage account, passing in your credentials.
	            CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);
	            
	            //Create a new client object, which will provide access to Blob service resources.
	            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
	
	            //Create a new container.
	            CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
	            container.CreateIfNotExist();
	
	            //Specify that the container is publicly accessible.
	            BlobContainerPermissions containerAccess = new BlobContainerPermissions();
	            containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
	            container.SetPermissions(containerAccess);
	
	            //Create a new blob and write some text to it.
	            CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
	            blob.UploadText("This is a test blob.");
	
	            //Set the Cache-Control header on the blob to specify your desired refresh interval.
	            blob.SetCacheControl("public, max-age=31536000");
	        }
	    }
	
	    public static class BlobExtensions
	    {
	        //A convenience method to set the Cache-Control header.
	        public static void SetCacheControl(this CloudBlob blob, string value)
	        {
	            blob.Properties.CacheControl = value;
	            blob.SetProperties();
	        }
	    }
	}

Testen Sie, ob das BLOB über die CDN-spezifische URL verfügbar ist. Für das BLOB oben sollte die URL etwa wie folgt aussehen:  

	http://az1234.vo.msecnd.net/cdncontent/testblob.txt  

Falls gewünscht, kann ein Tool wie **wget** oder Fiddler verwendet werden, um die Details der Anforderung und der Antwort zu überprüfen.

# Siehe auch

[Verwalten des Ablaufs von Cloud-Dienst-Inhalten im Azure CDN (Content Delivery Network))](cdn-manage-expiration-of-cloud-service-content.md)

<!--HONumber=49--> 