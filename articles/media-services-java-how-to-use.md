<properties linkid="develop-media-services-how-to-guides-media-services-java" urlDisplayName="Media Services" pageTitle="How to use Media Services (Java) - Azure feature guide" metaKeywords="Azure Media Services, Azure media, Azure streaming, azure media, azure streaming, azure encoding" description="Describes how to use Azure Media Services to perform common tasks including encoding, encrypting, and streaming resources." metaCanonical="" services="media-services" documentationCenter="Java" title="How to Use Media Services" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Verwenden von Media Services

Diese Anleitung beschreibt, die ersten Schritte bei der Programmierung für Azure Media Services mit Java. Hier finden Sie eine technische Übersicht über Media Services, die entsprechenden Konfigurationsschritte für Ihr Azure-Konto und Beispielcode für typische Programmieraufgaben.

## Inhaltsverzeichnis

-   [Was sind Media Services?][]
-   [Einrichten eines Azure-Kontos für Media Services][]
-   [Einrichtung für die Media Services-Entwicklung][]
-   [Gewusst wie: Verwenden von Media Services mit Java][]
-   [Zusätzliche Ressourcen][]

## <a name="what-are"></a><span class="short header">Was sind Media Services?</span>Was sind Media Services?

Azure Media Services bildet eine erweiterbare Medienplattform, welche die besten Komponenten der Microsoft-Medienplattform und der Medienkomponenten von Drittanbietern in Azure integriert. Media Services stellt in der Cloud eine Medienpipeline bereit, die es Industriepartnern ermöglicht, Komponententechnologien zu erweitern oder zu ersetzen. ISVs und Medienanbieter können mit Media Services durchgängige Medienlösungen erstellen. In dieser Übersicht werden die allgemeine Architektur und häufige Entwicklungsszenarien für Media Services beschrieben.

Das folgende Diagramm zeigt die grundlegende Media Services-Architektur.

![Media Services-Architektur][]

### Unterstützung von Media Services-Funktionen

Die aktuelle Version von Media Services stellt für die Entwicklung von Medienanwendungen in der Cloud die folgenden Funktionen bereit.

-   **Ingest**. Durch Ingest-Operationen werden Ressourcen in das System eingebracht, zum Beispiel, indem sie hochgeladen und verschlüsselt werden, bevor sie im Azure-Speicher platziert werden. Über die RTM-Version kann Media Services mit Partnerkomponenten integriert werden, um schnelle UDP (User Datagram-Protokoll)-Uploadlösungen anzubieten.
-   **Codieren**. Codieroperationen umfassen das Codieren, Transformieren und Konvertieren von Medienressourcen. Sie können Codierungsaufgaben in der Cloud mit dem Media Encoder ausführen, der in Media Services enthalten ist. Die folgenden Codieroptionen stehen zur Verfügung:
	-   Verwenden des Azure Media Encoder und Arbeiten mit einer Reihe von Standardcodecs und -formaten wie dem branchenführenden IIS Smooth Streaming, MP4 und Konvertierung in Apple HTTP Live Streaming
	-   Konvertieren ganzer Bibliotheken oder einzelner Dateien mit umfassender Kontrolle über Ein- und Ausgabe
	-   Eine breite Palette unterstützter Dateitypen, -Formate und Codecs (siehe [Unterstützte Dateitypen für Media Services][]).
	-   Unterstützte Formatkonvertierungen. Mit Media Services können Sie ISO MP4-Dateien (.mp4) in das Smooth Streaming-Dateiformat (PIFF 1.3) (.ismv; isma) konvertieren. Das Smooth Streaming-Dateiformat (PIFF) kann auch in Apple HTTP Live Streaming (.msu8, .ts) konvertiert werden.
-   **Schützen**. Schützen von Inhalten bedeutet, Live Streaming- oder bedarfsgesteuerte Inhalte zu verschlüsseln, damit sie sicher transportiert, gespeichert und zugestellt werden können. Media Services stellt eine von der DRM-Technologie unabhängige Lösung zum Schützen von Inhalten bereit. Momentan werden die DRM-Technologien Microsoft PlayReady Protection und MPEG Common Encryption unterstützt. Die Unterstützung weiterer DRM-Technologien ist vorgesehen.
-   **Streaming**. Streaminginhalte werden live oder bedarfsgesteuert an Clients ausgeliefert. Alternativ können Sie einzelne Mediendateien aus der Client abrufen oder herunterladen. Media Services stellt eine formatunabhängige Lösung zum Streamen von Inhalten bereit. Media Services unterstützt den Streamingursprung für die Formate Smooth Streaming, Apple HTTP Live Streaming und MP4. Die Unterstützung weiterer Formate ist vorgesehen. Sie können Streaminginhalte nahtlos über Azure CDN oder ein externes CDN ausliefern und Ihre Dienste auf diese Weise für Millionen von Benutzern skalieren.

