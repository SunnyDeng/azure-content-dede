<properties 
	pageTitle="Neue Schemaversion „2015-08-01-preview“" 
	description="Erfahren Sie, wie die JSON-Definition für die aktuelle Version der Logik-Apps geschrieben wird." 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="stepsic"/>
	
# Neue Schemaversion „2015-08-01-preview“

Die neue Schema- und API-Version für Logik-Apps enthält eine Reihe von Verbesserungen, die die Zuverlässigkeit und Benutzerfreundlichkeit von Logik-Apps erhöhen. Es gibt vier wichtige Unterschiede:

1. Der **APIApp**-Aktionstyp wurde auf einen neuen **APIConnection**-Aktionstyp aktualisiert.
2. **Repeat** wurde umbenannt in **Foreach**.
3. Die **HTTP-Listener**-API-App ist nicht mehr erforderlich.
4. Für den Aufruf untergeordneter Workflows wird ein neues Schema verwendet.

## 1\. Verschieben von API-Verbindungen

Die größte Änderung besteht darin, dass Sie zur Verwendung von APIs nicht mehr API-Apps in Ihrem Azure-Abonnement bereitstellen müssen. Zwei Arten von APIs können verwendet werden: * Verwaltete APIs * Benutzerdefinierte Web-APIs

Diese beiden API-Arten werden etwas unterschiedlich gehandhabt, da sich ihre Verwaltungs- und Hostingmodelle unterscheiden. Ein Vorteil dieses Modells besteht darin, dass Sie nicht mehr auf in Ihrer Ressourcengruppe bereitgestellte Ressourcen beschränkt sind.

### Verwaltete APIs

Es gibt eine Reihe von APIs, die von Microsoft in Ihrem Namen verwaltet werden, beispielsweise Office 365, Salesforce, Twitter, FTP usw. Einige dieser verwalteten APIs können ohne weitere Bearbeitung verwendet werden, etwa Bing Translate, wohingegen andere APIs konfiguriert werden müssen. Diese Konfiguration wird als *Verbindung* bezeichnet.

Wenn Sie Office 365 verwenden, müssen Sie beispielsweise eine Verbindung erstellen, die Ihr Office 365-Anmeldetoken enthält. Dieses Token wird sicher gespeichert und aktualisiert, damit Ihre Logik-App jederzeit die Office 365-API aufrufen kann. Wenn Sie eine Verbindung mit Ihrer SQL Server-Instanz oder dem FTP-Server herstellen möchten, müssen Sie eine Verbindung erstellen, die die Verbindungszeichenfolge enthält.

Innerhalb dieser Definition werden diese Aktionen als `APIConnection` bezeichnet. Hier sehen Sie ein Beispiel für eine Verbindung, die Office 365 zum Senden einer E-Mail aufruft:

```
{
    "actions": {
        "Send_Email": {
            "type": "ApiConnection",
            "inputs": {
                "host": {
                    "api": {
                        "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['shared_office365']['connectionId']"
                    }
                },
                "method": "post",
                "body": {
                    "Subject": "Reminder",
                    "Body": "Don't forget!",
                    "To": "me@contoso.com"
                },
                "path": "/Mail"
            }
        }
    }
}
```

Das `host`-Objekt stellt den Teil der Eingaben dar, der eindeutig für API-Verbindungen gilt. Das Objekt umfasst zwei Teile: `api` und `connection`.

`api` enthält die Laufzeit-URL, unter der die verwaltete API gehostet wird. Durch den Aufruf von `GET https://management.azure.com/subscriptions/{subid}/providers/Microsoft.Web/managedApis/?api-version=2015-08-01-preview` können Sie alle für Sie verfügbaren verwalteten APIs anzeigen.

Wenn Sie eine API verwenden, müssen die **Verbindungsparameter** nicht unbedingt festgelegt sein. Sind keine Parameter definiert, ist keine **Verbindung** erforderlich. Sind Parameter festgelegt, müssen Sie eine Verbindung erstellen. Wenn Sie diese Verbindung erstellen, erhält sie den von Ihnen gewählten Namen. Sie verweisen darauf im `connection`-Objekt innerhalb des `host`-Objekts. Rufen Sie zum Erstellen einer Verbindung in einer Ressourcengruppe Folgendes auf:

