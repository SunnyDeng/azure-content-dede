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
   ms.date="03/02/2016"
   ms.author="mandia"/>

# Erste Schritte mit der Google Drive-API
Verbinden Sie sich mit Google Drive, um Dateien zu erstellen, Zeilen abzurufen usw. Die Google Drive-API kann in Folgendem verwendet werden:

- Logik-Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

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

Wenn Sie diese API Ihren Logik-Apps hinzufügen, müssen Sie ihnen das Herstellen einer Verbindung mit Google Drive erlauben.

1. Melden Sie sich bei Ihrem Google Drive-Konto an.
2. Erlauben Sie, dass Ihre Logik-Apps sich mit Ihrem Google Drive verbinden und es nutzen. 

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Google Drive-Eigenschaften ein, z. B. Ordnerpfad oder Dateiname. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Google Drive-Verbindung in anderen Logik-Apps verwenden.


## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Datei erstellen    
Lädt eine Datei in Google Drive hoch. ```POST: /datasets/default/files```

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
Ruft den Inhalt der Datei anhand der ID aus Google Drive ab. ```GET: /datasets/default/files/{id}/content```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der aus Google Drive abzurufenden Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand des Pfads abrufen    
Ruft den Inhalt der Datei anhand des Pfads aus Google Drive ab. ```GET: /datasets/default/GetFileContentByPath```

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
Ruft Dateimetadaten anhand des Pfads aus Google Drive ab. ```GET: /datasets/default/GetFileByPath```

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

[Erstellen Sie eine Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

Gehen Sie zur [Liste der APIs](apis-list.md) zurück.


<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/create-api-googledrive/google-developers-console.png
[8]: ./media/create-api-googledrive/use-google-apis.png
[9]: ./media/create-api-googledrive/googledrive-api-overview.png
[10]: ./media/create-api-googledrive/enable-googledrive-api.png
[12]: ./media/create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/create-api-googledrive/configure-consent-screen.png
[14]: ./media/create-api-googledrive/create-client-id.png

<!---HONumber=AcomDC_0309_2016-->