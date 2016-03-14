<properties
	pageTitle="Hinzufügen der Box-API zu Ihren Logik-Apps | Microsoft Azure"
	description="Übersicht über die Box-API und REST-API-Parameter"
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

# Erste Schritte mit der Box-API
Verbinden Sie sich mit Box, um Dateien zu erstellen, zu löschen usw. Die Box-API kann in Folgendem verwendet werden:

- Logik-Apps 

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [Box-Connector](../app-service-logic/app-service-logic-connector-box.md).

Box ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Box abgerufen werden. 
- Verwenden von Triggern, wenn eine Datei erstellt oder aktualisiert wird.
- Verwenden von Aktionen, um eine Datei zu kopieren, zu löschen usw. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn z. B. in Box eine Datei geändert wird, können Sie diese Datei auswählen und mithilfe von Office 365 per E-Mail senden.

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Box weist die folgenden Trigger und Aktionen auf.

| Trigger | Aktionen|
| --- | --- |
|<ul><li>Wenn eine Datei erstellt wird</li><li>Wenn eine Datei geändert wird</li></ul> | <ul><li>Datei erstellen</li><li>Wenn eine Datei erstellt wird</li><li>Datei kopieren</li><li>Datei löschen</li><li>Archiv in Ordner extrahieren</li><li>Dateiinhalt anhand der ID abrufen</li><li>Dateiinhalt anhand des Pfads abrufen</li><li>Dateimetadaten anhand der ID abrufen</li><li>Dateimetadaten anhand des Pfads abrufen</li><li>Datei aktualisieren</li><li>Wenn eine Datei geändert wird</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen einer Verbindung mit Box
Wenn Sie diese API Ihren Logik-Apps hinzufügen, müssen Sie ihnen das Herstellen einer Verbindung mit Box erlauben.

1. Melden Sie sich bei Ihrem Box-Konto an.
2. Wählen Sie **Autorisieren** aus, um zu erlauben, dass Ihre Logik-Apps sich mit Ihrem Box-Konto verbinden und es nutzen. 

Nachdem Sie die Verbindung hergestellt haben, geben Sie die Box-Eigenschaften ein. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Box-Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Datei erstellen
Lädt eine Datei in Box hoch. ```POST: /datasets/default/files```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|Ja|query|Keine |Ordnerpfad zum Hochladen der Datei in Box|
|name|string|Ja|query|Keine |Name der Datei, die in Box erstellt werden soll|
|body|string(binary) |Ja|body|Keine |Inhalt der Datei, die in Box hochgeladen werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Wenn eine Datei erstellt wird
Löst einen Datenfluss aus, wenn in einem Box-Ordner eine neue Datei erstellt wird. ```GET: /datasets/default/triggers/onnewfile```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|Keine |Eindeutiger Bezeichner des Ordners in Box|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei kopieren
Kopiert eine Datei in Box. ```POST: /datasets/default/copyFile```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|Keine |URL zur Quelldatei|
|Ziel|string|Ja|query| Keine|Zieldateipfad in Box, einschließlich Zieldateiname|
|overwrite|Boolescher Wert|Nein|query| Keine|Überschreibt die Zieldatei, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei löschen
Löscht eine Datei aus Box. ```DELETE: /datasets/default/files/{id}```


| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|Keine |Eindeutiger Bezeichner der aus Box zu löschenden Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Archiv in Ordner extrahieren
Extrahiert eine Archivdatei in einen Ordner in Box (Beispiel: ZIP). ```POST: /datasets/default/extractFolderV2```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query| |Pfad zur Archivdatei|
|Ziel|string|Ja|query| |Pfad in Box, in den der Archivinhalt extrahiert wird|
|overwrite|Boolescher Wert|Nein|query| |Überschreibt die Zieldateien, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand der ID abrufen
Ruft den Dateiinhalt anhand der ID aus Box ab. ```GET: /datasets/default/files/{id}/content```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|Keine |Eindeutiger Bezeichner der Datei in Box|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand des Pfads abrufen
Ruft den Dateiinhalt anhand des Pfads aus Box ab. ```GET: /datasets/default/GetFileContentByPath```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|Keine |Eindeutiger Pfad zur Datei in Box|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand der ID abrufen
Ruft Dateimetadaten aus Box anhand der Datei-ID ab. ```GET: /datasets/default/files/{id}```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path| Keine|Eindeutiger Bezeichner der Datei in Box|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand des Pfads abrufen
Ruft Dateimetadaten aus Box anhand des Pfads ab. ```GET: /datasets/default/GetFileByPath```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|Keine |Eindeutiger Pfad zur Datei in Box|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei aktualisieren
Aktualisiert eine Datei in Box. ```PUT: /datasets/default/files/{id}```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path| Keine|Eindeutiger Bezeichner der Datei, die in Box aktualisiert werden soll|
|body|string(binary) |Ja|body|Keine |Inhalt der Datei, die in Box aktualisiert werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Wenn eine Datei geändert wird
Löst einen Datenfluss aus, wenn in einem Box-Ordner eine Datei geändert wird. ```GET: /datasets/default/triggers/onupdatedfile```

| Name|Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|Keine |Eindeutiger Bezeichner des Ordners in Box|

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

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0302_2016-->