```
PUT https://management.azure.com/subscriptions/{subid}/resourceGroups/{rgname}/providers/Microsoft.Web/connections/{name}?api-version=2015-08-01-preview
```

Verwenden Sie folgenden Text:


```
{
  "properties": {
    "api": {
      "id": "/subscriptions/{subid}/providers/Microsoft.Web/managedApis/azureblob"
    },
	"parameterValues" : {
		"accountName" : "{The name of the storage account -- the set of parameters is different for each API}"
	}
  },
  "location" : "{Logic app's location}"
}
```

### Bereitstellen von verwalteten APIs in einer Azure Resource Manager-Vorlage

Sie können eine vollständige Anwendung in einer ARM-Vorlage erstellen, solange keine interaktive Anmeldung erforderlich ist. Ist eine Anmeldung erforderlich, können Sie alle Elemente mit der ARM-Vorlage einrichten, müssen jedoch zum Autorisieren der Verbindung das Portal aufrufen.

```
	"resources": [{
		"apiVersion": "2015-08-01-preview",
		"name": "azureblob",
		"type": "Microsoft.Web/connections",
		"location": "[resourceGroup().location]",
		"properties": {
			"api": {
				"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
			},
			"parameterValues": {
				"accountName": "[parameters('storageAccountName')]",
				"accessKey": "[parameters('storageAccountKey')]"
			}
		}
	}, {
		"type": "Microsoft.Logic/workflows",
		"apiVersion": "2015-08-01-preview",
		"name": "[parameters('logicAppName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[resourceId('Microsoft.Web/connections', 'azureblob')]"
		],
		"properties": {
			"sku": {
				"name": "[parameters('sku')]",
				"plan": {
					"id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
				}
			},
			"definition": {
				"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
				"actions": {
					"Create_file": {
						"type": "apiconnection",
						"inputs": {
							"host": {
								"api": {
									"runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
								},
								"connection": {
									"name": "@parameters('$connections')['azureblob']['connectionId']"
								}
							},
							"method": "post",
							"queries": {
								"folderPath": "[concat('/',parameters('containerName'))]",
								"name": "helloworld.txt"
							},
							"body": "@decodeDataUri('data:,Hello+world!')",
							"path": "/datasets/default/files"
						},
						"conditions": []
					}
				},
				"contentVersion": "1.0.0.0",
				"outputs": {},
				"parameters": {
					"$connections": {
						"defaultValue": {},
						"type": "Object"
					}
				},
				"triggers": {
					"recurrence": {
						"type": "Recurrence",
						"recurrence": {
							"frequency": "Day",
							"interval": 1
						}
					}
				}
			},
			"parameters": {
				"$connections": {
					"value": {
						"azureblob": {
							"connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
							"connectionName": "azureblob",
							"id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
						}

					}
				}
			}
		}
	}]
```

Sie sehen in diesem Beispiel, dass es sich bei den Verbindungen lediglich um normale Ressourcen in der Ressourcengruppe handelt. Sie verweisen auf die verwalteten APIs, die Ihnen in Ihrem Abonnement zur Verfügung stehen.

### Ihre benutzerdefinierten Web-APIs

Wenn Sie eigene (d. h. nicht von Microsoft verwaltete) APIs einsetzen, sollten Sie für den Aufruf die integrierte **HTTP**-Aktion verwenden. Für die optimale Nutzung sollten Sie einen Swagger-Endpunkt für Ihre API verfügbar machen. Dadurch kann der Logik-App-Designer die Ein- und Ausgaben für Ihre API rendern. Ohne Swagger kann der Designer die Ein- und Ausgaben nur als nicht transparente JSON-Objekte anzeigen.

Hier sehen Sie ein Beispiel für die neue `metadata.apiDefinitionUrl`-Eigenschaft: ```
{
   "actions": {
        "mycustomAPI": {
            "type": "http",
            "metadata" : {
              "apiDefinitionUrl" : "https://mysite.azurewebsites.net/api/apidef/"  
            },
            "inputs": {
                "uri": "https://mysite.azurewebsites.net/api/getsomedata",
                "method" : "GET"
            }
        }
    }
}
```

Wenn Sie Ihre Web-API unter **App Service** hosten, wird sie automatisch in der Liste der im Designer verfügbaren Aktionen angezeigt. Andernfalls müssen Sie die URL direkt einfügen. Der Swagger-Endpunkt muss nicht authentifiziert sein, damit er innerhalb des Logik-App-Designers verwendet werden kann (die API selbst können Sie mit den im Swagger-Endpunkt unterstützten Methoden sichern).

