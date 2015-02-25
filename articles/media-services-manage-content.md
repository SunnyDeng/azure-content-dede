<properties pageTitle="Verwalten von Medieninhalten - Azure Media Services" description="Erfahren Sie, wie Sie Ihre Medieninhalte in Azure Media Services verwalten können." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>





# Verwalten von Inhalten in Media Services #

In diesem Thema wird veranschaulicht, wie Sie mit dem Azure-Verwaltungsportal Medieninhalte in Ihrem Media Services-Konto verwalten.

Derzeit können Sie im Portal die folgenden Inhaltsoperationen direkt ausführen:

- Zeigen Sie Inhaltsinformationen an wie Veröffentlichungsstatus, veröffentlichte URL, Größe, die Uhrzeit der letzten Aktualisierung und ob das Medienobjekt verschlüsselt ist.
- Hochladen neuer Inhalte
- Indizieren von Inhalten
- Codieren von Inhalten
- Wiedergeben von Inhalten
- Veröffentlichen/Aufheben der Veröffentlichung von Inhalten


## Gewusst wie: Hochladen von Inhalten 


1. Klicken Sie im [Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) auf **Media Services** und dann auf den Media Services-Kontonamen.
2. Wählen Sie die Seite "INHALT" aus. 
3. Klicken Sie auf der Seite oder unten im Portal auf **Hochladen**. 
4. Navigieren Sie im Dialogfeld **Inhalte hochladen** zur gewünschten Ressourcendatei. Klicken Sie auf die Datei, und klicken Sie dann auf **Öffnen**, oder drücken Sie **Eingabe**.

	![UploadContentDialog][uploadcontent]

5. Klicken Sie im Dialogfeld "Inhalte hochladen" auf den Häkchenknopf, um die Datei und den Inhaltsnamen zu akzeptieren.
6. Das Hochladen beginnt, und Sie können den Fortschritt unten im Portal verfolgen.  

	![JobStatus][status]

Nach Abschluss des Hochladevorgangs wird in der Liste "Inhalt" die neue Ressource aufgeführt. Entsprechend der Namenskonvention wird "**-Source**" an das Ende gehängt, damit neue Inhalte als Quellinhalte für Codieraufgaben erfasst werden können.

![ContentPage][contentpage]

Wenn der Wert für die Dateigröße nach dem Hochladen nicht aktualisiert wird, drücken Sie die Schaltfläche **Metadaten synchronisieren**. Damit wird die Dateigröße der Ressource mit der tatsächlichen Dateigröße im Speicher synchronisiert, und der Wert auf der Seite "Inhalt" wird aktualisiert.	

## Gewusst wie: Indizieren von Inhalten

Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. Sie können Ihre Inhalte mithilfe des Verwaltungsportals und den unten gezeigten Schritten indizieren. Wenn Sie mehr Kontrolle darüber möchten, wie und mit welchen Dateien die Indizierung erfolgt, können Sie das Media Services SDK für .NET oder REST-APIs verwenden. Weitere Informationen finden Sie unter [Indizieren von Mediendateien mit Azure Media Indexer](https://msdn.microsoft.com/de-de/library/azure/dn783455.aspx).

Die folgenden Schritte veranschaulichen, wie Sie das Verwaltungsportal verwenden, um die Inhalte zu indizieren.

1. Wählen Sie die Datei aus, die Sie indizieren möchten.
	Wenn die Indizierung für diesen Dateityp unterstützt wird, wird die Schaltfläche "Process" unten auf der Inhaltsseite aktiviert.
1. Klicken Sie auf die Schaltfläche "Process".
2. Wählen Sie im Dialogfeld **Process** den **Azure Media Indexer**-Prozessor aus.
3. Anschließend füllen Sie im Dialogfeld "Process" die Informationen zu **Titel** und **Beschreibung** der Eingabe-Mediendatei aus.
	
	![Process][process]

## Gewusst wie: Codieren von Inhalten

Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Media Services bietet einen Media Encoder, mit dem Sie angeben, wie Ihre Inhalte codiert werden sollen (z. B. Codecs, Dateiformat, Auflösung und Bitrate). 

Bei der Arbeit mit Azure Media Services ist eines der häufigsten Szenarios das adaptive Bitraten-Streaming an Clients. Mit adaptivem Bitrate-Streaming kann der Client während der Videodarstellung auf einen höheren oder niedrigeren Bitraten-Stream wechseln, basierend auf der aktuellen Netzwerkbandbreite, CPU-Auslastung und anderen Faktoren. Media Services unterstützt die folgenden adaptiven Bitraten-Streaming-Technologien: HTTP Live Streaming (HLS), Smooth Streaming, MPEG-DASH und HDS (für Adobe PrimeTime/Access-Lizenznehmer verfügbar). 

Media Services bietet dynamische Paketerstellung zum Übermitteln Ihrer MP4 mit adaptiver Bitrate oder Smooth Streaming-codierten Inhalte in Streaming-Formaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming, HDS), ohne dass Sie diese Streaming-Formate erneut verpacken müssen. 

