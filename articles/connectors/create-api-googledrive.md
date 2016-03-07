<properties
	pageTitle="Hinzufügen der Google Drive-API zu PowerApps oder Logik-Apps | Microsoft Azure"
	description="Übersicht über die Google Drive-API und REST-API-Parameter"
	services=""
    suite=""
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

# Erste Schritte mit der Google Drive-API
Verbinden Sie sich mit Google Drive, um Dateien zu erstellen, Zeilen abzurufen usw.

Die Google Drive-API kann von PowerApps und Logik-Apps verwendet werden.

Google Drive ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus einer Suche abgerufen werden. 
- Verwenden von Aktionen, um Bilder, Nachrichten und mehr zu suchen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Sie können z. B. ein Video suchen und dann Twitter verwenden, um das Video in einem Twitter-Feed zu posten.
- Fügen Sie die Google Drive-API zu PowerApps Enterprise hinzu. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger und Aktionen
Google Drive bietet die folgenden Aktionen. Es gibt keine Trigger.

Trigger | Aktionen
--- | ---
Keine | <ul><li>Datei erstellen</li><li>Zeile einfügen</li><li>Datei kopieren</li><li>Datei löschen</li><li>Zeile löschen</li><li>Archiv in Ordner extrahieren</li><li>Dateiinhalt anhand der ID abrufen</li><li>Dateiinhalt anhand des Pfads abrufen</li><li>Dateimetadaten anhand der ID abrufen</li><li>Dateimetadaten anhand des Pfads abrufen</li><li>Zeile abrufen</li><li>Datei aktualisieren</li><li>Zeile aktualisieren</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.


## Herstellen der Verbindung mit Google Drive

### Hinzufügen zusätzlicher Konfiguration in PowerApps
Wenn Sie Google Drive zu PowerApps Enterprise hinzufügen, geben Sie die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** Ihrer Google Drive-Anwendung ein. Der Wert von **Umleitungs-URL** wird auch in Ihrer Google Drive-Anwendung verwendet. Wenn Sie noch keine Google Drive-Anwendung haben, führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Melden Sie sich bei der [Google Developers Console][5] an, und wählen Sie **Leeres Projekt erstellen** aus: ![Google Developers Console][6]

2. Geben Sie die Anwendungseigenschaften ein, und wählen Sie **Erstellen** aus.
3. Wählen Sie **Google-APIs verwenden** aus: ![Verwenden von Google-APIs][8]  
4. Wählen Sie in der Übersicht **Drive API** aus: ![Übersicht über Google Drive-API][9]  
5. Wählen Sie **Enable API** aus: ![Aktivieren der Google Drive-API][10]  
6. Wählen Sie nach dem Aktivieren der Drive API die Registerkarte **Credentials** und dann **OAuth 2.0 Client ID** aus: ![Hinzufügen von Anmeldeinformationen][12]  
7. Wählen Sie **Configure consent screen** aus.
8. Geben Sie auf dem **OAuth-Zustimmungsbildschirm** unter **Produktname** einen Produktnamen ein, und wählen Sie **Speichern** aus: ![Konfigurieren des Zustimmungsbildschirms][13]  
9. Auf der Seite „Create client ID“:  

	1. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus.
	2. Geben Sie einen Namen für den Client ein.
	3. Geben Sie die Umleitungs-URL ein, die angezeigt wird, wenn Sie die Google Drive-API im Azure-Portal hinzufügen.
	4. Klicken Sie auf **Erstellen**.  

	![Erstellen der Client-ID][14]

11. Die Client-ID und der geheime Client-Schlüssel der registrierten Anwendung werden angezeigt.

Nun können Sie die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** kopieren und in Ihre Google Drive-Konfiguration im Azure-Portal einfügen.


### Hinzufügen zusätzlicher Konfiguration in Logik-Apps
Wenn Sie diese API Ihren Logik-Apps hinzufügen, müssen Sie ihnen das Herstellen einer Verbindung mit Google Drive erlauben.

1. Melden Sie sich bei Ihrem Google Drive-Konto an.
2. Wählen Sie **Autorisieren** aus, um zu erlauben, dass Ihre Logik-Apps sich mit Ihrem Google Drive-Konto verbinden und es nutzen. 

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Google Drive-Eigenschaften ein, z. B. Ordnerpfad oder Dateiname. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Google Drive-Verbindung in anderen Logik-Apps verwenden.


