<properties 
	pageTitle="Erstellen von Logik-App-Definitionen"
	description="Erfahren Sie, wie die JSON-Definition für Logik-Apps geschrieben wird."
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
	ms.date="06/16/2015"
	ms.author="stepsic"/>
	
#Erstellen von Logik-App-Definitionen
In diesem Thema wird die Verwendung von Definitionen für [App Service-Logik-Apps](app-service-logic-what-are-logic-apps.md) erläutert. Diese sind in einer einfachen, deklarativen JSON-Sprache verfasst. Machen Sie sich zunächst [mit dem Erstellen neuer Logik-Apps](../app-service-create-a-logic-app.md) vertraut, falls Sie dies noch nicht getan haben. Sie können auch [das umfassende MSDN-Referenzmaterial zur Definitionssprache](https://msdn.microsoft.com/library/azure/dn948512.aspx) lesen.

## Wiederholt ausgeführte Schritte in einer Liste

Häufig wird mit einem Schritt eine Liste mit Elementen abgerufen. Darüber hinaus gibt es noch zwei oder mehr Aktionen, die Sie für die Liste ausführen sollten.

![Wiederholen über Listen](./media/app-service-logic-author-definitions/repeatoverlists.png)

Dieses Beispiel umfasst drei Aktionen:

1. Eine Aktion, mit der eine Liste von Artikeln abgerufen wird. Damit wird ein Objekt zurückgegeben, das ein Array enthält.

2. Eine Aktion, die zu einer Linkeigenschaft für die einzelnen Artikel wechselt, die den tatsächlichen Speicherort des Artikels zurückgibt.

3. Eine Aktion, die alle Ergebnisse der zweiten Aktion per Iteration durchläuft, um die eigentlichen Artikel herunterzuladen.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            }
        },
        "readLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem().link"
            },
            "repeat": "@body('getArticles').responseData.feed.entries"
        },
        "downloadLinks": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatOutputs().headers.location"
            },
            "conditions": [
                {
                    "expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
                }
            ],
            "repeat": "@actions('readLinks').outputs.repeatItems"
        }
    },
    "outputs": {}
}
```

Wie unter [Verwenden von Logik-App-Features](app-service-logic-use-logic-app-features.md) beschrieben, führen Sie eine Iteration für die erste Liste durch, indem Sie die `repeat:`-Eigenschaft für die zweite Aktion verwenden. Für die dritte Aktion müssen Sie jedoch die `@actions('readLinks').outputs.repeatItems`-Eigenschaft auswählen, da die zweite Aktion für jeden Artikel ausgeführt wurde.

In der Aktion können Sie wahlweise die [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem)-, [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs)- oder [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody)-Funktion verwenden. In diesem Fall wollte ich den `location`-Header abrufen, also habe ich die [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs)-Funktion verwendet, um die Ausgaben der Aktionsausführung der zweiten Aktion abzurufen, über die nun eine Iteration ausgeführt wird.

## Zuordnen von Elementen in einer Liste zu einer anderen Konfiguration

Nehmen wir als Nächstes an, dass abhängig vom Wert einer Eigenschaft völlig unterschiedliche Inhalte abgerufen werden sollen. Wir können eine Zuordnung von Werten zu Zielen als Parameter erstellen.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "specialCategories": {
            "defaultValue": [
                "science",
                "google",
                "microsoft",
                "robots",
                "NSA"
            ],
            "type": "Array"
        },
        "destinationMap": {
            "defaultValue": {
                "science": "http://www.nasa.gov",
                "microsoft": "https://www.microsoft.com/de-DE/default.aspx",
                "google": "https://www.google.com",
                "robots": "https://en.wikipedia.org/wiki/Robot",
                "NSA": "https://www.nsa.gov/"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "getArticles": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=http://feeds.wired.com/wired/index"
            },
            "conditions": []
        },
        "getSpecialPage": {
            "repeat": "@body('getArticles').responseData.feed.entries",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(repeatItem().categories, parameters('specialCategories')))]"
            },
            "conditions": [
                {
                    "expression": "@greater(length(intersection(repeatItem().categories, parameters('specialCategories'))), 0)"
                }
            ]
        }
    }
}
```