Um dynamische Paketerstellung nutzen zu können, müssen Sie Folgendes ausführen:

- Codieren oder Transcodieren Ihrer Zwischendatei (Quelle) der Datei in einen Satz von MP4-Dateien oder adaptiven Smooth Streaming-Dateien mit adaptiver Bitrate (die Codierungsschritte sind weiter unten in diesem Lernprogramm veranschaulicht),  
- Abrufen mindestens einer bedarfsgesteuerten Streaming-Einheit für den Streaming-Endpunkt, von dem aus Sie Ihre Inhalte bereitstellen möchten. Weitere Informationen finden Sie unter [Skalieren von reservierten Einheiten für bedarfsgesteuertes Streaming](http://azure.microsoft.com/de-de/documentation/articles/media-services-how-to-scale/).

Mit dynamischer Paketerstellung müssen Sie nur speichern und für die Dateien in einem Speicherformat bezahlen, und Media Services erstellt und verarbeitet die entsprechende Antwort basierend auf Anforderungen von einem Client. 

Beachten Sie, dass reservierte Einheiten für bedarfsgesteuertes Streaming nicht nur die dynamische Paketerstellung ermöglichen, sondern auch eine dedizierte Ausgangskapazität bieten, die in Schritten von 200 Mbit/s erworben werden können. Standardmäßig wird das bedarfsgesteuerte Streaming in einem Modell mit einer gemeinsam genutzten Instanz konfiguriert, für das Serverressourcen (z. B. Rechen- und Ausgangskapazität usw.) mit allen anderen Benutzern gemeinsam genutzt werden. Um den Durchsatz des bedarfsgesteuerten Streamings zu erhöhen, sollten Sie die reservierten Einheiten für bedarfsgesteuertes Streaming kaufen.


Dieser Abschnitt beschreibt die Schritte, die Sie ausführen können, um Ihre Inhalte mit Azure Media Encoder im Verwaltungsportal zu codieren.

1.  Wählen Sie die Datei aus, die Sie codieren möchten.
	Wenn die Codierung für diesen Dateityp unterstützt wird, wird die Schaltfläche "Process" unten auf der Inhaltsseite aktiviert.
4. Wählen Sie im Dialogfeld **Process** den **Azure Media Encoder**-Prozessor aus.
5. Wählen Sie eine der **Codierungskonfigurationen** aus.

	![Process2][process2]

		
	Im Thema [Systemvoreinstellungen für den Azure Media Encoder](https://msdn.microsoft.com/de-de/library/azure/dn619392.aspx) wird erläutert, was jede Voreinstellung in den Kategorien **Voreinstellungen für adaptives Streaming (dynamische Paketerstellung)**, **Voreinstellungen für progressiven Download**, **Legacy-Voreinstellungen für adaptives Streaming**  bedeutet.  


	Die **anderen** Konfigurationen werden nachfolgend beschrieben:

	+ **Codieren mit PlayReady-Inhaltsschutz**. Diese Voreinstellung produziert ein Medienobjekt, das mit dem PlayReady-Inhaltsschutz codiert ist.  
	
	
		Standardmäßig wird der Media Services PlayReady-Lizenzierungsdienst verwendet. Verwenden Sie REST oder die Media Services .NET SDK-APIs, um andere Dienste zu verwenden, von denen Clients eine Lizenz zum Abspielen der mit PlayReady verschlüsselten Inhalte abrufen können. Weitere Informationen finden Sie unter [Statische Verschlüsselung zum Schützen Ihrer Inhalte]() und durch Einstellen der **licenseAcquisitionUrl** -Eigenschaft in der Media Encryptor-Voreinstellung. Alternativ können Sie dynamische Verschlüsselung verwenden und die Eigenschaft **PlayReadyLicenseAcquisitionUrl** laut Beschreibung unter[Verwenden dynamischer PlayReady-Verschlüsselung und des Lizenzbereitstellungsdiensts](http://go.microsoft.com/fwlink/?LinkId=507720) festlegen. 
	+ **Wiedergabe auf PC/Mac (über Flash/Silverlight)**. Diese Voreinstellung erzeugt eine Smooth Streaming-Ressource mit den folgenden Eigenschaften: AAC-Stereo-Audio mit 44,1 kHz und 16 Bits/Sample, codiert mit konstanter Bitrate (96 Kbit/s) und 720p-Video codiert mit 6 konstanten Bitraten von 3400 Kbit/s bis 400 Kbit/s mithilfe des H.264-Hauptprofils und 2-Sekunden-GOPs.
	+ **Wiedergabe über HTML5 (IE, Chrome, Safari)**. Diese Voreinstellung erzeugt eine einzelne MP4-Datei mit den folgenden Eigenschaften: AAC-Stereo-Audio mit 44,1 kHz und 16 Bits/Sample codiert mit konstanter Bitrate (128 Kbit/s) und 720p-Video codiert mit konstanter Bitrate von 4500 Kbit/s mithilfe des H.264-Hauptprofils.
	+ **Wiedergabe auf iOS-Geräten und PC/Mac**. Diese Voreinstellung erzeugt eine Ressource mit den gleichen Eigenschaften wie die Smooth Streaming-Ressource (Beschreibung siehe oben), jedoch in einem Format, das zur Übermittlung von Apple HLS-Streams auf iOS-Geräten verwendet werden kann. 

5. Anschließend geben Sie den gewünschten Anzeigenamen für den Inhalt ein oder übernehmen den Standardnamen. Klicken Sie anschließend auf den Häkchenknopf, um den Codiervorgang zu starten. Den Fortschritt des Vorgangs können Sie unten im Portal verfolgen.
6. Klicken Sie auf "OK".

	Nach Abschluss der Codierung enthält die Inhaltsseite die codierte Datei. 

	Um den Fortschritt des Codierungsauftrags anzuzeigen, wechseln Sie zur Seite **JOBS**.  


	Wenn der Wert für die Dateigröße nicht aktualisiert wird, nachdem die Codierung erfolgt ist, klicken Sie auf die Schaltfläche **Sync Metadata**. Damit wird die Dateigröße der Ausgaberessource mit der tatsächlichen Dateigröße im Speicher synchronisiert, und der Wert auf der Seite "Inhalt" wird aktualisiert.	

## Gewusst wie: Veröffentlichen von Inhalten

Wenn Sie den Inhalt veröffentlichen, wird er mit Streaming oder einer progressiven Download-URL bereitgestellt. Ihr Client kann die Videos mithilfe dieser URL wiedergeben.

1. Klicken Sie auf eine Ressource, die veröffentlicht werden soll. 
2. Klicken Sie dann auf die Schaltfläche "Publish". 
	
	Sobald die Inhalte unter einer URL veröffentlicht sind, kann die URL von einem Client-Player geöffnet werden, der den codierten Inhalt wiedergeben kann.

 ![PublishedContent][publishedcontent]

## Gewusst wie: Wiedergeben von Inhalten im Portal

Das Verwaltungsportal bietet einen Media Services Content Player, den Sie verwenden können, um das Video zu testen.

Klicken Sie auf den gewünschten Videoinhalt, und klicken Sie auf die Schaltfläche **Play** am unteren Rand des Portals. 
 
Im Portal können nur Inhalte wiedergegeben werden, die veröffentlicht wurden. Außerdem muss die Codierung vom Browser unterstützt werden.


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png

<!--HONumber=42-->
