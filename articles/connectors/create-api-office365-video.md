<properties
pageTitle="Hinzufügen der Office 365-Video-API zu Ihren Logik-Apps | Microsoft Azure"
description="Erste Schritte mit der Office 365-Video-API (Connector) in Ihren Microsoft Azure App Service-Logik-Apps"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/18/2016"
ms.author="deonhe"/>

# Erste Schritte mit der Office 365-Video-API

Die Office 365-Video-API stellt eine API zur Arbeit mit Office 365-Kanälen und -Videos bereit.

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Für die Schemaversion 2014-12-01-preview klicken Sie auf [Office 365-Videoconnector](../app-service-logic/app-service-logic-connector-Office365 Video Connector.md).

Mit der Office 365-Video-API können Sie folgende Aktionen ausführen:

* Erstellen von Logik-Apps

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Die Office 365-Video-API kann als Aktion verwendet werden. Es gibt keine Trigger. Alle APIs unterstützen Daten im JSON- und XML-Format.

 Die Office 365-Video-API verfügt über die folgenden Aktionen und/oder Trigger:

### Office 365-Video-API-Aktionen
Sie können die folgenden Aktionen ausführen:

|Aktion|Beschreibung|
|--- | ---|
|IsVideoPortalEnabled|Überprüft den Status des Videoportals, um festzustellen, ob Videodienste aktiviert sind|
|ListViewableChannels|Ruft alle Kanäle ab, auf die der Benutzer Zugriff hat|
|ListVideos|Listet alle in einem Kanal vorhanden Office 365-Videos auf|
|GetVideo|Ruft Informationen über ein bestimmtes Office 365-Video ab|
|GetPlaybackUrl|Ruft die Videowiedergabe-URL des Azure Media Services-Manifests für ein Video ab|
|GetStreamingKeyAccessToken|Ruft das Bearertoken für den Zugriff auf die Videoentschlüsselung ab|
ActionsTableReplaceMeLater## stellt eine Verbindung mit der Office 365-Video-API her. Zur Verwendung der Office 365-Video-API erstellen Sie zuerst eine **Verbindung** und geben dann die Details für die folgenden Eigenschaften an:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Tokenverschlüsselung|Ja|Bereitstellen von SharePoint Online-Anmeldeinformationen|


>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## Office 365-Video-REST-API – Referenz
#### Diese Dokumentation gilt für Version 1.0.


### Überprüft den Status des Videoportals, um festzustellen, ob Videodienste aktiviert sind
**```GET: /{tenant}/IsEnabled```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tenant|string|Ja|path|(Keine)|Der Name des Mandanten für das Verzeichnis, in dem sich der Benutzer befindet|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|404|Nicht gefunden|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft alle Kanäle ab, auf die der Benutzer Zugriff hat
**```GET: /{tenant}/Channels```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tenant|string|Ja|path|(Keine)|Der Name des Mandanten für das Verzeichnis, in dem sich der Benutzer befindet|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|404|Nicht gefunden|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Listet alle in einem Kanal vorhanden Office 365-Videos auf
**```GET: /{tenant}/Channels/{channelId}/Videos```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tenant|string|Ja|path|(Keine)|Der Name des Mandanten für das Verzeichnis, in dem sich der Benutzer befindet|
|channelId|string|Ja|path|(Keine)|Die Kanal-ID, mit der die Videos abgerufen werden müssen|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|404|Nicht gefunden|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft Informationen über ein bestimmtes Office 365-Video ab
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tenant|string|Ja|path|(Keine)|Der Name des Mandanten für das Verzeichnis, in dem sich der Benutzer befindet|
|channelId|string|Ja|path|(Keine)|Die Kanal-ID|
|videoId|string|Ja|path|(Keine)|Die Video-ID|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|404|Nicht gefunden|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft die Videowiedergabe-URL des Azure Media Services-Manifests für ein Video ab
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/playbackurl```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tenant|string|Ja|path|(Keine)|Der Name des Mandanten für das Verzeichnis, in dem sich der Benutzer befindet|
|channelId|string|Ja|path|(Keine)|Die Kanal-ID|
|videoId|string|Ja|path|(Keine)|Die Video-ID|
|streamingFormatType|string|Ja|query|(Keine)|Der Streaming-Formattyp. 1 – Smooth Streaming oder MPEG-DASH. 0 – HLS-Streaming|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|404|Nicht gefunden|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft das Bearertoken für den Zugriff auf die Videoentschlüsselung ab
**```GET: /{tenant}/Channels/{channelId}/Videos/{videoId}/token```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|tenant|string|Ja|path|(Keine)|Der Name des Mandanten für das Verzeichnis, in dem sich der Benutzer befindet|
|channelId|string|Ja|path|(Keine)|Die Kanal-ID|
|videoId|string|Ja|path|(Keine)|Die Video-ID|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|Vorgang war erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|404|Nicht gefunden|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



## Objektdefinitionen: 

 **Channel:** Channel-Klasse

Erforderliche Eigenschaften für Channel:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften:**


| Name | Datentyp |
|---|---|
|ID|string|
|Titel|string|
|Beschreibung|string|



 **Video:** Video-Klasse

Erforderliche Eigenschaften für Video:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften:**


| Name | Datentyp |
|---|---|
|ID|string|
|Titel|string|
|Beschreibung|string|
|CreationDate|string|
|Besitzer|string|
|ThumbnailUrl|string|
|VideoUrl|string|
|VideoDuration|integer|
|VideoProcessingStatus|integer|
|ViewCount|integer|


## Nächste Schritte
[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->