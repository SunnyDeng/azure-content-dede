<properties
	pageTitle="Hinzufügen der SQL Azure-API zu Ihren Logik-Apps | Microsoft Azure"
	description="Übersicht über die SQL Azure-API und REST-API-Parameter"
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


# Erste Schritte mit der SQL Azure-API
Stellen Sie eine Verbindung mit SQL Azure her, um Tabellen und Zeilen zu verwalten, z. B. Einfügen von Zeilen, Abrufen von Tabellen usw.

Die SQL Azure-API kann in Logik-Apps verwendet werden.

>[AZURE.NOTE] Diese Version des Artikels gilt für die Schemaversion 2015-08-01-preview für Logik-Apps. Um die Schemaversion 2014-12-01-preview aufzurufen, klicken Sie auf [SQL-Connector](../app-service-logic/app-service-logic-connector-sql.md).

Mit SQL können Sie folgende Aktionen ausführen:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die über SQL Azure abgerufen werden. 
- Verwenden Sie Aktionen zum Abrufen einer Zeile, Einfügen einer Zeile usw. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Sie können z. B. eine Datenzeile aus SQL Azure abrufen und dann die Daten in Excel hinzufügen. 

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Trigger und Aktionen
SQL bietet die folgenden Aktionen. Es gibt keine Trigger.

Trigger | Aktionen
--- | ---
Keine | <ul><li>Zeile abrufen</li><li>Zeilen abrufen</li><li>Zeile einfügen</li><li>Zeile löschen</li><li>Tabellen abrufen</li><li>Zeile aktualisieren</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Herstellen der SQL-Verbindung
Wenn Sie diese API Ihren Logik-Apps hinzufügen, geben Sie die folgenden Werte ein:

|Eigenschaft| Erforderlich|Beschreibung|
| ---|---|---|
|SQL-Verbindungszeichenfolge|Ja|Geben Sie Ihre SQL Azure-Verbindungszeichenfolge ein.|

Nachdem Sie die Verbindung hergestellt haben, geben Sie die SQL-Eigenschaften ein, z. B. den Tabellennamen. In der **REST-API-Referenz** in diesem Thema werden diese Eigenschaften beschrieben.

>[AZURE.TIP] Sie können diese Verbindung in anderen Logik-Apps verwenden.

## REST-API-Referenz

### Zeile abrufen 
Ruft eine einzelne Zeile aus einer SQL-Tabelle ab. ```GET: /datasets/default/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Name der SQL-Tabelle|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der abzurufenden Zeile|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeilen abrufen 
Ruft Zeilen aus einer SQL-Tabelle ab. ```GET: /datasets/default/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Name der SQL-Tabelle|
|$skip|integer|no|query|(Keine)|Anzahl der zu überspringenden Einträge (Standardeinstellung = 0)|
|$top|integer|no|query|(Keine)|Maximale Anzahl abzurufender Einträge (Standardeinstellung = 256)|
|$filter|string|no|query|(Keine)|Eine ODATA-Filterabfrage zum Einschränken der Anzahl der Einträge|
|$orderby|string|no|query|(Keine)|Eine ODATA-orderBy-Abfrage zum Angeben der Reihenfolge von Einträgen|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile einfügen 
Fügt eine neue Zeile in einer SQL-Tabelle ein. ```POST: /datasets/default/tables/{table}/items```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Name der SQL-Tabelle|
|item|ItemInternalId: Zeichenfolge|Ja|body|(Keine)|In der angegebenen SQL-Tabelle einzufügende Zeile|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile löschen 
Löscht eine einzelne Zeile aus einer SQL-Tabelle. ```DELETE: /datasets/default/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Name der SQL-Tabelle|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der zu löschenden Zeile|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Tabellen abrufen 
Ruft Tabellen aus einer SQL-Datenbank ab. ```GET: /datasets/default/tables```

Es gibt keine Parameter für diesen Aufruf.

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Zeile aktualisieren 
Aktualisiert eine vorhandene Zeile in einer SQL-Tabelle. ```PATCH: /datasets/default/tables/{table}/items/{id}```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Tabelle|string|Ja|path|(Keine)|Name der SQL-Tabelle|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der zu aktualisierenden Zeile|
|item|ItemInternalId: Zeichenfolge|Ja|body|(Keine)|Zeile mit aktualisierten Werten|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|

## Objektdefinitionen

#### DataSetsMetadata

|Eigenschaftenname | Datentyp | Erforderlich |
|---|---|---|
|tabular|nicht definiert|no|
|Blob|nicht definiert|no|

#### TabularDataSetsMetadata

|Eigenschaftenname | Datentyp | Erforderlich |
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Quelle|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|nicht definiert|no|

#### DataSetsList

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|value|array|no|

#### DataSet

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Tabelle

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Item

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|ItemInternalId|string|no|

#### ItemsList

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|value|array|no|

#### TablesList

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|value|array|no|


## Nächste Schritte

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->