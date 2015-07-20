
<properties 
	pageTitle="Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK" 
	description="Erfahren Sie, wie Sie Medienobjekte und verwandte Entitäten mit dem Media Services SDK für .NET verwalten." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="juliako"/>


#Verwalten von Medienobjekten und verwandten Entitäten mit dem Media Services .NET SDK

In diesem Thema wird gezeigt, wie Sie die folgenden Media Services-Verwaltungsaufgaben ausführen:

- Abrufen eines Verweises auf ein Medienobjekt 
- Abrufen eines Verweises auf einen Auftrag 
- Auflisten aller Medienobjekte 
- Auflisten von Aufträgen und Medienobjekten 
- Auflisten aller Zugriffsrichtlinien 
- Auflisten aller Locators 
- Löschen eines Medienobjekts 
- Löschen eines Auftrags 
- Löschen einer Zugriffsrichtlinie 

##Voraussetzungen 

Siehe [Einrichten der Umgebung](media-services-set-up-computer.md)

##Abrufen eines Verweises auf ein Medienobjekt

Eine häufige Aufgabe besteht darin, einen Verweis auf ein vorhandenes Medienobjekt in Media Services abzurufen. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen Verweis auf ein Medienobjekt aus der Sammlung der Medienobjekte für das Serverkontextobjekt auf Basis einer Medienobjekt-ID abrufen. Im folgenden Codebeispiel wird eine Linq-Abfrage zum Abrufen eines Verweises auf ein vorhandenes IAsset-Objekt verwendet.

	static IAsset GetAsset(string assetId)
	{
	    // Use a LINQ Select query to get an asset.
	    var assetInstance =
	        from a in _context.Assets
	        where a.Id == assetId
	        select a;
	    // Reference the asset as an IAsset.
	    IAsset asset = assetInstance.FirstOrDefault();
	
	    return asset;
	}

##Abrufen eines Verweises auf einen Auftrag

Bei der Verarbeitung von Aufgaben in Media Services-Code müssen Sie häufig einen Verweis auf einen vorhandenen Auftrag anhand einer ID abrufen. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen Verweis auf ein IJob-Objekt aus der Sammlung der Aufträge abrufen. Warnung: Möglicherweise müssen Sie einen Verweis auf einen Auftrag beim Starten eines Codierungsauftrags mit langer Laufzeit abrufen und den Auftragsstatus für einen Thread überprüfen. Wenn die Rückgabe der Methode in einem solchen Fall aus einem Thread erfolgt, müssen Sie einen aktualisierten Verweis auf einen Auftrag abrufen.

	static IJob GetJob(string jobId)
	{
	    // Use a Linq select query to get an updated 
	    // reference by Id. 
	    var jobInstance =
	        from j in _context.Jobs
	        where j.Id == jobId
	        select j;
	    // Return the job reference as an Ijob. 
	    IJob job = jobInstance.FirstOrDefault();
	
	    return job;
	}

##Auflisten aller Medienobjekte

