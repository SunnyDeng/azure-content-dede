<properties
pageTitle="Hinzufügen der Twilio-API zu Ihren Logik-Apps | Microsoft Azure"
description="Übersicht über die Twilio-API und REST-API-Parameter"
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
ms.date="02/23/2016"
ms.author="mandia"/>

# Erste Schritte mit der Twilio-API

Stellen Sie eine Verbindung mit Twilio her, um global SMS, MMS und IP-Nachrichten zu senden und zu empfangen.

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [Twilio](../app-service-logic/app-service-logic-connector-twilio.md).

Mit Twilio können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Twilio abgerufen werden. 
- Verwenden von Aktionen zum Abrufen einer Nachricht, von Listennachrichten und mehr. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn Sie eine neue Twilio-Nachricht erhalten, können Sie diese Nachricht z. B. annehmen und als Service Bus-Workflow verwenden. 

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Die Twilio-API bietet die folgenden Aktionen. Es gibt keine Trigger.

| Trigger | Aktionen|
| --- | --- |
|Keine| <ul><li>Nachricht abrufen</li><li>Nachrichten auflisten</li><li>Nachricht senden</li></ul>|

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit Twilio
Wenn Sie diese API Ihren Logik-Apps hinzufügen, geben Sie die folgenden Twilio-Werte ein:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Konto-ID|Ja|Geben Sie Ihre Twilio-Konto-ID ein.|
|Access Token|Ja|Geben Sie Ihr Twilio-Zugriffstoken ein.|

Informationen zum Erstellen eines Zugriffstokens finden Sie unter [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity).

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Twilio-Eigenschaften ein. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Twilio-Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
#### Diese Dokumentation gilt für Version 1.0.

### Get Message
Gibt eine einzelne Nachricht zurück, die in der bereitgestellten Nachrichten-ID angegeben ist. ```GET: /Messages/{MessageId}.json```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|MessageId|string|Ja|path|(Keine)|Nachrichten-ID|

### Antwort
|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|Ungültige Anforderung|
|404|Nachricht nicht gefunden|
|500|Interner Serverfehler. Unbekannter Fehler ist aufgetreten|
|die Standardeinstellung|Fehler beim Vorgang.|


### List Messages
Gibt eine Liste von Nachrichten zurück, die Ihrem Konto zugeordnet sind. ```GET: /Messages.json```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|To  
|string|no|query|(Keine)|Angerufene Telefonnummer|
|Aus|string|no|query|(Keine)|Anrufertelefonnummer|
|DateSent|string|no|query|(Keine)|Nur an diesem Datum (GMT) gesendete Nachrichten anzeigen, Datum im Format JJJJ-MM-TT. Beispiel: DateSent=2009-07-06. Sie können auch Ungleichheit angeben, d. h. DateSent<=JJJJ-MM-TT für Nachrichten, die an oder vor Mitternacht an einem bestimmten Datum gesendet wurden, sowie DateSent>=JJJJ-MM-TT für Nachrichten, die an oder nach Mitternacht an einem bestimmten Datum gesendet wurden.|
|PageSize|integer|no|query|50|Die Anzahl der Ressourcen, die auf jeder Listenseite zurückgegeben werden. Standard ist 50.|
|Seite|integer|no|query|0|Seitenzahl. Der Standardwert ist 0.|

### Antwort
|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|Ungültige Anforderung|
|500|Interner Serverfehler. Unbekannter Fehler ist aufgetreten|
|die Standardeinstellung|Fehler beim Vorgang.|



### Nachricht senden
Sendet eine neue Nachricht an eine Mobiltelefonnummer. ```POST: /Messages.json```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|sendMessageRequest| |Ja|body|(Keine)|Zu sendende Nachricht|

### Antwort
|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|Ungültige Anforderung|
|500|Interner Serverfehler. Unbekannter Fehler ist aufgetreten|
|die Standardeinstellung|Fehler beim Vorgang.|


## Objektdefinitionen

#### SendMessageRequest: Anforderungsmodell zum Senden von Nachrichten

|Eigenschaftenname | Datentyp | Erforderlich|
|---|---|---|
|from|string|Ja|
|auf fest|string|Ja|
|body|string|Ja|
|media\_url|array|no|
|status\_callback|string|no|
|messaging\_service\_sid|string|no|
|application\_sid|string|no|
|max\_price|string|no|


#### Message: Nachrichtenmodell

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|body|string|no|
|from|string|no|
|auf fest|string|no|
|status|string|no|
|sid|string|no|
|account\_sid|string|no|
|api\_version|string|no|
|num\_segments|string|no|
|num\_media|string|no|
|date\_created|string|no|
|date\_sent|string|no|
|date\_updated|string|no|
|direction|string|no|
|error\_code|string|no|
|error\_message|string|no|
|price|string|no|
|price\_unit|string|no|
|uri|string|no|
|subresource\_uris|array|no|
|messaging\_service\_sid|string|no|

#### MessageList: Antwortmodell zum Auflisten von Nachrichten

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|messages|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|uri|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|
|total|integer|no|
|previous\_page\_uri|string|no|

#### IncomingPhoneNumberList: Antwortmodell zum Auflisten von Nachrichten

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|incoming\_phone\_numbers|array|no|
|page|integer|no|
|page\_size|integer|no|
|num\_pages|integer|no|
|uri|string|no|
|first\_page\_uri|string|no|
|next\_page\_uri|string|no|


#### AddIncomingPhoneNumberRequest: Anforderungsmodell zum Hinzufügen eingehender Telefonnummern

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|PhoneNumber|string|Ja|
|AreaCode|string|no|
|FriendlyName|string|no|


#### IncomingPhoneNumber: Eingehende Telefonnummer

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|sid|string|no|
|account\_sid|string|no|
|date\_created|string|no|
|date\_updated|string|no|
|capabilities|nicht definiert|no|
|status\_callback|string|no|
|status\_callback\_method|string|no|
|api\_version|string|no|


#### Capabilities: Telefonnummernfunktionen

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|mms|Boolescher Wert|no|
|sms|Boolescher Wert|no|
|voice|Boolescher Wert|no|

#### AvailablePhoneNumbers: Verfügbare Telefonnummern

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|phone\_number|string|no|
|friendly\_name|string|no|
|lata|string|no|
|latitude|string|no|
|longitude|string|no|
|postal\_code|string|no|
|rate\_center|string|no|
|region|string|no|
|MMS|Boolescher Wert|no|
|SMS|Boolescher Wert|no|
|voice|Boolescher Wert|no|


#### UsageRecords: Klasse für Nutzungseinträge

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|category|string|no|
|Nutzung|string|no|
|usage\_unit|string|no|
|description|string|no|
|price|number|no|
|price\_unit|string|no|
|count|string|no|
|count\_unit|string|no|
|start\_date|string|no|
|end\_date|string|no|


## Nächste Schritte
[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->