### Media Services-Entwicklungsszenarien

Media Services unterstützt zahlreiche gängige Szenarien für die Medienentwicklung, wie in der folgenden Tabelle beschrieben.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<thead>

<tr>

<th>
Szenario

</th>

<th>
Beschreibung

</th>

</tr>

</thead>

<tbody>

<tr>

<td>
Erstellen von durchgängigen Workflows

</td>

<td>
Erstellen Sie umfassende Medienworkflows ausschließlich in der Cloud. Vom Hochladen von Medien bis zum Verteilen von Inhalten stellt Media Services eine Reihe von Komponenten zur Verfügung, die für bestimmte Anwendungsworkflows kombiniert werden können. Zu den aktuellen Fähigkeiten zählen Hochladen, Speichern, Codieren, Formatkonvertierung, Schutz von Inhalten und bedarfsgesteuertes Streaming.

</td>

</tr>

<tr>

<td>
Erstellen von hybriden Workflows

</td>

<td>
Sie können Media Services in vorhandene Tools und Prozesse integrieren. Codieren Sie Inhalte z. B. am Standort, und laden Sie sie danach zu Media Services hoch, um sie in mehreren Formaten zu transcodieren und über ein Azure-CDN oder ein Drittanbieter-CDN zu übermitteln. Media Services kann individuell über standardmäßige REST-APIs aufgerufen werden und in externe Anwendungen und Dienste integriert werden.

</td>

</tr>

<tr>

<td>
Cloud-Unterstützung für Media Player

</td>

<td>
Sie können Medien auf mehreren Geräten (einschließlich iOS-, Android- und Windows-Geräten) und Plattformen erstellen, verwalten und übermitteln.

</td>

</tr>

</tbody>

</table>
</p>
### Media Services-Cliententwicklung

Erweitern Sie die Reichweite Ihrer Media Services-Lösung mithilfe von SDKs und Player-Frameworks, um Medienclientanwendungen zu erstellen. Mit diesen Clients können Entwickler Media Services-Anwendungen erstellen, die auf einer großen Bandbreite von Geräten und Plattformen ein überzeugendes Benutzererlebnis bieten. Je nach den Geräten, für die Sie Clientanwendungen entwickeln möchten, stehen verschiedene SDKs und Player-Frameworks von Microsoft und externen Partnern zur Verfügung.

Im Folgenden sind einige der verfügbaren Client-SDKs und Player-Frameworks aufgeführt. Weitere Informationen zu diesen und weiteren geplanten SDKs und Player-Frameworks sowie zu deren unterstütztem Funktionsumfang finden Sie unter [Media Services-Cliententwicklung][].

#### Unterstützung von Mac- und PC-Clients

Für PCs und Macs können Sie eine Streaming-Anwendung mithilfe von Microsoft Silverlight oder Adobe Open Source Media Framework erstellen.

-   [Smooth Streaming Client for Silverlight (Smooth Streaming-Client für Silverlight, in englischer Sprache)][]
-   [Microsoft Media Platform: Player Framework for Silverlight][] (Microsoft-Medienplattform: Player-Framework für Silverlight, in englischer Sprache)
-   [Smooth Streaming Plugin for OSMF 2.0][] (Smooth Streaming-Plugin für OSMF 2.0, in englischer Sprache) Informationen zur Verwendung dieses Plug-In finden Sie unter [How to Use the Microsoft Smooth Streaming Plugin for the Adobe Open Source Media Framework][] (Verwenden des Microsoft Smooth Streaming-Plug-In für das Adobe Open Source Media Framework, in englischer Sprache).

#### Windows 8-Anwendungen

Für Windows 8 können Sie Windows Store-Anwendungen mit einer der unterstützten Entwicklungssprachen und Konstrukten wie HTML, Javascript, XAML, C# und C+ erstellen.

