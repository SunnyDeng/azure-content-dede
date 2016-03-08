<properties
pageTitle="Hinzufügen der Office 365-API zu Ihren Logik-Apps | Microsoft Azure"
description="Übersicht über die Office 365-API und REST-API-Parameter"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Erste Schritte mit der Office 365 Outlook-API

Die Office 365-API ermöglicht die Interaktion mit Office 365. Sie können beispielsweise Kontakte und Kalendereinträge erstellen, bearbeiten und aktualisieren.

>[AZURE.NOTE] Diese Version des Artikels gilt für Logik-Apps mit der Schemaversion „2015-08-01-preview“. Um die Schemaversion „2014-12-01-preview“ aufzurufen, klicken Sie auf [Office 365-API](../app-service-logic/app-service-logic-connector-Office365.md).

Office 365 ermöglicht Folgendes:

* Erstellen von Logik-Apps
* Erstellen von Power-Apps

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Die Office 365-API kann als Aktion verwendet werden und verfügt über Trigger. Alle APIs unterstützen Daten im JSON- und XML-Format.

 Die Office 365-API verfügt über die folgenden Aktionen und/oder Trigger:

### Office 365-Aktionen
Sie können diese Aktionen ausführen:

|Aktion|Beschreibung|
|--- | ---|
|GetEmails|Ruft E-Mails aus einem Ordner ab|
|SendEmail|Sendet eine E-Mail-Nachricht|
|DeleteEmail|Löscht eine E-Mail anhand der ID|
|MarkAsRead|Markiert eine E-Mail-Nachricht als gelesen|
|ReplyTo|Antwortet auf eine E-Mail-Nachricht|
|GetAttachment|Ruft die Nachrichtenanlage anhand der ID ab|
|SendMailWithOptions|Sendet eine E-Mail mit mehreren Optionen und wartet, bis der Empfänger mit einer der Optionen antwortet.|
|SendApprovalMail|Sendet eine Genehmigungs-E-Mail und wartet auf eine Antwort des Empfängers.|
|CalendarGetTables|Ruft Kalender ab|
|CalendarGetItems|Ruft Elemente aus einem Kalender ab|
|CalendarPostItem|Erstellt ein neues Ereignis|
|CalendarGetItem|Ruft ein bestimmtes Element aus einem Kalender ab|
|CalendarDeleteItem|Löscht ein Kalenderelement|
|CalendarPatchItem|Aktualisiert ein Kalenderelement teilweise|
|ContactGetTables|Ruft Ordner des Typs „Kontakte“ ab|
|ContactGetItems|Ruft Kontakte aus dem Ordner „Kontakte“ ab|
|ContactPostItem|Erstellt einen neuen Kontakt|
|ContactGetItem|Ruft einen bestimmten Kontakt aus einem Ordner „Kontakte“ ab|
|ContactDeleteItem|Löscht einen Kontakt|
|ContactPatchItem|Aktualisiert einen Kontakt teilweise|
### Office 365-Trigger
Sie können auf diese Ereignisse lauschen:

|Trigger | Beschreibung|
|--- | ---|
|OnUpcomingEvents|Löst einen Datenfluss aus, wenn ein anstehendes Kalenderereignis gestartet wird|
|OnNewEmail|Löst einen Datenfluss aus, wenn eine neue E-Mail eingeht|
|CalendarGetOnNewItems|Wird ausgelöst, wenn ein neues Kalenderelement erstellt wird|
|CalendarGetOnUpdatedItems|Wird ausgelöst, wenn ein Kalenderelement geändert wird|


## Herstellen einer Verbindung mit Office 365
Um die Office 365-API zu verwenden, stellen Sie zunächst eine **Verbindung** her und geben dann die Details für diese Eigenschaften an:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Token|Ja|Office 365-Anmeldeinformationen angeben|


>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## Office 365-REST-API – Referenz
#### Diese Dokumentation gilt für Version 1.0.


### Bei Start eines anstehenden Ereignisses 


 Löst einen Datenfluss aus, wenn ein anstehendes Ereignis gestartet wird ```GET: /Events/OnUpcomingEvents```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|query|(Keine)|Eindeutiger Bezeichner des Kalenders|
|lookAheadTimeInMinutes|integer|no|query|15|Zeit (in Minuten) für Suche nach anstehenden Ereignissen.|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### E-Mails abrufen 


 Ruft E-Mails aus einem Ordner ab ```GET: /Mail```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Posteingang|Pfad des Ordners zum Abrufen von Nachrichten (Standard: Posteingang)|
