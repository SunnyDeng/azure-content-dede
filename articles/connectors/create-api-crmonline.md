<properties
pageTitle="Hinzufügen der Dynamic CRM Online-API-in PowerApps Enterprise oder zu Ihren Logik-Apps | Microsoft Azure"
description="Übersicht über die CRM Online-API und REST-API-Parameter"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/23/2016"
ms.author="deonhe"/>

# Erste Schritte mit der CRM-API
Verbinden Sie sich mit Dynamics CRM Online, um z. B. einen neuen Datensatz zu erstellen oder ein Element zu aktualisieren.

Die CRM Online-API kann von PowerApps Enterprise- und Logik-Apps verwendet werden.

CRM Online ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus CRM Online abgerufen werden. 
- Verwenden von Aktionen, die z. B. einen Datensatz löschen oder Entitäten abrufen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn ein Element in CRM aktualisiert wird, können Sie z. B. über Office 365 eine E-Mail senden.


Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Die CRM-API bietet die folgenden Aktionen. Es gibt keine Trigger.

| Trigger | Aktionen|
| --- | --- |
|Keine.| <ul><li>Neuen Datensatz erstellen</li><li>Datensätze abrufen</li><li>Datensatz löschen</li><li>Datensatz abrufen</li><li>Entitäten abrufen</li><li>Element aktualisieren</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit CRM Online

### Hinzufügen zusätzlicher Konfiguration in PowerApps
Wenn Sie CRM Online zu PowerApps Enterprise hinzufügen, geben Sie die **Client-ID** und den **App-Schlüssel** Ihrer Azure Active Directory-Anwendung (AAD) für Dynamics CRM Online ein. Der Wert von **Umleitungs-URL** wird auch in Ihrer CRM Online-Anwendung verwendet. Wenn Sie noch keine Anwendung haben, führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), dann **Active Directory**, und wählen Sie den Namen des Mandanten Ihrer Organisation aus.
2. Klicken Sie auf der Registerkarte **Anwendungen**auf „Hinzufügen“. Auf der Seite **Anwendung hinzufügen**:  

	1. Geben Sie einen **Namen** für Ihre Anwendung ein.  
	2. Lassen Sie als Anwendungstyp **Web** ausgewählt.  
	3. Wählen Sie **Weiter**.

	![AAD-Anwendung hinzufügen – App-Info][9]

3. Unter **App-Eigenschaften**:

	1. Geben Sie unter **ANMELDE-URL** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest.
2. Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein.  
	3. Klicken Sie auf **OK**.  

	![AAD-Anwendung hinzufügen – App-Eigenschaften][10]

4. Wählen Sie in der neuen Anwendung **Konfigurieren** aus.
5. Legen Sie unter _OAuth 2_ die **Umleitungs-URL** auf den Wert der Umleitungs-URL fest, die angezeigt wird, wenn Sie die CRM Online-API im Azure-Portal hinzufügen: ![Contoso-AAD-App konfigurieren][12]

Nun können Sie die **Client-ID** und den **App-Schlüssel** kopieren und in Ihre CRM Online-Konfiguration im Azure-Portal einfügen.

### Hinzufügen zusätzlicher Konfiguration in Logik-Apps
Wenn Sie die API Ihren Logik-Apps hinzufügen, müssen Sie sich bei Dynamic CRM Online anmelden.

Nachdem Sie die Verbindung hergestellt haben, geben Sie die CRM Online-Eigenschaften ein, z. B. Tabelle oder Dataset. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
#### Diese Dokumentation gilt für Version 1.0.

### Neuen Datensatz erstellen 
Erstellt einen neuen Datensatz in einer Entität. ```POST: /datasets/{dataset}/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Eindeutiger Name der CRM-Organisation contoso.crm|
|Tabelle|string|Ja|path|(Keine)|Name der Entität|
|item| |Ja|body|(Keine)|Zu erstellender Datensatz|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datensätze abrufen 
 Ruft Datensätze für eine Entität ab. ```GET: /datasets/{dataset}/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Eindeutiger Name der CRM-Organisation contoso.crm|
|Tabelle|string|Ja|path|(Keine)|Name der Entität|
|$skip|integer|no|query|(Keine)|Anzahl der zu überspringenden Einträge. Der Standardwert ist 0.|
|$top|integer|no|query|(Keine)|Maximale Anzahl abzurufender Einträge. Der Standardwert ist 100.|
|$filter|string|no|query|(Keine)|ODATA-Filterabfrage zum Einschränken der Anzahl der Einträge.|
|$orderby|string|no|query|(Keine)|ODATA-orderBy-Abfrage zum Angeben der Reihenfolge von Einträgen.|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|




### Datasets zurückgeben 
 Gibt die Datasets zurück. ```GET: /datasets```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



### Tabellenelement abrufen 
Dient zum Abrufen eines bestimmten Datensatzes, der für eine CRM-Entität vorhanden ist. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Eindeutiger Name der CRM-Organisation contoso.crm|
|Tabelle|string|Ja|path|(Keine)|Name der Entität|
|id|string|Ja|path|(Keine)|Bezeichner des Datensatzes|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Element aus einer Liste löschen 
Löscht ein Element aus einer Liste. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Eindeutiger Name der CRM-Organisation contoso.crm|
|Tabelle|string|Ja|path|(Keine)|Name der Entität|
|id|string|Ja|path|(Keine)|Bezeichner des Datensatzes|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|



### Korrigiert ein vorhandenes Tabellenelement 
Dient zum teilweisen Aktualisieren eines für eine CRM-Entität vorhandenen Datensatzes. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Eindeutiger Name der CRM-Organisation contoso.crm|
|Tabelle|string|Ja|path|(Keine)|Name der Entität|
|id|string|Ja|path|(Keine)|Bezeichner des Datensatzes|
|item| |Ja|body|(Keine)|Zu aktualisierender Datensatz|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Entitäten abrufen 
Dient zum Abrufen der Liste der Entitäten, die in einer CRM-Instanz vorhanden sind. ```GET: /datasets/{dataset}/tables```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Eindeutiger Name der CRM-Organisation contoso.crm|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


## Objektdefinitionen

#### DataSetsMetadata

|Eigenschaftenname | Datentyp | Erforderlich|
|---|---|---|
|tabular|nicht definiert|no|
|Blob|nicht definiert|no|

#### TabularDataSetsMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|


#### TableMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|nicht definiert|no|

#### DataSetsList

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|

#### DataSet

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|


#### Tabelle

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Item

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


#### TablesList

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


## Nächste Schritte
Nach Hinzufügen der CRM Online-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)


[9]: ./media/create-api-crmonline/aad-tenant-applications-add-appinfo.png
[10]: ./media/create-api-crmonline/aad-tenant-applications-add-app-properties.png
[12]: ./media/create-api-crmonline/contoso-aad-app-configure.png

<!---HONumber=AcomDC_0224_2016-->