## Swagger-REST-API – Referenz

### Datei erstellen    
Lädt eine in Google Drive hoch. ```POST: /datasets/default/files```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|Ja|query|(Keine) |Ordnerpfad zum Hochladen der Datei in Google Drive|
|name|string|Ja|query|(Keine) |Name der Datei, die in Google Drive erstellt werden soll|
|body|string(binary) |Ja|body| (Keine)|Inhalt der Datei, die in Google Drive hochgeladen werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile einfügen    
Fügt eine Zeile in eine Google-Tabelle ein. ```POST: /datasets/{dataset}/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|dataset|string|Ja|path| (Keine)|Eindeutiger Bezeichner der Datei mit der Google-Tabelle|
|Tabelle|string|Ja|path|(Keine) |Eindeutige ID des Arbeitsblatts|
|item|ItemInternalId: string |Ja|body|(Keine) |In der angegebenen Tabelle einzufügende Zeile|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei kopieren    
Kopiert eine Datei in Google Drive. ```POST: /datasets/default/copyFile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query| (Keine)|URL zur Quelldatei|
|Ziel|string|Ja|query|(Keine) |Zieldateipfad in Google Drive, einschließlich Zieldateiname|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldatei, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei löschen    
Löscht eine Datei aus Google Drive. ```DELETE: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der aus Google Drive zu löschenden Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile löschen    
Löscht eine Zeile aus einer Google-Tabelle. ```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|dataset|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei mit der Google-Tabelle|
|Tabelle|string|Ja|path|(Keine) |Eindeutige ID des Arbeitsblatts|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der zu löschenden Zeile|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Archiv in Ordner extrahieren    
Extrahiert eine Archivdatei in einen Ordner in Google Drive (Beispiel: ZIP). ```POST: /datasets/default/extractFolderV2```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine) |Pfad zur Archivdatei|
|Ziel|string|Ja|query|(Keine) |Pfad in Google Drive, in den der Archivinhalt extrahiert wird|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldateien, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand der ID abrufen    
Ruft den Inhalt der Datei aus Google Drive anhand der ID ab. ```GET: /datasets/default/files/{id}/content```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der aus Google Drive abzurufenden Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand des Pfads abrufen    
Ruft den Inhalt der Datei aus Google Drive anhand des Pfads ab. ```GET: /datasets/default/GetFileContentByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine) |Pfad der Datei in Google Drive|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand der ID abrufen    
Ruft Dateimetadaten aus Google Drive anhand der ID ab. ```GET: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei in Google Drive|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand des Pfads abrufen    
Ruft Dateimetadaten aus Google Drive anhand des Pfads ab. ```GET: /datasets/default/GetFileByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine) |Pfad der Datei in Google Drive|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile abrufen    
Ruft eine einzelne Zeile aus einer Google-Tabelle ab. ```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|dataset|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei mit der Google-Tabelle|
|Tabelle|string|Ja|path|(Keine) |Eindeutige ID des Arbeitsblatts|
|id|string|Ja|path| (Keine)|Eindeutiger Bezeichner der abzurufenden Zeile|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei aktualisieren    
Aktualisiert eine Datei in Google Drive. ```PUT: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der in Google Drive zu aktualisierenden Datei|
|body|string(binary) |Ja|body| (Keine)|Inhalt der Datei, die in Google Drive hochgeladen werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile aktualisieren    
Aktualisiert eine Zeile in einer Google-Tabelle. ```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|dataset|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei mit der Google-Tabelle|
|Tabelle|string|Ja|path| (Keine)|Eindeutige ID des Arbeitsblatts|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der zu aktualisierenden Zeile|
|item|ItemInternalId: string |Ja|body|(Keine) |Zeile mit aktualisierten Werten|

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

#### BlobMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|ID|string|no|
|Name|string|no|
|DisplayName|string|no|
|Path|string|no|
|LastModified|string|no|
|Größe|integer|no|
|MediaType|string|no|
|IsFolder|Boolescher Wert|no|
|ETag|string|no|
|FileLocator|string|no|

#### TableMetadata

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|nicht definiert|no|

#### TablesList

|Eigenschaftenname | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|

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


## Nächste Schritte
Nach Hinzufügen der Google Drive-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0224_2016-->