### Verwenden der bereits bereitgestellten API-Apps mit „2015-08-01-preview“

Wenn Sie zuvor eine API-App bereitgestellt haben, können Sie sie über die **HTTP**-Aktion aufrufen.

Beispiel: Wenn Sie Dropbox zum Auflisten von Dateien verwenden, ist in der Schemaversionsdefinition **2014-12-01-preview** etwa Folgendes enthalten: ```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
            "defaultValue": "eyJ0eX...wCn90",
            "type": "String",
            "metadata": {
                "token": {
                    "name": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
                }
            }
        }
    },
    "actions": {
        "dropboxconnector": {
            "type": "ApiApp",
            "inputs": {
                "apiVersion": "2015-01-14",
                "host": {
                    "id": "/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                    "gateway": "https://avdemo.azurewebsites.net"
                },
                "operation": "ListFiles",
                "parameters": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Sie können die entsprechende HTTP-Aktion wie folgt erstellen (der Parameterabschnitt der Logik-App-Definition bleibt unverändert):

```
{
    "actions": {
        "dropboxconnector": {
            "type": "Http",
            "metadata" : {
              "apiDefinitionUrl" : "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
            },
            "inputs": {
                "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
                "method" : "POST",
                "body": {
                    "FolderPath": "/myfolder"
                },
                "authentication": {
                    "type": "Raw",
                    "scheme": "Zumo",
                    "parameter": "@parameters('/subscriptions/423db32d-...-b59f14c962f1/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
                }
            }
        }
    }
}
```

Informationen zu den einzelnen Eigenschaften:

| Aktionseigenschaft | Beschreibung |
| --------------- | -----------  |
| `type` | `Http` anstelle von `APIapp` |
| `metadata.apiDefinitionUrl` | Wenn Sie diese Aktion im Logik-App-Designer verwenden möchten, sollten Sie den Metadatenendpunkt aufnehmen. Dieser wird erstellt aus: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Dieser wird erstellt aus: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Immer `POST` |
| `inputs.body` | Identisch mit den API-App-Parametern | 
| `inputs.authentication` | Identisch mit der API-App-Authentifizierung |

Dieser Ansatz sollte bei allen API-App-Aktionen funktionieren. Bedenken Sie jedoch, dass diese vorherigen API-Apps nicht mehr unterstützt werden und eine der beiden oben genannten Optionen (verwaltete API oder Hosten einer benutzerdefinierten Web-API) verwendet werden sollte.

## 2\. „Repeat“ umbenannt in „Foreach“

Für die vorherige Schemaversion haben wir umfangreiches Kundenfeedback mit dem Hinweis erhalten, dass bei **Repeat** nicht ganz klar sei, dass es sich dabei tatsächlich um eine For-Each-Schleife handelt. Daher wurde sie in **Foreach** umbenannt. Beispiel:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "repeat": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{repeatItem()}"
            }
        }
    }
}
```

Wird jetzt folgendermaßen geschrieben:

```
{
    "actions": {
        "pingBing": {
            "type": "Http",
            "foreach": "@range(0,2)",
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com/search?q=@{item()}"
            }
        }
    }
}
```

Zuvor wurde mit der Funktion `@repeatItem()` auf das aktuelle Element verwiesen, das durchlaufen wurde. Dies wurde zu `@item()` verkürzt.

### Verweisen auf die Ausgaben von „Foreach“
Zur weiteren Vereinfachung werden die Ausgaben von **Foreach**-Aktionen nicht in ein Objekt namens **repeatItems** eingeschlossen. Die Ausgaben der oben genannten Wiederholung lauteten:

```
{
    "repeatItems": [
        {
            "name": "pingBing",
            "inputs": {
                "uri": "https://www.bing.com/search?q=0",
                "method": "GET"
            },
            "outputs": {
                "headers": { },
                "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
            }
            "status": "Succeeded"
        }
    ]
}
```

Nun hingegen lauten sie wie folgt:

```
[
    {
        "name": "pingBing",
        "inputs": {
            "uri": "https://www.bing.com/search?q=0",
            "method": "GET"
        },
        "outputs": {
            "headers": { },
            "body": "<!DOCTYPE html><html lang="en" xml:lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:Web="http://schemas.live.com/Web/">...</html>"
        }
        "status": "Succeeded"
    }
]
```