|top|integer|no|query|10|Anzahl der abzurufenden E-Mails (Standardeinstellung: 10)|
|fetchOnlyUnread|Boolescher Wert|no|query|true|Nur ungelesene Nachrichten abrufen?|
|includeAttachments|Boolescher Wert|no|query|false|Falls auf „true“ festgelegt, werden Anlagen zusammen mit der E-Mail-Nachricht abgerufen.|
|searchQuery|string|no|query|(Keine)|Suchabfrage zum Filtern von E-Mails|
|skip|integer|no|query|0|Anzahl der zu überspringenden E-Mails (Standardeinstellung: 0)|
|skipToken|string|no|query|(Keine)|Token überspringen, um neue Seite abzurufen|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### E-Mail senden 


 Sendet eine E-Mail-Nachricht ```POST: /Mail```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|emailMessage| |Ja|body|(Keine)|E-Mail-Nachricht-Instanz|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### E-Mail löschen 


 Löscht eine E-Mail anhand der ID ```DELETE: /Mail/{messageId}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|messageId|string|Ja|path|(Keine)|ID der zu löschenden Nachricht|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Als gelesen markieren 


 Markiert eine E-Mail-Nachricht als gelesen ```POST: /Mail/MarkAsRead/{messageId}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|messageId|string|Ja|path|(Keine)|ID der Nachricht, die als gelesen markiert werden soll|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Nachricht beantworten 


 Antwortet auf eine E-Mail-Nachricht ```POST: /Mail/ReplyTo/{messageId}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|messageId|string|Ja|path|(Keine)|ID der zu beantwortenden Nachricht|
|comment|string|Ja|query|(Keine)|Antwortkommentar|
|replyAll|Boolescher Wert|no|query|false|Allen Empfängern antworten|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Anlagen abrufen 


 Ruft die Nachrichtenanlage anhand der ID ab ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|messageId|string|Ja|path|(Keine)|ID der Nachricht|
|attachmentId|string|Ja|path|(Keine)|ID der herunterzuladenden Anlage|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Bei neuer E-Mail 


 Löst einen Datenfluss aus, wenn eine neue E-Mail eingeht ```GET: /Mail/OnNewEmail```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|no|query|Posteingang|Abzurufender E-Mail-Ordner (Standard: Posteingang)|
|auf fest|string|no|query|(Keine)|E-Mail-Adressen der Empfänger|
|from|string|no|query|(Keine)|Absenderadresse|
|importance|string|no|query|Normal|Wichtigkeit der E-Mail (High, Normal, Low) (Standard: Normal)|
|fetchOnlyWithAttachment|Boolescher Wert|no|query|false|Nur E-Mail-Nachrichten mit einer Anlage abrufen|
|includeAttachments|Boolescher Wert|no|query|false|Anlagen einbeziehen|
|subjectFilter|string|no|query|(Keine)|Zeichenfolge, die in der Betreffzeile gesucht werden soll.|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|Vorgang erfolgreich|
|202|Zulässig|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### E-Mails mit Optionen senden 


 Sendet eine E-Mail mit mehreren Optionen und wartet, bis der Empfänger mit einer der Optionen antwortet. ```POST: /mailwithoptions/$subscriptions```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |Ja|body|(Keine)|Abonnementanforderung für E-Mail-Optionen|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|201|Abonnement erstellt|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Genehmigungs-E-Mail senden 


 Sendet eine Genehmigungs-E-Mail und wartet auf eine Antwort des Empfängers. ```POST: /approvalmail/$subscriptions```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |Ja|body|(Keine)|Abonnementanforderung für Genehmigungs-E-Mail.|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|201|Abonnement erstellt|
|400|BadRequest|
|401|Nicht autorisiert|
|403|Verboten (403)|
|500|Interner Serverfehler|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Kalender abrufen 


 Ruft Kalender ab ```GET: /datasets/calendars/tables```

Es gibt keine Parameter für diesen Aufruf
#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ereignisse abrufen 


 Ruft Elemente aus einem Kalender ab ```GET: /datasets/calendars/tables/{table}/items```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner des abzurufenden Kalenders|
|$skip|integer|no|query|(Keine)|Anzahl der zu überspringenden Einträge (Standardeinstellung = 0)|
|$top|integer|no|query|(Keine)|Maximale Anzahl abzurufender Einträge (Standardeinstellung = 256)|
|$filter|string|no|query|(Keine)|Eine ODATA-Filterabfrage zum Einschränken der Anzahl der Einträge|
|$orderby|string|no|query|(Keine)|Eine ODATA-orderBy-Abfrage zum Angeben der Reihenfolge von Einträgen|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ereignis erstellen 


 Erstellt ein neues Ereignis ```POST: /datasets/calendars/tables/{table}/items```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|item| |Ja|body|(Keine)|Zu erstellendes Kalenderelement|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ereignis abrufen 


 Ruft ein bestimmtes Element aus einem Kalender ab ```GET: /datasets/calendars/tables/{table}/items/{id}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines abzurufenden Kalenderelements|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ereignis löschen 


 Löscht ein Kalenderelement ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders.|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines zu löschenden Kalenderelements|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ereignis aktualisieren 


 Aktualisiert ein Kalenderelement teilweise ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines zu aktualisierenden Kalenderelements|
