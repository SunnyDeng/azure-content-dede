<properties
	pageTitle="Lernprogramm – Erste Schritte mit der Azure Batch Apps-Bibliothek für .NET"
	description="Erfahren Sie anhand eines einfachen Szenarios, welche Konzepte Azure Batch Apps zugrunde liegen und wie Sie damit entwickeln."
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="07/21/2015"
	ms.author="yidingz"/>




# Erste Schritte mit der Azure Batch Apps-Bibliothek für .NET
In diesem Lernprogramm wird veranschaulicht, wie Sie mithilfe des Batch Apps-Diensts von Azure Batch-Workloads parallel verarbeiten.

Batch Apps ist ein Feature von Azure Batch, das eine anwendungsorientierte Methode zur Verwaltung und Ausführung von Batch-Workloads bietet. Mithilfe des Batch Apps-SDKs können Sie Pakete erstellen, um eine Anwendung für Batch zu aktivieren, und diese Pakete in Ihrem eigenen Konto bereitstellen oder für andere Batch-Benutzer zur Verfügung stellen. Batch stellt auch die Datenverwaltung, Auftragsüberwachung, integrierte Diagnose und Protokollierung sowie die Unterstützung für Abhängigkeiten zwischen Aufgaben bereit. Es enthält außerdem ein Verwaltungsportal, in dem Sie Aufträge verwalten, Protokolle anzeigen und Ausgaben herunterladen können, ohne einen eigenen Client schreiben zu müssen.

Im Batch Apps-Szenario schreiben Sie mithilfe des Batch Apps Cloud-SDKs Code, um Aufträge in parallele Aufgaben zu unterteilen, sämtliche Abhängigkeiten zwischen diesen Aufgaben zu beschreiben und anzugeben, wie die einzelnen Aufgaben ausgeführt werden. Dieser Code wird im Batch-Konto bereitgestellt. Clients können dann Aufträge ausführen, indem sie einfach die Art des Auftrags und die Eingabedateien für eine REST-API angeben.

>[AZURE.NOTE]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Sie können ein kostenloses Testkonto in wenigen Minuten erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/). Sie können NuGet zum Abrufen der Assemblys <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Batch-Apps-Cloud</a> und <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Batch-Apps-Client</a> verwenden. Nachdem Sie Ihr Projekt in Visual Studio erstellt haben, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus. Sie können auch die Visual Studio-Erweiterung für Batch-Apps <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">hier</a> herunterladen oder in Visual Studio über das Menü „Erweiterungen und Updates“ nach **Batch-Apps** suchen. Diese Erweiterung enthält eine Projektvorlage, um Anwendungen für die Cloud zu aktivieren und Anwendungen bereitzustellen. Es stehen darüber hinaus <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">umfangreiche Beispiele auf MSDN</a> zur Verfügung.
>

###Grundlagen zu Azure Batch Apps
Batch ist für die Arbeit mit vorhandenen rechenintensiven Anwendungen konzipiert. Es nutzt den vorhandenen Anwendungscode und wird in einer dynamischen, virtualisierten und universellen Umgebung ausgeführt. Damit eine Anwendung mit Batch Apps verwendet werden kann, müssen einige Vorkehrungen getroffen werden:

1.	Bereiten Sie eine ZIP-Datei der vorhandenen ausführbaren Dateien der Anwendung vor – dieselben ausführbaren Dateien, die auf einer herkömmlichen Serverfarm oder einem Cluster ausgeführt würden – sowie alle erforderlichen unterstützenden Dateien. Diese ZIP-Datei wird dann mithilfe des Verwaltungsportals oder der REST-API in das Batch-Konto hochgeladen.
2.	Erstellen Sie eine ZIP-Datei der "Cloud-Assemblys", die die Workloads an die Anwendung senden, und laden Sie diese dann über das Verwaltungsportal oder die REST-API hoch. Eine Cloud-Assembly enthält zwei Komponenten, die für das Cloud-SDK erstellt werden:
	1.	Auftragsteilung – Unterteilt den Auftrag in Aufgaben, die unabhängig voneinander verarbeitet werden können. Beispielsweise würde die Auftragsteilung in einem Animationsszenario einen Filmauftrag in Einzelbilder unterteilen.
	2.	Aufgabenprozessor – Ruft die ausführbare Datei der Anwendung für eine bestimmte Aufgabe auf. In einem Animationsszenario würde der Aufgabenprozessor z. B. ein Rendering-Programm aufrufen, um das durch eine Aufgabe angegebene Einzelbild zu verarbeiten.