In diesem Fall rufen wir zunächst eine Liste von Artikeln ab. Im zweiten Schritt wird dann in einer Zuordnung abgefragt, von welcher URL der Inhalt abgerufen werden soll, und zwar basierend auf der Kategorie, die als Parameter definiert wurde.

Hierbei sollten Sie auf zwei Dinge achten: Erstens dient die [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection)-Funktion dazu, zu überprüfen, ob die Kategorie mit einer der bekannten definierten Kategorien übereinstimmt. Zweitens können wir, sobald wir die Kategorie haben, das Element mithilfe eckiger Klammern aus der Zuordnung abrufen: `parameters[...]`.

## Verketten/Verschachteln von Logik-Apps während der Iteration über eine Liste

Häufig lassen sich Logik-Apps leichter verwalten, wenn sie stärker voneinander abgegrenzt sind. Dies erreichen Sie, indem Sie Ihre Logik in mehrere Definitionen einbeziehen und diese aus derselben übergeordneten Definition aufrufen. In diesem Beispiel gibt es eine übergeordnete Logik-App, die Aufträge empfängt, und eine untergeordnete Logik-App, die einige Schritte für die einzelnen Aufträge ausführt.

Übergeordnete Logik-App:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "iterateOverOrders": {
            "repeat": "@parameters('orders')",
            "type": "Workflow",
            "inputs": {
                "uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
                "apiVersion": "2015-02-01-preview",
                "trigger": {
                    "name": "submitOrder",
                    "outputs": {
                        "body": "@repeatItem()"
                    }
                },
                "authentication": {
                    "type": "Basic",
                    "username": "default",
                    "password": "xxxxxxxxxxxxxx"
                }
            }
        },
        "sendInvoices": {
            "repeat": "@outputs('iterateOverOrders').repeatItems",
            "type": "Http",
            "inputs": {
                "uri": "http://www.example.com/?invoiceID=@{repeatOutputs().run.outputs.deliverTime.value}",
                "method": "GET"
            }
        }
    },
    "outputs": {}
}
```

In der untergeordneten Logik-App verwenden Sie dann die [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody)-Funktion, um die Werte abzurufen, die an den untergeordneten Workflow übergeben werden. Anschließend füllen Sie die Ausgaben mit den Daten auf, die Sie an den übergeordneten Workflow zurückgeben möchten.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "triggers": {},
    "actions": {
        "calulatePrice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcPrice&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        },
        "calculateDeliveryTime": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?action=calcTime&id=@{triggerBody().id}&qty=@{triggerBody().quantity}"
            }
        }
    },
    "outputs": {
        "deliverTime": {
            "type": "String",
            "value": "@outputs('calculateDeliveryTime').headers.etag"
        }
    }
}
```

