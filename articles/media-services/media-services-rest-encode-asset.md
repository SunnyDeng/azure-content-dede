<properties 
	pageTitle="Codieren eines Medienobjekts mit Azure Media Encoder" 
	description="Erfahren Sie, wie Sie Medieninhalte in Media Services mithilfe des Azure Media Encoder codieren können. Die Codebeispiele basieren auf der REST-API." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Codieren eines Medienobjekts mit Azure Media Encoder

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

##Übersicht
Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Digitale Videodateien sind sehr umfangreich und möglicherweise zu groß, um sie über das Internet zu übermitteln oder auf den Geräten Ihrer Kunden ordnungsgemäß wiederzugeben. Bei der Codierung werden Video- und Audiodaten komprimiert, damit Ihre Kunden Ihre Medien anzeigen können.

Die Codierung ist einer der häufigsten Verarbeitungsvorgänge in Media Services. Sie erstellen Codierungsaufträge, um Mediendateien von einer Codierung in eine andere zu konvertieren. Zum Codieren können Sie den in Media Services integrierten Media Encoder verwenden. Außerdem können Sie Codierer von Media Services-Partnern verwenden. Externe Codierer finden Sie im Azure Marketplace. Sie können Details zu Codierungsaufgaben angeben, indem Sie für Ihren Encoder vordefinierte Zeichenfolgen oder vordefinierte Konfigurationsdateien verwenden. Welche Arten von Voreinstellungen verfügbar sind, erfahren Sie unter [Aufgabenvoreinstellungen für Azure Media Services](https://msdn.microsoft.com/library/azure/dn619392.aspx). Wenn Sie einen Encoder eines Drittanbieters verwendet haben, sollten Sie Ihre [Dateien überprüfen](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Je nach Art der Verarbeitung, die Sie durchführen möchten, können einem Auftrag eine oder mehrere Aufgaben zugeordnet sein. Über die REST-API können Sie Aufträge und die zugehörigen Aufgaben auf folgende zwei Arten erstellen: Aufgaben können Inline über die Aufgabennavigationseigenschaft von Auftragsentitäten oder über OData-Batchverarbeitung definiert werden. Das Media Services SDK verwendet Batchverarbeitung. Für die einfache Lesbarkeit der Codebeispiele in diesem Thema werden Aufgaben jedoch inline definiert. Weitere Informationen zur Batchverarbeitung finden Sie unter [Open Data Protocol (OData) Batch Processing](http://www.odata.org/documentation/odata-version-3-0/batch-processing/) (in englischer Sprache). Außerdem finden Sie ein Beispiel für die Batchverarbeitung im Thema [Auftrag](https://msdn.microsoft.com/library/azure/hh974289.aspx).

Es wird empfohlen, Zwischendateien immer in einen MP4-Satz mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung](https://msdn.microsoft.com/library/azure/jj889436.aspx) in das gewünschte Format zu konvertieren. Um dynamische Paketerstellung nutzen zu können, ist mindestens eine bedarfsgesteuerte Streamingeinheit für den Streamingendpunkt erforderlich, aus dem die Inhalte bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Skalieren von Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

Wenn Ihr Ausgabemedienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-rest-configure-asset-delivery-policy.md).



##Erstellen eines Auftrags mit einer einzelnen Codierungsaufgabe 

>[AZURE.NOTE]Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

>Nach der erfolgreichen Verbindung mit https://media.windows.net erhalten Sie eine 301 Redirect-Antwort, in der ein anderer Media Services-URI angegeben ist. Entsprechend der Beschreibung unter [Herstellen einer Verbindung mit einem Media Services-Konto über die Media Services-REST-API](media-services-rest-connect_programmatically.md) müssen Sie nachfolgende Aufrufe an den neuen URI senden.


