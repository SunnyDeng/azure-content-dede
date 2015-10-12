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
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Was ist das Azure WebJobs-SDK?

## <a id="overview"></a>Übersicht

Dieser Artikel erläutert, was das WebJobs-SDK ist. In ihm werden einige allgemeine Szenarien beleuchtet, für die es nützlich ist, und er enthält eine Übersicht, wie sie das SDK in Ihrem Code verwenden können.

[WebJobs](websites-webjobs-resources.md) ist ein Feature von Azure App Service, mit dem Sie ein Programm oder Skript im selben Kontext wie eine Web-App ausführen können. Der Zweck des WebJobs-SDKs besteht darin, die Aufgabe für das Schreiben von Code zu vereinfachen, die als ein WebJob ausgeführt wird und mit Azure Storage-Warteschlangen, Blobs und Tabellen sowie Service Bus-Warteschlangen funktioniert.

Das WebJobs-SDK enthält die folgenden Komponenten:

* **NuGet-Pakete**. NuGet-Pakete, die Sie zu einem Visual Studio-Konsolenanwendungsprojekt hinzufügen, bieten ein Framework, das Ihr Code verwendet, um mit dem Azure Storage-Dienst oder Service-Warteschlangen zu interagieren.   
  
* **Dashboard**. Ein Teil des WebJobs-SDKs ist in Azure App Service enthalten und bietet eine umfangreiche Überwachung und Diagnose für Programme, die NuGet-Pakete verwenden. Sie müssen keinen Code schreiben, um diese Überwachungs- und Diagnosefeatures zu verwenden.

## <a id="scenarios"></a>Szenarien

Im Folgenden finden Sie einige typische Szenarien, die Sie mit dem Azure WebJobs-SDK einfacher umsetzen können:

* Bildverarbeitung oder andere CPU-intensive Arbeiten. Ein übliches Feature von Websites ist die Fähigkeit, Bilder oder Videos hochzuladen. Oftmals möchten Sie den Inhalt ändern, nachdem er hochgeladen wurde, dabei soll der Benutzer aber nicht warten, während Sie dies vornehmen.

* Warteschlangenverarbeitung. Eine übliche Möglichkeit, damit ein Web-Front-End mit einem Back-End-Dienst kommunizieren kann, besteht in der Verwendung von Warteschlangen. Wenn die Website die Arbeit abschließen muss, überträgt sie eine Nachricht auf eine Warteschlange. Ein Back-End-Dienst bezieht Nachrichten aus der Warteschlange und führt die Arbeit aus. Sie können Warteschlangen für die Bildbearbeitung verwenden: Versetzen Sie beispielsweise, nachdem der Benutzer einige Dateien hochgeladen hat, die Dateinamen in eine Warteschlangennachricht, die vom Back-End für die Verarbeitung abgeholt wird. Alternativ könnten Sie Warteschlangen zum Verbessern der Websitereaktionsfähigkeit verwenden. Anstelle beispielsweise direkt in eine SQL-Datenbank zu schreiben, schreiben Sie in eine Warteschlange, teilen Sie dem Benutzer mit, dass Sie fertig sind, und überlassen Sie dem Back-End-Dienst die Verarbeitung der relationalen Datenbankarbeit mit hoher Latenz. Ein Beispiel einer Warteschlangenverarbeitung mit einem Bildvorgang finden Sie im [Lernprogramm für die ersten Schritte über das WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started.md).

* RSS-Aggregation. Wenn Sie über eine Website verfügen, die eine Liste von RSS-Feeds verwaltet, könnten Sie alle Artikel aus den Feeds in einen Hintergrundvorgang einziehen.

* Dateiwartung wie das Aggregieren oder Bereinigen von Protokolldateien. Sie verfügen möglicherweise über Protokolldateien, die von mehreren Websites oder für getrennte Zeitspannen erstellt wurden, die Sie kombinieren möchten, um Analyseaufträge für sie auszuführen. Alternativ möchten Sie möglicherweise eine Aufgabe planen, die wöchentlich ausgeführt wird, um alte Protokolldateien zu bereinigen.

* Eingang in Azure-Tabellen. Sie verfügen möglicherweise über gespeicherte Dateien und Blobs, und Sie möchten sie analysieren und die Daten in Tabellen speichern. Die Eingangsfunktion könnte eine Menge Zeilen (in manchen Fällen Millionen) schreiben, und das WebJobs-SDK ermöglicht die einfache Implementierung dieser Funktionalität. Das SDK bietet auch eine Echtzeitüberwachung der Statusanzeigen wie die Anzahl der in der Tabelle geschriebenen Zeilen.

