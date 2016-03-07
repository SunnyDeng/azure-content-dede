<properties
	pageTitle="Hinzufügen der Azure Blob Storage-API in Ihren Logik-Apps | Microsoft Azure"
	description="Übersicht über die Azure Blob Storage-API und die REST-API-Parameter"
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
   ms.date="02/23/2016"
   ms.author="mandia"/>

# Erste Schritte mit der Azure-Blobspeicher-API
Stellen Sie eine Verbindung mit einem Azure-Blob her, um Dateien in einem Blobcontainer zu verwalten, d. h,. Dateien zu erstellen, zu löschen usw.

Die Azure-Blobspeicher-API kann in Logik-Apps verwendet werden.

>[AZURE.NOTE] Diese Version des Artikels gilt für Logik-Apps mit der Schemaversion „2015-08-01-preview“. Um die Schemaversion „2014-12-01-preview“ aufzurufen, klicken Sie auf [Azure Storage-Blobconnector](../app-service-logic/app-service-logic-connector-azurestorageblob.md).

Azure-Blobspeicher ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus dem Blob abgerufen werden. 
- Ausführen von Aktionen, über die Sie u. a. eine Datei erstellen und Dateiinhalte abrufen können. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Sie können z. B. in einer Datei in Ihrem Blob den Begriff „Dringend“ suchen und dann alle Dateien mit diesem Begriff mithilfe von Office 365 in einer E-Mail senden. 

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Ein Azure-Blob bietet die folgenden Aktionen. Es gibt keine Trigger.

| Trigger | Aktionen|
| --- | --- |
| Keine. | <ul><li>Datei erstellen</li><li>Datei kopieren</li><li>Datei löschen</li><li>Archiv in Ordner extrahieren</li><li>Dateiinhalt abrufen</li><li>Dateiinhalt anhand des Pfads abrufen</li><li>Dateimetadaten abrufen</li><li>Dateimetadaten anhand des Pfads abrufen</li><li>Datei aktualisieren</li></ul> |

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit einem Azure-Blob
Wenn Sie Ihren Logik-Apps diese API hinzufügen, geben Sie die folgenden Werte des Speicherkontos ein:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Azure-Speicherkontoname | Ja | Namen des Blobspeicherkontos|
|Zugriffsschlüssel für das Azure-Speicherkonto | Ja | Zugriffsschlüssel Ihres Blobspeicherkontos|

Nachdem Sie die Verbindung hergestellt haben, geben Sie die Blobeigenschaften ein, z. B. Ordnerpfad oder Dateiname. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Blobverbindung in anderen Logik-Apps verwenden.
 

## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Datei erstellen
Lädt eine lokale Datei in Azure Blob Storage hoch. ```POST: /datasets/default/files```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|Ja|query| (Keine) |Ordnerpfad zum Hochladen der Datei in Azure Blob Storage|
|name|string|Ja|query|(Keine) |Name der Datei, die in Azure Blob Storage erstellt werden soll|
|body|string(binary) |Ja|body|(Keine)|Inhalt der Datei, die in Azure Blob Storage hochgeladen werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Datei kopieren
Kopiert eine Datei in Azure Blob Storage. ```POST: /datasets/default/copyFile```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine) |URL zur Quelldatei|
|Ziel|string|Ja|query| (Keine)|Zieldateipfad in Azure Blob Storage, einschließlich Zieldateiname|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldatei, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei löschen
Löscht eine Datei aus Azure Blob Storage. ```DELETE: /datasets/default/files/{id}```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei, die aus Azure Blob Storage gelöscht werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Archiv in Ordner extrahieren
Extrahiert eine Archivdatei in einen Ordner in Azure Blob Storage (Beispiel: ZIP). ```POST: /datasets/default/ExtractFolderV2```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query| (Keine)|Pfad zur Archivdatei|
|Ziel|string|Ja|query|(Keine) |Pfad in Azure Blob Storage zum Extrahieren des Archivinhalts|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldateien, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt abrufen
Ruft Dateiinhalte aus Azure Blob Storage anhand der ID ab. ```GET: /datasets/default/files/{id}/content```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand des Pfads abrufen
Ruft Dateiinhalte aus Azure Blob Storage anhand des Pfads ab. ```GET: /datasets/default/GetFileContentByPath```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine) |Eindeutiger Pfad zur Datei in Azure Blob Storage|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten abrufen
Ruft Dateimetadaten aus Azure Blob Storage anhand der Datei-ID ab. ```GET: /datasets/default/files/{id}```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand des Pfads abrufen
Ruft Dateimetadaten aus Azure Blob Storage anhand des Pfads ab. ```GET: /datasets/default/GetFileByPath```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine)|Eindeutiger Pfad zur Datei in Azure Blob Storage|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei aktualisieren
Aktualisiert eine Datei in Azure Blob Storage. ```PUT: /datasets/default/files/{id}```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei, die in Azure Blob Storage aktualisiert werden soll|
|body|string(binary) |Ja|body|(Keine) |Inhalt der Datei, die in Azure Blob Storage aktualisiert werden soll|

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

## Nächste Schritte

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->