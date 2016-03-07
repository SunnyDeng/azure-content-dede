<properties
	pageTitle="Azure Search-Verwaltungs-REST-API Version 2015-02-28 | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Azure Search-Verwaltungs-REST-API: Version 2015-02-28"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="02/04/2016"
	ms.author="heidist" />

# Management-API: Version 2015-02-28

Azure Search ist ein in Microsoft Azure gehosteter Cloudsuchdienst. Das vorliegende Dokument beschreibt die Version *2015-02-28** der Azure Search-Verwaltungs-REST-API. Sie wurde seitdem durch neuere Versionen ersetzt. Die neuesten Versionen finden Sie unter [Azure Search-Verwaltungs-REST-API 2015-08-19](https://msdn.microsoft.com/library/dn832684.aspx) auf MSDN.

##Dienstverwaltungsvorgänge

Die Verwaltungs-REST-API des Diensts Azure Search bietet auf die meisten Funktionen des Portals programmgesteuerten Zugriff und ermöglicht Administratoren somit die Automatisierung der folgenden Vorgänge:

- Erstellen oder Löschen eines Azure Search-Diensts
- Erstellen, neu Generieren oder Abrufen von `api-keys` für die Automatisierung regulärer Änderungen an den Admin-Schlüsseln für die Authentifizierung von Datensuchvorgängen 
- Anpassen der Skalierung eines Azure Search-Diensts in Reaktion auf Änderungen am Abfragevolumen oder den Speicheranforderungen

Für die vollständige programmgesteuerte Verwaltung des Diensts benötigen Sie zwei APIs: die Verwaltungs-REST-API von Azure Search sowie die allgemeine [Azure-Ressourcen-Manager-REST-API](https://msdn.microsoft.com/library/azure/dn790568.aspx). Über die Ressourcen-Manager-API werden allgemeine, dienstunspezifische Operationen wie die Abfrage von Abonnementdaten, die Auflistung geografischer Standorte und dergleichen mehr ausgeführt. Wenn Sie innerhalb Ihres Abonnements Azure Search-Dienste erstellen und verwalten möchten, muss Ihre HTTP-Anforderung den Ressourcen-Manager-Endpunkt, die Abonnement-ID, den Anbieter (in diesem Fall Azure Search) und die Azure Search-spezifische Operation enthalten.

In [Erste Schritte mit der Azure Search Verwaltungs-REST-API](http://go.microsoft.com/fwlink/p/?linkID=516968) finden Sie exemplarische Vorgehensweisen, welche die Aufgaben der Anwendungskonfiguration und Dienstverwaltung anhand von Beispielcode darstellen. Die Beispielanwendung gibt Anforderungen an die Azure-Ressourcen-Manager-API und die Dienstverwaltungs-API von Azure Search aus und ist somit ein gutes Beispiel für eine zusammenhängende Anwendung, die auf beide APIs zugreift.

### Endpunkt ###

Der Endpunkt für Dienstverwaltungsoperationen ist die URL für den Azure-Ressourcen-Manager: `https://management.azure.com`.

Beachten Sie, dass alle Aufrufe an die Verwaltungs-API die Abonnement-ID und eine API-Version enthalten müssen.

### Versionen ###

Die aktuelle Version der Azure Search Verwaltungs-REST-API ist `api-version=2015-02-28`. Die vorherige Version `api-version=2014-07-31-Preview` ist veraltet. Obwohl sie in den kommenden Monaten weiterhin funktioniert, empfehlen wir Ihnen, sobald wie möglich auf die neue Version zu wechseln.

### Authentifizierung und Zugriffssteuerung###

Die Azure Search Verwaltungs-REST-API ist eine Erweiterung für den Azure-Ressourcen-Manager. Somit gelten für sie die gleichen Abhängigkeiten. Active Directory ist daher eine Voraussetzung für die Dienstverwaltung von Azure Search. Alle Verwaltungsanforderungen aus dem Clientcode müssen vor der Übergabe an den Ressourcen-Manager mit Azure Active Directory authentifiziert werden.

Beachten Sie, dass Sie, wenn Ihr Anwendungscode sowohl *Dienstverwaltungs-* als auch *Datenoperationen* für Indizes oder Dokumente ausführt, zwei Authentifizierungsmethoden, eine für jede Azure Search-API verwenden:

- Die Dienst- und Schlüsselverwaltung verwendet aufgrund der Abhängigkeit vom Ressourcen-Manager Active Directory zur Authentifizierung.
- Datenanforderungen für Azure Search-Dienstendpunkte, wie "Create Index" oder "Search Documents", verwenden einen `api-key` im Anforderungsheader. Informationen zur Authentifizierung einer Datenanforderung finden Sie unter [Azure Search Service REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx) (+++REST-API des Azure Search-Diensts).

Die im Abschnitt [Erste Schritte mit der Azure Search-Verwaltungs-REST-API](http://go.microsoft.com/fwlink/p/?linkID=516968) dokumentierte Beispielanwendung zeigt die Authentifizierungsmethoden für jede Art von Operation. Anweisungen zur Konfiguration einer Clientanwendung für Active Directory sind in den ersten Schritten enthalten.

Die Zugriffssteuerung für den Azure-Ressourcen-Manager verwendet die integrierten Rollen "Besitzer", "Mitwirkender" und "Leser". Administratoren sind standardmäßig Mitglieder der Rolle "Besitzer". Details hierzu finden Sie im Artikel [Rollenbasierte Zugriffssteuerung im klassischen Azure-Portal](../active-directory/role-based-access-control-configure.md).


### Übersicht über die APIs ##

Die Operationen umfassen die folgenden APIs:

- <a name="CreateService">Suchdienst erstellen</a>

    `PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="GetService">Suchdienst abrufen</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListService">Suchdienste auflisten</a>

    `GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28`

- <a name="DeleteService">Suchdienst löschen</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="UpdateService">Suchdienst aktualisieren</a>

    `PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28`

- <a name="ListAdminKey">Admin-Schlüssel auflisten</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28`

- <a name="RegenAdminKey">Admin-Schlüssel neu generieren</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28`

- <a name="CreateQueryKey">Abfrageschlüssel erstellen</a>

    `POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28`

- <a name="ListQueryKey">Abfrageschlüssel auflisten</a>

    `GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28`

- <a name="DeleteQueryKey">Abfrageschlüssel löschen</a>

    `DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28`

<a name="ServiceOps"></a>
##Dienstoperationen

Sie können die Dienste von Azure Search mit HTTP-Anforderungen innerhalb Ihres Azure-Abonnements bereitstellen und wieder entfernen. Mittels dieser Dienstoperationen mögliche Szenarien umfassen die Erstellung benutzerdefinierter Verwaltungstools oder die Einrichtung einer vollständigen Produktions- oder Entwicklungsumgebung (von der Diensterstellung bis zum Füllen eines Index). Ebenso können Lösungsanbieter, die Cloudlösungen entwickeln und vertreiben, mit diesen Operationen eine automatisierte, wiederholbare Methode für die Bereitstellung ihrer Dienste für neue Kunden entwickeln.

**Operationen an einem Dienst**

Die dienstspezifischen Operationen umfassen die folgenden APIs:

- <a name="CreateService">Suchdienst erstellen</a>
- <a name="GetService">Suchdienst abrufen</a>
- <a name="ListService">Suchdienste auflisten</a>
- <a name="DeleteService">Suchdienst löschen</a>
- <a name="UpdateService">Suchdienst aktualisieren</a>


<a name="CreateService"></a>
###Suchdienst erstellen

Die Operation **Suchdienst erstellen** stellt einen neuen Suchdienst mit den angegebenen Parametern bereit. Mit dieser API kann eine vorhandene Dienstdefinition auch aktualisiert werden.

    PUT	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### URI-Parameter der Anforderung

`subscriptionId`: Erforderlich. Die `subscriptionID` des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Dienstnamen müssen zwischen 2 und 15 Zeichen lang sein und dürfen nur Kleinbuchstaben, Ziffern oder Bindestriche enthalten. Keine Bindestriche dürfen sie an den ersten beiden und der letzten Stelle enthalten und es dürfen auch nicht mehrere Bindestriche direkt aufeinander folgen. Da alle Namen letztendlich <name>.search.windows.net-Namen werden, müssen Dienstnamen eindeutig sein. Kein Dienstname innerhalb oder über Abonnements und Ressourcengruppen hinweg darf mehrmals vorkommen. Nach der Erstellung kann der Dienstname nicht mehr geändert werden.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.


#### Anforderungsheader

`Content-Type`: Erforderlich. Setzt diesen Header auf "application/json" (Anwendung/JSON).

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.


#### Anforderungstext

{ "location": "location of search service", "tags": { "key": "value", ... }, "properties": { "sku": { "name": "free | standard | standard2" }, "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, "partitionCount": 1 | 2 | 3 | 4 | 6 | 12 } }

#### Textparameter der Anforderung#

`location`: Erforderlich. Eine der von Azure unterstützten und registrierten geografischen Regionen (z. B. West US (USA (Westen)), East US (USA (Osten)), Southeast Asia (Südostasien) usw.). Nach der Erstellung kann der Standort einer Ressource nicht mehr geändert werden.

`tags`: Optional. Eine Liste mit Schlüssel-/Wertpaaren, welche die Ressource beschreiben. Diese Tags können über Ressourcengruppen hinweg zum Anzeigen und Gruppieren von Ressourcen verwendet werden. Pro Ressource können maximal 10 Tags eingegeben werden. Der Schlüssel eines Tags darf nicht länger als 128 Zeichen und der Wert nicht länger als 256 Zeichen sein.

`sku`: Erforderlich. Gültige Werte sind `free` und `standard`. `standard2` ist ebenfalls gültig, kann aber nur verwendet werden, wenn vom Microsoft Support für Ihr Azure-Abonnement aktiviert. `free` stellt den Dienst in freigegebenen Clustern bereit. `standard` stellt den Dienst in dedizierten Clustern bereit. Kostenlos kann nur ein Suchdienst erstellt werden. Alle weiteren Dienste müssen mit der Standardpreisstufe erstellt werden. Standardmäßig wird ein Dienst mit einer Partition und einem Replikat erstellt. Weitere Partitionen und Replikate werden in Sucheinheiten abgerechnet. Weitere Details finden Sie im Abschnitt [Limits und Einschränkungen](search-limits-quotas-capacity.md). Nach der Erstellung des Diensts kann `sku` nicht mehr geändert werden.

`replicaCount`: Optional. Der Standardwert ist 1. Gültige Werte sind 1 bis 6. Nur gültig, wenn `sku` auf `standard` gesetzt ist.

`partitionCount`: Optional. Der Standardwert ist 1. Gültige Werte sind 1, 2, 3, 4, 6 oder 12. Nur gültig, wenn `sku` auf `standard` gesetzt ist.


### Antwort

HTTP 200 (OK) wird bei der Aktualisierung einer Dienstdefinition zurückgegeben. HTTP 201 (Created) wird bei der Erstellung eines neuen Diensts zurückgegeben.


#### Antwortheader

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.


#### Antworttext 

Bei HTTP 200 und 201 enthält der Antworttext die Dienstdefinition.
    
    { 
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]", 
    
      "name": "service name", 
      "location": "location of search service", 
    "type": " Microsoft.Search/searchServices", 
      "tags": {
        "key": "value",
        ...
      },  
    "properties": { 
    	"sku": { 
          "name": "free | standard | standard2" 
       		 }, 
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6, 
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12, 
        "status": "running | provisioning | deleting | degraded | disabled | error", 
        "statusDetails": "details of the status", 
        "provisioningState": "succeeded | provisioning | failed" 
      } 
    } 


#### Textelemente der Antwort

`id`: Die ID ist die URL (ohne Hostname/Schema) dieses Suchdiensts.

`name`: Der Name des Suchdiensts.

`location`: Eine der von Azure unterstützten und registrierten geografischen Regionen (z. B. West US (USA (Westen)), East US (USA (Osten)), Southeast Asia (Südostasien) usw.).

`tags`: Eine Liste mit Schlüssel-/Wertpaaren, welche die Ressource beschreiben und über Ressourcengruppen hinweg zum Anzeigen und Gruppieren von Ressourcen verwendet werden.

`sku`: Gibt die Preisstufe an. Folgende Werte sind gültig:

- `free`: Freigegebener Cluster
- `standard`: Dedizierter Cluster
- `standard2`: Nur auf Anweisung von Microsoft Support verwenden 

`replicaCount`: Gibt die Anzahl der Replikate des Diensts an. Gültige Werte sind 1 bis 6.

`partitionCount`: Gibt die Anzahl der Partitionen des Diensts an. Gültige Werte sind 1, 2, 3, 4, 6 oder 12.

`status`: Der Status des Suchdiensts beim Aufruf der Operation. Folgende Werte sind gültig:

- `running`: Der Suchdienst wurde erstellt.
- `provisioning`: Der Suchdienst wird bereitgestellt.
- `deleting`: Der Suchdienst wird gelöscht.
- `degraded`: Der Suchdienst wurde heruntergestuft. Dies kann geschehen, wenn im Cluster ein Fehler auftritt, der die ordnungsgemäße Funktion des Diensts verhindern kann.
- `disabled`: Die Suche wurde deaktiviert. Mit diesem Status lehnt der Dienst alle API-Anforderungen ab.
- `error`: Der Suchdienst hat den Status "error". 

**Hinweis**: Wenn der Dienst den Status `degraded`, `disabled` oder `error` hat, untersucht das Azure Search-Team bereits das zugrundeliegende Problem. Dedizierte Dienste mit diesen Status sind weiterhin auf Basis der Anzahl der bereitgestellten Sucheinheiten fakturierbar.

`statusDetails`: Details zum Status.

`provisioningState`: Der aktuelle Status der Dienstbereitstellung. Folgende Werte sind gültig:

- `succeeded`: Die Bereitstellung war erfolgreich.
- `provisioning`: Der Dienst wird gerade bereitgestellt.
- `failed`: Bei der Dienstbereitstellung sind Fehler aufgetreten. 

Die Bereitstellung ist ein vorübergehender Zustand während der Einrichtung der Dienstkapazität. Nach deren Einrichtung wechselt `provisioningState` zu "succeeded" (erfolgreich) oder "failed" (Fehler). Um festzustellen, wann eine Operation abgeschlossen ist, können Clientanwendungen den Bereitstellungsstatus mit der Operation **Suchdienst abrufen** abrufen (empfohlenes Abrufintervall: 30 Sekunden bis 1 Minute). Bei Verwendung des kostenlosen Diensts wird der Status in der Regel direkt im Aufruf zur Diensterstellung als "succeeded" zurückgemeldet. Grund hierfür ist, dass der kostenlose Dienst bereits eingerichtete Kapazitäten verwendet.

<a name="GetService"></a>
### Suchdienst abrufen

Die Operation **Suchdienst abrufen** gibt die Eigenschaften des angegebenen Suchdiensts zurück. Es werden keine Admin-Schlüssel zurückgegeben. Zum Abrufen von Admin-Schlüsseln müssen Sie die Operation **Admin-Schlüssel abrufen** verwenden.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

#### Anforderungs-URI

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

#### Anforderungsheader

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.


#### Anforderungstext

Keiner.


#### Statuscode der Antwort

HTTP 200 (OK), wenn erfolgreich.


#### Antwortheader

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

#### Antworttext

    {
      "id": "/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]",
    
      "name": "service name",
      "location": "location of search service",
    "type": " Microsoft.Search/searchServices",
      "tags": {
        "key": "value",
        ... 
      }, 
    "properties": {
    	"sku": {
          "name": "free | standard | standard2"
    	},
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
        "status": "running | provisioning | deleting | degraded | disabled | error",
        "statusDetails": "details of the status",
        "provisioningState": "succeeded | provisioning | failed"
      }
    }

#### Textelemente der Antwort

`id`: Die ID ist die URL (ohne Hostname/Schema) dieses Suchdiensts.

`name`: Der Name des Suchdiensts.

`location`: Der Standort der Ressource. Dies ist eine der von Azure unterstützten und registrierten geografischen Regionen (z. B. West US (USA (Westen)), East US (USA (Osten)), Southeast Asia (Südostasien) usw.).

`tags`: Eine Liste mit Schlüssel-/Wertpaaren, welche die Ressource beschreiben. Diese Tags können über Ressourcengruppen hinweg zum Anzeigen und Gruppieren von Ressourcen verwendet werden.

`sku`: Gibt die Preisstufe an. Folgende Werte sind gültig:

- `free`: Freigegebener Cluster
- `standard`: Dedizierter Cluster
- `standard2`: Nur auf Anweisung von Microsoft Support verwenden

`replicaCount`: Gibt die Anzahl der Replikate des Diensts an. Gültige Werte sind 1 bis 6.

`partitionCount`: Gibt die Anzahl der Partitionen des Diensts an. Gültige Werte sind 1, 2, 3, 4, 6 oder 12.

`status`: Der Status des Suchdiensts beim Aufruf der Operation. Folgende Werte sind gültig:

- `running`: Der Suchdienst wurde erstellt.
- `provisioning`: Der Suchdienst wird bereitgestellt.
- `deleting`: Der Suchdienst wird gelöscht.
- `degraded`: Der Suchdienst wurde heruntergestuft. Dies kann geschehen, wenn im Cluster ein Fehler auftritt, der die ordnungsgemäße Funktion des Diensts verhindern kann.
- `disabled`: Die Suche wurde deaktiviert. Mit diesem Status lehnt der Dienst alle API-Anforderungen ab.
- `error`: Der Suchdienst hat den Status "error". 
 
**Hinweis**: Wenn der Dienst den Status `degraded`, `disabled` oder `error` hat, untersucht das Azure Search-Team bereits das zugrundeliegende Problem. Dedizierte Dienste mit diesen Status sind weiterhin auf Basis der Anzahl der bereitgestellten Sucheinheiten fakturierbar.
 
`statusDetails`: Details zum Status.

`provisioningState`: Folgende Werte sind gültig:

- `succeeded`: Die Bereitstellung war erfolgreich.
- `provisioning`: Der Dienst wird gerade bereitgestellt.
- `failed`: Bei der Bereitstellung sind Fehler aufgetreten.


<a name="ListService"></a>
### Suchdienste auflisten

Die Operation **Suchdienste auflisten** gibt eine Liste mit allen Suchdiensten im Abonnement einer bestimmten Ressourcengruppe zurück. Diese Operation gibt die Dienstdefinitionen ohne die Admin-API-Schlüssel zurück. Zum Abrufen von Admin-Schlüsseln müssen Sie die Operation **Admin-Schlüssel abrufen** verwenden.

    GET https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices?api-version=2015-02-28
    
#### URI-Parameter der Anforderung

`subscriptionId`: Erforderlich. Die `subscriptionID` des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

#### Anforderungsheader

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

####Anforderungstext

Keiner.

####Antwort

Bei erfolgreicher Ausführung lautet der Statuscode HTTP 200 (OK).

### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.


####Antworttext 

Als Antworttext wird ein JSON-Array mit einer Liste der Dienste zurückgegeben, wobei jeder Dienst das durch die Operation **Suchdienst abrufen** vorgegebene Format aufweist.

Beachten Sie, dass das Feld `nextLink` immer null ist, da die aktuelle Version keine Paginierung unterstützt. Die Rückgabe mit einem leeren Wert erfolgt zur Sicherstellung der Aufwärtskompatibilität.

    {
      "value": [
     	{
          "id": "id of service 1",
          "name": "service name",
          "location": "location of search service 1",
    	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          }, 
    	"properties": {
            "sku": {
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      },   
      {
          "id": "id of service 2",
          "name": "service name 2",
          "location": "location of search service 2",
   	 	"type": " Microsoft.Search/searchServices",
          "tags": {
            "key": "value",
            ...
          },
    	"properties": {
            "sku": { 
              "name": "free | standard | standard2"
            },
            "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
            "partitionCount": 1 | 2 | 3 | 4 | 6 | 12,
            "status": "running | provisioning | deleting | degraded | disabled | error",
            "statusDetails": "details of the status",
            "provisioningState": "succeeded | provisioning | failed"
    	}
      }
    ],
    "nextLink": null
    }


<a name="DeleteService"></a>
### Suchdienst löschen

Die Operation **Suchdienst löschen** löscht den Suchdienst mitsamt seiner Daten, einschließlich aller Indizes und Dokumente.
    
    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

**Hinweis:** Administratoren und Entwickler sichern ihre Anwendungsdaten vor dem Löschen von einem Produktionsserver routinemäßig. Azure Search bietet keine Sicherungsoperation. Wenn Sie den Index als primären Speicher für Ihre Anwendung verwenden, können Sie die Daten aus dem Index jedoch mit einer Suchoperation extrahieren und extern speichern.

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

###Anforderungsheader###

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

Keiner.

###Antwort###

Bei HTTP 200 ist der Antworttext leer. HTTP 200 (OK) wird zurückgegeben, wenn die Ressource nicht vorhanden ist.

Mit **Suchdienst-API abrufen** können Sie den Status des Löschvorgangs abrufen. Wir empfehlen Abrufintervalle von 30 Sekunden bis zu einer Minute.

###Antwortheader###

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

###Antworttext###

Keiner.

<a name="UpdateService"></a>
### Suchdienst aktualisieren ##

Die Operation **Suchdienst aktualisieren** ändert die Konfiguration des Suchdiensts. Gültige Änderungen beinhalten die Änderung der Tags sowie der Anzahl der Partitionen oder Replikate, wodurch für den Dienst Sucheinheiten als fakturierbare Ereignisse hinzugefügt bzw. entfernt werden. Wenn Sie versuchen, die Anzahl der Partitionen unter die zum Speichern des vorhandenen Suchkorpus erforderliche Zahl an Partitionen herabzusetzen, wird die Operation aufgrund eines Fehlers blockiert. Für Änderungen an der Diensttopologie muss eine geraume Zeit eingeplant werden, da das Verschieben von Daten sowie das Einrichten oder Aufheben von Clustern im Rechenzentrum eine Weile dauert.

Die Parameter "name", "location" und "sku" können nicht geändert werden. Das Ändern einer dieser Eigenschaften erfordert, dass Sie einen neuen Dienst erstellen.

    PATCH https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

Alternativ können Sie auch eine PUT-Anforderung verwenden.

    PUT https://management.azure.com/subscriptions/[subscriptionId]/resourcegroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]?api-version=2015-02-28

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die `subscriptionID` des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

###Anforderungsheader###

`Content-Type`: Erforderlich. Setzt diesen Header auf "application/json" (Anwendung/JSON).

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

    {
      "tags": {
        "key": "value",
        ...
      }, 
    	"properties": {
        "replicaCount": 1 | 2 | 3 | 4 | 5 | 6,
        "partitionCount": 1 | 2 | 3 | 4 | 6 | 12
     	}
    }

###Textparameter der Anforderung###

`tags`: Optional. Eine Liste mit Schlüssel-/Wertpaaren, welche die Ressource beschreiben. Diese Tags können über Ressourcengruppen hinweg zum Anzeigen und Gruppieren von Ressourcen verwendet werden. Pro Ressource können maximal 10 Tags eingegeben werden. Der Schlüssel eines Tags darf nicht länger als 128 Zeichen und der Wert nicht länger als 256 Zeichen sein.

`replicaCount`: Optional. Der Standardwert ist 1. Gültige Werte sind 1 bis 6. Nur gültig, wenn `sku` auf `standard` gesetzt ist.

`partitionCount`: Optional. Der Standardwert ist 1. Gültige Werte sind 1, 2, 3, 4, 6 oder 12. Nur gültig, wenn `sku` auf `standard` gesetzt ist.

###Antwort###

HTTP 200 (OK) wird zurückgegeben, wenn die Operation erfolgreich ausgeführt wurde. Mit **Suchdienst-API abrufen** können Sie den Status des Aktualisierungsvorgangs abrufen. Wir empfehlen Abrufintervalle von 30 Sekunden bis zu einer Minute.


### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

### Antworttext ###

Der Antworttext enthält die aktualisierte Dienstdefinition. Ein Beispiel finden Sie unter **Suchdienst-API abrufen**.


<a name="KeyOps"></a>
## Operationen an Schlüsseln

Für die Authentifizierung bei einem Azure Search-Dienst sind zwei Angaben erforderlich: die URL des Suchdiensts und ein API-Schlüssel. Die API-Schlüssel werden bei der Erstellung des Diensts generiert und können nach der Bereitstellung des Diensts bei Bedarf neu generiert werden. Es gibt zwei Arten von API-Schlüsseln:

- Admin-Schlüssel: Bietet Zugriff auf alle Operationen (maximal 2 pro Dienst)
- Abfrageschlüssel: Authentifiziert nur Abfrageanforderungen (maximal 50 pro Dienst)

Die Möglichkeit der programmgesteuerten Verwaltung der Admin- und Abfrageschlüssel Ihres Azure Search-Diensts ermöglicht Ihnen die Erstellung benutzerdefinierter Tools zum Beispiel für folgende Aufgaben: Routinemäßiges und regelmäßiges Rollover (Änderung) der Schlüssel aus Sicherheitsgründen; Rollover der Schlüssel, wenn ein Mitarbeiter das Unternehmen verlässt; Generieren und Abrufen der Schlüssel bei der Dienstbereitstellung, wenn zur Implementierung der Lösung ein Skript oder eine programmgesteuerte Vorgehensweise verwendet wird.

Abfrageschlüssel können abgerufen, erstellt und gelöscht werden. Für Admin-Schlüssel können lediglich vorhandene Schlüsselwerte abgerufen und neu generiert werden. Durch das Löschen eines Admin-Schlüssels sperren Sie sich unter Umständen für immer aus dem Dienst aus. Diese Operation steht daher nicht zur Verfügung.

Schlüssel sind eine zufällige Kombination aus Zahlen und Großbuchstaben. Ein API-Schlüssel kann nur für den Dienst verwendet werden, für den er erstellt wurde. Wenn Sie aus Sicherheitsgründen eine Rollover-Strategie implementiert haben, kann er sich in regelmäßigen Abständen ändern.

Achten Sie darauf, API-Schlüssel, insbesondere Admin-Schlüssel, als vertrauliche Daten zu behandeln. Jeder, der Ihren Admin-Schlüssel kennt, kann die Daten aus Ihren Indizes lesen und löschen.

**Operationen an Schlüsseln**

Für Schlüssel relevante Operationen umfassen die folgenden APIs:

- <a name="ListAdminKey">Admin-Schlüssel auflisten</a>
- <a name="RegenAdminKey">Admin-Schlüssel neu generieren</a>
- <a name="CreateQueryKey">Abfrageschlüssel erstellen</a>
- <a name="ListQueryKey">Abfrageschlüssel auflisten</a>
- <a name="DeleteQueryKey">Abfrageschlüssel löschen</a>


<a name="ListAdminKey"></a>
## Admin-Schlüssel auflisten ##

Die Operation **Admin-Schlüssel auflisten** gibt den primären und den sekundären Admin-Schlüssel für den angegebenen Suchdienst zurück. Hierzu wird die POST-Methode verwendet, da diese Aktion sowohl Lese- als auch Schreibschlüssel zurückgibt.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listAdminKeys?api-version=2015-02-28

Admin-Schlüssel werden mit dem Dienst erstellt. Es gibt immer zwei Schlüssel, einen primären und einen sekundären. Sie können diese Schlüssel neu generieren, aber nicht löschen.

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

`listAdminKeys`: Erforderlich. Diese Aktion ruft den primären und den sekundären Administratorschlüssel des Suchdiensts ab.

###Anforderungsheader###

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

Keiner.

###Antwort###

HTTP 200 (OK) wird zurückgegeben, wenn die Operation erfolgreich ausgeführt wurde.

### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

###Antworttext###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    

<a name="RegenAdminKey"></a>
## Admin-Schlüssel neu generieren ##

Die Operation **Admin-Schlüssel neu generieren** löscht den primären oder sekundären Schlüssel und generiert ihn neu. Mit einer Operation kann jeweils nur ein Schlüssel neu generiert werden. Achten Sie bei der Neugenerierung der Schlüssel darauf, dass Sie sich den Zugang auf den Dienst erhalten. Durch den sekundären Schlüssel steht Ihnen beim Rollover des primären Schlüssels weiterhin ein Schlüssel zur Verfügung. Jeder Dienst verfügt immer über beide Schlüssel. Sie können die Schlüssel neu generieren, aber sie weder löschen noch einen Dienst ohne sie ausführen.
 
    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/regenerateAdminKey/[keyKind]?api-version=2015-02-28

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.
	
`regenerateKey`: Erforderlich. Diese Aktion gibt an, dass der primäre oder der sekundäre Admin-Schlüssel neu generiert werden soll.

`keyKind`: Erforderlich. Gibt an, welcher Schlüssel neu generiert werden soll. Folgende Werte sind gültig:

- `primary`
- `secondary`

###Anforderungsheader###

`Content-Type`: Erforderlich. Setzt diesen Header auf "application/json" (Anwendung/JSON).

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

Keiner.

###Antwort###

HTTP 200 (OK) wird zurückgegeben, wenn die Operation erfolgreich ausgeführt wurde.

### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

###Antworttext###

    {
      "primaryKey": "api key",
      "secondaryKey": "api key"
    }
    
###Textelemente der Antwort###

`primaryKey`: Der primäre Admin-Schlüssel, wenn dieser neu generiert wurde.

`secondaryKey`: Der sekundäre Admin-Schlüssel, wenn dieser neu generiert wurde.



<a name="CreateQueryKey"></a>
## Abfrageschlüssel erstellen ##

Die Operation **Abfrageschlüssel erstellen** generiert einen neuen Abfrageschlüssel für den Suchdienst. Pro Dienst können Sie bis zu 50 Abfrageschlüssel erstellen.

    POST https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/createQueryKey/[name]?api-version=2015-02-28

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

`createQueryKey`: Erforderlich. Diese Aktion erstellt einen Abfrageschlüssel für den Suchdienst.

`name`: Erforderlich. Der Name des neuen Schlüssels.

###Anforderungsheader###

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

Keiner.

###Antwort###

Bei erfolgreicher Ausführung lautet der Statuscode HTTP 200 (OK).

### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

###Antworttext###

    {
      "name": "name of key",
      "key": "api key"
    }


###Textelemente der Antwort###

`name`: Der Name des Abfrageschlüssels.

`key`: Der Wert des Abfrageschlüssels.

<a name="ListQueryKey"></a>
## Abfrageschlüssel auflisten ##


Die Operation **Abfrageschlüssel auflisten** gibt die Abfrageschlüssel für den angegebenen Suchdienst zurück. Abfrageschlüssel werden zum Senden von Abfrage-API-Aufrufen (schreibgeschützt) an einen Suchdienst verwendet. Pro Dienst können bis zu 50 Abfrageschlüssel verwendet werden.

    GET	https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/listQueryKeys?api-version=2015-02-28

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.
	
`listQueryKeys`: Erforderlich. Diese Aktion ruft die Abfrageschlüssel für den Suchdienst ab.

###Anforderungsheader###

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

Keiner.

###Antwort###

Bei erfolgreicher Ausführung lautet der Statuscode HTTP 200 (OK).

### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

###Antworttext###

    {
      "value": [
    	{
          "name": "name of key 1",
          "key": "key 1"
      	},   
      	{   
          "name": "name of key 2",
          "key": "key 2"
      	}
      ],
    "nextLink": null
    }

###Textelemente der Antwort###

`name`: Der Name des Abfrageschlüssels.

`key`: Der Wert des Abfrageschlüssels.


<a name="DeleteQueryKey"></a>
## Abfrageschlüssel löschen ##

Die Operation **Abfrageschlüssel löschen** löscht den angegebenen Abfrageschlüssel. Abfrageschlüssel sind optional und werden nur für schreibgeschützte Abfragen verwendet.

    DELETE https://management.azure.com/subscriptions/[subscriptionId]/resourceGroups/[resourceGroupName]/providers/Microsoft.Search/searchServices/[serviceName]/deleteQueryKey/[key]?api-version=2015-02-28

Im Gegensatz zu Admin-Schlüsseln werden Abfrageschlüssel nicht neu generiert. Für die Neugenerierung eines Abfrageschlüssels müssen Sie diesen löschen und neu erstellen.

###URI-Parameter der Anforderung###

`subscriptionId`: Erforderlich. Die Abonnement-ID des Azure-Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`resourceGroupName`: Erforderlich. Der Name der Ressourcengruppe innerhalb des Abonnements des Benutzers. Dieser Wert kann über die Azure-Ressourcen-Manager-API oder das Portal abgerufen werden.

`serviceName`: Erforderlich. Der Name des Suchdiensts innerhalb der angegebenen Ressourcengruppe. Wenn Sie den Dienstnamen nicht kennen, können Sie über die Azure Search-API mit "Suchdienste auflisten" eine entsprechende Liste abrufen.

`api-version`: Erforderlich. Gibt die für diese Anforderung verwendete Protokollversion an. Die aktuelle Version ist `2015-02-28`.

`deleteQueryKey`: Erforderlich. Diese Aktion löscht einen vorhandenen Abfrageschlüssel des Suchdiensts.

`key`: Erforderlich. Der zu löschende Schlüssel.

###Anforderungsheader###

`x-ms-client-request-id`: Optional. Ein vom Client generierter GUID-Wert zur Identifizierung dieser Anforderung. Falls angegeben, wird dieser Wert in die Antwortinformationen eingeschlossen, um die Anforderung zuordnen zu können.

###Anforderungstext###

Keiner.

###Antwort###

Bei erfolgreicher Ausführung lautet der Statuscode HTTP 200 (OK).

### Antwortheader ###

`Content-Type`: Dieser Header ist immer auf "application/json" gesetzt.

`x-ms-request-id`: Eine eindeutige, vom Dienst generierte Kennung für die aktuelle Operation.

###Antworttext###

Keiner.

<!---HONumber=AcomDC_0224_2016-->