3.	Bieten Sie eine Möglichkeit, um Aufträge an die aktivierte Anwendung in Azure zu übermitteln. Hierbei kann es sich um ein Plug-In für die Benutzeroberfläche Ihrer Anwendung oder um ein Webportal bzw. sogar einen unbeaufsichtigten Dienst als Bestandteil der Ausführungspipeline handeln. Weitere Informationen finden Sie in den <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">Beispielen</a> auf MSDN.



###Schlüsselbegriffe von Batch Apps
Das Programmierungs- und Nutzungsmodell von Batch Apps stützt sich auf die folgenden Schlüsselbegriffe:

####Aufträge
Ein **Auftrag** ist eine vom Benutzer übermittelte Arbeitsleistung. Wenn ein Auftrag gesendet wird, gibt der Benutzer den Typ des Auftrags, alle Einstellungen für diesen Auftrag sowie die für den Auftrag erforderlichen Daten an. Diese Details können entweder über die aktivierte Implementierung im Auftrag des Benutzers ausgearbeitet werden oder der Benutzer stellt diese Informationen explizit über den Client bereit. Ein Auftrag besitzt Ergebnisse, die zurückgegeben werden. Jeder Auftrag verfügt über eine primäre Ausgabe und eine optionale Vorschauausgabe. Aufträge können bei Bedarf auch zusätzliche Ausgaben zurückgeben.

####Aufgaben
Eine **Aufgabe** ist eine im Rahmen eines Auftrags auszuführende Arbeitsleistung. Wenn ein Benutzer einen Auftrag übermittelt, wird dieser in kleinere Aufgaben unterteilt. Der Dienst verarbeitet diese einzelnen Aufgaben anschließend und fasst die Aufgabenergebnisse zu einer Gesamtausgabe des Auftrags zusammen. Die Art der Aufgaben ist vom Typ des Auftrags abhängig. Die Auftragsteilung definiert, wie ein Auftrag in Aufgaben aufgegliedert wird. Dabei wird sie von den Informationen gelenkt, welche Arbeitssegmente die Anwendung verarbeiten soll. Aufgabenausgaben können auch einzeln heruntergeladen werden und sind in einigen Fällen möglicherweise hilfreich, wenn z. B. ein Benutzer einzelne Aufgaben eines Animationsauftrags herunterladen möchte.

####Zusammenführen von Aufgaben
Eine **Zusammenführungsaufgabe** ist eine spezielle Art von Aufgabe, die die Ergebnisse einzelner Aufgaben in die abschließenden Auftragsergebnisse zusammenführt. Für einen Auftrag zur Filmdarstellung führt die Zusammenführungsaufgabe möglicherweise die verarbeiteten Einzelbilder zu einem Film zusammen oder komprimiert alle verarbeiteten Einzelbilder in einer einzelnen Datei. Jeder Auftrag verfügt über eine Zusammenführungsaufgabe, auch wenn keine eigentliche "Zusammenführung" erforderlich ist.

####Dateien
Eine **Datei** ist ein Datensegment, das als Eingabe für einen Auftrag verwendet wird. Einem Auftrag muss keine Eingabedatei zugeordnet sein, es können ihm aber auch eine oder mehrere Eingabedateien zugewiesen werden. Dieselbe Datei kann in mehreren Aufträgen verwendet werden, z. B. in einem Auftrag zum Rendern eines Films kann es sich bei den Dateien beispielsweise um Strukturen, Modelle usw. handeln. Bei Aufgaben in einem Datenanalyseauftrag können die Dateien eine Reihe von Beobachtungen oder Messungen sein.

###Aktivieren der Cloud-Anwendung
Ihre Anwendung muss ein statisches Feld oder eine Eigenschaft enthalten, das bzw. die alle Details der Anwendung enthält. Darin sind der Name der Anwendung und der Auftragstyp bzw. die Auftragstypen angegeben, die von der Anwendung behandelt werden. Dies wird bei der Verwendung der Vorlage im SDK bereitgestellt, die über die Visual Studio Gallery heruntergeladen werden kann.

Es folgt ein Beispiel für die Deklaration einer Cloud-Anwendung für eine parallele Workload:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implementieren von Auftragsteilung und Aufgabenprozessor
Für äußerst parallele Workloads müssen Sie eine Auftragsteilung und einen Aufgabenprozessor implementieren.

