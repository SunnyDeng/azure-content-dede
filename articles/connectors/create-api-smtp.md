<properties
	pageTitle="Hinzufügen der SMTP-API zu Ihren Logik-Apps | Microsoft Azure"
	description="Übersicht über die SMTP-API und REST-API-Parameter"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/11/2016"
   ms.author="mandia"/>

# Erste Schritte mit der SMTP-API
Stellen Sie eine Verbindung mit einem SMTP-Server zum Senden von E-Mail her.

Die SMTP-API kann in Logik-Apps verwendet werden.

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [SMTP-Connector](../app-service-logic/app-service-logic-connector-smtp.md).

Mit SMTP können Sie folgende Aktionen ausführen:

- Erstellen Ihres Geschäftsworkflows einschließlich Senden von E-Mail per SMTP 
- Verwenden einer Aktion zum Senden von E-Mail Diese Aktion erhält eine Antwort und stellt anschließend die Ausgabe anderen Aktionen zur Verfügung. Befindet sich eine neue Datei auf dem FTP-Server, können Sie diese Datei z. B. als E-Mail-Anlage mithilfe von SMTP senden. 

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Die SMTP-API bietet die folgenden Aktionen. Es gibt keine Trigger.

|Trigger | Aktionen|
|--- | ---|
|Keine | Senden von E-Mail|

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit SMTP
Wenn Sie diese API Ihren Logik-Apps hinzufügen, geben Sie die folgenden Werte ein:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
| SMTP-Servername | Ja | Geben Sie den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse des SMTP-Servers ein.|
| Benutzername |Ja |Geben Sie den Benutzernamen für die Verbindung mit dem SMTP-Server ein. |
| Kennwort | Ja|Geben Sie das Kennwort für den Benutzernamen ein. |

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die SMTP-Eigenschaften, z. B. die Werte für Empfänger und CC, ein. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe SMTP-Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz

### E-Mail senden
Sendet eine E-Mail an einen oder mehrere Empfänger.  
```POST: /SendEmail```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|emailMessage| Viele|Ja|body|(Keine) |E-Mail-Nachricht|

## Objektdefinitionen

#### Email: SMTP email

| Name | Datentyp | Erforderlich|
|---|---|---|
|To|string|no|
|CC|string|no|
|Betreff|string|no|
|Body|string|no|
|Aus|string|no|
|IsHtml|Boolescher Wert|no|
|Bcc|string|no|
|Priorität|string|no|
|Anlagen|array|no|


#### Anhang: E-Mail-Anhänge

| Name | Datentyp |Erforderlich|
|---|---|---|
|FileName|string|no|
|ContentId|string|no|
|ContentData|string|Ja|
|ContentType|string|Ja|
|ContentTransferEncoding|string|Ja|


## Nächste Schritte
[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->
