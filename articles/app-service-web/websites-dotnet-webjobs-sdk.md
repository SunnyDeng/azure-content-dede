<properties 
	pageTitle="Was ist das Azure WebJobs-SDK?" 
	description="Eine Einführung in Bezug auf das Azure WebJobs-SDK. Erläutert, was das SDK ist, typische Szenarien, für die es nützlich ist, und enthält Codebeispiele." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/14/2015" 
	ms.author="tdykstra"/>

# Was ist das Azure WebJobs-SDK?

## <a id="overview"></a>Übersicht

Dieser Artikel erläutert, was das WebJobs-SDK ist. In ihm werden einige allgemeine Szenarien beleuchtet, für die es nützlich ist, und er enthält eine Übersicht, wie sie das SDK in Ihrem Code verwenden können.

[WebJobs](websites-webjobs-resources.md) ist ein Feature von Azure App Service, mit dem Sie ein Programm oder Skript im selben Kontext wie eine Web-App, API-App oder mobile App ausführen können. Der Zweck des [WebJobs SDK](websites-webjobs-resources.md) besteht darin, den Code zu vereinfachen, den Sie für allgemeine Aufgaben schreiben, die ein WebJob ausführen kann, z. B. Bildverarbeitung, Warteschlangenverarbeitung, RSS-Aggregation, Dateiwartung und Senden von E-Mails. Das WebJobs SDK verfügt über integrierte Funktionen für die Arbeit mit Azure Storage und Service Bus, zum Planen von Aufgaben und Behandeln von Fehlern sowie für viele weitere allgemeine Szenarien. Darüber hinaus ist es erweiterbar, und es gibt ein [Open Source-Repository für Erweiterungen](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview).

Das WebJobs-SDK enthält die folgenden Komponenten:

* **NuGet-Pakete**. NuGet-Pakete, die Sie einem Visual Studio-Konsolenanwendungsprojekt hinzufügen, bieten ein Framework, das von Ihrem Code verwendet wird, indem Ihre Methoden mit Attributen des WebJobs SDK versehen werden.
  
* **Dashboard**. Ein Teil des WebJobs-SDKs ist in Azure App Service enthalten und bietet eine umfangreiche Überwachung und Diagnose für Programme, die NuGet-Pakete verwenden. Sie müssen keinen Code schreiben, um diese Überwachungs- und Diagnosefeatures zu verwenden.

## <a id="scenarios"></a>Szenarien

Im Folgenden finden Sie einige typische Szenarien, die Sie mit dem Azure WebJobs-SDK einfacher umsetzen können:

* Bildverarbeitung oder andere CPU-intensive Arbeiten. Ein übliches Feature von Websites ist die Fähigkeit, Bilder oder Videos hochzuladen. Oftmals möchten Sie den Inhalt ändern, nachdem er hochgeladen wurde, dabei soll der Benutzer aber nicht warten, während Sie dies vornehmen.

* Warteschlangenverarbeitung. Eine übliche Möglichkeit, damit ein Web-Front-End mit einem Back-End-Dienst kommunizieren kann, besteht in der Verwendung von Warteschlangen. Wenn die Website die Arbeit abschließen muss, überträgt sie eine Nachricht auf eine Warteschlange. Ein Back-End-Dienst bezieht Nachrichten aus der Warteschlange und führt die Arbeit aus. Sie können Warteschlangen für die Bildbearbeitung verwenden: Versetzen Sie beispielsweise, nachdem der Benutzer einige Dateien hochgeladen hat, die Dateinamen in eine Warteschlangennachricht, die vom Back-End für die Verarbeitung abgeholt wird. Alternativ könnten Sie Warteschlangen zum Verbessern der Websitereaktionsfähigkeit verwenden. Anstelle beispielsweise direkt in eine SQL-Datenbank zu schreiben, schreiben Sie in eine Warteschlange, teilen Sie dem Benutzer mit, dass Sie fertig sind, und überlassen Sie dem Back-End-Dienst die Verarbeitung der relationalen Datenbankarbeit mit hoher Latenz. Ein Beispiel einer Warteschlangenverarbeitung mit einem Bildvorgang finden Sie im [Lernprogramm für die ersten Schritte über das WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started.md).

