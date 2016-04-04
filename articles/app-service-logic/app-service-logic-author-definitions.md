<properties 
	pageTitle="Erstellen von Logik-App-Definitionen | Microsoft Azure" 
	description="Erfahren Sie, wie die JSON-Definition für Logik-Apps geschrieben wird." 
	authors="stepsic-microsoft-com" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="stepsic"/>
	
# Erstellen von Logik-App-Definitionen
In diesem Thema wird die Verwendung von Definitionen für [App Services-Logik-Apps](app-service-logic-what-are-logic-apps.md) erläutert. Diese sind in einer einfachen, deklarativen JSON-Sprache verfasst. Machen Sie sich zunächst [mit dem Erstellen neuer Logik-Apps](app-service-logic-create-a-logic-app.md) vertraut, falls Sie dies noch nicht getan haben. Sie können auch [das umfassende MSDN-Referenzmaterial zur Definitionssprache](https://msdn.microsoft.com/library/azure/mt643789.aspx) lesen.

## Wiederholt ausgeführte Schritte in einer Liste

Häufig wird mit einem Schritt eine Liste mit Elementen abgerufen. Anschließend werden noch zwei oder mehr Aktionen für die einzelnen Elemente in der Liste ausgeführt:

![Wiederholen über Listen](./media/app-service-logic-author-definitions/newrepeatoverlists.png)

![Wiederholen über Listen](./media/app-service-logic-author-definitions/newrepeatoverlists2.png)

![Wiederholen über Listen](./media/app-service-logic-author-definitions/newrepeatoverlists3.png)

![Wiederholen über Listen](./media/app-service-logic-author-definitions/newrepeatoverlists4.png)

 
Dieses Beispiel umfasst drei Aktionen:

1. Eine Aktion, mit der eine Liste von Artikeln abgerufen wird. Damit wird ein Objekt zurückgegeben, das ein Array enthält.

2. Eine Aktion, die zu einer Linkeigenschaft für die einzelnen Artikel wechselt, die den tatsächlichen Speicherort des Artikels zurückgibt.

3. Eine Aktion, die alle Ergebnisse der zweiten Aktion per Iteration durchläuft, um die eigentlichen Artikel herunterzuladen.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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
				"uri": "@item().link"
			},
			"forEach": "@body('getArticles').responseData.feed.entries"
		},
		"downloadLinks": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item().outputs.headers.location"
			},
			"conditions": [{
				"expression": "@not(equals(actions('readLinks').status, 'Skipped'))"
			}],
			"forEach": "@actions('readLinks').outputs"
		}
	},
	"outputs": {}
}
```

Wie unter [Verwenden von Logik-App-Features](app-service-logic-use-logic-app-features.md) beschrieben, führen Sie eine Iteration für die erste Liste durch, indem Sie die `forEach:`-Eigenschaft für die zweite Aktion verwenden. Für die dritte Aktion müssen Sie jedoch die `@actions('readLinks').outputs`-Eigenschaft auswählen, da die zweite Aktion für jeden Artikel ausgeführt wurde.

In der Aktion können Sie die [`item()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#item)-Funktion verwenden. In diesem Beispiel wollte ich den `location`-Header abrufen, also habe ich `@item().outputs.headers` weiterverwendet, um die Ausgaben der Ausführung der zweiten Aktion abzurufen, die nun durchlaufen wird.

## Zuordnen von Elementen in einer Liste zu einer anderen Konfiguration

Nehmen wir als Nächstes an, dass abhängig vom Wert einer Eigenschaft völlig unterschiedliche Inhalte abgerufen werden sollen. Wir können eine Zuordnung von Werten zu Zielen als Parameter erstellen:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"specialCategories": {
			"defaultValue": ["science", "google", "microsoft", "robots", "NSA"],
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
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
			},
			"conditions": [{
				"expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)"
			}],
			"forEach": "@body('getArticles').responseData.feed.entries"
		}
	}
}
```

In diesem Fall rufen wir zunächst eine Liste von Artikeln ab. Im zweiten Schritt wird dann in einer Zuordnung abgefragt, von welcher URL der Inhalt abgerufen werden soll, und zwar basierend auf der Kategorie, die als Parameter definiert wurde.

Hierbei sollten Sie auf zwei Dinge achten: Erstens dient die [`intersection()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#intersection)-Funktion dazu, zu überprüfen, ob die Kategorie mit einer der bekannten definierten Kategorien übereinstimmt. Zweitens können wir, sobald wir die Kategorie haben, das Element mithilfe eckiger Klammern aus der Zuordnung abrufen: `parameters[...]`.