* Andere lang ausgeführte Aufgaben, die Sie in einem Hintergrundthread ausführen möchten, wie das [Senden von E-Mails](https://github.com/victorhurdugaci/AzureWebJobsSamples/tree/master/SendEmailOnFailure).

In vielen dieser Szenarien können Sie eine Web-App so skalieren, dass sie auf mehreren VMs ausgeführt wird, die dann mehrere WebJobs gleichzeitig ausführen. In einigen Fällen könnte dies dazu führen, dass dieselben Daten mehrmals verarbeitet werden, aber dies ist kein Problem, wenn Sie die integrierte Warteschlange, Blob und Service Bus-Trigger des WebJobs SDK verwenden. Das SDK stellt sicher, dass Ihre Funktionen für jede Nachricht oder jedes Blob nur einmal verarbeitet werden.

## <a id="code"></a> Codebeispiele

Der Code für das Verarbeiten typischer Aufgaben, die mit Azure Storage interagieren, ist einfach. In einer Konsolenanwendung schreiben Sie Methoden für die Hintergrundaufgaben, die Sie ausführen möchten, und Sie versehen sie mit Attributen aus dem WebJobs-SDK. Die `Main`-Methode erstellt ein `JobHost`-Objekt, das die Aufrufe zu den von Ihnen geschriebenen Methoden koordiniert. Das WebJobs-SDK-Framework weiß anhand der WebJobs-SDK-Attribute, die Sie in ihnen verwenden, wann es Ihre Methoden aufrufen muss.

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

Die Trigger- und Binder-Features des WebJobs-SDKs vereinfachen den Code erheblich, den Sie schreiben müssen, um mit Azure Storage- und Service Bus-Warteschlangen zu arbeiten. Der zum Handhaben der Warteschlangen- und Blobverarbeitung erforderliche Low-Level-Code wird für Sie durch das WebJobs-SDK-Framework erstellt. Das Framework erstellt Warteschlangen, die bisher noch nicht vorhanden sind, öffnet die Warteschlange, lies Warteschlangennachrichten, löscht Warteschlangennachrichten nach fertig gestellter Verarbeitung, erstellt Blobcontainer, die noch nicht vorhanden sind, schreibt in Blobs usw.

Das WebJobs-SDK bietet viele Möglichkeiten für die Arbeit mit Azure Storage. Wenn beispielsweise der von Ihnen mit dem `QueueTrigger`-Attribut versehene Parameter ein Bytearray oder ein benutzerdefinierter Typ ist, wird er automatisch von JSON deserialisiert. Und Sie können ein `BlobTrigger`-Attribut verwenden, um einen Vorgang auszulösen, sobald ein neues Blob in einem Azure Storage-Konto erstellt wird. (Hinweis: Auch wenn `QueueTrigger` neue Warteschlangennachrichten innerhalb von wenigen Sekunden findet, kann `BlobTrigger` für das Ermitteln neuer Blobs bis zu 20 Minuten benötigen. `BlobTrigger` sucht nach Blobs, sobald der `JobHost` startet, und prüft dann regelmäßig die Azure Storage-Protokolle zum Ermitteln neuer Blobs.)

## <a id="workerrole"></a>Verwenden des WebJobs-SDK außerhalb von WebJobs

Ein das WebJobs-SDK verwendende Programm ist eine standardmäßige Konsolenanwendung und kann überall ausgeführt werden – es muss nicht als ein WebJob ausgeführt werden. Sie können das Programm lokal auf Ihrem Entwicklungscomputer testen, und in der Produktion können Sie es als eine Cloud Services-Workerrolle oder einen Windows-Dienst ausführen, wenn Sie eine dieser Umgebungen bevorzugen.

Das Dashboard steht jedoch nur als eine Erweiterung für eine Azure App Services-Web-App zur Verfügung. Wenn Sie es außerhalb von einem Webauftrag ausführen und das Dashboard weiterhin verwenden möchten, können Sie eine Web-App so konfigurieren, dasselbe Speicherkonto zu verwenden, worauf Ihre WebJobs-SDK-Dashboardverbindungszeichenfolge verweist, und das WebJobs-Dashboard dieser Web-App zeigt dann Daten über die Funktionsausführung von Ihrem Programm an, das irgendwo anders ausgeführt wird. Sie können das Dashboard über die URL https://*{webappname}*.scm.azurewebsites.net/azurejobs/#/functions abrufen. Weitere Informationen finden Sie unter [Getting a dashboard for local development with the WebJobs SDK](http://blogs.msdn.com/b/jmstall/archive/2014/01/27/getting-a-dashboard-for-local-development-with-the-webjobs-sdk.aspx) (Abrufen eines Dashboards für die lokale Entwicklung mit dem WebJobs-SDK) (in englischer Sprache). Beachten Sie jedoch, dass im Blogbeitrag ein alter Verbindungszeichenfolgenname gezeigt wird.

## <a id="nostorage"></a>Verwenden des WebJobs-SDK zum Aufrufen einer beliebigen Funktion

Das WebJobs-SDK bietet verschiedene Vorteile, auch wenn Sie nicht direkt mit Azure Storage-Warteschlangen, -Tabellen oder -Blobs oder mit Service Bus-Warteschlangen arbeiten müssen:

* Sie können Funktionen im Dashboard aufrufen.
* Sie können Funktionen im Dashboard wiedergeben.
* Sie können im Dashboard Protokolle anzeigen, die dem jeweiligen Webauftrag zugeordnet (Anwendungsprotokolle, die mit "Console.Out", "Console.Error", "Trace" usw. geschrieben wurden) oder mit dem speziellen Funktionsaufruf verknüpft sind, der sie generiert hat (Protokolle, die mit einem `TextWriter`-Objekt geschrieben wurde, das das SDK als Parameter an die Funktion übergibt). 

* Weitere Informationen finden Sie im Thema über das [Gewusst wie: Manuelle Aufrufen einer Funktion](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#manual) und im Thema über das [Gewusst wie: Schreiben von Protokollen](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)

## <a id="nextsteps"></a>Nächste Schritte

Weitere Informationen über das WebJobs-SDK finden Sie im Thema über die [empfohlenen Azure WebJobs-Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=Oct15_HO1-->