####Implementieren von "JobSplitter.SplitIntoTasks"
Die "SplitIntoTasks"-Implementierung muss eine Folge von Aufgaben zurückgeben. Jede Aufgabe stellt eine separate Arbeitskomponente dar, die für die Verarbeitung durch einen Serverknoten in eine Warteschlange gestellt wird. Jede Aufgabe muss eigenständig und mit allen Informationen eingerichtet sein, die für den Aufgabenprozessor erforderlich sind.

Die von der Auftragsteilung angegebenen Aufgaben werden als "TaskSpecifier"-Objekte dargestellt. "TaskSpecifier" verfügt über eine Reihe von Eigenschaften, die Sie vor der Rückgabe der Aufgabe festlegen können.


-	"TaskIndex" wird ignoriert, ist aber für Aufgabenprozessoren verfügbar. Dadurch können Sie einen Index an den Aufgabenprozessor übergeben. Wenn Sie keinen Index übergeben müssen, muss diese Eigenschaft nicht festgelegt werden.
-	Parameter sind standardmäßig eine leere Sammlung. Sie können etwas zur Sammlung hinzufügen oder sie durch eine neue Sammlung ersetzen. Sie können Einträge aus der Auftragsparametersammlung mithilfe der "WithJobParameters"- oder "WithAllJobParameters"-Methode kopieren.  


"RequiredFiles" stellt standardmäßig eine leere Sammlung dar. Sie können etwas zur Sammlung hinzufügen oder sie durch eine neue Sammlung ersetzen. Sie können Einträge aus der Auftragsdateiensammlung mithilfe der "RequiringJobFiles"- oder "RequiringAllJobFiles"-Methode kopieren.

Sie können eine Aufgabe angeben, die von einer bestimmten anderen Aufgabe abhängig ist. Zu diesem Zweck legen Sie die "TaskSpecifier.DependsOn"-Eigenschaft fest, und übergeben die ID der Aufgabe, von der diese abhängig ist:

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

Die Aufgabe wird nicht ausgeführt, bis die Ausgabe der Aufgabe verfügbar ist, zu der die Abhängigkeit besteht.

Sie können auch angeben, dass eine ganze Gruppe von Aufgaben nicht mit der Verarbeitung beginnen soll, bis eine andere Gruppe vollständig abgeschlossen ist. In diesem Fall können Sie die "TaskSpecifier.Stage"-Eigenschaft festlegen. Aufgaben mit einem angegebenen Phasenwert beginnen nicht mit der Verarbeitung, bis alle Aufgaben mit niedrigeren Phasenwerten abgeschlossen wurden. Aufgaben mit Phase 3 beginnen z. B. nicht mit der Verarbeitung, bis alle Aufgaben mit Phase 0, 1 oder 2 beendet wurden. Phasen müssen bei 0 beginnen, die Reihenfolge der Phasen darf keine Lücken aufweisen und "SplitIntoTasks" muss Aufgaben in Phasenreihenfolge zurückgeben. Es ist z. B. ein Fehler, eine Aufgabe mit Phase 0 nach einer Aufgabe mit Phase 1 zurückzugeben.

Jeder parallele Auftrag endet mit einer speziellen Aufgabe, die als "Zusammenführungsaufgabe" bezeichnet wird. Der Auftrag der Zusammenführungsaufgabe ist die Zusammenführung der Ergebnisse der parallelen Verarbeitungsaufgaben zu einem Endergebnis. Batch Apps erstellt die Zusammenführungsaufgabe automatisch für Sie.

In seltenen Fällen empfiehlt es sich, die Zusammenführungsaufgabe explizit zu steuern, z. B. zum Anpassen ihrer Parameter. In diesem Fall können Sie die Zusammenführungsaufgabe angeben, indem Sie ein "TaskSpecifier" mit "IsMerge"-Eigenschaft zurückgeben, die auf "true" gesetzt ist. Dadurch wird die automatische Zusammenführungsaufgabe außer Kraft gesetzt. Wenn Sie eine explizite Zusammenführungsaufgabe erstellen:

-	Sie können möglicherweise nur eine explizite Zusammenführungsaufgabe erstellen.
-	Es muss sich um die letzte Aufgabe in der Sequenz handeln.
-	"IsMerge" muss auf "true" festgelegt sein, während "IsMerge" für jede andere Aufgabe auf "false" gesetzt sein muss.  