Das folgende Beispiel zeigt, wie Sie einen Auftrag mit einer Aufgabe erstellen und bereitstellen, die für die Codierung eines Videos mit einer bestimmten Auflösung und Qualität konfiguriert wurde. Bei der Codierung mit Azure Media Encoder können Sie die [hier](https://msdn.microsoft.com/library/azure/dn619389.aspx) angegebenen Voreinstellungen für die Aufgabenkonfiguration verwenden.
	
Anforderung:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Antwort:
	
	HTTP/1.1 201 Created

	. . . 

##Überlegungen

- TaskBody-Eigenschaften MÜSSEN literalen XML-Code verwenden, um die Anzahl der von der Aufgabe verwendeten Eingabe- oder Ausgabemedienobjekte zu definieren. Das Thema „Aufgabe“ enthält die XML-Schemadefinition für den XML-Code.
- In der "TaskBody"-Definition muss jeder interne Wert für <inputAsset> und <outputAsset> als "JobInputAsset(value)" oder "JobOutputAsset(value)" festgelegt werden.
- Eine Aufgabe kann mehrere Ausgabemedienobjekte besitzen. Ein JobOutputAsset(x)-Objekt kann nur ein Mal als Ausgabe einer Aufgabe in einem Auftrag verwendet werden.
- Sie können JobInputAsset oder JobOutputAsset als Eingabemedienobjekt einer Aufgabe angeben.
- Aufgaben dürfen keine Schleife bilden.
- Der Value-Parameter, den Sie an JobInputAsset oder JobOutputAsset übergeben, stellt den Indexwert für ein Medienobjekt dar. Die tatsächlichen Medienobjekte werden in den Navigationseigenschaften InputMediaAssets und OutputMediaAssets für die Auftragsentitätsdefinition definiert. 
- Da Media Services als Grundlage OData v3 verwendet, wird auf die einzelnen Medienobjekte in den Navigationseigenschaftenauflistungen InputMediaAssets und OutputMediaAssets durch das Name-Wert-Paar „__metadata: uri“ verwiesen.
- InputMediaAssets ist mindestens einem Medienobjekt zugeordnet, das Sie in Media Services erstellt haben. OutputMediaAssets werden vom System erstellt. Sie verweisen nicht auf ein vorhandenes Medienobjekt.
- OutputMediaAssets können mithilfe des assetName-Attributs benannt werden. Wenn dieses Attribut nicht vorhanden ist, wird als Name von OutputMediaAsset der interne Textwert des <outputAsset>-Elements mit dem Wert des Auftragsnamens oder der Auftrags-ID (falls die Name-Eigenschaft nicht definiert ist) als Suffix verwendet. Wenn Sie für assetName z. B. den Wert „Sample“ festlegen, wird die Name-Eigenschaft von OutputMediaAsset auf „Sample“ festgelegt. Wenn Sie jedoch keinen Wert für assetName festgelegt haben, der Auftragsname jedoch auf NewJob festgelegt wurde, wird OutputMediaAsset der Name JobOutputAsset(Wert)_NewJob zugewiesen. 

Im folgenden Beispiel wird gezeigt, wie das AssetName-Attribut festgelegt wird:

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##Erstellen eines Auftrags mit verketteten Aufgaben

In zahlreichen Anwendungsszenarien möchten Entwickler eine Reihe von Verarbeitungsaufgaben erstellen. In Media Services können Sie eine Reihe verketteter Aufgaben erstellen. In jeder Aufgabe können verschiedene Verarbeitungsschritte ausgeführt und unterschiedliche Medienprozessoren verwendet werden. Innerhalb der verketteten Aufgaben kann ein Medienobjekt von einer Aufgabe an eine andere übergeben werden, sodass eine lineare Aufgabensequenz auf das Medienobjekt angewendet wird. In einem Auftrag ausgeführte Aufgaben müssen jedoch nicht sequenziell sein. Beim Erstellen einer verketteten Aufgabe werden die verketteten **ITask**-Objekte in einem einzelnen **IJob**-Objekt erstellt.

>[AZURE.NOTE]Die Aufgabenanzahl ist derzeit auf 30 Aufgaben pro Auftrag begrenzt. Wenn Sie mehr als 30 Aufgaben verketten müssen, erstellen Sie mehrere Aufträge, um die Aufgaben zu verteilen.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###Überlegungen

So aktivieren Sie die Aufgabenverkettung:

- Ein Auftrag muss mindestens zwei Aufgaben aufweisen.
- Es muss mindestens eine Aufgabe vorhanden sein, deren Eingabe die Ausgabe einer anderen Aufgabe im Auftrag ist.


## Erstellen eines Auftrags mithilfe einer JobTemplate


Bei der Verarbeitung von mehreren Medienobjekten, die einen gemeinsamen Satz von Aufgaben verwenden, eignen sich JobTemplates zum Festlegen der Standardaufgabenvorgaben, der Reihenfolge von Aufgaben usw.

Das folgende Beispiel zeigt, wie eine JobTemplate mit einer inline definierten TaskTemplate erstellt wird. Die TaskTemplate verwendet Azure Media Encoder als MediaProcessor zum Codieren der Medienobjektdatei. Es können jedoch auch andere MediaProcessors verwendet werden.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Im Gegensatz zu anderen Media Services-Entitäten müssen Sie eine neue GUID für jede TaskTemplate definieren und diese in der TaskTemplateId- und ID-Eigenschaft im Anforderungstext platzieren. Das Inhaltsidentifikationsschema muss das unter "Identifizieren von Azure Media Services-Entitäten" beschriebene Schema befolgen. Darüber hinaus können JobTemplates nicht aktualisiert werden. Stattdessen müssen Sie eine neue Vorlage mit Ihren aktualisierten Änderungen erstellen.
 

Im Erfolgsfall wird die folgende Antwort zurückgegeben:
	
	HTTP/1.1 201 Created
	
	. . .


Das folgende Beispiel zeigt, wie Sie einen Auftrag erstellen, der auf eine JobTemplate-ID verweist:

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

Im Erfolgsfall wird die folgende Antwort zurückgegeben:
	
	HTTP/1.1 201 Created
	
	. . . 


##Steuern von Media Services Encoder-Ausgabedateinamen 

Azure Media Encoder erstellt Ausgabedateinamen standardmäßig durch die Kombination verschiedener Attribute des Eingabemedienobjekts und des Codierungsvorgangs. Jedes Attribut wird mithilfe eines Makros identifiziert, wie nachstehend erläutert.

Im Folgenden finden Sie eine vollständige Liste der verfügbaren Makros für die Ausgabedateibenennung: "Audio Bitrate": Die beim Codieren der Audiodatei verwendete Bitrate, angegeben in KBit/s

- "Audio Codec": Der für die Audiocodierung verwendete Codec, gültige Werte sind: AAC, WMA und DDP
- "Channel Count": Die Anzahl der codierten Audiokanäle, gültige Werte sind: 1, 2 und 6
- "Default extension": Die Standarddateierweiterung 
- "Language": Der BCP-47-Sprachcode, der die in der Audiodatei verwendete Sprache anzeigt. Der Standardwert lautet derzeit "und". 
- "Original File Name": Der Name der Datei, die in den Azure-Speicher hochgeladen wird
- "StreamId": Die Datenstrom-ID gemäß Definition durch das StreamID-Attribut des <StreamInfo>-Element in der Voreinstellungsdatei 
- "Video Codec": Der für die Codierung verwendete Codec, gültige Werte sind: H264 und VC1
- "Video Bitrate": Die zum Codieren der Videodatei verwendete Bitrate, angegeben in KBit/s

Diese Makros können in jeder beliebigen Kombination verwendet werden, um die Benennung von mit Media Services Encoder generierten Dateien zu steuern. Beispielsweise lautet die standardmäßige Benennungskonvention:

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

Die Dateinamenkonvention wird mithilfe des DefaultMediaOutputFileName-Attributs des [<Voreinstellungen>](https://msdn.microsoft.com/library/azure/dn554334.aspx)-Elements angegeben. Beispiel:

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

Der Encoder fügt Unterstriche zwischen den einzelnen Makros ein, z. B. ergibt die Konfiguration oben einen Dateinamen wie "MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4".

##Nächste Schritte
Sie sind nun in der Lage, einen Auftrag zur Codierung von Medienobjekten zu erstellen und können mit dem Thema [How To Check Job Progress with Media Services](media-services-rest-check-job-progress.md) (Prüfen des Auftragsfortschritts mit Mediendiensten, in englischer Sprache) fortfahren.

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=58_postMigration-->