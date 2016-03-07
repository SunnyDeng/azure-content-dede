<properties
pageTitle="Hinzufügen der Excel-API zu PowerApps Enterprise | Microsoft Azure"
description="Übersicht über die Excel-API und REST-API-Parameter"
services=""	
documentationCenter="" 	
authors="msftman"	
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
ms.author="deonhe"/>

# Erste Schritte mit der Excel-API

Stelen Sie eine Verbindung mit Excel her, um eine Zeile einzufügen, eine Zeile zu löschen und mehr.

Die Excel-API kann in PowerApps Enterprise verwendet werden.

Mit Excel können Sie folgende Aktionen ausführen:

- Fügen Sie PowerApps Enterprise die Excel-API hinzu. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Excel bietet die folgenden Aktionen. Es gibt keine Trigger.

|Trigger|Actions|
|--- | ---|
|Keine | <ul><li>Zeilen abrufen</li><li>Zeile einfügen</li><li>Zeile löschen</li><li>Zeile abrufen</li><li>Tabellen abrufen</li><li>Zeile aktualisieren</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Swagger-REST-API – Referenz
#### Diese Dokumentation gilt für Version 1.0.


### Fügt eine neue Zeile in einer Excel-Tabelle ein.
**```POST: /datasets/{dataset}/tables/{table}/items```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Excel-Dateiname|
|Tabelle|string|Ja|path|(Keine)|Name der Excel-Tabelle|
|item| |Ja|body|(Keine)|In der angegebenen Excel-Tabelle einzufügende Zeile|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Ruft eine einzelne Zeile aus einer Excel-Tabelle ab
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Excel-Dateiname|
|Tabelle|string|Ja|path|(Keine)|Name der Excel-Tabelle|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der abzurufenden Zeile|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Löscht eine einzelne Zeile aus einer Excel-Tabelle
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Excel-Dateiname|
|Tabelle|string|Ja|path|(Keine)|Name der Excel-Tabelle|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der zu löschenden Zeile|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



### Aktualisiert eine vorhandene Zeile in einer Excel-Tabelle
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```**



| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|Dataset|string|Ja|path|(Keine)|Excel-Dateiname|
|Tabelle|string|Ja|path|(Keine)|Name der Excel-Tabelle|
|id|string|Ja|path|(Keine)|Eindeutiger Bezeichner der zu aktualisierenden Zeile|
|item| |Ja|body|(Keine)|Zeile mit aktualisierten Werten|


### Antwort

|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|
------



## Objektdefinition

#### DataSetsMetadata

| Name | Datentyp | Erforderlich|
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

#### DataSetsList

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|

#### DataSet

| Name | Datentyp |Erforderlich|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Tabelle

| Name | Datentyp |Erforderlich|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Item

| Name | Datentyp |Erforderlich|
|---|---|---|
|ItemInternalId|string|no|

#### TablesList

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|

#### ItemsList

| Name | Datentyp |Erforderlich|
|---|---|---|
|value|array|no|


## Nächste Schritte
[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md) [Erstellen einer Power-App](../power-apps/powerapps-get-started-azure-portal.md)

<!---HONumber=AcomDC_0224_2016-->