Weitere Informationen zur [Aktion des Typs "Logik-App" finden Sie im MSDN](https://msdn.microsoft.com/de-DE/library/azure/dn948511.aspx).

>[AZURE.NOTE]Der Logik-Apps-Designer unterstützt keine Aktionen des Typs "Logik-App", daher müssen Sie die Definition manuell bearbeiten.


## Ein Schritt zur Fehlerbehandlung, falls Fehler auftreten

Üblicherweise möchten Sie einen *Korrekturschritt* schreiben können – Logik, die **ausschließlich dann ausgeführt wird**, wenn einer oder mehrere Ihrer Aufrufe fehlschlagen. In diesem Beispiel rufen wir Daten von unterschiedlichen Stellen ab, aber wenn der Aufruf fehlschlägt, soll irgendwo eine Nachricht hinterlassen werden, um den Fehler später ermitteln zu können.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/de-DE/default.aspx",
                "https://gibberish.gibberish/"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "postToErrorMessageQueue": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?noteAnErrorFor=@{repeatItem().inputs.uri}"
            },
            "conditions": [
                {
                    "expression": "@equals(actions('readData').status, 'Failed')"
                },
                {
                    "expression": "@equals(repeatItem().status, 'Failed')"
                }
            ]
        }
    },
    "outputs": {}
}
```

Ich verwende zwei Bedingungen, da im ersten Schritt eine Iteration für eine Liste durchgeführt wird. Wenn Sie nur eine Aktion haben, benötigen Sie nur eine Bedingung (die erste). Beachten Sie außerdem, dass Sie die *Eingaben* für die fehlgeschlagene Aktion in Ihrem Korrekturschritt verwenden können – hier übergebe ich die fehlerhafte URL an den zweiten Schritt.

![Korrektur](./media/app-service-logic-author-definitions/remediation.png)

Nachdem Sie den Fehler nun behoben haben, wird der Lauf nicht länger als **fehlerhaft** gekennzeichnet. Wie Sie hier sehen können, ist dieser Lauf als **Erfolgreich** gekennzeichnet, obwohl ein Schritt fehlgeschlagen ist, da ich den Schritt zur Behebung dieses Fehlers geschrieben habe.

## Zwei (oder mehr) Schritte, die parallel ausgeführt werden

Um mehrere Aktionen Ausführung parallel und nicht nacheinander auszuführen, müssen Sie die `dependsOn`-Bedingung entfernen, die diese beiden Aktionen miteinander verknüpft. Sobald die Abhängigkeit entfernt ist, werden Aktionen automatisch parallel ausgeführt, es sei denn, sie benötigen Daten voneinander.

![Verzweigungen](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/de-DE/default.aspx",
                "https://office.live.com/start/default.aspx"
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "repeat": "@parameters('dataFeeds')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@repeatItem()"
            }
        },
        "branch1": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch1Logic=@{repeatItem().inputs.uri}"
            }
        },
        "branch2": {
            "repeat": "@actions('readData').outputs.repeatItems",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?branch2Logic=@{repeatItem().inputs.uri}"
            }
        }
    },
    "outputs": {}
}
```

Wie im oben im Beispiel zu sehen ist, hängen "branch1" und "branch2" nur vom Inhalt von "readData" ab. Daher werden beide dieser Verzweigungen parallel ausgeführt:

![Parallel](./media/app-service-logic-author-definitions/parallel.png)

Wie Sie sehen, ist der Zeitstempel für die beiden Verzweigungen identisch.

## Verknüpfen von zwei bedingten Verzweigungen der Logik

Sie können zwei bedingte Logikflüsse (die möglicherweise nicht ausgeführt wurden) mithilfe einer einzelnen Aktion kombinieren, die auf Daten aus beiden Verzweigungen zurückgreift.

Für welche Strategie Sie sich entscheiden, hängt davon ab, ob Sie es mit einem Element oder einer Sammlung von Elementen zu tun haben. Fall es nur um ein einzelnes Element geht, sollten Sie die [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce)-Funktion verwenden:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(parameters('order').quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
            },
            "conditions": [
                {
                    "expression": "@greater(parameters('order').quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
            },
            "conditions": [
                {
                    "expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
                }
            ]
        }
    },
    "outputs": {}
}
```
 
Wenn die ersten beiden Verzweigungen beispielsweise eine Liste von Aufträgen verarbeiten, können Sie auch die [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union)-Funktion verwenden, um die Daten aus beiden Verzweigungen zu kombinieren.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "orders": {
            "defaultValue": [
                {
                    "quantity": 10,
                    "id": "myorder1"
                },
                {
                    "quantity": 200,
                    "id": "specialOrder"
                },
                {
                    "quantity": 5,
                    "id": "myOtherOrder"
                }
            ],
            "type": "Array"
        }
    },
    "triggers": {},
    "actions": {
        "handleNormalOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderNormally=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@lessOrEquals(repeatItem().quantity, 100)"
                }
            ]
        },
        "handleSpecialOrders": {
            "repeat": "@parameters('orders')",
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?orderSpecially=@{repeatItem().id}"
            },
            "conditions": [
                {
                    "expression": "@greater(repeatItem().quantity, 100)"
                }
            ]
        },
        "submitInvoice": {
            "repeat": "@union(actions('handleNormalOrders').outputs.repeatItems, actions('handleSpecialOrders').outputs.repeatItems)",
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/?invoice=@{repeatOutputs().headers.etag}"
            },
            "conditions": [
                {
                    "expression": "@equals(repeatItem().status, 'Succeeded')"
                }
            ]
        }
    },
    "outputs": {}
}
```
## Arbeiten mit Zeichenfolgen

