<properties 
	pageTitle="App Service – API-App-Trigger | Microsoft Azure" 
	description="Hier wird erläutert, wie Trigger in einer API-App in Azure App Service implementiert werden." 
	services="app-service\logic" 
	documentationCenter=".net" 
	authors="guangyang"
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="na" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="guayan"/>

# Azure App Service – API-App-Trigger

## Übersicht

Dieser Artikel beschreibt die Implementierung von API-App-Triggern und deren Nutzung über eine Logik-App.

Alle Codeausschnitte in diesem Thema wurden aus dem [FileWatcher API-App-Codebeispiel](http://go.microsoft.com/fwlink/?LinkId=534802) kopiert.

Beachten Sie, dass Sie folgendes Nuget-Paket herunterladen müssen, damit Sie den Code in diesem Artikel erstellen und ausführen können: [http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/).

## Was sind API-App-Trigger?

Es ist ein übliches Szenario, dass eine API-App ein Ereignis auslöst, damit Clients der API-App als Reaktion auf das Ereignis die entsprechende Aktion durchführen können. Der REST-API-basierte Mechanismus, der dieses Szenario unterstützt, wird als API-App-Trigger bezeichnet.

Ein Beispiel: Ihr Clientcode verwendet die [Twitter Connector-API-App](../app-service-logic/app-service-logic-connector-twitter.md) und der Code muss auf eine Aktion basierend auf neuen Tweets ausführen, die bestimmte Wörter enthalten. In diesem Fall können Sie einen Abfrage- oder Pushtrigger einrichten, um diese Anforderung leichter zu erfüllen.

## Abfragetrigger im Vergleich zu Pushtriggern

Zurzeit werden zwei Arten von Triggern unterstützt:

- Abfragetrigger – Der Client fragt die API-App ab, um eine Benachrichtigung zu erhalten, dass ein Ereignis ausgelöst wurde. 
- Pushtrigger – Der Client wird durch die API-App benachrichtigt, wenn ein Ereignis ausgelöst wurde. 

### Abfragetrigger

Ein Abfragetrigger wird als reguläre REST-API implementiert und erwartet, dass die Clients (z. B. eine App-Logik) ihn abfragen, um eine Benachrichtigung zu erhalten. Während der Client sein Zustand beibehalten kann, ist der Abfragetrigger selbst zustandslos.

Die folgenden Informationen in Bezug auf die Anforderungs- und Antwortpakete veranschaulichen einige wichtige Aspekte des Abfragetriggervertrags:

- Request
    - HTTP-Methode: GET
    - Parameter
        - triggerState – Mit diesem optionalen Parameter können Clients ihren Status angeben. Der Abfragetrigger entscheidet aufgrund dieses Status, ob eine Benachrichtigung zurückgegeben wird oder nicht.
        - API-spezifische Parameter
- Antwort
    - Statuscode **200** – Die Anforderung ist gültig, und es gibt eine Benachrichtigung des Triggers. Der Inhalt der Benachrichtigung ist der Antworttext. Ein "Retry-After"-Header in der Antwort weist darauf hin, dass mit einem weiteren Anforderungsaufruf zusätzliche Benachrichtigungsdaten abgerufen werden müssen.
    - Statuscode **202** – Die Anforderung ist gültig, es gibt jedoch keine neue Benachrichtigung des Triggers.
    - Statuscode **4xx** – Die Anforderung ist ungültig. Der Client sollte die Anforderung nicht wiederholen.
    - Statuscode **5xx** – Die Anforderung hat zu einem internen Serverfehler und/oder einem vorübergehenden Problem geführt. Der Client sollte die Anforderung wiederholen.

Der folgende Codeausschnitt ist ein Beispiel für die Implementierung eines Abfragetriggers.

    // Implement a poll trigger.
    [HttpGet]
    [Route("api/files/poll/TouchedFiles")]
    public HttpResponseMessage TouchedFilesPollTrigger(
        // triggerState is a UTC timestamp
        string triggerState,
        // Additional parameters
        string searchPattern = "*")
    {
        // Check to see whether there is any file touched after the timestamp.
        var lastTriggerTimeUtc = DateTime.Parse(triggerState).ToUniversalTime();
        var touchedFiles = Directory.EnumerateFiles(rootPath, searchPattern, SearchOption.AllDirectories)
            .Select(f => FileInfoWrapper.FromFileInfo(new FileInfo(f)))
            .Where(fi => fi.LastAccessTimeUtc > lastTriggerTimeUtc);

        // If there are files touched after the timestamp, return their information.
        if (touchedFiles != null && touchedFiles.Count() != 0)
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventTriggered(new { files = touchedFiles });
        }
        // If there are no files touched after the timestamp, tell the caller to poll again after 1 mintue.
        else
        {
            // Extension method provided by the AppService service SDK.
            return this.Request.EventWaitPoll(new TimeSpan(0, 1, 0));
        }
    }

