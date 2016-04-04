<properties
   pageTitle="Resource Manager-REST-APIs | Microsoft Azure"
   description="Enthält eine Übersicht über die Authentifizierung mit Resource Manager-REST-APIs und Beispiele zur Verwendung."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>
   
# Resource Manager-REST-APIs
Hinter jedem Aufruf von Azure Resource Manager, hinter jeder bereitgestellten Vorlage und hinter jedem konfigurierten Speicherkonto steht mindestens ein Aufruf der RESTful-API des Azure Resource Managers. In diesem Thema geht es um diese APIs und das Aufrufen ganz ohne SDK. Dies kann sehr nützlich sein, wenn Sie die vollständige Kontrolle über alle Anforderungen an Azure haben möchten oder wenn das SDK für Ihre bevorzugte Sprache nicht verfügbar ist oder die gewünschten Vorgänge nicht unterstützt werden.

In diesem Artikel gehen wir nicht alle APIs durch, die in Azure verfügbar gemacht werden, sondern es werden einige APIs als Beispiele dafür verwendet, wie Sie die Verbindung herstellen können. Wenn Sie die Grundlagen verstanden haben, können Sie sich den Artikel [Azure Resource Manager REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn790568.aspx) durchlesen. Er enthält ausführliche Informationen dazu, wie Sie den Rest der APIs verwenden.

## Authentifizierung
Die Authentifizierung für ARM wird per Azure Active Directory (AD) durchgeführt. Zum Herstellen einer Verbindung mit einer API müssen Sie sich zuerst gegenüber Azure AD authentifizieren, um ein Authentifizierungstoken zu erhalten, das Sie für jede Anforderung übergeben können. Da wir einen reinen Aufruf direkt an die REST-APIs senden, wird auch vorausgesetzt, dass Sie die Authentifizierung nicht mit einem normalen Benutzernamen und Kennwort durchführen möchten – ggf. per Popupaufforderung zur Eingabe eines Benutzernamens und Kennworts und anderen Authentifizierungsverfahren, die in Szenarien mit zweistufiger Authentifizierung genutzt werden. Daher erstellen wir zuerst eine so genannte Azure AD-Anwendung und einen Dienstprinzipal, der für die Anmeldung verwendet wird. Bedenken Sie aber, dass Azure AD mehrere Authentifizierungsprozeduren unterstützt, die alle zum Abrufen dieses Authentifizierungstokens verwendet werden, das wir für nachfolgende API-Anforderungen benötigen. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals](./resource-group-create-service-principal-portal.md).

### Generieren eines Zugriffstokens 
Die Authentifizierung gegenüber Azure AD wird durchgeführt, indem Azure AD unter „login.microsoftonline.com“ aufgerufen wird. Zum Authentifizieren benötigen Sie die folgenden Informationen:

* Azure AD-Mandanten-ID (Name der Azure AD-Instanz, die Sie zum Anmelden verwenden und die häufig, aber nicht zwangsläufig, der Instanz Ihres Unternehmens entspricht)
* Anwendungs-ID (bei der Erstellung der Azure AD-Anwendung ermittelt)
* Kennwort (bei der Erstellung der Azure AD-Anwendung ausgewählt)

Stellen Sie bei der unten angegebenen HTTP-Anforderung sicher, dass Sie „Azure AD Tenant ID“, „Application ID“ und „Password“ durch die richtigen Werte ersetzen.

**Generische HTTP-Anforderung:**

```HTTP
POST /<Azure AD Tenant ID>.onmicrosoft.com/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... ähnelt (bei erfolgreicher Authentifizierung) dieser Antwort:

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(Die access\_token-Elemente in der obigen Antwort wurden gekürzt, um die Lesbarkeit zu verbessern.)

**Generieren des Zugriffstokens mit Bash:**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token?api-version=1.0
```

**Generieren des Zugriffstokens mit PowerShell:**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

Die Antwort enthält ein Zugriffstoken, Informationen zur Gültigkeitsdauer des Tokens und Informationen dazu, für welche Ressourcen Sie dieses Token verwenden können. Das Zugriffstoken, das Sie im vorherigen HTTP-Aufruf empfangen haben, muss für alle Anforderungen an die ARM-API als Header mit dem Namen „Authorization“ und dem Wert „Bearer YOUR\_ACCESS\_TOKEN“ übergeben werden. Beachten Sie das Leerzeichen zwischen „Bearer“ und Ihrem Zugriffstoken.

Wie Sie anhand des obigen HTTP-Ergebnisses erkennen können, ist das Token für einen bestimmten Zeitraum gültig, in dem Sie dieses Token zwischenspeichern und wiederverwenden können. Es ist zwar ggf. möglich, die Authentifizierung gegenüber Azure AD für jeden API-Aufruf durchzuführen, aber dies wäre sehr ineffizient.

## Aufrufen von ARM-REST-APIs