* RSS-Aggregation. Wenn Sie über eine Website verfügen, die eine Liste von RSS-Feeds verwaltet, könnten Sie alle Artikel aus den Feeds in einen Hintergrundvorgang einziehen.

* Dateiwartung wie das Aggregieren oder Bereinigen von Protokolldateien. Sie verfügen möglicherweise über Protokolldateien, die von mehreren Websites oder für getrennte Zeitspannen erstellt wurden, die Sie kombinieren möchten, um Analyseaufträge für sie auszuführen. Alternativ möchten Sie möglicherweise eine Aufgabe planen, die wöchentlich ausgeführt wird, um alte Protokolldateien zu bereinigen.

* Eingang in Azure-Tabellen. Sie verfügen möglicherweise über gespeicherte Dateien und Blobs, und Sie möchten sie analysieren und die Daten in Tabellen speichern. Die Eingangsfunktion könnte eine Menge Zeilen (in manchen Fällen Millionen) schreiben, und das WebJobs-SDK ermöglicht die einfache Implementierung dieser Funktionalität. Das SDK bietet auch eine Echtzeitüberwachung der Statusanzeigen wie die Anzahl der in der Tabelle geschriebenen Zeilen.

* Andere lang ausgeführte Aufgaben, die Sie in einem Hintergrundthread ausführen möchten, wie das [Senden von E-Mails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure).

* Alle Aufgaben, die nach einem Zeitplan ausgeführt werden sollen, z. B. das nächtliche Durchführen eines Sicherungsvorgangs.

In vielen dieser Szenarien können Sie eine Web-App so skalieren, dass sie auf mehreren VMs ausgeführt wird, die dann mehrere WebJobs gleichzeitig ausführen. In einigen Fällen könnte dies dazu führen, dass dieselben Daten mehrmals verarbeitet werden, aber dies ist kein Problem, wenn Sie die integrierte Warteschlange, Blob und Service Bus-Trigger des WebJobs SDK verwenden. Das SDK stellt sicher, dass Ihre Funktionen für jede Nachricht oder jedes Blob nur einmal verarbeitet werden.

Das WebJobs SDK erleichtert auch gängige Szenarien für die Fehlerbehandlung. Sie können Warnungen einrichten, um bei einem Funktionsfehler Benachrichtigungen zu senden, und Sie können Timeouts festlegen, um eine Funktion automatisch abzubrechen, wenn sie nicht innerhalb eines bestimmten Zeitraums abgeschlossen wird.

## <a id="code"></a> Codebeispiele

Der Code für das Verarbeiten typischer Aufgaben, die mit Azure Storage interagieren, ist einfach. In der `Main`-Methode Ihrer Konsolenanwendung erstellen Sie ein `JobHost`-Objekt, das die Aufrufe der von Ihnen geschriebenen Methoden koordiniert. Das WebJobs SDK-Framework erkennt, wann Ihre Methoden mit welchen Parameterwerten aufgerufen werden müssen, anhand der darin verwendeten WebJobs SDK-Attribute. Das SDK enthält *Trigger*, die angeben, unter welchen Umstände die Funktion aufgerufen wird, und *Binder*, die angeben, wie Informationen in Methodenparameter eingegeben und daraus ausgegeben werden.

Beispielsweise wird durch das Attribut [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md) eine Funktion aufgerufen, wenn eine Nachricht in einer Warteschlange empfangen wird. Wenn das Nachrichtenformat für ein Bytearray oder einen benutzerdefinierten Typ JSON lautet, wird die Nachricht automatisch deserialisiert. Das Attribut [BlobTrigger](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md) löst einen Vorgang aus, sobald ein neues Blob in einem Azure Storage-Konto erstellt wird.

Im Folgenden finden Sie ein einfaches Programm, das eine Warteschlange abfragt und ein Blob für jede empfangene Warteschlangennachricht erstellt:

		public static void Main()
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")] string inputText, 
            [Blob("containername/blobname")]TextWriter writer)
		{
		    writer.WriteLine(inputText);
		}

