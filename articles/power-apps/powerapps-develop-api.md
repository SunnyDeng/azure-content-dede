<properties
	pageTitle="Entwickeln von APIs für PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Bereitstellen benutzerdefinierter APIs für PowerApps"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="rajram"/>

# Entwickeln von APIs für PowerApps

Sie können Ihre eigene API erstellen oder entwickeln, die in PowerApps verwendet werden kann. Dazu müssen folgende Schritte ausgeführt werden:

- Erstellen und Bereitstellen der API
- Erstellen der [Swagger 2.0](http://swagger.io/)-API-Definition für die API

In diesem Thema werden diese Schritte aufgeführt.

## Schritt 1: Erstellen und Bereitstellen der API

APIs für PowerApps lassen sich auf die gleiche Weise wie andere APIs erstellen und bereitstellen. Sie können Ihre bevorzugte Programmiersprache und das gewünschte Framework auswählen. Sie können Ihre API zudem in einer beliebigen Umgebung hosten. Es empfiehlt sich, die API in der gleichen App Service-Umgebung von PowerApps wie die [API-App](https://azure.microsoft.com/services/app-service/api/) zu hosten.

In den folgenden Artikeln wird das Erstellen und Bereitstellen einer .NET-, Java- oder Node.js-API in der App Service-Umgebung erläutert:

- [Erstellen und Bereitstellen einer .NET-API-App in Azure App Service](../app-service-api-dotnet-get-started.md)
- [Erstellen und Bereitstellen einer Java-API-App in Azure App Service](../app-service-api-java-api-app.md)
- [Erstellen und Bereitstellen einer Node.js-API-App in Azure App Service](../app-service-api-nodejs-api-app.md)


## Schritt 2: Erstellen der Swagger 2.0-API-Definition für die API

Wenn Sie die Anweisungen in einem der unter *Schritt 1* aufgeführten Artikel befolgen, wird automatisch eine Swagger 2.0-API-Standarddefinition für Ihre API erstellt. Um die generierte Swagger 2.0-API-Definition für PowerApps zu optimieren, können Sie sie optional mit den folgenden Schemaerweiterungen anpassen.

Allgemeine Informationen zum Anpassen von Swagger 2.0-API-Definitionen finden Sie unter [Anpassen von mit Swashbuckle generierten API-Definitionen](../app-service-api-dotnet-swashbuckle-customize.md).

### Schemaerweiterungen
Neben den automatisch mit Swashbuckle generierten Swagger-Definitionen stehen beim Erstellen von APIs für PowerApps einige weitere Swagger-Erweiterungen zur Verfügung. Diese Erweiterungen werden in diesem Abschnitt aufgeführt und erläutert.

##### x-ms-summary
Eine Zeichenfolge, die die Anzeigenamen für Entitäten beschreibt, für die in der Swagger-Spezifikation kein Zusammenfassungsfeld definiert ist. **Parameter names** ist ein Beispiel.

##### x-ms-visibility
Dieser Wert beschreibt, ob die Entität im Logic Flow-Designer angezeigt wird. Folgende Werte sind verfügbar:

- „none“ (Standardwert)
- „advanced“
- „internal“: Diese Vorgänge werden im Logic Flow-Designer nicht angezeigt.

Wenn Vorgänge als „important“ gekennzeichnet sind, ist zu erwarten, dass sie auf dem Logic Flow-Client hervorgehoben werden.

##### x-ms-trigger
Definiert, ob der Vorgang im logischen Ablauf als Trigger verwendet werden kann. Folgende Optionen sind verfügbar:
	
- none (Standardwert): Der Vorgang kann nicht als Trigger verwendet werden.
- single: Der Vorgang kann auch als Trigger verwendet werden.
- batched: Der Vorgang kann als Trigger verwendet werden. Darüber hinaus antwortet der Vorgang mit einem JSON-„Array“ von Objekten, und im logischen Ablauf wird ein Trigger für jedes Element im Array ausgelöst.


##### x-ms-dynamic-values
Dies ist ein Hinweis für den Logic Flow-Designer, dass die API eine Liste mit dynamisch zulässigen Werten für den Parameter bereitstellt. Der Logic Flow-Designer kann einen Vorgang entsprechend der Definition durch den Wert dieses Felds aufrufen und die möglichen Werte aus dem Ergebnis extrahieren. Im Logic Flow-Designer können diese Werte dann als Optionen für den Endbenutzer angezeigt werden.

Der Wert ist ein Objekt, das die folgenden Eigenschaften enthält:
	
- operationId: Eine Zeichenfolge, die der operationId für den aufgerufenen Vorgang entspricht.
- parameters: Ein Objekt, mit dessen Eigenschaften die für den Vorgang erforderlichen Parameter definiert werden.
- value-collection: Eine Pfadzeichenfolge, die als Array von Objekten in der Antwortnutzlast ausgewertet wird.
- value-path: Eine Pfadzeichenfolge im Objekt innerhalb von „value-collection“, die auf den Wert für den Parameter verweist.
- value-title: Eine Pfadzeichenfolge im Objekt innerhalb von „value-collection“, die auf eine Beschreibung für den Wert verweist.


Beispiel:

```javascript
"/api/tables/{table}/items": {
  "post": {
    "operationId": "TableData_CreateItem",
    "summary": "Create an object in {Salesforce}",
    "parameters": [
      {
        "name": "table",
        "x-ms-summary": "Object Type",
        "x-ms-dynamic-values": {
          "operationId": "TableMetadata_ListTables",      // operation that needs to be invoked
          "parameters": { },                              // parameters for the above operation, if any
          "value-collection": "values",                   // field that contains the collection
          "value-path": "Name",                           // field that contains the value
          "value-title": "DisplayName"                    // field that contains a display name for the value
      }
      // ...
    ]
    // ...
  }
  // ...
}
```

Im Beispiel oben definiert die Swagger-Definition den Vorgang _TableData\_CreateItem_, der ein neues Objekt in Salesforce erstellt.

Salesforce verfügt über zahlreiche integrierte Objekte. _x-ms-dynamic-values_ wird hier verwendet, damit im Designer die Liste der integrierten Salesforce-Objekte ermittelt werden kann. Dies erfolgt durch Aufrufen von _TableMetadata\_ListTables_.

##### x-ms-dynamic-schema
Dies ist ein Hinweis für den Logic Flow-Designer, dass das Schema für den Parameter (oder die Antwort) dynamisch ist. Der Designer kann einen Vorgang entsprechend der Definition durch den Wert dieses Felds aufrufen und das Schema dynamisch ermitteln. Dann kann eine entsprechende Benutzeroberfläche angezeigt werden, in der Eingaben des Benutzers erfolgen oder verfügbare Felder angezeigt werden können.

Beispiel:

```javascript
{
  "name": "item",
  "in": "body",
  "required": true,
  "x-ms-dynamic-schema": {
    "operationId": "Metadata_GetTableSchema",
    "parameters": {
      "tablename": "{table}"              // the value that the user has selected from the above parameter
    },
    "value-path": "Schema"                // the field that contains the JSON schema
  }
},
```

Dies ist nützlich in Szenarios, in denen die Eingaben für einen Vorgang dynamisch vorgenommen werden. Betrachten Sie beispielsweise das SQL-Beispiel. Das Schema jeder Tabelle ist unterschiedlich. Wenn ein Benutzer eine bestimmte Tabelle auswählt, muss der Logic Flow-Designer daher die Struktur der Tabelle ermitteln, sodass die Spaltennamen angezeigt werden können. Wenn die Swagger-Definition in diesem Kontext über _x-ms-dynamic-schema_ verfügt, wird der entsprechende Vorgang zum Abrufen des Schemas aufgerufen.

<!---HONumber=AcomDC_1203_2015-->