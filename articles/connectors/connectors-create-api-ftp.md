<properties
	pageTitle="Hinzufügen der FTP-API zu Ihren Logik-Apps | Microsoft Azure"
	description="Übersicht über die FTP-API und REST-API-Parameter"
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
   ms.date="02/25/2016"
   ms.author="mandia"/>

# Erste Schritte mit der FTP-API
Stellen Sie eine Verbindung mit einem FTP-Server her, um Ihre Dateien zu verwalten, d. h. diese hochzuladen, zu löschen usw. Die FTP-API kann in Folgendem verwendet werden:

- Logik-Apps

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [FTP-Connector](../app-service-logic/app-service-logic-connector-ftp.md).

Mit FTP können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die über FTP abgerufen werden. 
- Implementieren eines Triggers, wenn eine Datei aktualisiert wird.
- Ausführen von Aktionen, mit denen Sie u. a. eine Datei erstellen und Dateiinhalte abrufen können. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Sie können z. B. den Inhalt einer Datei abrufen und dann eine SQL-Datenbank aktualisieren. 

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger und Aktionen
FTP verfügt über folgende Trigger und Aktionen:

Trigger | Actions
--- | ---
<ul><li>Ruft eine aktualisierte Datei ab. </li></ul> | <ul><li>Datei erstellen</li><li>Datei kopieren</li><li>Datei löschen</li><li>Archiv in Ordner extrahieren</li><li>Dateiinhalt abrufen</li><li>Dateiinhalt anhand des Pfads abrufen</li><li>Dateimetadaten abrufen</li><li>Dateimetadaten anhand des Pfads abrufen</li><li>Aktualisierte Datei abrufen</li><li>Datei aktualisieren</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer FTP-Verbindung
Wenn Sie diese API Ihren Logik-Apps hinzufügen, geben Sie die folgenden Werte ein:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|Serveradresse| Ja | Geben Sie den vollqualifizierten Domänennamen (FQDN) oder die IP-Adresse des FTP-Servers ein.|
|Benutzername| Ja | Geben Sie den Benutzernamen für die Verbindung mit dem FTP-Server ein.|
|Kennwort | Ja | Geben Sie das Kennwort für den Benutzernamen ein.|

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die FTP-Eigenschaften ein, z. B. Quelldatei oder Zielordner. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe FTP-Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Datei erstellen
Lädt eine Datei auf einen FTP-Server hoch. ```POST: /datasets/default/files```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|Ja|query|(Keine) |Ordnerpfad zum Hochladen der Datei auf den FTP-Server|
|name|string|Ja|query| (Keine)|Name der Datei, die auf dem FTP-Server erstellt werden soll|
|body| |Ja|body|(Keine) |Inhalt der Datei, die auf den FTP-Server hochgeladen werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Datei kopieren
Kopiert eine Datei auf einen FTP-Server. ```POST: /datasets/default/copyFile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine) |URL zur Quelldatei|
|Ziel|string|Ja|query|(Keine) |Zieldateipfad auf dem FTP-Server, einschließlich Zieldateiname|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldatei, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Datei löschen 
Löscht eine Datei auf dem FTP-Server. ```DELETE: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der auf dem FTP-Server zu löschenden Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Ordner extrahieren
Extrahiert eine Archivdatei in einen Ordner auf dem FTP-Server (Beispiel: ZIP). ```POST: /datasets/default/extractFolderV2```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query| (Keine)|Pfad zur Archivdatei|
|Ziel|string|Ja|query| (Keine)|Pfad zum Zielordner|
|overwrite|Boolescher Wert|no|query|(Keine)|Überschreibt die Zieldateien, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

### Dateiinhalte abrufen
Ruft den Dateiinhalt anhand der ID vom FTP-Server ab. ```GET: /datasets/default/files/{id}/content```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand des Pfads abrufen
Ruft den Dateiinhalt anhand des Pfads vom FTP-Server ab. ```GET: /datasets/default/GetFileContentByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine) |Eindeutiger Pfad zur Datei auf dem FTP-Server|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten abrufen 
Ruft Dateimetadaten auf dem FTP-Server anhand der Datei-ID ab. ```GET: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der Datei|

#### Antwort
| Name | Beschreibung |
| --- | --- |
| 200 | OK | 
| die Standardeinstellung | Fehler beim Vorgang.


### Dateimetadaten anhand des Pfads abrufen
Ruft Dateimetadaten auf dem FTP-Server anhand des Pfads ab. ```GET: /datasets/default/GetFileByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query| (Keine)|Eindeutiger Pfad zur Datei auf dem FTP-Server|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Ruft eine aktualisierte Datei ab
Ruft eine aktualisierte Datei ab. ```GET: /datasets/default/triggers/onupdatedfile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|(Keine) |Ordner-ID, unter der nach einer aktualisierten Datei gesucht werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei aktualisieren 
Aktualisiert eine Datei auf dem FTP-Server. ```PUT: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path| (Keine)|Eindeutiger Bezeichner der Datei, die auf dem FTP-Server aktualisiert werden soll|
|body| |Ja|body|(Keine) |Inhalt der Datei, die auf dem FTP-Server aktualisiert werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


## Objektdefinitionen

#### DataSetsMetadata

| Name | Datentyp | Erforderlich |
|---|---|---|
|tabular|nicht definiert|no|
|Blob|nicht definiert|no|

#### TabularDataSetsMetadata

| Name | Datentyp | Erforderlich |
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Name | Datentyp | Erforderlich |
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### BlobMetadata

| Name | Datentyp | Erforderlich |
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

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0323_2016-->