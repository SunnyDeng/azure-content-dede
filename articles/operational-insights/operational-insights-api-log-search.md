<properties
   pageTitle="Operational Insights – Protokollsuch-API"
   description="Bietet eine Übersicht über die Operational Insights Search-API und enthält Beispiele zur Verwendung."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/10/2015"
   ms.author="banders" />



# Operational Insights Search-API – Überblick und Referenz

Dieses Handbuch stellt ein Lernprogramm dar, in dem beschrieben wird, wie Sie die Protokollsuch-API von Operational Insights nutzen können. Außerdem werden Beispiele zur Verwendung der Befehle genannt.

## Übersicht über die Protokollsuch-API

Die Protokollsuch-API von Operational Insights ist RESTful. Der Zugriff darauf erfolgt über die Azure-Ressourcen-Manager-API. In diesem Dokument finden Sie Beispiele, in denen über [ARMClient](https://github.com/projectkudu/ARMClient) auf die API zugegriffen wird. Dies ist ein Open Source-Befehlszeilentool, das den Aufruf der Azure-Ressourcen-Manager-API vereinfacht. Die Verwendung von ARMClient und PowerShell ist eine von vielen Möglichkeiten, auf die Protokollsuch-API von Operational Insights zuzugreifen. Mit diesen Tools können Sie die RESTful-API von Azure-Ressourcen-Manager für Aufrufe an Operational Insights-Arbeitsbereiche nutzen und darin Suchbefehle ausführen. Die API wird Ihnen Suchergebnisse im JSON-Format ausgeben, und Sie können die Suchergebnisse programmgesteuert auf viele verschiedene Arten verwenden.

Der Azure-Ressourcen-Manager kann über eine [Library für .NET](https://msdn.microsoft.com/library/azure/dn910477.aspx) oder eine [REST-API](https://msdn.microsoft.com/library/azure/mt163658.aspx) verwendet werden. Überprüfen Sie die verlinkten Webseiten, um mehr zu erfahren.

## Lernprogramm zur Search-API

### Verwendung von ARMClient

1. Installieren Sie [Chocolatey](https://chocolatey.org/), ein Open Source Computer-Paket-Manager für Windows.
2. Öffnen Sie ein PowerShell-Fenster als Administrator, und führen Sie den folgenden Befehl aus:

    ```
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
    ```

2. Installieren Sie ARMClient, indem Sie den folgenden Befehl ausführen:

    ```
    choco install armclient
    ```

### Eine einfache Suche mit ARMClient

1. Melden Sie sich in Ihrem Microsoft- oder Organisations-ID-Konto an:

    ```
    armclient login
```
  Eine erfolgreiche Anmeldung listet alle Abonnements auf, die mit dem angegebenen Konto verknüpft sind. Beispiel:

    ```
    PS C:\Users\SampleUserName> armclient login
    Welcome YourEmail@ORG.com (Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz)
    User: YourEmail@ORG.com, Tenant: zzzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz (Example org)
    There are 3 subscriptions
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 1)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 2)
    Subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx (Example Name 3)
    ```

2. Operations Management Suite-Arbeitsbereiche aufrufen. Beispiel:

    ```
    armclient get /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces?api-version=2015-03-20
    ```

    Ein erfolgreicher Get-Aufruf gibt alle Arbeitsbereiche aus, die mit dem Abonnement verknüpft sind. Beispiel:

    ```
    {
    "value": [
    {
      "properties": {
    "source": "External",
    "customerId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "portalUrl": "https://eus.login.mms.microsoft.com/SignIn.aspx?returnUrl=https%3a%2f%2feus.mms.microsoft.com%2fMain.aspx%3fcid%xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/{WORKSPACE NAME}",
      "name": "{WORKSPACE NAME}",
      "type": "Microsoft.OperationalInsights/workspaces",
      "location": "East US"
       ]
    }
    ```
3. Erstellen Sie Ihre Search-Variable. Beispiel:

    ```
    $mySearch = "{ 'top':150, 'query':'Error'}";
    ```
4. Führen Sie mit der neuen Search-Variable eine Suche durch. Beispiel:

    ```
    armclient post /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{WORKSPACE NAME}/search?api-version=2015-03-20 $mySearch
    ```

## Referenzbeispiele für die Search-API
Die folgenden Beispiele zeigen Ihnen, wie Sie die Search-API verwenden können.

### Search – Aktion/Lesen

**Beispiel-URL:**

```
	/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search?api-version=2015-03-20
```

**Anforderung:**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```
Die folgende Tabelle beschreibt die verfügbaren Eigenschaften.

|**Eigenschaft**|**Beschreibung**|
|---|---|
|top|Die maximale Anzahl der zurückzugebenden Ergebnisse.|
|highlight|Enthält Pre- und Post-Parameter, wird in der Regel für die Hervorhebung von übereinstimmenden Feldern verwendet.|
|pre|Steht der angegebenen Zeichenfolge in den entsprechenden Feldern als Präfix voran.|
|post|Fügt die angegebene Zeichenfolge an die entsprechenden Felder an.|
|query|Die Suchabfrage zum Erfassen und Zurückgeben von Ergebnissen.|
|start|Der Anfang des Zeitfensters, in dem die Ergebnisse gesucht werden sollen.|
|end|Das Ende des Zeitfensters, in dem die Ergebnisse gesucht werden sollen.|


**Antwort:**

```
	{
	  "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	  "__metadata" : {
	    "resultType" : "raw",
	    "total" : 1455,
	    "top" : 150,
	    "StartTime" : "2015-02-11T21:09:07.0345815Z",
	    "Status" : "Successful",
	    "LastUpdated" : "2015-02-11T21:09:07.331463Z",
	    "CoreResponses" : [],
	    "sort" : [{
	      "name" : "TimeGenerated",
	      "order" : "desc"
	    }],
	    "requestTime" : 450
	  },
	  "value": [{
	    "SourceSystem" : "OpsManager",
	    "TimeGenerated" : "2015-02-07T14:07:33Z",
	    "Source" : "SideBySide",
	    "EventLog" : "Application",
	    "Computer" : "BAMBAM",
	    "EventCategory" : 0,
	    "EventLevel" : 1,
	    "EventLevelName" : "Error",
	    "UserName" : "N/A",
	    "EventID" : 78,
	    "MG" : "00000000-0000-0000-0000-000000000001",
	    "TimeCollected" : "2015-02-07T14:10:04.69Z",
	    "ManagementGroupName" : "AOI-5bf9a37f-e841-462b-80d2-1d19cd97dc40",
	    "id" : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	    "Type" : "Event",
	    "__metadata" : {
	      "Type" : "Event",
	      "TimeGenerated" : "2015-02-07T14:07:33Z",
	      "highlighting" : {
	      "EventLevelName" : ["{[hl]}Error{[/hl]}"]
	    }
	  }]
	],
	        "start" : "2015-02-04T21:03:29.231Z",
	        "end" : "2015-02-11T21:03:29.231Z"
	      }
	    }
	  }]
	}
