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
ms.date="02/25/2016"
ms.author="mandia"/>

# Erste Schritte mit der Office 365 Outlook-API 

Stellen Sie eine Verbindung mit Office 365 Outlook her, um E-Mail zu erhalten, auf eine E-Mail zu antworten, Ihren Kalender und Kontakte zu aktualisieren und vieles mehr. Die Office 365 Outlook-APIs kann verwendet werden von:

- PowerApps 
- Logik-Apps 

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion „2014-12-01-preview“ aufzurufen, klicken Sie auf [Office 365-API](../app-service-logic/app-service-logic-connector-office365.md).

Mit Office 365 Outlook können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Office 365 Outlook abgerufen werden. 
- Verwenden eines Triggers, wenn neue E-Mails eingegangen sind, wenn Sie einen neuen Kontakt erstellen usw.
- Verwenden von Aktionen, die auf eine E-Mail antworten, ein neues Kalenderereignis erstellen usw. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn beispielsweise ein neues Objekt in Salesforce vorhanden ist, können Sie dieses Objekt abrufen und Ihre Office 365 Outlook-Kontakte aktualisieren. 
- Hinzufügen der Office 365-Outlook-API zu PowerApps Enterprise. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen

Die Office 365 Outlook-API verfügt über folgende Trigger und Aktionen:

| Trigger | Aktionen|
| --- | --- |
|<ul><li>Bei Start eines anstehenden Ereignisses</li><li>Bei neuer E-Mail</li><li>Bei neuen Elementen</li><li>Bei aktualisierten Elementen</li></ul>| <ul><li>Kontakt erstellen</li><li>Ereignis erstellen</li><li>Genehmigungs-E-Mail senden</li><li>E-Mail senden</li><li>Kontakt löschen</li><li>E-Mail löschen</li><li>Ereignis löschen</li><li>Anlage abrufen</li><li>Kalender abrufen</li><li>Kontakt abrufen</li><li>Kontaktordner abrufen</li><li>Kontakte abrufen</li><li>E-Mails abrufen</li><li>Ereignis abrufen</li><li>Ereignisse abrufen</li><li>Als gelesen markieren</li><li>Bei Start eines anstehenden Ereignisses</li><li>Bei neuer E-Mail</li><li>Bei neuen Elementen</li><li>Bei aktualisierten Elementen</li><li>Auf eine Nachricht antworten</li><li>E-Mail mit Optionen senden</li><li>Kontakt aktualisieren</li><li>Ereignis aktualisieren</li></ul> |

Alle APIs unterstützen Daten im JSON- und XML-Format.


## Herstellen einer Verbindung mit Office 365

### Hinzufügen zusätzlicher Konfigurationen in PowerApps
Wenn Sie diese API zu PowerApps Enterprise hinzufügen, geben Sie die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** Ihrer Office 365-AAD-Anwendung (Azure Active Directory) ein. Der Wert von **Umleitungs-URL** wird auch in Ihrer Office 365-Anwendung verwendet. Wenn Sie noch keine Office 365-Anwendung haben, führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Öffnen Sie das [Azure-Portal][5], dann **Active Directory**, und wählen Sie den Namen des Mandanten Ihrer Organisation aus.
2. Klicken Sie auf die Registerkarte **Anwendungen**, und wählen Sie **Hinzufügen** aus: ![AAD-Mandanten-Anwendungen][7]

3. Auf der Seite **Anwendung hinzufügen**:

	1. Geben Sie einen **Namen** für Ihre Anwendung ein.  
	2. Lassen Sie als Anwendungstyp **Web** ausgewählt.  
	3. Wählen Sie **Weiter**.  

	![AAD-Anwendung hinzufügen – App-Info][8]

6. Unter **App-Eigenschaften**:

	1. Geben Sie unter **ANMELDE-URL** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest.
2. Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein.  
	3. Klicken Sie auf **OK**.  

	![AAD-Anwendung hinzufügen – App-Eigenschaften][9]

7. Wenn Sie fertig sind, wird die neue AAD-App geöffnet. Wählen Sie **Konfigurieren** aus: ![Contoso-AAD-App][10]

8. Legen Sie die **Antwort-URL** im Abschnitt _OAuth 2_ auf den Wert der Umleitungs-URL fest, der beim Hinzufügen der Office 365-Outlook-API im Azure-Portal angezeigt wurde. Wählen Sie **Anwendung hinzufügen** aus: ![Contoso-AAD-App konfigurieren][11]

9. Wählen Sie unter **Berechtigungen für andere Anwendungen** die Option **Office 365 Exchange Online** und dann **OK** aus: ![Contoso-App-Delegat][12]

	Auf der Konfigurationsseite können Sie nun sehen, dass _Office 365 Exchange Online_ der Liste _Berechtigungen für andere Anwendungen_ hinzugefügt wurde.

