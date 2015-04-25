<properties 
	pageTitle="Verwalten von Azure Media Services-Aufträgen" 
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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="juliako"/>

## Verwalten von Aufträgen

Ein **Auftrag** enthält Metadaten zur auszuführenden Verarbeitung. Jeder **Auftrag** enthält eine oder mehrere **Aufgaben**. Diese geben eine unteilbare Verarbeitungsaufgabe, die zugehörigen Eingabe- und Ausgabemedienobjekte, einen Medienprozessor und die zugehörigen Einstellungen an. Weitere Informationen zu Encoder-Einstellungen finden Sie in den Encoder-Handbüchern und -Schemata.

Ein Codierungsauftrag wird normalerweise mit anderen Verarbeitungsschritten kombiniert, z. B. mit der Paketerstellung oder Verschlüsselung von Medienobjekten oder mit dem Generieren von Medienobjekten durch den Encoder. Aufgaben innerhalb eines Auftrags können verkettet werden, wobei das Ausgabemedienobjekt als Eingabemedienobjekt für die nächste Aufgabe dient. Auf diese Weise kann ein Auftrag die gesamten Verarbeitungsschritte für eine Medienpräsentation umfassen.


>[AZURE.NOTE]Die Aufgabenanzahl ist derzeit auf 30 Aufgaben pro Auftrag begrenzt. Wenn Sie mehr als 30 Aufgaben verketten müssen, erstellen Sie mehrere Aufträge, um die Aufgaben zu verteilen.

## Arbeiten mit Aufträgen/Aufgaben

In diesem Abschnitt werden Links zu gängigen Aufgaben im Zusammenhang mit Aufträgen/Aufgaben bereitgestellt.

### Abrufen eines Medienprozessors

Rufen Sie einen Medienprozessor mit **.NET** oder **REST-API** ab.

[AZURE.INCLUDE [media-services-selector-get-media-processor](../includes/media-services-selector-get-media-processor.md)]

### Erstellen von Aufträgen 

Ein Auftrag ist eine Entität, die Metadaten zu einer Reihe von Aufgaben enthält (z. B. Codierung oder Indizierung). Jede Aufgabe führt einen unteilbaren Vorgang für das (oder die) Eingangsmedienobjekt(e) durch. Ein Beispiel zum Erstellen von Codierungsaufträgen finden Sie hier:

[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

### Indizieren

[AZURE.INCLUDE [media-services-selector-index-content](../includes/media-services-selector-index-content.md)]

### Codieren 

Codieren Sie mit dem **Azure Media Encoder** unter Verwendung von **Azure-Verwaltungsportal**, **.NET** oder **REST-API**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

### Überwachen des Auftragsfortschritts

Überwachen Sie den Auftragsfortschritt mit dem **Azure-Verwaltungsportal**, **.NET** oder **REST-API**.

[AZURE.INCLUDE [media-services-selector-job-progress](../includes/media-services-selector-job-progress.md)]

## Verwandte Links

[Kontingente und Einschränkungen](media-services-quotas-and-limitations.md) - beschreibt verwendete Kontingente und Einschränkungen für den Media Services Encoder

<!--HONumber=47-->