```

### Search/{ID} – Aktion/Lesen

**Fordern Sie den Inhalt einer gespeicherten Suche an:**

```
	armclient post /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/search/{SearchId}?api-version=2015-03-20
```

>[AZURE.NOTE]Wenn die Suche den Status "Ausstehend" zurückgibt, können die aktualisierten Ergebnisse über diese API aufgerufen werden. Nach 6 Minuten wird das Ergebnis der Suche aus dem Cache gelöscht, und es wird "Http Fehlend" zurückgegeben. Wenn die anfängliche Suchanforderung sofort den Status "Erfolgreich" zurückgibt, wird sie nicht zum Cache hinzugefügt. Dadurch gibt diese API bei einer Abfrage "Http Fehlend" zurück. Der Inhalt eines "Http 200"-Ergebnisses wird im gleichen Format wie die ursprüngliche Suchanforderung erstellt, nur mit aktualisierten Werten.

### Gespeicherte Suchvorgänge - nur REST

**Fordern Sie die Liste der gespeicherten Suchvorgänge an:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/savedSearches?api-version=2015-03-20
```

Unterstützte Methoden: GET PUT DELETE

Unterstützte Erfassungsmethoden: GET

Die folgende Tabelle beschreibt die verfügbaren Eigenschaften.

|Eigenschaft|Beschreibung|
|---|---|
|ID|Der eindeutige Bezeichner.|
|ETag|**Erforderlich für Patch**. Wird bei jedem Schreibvorgang vom Server aktualisiert. Zum Aktualisieren muss der Wert mit dem aktuellen gespeicherten Wert übereinstimmen oder "*" lauten. 409 für alte/ungültige Werte zurückgegeben.|
|properties.query|**Erforderlich**. Die Suchabfrage|
|properties.displayName|**Erforderlich**. Der benutzerdefinierte Anzeigename der Abfrage. Bei Modellierung als Azure-Ressource wäre dies ein Tag.|
|properties.category|**Erforderlich**. Die benutzerdefinierte Kategorie der Abfrage. Bei Modellierung als Azure-Ressource wäre dies ein Tag.|

>[AZURE.NOTE]Beim Abrufen gespeicherter Suchvorgänge in einem Arbeitsbereich gibt die Operational Insights Search-API zurzeit vom Benutzer erstellte gespeicherte Suchvorgänge zurück. Momentan gibt die API keine von Lösungen bereitgestellten gespeicherten Suchvorgänge zurück. Diese Funktionalität wird zu einem späteren Zeitpunkt hinzugefügt.

### Löschen gespeicherter Suchvorgänge

**Anforderung:**

```
	armclient delete /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20
```

