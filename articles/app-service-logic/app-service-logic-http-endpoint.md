<properties
   pageTitle="Logik-Apps als aufrufbare Endpunkte"
   description="Informationen zum Erstellen und Konfigurieren des HTTP-Listeners und seinem Verwenden in einer Logik-App in Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="02/17/2016"
   ms.author="stepsic"/>


# Logik-Apps als aufrufbare Endpunkte

Die vorherige Schemaversion von Logik-Apps (*2014-12-01-preview*) erforderte eine API-App mit dem Namen **HTTP-Listener**, um einen HTTP-Endpunkt verfügbar zu machen, der synchron aufgerufen werden kann. Mithilfe des neuesten Schema (*2015-08-01-preview*) können Logik-Apps einen synchronen HTTP-Endpunkt nativ verfügbar machen.

## Hinzufügen eines Triggers zur Definition
Im ersten Schritt wird der Definition Ihrer Logik-App ein Trigger hinzugefügt, der eingehende Anforderungen empfangen kann. Es gibt drei Arten von Triggern, die Anforderungen empfangen können: „manual “, „apiConnectionWebhook“ und „httpWebhook“.

Im weiteren Verlauf des Artikels verwenden wir **manual** als Beispiel, wobei sämtliche Prinzipien auch genauso für die anderen beiden Triggertypen gelten. Nach Hinzufügen dieses Triggers zu Ihrer Workflowdefinition sieht diese wie folgt aus:

```
{
    "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "triggers" : {
        "myendpointtrigger" : {
            "type" : "manual"
        }
    }
}
```

Dadurch wird ein Endpunkt erstellt, den Sie über eine URL wie diese aufrufen können:
 
```
https://prod-03.brazilsouth.logic.azure.com:443/workflows/080cb66c52ea4e9cabe0abf4e197deff/triggers/myendpointtrigger?...
```

Sie erhalten diesen Endpunkt auf der Benutzeroberfläche oder durch Aufrufen von:

```
POST https://management.azure.com/{resourceID of your logic app}/triggers/myendpointtrigger/listCallbackURL?api-version=2015-08-01-preview
```

## Aufrufen des Endpunkts des Triggers der Logik-App
Sobald Sie über den Endpunkt des Triggers verfügen, können Sie ihn in Ihrem Back-End-System speichern und über einen `POST`-Befehl mit der vollständigen URL aufrufen. Sie können zusätzliche Abfrageparameter, Header und Inhalte in Ihrem Hauptteil einschließen.

Wenn „content-type“ `application/json` ist, können Sie innerhalb der Anforderung auf Eigenschaften verweisen. Andernfalls wird sie als einzelne binäre Einheit behandelt, die an andere APIs übergeben werden kann, ohne dass innerhalb der Anforderung auf Eigenschaften verwiesen werden kann.

## Verweisen auf den Inhalt der eingehenden Anforderung
Die `@triggerOutputs()`-Funktion gibt den Inhalt der eingehenden Anforderung aus. Dieser kann beispielsweise so aussehen:

```
{
    "headers" : {
        "content-type" : "application/json"
    },
    "body" : {
        "myprop" : "a value"
    }
}
```

Sie können über die Verknüpfung `@triggerBody()` spezifisch auf die `body`-Eigenschaft zugreifen.

Dies ist ein geringfügiger Unterschied zur Version *2014-12-01-preview*, bei der Sie auf den Hauptteil eines HTTP-Listeners über eine Funktion wie `@triggerOutputs().body.Content` zugreifen.

## Reagieren auf die Anforderung
Bei einige Anforderungen, die eine Logik-App starten, können Sie mit Inhalten auf den Aufrufer reagieren. Es gibt den neuen Aktionstyp **response**, der verwendet werden kann, um den Statuscode, Hauptteil und Header für die Antwort zu erstellen. Wenn **response** nicht vorhanden ist, antwortet der Endpunkt der Logik-App *sofort* mit **202 – Zulässig** (dies ist die Entsprechung von *Antwort automatisch senden* im HTTP-Listener).

```
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "conditions" : []
}
```

Antworten haben die folgenden Eigenschaften:

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| statusCode | Der HTTP-Statuscode, mit dem auf die eingehende Anforderung reagiert wird. Möglich sind alle gültigen Statuscodes, die mit 2xx, 4xx oder 5xx beginnen. Mit 3xx beginnende Statuscodes sind nicht zulässig. | 
| body | Ein Hauptteilobjekt, das eine Zeichenfolge, ein JSON-Objekt oder sogar binäre Inhalte aufweisen kann, auf die in einem vorherigen Schritt verwiesen wird. | 
| headers | Sie können eine beliebige Anzahl von Headern definieren, die in der Antwort enthalten sein können. | 

Alle Schritte in der Logik-App, die für die Antwort erforderlich sind, müssen binnen *60 Sekunden* abgeschlossen sein, damit die ursprüngliche Anforderung die Antwort empfängt. Wenn binnen 60 Sekunden keine Antwortaktion erfolgt, kommt es bei der eingehenden Anforderung zu einem Timeout mit der HTTP-Antwort **408 – Clienttimeout**.

## Erweiterte Endpunktkonfiguration
Logik-Apps bieten eine integrierte Unterstützung des Direktzugriffsendpunks und können stets die `POST`-Methode zum Starten der Ausführung verwenden. Die API-App **HTTP-Listener** unterstützte zuvor auch das Ändern der URL-Segmente und HTTP-Methode. Sie konnten sogar eine zusätzliche Sicherheits- oder benutzerdefinierte Domäne einrichten, indem Sie sie dem API-App-Host hinzufügten (der Web-App, die die API-App gehostet hat).

Diese Funktionalität ist über **API Management** verfügbar: – [Ändern der Methode der Anforderung](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) – [Ändern der URL-Segmente der Anforderung](https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL) – Einrichten von API Management-Domänen auf der Registerkarte **Konfigurieren** im klassischen Azure-Portal– Einrichten der Richtlinie zum Überprüfen auf Standardauthentifizierung (**Link erforderlich**)

## Vergleich der beiden Versionen hinsichtlich einer Migration

| 2014-12-01-preview | 2015-08-01-preview |
|---------------------|--------------------|
| Klicken auf die API-App **HTTP-Listener** | Klicken auf **Manueller Trigger** (keine API-App erforderlich) |
| HTTP-Listener-Einstellung *Sendet Antwort automatisch* | Hinzufügen einer **response**-Aktion zur Workflowdefinition oder nicht |
| Konfigurieren der Standard- oder OAuth-Authentifizierung | Über API Management |
| Konfigurieren der HTTP-Methode | Über API Management |
| Konfigurieren des relativen Pfads | Über API Management |
| Verweisen auf den eingehenden Hauptteil über `@triggerOutputs().body.Content` | Verweisen über `@triggerOutputs().body` |
| Aktion **HTTP-Antwort senden** im HTTP-Listener | Klicken auf **Auf HTTP-Anforderung reagieren** (keine API-App erforderlich)

<!---HONumber=AcomDC_0224_2016-->