Beim Verweisen auf diese Ausgaben mussten Sie daher zum Aufrufen des Texts der Aktion Folgendes verwenden:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "repeat" : "@outputs('pingBing').repeatItems",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@repeatItem().outputs.body"
            }
        }
    }
}
```

Nun führen Sie stattdessen Folgendes aus:

```
{
    "actions": {
        "secondAction" : {
            "type" : "Http",
            "foreach" : "@outputs('pingBing')",
            "inputs" : {
                "method" : "POST",
                "uri" : "http://www.example.com",
                "body" : "@item().outputs.body"
            }
        }
    }
}
```

Infolge dieser Änderungen werden die Funktionen `@repeatItem()`, `@repeatBody()` und `@repeatOutputs()` entfernt.

## 3\. Nativer HTTP-Listener 
Die HTTP-Listener-Funktionen sind jetzt integriert, daher müssen Sie keine HTTP-Listener-API-App mehr bereitstellen. Ausführliche Informationen dazu, wie Sie den Logik-App-Endpunkt aufrufbar machen, finden Sie [hier](app-service-logic-http-endpoint.md).

Im Rahmen dieser Änderungen wurde die Funktion `@accessKeys()` entfernt und durch die Funktion `@listCallbackURL()` zum Abrufen des Endpunkts (wenn erforderlich) ersetzt. Darüber hinaus müssen Sie jetzt mindestens einen Trigger in Ihrer Logik-App definieren. Wenn Sie für den Workflow `/run` ausführen möchten, benötigen Sie den Trigger `manual`, `apiConnectionWebhook` oder `httpWebhook`.

## 4\. Aufrufen von untergeordneten Workflows

Zuvor mussten Sie zum Aufrufen von untergeordneten Workflows den gewünschten Workflow aufrufen, das Zugriffstoken abrufen und anschließend dieses Token in die Definition der Logik-App einfügen, die den untergeordneten Workflow aufrufen soll. Bei der neuen Schemaversion generiert das Logik-App-Modul zur Laufzeit automatisch eine SAS für den untergeordneten Workflow. Das bedeutet, dass Sie keine geheimen Schlüssel in die Definition einfügen müssen. Beispiel:

```
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName" : "myendpointtrigger"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "conditions" : []
}
```

Eine zweite Verbesserung ist, dass die untergeordneten Workflows vollen Zugriff auf die eingehenden Anforderung erhalten. Das bedeutet, dass Sie Parameter im Abschnitt *queries* und im Objekt *headers* übergeben und den gesamten Text vollständig festlegen können.

Schließlich müssen Änderungen am untergeordneten Workflow vorgenommen werden. Zuvor konnten Sie einen untergeordneten Workflow einfach aufrufen. Nun müssen Sie einen Trigger-Endpunkt im Workflow festlegen, den der übergeordnete Workflow aufruft. Im Allgemeinen bedeutet das, dass Sie einen Trigger vom Typ **Manuell** hinzufügen und ihn dann in der übergeordneten Definition verwenden. Beachten Sie, dass die `host`-Eigenschaft ein spezielles `triggerName`-Element enthält, da Sie immer angeben müssen, welchen Trigger Sie aufrufen.

## Weitere Änderungen

### Neue queries-Eigenschaft
Alle Aktionstypen unterstützen jetzt die neue Eingabe **queries**. Dabei kann es sich um ein strukturiertes Objekt handeln, sodass Sie die Zeichenfolge nicht manuell zusammensetzen müssen.

### parse()-Funktion umbenannt
Da wir bald weitere Inhaltstypen hinzufügen, wurde die Funktion `parse()` umbenannt in `json()`.

## In Kürze verfügbar: Enterprise Integration-APIs
Zu diesem Zeitpunkt sind noch keine verwalteten Versionen der Enterprise Integration-APIs verfügbar (z. B. AS2). Wie in der [Roadmap](http://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/) beschrieben, stehen diese in Kürze zur Verfügung. In der Zwischenzeit können Sie Ihre vorhandenen bereitgestellten BizTalk-APIs über die HTTP-Aktion verwenden. Die Vorgehensweise wird weiter oben unter „Verwenden der bereits bereitgestellten API-Apps“ beschrieben.

<!---HONumber=AcomDC_0224_2016-->