Das `JobHost`-Objekt ist ein Container für eine Reihe von Hintergrundfunktionen. Das `JobHost`-Objekt überwacht die Funktionen, überwacht auf Ereignisse, die sie auslösen, und führt die Funktionen aus, wenn die Trigger auftreten. Sie rufen eine `JobHost`-Methode auf, um anzugeben, ob der Containervorgang auf dem aktuellen Thread oder auf einem Hintergrundthread ausgeführt werden soll. Im Beispiel führt die `RunAndBlock`-Methode den Vorgang fortlaufend auf dem aktuellen Thread aus.

Da die `ProcessQueueMessage`-Methode in diesem Beispiel ein `QueueTrigger`-Attribut aufweist, ist der Trigger für diese Funktion der Empfang einer neuen Warteschlangennachricht. Das `JobHost`-Objekt überwacht die angegebene Warteschlange auf neue Warteschlangennachrichten (in diesem Beispiel "webjobsqueue"). Wird eine neue Nachricht ermittelt, wird `ProcessQueueMessage` aufgerufen.

Das `QueueTrigger`-Attribut bindet den `inputText`-Parameter an den Wert der Warteschlangennachricht. Das `Blob`-Attribut bindet außerdem ein `TextWriter`-Objekt an ein Blob mit dem Namen "blobname" in einem Container mit dem Namen "containername".

		public static void ProcessQueueMessage([QueueTrigger("webjobsqueue")]] string inputText, 
		    [Blob("containername/blobname")]TextWriter writer)

Die Funktion verwendet dann diese Parameter, um den Wert der Warteschlangennachricht in das Blob zu schreiben:

		writer.WriteLine(inputText);

Durch die Trigger- und Binderfeatures des WebJobs SDK wird der Code, den Sie schreiben müssen, erheblich vereinfacht. Der detaillierte Code, der zum Verarbeiten von Warteschlangen, Blobs oder Dateien bzw. zum Initiieren geplanter Tasks erforderlich ist, wird durch das WebJobs SDK-Framework für Sie erstellt. Das Framework erstellt beispielsweise Warteschlangen, die bisher noch nicht vorhanden sind, öffnet die Warteschlange, lies Warteschlangennachrichten, löscht Warteschlangennachrichten nach fertig gestellter Verarbeitung, erstellt Blobcontainer, die noch nicht vorhanden sind, schreibt in Blobs usw.

Das folgende Codebeispiel zeigt eine Vielzahl von Triggern in einem WebJob: `QueueTrigger`, `FileTrigger`, `WebHookTrigger` und `ErrorTrigger`.

```
    public class Functions
    {
        public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        TextWriter log)
        {
            log.WriteLine(message);
        }

        public static void ProcessFileAndUploadToBlob(
            [FileTrigger(@"import\{name}", "*.*", autoDelete: true)] Stream file,
            [Blob(@"processed/{name}", FileAccess.Write)] Stream output,
            string name,
            TextWriter log)
        {
            output = file;
            file.Close();
            log.WriteLine(string.Format("Processed input file '{0}'!", name));
        }

        [Singleton]
        public static void ProcessWebHookA([WebHookTrigger] string body, TextWriter log)
        {
            log.WriteLine(string.Format("WebHookA invoked! Body: {0}", body));
        }

        public static void ProcessGitHubWebHook([WebHookTrigger] string body, TextWriter log)
        {
            dynamic issueEvent = JObject.Parse(body);
            log.WriteLine(string.Format("GitHub WebHook invoked! ('{0}', '{1}')",
                issueEvent.issue.title, issueEvent.action));
        }

        public static void ErrorMonitor(
        [ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
        [SendGrid(
            To = "admin@emailaddress.com",
            Subject = "Error!")]
         SendGridMessage message)
        {
            // log last 5 detailed errors to the Dashboard
            log.WriteLine(filter.GetDetailedMessage(5));
            message.Text = filter.GetDetailedMessage(1);
        }
    }
```

## <a id="schedule"></a> Zeitpläne

Das `TimerTrigger`-Attribut bietet Ihnen die Möglichkeit, die Ausführung von Funktionen nach einem Zeitplan auszulösen. Sie können einen WebJob als Ganzes über Azure planen oder einzelne Funktionen eines WebJobs mithilfe von `TimerTrigger` im WebJobs SDK planen. Hier sehen Sie ein Codebeispiel.

