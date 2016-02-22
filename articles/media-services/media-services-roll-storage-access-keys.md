<properties 
	pageTitle="Aktualisieren von Media Services nach dem Austausch der Speicherschlüssel" 
	description="In diesem Artikel lernen Sie, wie Sie Media Services nach dem Austausch der Speicherzugriffsschlüssel aktualisieren." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,milangada,cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
 	ms.date="02/03/2016"  
	ms.author="juliako"/>

#Gewusst wie: Aktualisieren von Media Services nach dem Austausch der Speicherschlüssel

Wenn Sie ein neues Azure Media Services-Konto erstellen, müssen Sie auch ein Azure-Speicherkonto auswählen, das zum Speichern Ihrer Medieninhalte verwendet wird. Beachten Sie, dass Sie Ihrem Media Services-Konto [mehrere Speicherkonten](meda-services-managing-multiple-storage-accounts.md) hinzufügen können.

Wenn ein neues Speicherkonto erstellt wird, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die zum Authentifizieren des Zugriffs auf das Speicherkonto verwendet werden. Um Ihre Speicherverbindungen sicherer zu gestalten, sollten Sie Ihre Speicherzugriffsschlüssel in regelmäßigen Abständen neu generieren und austauschen. Zwei Zugriffsschlüssel (primär und sekundär) werden zugewiesen, damit Sie Verbindungen zum Speicherkonto mit einem Zugriffsschlüssel aufrechterhalten können, während Sie den anderen Zugriffsschlüssel neu generieren. Dieser Vorgang wird als Austausch der Zugriffsschlüssel bezeichnet.

Für Media Services muss ein Speicherschlüssel bereitgestellt werden. Insbesondere hängen die Locators, mit denen Ihre Assets gestreamt oder heruntergeladen werden, vom angegebenen Speicherzugriffsschlüssel ab. Beim Erstellen eines AMS-Kontos wird standardmäßig die Abhängigkeit vom primären Speicherzugriffsschlüssel übernommen. Sie können als Benutzer aber den Speicherschlüssel von AMS aktualisieren. Sie müssen Media Services darüber informieren, welcher Schlüssel verwendet werden soll. Führen Sie dazu die Schritte in diesem Thema aus. Außerdem müssen Sie beim Austausch der Speicherzugriffsschlüssel sicherstellen, dass Ihre Locators aktualisiert werden, damit keine Unterbrechungen Ihres Streamingdiensts auftreten (dieser Schritt wird in diesem Thema ebenfalls beschrieben).

>[AZURE.NOTE]Wenn Sie mehrere Speicherkonten verwenden, müssen Sie dieses Verfahren für jedes Speicherkonto einzeln ausführen.
>
>Sie sollten vor der Ausführung der in diesem Thema beschriebenen Schritte unter einem Produktionskonto zunächst einen Test unter einem Vorproduktionskonto durchführen.


## Schritt 1: Erneutes Generieren des sekundären Speicherzugriffsschlüssels