Es gibt verschiedene Funktionen, die zum Ändern von Zeichenfolgen verwendet werden können. Sehen wir uns ein Beispiel mit einer Zeichenfolge an, die an ein System übergeben werden soll. In diesem Fall sind wir aber nicht sicher, ob die Zeichencodierung ordnungsgemäß verarbeitet wird. Eine Möglichkeit besteht darin, diese Zeichenfolge mit "base64" zu codieren. Allerdings werden zur Vermeidung von Escapezeichen in einer URL einige Zeichen ersetzt.

Wir wollen außerdem eine Teilzeichenfolge des Namens des Auftrag, da die ersten fünf Zeichen nicht verwendet werden.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1",
                "orderer": "NAME=Stèphén__Šīçiłianö"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').orderer,5,sub(length(parameters('order').orderer), 5) )),'+','-') ,'/' ,'_' )}"
            }
        }
    },
    "outputs": {}
}
```

Gehen Sie von innen nach außen vor:

1. Rufen Sie die [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) des Namens des Auftraggebers ab. Damit wird die Gesamtanzahl der Zeichen zurückgegeben.

2. Ziehen Sie fünf ab (da wir eine kürzere Zeichenfolge wollen).

3. Verwenden Sie die [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring) . Beginnen Sie bei Index `5`, und fahren Sie mit dem Rest der Zeichenfolge fort.

4. Wandeln Sie diese Teilzeichenfolge in eine [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64)-Zeichenfolge um.

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) Sie alle der `+`-Zeichen durch `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) Sie alle der `/`-Zeichen durch `_`.

## Arbeiten mit Zeitangaben

Zeitangaben sind insbesondere dann nützlich, wenn Sie versuchen, Daten aus einer Datenquelle abzurufen, die **Trigger** grundsätzlich nicht unterstützt. Sie können Zeitangaben auch verwenden, um zu ermitteln, wie lange die einzelnen Schritte dauern.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "order": {
            "defaultValue": {
                "quantity": 10,
                "id": "myorder1"
            },
            "type": "Object"
        }
    },
    "triggers": {},
    "actions": {
        "order": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?id=@{parameters('order').id}"
            }
        },
        "timingWarning": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
        	},
            "conditions": [
                {
                    "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
                }
            ]
        }
    },
    "outputs": {}
}
```

In diesem Fall extrahieren wir die `startTime` des vorherigen Schritts. Dann rufen wir die aktuelle Uhrzeit ab und ziehen eine Sekunde ab :[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (Sie können auch andere Zeiteinheiten als `minutes` oder `hours` verwenden). Abschließend können wir diese beiden Werte vergleichen. Wenn der erste kleiner ist als der zweite, bedeutet dies, dass mehr als eine Sekunde verstrichen ist, seit der Auftrag erteilt wurde.

Beachten Sie außerdem, dass Zeichenfolgenformatierer zum Formatieren von Datumsangaben verwendet werden können: In der Abfragezeichenfolge verwende ich [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow), um das RFC1123-Format zu erhalten. Alle Datumsformate [sind im MSDN dokumentiert](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## Übergeben von Werten zur Laufzeit, um Verhalten zu variieren

Angenommen, Sie verfügen über unterschiedliche Verhalten, die Sie basierend auf einem Wert ausführen möchten, den Sie zum Starten Ihrer Logik-App verwenden. Sie können die [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs)-Funktion zum Abrufen dieser Werte aus dem verwenden, was Sie übergeben haben:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@triggerOutputs().uriToGet"
            }
        },
        "extraStep": {
            "type": "Http",
            "inputs": {
                "method": "POST",
                "uri": "http://www.example.com/extraStep"
            },
            "conditions": [
                {
                    "expression": "@triggerOutputs().doMoreLogic"
                }
            ]
        },  
    },
    "outputs": {}
}
```