Beachten Sie jedoch, dass Sie die Zusammenführungsaufgabe normalerweise nicht explizit erstellen müssen.

Der folgende Code veranschaulicht eine einfache Implementierung von "SplitIntoTasks".

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implementieren von "ParallelTaskProcessor.RunExternalTaskProcess"

"RunExternalTaskProcess" wird für jede nicht zusammenführende Aufgabe aufgerufen, die von der Auftragsteilung zurückgegeben wird. Es sollte Ihre Anwendung mit den entsprechenden Argumenten aufrufen und eine Sammlung von Ausgaben zurückgeben, die für die spätere Verwendung aufbewahrt werden müssen.

Das folgende Fragment zeigt, wie Sie ein Programm namens "application.exe" aufrufen. Beachten Sie, dass Sie die Hilfsmethode "ExecutablePath" verwenden können, um absolute Dateipfade zu erstellen.

Die "ExternalProcess"-Klasse im Cloud-SDK bietet eine nützliche Hilfslogik zum Ausführen der ausführbaren Dateien der Anwendung. "ExternalProcess" kann sich um Abbrüche, die Übersetzung von Exitcodes in Ausnahmen, die Erfassung der Standardausgabe und von Standardfehlern sowie die Einrichtung von Umgebungsvariablen kümmern. Sie können darüber hinaus die .NET-Prozessklasse direkt zum Ausführen von Programmen verwenden, wenn Sie dies bevorzugen.

Die "RunExternalTaskProcess"-Methode gibt ein "TaskProcessResult" zurück, das eine Liste von Ausgabedateien enthält. Diese muss mindestens alle für die Zusammenführung erforderlichen Dateien enthalten. Sie können optional auch Protokolldateien, Vorschaudateien und temporäre Dateien zurückgeben (z. B. zu Diagnosezwecken, wenn die Aufgabe fehlgeschlagen ist). Beachten Sie, dass die Methode die Dateipfade, nicht den Dateiinhalt zurückgibt.

Jede Datei muss mit dem Typ der Ausgabe identifiziert werden, den sie enthält: Ausgabe (d. h. Teil der letztendlichen Auftragsausgabe), Vorschau, Protokoll oder Zwischenergebnis. Diese Werte stammen von der "TaskOutputFileKind"-Enumeration. Das folgende Fragment gibt eine einzelne Aufgabenausgabe und keine Vorschau oder Protokoll zurück. Die "TaskProcessResult.FromExternalProcessResult"-Methode vereinfacht die gängigen Szenarien zum Erfassen von Exitcode, Prozessorausgaben und Ausgabedateien über ein Befehlszeilenprogramm:

Der folgende Code veranschaulicht eine einfache Implementierung von "ParallelTaskProcessor.RunExternalTaskProcess".

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implementieren von "ParallelTaskProcessor.RunExternalMergeProcess"

Dies wird für eine Zusammenführungsaufgabe aufgerufen. Es sollte die Anwendung aufrufen, um Ausgaben der vorherigen Aufgaben auf eine für die Anwendung angemessene Weise zu kombinieren, und die kombinierte Ausgabe zurückgeben.

Die Implementierung von "RunExternalMergeProcess" ähnelt "RunExternalTaskProcess", jedoch mit folgender Ausnahme:

-	"RunExternalMergeProcess" nutzt die Ausgaben vorheriger Aufgaben anstelle von Benutzereingabedateien. Sie sollten daher die Namen der Dateien, die Sie verarbeiten möchten, auf Basis der Aufgaben-ID und nicht über die "Task.RequiredFiles"-Sammlung ermitteln.
-	"RunExternalMergeProcess" gibt eine "JobOutput"-Datei und optional eine "JobPreview"-Datei zurück.


Der folgende Code veranschaulicht eine einfache Implementierung von "ParallelTaskProcessor.RunExternalMergeProcess".

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Übermitteln von Aufträgen an Batch Apps
Ein Auftrag beschreibt eine auszuführende Workloads und muss alle Informationen zur Workload mit Ausnahme der Dateiinhalte enthalten. Der Auftrag enthält z. B. Konfigurationseinstellungen, die vom Client zur Auftragsteilung und anschließend zu den Aufgaben gelangen. Die auf MSDN bereitgestellten Beispiele sind Beispiele für die Übermittlung von Aufträgen. Sie stellen mehrere Clients bereit, einschließlich eines Webportal- und Befehlszeilenclients.

<!---HONumber=Oct15_HO3-->