Beginnen Sie mit dem Neugenerieren des sekundären Speicherschlüssels. Standardmäßig wird der sekundäre Schlüssel nicht von Media Services verwendet. Informationen zum Austausch der Speicherschlüssel finden Sie unter [Gewusst wie: Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
  
##<a id="step2"></a>Schritt 2: Aktualisieren von Media Services für die Verwendung des neuen sekundären Speicherschlüssels

Aktualisieren Sie Media Services, damit der sekundäre Speicherzugriffsschlüssel verwendet wird. Sie können eine der beiden folgenden Methoden anwenden, um den neu generierten Speicherschlüssel mit Media Services zu synchronisieren.

- Im klassischen Azure-Portal: Wählen Sie Ihr Media Services-Konto aus, und klicken Sie unten im Portalfenster auf das Symbol "SCHLÜSSEL VERWALTEN". Je nachdem, welcher Speicherschlüssel mit Media Services synchronisiert werden soll, klicken Sie auf die Schaltfläche für das Synchronisieren des primären oder des sekundären Schlüssels. Verwenden Sie in diesem Fall den sekundären Schlüssel.

- Mit der Media Services REST-API.

Im folgenden Codebeispiel wird veranschaulicht, wie die Abfrage "https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key" erstellt wird, um den angegebenen Speicherschlüssel mit Media Services zu synchronisieren. In diesem Fall wird der Wert des sekundären Speicherschlüssels verwendet. Weitere Informationen finden Sie unter [Vorgehensweise: Verwenden der Media Services-Verwaltungs-REST-API](http://msdn.microsoft.com/library/azure/dn167656.aspx).
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

Aktualisieren Sie nach diesem Schritt die vorhandenen Locators (die vom alten Speicherschlüssel anhängig sind). Die Vorgehensweise wird im nächsten Schritt beschrieben.

>[AZURE.NOTE]Warten Sie 30 Minuten, bevor Sie Vorgänge mit Media Services durchführen (z. B. zum Erstellen neuer Locator), um Auswirkungen auf ausstehende Aufträge zu verhindern.

##Schritt 3: Aktualisieren der Locators 

>[AZURE.NOTE]Sie müssen beim Austausch der Speicherzugriffsschlüssel sicherstellen, dass Ihre vorhandenen Locators aktualisiert werden, damit keine Unterbrechungen Ihres Streamingdiensts auftreten.

Warten Sie nach der Synchronisierung des neuen Speicherschlüssels mit AMS mindestens 30 Minuten. Anschließend können Sie die OnDemand-Locators neu erstellen, damit sie den angegebenen Speicherschlüssel nutzen und die vorhandene URL beibehalten.

Beachten Sie, dass sich beim Aktualisieren (oder Neuerstellen) eines SAS-Locators die URL immer ändert.

>[AZURE.NOTE] Um sicherzustellen, dass die vorhandenen URLs der OnDemand-Locators beibehalten werden, müssen Sie den vorhandenen Locator löschen und einen neuen mit der gleichen ID erstellen.
 
Im folgenden .NET-Beispiel wird das Neuerstellen eines Locators mit der gleichen ID gezeigt.
	
	private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
	{
	    // Save properties of existing locator.
	    var asset = locator.Asset;
	    var accessPolicy = locator.AccessPolicy;
	    var locatorId = locator.Id;
	    var startDate = locator.StartTime;
	    var locatorType = locator.Type;
	    var locatorName = locator.Name;
	
	    // Delete old locator.
	    locator.Delete();
	
	    if (locator.ExpirationDateTime <= DateTime.UtcNow)
	    {
	        throw new Exception(String.Format(
	            "Cannot recreate locator Id={0} because its locator expiration time is in the past",
	            locator.Id));
	    }
	
	    // Create new locator using saved properties.
	    var newLocator = context.Locators.CreateLocator(
	        locatorId,
	        locatorType,
	        asset,
	        accessPolicy,
	        startDate,
	        locatorName);
	
	
	
	    return newLocator;
	}


##Schritt 5: Erneutes Generieren des primären Speicherzugriffsschlüssels

Generieren Sie den primären Speicherzugriffsschlüssel neu. Informationen zum Austausch der Speicherschlüssel finden Sie unter [Gewusst wie: Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

##Schritt 6: Aktualisieren von Media Services für die Verwendung des neuen primären Speicherschlüssels
	
Wenden Sie das unter [Schritt 2](media-services-roll-storage-access-keys.md#step2) beschriebene Verfahren an, aber synchronisieren Sie dieses Mal das Media Services-Konto mit dem primären Speicherzugriffsschlüssel.

>[AZURE.NOTE]Warten Sie 30 Minuten, bevor Sie Vorgänge mit Media Services durchführen (z. B. zum Erstellen neuer Locator), um Auswirkungen auf ausstehende Aufträge zu verhindern.

##Schritt 7: Aktualisieren der Locators  

Nach 30 Minuten können Sie die OnDemand-Locators neu erstellen, damit sie den neuen primären Speicherschlüssel nutzen und die vorhandene URL beibehalten.

Gehen Sie auf die gleiche Weise wie in [Schritt 3](media-services-roll-storage-access-keys.md#step-3-update-locators) vor.


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###Danksagungen 

Wir möchten folgenden Personen für ihre Beiträge zur Erstellung dieses Dokuments danken: Cenk Dingiloglu, Milan Gada, Seva Titov.

<!---HONumber=AcomDC_0211_2016-->