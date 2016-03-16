<properties
	pageTitle="Hinzufügen der Dropbox-API zu PowerApps Enterprise- oder Logik-Apps | Microsoft Azure"
	description="Übersicht über die Dropbox-API und REST-API-Parameter"
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

# Erste Schritte mit der Dropbox-API 
Verbinden Sie sich mit Dropbox, um Dateien zu verwalten, d. h. erstellen, abrufen usw. Die Dropbox-API kann in Folgendem verwendet werden:

- Logik-Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-dropbox.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-dropbox.md)

Dropbox ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus Dropbox abgerufen werden. 
- Verwenden von Triggern, wenn eine Datei erstellt oder aktualisiert wird.
- Verwenden von Aktionen, um z. B. eine Datei zu kopieren oder zu löschen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Wenn eine neue Datei in Dropbox erstellt wird, können Sie die Datei mit Office 365 per E-Mail senden.
- Hinzufügen der Dropbox-API zu PowerApps Enterprise. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Dropbox weist die folgenden Trigger und Aktionen auf.

Trigger | Aktionen
--- | ---
<ul><li>Wenn eine Datei erstellt wird</li><li>Wenn eine Datei geändert wird</li></ul> | <ul><li>Datei erstellen</li><li>Wenn eine Datei erstellt wird</li><li>Datei kopieren</li><li>Datei löschen</li><li>Archiv in Ordner extrahieren</li><li>Dateiinhalt anhand der ID abrufen</li><li>Datei mithilfe des Pfads abrufen</li><li>Dateimetadaten anhand der ID abrufen</li><li>Dateimetadaten anhand des Pfads abrufen</li><li>Datei aktualisieren</li><li>Wenn eine Datei geändert wird</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen der Verbindung mit Dropbox

### Hinzufügen zusätzlicher Konfiguration in PowerApps
Wenn Sie Dropbox zu PowerApps Enterprise hinzufügen, geben Sie die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** Ihrer Dropbox-Anwendung ein. Der Wert von **Umleitungs-URL** wird auch in Ihrer Dropbox-Anwendung verwendet. Wenn Sie noch keine Dropbox-Anwendung haben, führen Sie die folgenden Schritte aus, um die Anwendung zu erstellen:

1. Melden Sie sich bei [Dropbox][1] an.
2. Wechseln Sie zur Dropbox-Entwicklerwebsite und wählen Sie **My apps**: ![Dropbox-Entwicklerwebsite][8]  
3. Klicken Sie auf **Create app**: ![Dropbox-App erstellen][9]  
4. In **Create a new app on the Dropbox Platform**:  

	1. Wählen Sie für **Choose API** die **Dropbox API** aus.
	2. Wählen Sie für **Choose the type of access you need** die Option **Full Dropbox...** aus.  
	3. Geben Sie einen Namen für die App ein.  

	![Dropbox-App erstellen Seite 1][10]

5. Auf der Seite mit den App-Einstellungen:

	1. Geben Sie in **OAuth 2** den Wert der **Umleitungs-URL** ein, die angezeigt wird, wenn Sie die Dropbox-API im Azure-Portal hinzufügen. Wählen Sie **Hinzufügen**.  
	2. Wählen Sie den Link **Anzeigen** aus, damit der **geheime App-Schlüssel** sichtbar wird:  

	![Dropbox-App erstellen Seite 2][11]

Nun können Sie die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** kopieren und in Ihre Dropbox-Konfiguration im Azure-Portal einfügen.

### Hinzufügen zusätzlicher Konfiguration in Logik-Apps
Wenn Sie diese API Ihren Logik-Apps hinzufügen, müssen Sie den Logik-Apps das Herstellen einer Verbindung mit Dropbox erlauben.

1. Melden Sie sich bei Ihrem Dropbox-Konto an.
2. Wählen Sie **Autorisieren** aus, um zu erlauben, dass Ihre Logik-Apps sich mit Ihrer Dropbox verbinden und diese nutzen. 