Wenn die Anzahl der Medienobjekte im Speicher steigt, ist es hilfreich, diese Medienobjekte aufzulisten. Im folgenden Codebeispiel wird veranschaulicht, wie Sie die Sammlung der Medienobjekte für das Serverkontextobjekt durchlaufen. Mit jedem Medienobjekt werden im Codebeispiel außerdem einige Eigenschaftswerte in die Konsole geschrieben. Beispielsweise kann jedes Medienobjekt zahlreiche Mediendateien enthalten. Im Codebeispiel werden alle Dateien aufgeführt, die den einzelnen Medienobjekten zugeordnet sind.



	static void ListAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IAsset asset in _context.Assets)
	    {
	        // Display the collection of assets.
	        builder.AppendLine("");
	        builder.AppendLine("******ASSET******");
	        builder.AppendLine("Asset ID: " + asset.Id);
	        builder.AppendLine("Name: " + asset.Name);
	        builder.AppendLine("==============");
	        builder.AppendLine("******ASSET FILES******");
	
	        // Display the files associated with each asset. 
	        foreach (IAssetFile fileItem in asset.AssetFiles)
	        {
	            builder.AppendLine("Name: " + fileItem.Name);
	            builder.AppendLine("Size: " + fileItem.ContentFileSize);
	            builder.AppendLine("==============");
	        }
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Auflisten von Aufträgen und Medienobjekten

Eine wichtige verwandte Aufgabe besteht darin, Medienobjekte mit dem zugehörigen Auftrag in Media Services aufzulisten. Im folgenden Codebeispiel wird veranschaulicht, wie Sie jedes IJob-Objekt auflisten. Anschließend werden für jeden Auftrag Eigenschaften zu dem Auftrag, alle verwandten Aufgaben sowie alle Eingabe- und alle Ausgabemedienobjekte angezeigt. Der Code in diesem Beispiel kann für viele andere Aufgaben hilfreich sein. Wenn Sie beispielsweise die Ausgabemedienobjekte eines oder mehrerer zuvor ausgeführter Codierungsaufträge auflisten möchten, veranschaulicht dieser Code, wie Sie auf die Ausgabemedienobjekte zugreifen. Wenn Sie über einen Verweis auf ein Ausgabemedienobjekt verfügen, können Sie den Inhalt dann für andere Benutzer oder Anwendungen bereitstellen, indem Sie ihn herunterladen oder URLs zur Verfügung stellen.

Weitere Informationen zu den Optionen für die Bereitstellung von Medienobjekten finden Sie unter [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET](media-services-deliver-streaming-content.md).

	// List all jobs on the server, and for each job, also list 
	// all tasks, all input assets, all output assets.

	static void ListJobsAndAssets()
	{
	    string waitMessage = "Building the list. This may take a few "
	        + "seconds to a few minutes depending on how many assets "
	        + "you have."
	        + Environment.NewLine + Environment.NewLine
	        + "Please wait..."
	        + Environment.NewLine;
	    Console.Write(waitMessage);
	
	    // Create a Stringbuilder to store the list that we build. 
	    StringBuilder builder = new StringBuilder();
	
	    foreach (IJob job in _context.Jobs)
	    {
	        // Display the collection of jobs on the server.
	        builder.AppendLine("");
	        builder.AppendLine("******JOB*******");
	        builder.AppendLine("Job ID: " + job.Id);
	        builder.AppendLine("Name: " + job.Name);
	        builder.AppendLine("State: " + job.State);
	        builder.AppendLine("Order: " + job.Priority);
	        builder.AppendLine("==============");
	
	
	        // For each job, display the associated tasks (a job  
	        // has one or more tasks). 
	        builder.AppendLine("******TASKS*******");
	        foreach (ITask task in job.Tasks)
	        {
	            builder.AppendLine("Task Id: " + task.Id);
	            builder.AppendLine("Name: " + task.Name);
	            builder.AppendLine("Progress: " + task.Progress);
	            builder.AppendLine("Configuration: " + task.Configuration);
	            if (task.ErrorDetails != null)
	            {
	                builder.AppendLine("Error: " + task.ErrorDetails);
	            }
	            builder.AppendLine("==============");
	        }
	
	        // For each job, display the list of input media assets.
	        builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
	        foreach (IAsset inputAsset in job.InputMediaAssets)
	        {
	
	            if (inputAsset != null)
	            {
	                builder.AppendLine("Input Asset Id: " + inputAsset.Id);
	                builder.AppendLine("Name: " + inputAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	        // For each job, display the list of output media assets.
	        builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
	        foreach (IAsset theAsset in job.OutputMediaAssets)
	        {
	            if (theAsset != null)
	            {
	                builder.AppendLine("Output Asset Id: " + theAsset.Id);
	                builder.AppendLine("Name: " + theAsset.Name);
	                builder.AppendLine("==============");
	            }
	        }
	
	    }
	
	    // Display output in console.
	    Console.Write(builder.ToString());
	}

##Auflisten aller Zugriffsrichtlinien

In Media Services können Sie eine Zugriffsrichtlinie für ein Medienobjekt oder die zugehörigen Dateien definieren. Eine Zugriffsrichtlinie definiert die Berechtigungen für eine Datei oder ein Medienobjekt (den Zugriffstyp und die Dauer). Im Media Services-Code definieren Sie eine Zugriffsrichtlinie in der Regel, indem Sie ein IAccessPolicy-Objekt erstellen und dieses dann einem vorhandenen Medienobjekt zuordnen. Anschließend erstellen Sie ein ILocator-Objekt, über das Sie den direkten Zugriff auf Medienobjekte in Media Services bereitstellen können. Das Visual Studio-Projekt, das diese Dokumentationsreihe begleitet, enthält verschiedene Codebeispiele, die veranschaulichen, wie Zugriffsrichtlinien und Locators erstellt und Medienobjekten zugeordnet werden.

Im folgenden Codebeispiel wird veranschaulicht, wie alle Zugriffsrichtlinien auf dem Server aufgelistet werden. Außerdem werden die Typen der jeweils zugeordneten Berechtigungen angezeigt. Eine weitere nützliche Methode zum Anzeigen der Zugriffsrichtlinien besteht darin, alle ILocator-Objekte auf dem Server aufzulisten. Dann können Sie für jeden Locator die zugeordnete Zugriffsrichtlinie auflisten, indem Sie die AccessPolicy-Eigenschaft verwenden.

	static void ListAllPolicies()
	{
	    foreach (IAccessPolicy policy in _context.AccessPolicies)
	    {
	        Console.WriteLine("");
	        Console.WriteLine("Name:  " + policy.Name);
	        Console.WriteLine("ID:  " + policy.Id);
	        Console.WriteLine("Permissions: " + policy.Permissions);
	        Console.WriteLine("==============");
	
	    }
	}

##Auflisten aller Locators

Ein Locator ist eine URL, die einen direkten Pfad für den Zugriff auf ein Medienobjekt zusammen mit den entsprechenden Berechtigungen bereitstellt, die durch die zugehörige Zugriffsrichtlinie des Locators definiert sind. Jedem Medienobjekt kann eine Sammlung von ILocator-Objekten über die Locators-Eigenschaft zugeordnet sein. Der Serverkontext enthält ebenfalls eine Locators-Sammlung, die alle Locators enthält.

Im folgenden Codebeispiel werden alle Locators auf dem Server aufgelistet. Für jeden Locator wird die ID für das verknüpfte Medienobjekt und die Zugriffsrichtlinie angezeigt. Außerdem werden der Typ der Berechtigungen, das Ablaufdatum und der vollständige Pfad des Medienobjekts angezeigt.

Beachten Sie, dass ein Locator-Pfad für ein Medienobjekt nur eine Basis-URL des Medienobjekts ist. Um einen direkten Pfad zu den einzelnen Dateien zu erstellen, die ein Benutzer oder eine Anwendung durchsuchen könnte, muss dem Locator-Pfad im Code der spezifische Dateipfad hinzugefügt werden. Weitere Informationen hierzu finden Sie im Thema [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET](media-services-deliver-streaming-content.md).

	static void ListAllLocators()
	{
	    foreach (ILocator locator in _context.Locators)
	    {
	        Console.WriteLine("***********");
	        Console.WriteLine("Locator Id: " + locator.Id);
	        Console.WriteLine("Locator asset Id: " + locator.AssetId);
	        Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
	        Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
	        Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
	        // The locator path is the base or parent path (with included permissions) to access  
	        // the media content of an asset. To create a full URL to a specific media file, take 
	        // the locator path and then append a file name and info as needed.  
	        Console.WriteLine("Locator base path: " + locator.Path);
	        Console.WriteLine("");
	    }
	}


##Löschen eines Medienobjekts

Im folgenden Beispiel wird ein Medienobjekt gelöscht.

	static void DeleteAsset( IAsset asset)
	{
	    // delete the asset
	    asset.Delete();
	
	    // Verify asset deletion
	    if (GetAsset(asset.Id) == null)
	        Console.WriteLine("Deleted the Asset");
	
	}

##Löschen eines Auftrags

Um einen Auftrag zu löschen, müssen Sie den Status des Auftrags überprüfen, der in der State-Eigenschaft angegeben ist. Beendete oder abgebrochene Aufträge können gelöscht werden, während Aufträge mit einem bestimmten anderen Status, z. B. in der Warteschlange, geplant oder in Verarbeitung, zunächst abgebrochen werden müssen. Anschließend können sie gelöscht werden. Das folgende Codebeispiel zeigt eine Methode zum Löschen eines Auftrags, indem der Auftragsstatus überprüft und der Auftrag dann gelöscht wird, wenn er abgeschlossen oder abgebrochen wurde. Dieser Code beruht auf dem vorherigen Abschnitt in diesem Thema über das Abrufen eines Verweises auf einen Auftrag: Abrufen eines Verweises auf einen Auftrag.

	static void DeleteJob(string jobId)
	{
	    bool jobDeleted = false;
	
	    while (!jobDeleted)
	    {
	        // Get an updated job reference.  
	        IJob job = GetJob(jobId);
	
	        // Check and handle various possible job states. You can 
	        // only delete a job whose state is Finished, Error, or Canceled.   
	        // You can cancel jobs that are Queued, Scheduled, or Processing,  
	        // and then delete after they are canceled.
	        switch (job.State)
	        {
	            case JobState.Finished:
	            case JobState.Canceled:
	            case JobState.Error:
	                // Job errors should already be logged by polling or event 
	                // handling methods such as CheckJobProgress or StateChanged.
	                // You can also call job.DeleteAsync to do async deletes.
	                job.Delete();
	                Console.WriteLine("Job has been deleted.");
	                jobDeleted = true;
	                break;
	            case JobState.Canceling:
	                Console.WriteLine("Job is cancelling and will be deleted "
	                    + "when finished.");
	                Console.WriteLine("Wait while job finishes canceling...");
	                Thread.Sleep(5000);
	                break;
	            case JobState.Queued:
	            case JobState.Scheduled:
	            case JobState.Processing:
	                job.Cancel();
	                Console.WriteLine("Job is scheduled or processing and will "
	                    + "be deleted.");
	                break;
	            default:
	                break;
	        }
	
	    }
	}


##Löschen einer Zugriffsrichtlinie

Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen Verweis auf eine Zugriffsrichtlinie auf Basis der ID abrufen und die Richtlinie dann löschen.

	static void DeleteAccessPolicy(string existingPolicyId)
	{
	    // To delete a specific access policy, get a reference to the policy.  
	    // based on the policy Id passed to the method.
	    var policyInstance =
	            from p in _context.AccessPolicies
	            where p.Id == existingPolicyId
	            select p;
	    IAccessPolicy policy = policyInstance.FirstOrDefault();
	
	    policy.Delete();
	
	}
	

<!---HONumber=July15_HO2-->