-   [Microsoft Smooth Streaming Client SDK for Windows 8][] (in englischer Sprache). Weitere Informationen zum Erstellen einer Windows Store-Anwendung mit diesem SDK finden Sie unter [Erstellen einer Smooth Streaming Windows Store-Anwendung][]. Informationen zum Erstellen eines Smooth Streaming-Players in HTML5 finden Sie unter [Walkthrough: Building Your First HTML5 Smooth Streaming Player][] (Exemplarische Vorgehensweise: Erstellen Ihres ersten HTML5 Smooth Streaming-Players, in englischer Sprache).

-   [Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications][] (Microsoft-Medienplattform: Player-Framework für Windows 8 Windows Store-Anwendungen, in englischer Sprache)

#### Xbox

Xbox unterstützt Xbox LIVE-Anwendungen, die Smooth Streaming-Inhalte nutzen können. Das Xbox LIVE Application Development Kit (ADK) umfasst Folgendes:

-   Smooth Streaming-Client für Xbox LIVE ADK
-   Microsoft Media Platform: Player-Framework für Xbox LIVE ADK

#### Eingebettete oder dedizierte Geräte

Geräte wie verbundene Fernsehgeräte, Set-Top-Boxen, Blu-Ray-Player, OTT-TV-Boxen und mobile Geräte, die ein benutzerdefiniertes Anwendungsentwicklungs-Framework und eine benutzerdefinierte Medienpipeline haben. Microsoft stellt die folgenden Portierungs-Kits zur Verfügung, die lizenziert werden können, und gibt Partnern die Möglichkeit, die Smooth Streaming-Wiedergabe für die Plattform zu portieren.

-   [Smooth Streaming Client Porting Kit (Portierungs-Kit für Smooth Streaming-Client, in englischer Sprache)][]
-   [Microsoft PlayReady Device Porting Kit (Portierungs-Kit für Microsoft-PlayReady-Geräte, in englischer Sprache)][]

#### Windows Phone

Microsoft stellt ein SDK bereit, das zum Erstellen von erstklassigen Videoanwendungen für Windows Phone verwendet werden kann.

-   [Smooth Streaming Client for Silverlight (Smooth Streaming-Client für Silverlight, in englischer Sprache)][]
-   [Microsoft Media Platform: Player Framework for Silverlight][] (Microsoft-Medienplattform: Player-Framework für Silverlight, in englischer Sprache)

#### iOS-Geräte

Für iOS-Geräte wie z. B. iPhone, iPod und iPad stellt Microsoft ein SDK zur Verfügung, mit dem Sie Anwendungen für diese Plattformen erstellen können, um erstklassige Videoinhalte zu übermitteln: Smooth Streaming SDK für iOS Devices mit PlayReady. Das SDK ist nur für Lizenzinhaber verfügbar. Wenn Sie weitere Informationen wünschen, [senden Sie eine E-mail an Microsoft][]. Informationen zur iOS-Entwicklung finden Sie im [iOS Developer Center][].

#### Android-Geräte

Mehrere Microsoft-Partner stellen SDKs für die Android-Plattform zur Verfügung, welche die Smooth Streaming-Wiedergabe auf Android-Geräten ermöglichen. Wenden Sie sich per [E-Mail an Microsoft][], wenn Sie mehr über die Partner erfahren möchten.

## <a name="setup-account"></a><span class="short header">Einrichten eines Kontos</span>Einrichten eines Azure-Kontos für Media Services

Sie können Ihr Media Services-Konto im Azure-Verwaltungsportal einrichten. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos][]. Nach der Erstellung des Kontos im Verwaltungsportal können Sie Ihren Computer für die Mediendienste-Entwicklung einrichten.

## <a name="setup-dev"> </a><span class="short header">Einrichtung für die Media Services-Entwicklung</span>

Dieser Abschnitt beschreibt allgemeine Voraussetzungen für die Media Services-Entwicklung mithilfe des Media Services SDK für Java.

### Voraussetzungen

-   Ein Mediendienstekonto in einem neuen oder existierenden Azure-Abonnement. Weitere Informationen finden Sie unter [Vorgehensweise: Erstellen eines Mediendienstekontos][].
-   Die Azure-Bibliotheken für Java, die Sie im [Azure Developer Center für Java][] installieren können.