### Aktualisieren gespeicherter Suchvorgänge

 **Anforderung:**

```
	$savedSearchParametersJson = "{'etag': 'W/`"datetime\'2015-04-16T23%3A35%3A35.3182423Z\'`"', 'properties': { 'Category': 'myCategory', 'DisplayName':'myDisplayName', 'Query':'* | measure Count() by Source', 'Version':'1'  }"
	armclient put /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/savedSearches/thisIsMyId?api-version=2015-03-20 $savedSearchParametersJson
```

### Metadaten: nur JSON

Dies ist eine Möglichkeit, die Felder für alle Protokolltypen für die Daten anzuzeigen, die im Arbeitsbereich gesammelt wurden. Wenn Sie beispielsweise wissen möchten, ob der Ereignistyp ein Feld mit dem Namen "Computer" aufweist, können Sie dies unter anderem hiermit herausfinden.

**Felder anfordern:**

```
	armclient get /subscriptions/{SubId}/resourceGroups/{ResourceGroupId}/providers/Microsoft.OperationalInsights/workspaces/{WorkspaceName}/schema?api-version=2015-03-20
```

**Antwort:**

```
	{
	  "__metadata" : {
	    "schema" : {
	      "name" : "Example Name",
	      "version" : 2
	    },
	    "resultType" : "schema",
	    "requestTime" : 35
	  },
	  "value" : [{
	      "name" : "MG",
	      "displayName" : "MG",
	      "type" : "Guid",
	      "facetable" : true,
	      "display" : false,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Capacity_SMBUtilizationByHost", "Capacity_ArrayUtilization", "Capacity_SMBShareUtilization", "SQLAssessmentRecommendation", "Event", "ConfigurationChange", "ConfigurationAlert", "ADAssessmentRecommendation", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }, {
	      "name" : "ManagementGroupName",
	      "displayName" : "ManagementGroupName",
	      "type" : "String",
	      "facetable" : true,
	      "display" : true,
	      "ownerType" : ["PerfHourly", "ProtectionStatus", "Event", "ConfigurationChange", "ConfigurationAlert", "W3CIISLog", "AlertHistory", "Recommendation", "Alert", "SecurityEvent", "UpdateAgent", "RequiredUpdate", "ConfigurationObject", "ConfigurationObjectProperty"]
	    }
	  ]
	}
```

Die folgende Tabelle beschreibt die verfügbaren Eigenschaften.

|**Eigenschaft**|**Beschreibung**|
|---|---|
|Name|Feldname.|
|displayName|Der Anzeigename des Felds.|
|type|Der Typ des Feldwerts.|
|facetable|Kombination der aktuellen Eigenschaften "indexed", "stored" und "facet".|
|display|Aktuelle display-Eigenschaft. "True", wenn das Feld in Search angezeigt wird.|
|ownerType|Nur Typen, die zu eingebundenen IP-Adressen gehören.|


## Optionale Parameter
Im Folgenden werden die verfügbaren optionalen Parameter beschrieben.

### Hervorhebungen

Der Parameter "Highlight" ist ein optionaler Parameter, den Sie verwenden können, damit das Suchsubsystem einen Satz von Markern in die Antwort einbezieht.

Diese Marker stehen für den Beginn und das Ende von hervorgehobenem Text, der den in Ihrer Suchabfrage angegebenen Begriffen entspricht.
Sie können die Start- und Endmarker angeben, die bei der Suche zum Einschließen des markierten Begriffs verwendet werden.

**Beispiel für eine Suchabfrage**

```
	$savedSearchParametersJson =
	{
	  "top":150,
	  "highlight":{
	    "pre":"{[hl]}",
	    "post":"{[/hl]}"
	  },
	  "query":"*",
	  "start":"2015-02-04T21:03:29.231Z",
	  "end":"2015-02-11T21:03:29.231Z"
	}
	armclient post /subscriptions/{Subscription ID}/resourceGroups/OI-Default-East-US/providers/Microsoft.OperationalInsights/workspaces/{Workspace ID}/search?api-version=2015-03-20 $searchParametersJson
```

**Beispielergebnis:**

```
	{
	    "TimeGenerated":
	    "2015-05-18T23:55:59Z", "Source":
	    "EventLog": "Application",
	    "Computer": "smokedturkey.net",
	    "EventCategory": 0,
	    "EventLevel":1,
	    "EventLevelName":
	    "Error"
	    "Manager ", "__metadata":
	    {
	        "Type": "Event",
	        "TimeGenerated": "2015-05-18T23:55:59Z",
	        "highlighting": {
	            "EventLevelName":
	            ["{[hl]}Error{[/hl]}"]
	        }
	    }
	}
```

Beachten Sie, dass das oben genannte Ergebnis eine Fehlermeldung enthält, die mit dem Präfix versehen und angefügt wurde.

<!---HONumber=Sept15_HO3-->