|item| |Ja|body|(Keine)|Zu aktualisierendes Kalenderelement|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Bei neuen Elementen 


 Wird ausgelöst, wenn ein neues Kalenderelement erstellt wird ```GET: /datasets/calendars/tables/{table}/onnewitems```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|$skip|integer|no|query|(Keine)|Anzahl der zu überspringenden Einträge (Standardeinstellung = 0)|
|$top|integer|no|query|(Keine)|Maximale Anzahl abzurufender Einträge (Standardeinstellung = 256)|
|$filter|string|no|query|(Keine)|Eine ODATA-Filterabfrage zum Einschränken der Anzahl der Einträge|
|$orderby|string|no|query|(Keine)|Eine ODATA-orderBy-Abfrage zum Angeben der Reihenfolge von Einträgen|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Bei aktualisierten Elementen 


 Wird ausgelöst, wenn ein Kalenderelement geändert wird ```GET: /datasets/calendars/tables/{table}/onupdateditems```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|$skip|integer|no|query|(Keine)|Anzahl der zu überspringenden Einträge (Standardeinstellung = 0)|
|$top|integer|no|query|(Keine)|Maximale Anzahl abzurufender Einträge (Standardeinstellung = 256)|
|$filter|string|no|query|(Keine)|Eine ODATA-Filterabfrage zum Einschränken der Anzahl der Einträge|
|$orderby|string|no|query|(Keine)|Eine ODATA-orderBy-Abfrage zum Angeben der Reihenfolge von Einträgen|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ordner „Kontakte“ abrufen 


 Ruft Ordner des Typs „Kontakte“ ab ```GET: /datasets/contacts/tables```

Es gibt keine Parameter für diesen Aufruf
#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Kontakte abrufen 


 Ruft Kontakte aus einem Ordner des Typs „Kontakte“ ab ```GET: /datasets/contacts/tables/{table}/items```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner des abzurufenden Ordners „Kontakte“|
|$skip|integer|no|query|(Keine)|Anzahl der zu überspringenden Einträge (Standardeinstellung = 0)|
|$top|integer|no|query|(Keine)|Maximale Anzahl abzurufender Einträge (Standardeinstellung = 256)|
|$filter|string|no|query|(Keine)|Eine ODATA-Filterabfrage zum Einschränken der Anzahl der Einträge|
|$orderby|string|no|query|(Keine)|Eine ODATA-orderBy-Abfrage zum Angeben der Reihenfolge von Einträgen|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Kontakt erstellen 


 Erstellt einen neuen Kontakt ```POST: /datasets/contacts/tables/{table}/items```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“|