## Verketten/Verschachteln von Logik-Apps während der Iteration über eine Liste

Häufig lassen sich Logik-Apps leichter verwalten, wenn sie stärker voneinander abgegrenzt sind. Dies erreichen Sie, indem Sie Ihre Logik in mehrere Definitionen einbeziehen und diese aus derselben übergeordneten Definition aufrufen. In diesem Beispiel gibt es eine übergeordnete Logik-App, die Aufträge empfängt, und eine untergeordnete Logik-App, die einige Schritte für die einzelnen Aufträge ausführt.

Übergeordnete Logik-App:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"orders": {
			"defaultValue": [{
				"quantity": 10,
				"id": "myorder1"
			}, {
				"quantity": 200,
				"id": "specialOrder"
			}, {
				"quantity": 5,
				"id": "myOtherOrder"
			}],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"iterateOverOrders": {
			"type": "Workflow",
			"inputs": {
				"uri": "https://westus.logic.azure.com/subscriptions/xxxxxx-xxxxx-xxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Logic/workflows/xxxxxxx",
				"apiVersion": "2015-02-01-preview",
				"trigger": {
					"name": "submitOrder",
					"outputs": {
						"body": "@item()"
					}
				},
				"authentication": {
					"type": "Basic",
					"username": "default",
					"password": "xxxxxxxxxxxxxx"
				}
			},
			"forEach": "@parameters('orders')"
		},
		"sendInvoices": {
			"type": "Http",
			"inputs": {
				"uri": "http://www.example.com/?invoiceID=@{item().outputs.run.outputs.deliverTime.value}",
				"method": "GET"
			},
			"forEach": "@outputs('iterateOverOrders')"
		}
	},
	"outputs": {}
}
```

In der untergeordneten Logik-App verwenden Sie dann die [`triggerBody()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#triggerBody)-Funktion, um die Werte abzurufen, die an den untergeordneten Workflow übergeben werden. Anschließend füllen Sie die Ausgaben mit den Daten auf, die Sie an den übergeordneten Workflow zurückgeben möchten.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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

Weitere Informationen zur [Aktion des Typs "Logik-App" finden Sie im MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx).

>[AZURE.NOTE]Der Logik-Apps-Designer unterstützt keine Aktionen des Typs "Logik-App", daher müssen Sie die Definition manuell bearbeiten.


## Ein Schritt zur Fehlerbehandlung, falls Fehler auftreten

