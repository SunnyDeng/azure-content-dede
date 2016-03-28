<properties 
    pageTitle="DocumentDB-Änderungsbenachrichtigungen mit Logik-Apps | Microsoft Azure" 
    description="." 
    keywords="Änderungsbenachrichtigung"
    services="documentdb" 
    authors="hedidin" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="rest-api" 
    ms.topic="article" 
    ms.date="03/10/2016" 
    ms.author="b-hoedid"/>

# Benachrichtigungen über neue oder geänderte DocumentDB-Ressourcen mit Logik-Apps

Den Anstoß für diesen Artikel gab mir eine Frage, die in einem der DocumentDB-Communityforen veröffentlicht wurde. Die Frage lautete: **Unterstützt DocumentDB Benachrichtigungen über geänderte Ressourcen**?

Ich habe seit vielen Jahren mit BizTalk Server gearbeitet, und dies ist ein sehr gängiges Szenario bei Verwendung des [WCF-LOB-Adapters](https://msdn.microsoft.com/library/bb798128.aspx). Also entschied ich mich, festzustellen, ob ich diese Funktionalität in DocumentDB für neue und/oder geänderte Dokumente duplizieren konnte.

Dieser Artikel bietet eine Übersicht über die Komponenten der Änderungsbenachrichtigungslösung, einschließlich eines [Triggers](documentdb-programming.md#trigger) und einer [Logik-App](../app-service-logic/app-service-logic-what-are-logic-apps.md). Wichtige Codeausschnitte sind eingebunden, und die gesamte Lösung ist bei [GitHub](https://github.com/HEDIDIN/DocDbNotifications) verfügbar.

## Anwendungsfall

Die folgende Geschichte ist der Anwendungsfall für diesen Artikel.

DocumentDB ist das Repository für Dokumente für Health Level Seven International (HL7) Fast Healthcare Interoperability Resources (FHIR). Nehmen wir an, dass Ihre DocumentDB-Datenbank kombiniert mit Ihrer API und Logik-App einen HL7 FHIR-Server bildet. Eine Einrichtung im Gesundheitswesen speichert Patientendaten in der DocumentDB-Datenbank „Patients“. Es gibt mehrere Sammlungen in der Datenbank „Patients“; „Clinical“, „Identification“ etc. Patientendaten fallen unter „Identification“. Sie haben eine Sammlung mit dem Namen „Patient“.

Die Kardiologieabteilung verfolgt persönliche Gesundheits- und Übungsdaten. Die Suche nach neuen oder geänderten „Patient“-Datensätzen ist zeitaufwändig. Die IT-Abteilung wurde gebeten, eine Möglichkeit zu finden, dass sie eine Benachrichtigung über neue oder geänderte „Patient“-Datensätze erhalten könnte.

Die IT-Abteilung antwortete, sie könne dies mühelos bereitstellen. Sie sagten auch, dass sie die Dokumente in den [Azure-Blobspeicher](https://azure.microsoft.com/services/storage/) übertragen könnten, damit die Kardiologieabteilung problemlos darauf zugreifen könne.

## So hat die IT-Abteilung das Problem gelöst

Die IT-Abteilung entschied, um diese Anwendung zu erstellen, sie zuerst im Modell darzustellen. Das Schöne an der Verwendung von Business Process Model and Notation (BPMN, Geschäftsprozessmodell und -notation) ist, dass die Sprache für Techniker und Nichttechniker leicht verständlich ist. Der gesamte Benachrichtigungsprozess gilt als Geschäftsprozess.

## Übersicht des Benachrichtigungsprozesses

1. Sie beginnen mit einer Logik-App, die über einen Timer als Trigger verfügt. Standardmäßig wird der Trigger stündlich ausgeführt.
2. Als Nächstes senden Sie eine HTTP-POST-Anfrage an die Logik-App.
3. Die Logik-App erledigt die gesamte Arbeit.

![Übersicht](./media/documentdb-change-notification/high-level-view.png)

### Werfen wir einen Blick auf die Funktionsweise dieser Logik-App.
In der folgenden Abbildung sehen Sie, dass der Workflow der Logik-App mehrere Schritte umfasst.

![Hauptlogikprozess](./media/documentdb-change-notification/main-logic-app-process.png)

Die Schritte lauten wie folgt:

1. Sie müssen den aktuellen UTC-DateTime-Wert aus einer API-App abrufen. Der Standardwert ist eine Stunde früher.

2. Der UTC-DateTime-Wert wird in ein Unix-TimeStamp-Format konvertiert. Dies ist das Standardformat für Zeitstempel in DocumentDB.

3. Sie SENDEN den Wert an eine API-App, die eine DocumentDB-Abfrage ausführt. Der Wert wird in einer Abfrage verwendet.

    ```SQL
     	SELECT * FROM Patients p WHERE (p._ts >= @unixTimeStamp)
    ```

    > [AZURE.NOTE] Das „\_ts“ stellt die TimeStamp-Metadaten für alle DocumentDB-Ressourcen dar.

4. Wenn Dokumente gefunden werden, wird der Antworttext an Ihren Azure-Blobspeicher gesendet.

    > [AZURE.NOTE] Blobspeicher setzt ein Azure Storage-Konto voraus. Sie müssen ein Azure-Blobspeicherkonto bereitstellen und ein neues Blob mit Namen „Patients“ hinzufügen. Weitere Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md) und [Erste Schritte mit Azure Blob Storage mit .NET](../storage/storage-dotnet-how-to-use-blobs.md).

5. Abschließend wird eine E-Mail gesendet, die den Empfänger über die Anzahl der gefundenen Dokumente informiert. Wenn keine Dateien gefunden wurden, würde der E-Mail-Text „0 Documents Found“ (Null Dokumente gefunden) lauten.

Da Sie nun den Workflow kennen gelernt haben, erfahren Sie im Folgenden, wie Sie ihn implementieren.

### Beginnen wir mit der Haupt-Logik-App

Wenn Sie mit Logik-Apps nicht vertraut sind: Sie stehen im [Azure Marketplace](https://portal.azure.com/) zur Verfügung, und unter [Was sind Logik-Apps?](../app-service-logic/app-service-logic-what-are-logic-apps.md) finden Sie weitere Informationen.

Wenn Sie eine neue Logik-App erstellen, werden Sie gefragt: **Wie möchten Sie beginnen?**

Wenn Sie in das Textfeld klicken, können Sie aus verschiedenen Ereignissen wählen. Wählen Sie für diese Logik-App, wie unten dargestellt, **Manuell – Wenn eine HTTP-Anforderung empfangen wird**.

![Beginnen](./media/documentdb-change-notification/starting-off.png)

### Entwurfsansicht der abgeschlossenen Logik-App
Wir machen nun einen Sprung nach vorn und betrachten die abgeschlossene Entwurfsansicht für die Logik-App mit dem Namen DocDB.

![Logik-App-Workflow](./media/documentdb-change-notification/workflow-expanded.png)

Beim Bearbeiten der Aktionen im Logik-App-Designer können Sie **Ausgaben** aus der HTTP-Anforderung oder aus der vorherigen Aktion auswählen, wie in der sendMail-Aktion unten dargestellt.

![Ausgaben wählen](./media/documentdb-change-notification/choose-outputs.png)

Vor jeder Aktion in Ihrem Workflow können Sie eine Entscheidung treffen; **Eine Aktion hinzufügen** oder **Eine Bedingung hinzufügen**, wie in der folgenden Abbildung dargestellt.

![Entscheidung treffen](./media/documentdb-change-notification/add-action-or-condition.png)

Wenn Sie **Eine Bedingung hinzufügen** wählen, wird ein Formular wie in der folgenden Abbildung angezeigt, in das Sie Ihren Code eingeben. Dies ist im Wesentlichen eine Geschäftsregel. Wenn Sie in ein Feld klicken, können Sie Parameter aus der vorherigen Aktion wählen. Sie können die Werte auch direkt eingeben.

![Bedingung hinzufügen](./media/documentdb-change-notification/condition1.png)

> [AZURE.NOTE] Außerdem haben Sie die Möglichkeit, alles in der Codeansicht einzugeben.

Betrachten wir nun die vollständige Logik-App in der Codeansicht.

```JSON
   
   	"$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
    "actions": {
        "Conversion": {
            "conditions": [
                {
                    "dependsOn": "GetUtcDate"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "currentdateTime": "@{body('GetUtcDate')}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "Createfile": {
            "conditions": [
                {
                    "expression": "@greater(length(body('GetDocuments')), 0)"
                },
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "@body('GetDocuments')",
                "host": {
                    "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                    },
                    "connection": {
                        "name": "@parameters('$connections')['azureblob']['connectionId']"
                    }
                },
                "method": "post",
                "path": "/datasets/default/files",
                "queries": {
                    "folderPath": "/patients",
                    "name": "Patient_@{guid()}.json"
                }
            },
            "type": "ApiConnection"
        },
        "GetDocuments": {
            "conditions": [
                {
                    "dependsOn": "Conversion"
                }
            ],
            "inputs": {
                "method": "post",
                "queries": {
                    "unixTimeStamp": "@body('Conversion')"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "GetUtcDate": {
            "conditions": [],
            "inputs": {
                "method": "get",
                "queries": {
                    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
                },
                "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
            },
            "metadata": {
                "apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1",
                "swaggerSource": "custom"
            },
            "type": "Http"
        },
        "sendMail": {
            "conditions": [
                {
                    "dependsOn": "GetDocuments"
                }
            ],
            "inputs": {
                "body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
                "headers": {
                    "Content-type": "application/x-www-form-urlencoded"
                },
                "method": "POST",
                "uri": "https://api.sendgrid.com/api/mail.send.json"
            },
            "type": "Http"
        }
    },
    "contentVersion": "1.0.0.0",
    "outputs": {
        "Results": {
            "type": "String",
            "value": "@{int(length(body('GetDocuments')))} Records Found"
        }
    },
    "parameters": {
        "$connections": {
            "defaultValue": {},
            "type": "Object"
        },
        "fromAddress": {
            "defaultValue": "user@msn.com",
            "type": "String"
        },
        "toAddress": {
            "defaultValue": "XXXXX@XXXXXXX.net",
            "type": "String"
        }
    },
    "triggers": {
        "manual": {
            "inputs": {
                "schema": {
                    "properties": {},
                    "required": [],
                    "type": "object"
                }
            },
            "type": "Manual"
        }
	
```

Wenn Ihnen nicht bekannt ist, was die verschiedenen Abschnitte im Code darstellen, finden Sie weitere Informationen in der Dokumentation [Workflow Definition Language](http://aka.ms/logicappsdocs) (Workflow-Definitionssprache).

Für diesen Workflow verwenden Sie einen [HTTP-Webhook-Trigger](https://sendgrid.com/blog/whats-webhook/). Wenn Sie den oben dargestellten Code betrachten, sehen Sie Parameter wie im folgenden Beispiel.

```C#

    =@{triggerBody()['Subject']}

```

`triggerBody()` stellt die Parameter dar, die im Hauptteil eines REST-POST an die Logik-App-REST-API enthalten sind. `()['Subject']` stellt das Feld dar. Alle diese Parameter bilden den Text im JSON-Format.

> [AZURE.NOTE] Mit einem Webhook haben Sie vollständigen Zugriff auf Header und Text der Triggeranforderung. In dieser Anwendung geht es Ihnen um den Text.

Wie bereits erwähnt, können Sie Parameter im Designer oder in der Codeansicht zuweisen. Bei Zuweisung in der Codeansicht definieren Sie, welche Eigenschaften einen Wert benötigen, wie im folgenden Codebeispiel gezeigt.

```JSON

	"triggers": {
		"manual": {
		    "inputs": {
			"schema": {
			    "properties": {
			"Subject": {
			    "type" : "String"	

			}
			},
			    "required": [
			"Subject"
			     ],
			    "type": "object"
			}
		    },
		    "type": "Manual"
		}
	    }
```

Sie erstellen ein JSON-Schema, das aus dem HTTP-POST-Text übergeben wird. Um den Trigger auszulösen, benötigen Sie eine Rückruf-URL. Später in diesem Tutorial erfahren Sie, wie Sie sie generieren.

## Aktionen
Betrachten wir nun, was die einzelnen Aktionen in unserer Logik-App bewirken.

### GetUTCDate

**Designeransicht**

![](./media/documentdb-change-notification/getutcdate.png)

**Codeansicht**

```JSON

	"GetUtcDate": {
		    "conditions": [],
		    "inputs": {
			"method": "get",
			"queries": {
			    "hoursBack": "@{int(triggerBody()['GetUtcDate_HoursBack'])}"
			},
			"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization"
		    },
		    "metadata": {
			"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
		    },
		    "type": "Http"
		},

```

Diese HTTP-Aktion führt einen GET-Vorgang aus. Sie ruft die API-APP-GetUtcDate-Methode auf. Der URI verwendet die Eigenschaft „GetUtcDate\_HoursBack“, die dem Text des Triggers übergeben wird. Der Wert „GetUtcDate\_HoursBack“ wird in der ersten Logik-App festgelegt. Später im Tutorial erfahren Sie mehr über die Trigger-Logik-App.

Diese Aktion ruft Ihre API-App zur Rückgabe des UTC-Datumszeichenfolgenwerts auf.

#### Vorgänge

**Anforderung**

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Authorization",
	    "method": "get",
	    "queries": {
		  "hoursBack": "24"
	    }
	}

```

**Antwort**

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": "Fri, 15 Jan 2016 23:47:33 GMT"
	}

```

Im nächsten Schritt wird der UTC-DateTime-Wert in Unix-TimeStamp konvertiert, was ein .NET-Double-Typ ist.

### Konvertierung

##### Designeransicht

![Konvertierung](./media/documentdb-change-notification/conversion.png)

##### Codeansicht

```JSON

	"Conversion": {
	    "conditions": [
		{
		    "dependsOn": "GetUtcDate"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "currentDateTime": "@{body('GetUtcDate')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

In diesem Schritt übergeben Sie den von „GetUTCDate“ zurückgegebenen Wert. Es gibt eine Bedingung „dependsOn“, was bedeutet, dass die GetUTCDate-Aktion erfolgreich abgeschlossen werden muss. Wenn nicht, wird diese Aktion übersprungen.

Diese Aktion ruft Ihre API-App zur Abwicklung der Konvertierung auf.

#### Vorgänge

##### Request

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Conversion",
	    "method": "post",
	    "queries": {
		"currentDateTime": "Fri, 15 Jan 2016 23:47:33 GMT"
	    }
	}   
```

##### Antwort

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		  "pragma": "no-cache",
		  "cache-Control": "no-cache",
		  "date": "Fri, 26 Feb 2016 15:47:33 GMT",
		  "server": "Microsoft-IIS/8.0",
		  "x-AspNet-Version": "4.0.30319",
		  "x-Powered-By": "ASP.NET"
	    },
	    "body": 1452901653
	}
```

In der nächsten Aktion werden Sie einen POST-Vorgang ausführen, der an die API-App gerichtet ist.

### GetDocuments 

##### Designeransicht

![Dokumente abrufen](./media/documentdb-change-notification/getdocuments.png)

##### Codeansicht

```JSON

	"GetDocuments": {
	    "conditions": [
		{
		    "dependsOn": "Conversion"
		}
	    ],
	    "inputs": {
		"method": "post",
		"queries": {
		    "unixTimeStamp": "@{body('Conversion')}"
		},
		"uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient"
	    },
	    "metadata": {
		"apiDefinitionUrl": "https://docdbnotificationapi-debug.azurewebsites.net/swagger/docs/v1"
	    },
	    "type": "Http"
	},

```

Für die GetDocuments-Aktion übergeben Sie den Antworttext aus der Konvertierungsaktion. Dies ist ein Parameter im URI:

 
```C#

	unixTimeStamp=@{body('Conversion')}

```

Die QueryDocuments-Aktion führt einen HTTP-POST-Vorgang aus, der an die API-App gerichtet ist.

Die aufgerufene Methode ist **QueryForNewPatientDocuments**.

#### Vorgänge

##### Request

```JSON

	{
	    "uri": "https://docdbnotificationapi-debug.azurewebsites.net/api/Patient",
	    "method": "post",
	    "queries": {
		"unixTimeStamp": "1452901653"
	    }
	}
```

##### Antwort

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "Microsoft-IIS/8.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": [
		{
		    "id": "xcda",
		    "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
		    "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
		    "_ts": 1454874620,
		    "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
		    "resourceType": "Patient",
		    "text": {
			"status": "generated",
			"div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
		    },
		    "identifier": [
			{
			    "use": "usual",
			    "type": {
				"coding": [
				    {
					"system": "http://hl7.org/fhir/v2/0203",
					"code": "MR"
				    }
				]
			    },
			    "system": "urn:oid:2.16.840.1.113883.19.5",
			    "value": "12345"
			}
		    ],
		    "active": true,
		    "name": [
			{
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Die nächste Aktion ist das Speichern der Dokumente im [Azure-Blobspeicher](https://azure.microsoft.com/services/storage/).

> [AZURE.NOTE] Blobspeicher setzt ein Azure Storage-Konto voraus. Sie müssen ein Azure-Blobspeicherkonto bereitstellen und ein neues Blob mit Namen „Patients“ hinzufügen. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/storage-dotnet-how-to-use-blobs.md).

### Erstellen der Datei

##### Designeransicht

![Datei erstellen](./media/documentdb-change-notification/createfile.png)

##### Codeansicht

```JSON

	{
    "host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },

```

Der Code wird aus der Aktion im Designer generiert. Sie müssen den Code nicht ändern.

Wenn Ihnen die Verwendung der Azure-Blob-API nicht vertraut ist, finden Sie weitere Informationen unter [Get started with the Azure blob storage API](../connectors/create-api-azureblobstorage.md) (Erste Schritte mit der Azure-Blobspeicher-API).

#### Vorgänge

##### Request

```JSON

	"host": {
        "api": {
            "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
        },
        "connection": {
            "name": "subscriptions/fxxxxxc079-4e5d-b002-xxxxxxxxxx/resourceGroups/Api-Default-Central-US/providers/Microsoft.Web/connections/azureblob"
        }
    },
    "method": "post",
    "path": "/datasets/default/files",
    "queries": {
        "folderPath": "/patients",
        "name": "Patient_17513174-e61d-4b56-88cb-5cf383db4430.json"
    },
    "body": [
        {
            "id": "xcda",
            "_rid": "vCYLAP2k6gAXAAAAAAAAAA==",
            "_self": "dbs/vCYLAA==/colls/vCYLAP2k6gA=/docs/vCYLAP2k6gAXAAAAAAAAAA==/",
            "_ts": 1454874620,
            "_etag": ""00007d01-0000-0000-0000-56b79ffc0000"",
            "resourceType": "Patient",
            "text": {
                "status": "generated",
                "div": "<div>\n      \n      <p>Henry Levin the 7th</p>\n    \n    </div>"
            },
            "identifier": [
                {
                    "use": "usual",
                    "type": {
                        "coding": [
                            {
                                "system": "http://hl7.org/fhir/v2/0203",
                                "code": "MR"
                            }
                        ]
                    },
                    "system": "urn:oid:2.16.840.1.113883.19.5",
                    "value": "12345"
                }
            ],
            "active": true,
            "name": [
                {
                    "family": [
                        "Levin"
                    ],
                    "given": [
                        "Henry"
                    ]
                }
            ],
            "gender": "male",
            "birthDate": "1932-09-24",
            "managingOrganization": {
                "reference": "Organization/2.16.840.1.113883.19.5",
                "display": "Good Health Clinic"
            }
        },….


```

##### Antwort

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-request-id": "2b2f7c57-2623-4d71-8e53-45c26b30ea9d",
		"cache-Control": "no-cache",
		"date": "Fri, 26 Feb 2016 15:47:36 GMT",
		"set-Cookie": "ARRAffinity=29e552cea7db23196f7ffa644003eaaf39bc8eb6dd555511f669d13ab7424faf;Path=/;Domain=127.0.0.1",
		"server": "Microsoft-HTTPAPI/2.0",
		"x-AspNet-Version": "4.0.30319",
		"x-Powered-By": "ASP.NET"
	    },
	    "body": {
		"Id": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE",
		"Name": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"DisplayName": "Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"Path": "/Patient/Patient_47a2a0dc-640d-4f01-be38-c74690d085cb.json",
		"LastModified": "2016-02-26T15:47:36.215Z",
		"Size": 65647,
		"MediaType": "application/octet-stream",
		"IsFolder": false,
		"ETag": ""c-g_a-1OtaH-kNQ4WBoXLp3Zv9s/MTQ1NjUwMTY1NjIxNQ"",
		"FileLocator": "0B0nBzHyMV-_NRGRDcDNMSFAxWFE"
	    }
	}
```

Im letzten Schritt senden Sie eine E-Mail-Benachrichtigung.

### sendEmail

##### Designeransicht

![E-Mail senden](./media/documentdb-change-notification/sendemail.png)

##### Codeansicht

```JSON


	"sendMail": {
	    "conditions": [
		{
		    "dependsOn": "GetDocuments"
		}
	    ],
	    "inputs": {
		"body": "api_user=@{triggerBody()['sendgridUsername']}&api_key=@{triggerBody()['sendgridPassword']}&from=@{parameters('fromAddress')}&to=@{triggerBody()['EmailTo']}&subject=@{triggerBody()['Subject']}&text=@{int(length(body('GetDocuments')))} Documents Found",
		"headers": {
		    "Content-type": "application/x-www-form-urlencoded"
		},
		"method": "POST",
		"uri": "https://api.sendgrid.com/api/mail.send.json"
	    },
	    "type": "Http"
	}
```

In dieser Aktion senden Sie eine E-Mail-Benachrichtigung. Sie verwenden [SendGrid](https://sendgrid.com/marketing/sendgrid-services?cvosrc=PPC.Bing.sendgrib&cvo_cid=SendGrid%20-%20US%20-%20Brand%20-%20&mc=Paid%20Search&mcd=BingAds&keyword=sendgrib&network=o&matchtype=e&mobile=&content=&search=1&utm_source=bing&utm_medium=cpc&utm_term=%5Bsendgrib%5D&utm_content=%21acq%21v2%2134335083397-8303227637-1649139544&utm_campaign=SendGrid+-+US+-+Brand+-+%28English%29).

Der Code hierfür wurde mithilfe einer Vorlage für Logik-App und SendGrid generiert, die im [101-logic-app-sendgrid-Github-Repository](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sendgrid) enthalten ist.
 
Der HTTP-Vorgang ist ein POST-Vorgang.

Die Autorisierungsparameter sind in den Triggereigenschaften enthalten.

```JSON

	},
		"sendgridPassword": {
			 "type": "SecureString"
		 },
		 "sendgridUsername": {
			"type": "String"
		 }

		In addition, other parameters are static values set in the Parameters section of the Logic App. These are:
		},
		"toAddress": {
		    "defaultValue": "XXXX@XXXX.com",
		    "type": "String"
		},
		"fromAddress": {
		    "defaultValue": "XXX@msn.com",
		    "type": "String"
		},
		"emailBody": {
		    "defaultValue": "@{string(concat(int(length(actions('QueryDocuments').outputs.body)) Records Found),'/n', actions('QueryDocuments').outputs.body)}",
		    "type": "String"
		},

```

In „emailBody“ wird die von der Abfrage zurückgegebene Anzahl der Dokumente, die „0“ oder mehr sein kann, mit „Records Found“ (gefundene Datensätze) verkettet. Die verbleibenden Parameter werden gemäß der Triggerparameter festgelegt.

Diese Aktion hängt von der Aktion **GetDocuments** ab.

#### Vorgänge

##### Request
```JSON

	{
	    "uri": "https://api.sendgrid.com/api/mail.send.json",
	    "method": "POST",
	    "headers": {
		"Content-type": "application/x-www-form-urlencoded"
	    },
	    "body": "api_user=azureuser@azure.com&api_key=Biz@Talk&from=user@msn.com&to=XXXX@XXXX.com&subject=New Patients&text=37 Documents Found"
	}

```

##### Antwort

```JSON

	{
	    "statusCode": 200,
	    "headers": {
		"connection": "keep-alive",
		"x-Frame-Options": "DENY,DENY",
		"access-Control-Allow-Origin": "https://sendgrid.com",
		"date": "Fri, 26 Feb 2016 15:47:35 GMT",
		"server": "nginx"
	    },
	    "body": {
		"message": "success"
	    }
	}
```

Schließlich möchten Sie die Ergebnisse Ihrer Logik-App im Azure-Portal sehen. Zu diesem Zweck fügen Sie dem Abschnitt „outputs“ einen Parameter hinzu.


```JSON

	"outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{int(length(actions('QueryDocuments').outputs.body))} Records Found"
		}

```

Dieser gibt den gleichen Wert zurück, der an den E-Mail-Text gesendet wird. Die folgende Abbildung zeigt ein Beispiel mit „29 Records Found“ (29 gefundenen Datensätzen).

![Ergebnisse](./media/documentdb-change-notification/logic-app-run.png)

## Metriken
Sie können die Überwachung für die Haupt-Logik-App im Portal konfigurieren. So können Sie die Ausführungswartezeit und andere Ereignisse wie in der folgenden Abbildung dargestellt anzeigen.

![](./media/documentdb-change-notification/metrics.png)

## DocDb-Trigger

Diese Logik-App ist der Trigger, der den Workflow in Ihrer Haupt-Logik-App startet.

Die folgende Abbildung zeigt die Designeransicht.

![](./media/documentdb-change-notification/trigger-recurrence.png)

```JSON

	{
	    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2015-08-01-preview/workflowdefinition.json#",
	    "actions": {
		"Http": {
		    "conditions": [],
		    "inputs": {
			"body": {
			    "EmailTo": "XXXXXX@XXXXX.net",
			    "GetUtcDate_HoursBack": "24",
			    "Subject": "New Patients",
			    "sendgridPassword": "********",
			    "sendgridUsername": "azureuser@azure.com"
			},
			"method": "POST",
			"uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c"
		    },
		    "type": "Http"
		}
	    },
	    "contentVersion": "1.0.0.0",
	    "outputs": {
		"Results": {
		    "type": "String",
		    "value": "@{body('Http')['status']}"
		}
	    },
	    "parameters": {},
	    "triggers": {
		"recurrence": {
		    "recurrence": {
			"frequency": "Hour",
			"interval": 24
		    },
		    "type": "Recurrence"
		}
	    }
	}

```

Der Trigger wird für eine Serie von 24 Stunden festgelegt. Die Aktion ist ein HTTP-POST, wobei die Rückruf-URL für die Haupt-Logik-App verwendet wird. Der Text enthält die Parameter, die im JSON-Schema angegeben werden.

#### Vorgänge

##### Request

```JSON

	{
	    "uri": "https://prod-01.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=ObTlihr529ATIuvuG-dhxOgBL4JZjItrvPQ8PV6973c",
	    "method": "POST",
	    "body": {
		"EmailTo": "XXXXXX@XXXXX.net",
		"GetUtcDate_HoursBack": "24",
		"Subject": "New Patients",
		"sendgridPassword": "********",
		"sendgridUsername": "azureuser@azure.com"
	    }
	}

```

##### Antwort

```JSON

	{
	    "statusCode": 202,
	    "headers": {
		"pragma": "no-cache",
		"x-ms-ratelimit-remaining-workflow-writes": "7486",
		"x-ms-ratelimit-burst-remaining-workflow-writes": "1248",
		"x-ms-request-id": "westus:2d440a39-8ba5-4a9c-92a6-f959b8d2357f",
		"cache-Control": "no-cache",
		"date": "Thu, 25 Feb 2016 21:01:06 GMT"
	    }
	}
```

Betrachten wir nun die API-App.

## DocDBNotificationApi

Es gibt zwar mehrere Vorgänge in der App, doch Sie verwenden nur drei.

* GetUtcDate
* ConvertToTimeStamp
* QueryForNewPatientDocuments

### DocDBNotificationApi-Vorgänge
Betrachten wir nun die Swagger-Dokumentation

> [AZURE.NOTE] Damit Sie die Vorgänge extern aufrufen können, müssen Sie einen für CORS zulässigen Ursprungswert „*“ (ohne Anführungszeichen) in den Einstellungen Ihrer API-App hinzufügen, wie in der folgenden Abbildung dargestellt.

![CORS-Konfiguration](./media/documentdb-change-notification/cors.png)

#### GetUtcDate

![G](./media/documentdb-change-notification/getutcdateswagger.png)

#### ConvertToTimeStamp

![UTC-Datum abrufen](./media/documentdb-change-notification/converion-swagger.png)

#### QueryForNewPatientDocuments

![Abfrage](./media/documentdb-change-notification/patientswagger.png)

Betrachten wir nun den Code hinter diesem Vorgang.

#### GetUtcDate

```C#

    /// <summary>
	/// Gets the current UTC Date value
	/// </summary>
	/// <returns></returns>
	[H ttpGet]
	[Metadata("GetUtcDate", "Gets the current UTC Date value minus the Hours Back")]
	[SwaggerOperation("GetUtcDate")]
	[SwaggerResponse(HttpStatusCode.OK, type: typeof (string))]
	[SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
	public string GetUtcDate(
	   [Metadata("Hours Back", "How many hours back from the current Date Time")] int hoursBack)
	{


	    return DateTime.UtcNow.AddHours(-hoursBack).ToString("r");
	}
```

Dieser Vorgang gibt einfach den aktuellen UTC-DateTime-Wert abzüglich des HoursBack-Werts zurück.

#### ConvertToTimeStamp

``` C#

        /// <summary>
        ///     Converts DateTime to double
        /// </summary>
        /// <param name="currentdateTime"></param>
        /// <returns></returns>
        [Metadata("Converts Universal DateTime to number")]
        [SwaggerResponse(HttpStatusCode.OK, null, typeof (double))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "DateTime is invalid")]
        [SwaggerResponse(HttpStatusCode.InternalServerError)]
        [SwaggerOperation(nameof(ConvertToTimestamp))]
        public double ConvertToTimestamp(
            [Metadata("currentdateTime", "DateTime value to convert")] string currentdateTime)
        {
            double result;

            try
            {
                var uncoded = HttpContext.Current.Server.UrlDecode(currentdateTime);

                var newDateTime = DateTime.Parse(uncoded);
                //create Timespan by subtracting the value provided from the Unix Epoch
                var span = newDateTime - new DateTime(1970, 1, 1, 0, 0, 0, 0).ToLocalTime();

                //return the total seconds (which is a UNIX timestamp)
                result = span.TotalSeconds;
            }
            catch (Exception e)
            {
                throw new Exception("unable to convert to Timestamp", e.InnerException);
            }

            return result;
        }

```

Dieser Vorgang konvertiert die Antwort aus dem GetUtcDate-Vorgang in einen Double-Wert.

#### QueryForNewPatientDocuments

```C#

	    /// <summary>
        ///     Query for new Patient Documents
        /// </summary>
        /// <param name="unixTimeStamp"></param>
        /// <returns>IList</returns>
        [Metadata("QueryForNewDocuments",
            "Query for new Documents where the Timestamp is greater than or equal to the DateTime value in the query parameters."
            )]
        [SwaggerOperation("QueryForNewDocuments")]
        [SwaggerResponse(HttpStatusCode.OK, type: typeof (Task<IList<Document>>))]
        [SwaggerResponse(HttpStatusCode.BadRequest, "The syntax of the SQL Statement is incorrect")]
        [SwaggerResponse(HttpStatusCode.NotFound, "No Documents were found")]
        [SwaggerResponse(HttpStatusCode.InternalServerError, "Internal Server Operation Error")]
        // ReSharper disable once ConsiderUsingAsyncSuffix
        public IList<Document> QueryForNewPatientDocuments(
            [Metadata("UnixTimeStamp", "The DateTime value used to search from")] double unixTimeStamp)
        {
            var context = new DocumentDbContext();
            var filterQuery = string.Format(InvariantCulture, "SELECT * FROM Patient p WHERE p._ts >=  {0}",
                unixTimeStamp);
            var options = new FeedOptions {MaxItemCount = -1};


            var collectionLink = UriFactory.CreateDocumentCollectionUri(DocumentDbContext.DatabaseId,
                DocumentDbContext.CollectionId);

            var response =
                context.Client.CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();

            return response.ToList();
	}

```

Dieser Vorgang verwendet das [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) zum Erstellen einer Dokumentabfrage.

```C#
     CreateDocumentQuery<Document>(collectionLink, filterQuery, options).AsEnumerable();
```

Die Antwort aus dem Vorgang „ConvertToTimeStamp“ („unixTimeStamp“) wird übergeben. Der Vorgang gibt eine Liste von Dokumenten zurück, `IList<Document>`.

Zuvor haben wir die CallbackURL behandelt. Um den Workflow in der Haupt-Logik-App zu starten, müssen Sie sie mithilfe der CallbackURL aufrufen.

## CallbackURL

Zu Beginn benötigen Sie Ihr Azure AD-Token. Es kann schwierig, sein dieses Token abzurufen. Ich suchte nach einer einfachen Methode, und Jeff Hollan, ein Azure-Logik-App-Programm-Manager, empfahl die Verwendung von [armclient](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) in PowerShell. Sie können das Tool nach den bereitgestellten Anweisungen installieren.

Die Vorgänge, die Sie verwenden möchten, sind die Anmeldung bei der ARM-API und deren Aufruf.
 
Anmeldung: Sie verwenden die gleichen Anmeldeinformationen wie für die Anmeldung beim Azure-Portal.

Der Aufruf der ARM-API generiert Ihre CallBackURL.

In PowerShell erfolgt der Aufruf wie folgt:

```powershell

	ArmClient.exe post https://management.azure.com/subscriptions/[YOUR SUBSCRIPTION ID/resourcegroups/[YOUR RESOURCE GROUP]/providers/Microsoft.Logic/workflows/[YOUR LOGIC APP NAME/triggers/manual/listcallbackurl?api-version=2015-08-01-preview

```

Das Ergebnis sollte folgendermaßen aussehen:

```powershell

	https://prod-02.westus.logic.azure.com:443/workflows/12a1de57e48845bc9ce7a247dfabc887/triggers/manual/run?api-version=2015-08-01-prevaiew&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=XXXXXXXXXXXXXXXXXXX

```

Mit einem Tool wie [postman](http://www.getpostman.com/) können Sie Ihre Haupt-Logik-App wie in der folgenden Abbildung dargestellt testen.

![Postman](./media/documentdb-change-notification/newpostman.png)

In der folgenden Tabelle werden die Triggerparameter aufgeführt, aus denen der Text der DocDB-Trigger-Logik-App besteht.

Parameter | Beschreibung 
--- | --- 
GetUtcDate\_HoursBack | Legt die Anzahl der Stunden für das Startdatum der Suche fest
sendgridUsername | Legt die Anzahl der Stunden für das Startdatum der Suche fest
sendgridPassword | Der Benutzername für die SendGrid-E-Mail
EmailTo | Die E-Mail-Adresse, die die E-Mail-Benachrichtigung empfängt
Betreff | Der Betreff der E-Mail

## Anzeigen der Patientendaten im Azure-Blobdienst

Wechseln Sie zu Ihrem Azure Storage-Konto, und wählen Sie Blobs unter Diensten wie in der folgenden Abbildung dargestellt aus.

![Speicherkonto](./media/documentdb-change-notification/docdbstorageaccount.png)

Sie können die Patienten-Blobdateiinformationen wie unten dargestellt anzeigen.

![Blob-Dienst](./media/documentdb-change-notification/blobservice.png)


## Zusammenfassung

In dieser exemplarischen Vorgehensweise haben Sie Folgendes gelernt:

* Es ist möglich, Benachrichtigungen in DocumentDB zu implementieren.
* Mithilfe von Logik-Apps können Sie den Prozess automatisieren.
* Mithilfe von Logik-Apps können Sie die Zeit reduzieren, die benötigt wird, um eine Anwendung bereitzustellen.
* Mithilfe von HTTP können Sie mühelos eine API-App innerhalb einer Logik-App nutzen.
* Sie können mühelos eine CallBackURL erstellen, die den HTTP-Listener ersetzt.
* Sie können mühelos benutzerdefinierte Workflows mit dem Logik-Apps-Designer erstellen.

Der springende Punkt ist, vorauszuplanen und den Workflow im Modell darzustellen.

## Nächste Schritte
Bitte verwenden Sie den Logik-App-Code, den Sie bei [Github](https://github.com/HEDIDIN/DocDbNotifications) herunterladen können. Ich lade Sie ein, an der Anwendung weiterzuarbeiten und Änderungen an das Repository zu senden.

Weitere Informationen zu DocumentDB finden Sie im [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

<!---HONumber=AcomDC_0316_2016-->