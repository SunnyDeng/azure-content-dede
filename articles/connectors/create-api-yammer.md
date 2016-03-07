<properties
pageTitle="Hinzufügen der Yammer-API zu Ihren Logik-Apps | Microsoft Azure"
description="Übersicht über die Yammer-API und REST-API-Parameter"
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
ms.author="deonhe"/>

# Erste Schritte mit der Yammer-API

Verbinden Sie sich mit Yammer, um auf Konversationen in Ihrem Unternehmensnetzwerk zuzugreifen.

>[AZURE.NOTE] Diese Version des Artikels gilt für Logik-Apps mit der Schemaversion „2015-08-01-preview“. Um die Schemaversion „2014-12-01-preview“ aufzurufen, klicken Sie auf [Yammer](../app-service-logic/app-service-logic-connector-yammer.md).

Yammer ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Yammer abgerufen werden. 
- Verwenden von Triggern, wenn es eine neue Nachricht in einer Gruppe oder in einem Feed gibt, dem Sie folgen.
- Verwenden Sie Aktionen, um z. : eine Nachricht senden oder alle Nachrichten abzurufen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn z. B. eine neue Nachricht vorhanden ist, können Sie über Office 365 eine E-Mail senden.

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Yammer weist die folgenden Trigger und Aktionen auf.

Trigger | Aktionen
--- | ---
<ul><li>Wenn es in einer Gruppe eine neue Nachricht gibt</li><li>Wenn es in meinem Feed „Folgen“ eine neue Nachricht gibt</li></ul>| <ul><li>Alle Nachrichten abrufen</li><li>Nachrichten in einer Gruppe abrufen</li><li>Nachrichten aus meinem Feed „Folgen“ abrufen</li><li>Nachricht senden</li><li>Wenn es in einer Gruppe eine neue Nachricht gibt</li><li>Wenn es in meinem Feed „Folgen“ eine neue Nachricht gibt</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit Yammer
Um die Yammer-API zu verwenden, stellen Sie zunächst eine **Verbindung** her und geben dann die Details für diese Eigenschaften an:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Token|Ja|Angeben der Yammer-Anmeldeinformationen|


>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## Yammer-REST-API – Referenz
Diese Dokumentation gilt für Version 1.0.


### Alle öffentlichen Nachrichten im Yammer-Netzwerk des angemeldeten Benutzers abrufen
Entspricht „allen“ Konversationen auf der Yammer-Weboberfläche. ```GET: /messages.json```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|older\_than|integer|no|query|(Keine)|Gibt Nachrichten, die älter als die Nachrichten-ID sind, als numerische Zeichenfolge zurück. Dies ist nützlich zum Aufteilen von Nachrichten auf Seiten. Wenn Sie beispielsweise gerade 20 Nachrichten anzeigen und die älteste die Nummer 2912 hat, können Sie „?older\_than=2912“ an Ihre Anforderung anfügen, um die 20 Nachrichten abzurufen, die vor den aktuell angezeigten liegen.|
|newer\_than|integer|no|query|(Keine)|Gibt Nachrichten, die neuer als die Nachrichten-ID sind, als numerische Zeichenfolge zurück. Dies ist für Abfragen auf neue Nachrichten nützlich. Wenn Sie Nachrichten suchen und die neueste zurückgegebene Nachricht die Nummer 3516 hat, können Sie eine Anforderung mit dem Parameter „newer\_than=3516“ stellen. Dadurch stellen Sie sicher, dass Sie keine Duplikate von Nachrichten erhalten, die sich bereits auf der Seite befinden.|
|limit|integer|no|query|(Keine)|Gibt nur die angegebene Anzahl von Nachrichten zurück.|
|page|integer|no|query|(Keine)|Ruft die angegebene Seite ab. Wenn die zurückgegeben Daten größer als dieser Grenzwert sind, können Sie dieses Feld für den Zugriff auf nachfolgende Seiten nutzen.|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|400|Ungültige Anforderung|
|408|Anforderungstimeout|
|429|Zu viele Anforderungen|
|500|Interner Serverfehler. Unbekannter Fehler aufgetreten|
|503|Yammer-Dienst nicht verfügbar|
|504|Gatewaytimeout|
|die Standardeinstellung|Fehler beim Vorgang.|


### Eine Nachricht an eine Gruppe oder den Unternehmensfeed „Alle“ senden
Wenn eine Gruppen-ID angegeben ist, wird die Nachricht an die angegebene Gruppe, andernfalls an den Unternehmensfeed „Alle“ gesendet. ```POST: /messages.json```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|input| |Ja|body|(Keine)|Nachrichtenanforderung senden|


### Antwort

|Name|Beschreibung|
|---|---|
|201|Erstellt|



## Objektdefinitionen

#### Message: Yammer-Nachricht

| Name | Datentyp | Erforderlich |
|---|---| --- | 
|id|integer|no|
|content\_excerpt|string|no|
|sender\_id|integer|no|
|replied\_to\_id|integer|no|
|created\_at|string|no|
|network\_id|integer|no|
|message\_type|string|no|
|sender\_type|string|no|
|url|string|no|
|web\_url|string|no|
|group\_id|integer|no|
|body|nicht definiert|no|
|thread\_id|integer|no|
|direct\_message|Boolescher Wert|no|
|client\_type|string|no|
|client\_url|string|no|
|Sprache|string|no|
|notified\_user\_ids|array|no|
|privacy|string|no|
|liked\_by|nicht definiert|no|
|system\_message|Boolescher Wert|no|

#### PostOperationRequest: Stellt eine Sendeanforderung für den Yammer-Connector dar, die an Yammer gesendet werden soll.

| Name | Datentyp | Erforderlich |
|---|---| --- | 
|body|string|Ja|
|group\_id|integer|no|
|replied\_to\_id|integer|no|
|direct\_to\_id|integer|no|
|broadcast|Boolescher Wert|no|
|topic1|string|no|
|topic2|string|no|
|topic3|string|no|
|topic4|string|no|
|topic5|string|no|
|topic6|string|no|
|topic7|string|no|
|topic8|string|no|
|topic9|string|no|
|topic10|string|no|
|topic11|string|no|
|topic12|string|no|
|topic13|string|no|
|topic14|string|no|
|topic15|string|no|
|topic16|string|no|
|topic17|string|no|
|topic18|string|no|
|topic19|string|no|
|topic20|string|no|

#### MessageList: Liste der Nachrichten

| Name | Datentyp | Erforderlich |
|---|---| --- | 
|messages|array|no|


#### MessageBody: Nachrichtentext

| Name | Datentyp | Erforderlich |
|---|---| --- | 
|parsed|string|no|
|plain|string|no|
|rich|string|no|

#### LikedBy: Gefällt

| Name | Datentyp | Erforderlich |
|---|---| --- | 
|count|integer|no|
|names|array|no|

#### YammmerEntity: Gefällt

| Name | Datentyp | Erforderlich |
|---|---| --- | 
|type|string|no|
|id|integer|no|
|full\_name|string|no|


## Nächste Schritte
[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->