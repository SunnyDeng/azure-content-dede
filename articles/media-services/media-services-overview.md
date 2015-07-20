<properties 
	pageTitle="Azure Media Services – Übersicht" 
	description="Dieses Thema bietet eine Übersicht über Azure Media Services." 
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
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Azure Media Services – Übersicht

Microsoft Azure Media Services ist eine erweiterbare, cloudbasierte Plattform, die Entwicklern das Erstellen von skalierbaren Medienverwaltungslösungen und Bereitstellungsanwendungen ermöglicht. Media Services basiert auf REST-APIs, mit denen Sie auf sichere Weise Video- oder Audioinhalte hochladen, speichern, codieren und paketieren können – sowohl für eine bedarfsgesteuerte als auch für eine Livestreaming-basierte Bereitstellung auf verschiedenen Clients (z. B. TV, PC und mobile Geräte).

Sie können mithilfe von Media Services vollständige End-to-End-Workflows erstellen. Es ist auch möglich, Drittanbieterkomponenten für einige Elemente Ihres Workflows zu nutzen. Beispielsweise können Sie die Codierung mit einem Encoder eines Drittanbieters durchführen. Anschließend sorgen Sie mit Media Services für Upload, Schutz, Paketierung und Bereitstellung.

Zum Entwickeln von Media Services-Lösungen können Sie folgende Komponenten verwenden:

- [Media Services-REST-API](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Eines der verfügbaren Client-SDKs: [Azure Media Services-SDK für .NET](https://github.com/Azure/azure-sdk-for-media-services), [Azure-SDK für Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services für Node.js](https://github.com/fritzy/node-azure-media), [Azure-PHP-SDK](https://github.com/Azure/azure-sdk-for-php)
- Vorhandene Tools: [Azure-Verwaltungsportal](http://manage.windowsazure.com/) oder [Azure Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


Das folgende Poster zeigt Azure Media Services-Workflows, von der Medienerstellung bis zur Nutzung. Sie können das Poster hier herunterladen: [Azure Media Services-Poster](http://www.microsoft.com/download/details.aspx?id=38195).

![Übersicht][overview]

**Vereinbarung zum Servicelevel (SLA)**:

- Media Services gewährleistet eine Verfügbarkeit von 99,9 % für die REST-API-Transaktionen zur Media Services-Codierung.
- Das Streaming sorgt für eine erfolgreiche Verarbeitung von Dienstanforderungen mit 99,9 % Verfügbarkeitsgarantie für vorhandene Medieninhalte, wenn mindestens eine Streamingeinheit erworben wird.
- Für Livekanäle wird garantiert, dass ausgeführte Kanäle mindestens 99,9 % der Zeit über externe Konnektivität verfügen.
- Für den Schutz von Inhalten garantieren wir, dass Schlüsselanforderungen mindestens 99,9 % der Zeit erfolgreich bearbeitet werden.
- Für Indexer werden Indexer-Aufgabenanforderungen, die mit einer reservierten Einheit für die Codierung verarbeitet werden, 99,9 % der Zeit erfolgreich bearbeitet.

	Weitere Informationen finden Sie im [Microsoft Azure-SLA](http://azure.microsoft.com/support/legal/sla/).

##Konzepte

Weitere Informationen finden Sie unter [Konzepte](media-services-concepts.md).


##Bereitstellen von Medien-on-Demand mit Azure Media Services

In diesem Thema werden die Schritte eines typischen Media Services-VoD-Workflows beschrieben. Das Thema enthält Links zu anderen Themen, die zeigen, wie Sie diese Schritte mithilfe von von Media Services unterstützten Technologien ausführen.

[Bereitstellen von Medien-on-Demand mit Azure Media Services](media-services-video-on-demand-workflow.md)

##Bereitstellen von Livestreaming mit Azure Media Services

In diesem Thema werden die Schritte eines typischen Media Services-Livestreaming-Workflows beschrieben. Das Thema enthält Links zu anderen Themen, die zeigen, wie Sie diese Schritte mithilfe von von Media Services unterstützten Technologien ausführen.

[Bereitstellen von Livestreaming mit Azure Media Services](media-services-live-streaming-workflow.md)

##Nutzung von Inhalten

Azure Media Services stellt die Tools zur Verfügung, die zum Erstellen leistungsstarker, dynamischer Clientplayeranwendungen für die gängigsten Plattformen erforderlich sind, darunter: iOS-Geräte, Android-Geräte, Windows, Windows Phone, Xbox und Set-Top-Boxen. Das Thema enthält Links zu SDKs und Player-Frameworks, mit denen Sie Ihre eigenen Clientanwendungen entwickeln können, die Streamingmedien aus Media Services verarbeiten.

[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)

##Muster & Leitfäden

[Muster & Leitfäden](https://wamsg.codeplex.com/) [Onlinedokumentation](https://msdn.microsoft.com/library/dn735912.aspx) [Herunterladbares E-Book](https://www.microsoft.com/download/details.aspx?id=42629)

##Support

Der [Azure-Support](http://azure.microsoft.com/support/options/) bietet Supportoptionen für Azure, Media Services eingeschlossen.

##Nächste Schritte

[Bereitstellen von Livestreaming mit Azure Media Services](media-services-live-streaming-workflow.md)

[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)
 
[Entwickeln von Videoplayeranwendungen](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=July15_HO2-->