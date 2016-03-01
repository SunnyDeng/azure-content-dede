<properties
	pageTitle="Konfigurieren von Azure-Warnungen zum Senden an andere Systeme"
	description="Umleiten von Azure-Warnungen an andere Azure-fremde Systeme."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="ashwink"/>

# Konfigurieren von Webhooks für Warnungen

Mithilfe von Webhooks können Benutzer die Azure-Warnungsbenachrichtigungen an andere Systeme für die Nachbearbeitung oder benutzerdefinierte Benachrichtigungen weiterleiten. Beispielsweise können Warnungen an Dienste weitergeleitet werden, die eine eingehende Webanforderung zum Senden einer SMS, zum Protokollieren von Fehlern oder Benachrichtigen eines Teams über Chat-/Messagingdienste bearbeiten können.

Der URI des Webhooks muss ein gültiger HTTP- oder HTTPS-Endpunkt sein. Der Azure-Benachrichtigungsdienst führt einen POST-Vorgang auf dem angegebenen Webhook durch und übergibt eine bestimmte JSON-Nutzlast und ein bestimmtes JSON-Schema.

## Konfigurieren von Webhooks über das Portal

Im Bildschirm "Benachrichtigungen erstellen" oder "Benachrichtigungen bearbeiten" im [Azure-Portal](https://portal.azure.com/) können Sie den Webhook-URI hinzufügen oder aktualisieren.

![Hinzufügen einer Warnungsregel](./media/insights-webhooks-alerts/Alertwebhook.png)


## Authentifizierung

Zwei Authentifizierungstypen sind möglich:

1. **Tokenbasierte Authentifizierung**: In diesem Fall wird der Webhook-URI mit einer Token-ID gespeichert, z. B. **https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue*.
2.	**Standardauthentifizierung** mit Benutzer-ID und Kennwort: In diesem Fall wird der Webhook-URI als **https://userid:password@mysamplealert/webcallback?someparamater=somevalue&foo=bar* gespeichert.

## Nutzlast und Schema

Der POST-Vorgang enthält die folgende JSON-Nutzlast und das folgende JSON-Schema für alle metrikbasierten Warnungen.

```
{
"status": "Activated",
"context": {
            "timestamp": "2015-08-14T22:26:41.9975398Z",
            "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
            "name": "ruleName1",
            "description": "some description",
            "conditionType": "Metric",
            "condition": {
                        "metricName": "Requests",
                        "metricUnit": "Count",
                        "metricValue": "10",
                        "threshold": "10",
                        "windowSize": "15",
                        "timeAggregation": "Average",
                        "operator": "GreaterThanOrEqual"
                },
            "subscriptionId": "s1",
            "resourceGroupName": "useast",                                
            "resourceName": "mysite1",
            "resourceType": "microsoft.foo/sites",
            "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
            "resourceRegion": "centralus",
            "portalLink": “https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1”                                
},
"properties": {
              "key1": "value1",
              "key2": "value2"
              }
}
```

>[AZURE.NOTE] Bei der nächsten Aktualisierung wird die Unterstützung von Warnungen für Ereignisse hinzugefügt ("conditionType" : "Event").


| Feld | Erforderlich? | Feste Gruppe von Werten? | Hinweise |
| :-------------| :-------------   | :-------------   | :-------------   |
|status|J|"Activated", "Resolved"|So wird der Warnungstyp ermittelt. Azure sendet automatisch aktivierte und aufgelöste Warnungen für die festgelegte Bedingung.|
|context| J | | Der Warnungskontext|
|timestamp| J | | Der Zeitpunkt, an dem die Warnung ausgelöst wurde. Die Warnung wird ausgelöst, sobald die Metrik aus dem Diagnosespeicher gelesen wird.|
|id | J | | Jeder Warnungsregel ist eine eindeutige ID zugewiesen.|
|Name|J | |
|description |J | |Beschreibung der Warnung.|
|conditionType |J |"Metric", "Event" |Zwei Arten von Warnungen werden unterstützt. Eine Warnung basiert auf der Metrik und die andere auf Ereignissen. Zukünftig werden Warnungen für Ereignisse unterstützt. Verwenden Sie daher diesen Wert, um zu prüfen, ob die Warnung auf der Metrik oder auf einem Ereignis basiert.|
|condition |J | |Legt die Felder fest, die basierend auf "conditionType" überprüft werden.|
|metricName |für Warnungen des Typs "Metric" | |Der Name der Metrik, die definiert, welche Elemente mit der Regel überwacht werden.|
|metricUnit |für Warnungen des Typs "Metric" |"Bytes", "BytesPerSecond", "Count", "CountPerSecond", "Percent", "Seconds"|	 Die in der Metrik zulässige Einheit. Zulässige Werte: https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx|
|metricValue |für Warnungen des Typs "Metric" | |Der tatsächliche Wert der Metrik, die die Warnung ausgelöst hat.|
|threshold |für Warnungen des Typs "Metric" | |Der Schwellenwert, der die Warnung aktiviert.|
|windowSize |für Warnungen des Typs "Metric" | |Der Zeitraum, in dem die Aktivität der Warnung basierend auf dem Schwellenwert überwacht wird. Muss zwischen 5 Minuten und 1 Tag liegen. Format der Zeitspanne nach ISO 8601.|
|timeAggregation |für Warnungen des Typs "Metric" |"Average", "Last", "Maximum", "Minimum", "None", "Total" |	Legt fest, wie die erfassten Daten im Zeitverlauf kombiniert werden sollen. Der Standardwert ist "Average". Zulässige Werte: https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx|
|operator |für Warnungen des Typs "Metric" | |Der zum Vergleichen der Daten und des Schwellenwerts verwendete Operator.|
|subscriptionId |J | |Azure-Abonnement-GUID|
|ResourceGroupName |J | |Ressourcengruppenname der betroffenen Ressource|
|resourceName |J | |Ressourcenname der betroffenen Ressource|
|resourceType |J | |Ressourcentyp der betroffenen Ressource|
|Ressourcen-ID |J | |URI der Ressourcen-ID, der die Ressource eindeutig identifiziert|
|resourceRegion |J | |Region/Speicherort der betroffenen Ressource|
|portalLink |J | |Direkter Link vom Azure-Portal zur Zusammenfassungsseite der Ressource|
|Eigenschaften |N |Optional |Eine Gruppe von <Key  Value> Paaren (d. h. Wörterbuch<String  String>), die Details zum Ereignis enthält. Das Feld "properties" ist optional. In einer angepassten Benutzeroberfläche oder einem auf Logik-Apps basierenden Workflow können Benutzer Schlüssel und Werte eingeben, die über die Nutzlast übergeben werden können. Alternativ können benutzerdefinierte Eigenschaften direkt über den Webhook-URI an den Webhook zurückgegeben werden (als Abfrageparameter).|


>[AZURE.NOTE] Das Feld "properties" kann nicht über das Portal verwendet werden. In der nächsten Version des Insights SDK können Sie die Eigenschaften über die Warnung-API festlegen.

## Nächste Schritte

Im Video [Integrate Azure Alerts with PagerDuty](http://go.microsoft.com/fwlink/?LinkId=627080) (in englischer Sprache) erhalten Sie zusätzliche Hintergrundinformationen zu Azure-Warnungen und Webhooks.

Informationen zum programmgesteuerten Erstellen von Webhooks finden Sie unter [Create an Alert with Webhooks using Azure Insights SDK (C#)](https://code.msdn.microsoft.com/Create-Azure-Alerts-with-b938077a) (in englischer Sprache).

Nach dem Einrichten Ihrer Webhooks und Warnungen können Sie die folgenden Optionen zum Initiieren eines automatisierten Skripts austesten.

[Execute Azure Automation scripts (Runbooks) (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=627081)

Verwenden Sie Azure-Warnungen zum Senden von Nachrichten an andere Dienste. Zum Einstieg können Sie die folgenden Beispielvorlagen verwenden.

[Use Logic App to send SMS via Twilio API (in englischer Sprache)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)

[Use Logic App to send Slack messages (in englischer Sprache)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)

[Use Logic App to send messages to an Azure Queue (in englischer Sprache)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<!---HONumber=AcomDC_0218_2016-->