<properties 
	pageTitle="Übermitteln von Widevine-Lizenzen an Azure Media Services mithilfe von Partnern" 
	description="In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Media Services (AMS) einen Stream übermitteln, der von AMS mit PlayReady- und Widevine-DRMs dynamisch verschlüsselt wird. Die PlayReady-Lizenz stammt vom Media Services PlayReady-Lizenzserver, und die Widevine-Lizenz wird vom castLabs-Lizenzserver übermittelt." 
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
	ms.date="10/07/2015"  
	ms.author="juliako"/>

#Übermitteln von Widevine-Lizenzen an Azure Media Services mithilfe von Partnern

##Übersicht

Mit Microsoft Azure Media Services können Sie mit Widevine DRM geschützte MPEG-DASH-Inhalte übermitteln. Widevine DRM wird gemäß der Common Encryption (CENC)-Spezifikation verschlüsselt.

>[AZURE.NOTE]Media Services bietet derzeit keinen Widevine-Lizenzserver. Sie können sich von folgenden AMS-Partnern bei der Übermittlung von Widevine-Lizenzen unterstützen lassen: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/).

##castLabs

Sie können Widevine-Lizenzen mithilfe von [castLabs](http://castlabs.com/company/partners/azure/) übermitteln. Weitere Informationen finden Sie unter [Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von castLabs](media-services-castlabs-integration.md).

##Axinom

Sie können Widevine-Lizenzen mithilfe von [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/) übermitteln. Weitere Informationen finden Sie unter [Übermitteln von DRM-Lizenzen an Azure Media Services mithilfe von Axinom](media-services-axinom-integration.md).


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Weitere Informationen

[Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-drm.md).

[Blog von Mingfei](https://azure.microsoft.com/de-DE/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)

<!---HONumber=Nov15_HO3-->