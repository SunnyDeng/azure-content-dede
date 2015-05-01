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
	ms.date="03/10/2015" 
	ms.author="juliako"/>

#Arbeiten mit Azure Media Services-Aufträgen

Ein **Auftrag** enthält Metadaten zur auszuführenden Verarbeitung. Jeder **Auftrag** enthält eine oder mehrere **Aufgaben**. Diese geben eine unteilbare Verarbeitungsaufgabe, die zugehörigen Eingabe- und Ausgabemedienobjekte, einen Medienprozessor und die zugehörigen Einstellungen an. Weitere Informationen zu Encoder-Einstellungen finden Sie in den Encoder-Handbüchern und -Schemata.

Ein Codierungsauftrag wird normalerweise mit anderen Verarbeitungsschritten kombiniert, z. B. mit der Paketerstellung oder Verschlüsselung von Medienobjekten oder mit dem Generieren von Medienobjekten durch den Encoder. Aufgaben innerhalb eines Auftrags können verkettet werden, wobei das Ausgabemedienobjekt als Eingabemedienobjekt für die nächste Aufgabe dient. Auf diese Weise kann ein Auftrag die gesamten Verarbeitungsschritte für eine Medienpräsentation umfassen.

In diesem Abschnitt werden Links zu gängigen Aufgaben im Zusammenhang mit Aufträgen/Aufgaben bereitgestellt.

>[AZURE.NOTE]Die Aufgabenanzahl ist derzeit auf 30 Aufgaben pro Auftrag begrenzt. Wenn Sie mehr als 30 Aufgaben verketten müssen, erstellen Sie mehrere Aufträge, um die Aufgaben zu verteilen.


##Abrufen eines Medienprozessors

Rufen Sie mithilfe von **.NET** oder **REST-API** einen Medienprozessor ab.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

##Erstellen von Aufträgen 

Ein Auftrag ist eine Entität, die Metadaten zu einer Reihe von Aufgaben enthält (z. B. Codierung oder Indizierung). Jede Aufgabe führt einen unteilbaren Vorgang für das (oder die) Eingangsmedienobjekt(e) durch. Ein Beispiel zum Erstellen von Codierungsaufträgen finden Sie hier:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

##Indizieren

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

##Codieren 

Codieren Sie mit dem **Azure Media Encoder** unter Verwendung von **Azure-Verwaltungsportal**, **.NET** oder **REST-API**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

##Überwachen des Auftragsfortschritts

Überwachen Sie den Auftragsfortschritt mithilfe von **Azure-Verwaltungsportal**, **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

##Verwandte Links

[Kontingente und Einschränkungen](Media-Services-Kontingente-und-limitations.md) - beschreibt verwendete Kontingente und Einschränkungen von Media Services-Encoder


<!--HONumber=52-->