|item| |Ja|body|(Keine)|Zu erstellender Kontakt|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Kontakt abrufen 


 Ruft einen bestimmten Kontakt aus einem Ordner „Kontakte“ ab ```GET: /datasets/contacts/tables/{table}/items/{id}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner des abzurufenden Kontakts|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Kontakt löschen 


 Löscht einen Kontakt ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“.|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner des zu löschenden Kontakts|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Kontakt aktualisieren 


 Aktualisiert einen Kontakt teilweise ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner des zu aktualisierenden Kontakts|
|item| |Ja|body|(Keine)|Zu aktualisierendes Kontaktelement|


#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



## Objektdefinitionen: 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

Erforderliche Eigenschaften für TriggerBatchResponse[IDictionary[String,Object]]:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|value|array|



 **SendMessage**: E-Mail-Nachricht senden

Erforderliche Eigenschaften für SendMessage:

Subject, Body, To

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Anlagen|array|
|Aus|string|
|Cc|string|
|Bcc|string|
|Betreff|string|
|Body|string|
|Priorität|string|
|IsHtml|Boolescher Wert|
|To  
|string|



 **SendAttachment**: Anlage

Erforderliche Eigenschaften für SendAttachment:

Name, ContentBytes

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|@odata.type|string|
|Name|string|
|ContentBytes|string|



 **ReceiveMessage**: E-Mail-Nachricht empfangen

Erforderliche Eigenschaften für ReceiveMessage:

Subject, Body, To

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|ID|string|
|IsRead|Boolescher Wert|
|HasAttachment|Boolescher Wert|
|DateTimeReceived|string|
|Anlagen|array|
|Aus|string|
|Cc|string|
|Bcc|string|
|Betreff|string|
|Body|string|
|Priorität|string|
|IsHtml|Boolescher Wert|
|To  
|string|



 **ReceiveAttachment**: Anlage empfangen

Erforderliche Eigenschaften für ReceiveAttachment:

Id, ContentType, Name, ContentBytes

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|ID|string|
|ContentType|string|
|@odata.type|string|
|Name|string|
|ContentBytes|string|



 **DigestMessage**: E-Mail-Nachricht senden

Erforderliche Eigenschaften für DigestMessage:

Subject, Digest, To

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Betreff|string|
|Body|string|
|Priorität|string|
|Digest|array|
|Anlagen|array|
|To  
|string|



 **TriggerBatchResponse [ReceiveMessage]**:

Erforderliche Eigenschaften für TriggerBatchResponse [ReceiveMessage]:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|value|array|



 **DataSetsMetadata**:

Erforderliche Eigenschaften für DataSetsMetadata:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|tabular|nicht definiert|
|Blob|nicht definiert|



 **TabularDataSetsMetadata**:

Erforderliche Eigenschaften für TabularDataSetsMetadata:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Quelle|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Erforderliche Eigenschaften für BlobDataSetsMetadata:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Quelle|string|
|displayName|string|
|urlEncoding|string|



 **TableMetadata**:

Erforderliche Eigenschaften für TableMetadata:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|name|string|
|title|string|
|x-ms-permission|string|
|schema|nicht definiert|



 **OptionsEmailSubscription**: Modell für Optionen für E-Mail-Abonnement

Erforderliche Eigenschaften für OptionsEmailSubscription:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|NotificationUrl|string|
|Message|nicht definiert|



 **MessageWithOptions**: E-Mail-Nachricht mit Benutzeroptionen Dies ist die Nachricht, die als Teil der Benutzereingabe erwartet wird

Erforderliche Eigenschaften für MessageWithOptions:

Subject, Options, To

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Betreff|string|
|Optionen|string|
|Body|string|
|Priorität|string|
|Anlagen|array|
|To  
|string|



 **SubscriptionResponse**: Modell für das Genehmigungs-E-Mail-Abonnement

Erforderliche Eigenschaften für SubscriptionResponse:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|id|string|
|resource|string|
|notificationType|string|
|notificationUrl|string|



 **ApprovalEmailSubscription**: Modell für das Genehmigungs-E-Mail-Abonnement

Erforderliche Eigenschaften für ApprovalEmailSubscription:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|NotificationUrl|string|
|Message|nicht definiert|



 **ApprovalMessage**: Genehmigungs-E-Mail-Nachricht. Dies ist die Nachricht, die als Teil der Benutzereingabe erwartet wird

Erforderliche Eigenschaften für ApprovalMessage:

Subject, Options, To

**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Betreff|string|
|Optionen|string|
|Body|string|
|Priorität|string|
|Anlagen|array|
|To  
|string|



 **ApprovalEmailResponse**: Genehmigungs-E-Mail-Antwort

Erforderliche Eigenschaften für ApprovalEmailResponse:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|SelectedOption|string|



 **TablesList**:

Erforderliche Eigenschaften für TablesList:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|value|array|



 **Table**:

Erforderliche Eigenschaften für „Table“:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Name|string|
|DisplayName|string|



 **Item**:

Erforderliche Eigenschaften für „Item“:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|ItemInternalId|string|



 **CalendarItemsList**: Die Liste der Kalenderelemente

Erforderliche Eigenschaften für CalendarItemsList:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|value|array|



 **CalendarItem**: Stellt ein Kalendertabellenelement dar

Erforderliche Eigenschaften für CalendarItem:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|ItemInternalId|string|



 **ContactItemsList**: Die Liste der Kontaktelemente

Erforderliche Eigenschaften für ContactItemsList:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|value|array|



 **ContactItem**: Stellt ein Element der Kontakttabellenelement dar

Erforderliche Eigenschaften für ContactItem:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|ItemInternalId|string|



 **DataSetsList**:

Erforderliche Eigenschaften für DataSetsList:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|value|array|



 **DataSet**:

Erforderliche Eigenschaften für DataSet:


Keine der Eigenschaften ist erforderlich.


**Alle Eigenschaften**:


| Name | Datentyp |
|---|---|
|Name|string|
|DisplayName|string|


## Nächste Schritte
Nach Hinzufügen der Office 365-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->