Nachdem Sie eine Verbindung hergestellt haben, geben Sie die Dropbox-Eigenschaften ein, z. B. Ordnerpfad oder Dateiname. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können dieselbe Dropbox-Verbindung in anderen Logik-Apps verwenden.

## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Datei erstellen    
Lädt eine Datei in Dropbox hoch. ```POST: /datasets/default/files```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderPath|string|Ja|query|(Keine) |Ordnerpfad zum Hochladen der Datei in Dropbox|
|name|string|Ja|query|(Keine) |Name der Datei, die in Dropbox erstellt werden soll|
|body|string(binary) |Ja|body|(Keine) |Inhalt der Datei, die in Dropbox hochgeladen werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Wenn eine Datei erstellt wird    
Löst einen Datenfluss aus, wenn in einem Dropbox-Ordner eine neue Datei erstellt wird. ```GET: /datasets/default/triggers/onnewfile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|(Keine) |Eindeutiger Bezeichner des Ordners in Dropbox|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei kopieren    
Kopiert eine Datei in Dropbox. ```POST: /datasets/default/copyFile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine) |URL zur Quelldatei|
|Ziel|string|Ja|query| (Keine)|Zieldateipfad in Dropbox, einschließlich Zieldateiname|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldatei, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei löschen    
Löscht eine Datei aus Dropbox. ```DELETE: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der aus Dropbox zu löschenden Datei|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Archiv in Ordner extrahieren    
Extrahiert eine Archivdatei in einen Ordner in Dropbox (Beispiel: ZIP). **```POST: /datasets/default/extractFolderV2```**

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Quelle|string|Ja|query|(Keine) |Pfad zur Archivdatei|
|Ziel|string|Ja|query|(Keine) |Pfad in Dropbox, in den der Archivinhalt extrahiert wird|
|overwrite|Boolescher Wert|no|query|(Keine) |Überschreibt die Zieldateien, falls auf „True“ festgelegt|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand der ID abrufen    
Ruft den Dateiinhalt anhand der ID aus Dropbox ab. ```GET: /datasets/default/files/{id}/content```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei in Dropbox|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateiinhalt anhand des Pfads abrufen    
Ruft den Dateiinhalt anhand des Pfads aus Dropbox ab. ```GET: /datasets/default/GetFileContentByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine) |Eindeutiger Pfad zur Datei in Dropbox|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand der ID abrufen    
Ruft Dateimetadaten aus Dropbox anhand der Datei-ID ab. ```GET: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path|(Keine) |Eindeutiger Bezeichner der Datei in Dropbox|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Dateimetadaten anhand des Pfads abrufen    
Ruft Dateimetadaten aus Dropbox anhand des Pfads ab. ```GET: /datasets/default/GetFileByPath```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|path|string|Ja|query|(Keine) |Eindeutiger Pfad zur Datei in Dropbox|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Datei aktualisieren    
Aktualisiert eine Datei in Dropbox. ```PUT: /datasets/default/files/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|id|string|Ja|path| (Keine)|Eindeutiger Bezeichner der Datei, die in Dropbox aktualisiert werden soll|
|body|string(binary) |Ja|body|(Keine) |Inhalt der Datei, die in Dropbox aktualisiert werden soll|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Wenn eine Datei geändert wird    
Löst einen Datenfluss aus, wenn in einem Dropbox-Ordner eine Datei geändert wird. ```GET: /datasets/default/triggers/onupdatedfile```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|folderId|string|Ja|query|(Keine) |Eindeutiger Bezeichner des Ordners in Dropbox|

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
Nach Hinzufügen der Dropbox-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md)


<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[8]: ./media/create-api-dropbox/dropbox-developer-site.png
[9]: ./media/create-api-dropbox/dropbox-create-app.png
[10]: ./media/create-api-dropbox/dropbox-create-app-page1.png
[11]: ./media/create-api-dropbox/dropbox-create-app-page2.png

<!---HONumber=AcomDC_0302_2016-->