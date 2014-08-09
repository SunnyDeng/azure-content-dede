<properties linkid="develop-media-services-tutorials-get-started" urlDisplayName="Get Started with Media Services" pageTitle="Get Started with Media Services - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="" solutions="" manager="" editor="" />

Erste Schritte mit Mediendiensten
=================================

In diesem Lernprogramm erfahren Sie, wie Sie für Azure-Mediendienste entwickeln können. Sie lernen den Mediendienste-Arbeitsfluss und die gängigsten Programmierobjekte und Aufgaben für die Mediendienste-Entwicklung kennen. Nach Abschluss des Lernprogramms sind Sie in der Lage, eine einfache Mediendatei abzuspielen, die Sie hochgeladen, codiert und heruntergeladen haben. Alternativ können Sie zum codierten Objekt navigieren und dieses auf dem Server abspielen.

Sie finden ein C\# Visual Studio-Projekt mit dem Code für dieses Lernprogramm hier: [Download](http://go.microsoft.com/fwlink/?linkid=253275).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

-   [Einrichten Ihres Projekts](#Step1)
-   [Abrufen des Mediendienstserver-Kontexts](#Step2)
-   [Erstellen eines Medienobjekts und Hochladen von zugehörigen Dateien in Mediendiensten](#Step3)
-   [Codierung eines Medienobjekts und Download des Ausgabe-Medienobjekts](#Step4)

Voraussetzungen
---------------

Für das Lernprogramm und die Entwicklung mit dem Azure Mediendienste-SDK benötigen Sie Folgendes.

-   Ein Mediendienstekonto in einem neuen oder existierenden Azure-Abonnement. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos](http://go.microsoft.com/fwlink/?LinkId=256662).
-   Betriebssysteme: Windows 7, Windows 2008 R2 oder Windows 8.
-   .NET Framework 4.5 oder .NET Framework 4.
-   Visual Studio 2012 oder Visual Studio 2010 SP1 (Professional, Premium, Ultimate oder Express).
-   Installieren Sie **Azure SDK für .NET.**, **Azure Mediendienste-SDK für .NET** und **WCF Data Services 5.0 für OData V3-Bibliotheken** und fügen Sie Verweise auf Ihr Projekt mit dem [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices)-Paket hinzu. Der folgende Abschnitt behandelt Installation und Einrichtung dieser Schnittstellen.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A8A8397B5).

Einrichten Ihres Projekts
-------------------------

1.  Erstellen Sie eine neue C\#-Konsolenanwendung in Visual Studio 2012 oder Visual Studio 2010 SP1. Geben Sie **Name**, **Ort** und **Lösungsname** ein und klicken Sie auf OK.

2.  Fügen Sie einen Verweis auf die Assembly System.Configuration hinzu.

    Um Verweise mithilfe des **Verweise verwalten**-Dialogfelds hinzuzufügen, gehen Sie wie folgt vor. Klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf den Knoten **Verweise** und klicken Sie auf **Verweis hinzufügen**. Wählen Sie im Dialog **Verweise verwalten** die entsprechenden Assemblys aus (in diesem Fall System.Configuration.)

3.  Falls Sie dies noch nicht getan haben, erstellen Sie Verweise auf die Bibliotheken **Azure SDK für .NET**.(Microsoft.WindowsAzure.StorageClient.dll), **Azure Mediendienste-SDK für .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) und **WCF Data Services 5.0 für OData V3** (Microsoft.Data.OData.dll) mithilfe des [windowsazure.mediaservices Nuget](http://nuget.org/packages/windowsazure.mediaservices)-Pakets.

    Um Verweise mit Nuget hinzuzufügen, gehen Sie wie folgt vor. Wählen Sie im Visual Studio-Hauptmenü TOOLS -\> Paketverwaltung-\> Paketverwaltungskonsole aus. Geben Sie im Konsolenfenster *Install-Package [Paketname]* ein und drücken Sie die Eingabetaste (verwenden Sie in diesem Fall den folgenden Befehl: *Install-Package windowsazure.mediaservices*.)

4.  Fügen Sie einen *appSettings*-Bereich zur **app.config**-Datei hinzu und geben Sie Ihren Kontonamen und Kontoschlüssel für Azure-Mediendienste ein. Den Mediendienste-Benutzernamen und den Kontoschlüssel haben Sie bei der Kontoeinrichtung erhalten. Geben Sie diese Werte in die Attribute der einzelnen Einstellungen in der Datei app.config im Visual Studio-Projekt ein.

    > [WACOM.NOTE] 
    > In Visual Studio 2012 wird die Datei App.config standardmäßig hinzugefügt. In Visual Studio 2010 müssen Sie die Anwendungskonfigurationsdatei manuell erstellen.

    ``` {}
     <configuration>?     . . . 
        <appSettings>?    
        <add key="accountName" value="Add-Media-Services-Account-Name" />?    
        <add key="accountKey" value="Add-Media-Services-Account-Key" />?      
     </appSettings>? 
    </configuration>
     
    ```

5.  Erstellen Sie einen Ordner auf Ihrem lokalen Computer mit dem Namen supportFiles (in diesem Beispiel befindet sich supportFiles unterhalb des MediaServicesGettingStarted-Projektordners.) Das zugehörige [Projekt](http://go.microsoft.com/fwlink/?linkid=253275) zu diesem Lernprogramm enthält den supportFiles-Ordner. Sie können den Inhalt dieses Ordners in Ihren supportFiles-Ordner kopieren.

6.  Überschreiben Sie die existierenden using-Anweisungen am Anfang von Program.cs durch den folgenden Code.

         using System;
         using System.Linq;
         using System.Configuration;
         using System.IO;
         using System.Text;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Collections.Generic;
         using Microsoft.WindowsAzure;
         using Microsoft.WindowsAzure.MediaServices.Client;

7.  Fügen Sie die folgenden Pfadvariablen auf Klassenebene hinzu. Der **\_supportFiles**-Pfad muss auf den Ordner aus dem vorherigen Schritt verweisen.

         // Basispfad für Unterstützungsdateien.  Verwenden Sie dieses Feld, um auf den Basispfad  
         // des von Ihnen erstellten supportFiles-Ordners zu zeigen. 
         private static readonly string _supportFiles =
                     Path.GetFullPath(@"../..\supportFiles");
            
         // Pfade zu Unterstützungsdateien (unterhalb des obigen Basispfads). Sie können die 
         // mitgelieferten Beispiel-Mediendateien aus dem "supportFiles"-Ordner verwenden 
         // oder Pfade zu eigenen Mediendateien eingeben, um die Beispiele auszuführen.
         private static readonly string _singleInputFilePath =
             Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
         private static readonly string _outputFilesFolder =
             Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  Fügen Sie die folgenden Variablen auf Klassenebene hinzu, um Authentifizierungs- und Verbindungseinstellungen abzurufen. Die Einstellungen werden aus der App.Config-Datei geladen und werden für die Verbindung zu Mediendiensten sowie für Authentifikation und den Abruf eines Tokens für den Serverkontext benötigt. Der Projektcode referenziert diese Variablen, um eine Instanz des Serverkontexts zu erstellen.

         private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
         private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  Fügen Sie die folgende Variable auf Klassenebene hinzu, die als statischer Verweis auf den Serverkontext dient.

         // Feld für den Dienstkontext.
         private static CloudMediaContext _context = null;

Abrufen des Mediendienst-Kontexts
---------------------------------

Das Mediendienst-Kontextobjekt enthält alle wichtigen Objekte und Sammlung für die Mediendienste-Programmierung. Der Kontext enthält Verweise auf wichtige Sammlungen inklusive Aufgaben, Medienobjekt, Dateien, Zugriffsrichtlinien, Locators und andere Objekte. Für die meisten Mediendienst-Programmieraufgaben benötigen Sie den Serverkontext.

Fügen Sie in der Datei Program.cs den folgenden Code zu Anfang Ihrer **Main**-Methode hinzu. Dieser Code verwendet Ihren Mediendienste-Kontonamen und Kontoschlüssel aus der Datei app.config, um eine Instanz des Serverkontexts zu erstellen. Diese Instanz wird der Variable **\_context** zugewiesen, die Sie auf Klassenebene erstellt haben.

    // Abrufen des Dienstkontexts.
    _context = new CloudMediaContext(_accountName, _accountKey);

Erstellen eines Medienobjekts und Hochladen einer Datei
-------------------------------------------------------

Der Code in diesem Abschnitt erfüllt die folgenden Aufgaben:

1.  Erstellt ein leeres Medienobjekt
     Bei der Erstellung von Medienobjekten können Sie drei verschiedene Optionen für deren Verschlüsselung auswählen.

    -   **AssetCreationOptions.None**: keine Verschlüsselung. Wählen Sie diese Option aus, wenn Sie Ihr Medienobjekt nicht verschlüsseln möchten.
    -   **AssetCreationOptions.CommonEncryptionProtected**: für Dateien mit Common Encryption (CENC). Ein Beispiel ist ein Satz von Dateien, die bereits PlayReady-verschlüsselt sind.
    -   **AssetCreationOptions.StorageEncrypted**: Speicherverschlüsselung. Verschlüsselt eine unverschlüsselte Eingabedatei vor dem Hochladen in den Azure-Speicher.

        **Hinweis**

        Mediendienste bieten Speicherverschlüsselung auf der Festplatte, und nicht für die Netzwerkkommunikation wie Digital Rights Manager (DRM.)



2.  Erstellt eine AssetFile-Instanz, der wir das Medienobjekt zuweisen werden.
3.  Erstellt eine AccessPolicy-Instanz mit den Berechtigungen und der Zugriffsdauer auf das Medienobjekt.
4.  Erstellt eine Locator-Instanz, die Zugriff auf das Medienobjekt bietet.
5.  Lädt eine einzelne Datei in einen Mediendienst hoch. Den Erstellungs- und Uploadprozess nennt man auch die Erfassung von Medienobjekten.

Fügen Sie die folgenden Methoden zur Klasse hinzu:

<pre><code>
static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
{
    var asset = _context.Assets.Create(assetName, assetCreationOptions);

    Console.WriteLine("Asset name: " + asset.Name);
    Console.WriteLine("Time created: " + asset.Created.Date.ToString());

    return asset;
}

static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
{
    var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
    var asset = CreateEmptyAsset(assetName, assetCreationOptions);

    var fileName = Path.GetFileName(singleFilePath);

    var assetFile = asset.AssetFiles.Create(fileName);

    Console.WriteLine("Created assetFile {0}", assetFile.Name);

    var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                        AccessPermissions.Write | AccessPermissions.List);

    var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

    Console.WriteLine("Upload {0}", assetFile.Name);

    assetFile.Upload(singleFilePath);
    Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

    locator.Delete();
    accessPolicy.Delete();

    return asset;
}

</code></pre>

Fügen Sie einen Aufruf der Methode nach der Zeile **\_context = new CloudMediaContext(\_accountName, \_accountKey);** in Ihrer Main-Methode hinzu.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

Codierung des Medienobjekts auf dem Server und Download des Ausgabe-Medienobjekts
---------------------------------------------------------------------------------

In Mediendiensten können Sie Aufträge erstellen, die Medieninhalte auf verschiedene Arten verarbeiten: Codierung, Verschlüsselung, Formatumwandlungen usw. Ein Mediendienste-Auftrag enthält immer eine oder mehrere Aufgaben, in denen die Verarbeitungsdetails festgelegt sind. In diesem Abschnitt erstellen Sie eine einfache Codierungsaufgabe und führen diese anschließend in einem Auftrag mit dem Azure-Medienencoder aus. Die Aufgabe verwendet eine vorkonfigurierte Zeichenfolge, um die auszuführende Codierung anzugeben. Eine Liste der voreingestellten Codierungswerte finden Sie unter [Task Preset Strings for Azure Media Encoder](http://msdn.microsoft.com/de-de/library/windowsazure/jj129582.aspx) (Aufgaben-Voreinstellungen für den Azure-Medienencoder, in englischer Sprache). Mediendienste unterstützen dieselben Ein- und Ausgabeformate für Mediendatei wie der Microsoft Expression Encoder. Eine Liste unterstützter Formate finden Sie unter [Supported File Types for Media Services](http://msdn.microsoft.com/de-de/library/windowsazure/hh973634.aspx) (Unterstützte Dateitypen für Mediendienste, in englischer Sprache).

1.  Fügen Sie die folgende **CreateEncodingJob**-Methode zu Ihrer Klasse hinzu. Diese Methode demonstriert die Ausführung verschiedener Aufgaben für einen Codierungsauftrag:

    -   Definieren eines neuen Auftrags.
    -   Definieren eines Medienprozessors für die Verarbeitung des Auftrags. Der Medienprozessor ist für Codierung, Verschlüsselung, Formatumwandlungen und andere verwandte Verarbeitungsaufträge zuständig. Ihnen stehen mehrere verschiedene Medienprozessoren zur Verfügung (mit \_context.MediaProcessors können Sie diese Liste durchlaufen). Die später in diesem Lernprogramm gezeigte GetLatestMediaProcessorByName-Methode gibt den Azure-Medienencoder-Prozessor zurück.
    -   Definieren einer neuen Aufgabe. Jeder Auftrag enthält eine oder mehrere Aufgaben. Sie übergeben mit der Aufgabe einen lesbaren Namen, eine Medienprozessor-Instanz, eine Aufgabenkonfigurations-Zeichenfolge und Optionen für die Aufgabenerstellung. Die Konfigurations-Zeichenfolge gibt die Codierungseinstellungen vor. Dieses Beispiel verwendet die Einstellung **H264 Broadband 720p**. Diese Voreinstellung produziert eine einzige MP4-Datei. Weitere Informationen zu voreingestellten Codierungswerten finden Sie unter [Task Preset Strings for Azure Media Encoder](http://msdn.microsoft.com/library/windowsazure/jj129582.aspx) (Aufgaben-Voreinstellungen für den Azure-Medienencoder, in englischer Sprache).
    -   Hinzufügen eines Eingabe-Medienobjekts zur Aufgabe. Für dieses Beispiel verwenden Sie das im vorigen Abschnitt erstellte Eingabe-Medienobjekt.
    -   Hinzufügen eines Ausgabe-Medienobjekts zur Aufgabe. Übergeben Sie für das Ausgabe-Medienobjekt einen lesbaren Namen, einen booleschen Wert, der angibt, ob die Ausgabe nach Abschluss auf dem Server gespeichert werden soll und einen **AssetCreationOptions.None**-Wert, um anzugeben, dass die Ausgabe nicht für Speicher und Transport verschlüsselt werden soll.
    -   Übermitteln des Auftrags.
         Die Übermittlung des Auftrags ist der letzte erforderliche Schritt für Codierungsaufgaben.

    Diese Methode demonstriert ebenfalls andere nützliche (aber optionale) Aufgaben wie z. B. Verfolgung des Fortschritts und Zugriff auf das Ausgabe-Medienobjekt Ihres Auftrags.

<pre><code>
static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("My encoding job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

    // Create a task with the encoding details, using a string preset.
    ITask task = job.Tasks.AddNew("My encoding task",
        processor,
        "H264 Broadband 720p",
        Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

    // Specify the input asset to be encoded.
    task.InputAssets.Add(asset);
    // Add an output asset to contain the results of the job. 
    // This output is specified as AssetCreationOptions.None, which 
    // means the output asset is not encrypted. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);
    // Use the following event handler to check job progress.  
    job.StateChanged += new
            EventHandler&lt;JobStateChangedEventArgs&gt;(StateChanged);

    // Launch the job.
    job.Submit();

    // Optionally log job details. This displays basic job details
    // to the console and saves them to a JobDetails-{JobId}.txt file 
    // in your output folder.
    LogJobDetails(job.Id);

    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();

    // **********
    // Optional code.  Code after this point is not required for 
    // an encoding job, but shows how to access the assets that 
    // are the output of a job, either by creating URLs to the 
    // asset on the server, or by downloading. 
    // **********

    // Get an updated job reference.
    job = GetJob(job.Id);

    // If job state is Error the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
        return job;
    }

    // Get a reference to the output asset from the job.
    IAsset outputAsset = job.OutputMediaAssets[0];
    IAccessPolicy policy = null;
    ILocator locator = null;

    // Declare an access policy for permissions on the asset. 
    // You can call an async or sync create method. 
    policy =
        _context.AccessPolicies.Create("My 30 days readonly policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

    // Create a SAS locator to enable direct access to the asset 
    // in blob storage. You can call a sync or async create method.  
    // You can set the optional startTime param as 5 minutes 
    // earlier than Now to compensate for differences in time  
    // between the client and server clocks. 

    locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
        policy,
        DateTime.UtcNow.AddMinutes(-5));

    // Build a list of SAS URLs to each file in the asset. 
    List&lt;String&gt; sasUrlList = GetAssetSasUrlList(outputAsset, locator);

    // Write the URL list to a local file. You can use the saved 
    // SAS URLs to browse directly to the files in the asset.
    if (sasUrlList != null)
    {
        string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
        StringBuilder fileList = new StringBuilder();
        foreach (string url in sasUrlList)
        {
            fileList.AppendLine(url);
            fileList.AppendLine();
        }
        WriteToFile(outFilePath, fileList.ToString());

        // Optionally download the output to the local machine.
        DownloadAssetToLocal(job.Id, outputFolder);
    }

    
    return job;
}

</code></pre>
</li>
<li>

2 .  Fügen Sie einen Aufruf an **CreateEncodingJob** zu Ihrer **Main**-Methode im Anschluss an die zuvor erstellten Zeilen hinzu.

   
    CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);
    

3 .  Fügen Sie die folgenden Helfermethoden zur Klasse hinzu. Diese Methoden unterstützten die **CreateEncodingJob**-Methode. Es folgt eine Zusammenfassung der Helfermethoden.
-   Die **GetLatestMediaProcessorByName**-Methode gibt einen korrekten Medienprozessor für Codierung, Verschlüsselung oder andere verwandte Verarbeitungsaufgaben zurück. Sie können den Medienprozessor mit dem entsprechenden Zeichenfolgennamen des Prozessors erstellen. Die folgenden Zeichenfolgen können an die Methode für den mediaProcessor-Parameter übergeben werden: **Azure Media Encoder**, **Azure Media Packager**, **Azure Media Encryptor**, **Storage Decryption**.

        ``` {}
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)    
             // Die folgenden Zeichenfolgen können an die Methode für den          
             // mediaProcessor-Parameter übergeben werden:
            //   Azure Media Encoder    
            //   Azure Media Packager   
            //   Azure Media Encryptor    
            //   Storage Decryption?    

            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)        
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));??    
            return processor;?}
        ```

-   Beim Ausführen von Aufträgen ist es nützlich, deren Fortschritt verfolgen zu können. Das folgende Codebeispiel definiert den StateChanged-Ereignishandler. Dieser Ereignishandler verfolgt den Auftragsfortschritt und liefert Statusupdates je nach Auftragsstatus. Der Code definiert außerdem die LogJobStop-Methode. Diese Helfermethode protokolliert Fehlerdetails.

<pre><code>
	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
    	Console.WriteLine("Job state changed event:");
    	Console.WriteLine("  Previous state: " + e.PreviousState);
    	Console.WriteLine("  Current state: " + e.CurrentState);

    	switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("********************");
            Console.WriteLine("Job is finished.");
            Console.WriteLine("Please wait while local tasks or downloads complete...");
            Console.WriteLine("********************");
            Console.WriteLine();
            Console.WriteLine();
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
        case JobState.Error:
            // Cast sender as a job.
            IJob job = (IJob)sender;
            // Display or log error details as needed.
            LogJobStop(job.Id);
            break;
        default:
            break;
    }
	}

		private static void LogJobStop(string jobId)
	{
    	StringBuilder builder = new StringBuilder();
    	IJob job = GetJob(jobId);

    	builder.AppendLine("\nThe job stopped due to cancellation or an error.");
    	builder.AppendLine("***************************");
    	builder.AppendLine("Job ID: " + job.Id);
    	builder.AppendLine("Job Name: " + job.Name);
    	builder.AppendLine("Job State: " + job.State.ToString());
    	builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
   		builder.AppendLine("Media Services account name: " + _accountName);
    	// Log job errors if they exist.  
    	if (job.State == JobState.Error)
    {
        builder.Append("Error Details: \n");
        foreach (ITask task in job.Tasks)
        {
            foreach (ErrorDetail detail in task.ErrorDetails)
            {
                builder.AppendLine("  Task Id: " + task.Id);
                builder.AppendLine("    Error Code: " + detail.Code);
                builder.AppendLine("    Error Message: " + detail.Message + "\n");
            }
        }
    }
    	builder.AppendLine("***************************\n");
    	// Write the output to a local file and to the console. The template 
    	// for an error output file is:  JobStop-{JobId}.txt
   	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
   		WriteToFile(outputFile, builder.ToString());
   		Console.Write(builder.ToString());
	}

	private static void LogJobDetails(string jobId)
	{
    	StringBuilder builder = new StringBuilder();
    	IJob job = GetJob(jobId);

    	builder.AppendLine("\nJob ID: " + job.Id);
    	builder.AppendLine("Job Name: " + job.Name);
    	builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
    	builder.AppendLine("Media Services account name: " + _accountName);

    	// Write the output to a local file and to the console. The template 
    	// for an error output file is:  JobDetails-{JobId}.txt
    	string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
    	WriteToFile(outputFile, builder.ToString());
    	Console.Write(builder.ToString());
	}
        
	private static string JobIdAsFileName(string jobID)
	{
    	return jobID.Replace(":", "_");
	}
</code></pre>
</li>
<li>

-   Die WriteToFile-Methode schreibt eine Datei in den angegebenen Ausgabeordner.

<pre><code>
static void WriteToFile(string outFilePath, string fileContent)
{
    StreamWriter sr = File.CreateText(outFilePath);
    sr.Write(fileContent);
    sr.Close();
}

</code></pre>
</li>
<li>
-   Nach der Codierung von Medienobjekten in Mediendiensten können Sie die Ausgabe-Medienobjekte des Codierungsauftrags abrufen. In diesem Lernprogramm werden zwei Zugriffsmöglichkeiten auf die Ausgabe von Codierungsaufträgen vorgestellt:

     -   Erstellen einer SAS-URL zu einem Medienobjekt auf dem Server.
     -   Download des Ausgabe-Medienobjekts vom Server.

   Die GetAssetSasUrlList-Methode erstellt eine Liste von SAS-URLs zu allen Dateien in einem Medienobjekt.

  
        static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
        {    

            // Deklarieren einer Liste für alle SAS-URLs.
            List<String> fileSasUrlList = new List<String>();    
            // Falls das Medienobjekt Dateien enthält, wird eine Liste     
            // von URLs zu den einzelnen Dateien erstellt und zurückgegeben. 
            foreach (IAssetFile file in asset.AssetFiles)?    
            {        
                 string sasUrl = BuildFileSasUrl(file, locator);        fileSasUrlList.Add(sasUrl);    
			}  
  			// Zurückgeben der Liste von SAS-URLs.
            return fileSasUrlList;
  		}
		// Erstellen und Zurückgeben einer SAS-URL zu einer einzelnen Datei in einem Medienobjekt. 
        static string BuildFileSasUrl(IAssetFile file, ILocator locator)
		{   
 			// Hinzufügen des Dateinamens zum Locatorpfad und?    
			// Erstellen einer kompletten SAS-URL für den Dateizugriff. Dieser Code reicht für die 
			// Erstellung der kompletten URL aus.
            var uriBuilder = new UriBuilder(locator.Path);   
			uriBuilder.Path += "/" + file.Name;??   

 			// Optional:  print the locator.Path to the asset, and ?   
 			// die komplette SAS-URL zur Datei?    
			Console.WriteLine("Locator path: ");?    
			Console.WriteLine(locator.Path);?    
			Console.WriteLine();?    
			Console.WriteLine("Full URL to file: ");?    
			Console.WriteLine(uriBuilder.Uri.AbsoluteUri);?    
			Console.WriteLine();???    
			

			//Zurückgeben der SAS-URL.
      		return uriBuilder.Uri.AbsoluteUri;
		}
        

    -   Die **DownloadAssetToLocal**-Methode lädt die einzelnen Dateien des Medienobjekts in einen lokalen Ordner herunter. Da das Medienobjekt in diesem Beispiel mit einer Eingabe-Mediendatei erstellt wurde, enthält das Ausgabe-Medienobjekt zwei Dateien: die codierte Mediendatei (eine .mp4-Datei) und eine .xml-Datei mit Metadaten zum Medienobjekt. Die Methode lädt beide Dateien herunter.

        ``` {}
        static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
		{    
			// Diese Methode zeigt den Download eines einzelnen Medienobjekts. 
            // Sie können jedoch die OutputAssets-Sammlung?    
			// durchlaufen und eventuell vorhandene weitere Medienobjekte herunterladen. 

            // Abrufen eines Verweises auf den Auftrag. 
            IJob job = GetJob(jobId);?    
			// Abrufen eines Verweises auf das erste Ausgabe-Medienobjekt. Im Fall von mehreren Ausgabe-Medienobjekten ?   
 			// können Sie die Liste durchlaufen und die einzelnen Objekte verarbeiten.
            IAsset outputAsset = job.OutputMediaAssets[0];
    
			IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);?    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);?    BlobTransferClient blobTransfer = new BlobTransferClient?    
		{
	        NumberOfConcurrentTransfers = 10,
	        ParallelTransferThreadCount = 10   
	    };
			var downloadTasks = new List<Task>();
	        foreach (IAssetFile outputFile in outputAsset.AssetFiles)  
		{        
			// Der folgende Ereignishandler prüft den Downloadfortschritt.
            outputFile.DownloadProgressChanged += DownloadProgress;

	        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
	
	        Console.WriteLine("File download path:  " + localDownloadPath);

	        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));??        outputFile.DownloadProgressChanged -= DownloadProgress;
	    }
		
		    Task.WaitAll(downloadTasks.ToArray());

		    return outputAsset;
		}
		
		static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
		
		{
		    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));

		}
        ```

    -   Die GetJob- und GetAsset-Helfermethoden rufen einen Verweis auf ein Auftragsobjekt und ein Medienobjekt mit gegebenen IDs ab. Mit einer ähnlichen LINQ-Abfrage können Sie Verweise auf andere Mediendienst-Objekte auf dem Server abrufen.

        ``` {}
        static IJob GetJob(string jobId)
		{
		    // Abrufen eines aktualisierten Verweises?    
			// nach Id mit einer Linq-Abfrage. 
            var jobInstance =        
				from j in _context.Jobs        
				where j.Id == jobId        
				select j;    
			// Zurückgeben des Auftragsverweises als Ijob. 
            IJob job = jobInstance.FirstOrDefault();

		    return job;
		}
		static IAsset GetAsset(string assetId)
		{
		    // Abrufen eines Medienobjekts mit einer Linq-Abfrage.
            var assetInstance =        
				from a in _context.Assets        
				where a.Id == assetId        
				select a;    
			// Verweisen auf das Medienobjekt als IAsset.
            IAsset asset = assetInstance.FirstOrDefault();    
			
			return asset;?}
        ```

Testen des Codes
----------------

Führen Sie das Programm aus (drücken Sie F5). Die Konsolenausgabe sollte in etwa wie folgt aussehen:

<pre><code>
Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
Time created: 11/14/2012 12:00:00 AM
Created assetFile interview2.wmv
Upload interview2.wmv
Done uploading of interview2.wmv using Upload()

Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
Job Name: My encoding job
Job submitted (client UTC time): 11/14/2012 10:09:39 PM
Media Services account name: Add-Media-Services-Account-Name
Media Services account location: Add-Media-Services-account-location-name

Job(My encoding job) state: Queued.
Please wait...

Job(My encoding job) state: Processing.
Please wait...

********************
Job(My encoding job) is finished.
Please wait while local tasks or downloads complete...
********************

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-11-14T23%3A07%3A01Z&amp;sr=c&amp;
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-11-14T23%3
A07%3A01Z&amp;sr=c&amp;si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8Fod3NsV%2F
BxERnav8Jb6hL7fxylq3oESc%3D

Locator path:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-11-14T23%3A07%3A01Z&amp;sr=c&amp;
si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
xylq3oESc%3D

Full URL to file:
https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
-9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&amp;se=2012-1
1-14T23%3A07%3A01Z&amp;sr=c&amp;si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&amp;sig=XKMo0qJI5w8F
od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

Downloads are in progress, please wait.

File download path:  C:\supportFiles\outputfiles\interview2.mp4
1.70952185308162 % download progress.
3.68508804454907 % download progress.
6.48870388360293 % download progress.
6.83808741232649 % download progress.
. . . 
99.0763740574049 % download progress.
99.1522674787341 % download progress.
100 % download progress.
File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
100 % download progress.

</code></pre>

1.  Nach der Ausführung der Anwendung geschieht Folgendes:

2.  Eine .wmv-Datei wird zum Mediendienst hochgeladen.

3.  Anschließend wird die Datei mit der **H264 Broadband 720p**-Voreinstellung des **Azure Media Encoders** codiert.

4.  Die Datei FileSasUrlList.txt wird im Ordner \\supportFiles\\outputFiles erstellt. Die Datei enthält die URL zum codierten Medienobjekt.

    Kopieren Sie die Medienobjekt-URL aus der Textdatei und fügen Sie die URL in einen Browser ein, um die Mediendatei abzuspielen.

5.  Die .mp4-Mediendatei und die \_metadata.xml-Datei werden in den Ordner outputFiles heruntergeladen.

**Hinweis**

Im Mediendienste-Objektmodell ist ein Medienobjekt eine Sammlung von Mediendienste-Inhalten, die eine oder mehrere Dateien enthält. Der Locatorpfad liefert eine Azure-Blob-URL, die als Basispfad zu diesem Medienobjekt im Azure-Speicher dient. Fügen Sie einen Dateinamen zum Locator-Basispfad hinzu, um eine einzelne Datei im Medienobjekt abzurufen.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die verschiedenen Programmieraufgaben für die Erstellung einer einfachen Mediendienste-Anwendung beschrieben. Sie kennen nun die grundlegenden Mediendienste-Programmieraufgaben inklusive Abrufen des Serverkontexts, Erstellen und Codieren von Medienobjekten und Herunterladen von Medienobjekt auf dem Server. Weitere Schritte und komplexere Programmieraufgaben finden Sie unter:

-   [Verwenden von Mediendiensten](http://www.windowsazure.com/de-de/develop/net/how-to-guides/media-services/)
-   [Erstellen von Anwendungen mit der Mediendienste-REST-API](http://msdn.microsoft.com/de-de/library/windowsazure/hh973618.aspx)