Üblicherweise möchten Sie einen *Korrekturschritt* schreiben können – Logik, die **ausschließlich dann ausgeführt wird**, wenn einer oder mehrere Ihrer Aufrufe fehlschlagen. In diesem Beispiel rufen wir Daten von unterschiedlichen Stellen ab, aber wenn der Aufruf fehlschlägt, soll irgendwo eine Nachricht hinterlassen werden, um den Fehler später ermitteln zu können:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"dataFeeds": {
			"defaultValue": ["https://www.microsoft.com/de-DE/default.aspx", "https://gibberish.gibberish/"],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item()"
			},
			"forEach": "@parameters('dataFeeds')"
		},
		"postToErrorMessageQueue": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?noteAnErrorFor=@{item().inputs.uri}"
			},
			"conditions": [{
				"expression": "@equals(actions('readData').status, 'Failed')"
			}, {
				"expression": "@equals(item().status, 'Failed')"
			}],
			"forEach": "@actions('readData').outputs"
		}
	},
	"outputs": {}
}
```

Ich verwende zwei Bedingungen, da im ersten Schritt eine Iteration für eine Liste durchgeführt wird. Wenn Sie nur eine Aktion haben, benötigen Sie nur eine Bedingung (die erste). Beachten Sie außerdem, dass Sie die *Eingaben* für die fehlgeschlagene Aktion in Ihrem Korrekturschritt verwenden können – hier übergebe ich die fehlerhafte URL an den zweiten Schritt:

![Korrektur](./media/app-service-logic-author-definitions/remediation.png)

Nachdem Sie den Fehler nun behoben haben, wird der Lauf nicht länger als **fehlerhaft** gekennzeichnet. Wie Sie hier sehen können, ist dieser Lauf als **Erfolgreich** gekennzeichnet, obwohl ein Schritt fehlgeschlagen ist, da ich den Schritt zur Behebung dieses Fehlers geschrieben habe.

## Zwei (oder mehr) Schritte, die parallel ausgeführt werden

Um mehrere Aktionen parallel und nicht nacheinander auszuführen, müssen Sie die `dependsOn`-Bedingung entfernen, die diese beiden Aktionen miteinander verknüpft. Sobald die Abhängigkeit entfernt ist, werden Aktionen automatisch parallel ausgeführt, es sei denn, sie benötigen Daten voneinander.

![Verzweigungen](./media/app-service-logic-author-definitions/branches.png)

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"dataFeeds": {
			"defaultValue": ["https://www.microsoft.com/de-DE/default.aspx", "https://office.live.com/start/default.aspx"],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"readData": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "@item()"
			},
			"forEach": "@parameters('dataFeeds')"
		},
		"branch1": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?branch1Logic=@{item().inputs.uri}"
			},
			"forEach": "@actions('readData').outputs"
		},
		"branch2": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?branch2Logic=@{item().inputs.uri}"
			},
			"forEach": "@actions('readData').outputs"
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

Für welche Strategie Sie sich entscheiden, hängt davon ab, ob Sie es mit einem Element oder einer Sammlung von Elementen zu tun haben. Fall es nur um ein einzelnes Element geht, sollten Sie die [`coalesce()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#coalesce)-Funktion verwenden:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
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
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"handleNormalOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderNormally=@{parameters('order').id}"
			},
			"conditions": [{
				"expression": "@lessOrEquals(parameters('order').quantity, 100)"
			}]
		},
		"handleSpecialOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderSpecially=@{parameters('order').id}"
			},
			"conditions": [{
				"expression": "@greater(parameters('order').quantity, 100)"
			}]
		},
		"submitInvoice": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?invoice=@{coalesce(outputs('handleNormalOrders')?.headers?.etag,outputs('handleSpecialOrders')?.headers?.etag )}"
			},
			"conditions": [{
				"expression": "@or(equals(actions('handleNormalOrders').status, 'Succeeded'), equals(actions('handleSpecialOrders').status, 'Succeeded'))"
			}]
		}
	},
	"outputs": {}
}
```
 
Wenn die ersten beiden Verzweigungen beispielsweise eine Liste von Aufträgen verarbeiten, können Sie auch die [`union()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#union)-Funktion verwenden, um die Daten aus beiden Verzweigungen zu kombinieren.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"orders": {
			"defaultValue": [{
				"quantity": 10,
				"id": "myorder1"
			}, {
				"quantity": 200,
				"id": "specialOrder"
			}, {
				"quantity": 5,
				"id": "myOtherOrder"
			}],
			"type": "Array"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"handleNormalOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderNormally=@{item().id}"
			},
			"conditions": [{
				"expression": "@lessOrEquals(item().quantity, 100)"
			}],
			"forEach": "@parameters('orders')"
		},
		"handleSpecialOrders": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://www.example.com/?orderSpecially=@{item().id}"
			},
			"conditions": [{
				"expression": "@greater(item().quantity, 100)"
			}],
			"forEach": "@parameters('orders')"
		},
		"submitInvoice": {
			"type": "Http",
			"inputs": {
				"method": "POST",
				"uri": "http://www.example.com/?invoice=@{item().outputs.headers.etag}"
			},
			"conditions": [{
				"expression": "@equals(item().status, 'Succeeded')"
			}],
			"forEach": "@union(actions('handleNormalOrders').outputs, actions('handleSpecialOrders').outputs)"
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
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
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
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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

1. Rufen Sie die [`length()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#length) des Namens des Auftraggebers ab. Damit wird die Gesamtanzahl der Zeichen zurückgegeben.

2. Ziehen Sie fünf ab (da wir eine kürzere Zeichenfolge wollen).

3. Verwenden Sie die [`substring()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#substring) . Beginnen Sie bei Index `5`, und fahren Sie mit dem Rest der Zeichenfolge fort.

4. Wandeln Sie diese Teilzeichenfolge in eine [`base64()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#base64)-Zeichenfolge um.

5. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) Sie alle der `+`-Zeichen durch `-`.

6. [`replace()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#replace) Sie alle der `/`-Zeichen durch `_`.

## Arbeiten mit Zeitangaben

Zeitangaben sind insbesondere dann nützlich, wenn Sie versuchen, Daten aus einer Datenquelle abzurufen, die **Trigger** grundsätzlich nicht unterstützt. Sie können Zeitangaben auch verwenden, um zu ermitteln, wie lange die einzelnen Schritte dauern.

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
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
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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
			"conditions": [{
				"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))"
			}]
		}
	},
	"outputs": {}
}
```

In diesem Fall extrahieren wir die `startTime` des vorherigen Schritts. Dann rufen wir die aktuelle Uhrzeit ab und ziehen eine Sekunde ab :[`addseconds(..., -1)`](https://msdn.microsoft.com/library/azure/mt643789.aspx#addseconds) (Sie können auch andere Zeiteinheiten als `minutes` oder `hours` verwenden). Abschließend können wir diese beiden Werte vergleichen. Wenn der erste kleiner ist als der zweite, bedeutet dies, dass mehr als eine Sekunde verstrichen ist, seit der Auftrag erteilt wurde.

Beachten Sie außerdem, dass Zeichenfolgenformatierer zum Formatieren von Datumsangaben verwendet werden können: In der Abfragezeichenfolge verwende ich [`utcnow('r')`](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow), um das RFC1123-Format zu erhalten. Alle Datumsformate [sind im MSDN dokumentiert](https://msdn.microsoft.com/library/azure/mt643789.aspx#utcnow).

## Übergeben von Werten zur Laufzeit, um Verhalten zu variieren

Angenommen, Sie verfügen über unterschiedliche Verhalten, die Sie basierend auf einem Wert ausführen möchten, den Sie zum Starten Ihrer Logik-App verwenden. Sie können die [`triggerOutputs()`](https://msdn.microsoft.com/library/azure/mt643789.aspx#triggerOutputs)-Funktion zum Abrufen dieser Werte aus dem verwenden, was Sie übergeben haben:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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
			"conditions": [{
				"expression": "@triggerOutputs().doMoreLogic"
			}]
		}
	},
	"outputs": {}
}
```