## <a name="connect"> </a><span class="short header">Verwenden von Media Services mit Java</span>Gewusst wie: Verwenden von Media Services mit Java

Der folgende Code erstellt ein Medienobjekt, lädt eine Mediendatei in das Medienobjekt hoch, führt eine Aufgabe zur Transformation des Medienobjekts aus und lädt die Ausgabedateien des transformierten Medienobjekts herunter.

Sie benötigen ein Media Services-Konto, um diesen Code verwenden zu können. Informationen zum Einrichten eines Kontos finden Sie unter [Erstellen eines Media Services-Kontos][].

Fügen Sie Ihre eigenen Werte in die Variablen `clientId` und `clientSecret` ein. Der Code verwendet außerdem eine lokale Datei mit dem Namen `c:/media/MPEG4-H264.mp4`. Hier müssen Sie eine eigene Datei angeben. Der Code benötigt außerdem einen Ausgabeordner mit dem Namen `c:/output`, in den die Ausgabedateien heruntergeladen werden.

    import java.io.*;
    import java.net.URI;
    import java.net.URISyntaxException;
    import java.security.NoSuchAlgorithmException;
    import java.util.EnumSet;
    import java.util.List;

    import com.microsoft.windowsazure.services.blob.client.*;
    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.core.storage.StorageException;
    import com.microsoft.windowsazure.services.media.*;
    import com.microsoft.windowsazure.services.media.models.*;

    public class HelloMediaServices 
    {

        private static MediaContract mediaService;
        private static AssetInfo assetToEncode, encodedAsset;

        public static void main(String[] args) 
        {
            try 
            {

                // Set up the MediaContract object to call into the media services.
                Init();
                
                // Upload a local file to a media asset.
                Upload();

                // Transform the asset.
                Transform();

                // Retrieve the URL of the asset's transformed output.
                Download();

                // Delete all assets. 
                // When you want to delete the assets that have been uploaded, 
                // comment out the calls to Upload(), Transfer(), and Download(), 
                // and uncomment the following call to Cleanup().
                //Cleanup();

                System.out.println("Application completed.");
            }
            catch (ServiceException se) 
            {
                System.out.println("ServiceException encountered.");
                System.out.println(se.getMessage());
            }
            catch (Exception e) 
            {
                System.out.println("Exception encountered.");
                System.out.println(e.getMessage());
            }
        }

        // Initialize the server context to get programmatic access to the Media Services programming objects.
        // The media services URI, OAuth URI and scope can be used exactly as shown.
        // Substitute your media service account name and access key for the clientId and clientSecret variables.
        // You can obtain your media service account name and access key from the Media Services section
        // of the Azure Management portal, https://manage.windowsazure.com.
        private static void Init() throws ServiceException 
        {
            String mediaServiceUri = "https://media.windows.net/API/";
            String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
            String clientId = "your_client_id";  // Use your media service account name.
            String clientSecret = "your_client_secret"; // Use your media service access key. 
            String scope = "urn:WindowsAzureMediaServices";

            // Specify the configuration values to use with the MediaContract object.
            Configuration configuration = MediaConfiguration
                    .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);

            // Create the MediaContract object using the specified configuration.
            mediaService = MediaService.create(configuration);
            
        }

        // Upload a media file to your Media Services account.
        // This code creates an asset, an access policy (using Write access) and a locator, 
        // and uses those objects to upload a local file to the asset.
        private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
        {
            
            WritableBlobContainerContract uploader;
            
            AccessPolicyInfo uploadAccessPolicy;
            LocatorInfo uploadLocator = null;
            
            // Create an asset.
            assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
            System.out.println("Created asset with id: " + assetToEncode.getId());

            // Create an access policy that provides Write access for 15 minutes.
            uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
                                                                         15.0, 
                                                                         EnumSet.of(AccessPolicyPermission.WRITE)));
            System.out.println("Created upload access policy with id: "
                    + uploadAccessPolicy.getId());

            // Create a locator using the access policy and asset.
            // This will provide the location information needed to add files to the asset.
            uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
                    assetToEncode.getId(), LocatorType.SAS));
            System.out.println("Created upload locator with id: " + uploadLocator.getId());

            // Create the blob writer using the locator.
            uploader = mediaService.createBlobWriter(uploadLocator);

            // The name of the file as it will exist in your Media Services account.
            String fileName = "MPEG4-H264.mp4";  

            // The local file that will be uploaded to your Media Services account.
            InputStream input = new FileInputStream(new File("c:/media/" + fileName));

            // Upload the local file to the asset.
            uploader.createBlockBlob(fileName, input);

            // Inform Media Services about the uploaded files.
            mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
            System.out.println("File uploaded.");
            
           
            System.out.println("Deleting upload locator and access policy.");
            mediaService.delete(Locator.delete(uploadLocator.getId()));
            mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
            
        }

        // Create a job that contains a task to transform the asset.
        // In this example, the asset will be transformed using the Azure Media Encoder.
        private static void Transform() throws ServiceException, InterruptedException 
        {
            // Use the Azure Media Encoder, by specifying it by name.
            // Retrieve the list of media processors that match this name.      
            ListResult<MediaProcessorInfo> mediaProcessors = mediaService
                    .list(MediaProcessor.list()
                    .set("$filter", "Name eq 'Azure Media Encoder'"));
            
            // Use the latest version of the media processor.
            MediaProcessorInfo mediaProcessor = null;
            for (MediaProcessorInfo info : mediaProcessors)
            {
                if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
                {
                    mediaProcessor = info;
                }
            }

            System.out.println("Using processor: " + mediaProcessor.getName() +
                    " " + mediaProcessor.getVersion());

            // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
            // Information on the various configurations can be found at
            // http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx.
            // This example uses only one task, but others could be added.
            Task.CreateBatchOperation task = Task.create(
                    mediaProcessor.getId(),
                    "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
                    .setConfiguration("H264 Broadband 720p").setName("MyTask");

            // Create a job creator that specifies the asset, priority and task for the job. 
            Job.Creator jobCreator = Job.create()
                .setName("myJob")
                .addInputMediaAsset(assetToEncode.getId())
                .setPriority(2)
                .addTaskCreator(task);

            // Create the job within your Media Services account.
            // Creating the job automatically schedules and runs it.
            JobInfo jobInfo = mediaService.create(jobCreator);
            String jobId = jobInfo.getId();
            System.out.println("Created job with id: " + jobId);
            // Check to see if the job has completed.
            CheckJobStatus(jobId);
            // Done with the job.

            // Retrieve the output asset.
            ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
            encodedAsset = outputAssets.get(0);
        }

        // Download the output assets of the transformed asset.
        private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
        {
            
            AccessPolicyInfo downloadAccessPolicy = null;

            downloadAccessPolicy =
                    mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
            System.out.println("Created download access policy with id: "
                    + downloadAccessPolicy.getId());
            
            LocatorInfo downloadLocator = null;
            downloadLocator = mediaService.create(
                    Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
            System.out.println("Created download locator with id: " + downloadLocator.getId());        

            System.out.println("Accessing the output files of the encoded asset.");
            // Iterate through the files associated with the encoded asset.
            for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
            {
                String fileName = assetFile.getName();
                
                System.out.print("Downloading file: " + fileName + ". ");
                String locatorPath = downloadLocator.getPath();
                int startOfSas = locatorPath.indexOf("?");

                String blobPath = locatorPath + fileName;
                if (startOfSas >= 0) 
                {
                    blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
                }
                URI baseuri = new URI(blobPath);
                CloudBlobClient blobClient;
                blobClient = new CloudBlobClient(baseuri);
                
                // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
                String localFileName = "c:/output/" + fileName;
                
                CloudBlockBlob sasBlob;
                sasBlob = new CloudBlockBlob(baseuri, blobClient);
                File fileTarget = new File(localFileName);
                
                sasBlob.download(new FileOutputStream(fileTarget));
                System.out.println("Download complete.");
                
            }

            System.out.println("Deleting download locator and access policy.");
            mediaService.delete(Locator.delete(downloadLocator.getId()));
            mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
          
        }
        
        // Remove all assets from your Media Services account.
        // You could instead remove assets by name or ID, etc., but for 
        // simplicity this example removes all of them.
        private static void Cleanup() throws ServiceException 
        {
            // Retrieve a list of all assets.
            List<AssetInfo> assets = mediaService.list(Asset.list());

            // Iterate through the list, deleting each asset.
            for (AssetInfo asset: assets)
            {
                System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
                mediaService.delete(Asset.delete(asset.getId()));
            }
        }

        // Helper function to check to on the status of the job.
        private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
        {
            int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
            JobState jobState = null;
            while (maxRetries > 0) 
            {
                Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
                // Determine the job state.
                jobState = mediaService.get(Job.get(jobId)).getState();
                System.out.println("Job state is " + jobState);

                if (jobState == JobState.Finished || 
                    jobState == JobState.Canceled || 
                    jobState == JobState.Error) 
                {
                    // The job is done.
                    break;
                }
                // The job is not done. Sleep and loop if max retries 
                // has not been reached.
                maxRetries--;
            }
      
        }

    }

Ihre Medienobjekte werden im Azure-Speicher gespeichert. Sie verwenden jedoch ausschließlich die Azure Media Services-APIs (nicht die Azure-Speicher-APIs) zum Hinzufügen, Ändern und Löschen von Medienobjekten.

### Ermitteln der verfügbaren Medienprozessoren

Der obige Code verwendet einen Medienprozessor direkt über dessen Namen (falls mehrere Versionen existieren, wird die neueste Version verwendet). Sie können den folgenden Code verwenden, um zu ermitteln, welche Medienprozessoren verfügbar sind.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

Als Alternative zeigt der folgende Code, wie Sie die ID eines Medienprozessors über dessen Namen abrufen können.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

### Abbrechen eines Auftrags

Der folgende Code zeigt, wie Sie einen laufenden Auftrag über dessen ID abbrechen können.

    mediaService.action(Job.cancel(jobId));

## <a name="additional-resources"></a><span class="short header">Zusätzliche Ressourcen</span>Zusätzliche Ressourcen

Sie finden die Media Services Javadoc-Dokumentation unter [Dokumentation der Azure-Bibliotheken für Java][].

  [Was sind Media Services?]: #what-are
  [Einrichten eines Azure-Kontos für Media Services]: #setup-account
  [Einrichtung für die Media Services-Entwicklung]: #setup-dev
  [Gewusst wie: Verwenden von Media Services mit Java]: #connect
  [Zusätzliche Ressourcen]: #additional-resources
  [Media Services-Architektur]: ./media/media-services-dotnet-how-to-use/wams-01.png
  [Media Services-Cliententwicklung]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223283.aspx
  [Smooth Streaming Client for Silverlight (Smooth Streaming-Client für Silverlight, in englischer Sprache)]: http://www.iis.net/download/smoothclient
  [Microsoft Media Platform: Player Framework for Silverlight]: http://smf.codeplex.com/documentation
  [Smooth Streaming Plugin for OSMF 2.0]: http://go.microsoft.com/fwlink/?LinkId=275022
  [How to Use the Microsoft Smooth Streaming Plugin for the Adobe Open Source Media Framework]: http://go.microsoft.com/fwlink/?LinkId=275034
  [Microsoft Smooth Streaming Client SDK for Windows 8]: http://go.microsoft.com/fwlink/?LinkID=246146
  [Erstellen einer Smooth Streaming Windows Store-Anwendung]: http://go.microsoft.com/fwlink/?LinkId=271647
  [Walkthrough: Building Your First HTML5 Smooth Streaming Player]: http://msdn.microsoft.com/en-us/library/jj573656.aspx
  [Microsoft Media Platform: Player Framework for Windows 8 Windows Store Applications]: http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home
  [Smooth Streaming Client Porting Kit (Portierungs-Kit für Smooth Streaming-Client, in englischer Sprache)]: http://www.microsoft.com/en-us/mediaplatform/sspk.aspx
  [Microsoft PlayReady Device Porting Kit (Portierungs-Kit für Microsoft-PlayReady-Geräte, in englischer Sprache)]: http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx
  [senden Sie eine E-mail an Microsoft]: mailto:askdrm@microsoft.com
  [iOS Developer Center]: https://developer.apple.com/devcenter/ios/index.action
  [E-Mail an Microsoft]: mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices
  [Vorgehensweise: Erstellen eines Mediendienstekontos]: http://go.microsoft.com/fwlink/?linkid=256662
  [Azure Developer Center für Java]: http://www.windowsazure.com/en-us/develop/java/
  [Erstellen eines Media Services-Kontos]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [Dokumentation der Azure-Bibliotheken für Java]: http://dl.windowsazure.com/javadoc/