Damit dies beim Starten des Laufs funktioniert, müssen Sie die gewünschten Eigenschaften übergeben (`uriToGet` und `doMoreLogic` im obigen Beispiel). Hier ist der Aufruf, [für den Sie die Standardauthentifizierung verwenden können](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth):

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

Mit der folgenden Nutzlast. Beachten Sie, dass Sie die Logik-App mit den jetzt zu verwendenden Werten bereitgestellt haben:

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

Wenn diese Logik-App ausgeführt wird, ruft sie den von mir übergebenen URI auf und führt diesen zusätzlichen Schritt aus, da ich `true` übergeben habe. Wenn Sie nur die Parameter zur Bereitstellungszeit variieren möchten (nicht für *jeden Lauf*), sollten Sie `parameters` wie nachfolgend gezeigt verwenden.

## Verwenden von Parametern zur Bereitstellungszeit für unterschiedliche Umgebungen

Üblicherweise verfügen Sie über einen Bereitstellungslebenszyklus mit einer Entwicklungsumgebung, einer Stagingumgebung und einer Produktionsumgebung. In all diesen Umgebungen können Sie dieselbe Definition, aber beispielsweise unterschiedliche Datenbanken verwenden. Ebenso sollten Sie dieselbe Definition übergreifend in vielen verschiedenen Regionen verwenden, um eine hohe Verfügbarkeit zu erzielen. Die einzelnen Logik-App-Instanzen sollten jedoch mit der Datenbank der betreffenden Region kommunizieren.

Beachten Sie, dass sich dies von der Verwendung anderer Parameter zur *Laufzeit* unterscheidet. Dazu sollten Sie die `trigger()`-Funktion verwenden, wie oben beschrieben.

Sie können mit einer sehr einfachen Definition wie dieser beginnen:

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "connection": {
            "type": "string"
        }
    },
    "triggers": {},
    "actions": {
        "readData": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "@parameters('connection')"
            }
        }        
    },
    "outputs": {}
}
```

In der tatsächlichen `PUT`-Anforderung für die Logik-App können Sie dann den `connection`-Parameter angeben. Da es keinen Standardwert mehr gibt, ist dieser Parameter für die Logik-App-Nutzlast erforderlich:

```
{
    "properties": {
        "sku": {
            "name": "Premium",
            "plan": {
                "id": "/subscriptions/xxxxx/resourceGroups/xxxxxx/providers/Microsoft.Web/serverFarms/xxxxxx"
            }
        },
        "definition": {
          // Use the definition from above here
        },
        "parameters": {
            "connection": {
                "value": "https://my.connection.that.is.per.enviornment"
            }
        }
    },
    "location": "westus"
}
``` 

In den einzelnen Umgebungen können Sie dann einen anderen Wert für den `connection`-Parameter angeben. In der [REST-API-Dokumentation](https://msdn.microsoft.com/library/azure/dn948513.aspx) finden Sie Informationen dazu, welche Möglichkeiten Sie zum Erstellen und Verwalten von Logik-Apps haben.

<!---HONumber=September15_HO1-->