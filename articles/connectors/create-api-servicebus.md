<properties
pageTitle="Verwenden der Azure Service Bus-API in Ihren Logik-Apps | Microsoft Azure"
description="Erste Schritte mit der Azure Service Bus-API (Connector) in Ihren Microsoft Azure App Service-Logik-Apps"
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

# Erste Schritte mit der Azure Service Bus-API

Stellen Sie eine Verbindung mit Azure Service Bus her, um Nachrichten zu senden und zu empfangen. Sie können Aktionen ausführen wie Senden an eine Warteschlange, Senden an ein Thema, Empfangen aus der Warteschlange, Empfangen aus Abonnements usw.

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [Azure Service Bus](../app-service-logic/app-service-logic-connector-azureservicebus.md).

Mit Azure Service Bus können Sie folgende Aktionen ausführen:

* Erstellen von Logik-Apps  

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Die Azure Service Bus-API kann als Aktion verwendet werden. Sie verfügt über Trigger. Alle APIs unterstützen Daten im JSON- und XML-Format.

 Die Azure Service Bus-API verfügt über die folgenden Aktionen und/oder Trigger:

### Azure Service Bus-Aktionen
Sie können die folgenden Aktionen ausführen:

|Aktion|Beschreibung|
|--- | ---|
|SendMessage|Sendet eine Nachricht an ein Azure Service Bus-Warteschlange oder -Thema.|
### Azure Service Bus-Trigger
Sie können auf diese Ereignisse lauschen:

|Trigger | Beschreibung|
|--- | ---|
|GetMessageFromQueue|Ruft eine neue Nachricht aus der Azure Service Bus-Warteschlange ab.|
|GetMessageFromTopic|Ruft eine neue Nachricht aus dem Azure Service Bus-Themenabonnement ab.|


## Erstellen einer Verbindung mit Azure Service Bus
Um die Azure Service Bus-API zu verwenden, stellen Sie zunächst eine **Verbindung** her und geben dann die Details für diese Eigenschaften an:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|ConnectionString|Ja|Angeben einer Azure Service Bus-Verbindungszeichenfolge|  

Führen Sie die folgenden Schritte zum Erstellen einer Service Bus-**Verbindung** aus, die Sie dann in Ihrer Logik-App verwenden können:

1. Wählen Sie **Wiederholung** aus.
2. Wählen Sie eine **Häufigkeit** aus, und geben Sie ein **Intervall** an. ![Service Bus konfigurieren][1] 
3. Wählen Sie **Aktion hinzufügen** aus. ![Service Bus konfigurieren][2]   
4. Geben Sie in das Suchfeld **Service Bus** ein, und warten Sie, bis die Suche alle Einträge mit „Service Bus“ im Namen zurückgibt.
5. Wählen Sie **Service Bus – Nachricht senden** aus. ![Service Bus konfigurieren][3]
7. Geben Sie einen **Verbindungsnamen** und eine **Verbindungszeichenfolge** ein, und wählen Sie dann **Verbindung erstellen** aus: ![Service Bus konfigurieren][4]
7. Nachdem die Verbindung erstellt wurde, wird das Dialogfeld **Nachricht senden** angezeigt. Geben Sie die erforderlichen Informationen zum Senden einer Nachricht ein. ![Service Bus konfigurieren][5]
8. Klicken Sie oben im Menü auf die Schaltfläche **Speichern**, um Ihre Arbeit zu speichern.    

>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## Azure Service Bus-REST-API-Referenz
#### Diese Dokumentation gilt für Version 1.0.


### Sendet eine Nachricht an ein Azure Service Bus-Warteschlange oder -Thema.
**```POST: /{entityName}/messages```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|message| |Ja|body|(Keine)|Service Bus-Nachricht|
|entityName|string|Ja|path|(Keine)|Name der Warteschlange oder des Themas|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft eine neue Nachricht aus der Azure Service Bus-Warteschlange ab.
**```GET: /{queueName}/messages/head```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|queueName|string|Ja|path|(Keine)|Name der Warteschlange.|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft eine neue Nachricht aus dem Azure Service Bus-Themenabonnement ab.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|topicName|string|Ja|path|(Keine)|Name des Themas.|
|subscriptionName|string|Ja|path|(Keine)|Name des Themenabonnements.|


### Im Folgenden sind die möglichen Antworten aufgeführt:

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



## Objektdefinitionen: 

 **ServiceBusMessage:** Nachricht besteht aus Inhalt und Eigenschaften

Erforderliche Eigenschaften für ServiceBusMessage:

ContentTransferEncoding

**Alle Eigenschaften:**


| Name | Datentyp |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|Eigenschaften|Objekt|


## Nächste Schritte
[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/create-api-servicebus/connectionconfig1.png
[2]: ./media/create-api-servicebus/connectionconfig2.png
[3]: ./media/create-api-servicebus/connectionconfig3.png
[4]: ./media/create-api-servicebus/connectionconfig4.png
[5]: ./media/create-api-servicebus/connectionconfig5.png
[6]: ./media/create-api-servicebus/connectionconfig6.png

<!---HONumber=AcomDC_0302_2016-->