Damit dies beim Starten des Laufs funktioniert, müssen Sie die gewünschten Eigenschaften übergeben (`uriToGet` und `doMoreLogic` im obigen Beispiel).

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
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {
		"connection": {
			"type": "string"
		}
	},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
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

In den einzelnen Umgebungen können Sie dann einen anderen Wert für den `connection`-Parameter angeben.

## Ausführen eines Schritts, bis eine Bedingung erfüllt ist

Sie verfügen möglicherweise über eine API, die Sie aufrufen, und Sie möchten mit dem Fortfahren warten, bis Sie eine bestimmte Antwort erhalten. Angenommen, Sie möchten z. B. warten, bis eine andere Person eine Datei in ein Verzeichnis hochgeladen hat, bevor diese Datei verarbeitet wird. Sie erreichen dies mit *do-until*:

```
{
	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	"contentVersion": "1.0.0.0",
	"parameters": {},
	"triggers": {
		"manual": {
			"type": "manual"
		}
	},
	"actions": {
		"http0": {
			"type": "Http",
			"inputs": {
				"method": "GET",
				"uri": "http://mydomain/listfiles"
			},
			"until": {
				"limit": {
					"timeout": "PT10M"
				},
				"conditions": [{
					"expression": "@greater(length(action().outputs.body),0)"
				}]
			}
		}
	},
	"outputs": {}
}
```

In der [REST-API-Dokumentation](https://msdn.microsoft.com/library/azure/mt643787.aspx) finden Sie Informationen dazu, welche Möglichkeiten Sie zum Erstellen und Verwalten von Logik-Apps haben.

<!---HONumber=AcomDC_0323_2016-->