```
public class Functions
{
    public static void ProcessTimer([TimerTrigger("*/15 * * * * *", RunOnStartup = true)]
    TimerInfo info, [Queue("queue")] out string message)
    {
        message = info.FormatNextOccurrences(1);
    }
}
```

Weitere Codebeispiele finden Sie unter [TimerSamples.cs](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/ExtensionsSample/Samples/TimerSamples.cs) im Repository „azure-webjobs-sdk-extensions“ auf GitHub.com.

## Erweiterbarkeit

Sie sind nicht auf die vorinstallierten Funktionen beschränkt: Das WebJobs SDK ermöglicht Ihnen das Schreiben eigener Trigger und Binder. Beispielsweise können Sie Trigger für Cacheereignisse und regelmäßige Zeitpläne schreiben. Ein [Open Source-Repository](https://github.com/Azure/azure-webjobs-sdk-extensions) enthält einen [ausführlichen Leitfaden zur Erweiterbarkeit des WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) und Beispielcode, um Ihnen den Einstieg in das Schreiben eigener Trigger und Binder zu erleichtern.

## <a id="workerrole"></a>Verwenden des WebJobs-SDK außerhalb von WebJobs

Ein das WebJobs-SDK verwendende Programm ist eine standardmäßige Konsolenanwendung und kann überall ausgeführt werden – es muss nicht als ein WebJob ausgeführt werden. Sie können das Programm lokal auf Ihrem Entwicklungscomputer testen, und in der Produktion können Sie es als eine Cloud Services-Workerrolle oder einen Windows-Dienst ausführen, wenn Sie eine dieser Umgebungen bevorzugen.

Das Dashboard steht jedoch nur als eine Erweiterung für eine Azure App Services-Web-App zur Verfügung. Wenn Sie es außerhalb von einem Webauftrag ausführen und das Dashboard weiterhin verwenden möchten, können Sie eine Web-App so konfigurieren, dasselbe Speicherkonto zu verwenden, worauf Ihre WebJobs-SDK-Dashboardverbindungszeichenfolge verweist, und das WebJobs-Dashboard dieser Web-App zeigt dann Daten über die Funktionsausführung von Ihrem Programm an, das irgendwo anders ausgeführt wird. Sie können das Dashboard über die URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions abrufen. Weitere Informationen finden Sie unter [Getting a dashboard for local development with the WebJobs SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (Abrufen eines Dashboards für die lokale Entwicklung mit dem WebJobs-SDK) (in englischer Sprache). Beachten Sie jedoch, dass im Blogbeitrag ein alter Verbindungszeichenfolgenname gezeigt wird.

## <a id="nostorage"></a>Dashboardfeatures

Das WebJobs SDK bietet auch dann mehrere Vorteile, wenn Sie keine WebJobs SDK-Trigger oder -Binder verwenden:

* Sie können Funktionen im Dashboard aufrufen.
* Sie können Funktionen im Dashboard wiedergeben.
* Sie können im Dashboard Protokolle anzeigen, die dem jeweiligen Webauftrag zugeordnet (Anwendungsprotokolle, die mit "Console.Out", "Console.Error", "Trace" usw. geschrieben wurden) oder mit dem speziellen Funktionsaufruf verknüpft sind, der sie generiert hat (Protokolle, die mit einem `TextWriter`-Objekt geschrieben wurde, das das SDK als Parameter an die Funktion übergibt). 

Weitere Informationen finden Sie im Thema über das [Gewusst wie: Manuelle Aufrufen einer Funktion](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) und im Thema über das [Gewusst wie: Schreiben von Protokollen](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>Nächste Schritte

Weitere Informationen über das WebJobs-SDK finden Sie im Thema über die [empfohlenen Azure WebJobs-Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).

Informationen zu den neuesten Verbesserungen für das WebJobs SDK finden Sie in den [Versionshinweisen](https://github.com/Azure/azure-webjobs-sdk/wiki/Release-Notes).
 

<!---HONumber=AcomDC_1217_2015-->