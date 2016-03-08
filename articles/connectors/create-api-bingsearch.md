<properties
	pageTitle="Hinzufügen der Bing-Such-API zu PowerApps oder Logik-Apps | Microsoft Azure"
	description="Übersicht über die Bing-Such-API und REST-API-Parameter"
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

# Erste Schritte mit der Bing-Such-API 
Verbinden Sie sich mit der Bing-Suche, um Nachrichten, Videos und mehr zu suchen.

Die Bing-Such-API kann von PowerApps und Logik-Apps verwendet werden.

Die Bing-Suche ermöglicht Folgendes:

- Erstellen eines Geschäftsworkflows basierend auf den Daten, die aus einer Suche abgerufen werden. 
- Verwenden von Aktionen, um Bilder, Nachrichten und mehr zu suchen. Diese Aktionen erhalten eine Antwort und stellen anschließend die Ausgabe anderen Aktionen zur Verfügung. Sie können z. B. ein Video suchen und dann Twitter verwenden, um das Video in einem Twitter-Feed zu posten.
- Hinzufügen der Bing-Such-API zu PowerApps Enterprise. Die Benutzer können diese API anschließend in ihren Apps verwenden. 

Informationen zum Hinzufügen einer API in PowerApps Enterprise finden Sie unter [Registrieren einer API in PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Informationen zum Hinzufügen eines Vorgangs in Logik-Apps finden Sie unter [Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Trigger und Aktionen
Die Bing-Suche bietet die folgenden Aktionen. Es gibt keine Trigger.

Trigger | Aktionen
--- | ---
Keine | <ul><li>Web durchsuchen</li><li>Videos suchen</li><li>Bilder suchen</li><li>Nachrichten suchen</li><li>Verwandte Informationen suchen</li><li>Schreibweisen suchen</li><li>Alles suchen</li></ul>

Alle APIs unterstützen Daten im JSON- und XML-Format.

## Hinzufügen zusätzlicher Konfiguration
Wenn Sie die Bing-Suche PowerApps Enterprise hinzufügen, werden Sie aufgefordert, einen Kontoschlüssel anzugeben. Wenn Sie keinen Schlüssel für die Bing-Suche haben, nutzen Sie das kostenlose [Angebot für die Bing-Suche](https://datamarket.azure.com/dataset/bing/search), um einen Schlüssel zu erhalten.


## Swagger-REST-API – Referenz
Gilt für Version: 1.0.

### Web durchsuchen 
Ruft Websites aus einer Bing-Suche ab. ```GET: /Web```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query|(Keine) |Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query| (Keine)|Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(Keine) |Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query| (Keine)|Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query| (Keine)|Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|
|webFileType|string|no|query|(Keine) |Dateityp zum Eingrenzen der Suche (Beispiel: DOC)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Videos suchen 
Ruft Videos aus einer Bing-Suche ab. ```GET: /Video```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query| (Keine)|Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query|(Keine) |Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(Keine) |Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query|(Keine) |Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query|(Keine) |Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|
|videoFilters|string|no|query|(Keine) |Filtern Sie die Suche basierend auf Größe, Aspekt, Farbe, Format, Gesicht und einer beliebigen Kombination dieser Angaben. Gültige Werte: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <p>Beispiel: Duration:Short+Resolution:High</p>|
|videoSortBy|string|no|query|(Keine) |Sortierreihenfolge für Ergebnisse. Gültige Werte: <ul><li>Date</li><li>Relevance</li></ul> <p>Die Sortierung nach Datum erfolgt absteigend.</p>|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Bilder suchen    
Ruft Bilder aus einer Bing-Suche ab. ```GET: /Image```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query|(Keine) |Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query|(Keine) |Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(Keine) |Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query| (Keine)|Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query|(Keine) |Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|
|imageFilters|string|no|query|(Keine) |Filtern Sie die Suche basierend auf Größe, Aspekt, Farbe, Format, Gesicht und einer beliebigen Kombination dieser Angaben. Gültige Werte: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><p>Beispiel: Size:Small+Aspect:Square</p>|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Nachrichten suchen    
Ruft Ergebnisse zu Nachrichten aus einer Bing-Suche ab. ```GET: /News```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query|(Keine) |Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query| (Keine)|Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(Keine) |Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query|(Keine) |Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query|(Keine) |Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|
|newsSortBy|string|no|query| (Keine)|Sortierreihenfolge für Ergebnisse. Gültige Werte: <ul><li>Date</li><li>Relevance</li></ul> <p>Die Sortierung nach Datum erfolgt absteigend.</p>|
|newsCategory|string|no|query| |Kategorie von Nachrichten zum Eingrenzen der Suche (Beispiel: rt\_Business)|
|newsLocationOverride|string|no|query|(Keine) |Erkennung des Standorts durch Bing außer Kraft setzen. Dieser Parameter gilt nur für den Markt „de-DE“. Das Format für die Eingabe ist: US./<state /> (Beispiel: US. WA)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Schreibweisen suchen    
Ruft Vorschläge für Schreibweisen ab. ```GET: /SpellingSuggestions```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query| (Keine)|Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query|(Keine) |Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query| (Keine)|Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query| (Keine)|Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query|(Keine) |Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query|(Keine) |Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Verwandte Informationen suchen    
Ruft verwandte Suchergebnisse aus einer Bing-Suche ab. ```GET: /RelatedSearch```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query|(Keine) |Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query| (Keine)|Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(Keine) |Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query|(Keine) |Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query| (Keine)|Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


### Alles suchen    
Ruft alle Websites, Videos, Bilder usw. aus einer Bing-Suche ab. ```GET: /CompositeSearch```

| Name| Datentyp|Erforderlich|Enthalten in|Standardwert|Beschreibung|
| ---|---|---|---|---|---|
|query|string|Ja|query|(Keine) |Zu suchender Text (Beispiel: Xbox)|
|maxResult|integer|no|query|(Keine) |Maximale Anzahl der zurückzugebenden Ergebnisse|
|startOffset|integer|no|query|(Keine) |Anzahl zu überspringender Ergebnisse|
|adultContent|string|no|query|(Keine) |Filter für nicht jugendfreie Inhalte. Gültige Werte: <ul><li>Off</li><li>Moderate</li><li>Strict</li></ul>|
|market|string|no|query|(Keine) |Markt oder Region zum Eingrenzen der Suche (Beispiel: de-DE)|
|longitude|number|no|query|(Keine) |Längengrad (Ost-West-Koordinate) zum Eingrenzen der Suche (Beispiel: 47,603450)|
|latitude|number|no|query|(Keine) |Breitengrad (Nord-Süd-Koordinate) zum Eingrenzen der Suche (Beispiel: -122,329696)|
|webFileType|string|no|query|(Keine) |Dateityp zum Eingrenzen der Suche (Beispiel: DOC)|
|videoFilters|string|no|query|(Keine) |Filtern Sie die Suche basierend auf Größe, Aspekt, Farbe, Format, Gesicht und einer beliebigen Kombination dieser Angaben. Gültige Werte: <ul><li>Duration:Short</li><li>Duration:Medium</li><li>Duration:Long</li><li>Aspect:Standard</li><li>Aspect:Widescreen</li><li>Resolution:Low</li><li>Resolution:Medium</li><li>Resolution:High</li></ul> <p>Beispiel: Duration:Short+Resolution:High</p>|
|videoSortBy|string|no|query|(Keine) |Sortierreihenfolge für Ergebnisse. Gültige Werte: <ul><li>Date</li><li>Relevance</li></ul> <p>Die Sortierung nach Datum erfolgt absteigend.</p>|
|imageFilters|string|no|query|(Keine) |Filtern Sie die Suche basierend auf Größe, Aspekt, Farbe, Format, Gesicht und einer beliebigen Kombination dieser Angaben. Gültige Werte: <ul><li>Size:Small</li><li>Size:Medium</li><li>Size:Large</li><li>Size:Width:[Width]</li><li>Size:Height:[Height]</li><li>Aspect:Square</li><li>Aspect:Wide</li><li>Aspect:Tall</li><li>Color:Color</li><li>Color:Monochrome</li><li>Style:Photo</li><li>Style:Graphics</li><li>Face:Face</li><li>Face:Portrait</li><li>Face:Other</li></ul><p>Beispiel: Size:Small+Aspect:Square</p>|
|newsSortBy|string|no|query|(Keine) |Sortierreihenfolge für Ergebnisse. Gültige Werte: <ul><li>Date</li><li>Relevance</li></ul> <p>Die Sortierung nach Datum erfolgt absteigend.</p>|
|newsCategory|string|no|query|(Keine) |Kategorie von Nachrichten zum Eingrenzen der Suche (Beispiel: rt\_Business)|
|newsLocationOverride|string|no|query|(Keine) |Erkennung des Standorts durch Bing außer Kraft setzen. Dieser Parameter gilt nur für den Markt „de-DE“. Das Format für die Eingabe ist: US./<state /> (Beispiel: US. WA)|

#### Antwort
|Name|Beschreibung|
|---|---|
|200|OK|
|die Standardeinstellung|Fehler beim Vorgang.|


## Objektdefinitionen

#### WebResultModel: Bing-Ergebnisse für Websuche

|Eigenschaftenname | Datentyp | Erforderlich |
|---|---|---|
|Titel|string|no|
|Beschreibung|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|FullUrl|string|no|

#### VideoResultModel: Bing-Suchergebnisse für Videos

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Titel|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|MediaUrl|string|no|
|Laufzeit|integer|no|
|Miniaturansicht|nicht definiert|no|

#### ThumbnailModel: Miniaturansichteigenschaften des Multimediaelements

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|MediaUrl|string|no|
|ContentType|string|no|
|Breite|integer|no|
|Höhe|integer|no|
|FileSize|integer|no|

#### ImageResultModel: Bing-Suchergebnisse für Bilder

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Titel|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|MediaUrl|string|no|
|SourceUrl|string|no|
|Miniaturansicht|nicht definiert|no|

#### NewsResultModel: Bing-Suchergebnisse für Nachrichten

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Titel|string|no|
|Beschreibung|string|no|
|DisplayUrl|string|no|
|ID|string|no|
|Quelle|string|no|
|Date|string|no|

#### SpellResultModel: Bing-Suchergebnisse für Schreibweisen

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|ID|string|no|
|Wert|string|no|

#### RelatedSearchResultModel: Bing-Suchergebnisse für verwandte Informationen

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|Titel|string|no|
|ID|string|no|
|BingUrl|string|no|

#### CompositeSearchResultModel: Zusammengesetzte Bing-Suchergebnisse

|Eigenschaftenname | Datentyp |Erforderlich |
|---|---|---|
|WebResultsTotal|integer|no|
|ImageResultsTotal|integer|no|
|VideoResultsTotal|integer|no|
|NewsResultsTotal|integer|
|SpellSuggestionsTotal|integer|no|
|WebResults|array|no|
|ImageResults|array|no|
|VideoResults|array|no|
|NewsResults|array|no|
|SpellSuggestionResults|array|no|
|RelatedSearchResults|array|no|

## Nächste Schritte

Nach Hinzufügen der Bing-Such-API zu PowerApps Enterprise [erteilen Sie den Benutzern Berechtigungen](../power-apps/powerapps-manage-api-connection-user-access.md) zum Verwenden der API in ihren Apps.

[Erstellen einer Logik-App](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->