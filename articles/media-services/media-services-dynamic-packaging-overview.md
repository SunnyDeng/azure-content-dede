<properties 
	pageTitle="Dynamische Paketerstellung – Übersicht" 
	description="In diesem Thema finden Sie eine Übersicht über die dynamische Paketerstellung." 
	authors="Juliako" 
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
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#Dynamische Paketerstellung 

##Übersicht

Microsoft Azure Media Services kann verwendet werden, um zahlreiche Medien-Quelldateiformate, Medienstreamingformate und Inhaltsschutzformate in verschiedenen Clienttechnologien wie iOS, XBOX, Silverlight oder Windows 8 bereitzustellen. Von den Clients werden verschiedene Protokolle verarbeitet. So ist für iOS ein HLS-V4-Format (HTTP Live Streaming) erforderlich, für Silverlight und Xbox dagegen Smooth Streaming. Wenn Sie über einen Satz MP4-Dateien (ISO Base Media 14496-12) mit adaptiver Bitrate (Multi-Bitrate) oder einen Satz von Smooth Streaming-Dateien mit adaptiver Bitrate verfügen, die Sie Clients bereitstellen möchten, von denen MPEG DASH, HLS oder Smooth Streaming verarbeitet wird, sollten Sie die Vorteile der dynamischen Paketerstellung von Media Services nutzen.

Bei der dynamischen Paketerstellung müssen Sie lediglich ein Medienobjekt erstellen, das einen Satz von MP4- oder Smooth Streaming-Dateien mit adaptiver Bitrate enthält. Dann wird durch den bedarfsgesteuerten Streamingserver auf Basis des in der Manifest- oder Fragmentanforderung angegebenen Formats sichergestellt, dass Sie den Datenstrom im ausgewählten Protokoll erhalten. So müssen Sie die Dateien nur in einem Speicherformat speichern und bezahlen. Die entsprechende Antwort wird von Media Services basierend auf Clientanforderungen erstellt und verfügbar gemacht.

Im folgenden Diagramm wird der herkömmliche Workflow zur Codierung und statischen Paketerstellung dargestellt.

![Statische Codierung](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Im folgenden Diagramm wird der Workflow zur dynamischen Paketerstellung dargestellt.

![Dynamische Codierung](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


>[AZURE.NOTE]Um dynamische Paketerstellung nutzen zu können, ist mindestens eine bedarfsgesteuerte Streamingeinheit für den Streamingendpunkt erforderlich, aus dem die Inhalte bereitgestellt werden sollen. Weitere Informationen finden Sie unter [How to Scale Media Services](media-services-manage-origins.md#scale_streaming_endpoints) (Skalieren von Media Services).

##Allgemeines Szenario

1. Laden Sie eine Eingabedatei (auch Mezzaninedatei genannt) hoch. Beispielformate: H.264, MP4 oder WMV (eine Liste unterstützter Formate finden Sie unter „Von Media Services Encoder unterstützte Formate“).
 
1. Codieren Sie Ihre Mezzaninedatei zu H.264-MP4-Sätzen mit adaptiver Bitrate.
 
1. Veröffentlichen Sie das Medienobjekt, das den MP4-Satz mit adaptiver Bitrate enthält, indem Sie den On-Demand-Locator erstellen.
 
1. Erstellen Sie die Streaming-URLs zum Zugreifen und Streamen Ihrer Inhalte.
 
>[AZURE.NOTE]Es werden nicht alle MP4-Dateiformate von der dynamischen Paketerstellung unterstützt. Weitere Informationen finden Sie unter [Formats that are not supported by dynamic packaging](media-services-dynamic-packaging-overview.md#unsupported_formats) (Formate, die nicht von der dynamischen Paketerstellung unterstützt werden).

##Vorbereiten von Medienobjekten auf dynamisches Streaming

Zum Vorbereiten Ihres Medienobjekts auf dynamisches Streaming haben Sie zwei Möglichkeiten:

- Laden Sie eine Masterdatei hoch, und generieren Sie mithilfe von Azure Media Encoder H.264 MP4-Sätze mit adaptiver Bitrate.
- Laden Sie vorhandene Sätze mit adaptiver Bitrate hoch, und überprüfen Sie sie mithilfe von Media Packager.

###Laden Sie eine Masterdatei hoch, und generieren Sie mithilfe von Azure Media Encoder H.264 MP4-Sätze mit adaptiver Bitrate.

Informationen zum Hochladen und Codieren von Medienobjekten finden Sie in den folgenden Artikeln:


Laden Sie Ihre Dateien mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET** oder **REST-API** hoch.

[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

Führen Sie die Codierung mit **Azure Media Encoder** mithilfe des **Azure-Verwaltungsportals**, mithilfe von **.NET**, oder **REST-API** aus.
 
[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]


###Laden Sie vorhandene Sätze mit adaptiver Bitrate hoch, und überprüfen Sie sie mithilfe von Media Packager.

Diese Aufgabe führen Sie in der Regel dann aus, wenn Sie einen Satz von MP4-Dateien mit adaptiver Bitrate hochladen, die nicht mit Media Services Encoder codiert wurden. Im Thema [Überprüfen von MP4-Dateien mit adaptiver Bitrate, die mit externen Encodern codiert wurden](https://msdn.microsoft.com/library/azure/dn750842.aspx) wird erläutert, wie diese Aufgaben ausgeführt werden.

##Streamen Ihre Inhalte an Clients

Wenn die Datensätze mit adaptiver Bitrate vorhanden sind, können Sie Ihr Medienobjekt veröffentlichen, indem Sie einen On-Demand-Locator und die Streaming-URLs für Smooth Streaming, MPEG DASH, HLS und HDS (nur mit Adobe PrimeTime/Access-Lizenz) erstellen.

Informationen zum Erstellen von Locators und zum Verwenden der dynamischen Paketerstellung zum Streamen von Inhalten finden Sie unter den folgenden Themen:

[Bereitstellen von Inhalten für Kunden – Übersicht](media-services-deliver-content-overview.md)

Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mithilfe von **.NET** oder **REST-API**

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

Veröffentlichen von Medienobjekten (durch Locator-Erstellung) mithilfe des **Azure-Verwaltungsportals** oder mithilfe von **.NET**.

[AZURE.INCLUDE [media-services-selector-publish](../../includes/media-services-selector-publish.md)]


##<a id="unsupported_formats"></a>Formate, die nicht von der dynamischen Paketerstellung unterstützt werden

Die folgenden Quelldateiformate werden von der dynamischen Paketerstellung nicht unterstützt:

- Dolby Digital mit MP4-Dateien
- Dolby Digital mit Smooth-Dateien 

<!---HONumber=July15_HO2-->