Die [Azure Resource Manager-REST-APIs sind hier dokumentiert](https://msdn.microsoft.com/library/azure/dn790568.aspx). Es würde den Rahmen dieses Tutorials sprengen, die Nutzung aller APIs zu dokumentieren. In dieser Dokumentation werden nur einige APIs verwendet, um die grundlegende Nutzung der APIs zu erläutern. Danach verweisen wir Sie auf die offizielle Dokumentation.

### Auflisten aller Abonnements

Eine der einfachsten Möglichkeiten besteht darin, die verfügbaren Abonnements aufzulisten, auf die Sie zugreifen können. In der Anforderung unten sehen Sie, wie das Zugriffstoken als Header übergeben wird.

(Ersetzen Sie YOUR\_ACCESS\_TOKEN durch Ihr Zugriffstoken.)

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Sie erhalten dann eine Liste mit den Abonnements, auf die dieser Dienstprinzipal zugreifen kann.

(Die unten angegebenen Abonnement-IDs wurden gekürzt, um die Lesbarkeit zu verbessern.)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### Auflisten aller Ressourcengruppen in einem bestimmten Abonnement

Alle Ressourcen, die für die ARM-APIs verfügbar sind, sind in einer Ressourcengruppe geschachtelt. Wir fragen ARM nach vorhandenen Ressourcengruppen in unserem Abonnement ab, indem wir die unten angegebene HTTP GET-Anforderung verwenden. Beachten Sie, wie die Abonnement-ID hierbei jetzt als Teil der URL übergeben wird.

(Ersetzen Sie YOUR\_ACCESS\_TOKEN und SUBSCRIPTION\_ID durch Ihr Zugriffstoken bzw. Ihre Abonnement-ID.)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

Die Antwort hängt davon ab, ob Sie Ressourcengruppen definiert haben bzw. wie viele Ressourcengruppen Sie definiert haben.

(Die unten angegebenen Abonnement-IDs wurden gekürzt, um die Lesbarkeit zu verbessern.)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### Erstellen einer Ressourcengruppe

Bisher haben wir nur die ARM-APIs nach Informationen abgefragt, und es wird Zeit, dass wir stattdessen einige Ressourcen erstellen. Wir beginnen mit der einfachsten Ressource: einer Ressourcengruppe. Mit der folgenden HTTP-Anforderung wird eine neue Ressourcengruppe in einer Region bzw. an einem Standort Ihrer Wahl erstellt, und ihr werden ein oder mehrere Tags hinzugefügt (im Beispiel unten wird nur ein Tag hinzugefügt).

(Ersetzen Sie YOUR\_ACCESS\_TOKEN, SUBSCRIPTION\_ID und RESOURCE\_GROUP\_NAME durch Ihr Zugriffstoken, die Abonnement-ID und den Namen der Ressourcengruppe, die Sie erstellen möchten.)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

Bei erfolgreicher Ausführung erhalten Sie eine Antwort, die folgender Antwort ähnelt:

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Sie haben erfolgreich eine Ressourcengruppe in Azure erstellt. Glückwunsch!

### Bereitstellen von Ressourcen in einer Ressourcengruppe mit einer ARM-Vorlage

Mit ARM können Sie Ihre Ressourcen bereitstellen, indem Sie die ARM-Vorlagen verwenden. Ein ARM-Vorlage definiert verschiedene Ressourcen und ihre Abhängigkeiten. In diesem Abschnitt wird lediglich davon ausgegangen, dass Sie mit ARM-Vorlagen vertraut sind. Es wird nur gezeigt, wie Sie den API-Aufruf durchführen, um mit der Bereitstellung zu beginnen. Eine ausführliche Dokumentation zu ARM-Vorlagen finden Sie hier.

Die Bereitstellung einer ARM-Vorlage unterscheidet sich nicht sehr vom Aufrufen anderer APIs. Ein wichtiger Aspekt ist, dass die Bereitstellung einer Vorlage ziemlich lange dauern kann. Dies hängt davon ab, was sich innerhalb der Vorlage befindet, und für den API-Aufruf wird nur die Rückgabe durchgeführt. Es liegt an Ihnen als Entwickler, den Status der Bereitstellung abzufragen, um zu ermitteln, wann die Bereitstellung abgeschlossen ist.

Für dieses Beispiel verwenden wir eine öffentlich verfügbar gemachte ARM-Vorlage, die bei [GitHub](https://github.com/Azure/azure-quickstart-templates) verfügbar ist. Mit der hier verwendeten Vorlage wird eine Linux-VM für die Region „USA, Westen“ bereitgestellt. Diese Vorlage ist zwar in einem öffentlichen Repository wie GitHub verfügbar, aber Sie können sich auch dafür entscheiden, die vollständige Vorlage als Teil der Anforderung zu übergeben. Beachten Sie, dass wir Parameterwerte im Rahmen der Anforderung bereitstellen, die in der genutzten Vorlage verwendet werden.

(Ersetzen Sie SUBSCRIPTION\_ID, RESOURCE\_GROUP\_NAME, DEPLOYMENT\_NAME, YOUR\_ACCESS\_TOKEN, GLOBALY\_UNIQUE\_STORAGE\_ACCOUNT\_NAME, ADMIN\_USER\_NAME,ADMIN\_PASSWORD und DNS\_NAME\_FOR\_PUBLIC\_IP durch die geeigneten Werte für Ihre Anforderung.)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

Die relativ lange JSON-Antwort auf diese Anforderung wurde weggelassen, um die Lesbarkeit dieser Dokumentation zu verbessern. Die Antwort enthält Informationen zu der Bereitstellung mit Vorlage, die Sie eben erstellt haben.

<!---HONumber=AcomDC_0323_2016-->