10. Wählen Sie für **Office 365 Exchange Online** die Option **Delegierte Berechtigungen** und dann die folgenden Berechtigungen aus:

	- Lesen und Schreiben von Benutzerkontakten
	- Lesen von Benutzerkontakten
	- Lesen und Schreiben von Benutzerkalendern
	- Lesen von Benutzerkalendern
	- Senden von E-Mails als Benutzer
	- Lesen und Schreiben von Benutzer-E-Mails
	- Lesen von Benutzer-E-Mails

	![Berechtigungen für Contoso-App-Delegat][13]

Eine neue Azure Active Directory-App wird erstellt. Sie können die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** kopieren und in Ihre Office 365 Outlook-API-Konfiguration im Azure-Portal einfügen.

Einige hilfreiche Informationen zu AAD-Anwendungen finden Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](../active-directory/active-directory-how-applications-are-added.md).

### Hinzufügen zusätzlicher Konfiguration in Logik-Apps
Wenn Sie Ihren Logik-Apps diese API hinzufügen, müssen Sie sich bei Ihrem Office 365 Outlook-Konto anmelden und den Logik-Apps das Herstellen einer Verbindung mit Ihrem Konto gestatten.

1. Melden Sie sich bei Ihrem Office 365 Outlook-Konto an.
2. Erlauben Sie Ihren Logik-Apps, sich mit Ihrem Office 365-Konto zu verbinden und es zu nutzen. 

Nachdem Sie die Verbindung hergestellt haben, geben Sie die Office 365 Outlook-Eigenschaften ein, z. B. den Posteingangsordner oder die E-Mail. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Office 365 Outlook-Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
Gilt für Version: 1.0.


### Bei Start eines anstehenden Ereignisses 
Löst einen Fluss aus, wenn ein anstehendes Kalenderereignis gestartet wird. ```GET: /Events/OnUpcomingEvents```

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


### E-Mails abrufen 
Ruft E-Mails aus einem Ordner ab. ```GET: /Mail```

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


### E-Mail senden 
Sendet eine E-Mail. ```POST: /Mail```

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


### E-Mail löschen 
Löscht eine E-Mail anhand der ID. ```DELETE: /Mail/{messageId}```

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


### Als gelesen markieren 
Markiert eine E-Mail als gelesen. ```POST: /Mail/MarkAsRead/{messageId}```

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


### Nachricht beantworten 
Antwortet auf eine E-Mail. ```POST: /Mail/ReplyTo/{messageId}```

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


### Anlagen abrufen 
Ruft die Nachrichtenanlage anhand der ID ab. ```GET: /Mail/{messageId}/Attachments/{attachmentId}```

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


### Bei neuer E-Mail 
Löst einen Fluss aus, wenn eine neue E-Mail eingeht. ```GET: /Mail/OnNewEmail```

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




### Kalender abrufen 
Ruft Kalender ab. ```GET: /datasets/calendars/tables```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|




### Ereignisse abrufen 
Ruft Elemente aus einem Kalender ab. ```GET: /datasets/calendars/tables/{table}/items```

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


### Ereignis erstellen 
Erstellt ein neues Ereignis. ```POST: /datasets/calendars/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|item| |Ja|body|(Keine)|Zu erstellendes Kalenderelement|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Ereignis abrufen 
Ruft ein bestimmtes Element aus einem Kalender ab. ```GET: /datasets/calendars/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines abzurufenden Kalenderelements|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Ereignis löschen 
Löscht ein Kalenderelement. ```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Kalenders.|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines zu löschenden Kalenderelements|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Ereignis aktualisieren 
Aktualisiert ein Kalenderelement teilweise. ```PATCH: /datasets/calendars/tables/{table}/items/{id}```

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


### Bei neuen Elementen 
Wird ausgelöst, wenn ein neues Kalenderelement erstellt wird. ```GET: /datasets/calendars/tables/{table}/onnewitems```

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


### Bei aktualisierten Elementen 
Wird ausgelöst, wenn ein Kalenderelement geändert wird. ```GET: /datasets/calendars/tables/{table}/onupdateditems```

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


### Ordner „Kontakte“ abrufen 
Ruft Ordner des Typs „Kontakte“ ab. ```GET: /datasets/contacts/tables```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Kontakte abrufen 
Ruft Kontakte aus einem Ordner des Typs „Kontakte“ ab. ```GET: /datasets/contacts/tables/{table}/items```

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


### Kontakt erstellen 
Erstellt einen neuen Kontakt. ```POST: /datasets/contacts/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“|
|item| |Ja|body|(Keine)|Zu erstellender Kontakt|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Kontakt abrufen 
Ruft einen bestimmten Kontakt aus einem Ordner „Kontakte“ ab. ```GET: /datasets/contacts/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner des abzurufenden Kontakts|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Kontakt löschen 
Löscht einen Kontakt. ```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Eindeutiger Bezeichner eines Ordners des Typs „Kontakte“.|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner des zu löschenden Kontakts|

#### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Kontakt aktualisieren 
Aktualisiert einen Kontakt teilweise. ```PATCH: /datasets/contacts/tables/{table}/items/{id}```

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


## Objektdefinitionen

#### TriggerBatchResponse[IDictionary[String,Object]]

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### SendMessage: E-Mail senden

| Name | Datentyp |Erforderlich|
|---|---|---|
|Anlagen|array|no|
|Aus|string|no|
|Cc|string|no|
|Bcc|string|no|
|Betreff|string|Ja|
|Body|string|Ja|
|Priorität|string|no|
|IsHtml|Boolescher Wert|no|
|To  
|string|Ja|

#### SendAttachment: Anlage

| Name | Datentyp |Erforderlich|
|---|---|---|
|@odata.type|string|no|
|Name|string|Ja|
|ContentBytes|string|Ja|


#### ReceiveMessage: E-Mail empfangen

| Name | Datentyp |Erforderlich|
|---|---|---|
|ID|string|no|
|IsRead|Boolescher Wert|no|
|HasAttachment|Boolescher Wert|no|
|DateTimeReceived|string|no|
|Anlagen|array|no|
|Aus|string|no|
|Cc|string|no|
|Bcc|string|no|
|Betreff|string|Ja|
|Body|string|Ja|
|Priorität|string|no|
|IsHtml|Boolescher Wert|no|
|To  
|string|Ja|


#### ReceiveAttachment: Anlage empfangen

| Name | Datentyp |Erforderlich|
|---|---|---|
|ID|string|Ja|
|ContentType|string|Ja|
|@odata.type|string|no|
|Name|string|no|
|ContentBytes|string|Ja|


#### DigestMessage: E-Mail senden

| Name | Datentyp |Erforderlich|
|---|---|---|
|Betreff|string|Ja|
|Body|string|no|
|Priorität|string|no|
|Digest|array|Ja|
|Anlagen|array|no|
|To  
|string|Ja|

#### TriggerBatchResponse[ReceiveMessage]

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### DataSetsMetadata

| Name | Datentyp |Erforderlich|
|---|---|---|
|tabular|nicht definiert|no|
|Blob|nicht definiert|no|


#### TabularDataSetsMetadata

| Name | Datentyp |Erforderlich|
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|


#### BlobDataSetsMetadata

| Name | Datentyp |Erforderlich|
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

| Name | Datentyp |Erforderlich|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|nicht definiert|no|


#### OptionsEmailSubscription: Modell für Optionen für E-Mail-Abonnement

| Name | Datentyp |Erforderlich|
|---|---|---|
|NotificationUrl|string|no|
|Message|nicht definiert|no|

#### MessageWithOptions: E-Mail mit Benutzeroptionen Dies ist die Nachricht, die als Teil der Benutzereingabe erwartet wird

| Name | Datentyp |Erforderlich|
|---|---|---|
|Betreff|string|Ja|
|Optionen|string|Ja|
|Body|string|no|
|Priorität|string|no|
|Anlagen|array|no|
|To  
|string|Ja|

#### SubscriptionResponse: Modell für das Genehmigungs-E-Mail-Abonnement

| Name | Datentyp |Erforderlich|
|---|---|---|
|id|string|no|
|resource|string|no|
|notificationType|string|no|
|notificationUrl|string|no|


#### ApprovalEmailSubscription: Modell für das Genehmigungs-E-Mail-Abonnement

| Name | Datentyp |Erforderlich|
|---|---|---|
|NotificationUrl|string|no|
|Message|nicht definiert|no|


#### ApprovalMessage: Genehmigungs-E-Mail-Nachricht. Dies ist die Nachricht, die als Teil der Benutzereingabe erwartet wird

| Name | Datentyp |Erforderlich|
|---|---|---|
|Betreff|string|Ja|
|Optionen|string|Ja|
|Body|string|no|
|Priorität|string|no|
|Anlagen|array|no|
|To  
|string|Ja|

#### ApprovalEmailResponse: Genehmigungs-E-Mail-Antwort

| Name | Datentyp |Erforderlich|
|---|---|---|
|SelectedOption|string|no|

#### TablesList

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### Tabelle

| Name | Datentyp |Erforderlich|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|


#### Item

| Name | Datentyp |Erforderlich|
|---|---|---|
|ItemInternalId|string|no|


#### CalendarItemsList: Die Liste der Kalenderelemente

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### CalendarItem: Stellt ein Kalendertabellenelement dar

| Name | Datentyp |Erforderlich|
|---|---|---|
|ItemInternalId|string|no|


#### ContactItemsList: Die Liste der Kontaktelemente

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### ContactItem: Stellt ein Kontakttabellenelement dar

| Name | Datentyp |Erforderlich|
|---|---|---|
|ItemInternalId|string|no|


#### DataSetsList

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### DataSet

| Name | Datentyp | Erforderlich|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|


## Nächste Schritte
Nach dem Hinzufügen der Office 365-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen Sie eine Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0302_2016-->