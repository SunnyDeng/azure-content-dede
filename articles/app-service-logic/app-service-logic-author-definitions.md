<properties 
	pageTitle="Autor Logik App-Definitionen" 
	description="Erfahren Sie, wie die JSON-Definition für Logik apps schreiben." 
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
	ms.date="05/23/2015"
	ms.author="stepsic"/>
	
#Autor Logik App-Definitionen
In diesem Thema veranschaulicht, wie [App Services Logik Apps](app-service-logic-what-are-logic-apps.md) Definitionen, die eine einfache, deklarative JSON-Sprache ist. Wenn dies noch nicht geschehen, sollten Sie sich [zum Erstellen einer neuen Logik app](../app-service-create-a-logic-app.md) ersten. Sie können auch Lesen der [vollständige Referenzmaterial, das von der Definitionssprache auf MSDN](https://msdn.microsoft.com/library/azure/dn948512.aspx).

## Mehrere Schritte, die über eine Liste wiederholen

Ein gängiges Muster besteht darin, einen Schritt, der eine Liste der Elemente abruft, und haben Sie eine Reihe von zwei oder mehr Aktionen, die in der Liste angezeigt werden soll.

![Wiederholen Sie über Listen](./media/app-service-logic-author-definitions/repeatoverlists.png)

In diesem Beispiel sind 3 Aktionen:

1. Enthält eine Liste von Artikeln. Dies gibt wieder ein Objekt, das ein Array enthält.

2. Eine Aktion, die auf eine Linkeigenschaft für jeden Artikel geht die wieder den tatsächlichen Speicherort des Artikels zurückgibt.

3. Eine Aktion, die eine Iteration über alle Ergebnisse aus der zweiten Aktion die eigentlichen Artikel herunterladen.

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

Wie unter [Logik app Funktionen](app-service-logic-use-logic-app-features.md), Sie mit der ersten Liste durchlaufen die `repeat:` Eigenschaft für die zweite Aktion. Für die dritte Aktion, Sie müssen jedoch wählen die `@actions('readLinks').outputs.repeatItems` -Eigenschaft, da die zweite für jeden Artikel ausgeführt.

Innerhalb der Aktion verwenden Sie entweder den: [`repeatItem()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatItem), der [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) oder [`repeatBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatBody) Funktionen. In diesem Beispiel, ich möchte die `location` Header, so dass ich verwendet die [`repeatOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#repeatOutputs) Funktion, um die Ausgaben der Ausführung der Aktion aus der zweiten Aktion abzurufen, die wir jetzt durchlaufen werden.

## Zuordnen von Elementen in einer Liste zu einige andere Konfiguration

Als Nächstes nehmen wir an, dass völlig unterschiedliche Inhalte abhängig von einem Wert einer Eigenschaft abgerufen werden soll. Wir können eine Zuordnung von Werten an Ziele als Parameter erstellen.

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
                "microsoft": "https://www.microsoft.com/de-de/default.aspx",
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

In diesem Fall wir zunächst eine Liste von Artikeln erhalten und dann im zweite Schritt sucht in einer Zuordnung, basierend auf der Kategorie, die als Parameter, welche URL zum Abrufen des Inhalts aus definiert wurde.

Zwei Elemente hier achten: die [`intersection()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#intersection) Funktion wird verwendet, um zu überprüfen, um festzustellen, ob die Kategorie einer bekannten definierten Kategorien entspricht. Zweitens, nachdem wir die Kategorie abrufen, können, rufen wir das Element von der Karte mithilfe von eckigen Klammern: `parameters[...]`.

## Kette/verschachteln Logik Apps während einer wiederholten

Es kann häufig einfacher sein, Ihre Apps Logik zu verwalten, wenn sie diskreter sind. Dazu können Sie die Logik in mehrere Definitionen Finanzierung und Ihren Aufruf aus derselben übergeordneten Definition. In diesem Beispiel werden eine übergeordnete-Logik-app, die Aufträge empfängt, und eine untergeordnete Logik-app, die einige Schritte für jeden Auftrag ausgeführt wird.

In der übergeordneten Logik app:

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

Klicken Sie dann in der untergeordneten Logik app verwenden Sie die [`triggerBody()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerBody) Funktion zum Abrufen der Werte, die in den untergeordneten Workflow übergeben wurden. Sie müssen dann die Ausgaben mit den Daten aufzufüllen, die an den übergeordneten Fluss zurückkehren möchten.

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

Sie erhalten Informationen über die [Logik app die Aktion auf der MSDN-](https://msdn.microsoft.com/en-US/library/azure/dn948511.aspx).

>[AZURE.NOTE]Logik Anwendungs-Designer unterstützt keine Logik app Typ Aktionen, daher Sie die Definition manuell bearbeiten müssen.


## Ein Schritt Fehlerbehandlungsroutine, wenn etwas schiefgeht

Häufig schreiben möchten eine *Wiederherstellung Schritt* --Logik, die ausführt, wenn Sie **und nur dann, wenn**, ein oder mehrere Anrufe ist fehlgeschlagen. In diesem Beispiel werden wir Abrufen von Daten aus einer Vielzahl von Stellen, wenn der Aufruf fehlschlägt, sollen jedoch zum Übermitteln einer Nachricht an einer beliebigen Stelle, damit ich später Fehler aufspüren können.

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/de-de/default.aspx",
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

Ich verwende zwei Bedingungen, da im ersten Schritt ich über eine Liste wiederholt wird. Wenn Sie nur eine einzelne Aktion haben, benötigen Sie nur eine Bedingung (der erste Vorgang). Beachten Sie auch, mit denen Sie die *Eingaben* auf die fehlgeschlagene Aktion in der Wiederherstellung Schritt – hier ich übergebe die fehlerhafte URL mit dem zweiten Schritt fort.

![Wiederherstellung](./media/app-service-logic-author-definitions/remediation.png)

Schließlich, da Sie nun den Fehler behandelt haben, wir nicht mehr markieren Ausführen als **Fehler**. Wie Sie hier sehen können, ist diese Ausführung **erfolgreich** obwohl Sie einen Schritt fehlgeschlagen, da habe ich die Schritte, um diesen Fehler zu behandeln.

## Zwei (oder mehr) Schritte, die parallel ausgeführt.

Um mehrere Aktionen Ausführung in parallel und nicht in der Reihenfolge haben, müssen Sie entfernen die `dependsOn` Bedingung, die diese zwei Aktionen miteinander verknüpft. Sobald die Abhängigkeit entfernt wird, werden Aktionen automatisch parallel ausführen, es sei denn, diese Daten voneinander.

![Verzweigungen](./media/app-service-logic-author-definitions/branches.png)

```
{
    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "dataFeeds": {
            "defaultValue": [
                "https://www.microsoft.com/de-de/default.aspx",
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

Wie im obigen Beispiel sehen ist, hängt branch1 und branch2 nur den Inhalt von Daten lesen. Daher werden beide dieser Verzweigungen parallel ausgeführt:

![Parallele](./media/app-service-logic-author-definitions/parallel.png)

Sie können sehen, dass der Zeitstempel für die beiden Verzweigungen identisch ist.

## Verknüpfen von zwei bedingte Verzweigungen der Logik

Zwei bedingte Abläufe Logik (, die möglicherweise nicht ausgeführt) zu kombinieren, müssen eine einzelne Aktion, die Daten aus beiden Verzweigungen erfordert.

Ihre Strategie für diese variiert abhängig, wenn Sie ein Element oder eine Auflistung von Elementen behandeln. Im Fall eines einzelnen Elements, sollten Sie verwenden die [`coalesce()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#coalesce) Funktion:

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
 
Auch wenn die ersten beiden Verzweigungen auf eine Liste mit Bestellungen arbeiten, z. B. werde soll die [`union()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#union) -Funktion verwendet, um die Daten aus beiden Verzweigungen zu kombinieren.

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

Es gibt verschiedene Funktionen, die zu maniplate Zeichenfolge verwendet werden kann. Sehen wir uns ein Beispiel, in dem haben wir einer Zeichenfolge, die an ein System übertragen werden soll, aber wir sind nicht darauf verlassen, dass die Zeichencodierung richtig behandelt werden. Eine Möglichkeit besteht darin, base64 codieren Sie diese Zeichenfolge. Allerdings werden zur Vermeidung von Escapezeichen in einer URL wir einige Zeichen zu ersetzen.

Wir möchten auch eine Teilzeichenfolge des der des Auftrags werden, da die ersten 5 Zeichen nicht verwendet werden.

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

Arbeiten von innen heraus:

1. Abrufen der [`length()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#length) der sortierelement Namens, gibt dies wieder die Gesamtanzahl der Zeichen

2. Subtrahieren von 5 (da wir eine kürzere Zeichenfolge wollen)

3. Tatsächlich werden die [`substring()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#substring) . Wir beginnen mit Index `5` und den Rest der Zeichenfolge.

4. Diese Teilzeichenfolge Konvertieren einer [`base64()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#base64) Zeichenfolge

5. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) alle von der `+` mit Zeichen `-`

6. [`replace()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#replace) alle von der `/` mit Zeichen `_`

## Arbeiten mit Zeitangaben

Datum Uhrzeit kann nützlich sein, insbesondere, wenn Sie versuchen, Daten aus einer Datenquelle abrufen, die natürlich nicht unterstützt **Trigger**. Datum Uhrzeit können auch herausfinden, wie lange die verschiedenen Schritte unternommen werden.

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

In diesem Beispiel werden wir Extrahieren der `startTime` des vorherigen Schritts. Anschließend werden die aktuelle Uhrzeit abrufen und Subtrahieren von einer Sekunde:[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/dn948512.aspx#addseconds) (Sie können andere Zeiteinheiten wie z. B. `minutes` oder `hours`). Schließlich können wir diese zwei Werte vergleichen. Wenn die erste kleiner als die zweite, dann bedeutet dies, dass mehr als eine Sekunde verstrichen ist, da der Auftrag wurde eingefügt.

Beachten Sie auch, dass wir Zeichenfolge Formatierer zum Formatieren von Datumsangaben verwenden können: in der Abfragezeichenfolge ich verwende [`utcnow('r')`](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow) der RFC1123 abgerufen. Alle Datum formatieren [auf MSDN dokumentiert ist](https://msdn.microsoft.com/library/azure/dn948512.aspx#utcnow).

## Übergeben Sie die Werte zur Laufzeit Verhalten zu ändern

Nehmen wir an, Sie verfügen über unterschiedliche Verhaltensweisen, die Sie basierend auf einem Wert ausgeführt, die Sie starten aus Ihrer app Logik verwenden möchten. Sie können die [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/dn948512.aspx#triggerOutputs) Funktion zum Abrufen dieser Werte aus, was Sie übergeben:

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

Diese Arbeit tatsächlich vornehmen, beim Starten der Ausführung die Eigenschaften übergeben werden sollen (im obigen Beispiel `uriToGet` und `doMoreLogic`). Hier ist der können Sie [Verwenden Sie die Standardauthentifizierung für](https://msdn.microsoft.com/library/azure/dn948513.aspx#basicAuth):

```
POST https://<<Logic app endpoint from the Essentials>>/run?api-version=2015-02-01-preview
Authorization: Basic <<Based 64 encoded username (default) : password (from the Settings blade)>>
Content-type: application/json
```

Mit der folgenden Nutzlast. Beachten Sie, dass Sie mit den Werten jetzt verwenden die Logik app bereitgestellt haben:

```
{
    "outputs": {
        "uriToGet" : "http://my.uri.I.want/",
        "doMoreLogic" : true
    }
}
``` 

Wenn diese Anwendung Logik ausgeführt wird, aufrufen, den ich übergebenen Uri und dieser zusätzlichen Schritt ausgeführt werden, da ich übergeben `true`. Wenn Sie nur die Parameter zum Zeitpunkt der Bereitstellung variieren möchten (nicht für *jede Ausführung*), verwenden, sollten `parameters` wie genannt, unten.

## Verwenden von Parametern für die Bereitstellungszeit für unterschiedliche Umgebungen

Es ist üblich, eine Bereitstellungslebenszyklus vorliegt, bei dem Sie eine Entwicklungsumgebung, einer Stagingumgebung und einer Produktionsumgebung. In allen diesen können Sie dieselbe Definition werden soll, jedoch unterschiedliche Datenbanken verwenden, z. B.. Ebenso sollten Sie dieselbe Definition in vielen verschiedenen Regionen für hohe Verfügbarkeit verwenden, aber jeder Logik app-Instanz in dieser Region-Datenbank kommunizieren soll.

Beachten Sie, dass dies nicht mit anderen Parametern an *Common Language Runtime*, sollten Sie verwenden die `trigger()` funktionieren wie oben hingewiesen.

Sie können mit einer sehr einfach gehalten wie diese starten:

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

Klicken Sie auf den tatsächlichen `PUT` Anforderung für die Anwendung Logik können Sie den Parameter bereitstellen `connection`. Beachten Sie, wie es ist nicht mehr ein Default-Wert, der diesen Parameter in der app-Nutzlast Logik erforderlich ist:

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

In jeder Umgebung können Sie dann einen anderen Wert für Bereitstellen der `connection` Parameter. Siehe die [REST-API-Dokumentation](https://msdn.microsoft.com/library/azure/dn948513.aspx) für alle Optionen, die Sie zum Erstellen und Verwalten von Logik apps verfügen.

<!---HONumber=GIT-SubDir_Tue_AM_dede-->