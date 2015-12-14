<properties 
	pageTitle="Arbeiten mit Azure Media Services-Aufträgen" 
	description="Das vorliegende Thema bietet einen Überblick über die Verwaltung von Azure Media Services-Aufträge." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Arbeiten mit Azure Media Services-Aufträgen

Ein **Auftrag** enthält Metadaten zur auszuführenden Verarbeitung. Jeder **Auftrag** enthält eine oder mehrere **Aufgaben**. Diese geben eine unteilbare Verarbeitungsaufgabe, die zugehörigen Eingabe- und Ausgabemedienobjekte, einen Medienprozessor und die zugehörigen Einstellungen an. Weitere Informationen zu Encoder-Einstellungen finden Sie in den Encoder-Handbüchern und -Schemata.

Ein Codierungsauftrag wird normalerweise mit anderen Verarbeitungsschritten kombiniert, z. B. mit der Paketerstellung oder Verschlüsselung von Medienobjekten oder mit dem Generieren von Medienobjekten durch den Encoder. Aufgaben innerhalb eines Auftrags können verkettet werden, wobei das Ausgabemedienobjekt als Eingabemedienobjekt für die nächste Aufgabe dient. Auf diese Weise kann ein Auftrag die gesamten Verarbeitungsschritte für eine Medienpräsentation umfassen.

In diesem Abschnitt werden Links zu gängigen Aufgaben im Zusammenhang mit Aufträgen/Aufgaben bereitgestellt.

>[AZURE.NOTE]Die Aufgabenanzahl ist derzeit auf 30 Aufgaben pro Auftrag begrenzt. Wenn Sie mehr als 30 Aufgaben verketten müssen, erstellen Sie mehrere Aufträge, um die Aufgaben zu verteilen.


##Abrufen eines Medienprozessors

Rufen Sie mithilfe von **.NET** oder **REST API** einen Medienprozessor ab.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../../includes/media-services-selector-get-media-processor.md)]

##Erstellen von Aufträgen

Ein Auftrag ist eine Entität, die Metadaten zu einer Reihe von Aufgaben enthält (z. B. Codierung oder Indizierung). Jede Aufgabe führt einen unteilbaren Vorgang für das (oder die) Eingangsmedienobjekt(e) durch. Ein Beispiel zum Erstellen von Codierungsaufträgen finden Sie hier:

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Indizieren

[AZURE.INCLUDE [media-services-selector-index-content](../../includes/media-services-selector-index-content.md)]

##Codieren

Führen Sie die Codierung mit **Azure Media Encoder** mithilfe des klassischen **Azure-Portals**, mithilfe von **.NET**, oder **REST-API** aus.

[AZURE.INCLUDE [media-services-selector-encode](../../includes/media-services-selector-encode.md)]

##Überwachen des Auftragsfortschritts

Überwachen Sie den Auftragsfortschritt mit dem klassischen **Azure-Portal**, **.NET** oder der **REST-API**.

[AZURE.INCLUDE [media-services-selector-job-progress](../../includes/media-services-selector-job-progress.md)]

##Verwandte Links

[Kontingente und Einschränkungen](media-services-quotas-and-limitations.md): Beschreibt verwendete Kontingente und Einschränkungen für den Media Services Encoder


##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1203_2015-->