Um diesen Abfragetrigger zu testen, gehen Sie folgendermaßen vor:

1. Stellen Sie die API-App mit der Authentifizierungseinstellung **Öffentlich (anonym)** bereit.
2. Rufen Sie den **touch**-Vorgang auf, um eine Datei zu bearbeiten. Die folgende Abbildung zeigt eine Beispielanforderung über Postman. ![Aufrufen des Touchvorgangs über Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
3. Rufen Sie den Abfragetrigger mit dem **triggerState**-Parameter auf, der auf einen Zeitstempel vor Schritt 2 festgelegt wurde. Die folgende Abbildung zeigt die Beispielanforderung über Postman. ![Aufrufen des Abfragetriggers über Postman](./media/app-service-api-dotnet-triggers/callpolltriggerfrompostman.PNG)

### Pushtrigger

Ein Pushtrigger wird als reguläre REST-API implementiert, die Benachrichtigungen an Clients sendet, die für den Erhalt von Benachrichtigungen nach der Auslösung bestimmter Ereignisse registriert wurden.

Die folgenden Informationen in Bezug auf die Anforderungs- und Antwortpakete veranschaulichen einige wichtige Aspekte des Pushtriggervertrags:

- Request
    - HTTP-Methode: PUT
    - Parameter
        - triggerId: erforderlich – nicht transparente Zeichenfolge (z. B. eine GUID), welche die Registrierung eines Pushtriggers darstellt.
        - callbackUrl: erforderlich – URL des Rückrufs, der bei Auslösung des Ereignisses aufgerufen werden soll. Der Aufruf ist ein einfacher POST-HTTP-Aufruf.
        - API-spezifische Parameter
- Antwort
    - Statuscode **200** – Die Anforderung zur Registrierung des Client war erfolgreich.
    - Statuscode **4xx** – Die Anforderung ist ungültig. Der Client sollte die Anforderung nicht wiederholen.
    - Statuscode **5xx** – Die Anforderung hat zu einem internen Serverfehler und/oder einem vorübergehenden Problem geführt. Der Client sollte die Anforderung wiederholen.
- Rückruf
    - HTTP-Methode: POST
    - Anforderungstext: Benachrichtigungsinhalt.

Der folgende Codeausschnitt ist ein Beispiel für die Implementierung eines Pushtriggers.

    // Implement a push trigger.
    [HttpPut]
    [Route("api/files/push/TouchedFiles/{triggerId}")]
    public HttpResponseMessage TouchedFilesPushTrigger(
        // triggerId is an opaque string.
        string triggerId,
        // A helper class provided by the AppService service SDK.
        // Here it defines the input of the push trigger is a string and the output to the callback is a FileInfoWrapper object.
        [FromBody]TriggerInput<string, FileInfoWrapper> triggerInput)
    {
        // Register the trigger to some trigger store.
        triggerStore.RegisterTrigger(triggerId, rootPath, triggerInput);

        // Extension method provided by the AppService service SDK indicating the registration is completed.
        return this.Request.PushTriggerRegistered(triggerInput.GetCallback());
    }

    // A simple in-memory trigger store.
    public class InMemoryTriggerStore
    {
        private static InMemoryTriggerStore instance;

        private IDictionary<string, FileSystemWatcher> _store;

        private InMemoryTriggerStore()
        {
            _store = new Dictionary<string, FileSystemWatcher>();
        }

        public static InMemoryTriggerStore Instance
        {
            get
            {
                if (instance == null)
                {
                    instance = new InMemoryTriggerStore();
                }
                return instance;
            }
        }

        // Register a push trigger.
        public void RegisterTrigger(string triggerId, string rootPath,
            TriggerInput<string, FileInfoWrapper> triggerInput)
        {
            // Use FileSystemWatcher to listen to file change event.
            var filter = string.IsNullOrEmpty(triggerInput.inputs) ? "*" : triggerInput.inputs;
            var watcher = new FileSystemWatcher(rootPath, filter);
            watcher.IncludeSubdirectories = true;
            watcher.EnableRaisingEvents = true;
            watcher.NotifyFilter = NotifyFilters.LastAccess;

            // When some file is changed, fire the push trigger.
            watcher.Changed +=
                (sender, e) => watcher_Changed(sender, e,
                    Runtime.FromAppSettings(),
                    triggerInput.GetCallback());

            // Assoicate the FileSystemWatcher object with the triggerId.
            _store[triggerId] = watcher;

        }

        // Fire the assoicated push trigger when some file is changed.
        void watcher_Changed(object sender, FileSystemEventArgs e,
            // AppService runtime object needed to invoke the callback.
            Runtime runtime,
            // The callback to invoke.
            ClientTriggerCallback<FileInfoWrapper> callback)
        {
            // Helper method provided by AppService service SDK to invoke a push trigger callback.
            callback.InvokeAsync(runtime, FileInfoWrapper.FromFileInfo(new FileInfo(e.FullPath)));
        }
    }

Um diesen Abfragetrigger zu testen, gehen Sie folgendermaßen vor:

1. Stellen Sie die API-App mit der Authentifizierungseinstellung **Öffentlich (anonym)** bereit.
2. Navigieren Sie zu [http://requestb.in/](http://requestb.in/), um ein RequestBin-Element zu erstellen, das als Rückruf-URL verwendet wird.
3. Rufen Sie den Pushtrigger mit der GUID als **triggerId** und der RequestBin-URL als **callbackUrl** auf. ![Aufrufen des Pushtriggers über Postman](./media/app-service-api-dotnet-triggers/callpushtriggerfrompostman.PNG)
4. Rufen Sie den **touch**-Vorgang auf, um eine Datei zu bearbeiten. Die folgende Abbildung zeigt eine Beispielanforderung über Postman. ![Aufrufen des Touchvorgangs über Postman](./media/app-service-api-dotnet-triggers/calltouchfilefrompostman.PNG)
5. Überprüfen Sie das RequestBin-Element, um sicherzustellen, dass der Pushtriggerrückruf mit der Ausgabe der Eigenschaften aufgerufen wird. ![Aufrufen des Abfragetriggers über Postman](./media/app-service-api-dotnet-triggers/pushtriggercallbackinrequestbin.PNG)

### Beschreiben der Trigger in der API-Definition

Navigieren Sie nach der Implementierung der Trigger und der Bereitstellung der API-App in Azure zum Blatt **API-Definition** im Azure-Vorschauportal. Dort sehen Sie, dass die Trigger auf der Benutzeroberfläche, die durch die Swagger 2.0-Definition der API-App gesteuert wird, automatisch erkannt werden.

![Blatt "API-Definition"](./media/app-service-api-dotnet-triggers/apidefinitionblade.PNG)

Wenn Sie auf die Schaltfläche **Swagger herunterladen** klicken und die JSON-Datei öffnen, sehen Sie Ergebnisse ähnlich den folgenden:

    "/api/files/poll/TouchedFiles": {
      "get": {
        "operationId": "Files_TouchedFilesPollTrigger",
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    },
    "/api/files/push/TouchedFiles/{triggerId}": {
      "put": {
        "operationId": "Files_TouchedFilesPushTrigger",
        ...
        "x-ms-scheduler-trigger": "push"
      }
    }

Mit der Erweiterungseigenschaft **x-ms-scheduler-trigger** werden Trigger in der API-Definition beschrieben. Diese Eigenschaft wird automatisch vom API-App-Gateway hinzugefügt, wenn Sie die API-Definition über das Gateway anfordern und die Anforderung eines der folgenden Kriterien erfüllt. (Sie können diese Eigenschaft auch manuell hinzufügen.)

- Abfragetrigger
    - Wenn die HTTP-Methode **GET** lautet.
    - Wenn die **operationId**-Eigenschaft die Zeichenfolge **trigger** enthält.
    - Wenn die **parameters**-Eigenschaft einen Parameter enthält, dessen **name**-Eigenschaft auf **triggerState** festgelegt ist.
- Pushtrigger
    - Wenn die HTTP-Methode **PUT** lautet.
    - Wenn die **operationId**-Eigenschaft die Zeichenfolge **trigger** enthält.
    - Wenn die **parameters**-Eigenschaft einen Parameter enthält, dessen **name**-Eigenschaft auf **triggerId** festgelegt ist.

## Verwenden von API-App-Triggern in Logik-Apps

### Auflisten und Konfigurieren von API-App-Triggern im Logik-Apps-Designer

Wenn Sie eine Logik-App in der gleichen Ressourcengruppe wie die API-App erstellen, können Sie die App zum Designerbereich hinzufügen, indem Sie einfach darauf klicken. Die folgenden Abbildungen verdeutlichen dies:

![Trigger im Logik-Apps-Designer](./media/app-service-api-dotnet-triggers/triggersinlogicappdesigner.PNG)

![Konfigurieren des Abfragetriggers im Logik-Apps-Designer](./media/app-service-api-dotnet-triggers/configurepolltriggerinlogicappdesigner.PNG)

![Konfigurieren des Pushtriggers im Logik-Apps-Designer](./media/app-service-api-dotnet-triggers/configurepushtriggerinlogicappdesigner.PNG)

## Optimieren von API-App-Triggern für Logik-Apps

Nachdem Sie Trigger zu einer API-App hinzugefügt haben, können Sie einige Maßnahmen ergreifen, um die Benutzerfreundlichkeit bei der Verwendung der API-App in einer Logik-App zu verbessern.

Beispielsweise sollte der **triggerState**-Parameter für Abfragetrigger auf den folgenden Ausdruck in der Logik-App festgelegt werden. Der Ausdruck sollte den letzten Aufruf des Triggers aus der Logik-App auswerten und diesen Wert zurückgeben.

	@coalesce(triggers()?.outputs?.body?['triggerState'], '')

HINWEIS: Eine Erläuterung der in oben stehendem Ausdruck verwendeten Funktionen finden Sie in der Dokumentation zur [Definitionssprache für Logik-App-Workflows](https://msdn.microsoft.com/library/azure/dn948512.aspx).

Benutzer von Logik-Apps müssen bei Verwendung des Triggers den oben stehenden Ausdruck für den **triggerState**-Parameter bereitstellen. Es ist möglich, diesen Wert durch den Logik-Apps-Designer über die Erweiterungseigenschaft **x-ms-scheduler-recommendation** voreinstellen zu lassen. Die **x-ms-visibility**-Erweiterungseigenschaft kann auf den Wert *internal* festgelegt werden, sodass der Parameter selbst im Designer nicht angezeigt wird. Der folgende Codeausschnitt veranschaulicht dies.

    "/api/Messages/poll": {
      "get": {
	    "operationId": "Messages_NewMessageTrigger",
        "parameters": [
          {
            "name": "triggerState",
            "in": "query",
            "required": true,
            "x-ms-visibility": "internal",
            "x-ms-scheduler-recommendation": "@coalesce(triggers()?.outputs?.body?['triggerState'], '')",
            "type": "string"
          }
        ]
        ...
        "x-ms-scheduler-trigger": "poll"
      }
    }

Bei Pushtriggern muss der **triggerId**-Parameter die Logik-App eindeutig identifizieren. Es empfiehlt sich, diese Eigenschaft mithilfe des folgenden Ausdrucks auf den Namen des Workflows festzulegen:

    @workflow().name

Mithilfe der Erweiterungseigenschaften **x-ms-scheduler-recommendation** und **x-ms-visibility** in der API-Definition kann die API-App den Logik-Apps-Designer anweisen, diesen Ausdruck automatisch für den Benutzer festzulegen.

        "parameters":[  
          {  
            "name":"triggerId",
            "in":"path",
            "required":true,
            "x-ms-visibility":"internal",
            "x-ms-scheduler-recommendation":"@workflow().name",
            "type":"string"
          },


### Hinzufügen von Erweiterungseigenschaften in der API-Definition

In der API-Definition können zusätzliche Metadateninformationen – wie z. B. die Erweiterungseigenschaften **x-ms-scheduler-recommendation** und **x-ms-visibility** – auf zwei Arten hinzugefügt werden: statisch oder dynamisch.

Bei statischen Metadaten können Sie die Datei */metadata/apiDefinition.swagger.json* in Ihrem Projekt direkt bearbeiten und die Eigenschaften manuell hinzufügen.

Bei API-Apps, die dynamische Metadaten verwenden, können Sie die Datei "SwaggerConfig.cs" bearbeiten, um einen Vorgangsfilter einzufügen, der diese Erweiterungen hinzufügen kann.

    GlobalConfiguration.Configuration 
        .EnableSwagger(c =>
            {
                ...
                c.OperationFilter<TriggerStateFilter>();
                ...
            }


Im Folgenden finden Sie ein Beispiel dafür, wie diese Klasse implementiert werden kann, um das Szenario mit dynamischen Metadaten zu ermöglichen.

    // Add extension properties on the triggerState parameter
    public class TriggerStateFilter : IOperationFilter
    {

        public void Apply(Operation operation, SchemaRegistry schemaRegistry, System.Web.Http.Description.ApiDescription apiDescription)
        {
            if (operation.operationId.IndexOf("Trigger", StringComparison.InvariantCultureIgnoreCase) >= 0)
            {
                // this is a possible trigger
                var triggerStateParam = operation.parameters.FirstOrDefault(x => x.name.Equals("triggerState"));
                if (triggerStateParam != null)
                {
                    if (triggerStateParam.vendorExtensions == null)
                    {
                        triggerStateParam.vendorExtensions = new Dictionary<string, object>();
                    }

                    // add 2 vendor extensions
                    // x-ms-visibility: set to 'internal' to signify this is an internal field
                    // x-ms-scheduler-recommendation: set to a value that logic app can use
                    triggerStateParam.vendorExtensions.Add("x-ms-visibility", "internal");
                    triggerStateParam.vendorExtensions.Add("x-ms-scheduler-recommendation",
                                                           "@coalesce(triggers()?.outputs?.body?['triggerState'], '')");
                }
            }
        }
    }
 

<!---HONumber=AcomDC_0316_2016-->