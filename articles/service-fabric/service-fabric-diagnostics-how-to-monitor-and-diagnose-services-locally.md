<properties
   pageTitle="Microsoft Azure Service Fabric – Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten"
   description="In diesem Artikel wird beschrieben, wie Sie mit Microsoft Azure Service Fabric erstellte Dienste auf einem lokalen Entwicklungscomputer überwachen und diagnostizieren."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/04/2015"
   ms.author="kunalds"/>


# Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer
Überwachung, Erkennung, Diagnose und Problembehandlung ermöglichen das Ausführen von Diensten mit minimalen Unterbrechungen für Benutzer. Diese Vorgänge sind in einer tatsächlichen bereitgestellten Produktionsumgebung wichtig, die Wirksamkeit hängt jedoch davon ab, ob bei der Entwicklung von Diensten ein ähnliches Modell verwendet wird. Nur so ist sichergestellt, dass die Vorgänge tatsächlich funktionieren. Service Fabric erleichtert Dienstentwicklern das Implementieren von Diagnosen, die in einer lokalen Umgebung auf einem einzelnen Computer und in der tatsächlichen Konfiguration in einem Produktionscluster nahtlos verwendet werden können.

## Gründe für die Verwendung von ETW für Ablaufverfolgung und Protokollierung
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) ist die empfohlene Technologie für das Nachverfolgen von Meldungen in Service Fabric. Hierfür gibt es folgende Gründe:

* ETW ist schnell. Es wurde als Technologie für die Ablaufverfolgung erstellt, die minimale Auswirkungen auf die Codeausführungszeiten hat.
* Die ETW-Ablaufverfolgung funktioniert nahtlos in lokalen Entwicklungsumgebungen und in realen Clusterkonfigurationen. Das heißt, Sie müssen den Ablaufverfolgungscode nicht neu schreiben, wenn Sie den Code in einem echten Cluster bereitstellen.
* Der Service Fabric-Systemcode verwendet ETW auch für die interne Ablaufverfolgung. Dadurch können Sie Ihre Anwendungsablaufverfolgung überlappend mit der Service Fabric-Systemablaufverfolgung anzeigen, um die Sequenzen und Beziehungen zwischen dem Anwendungscode und den Ereignissen im zugrunde liegenden System besser nachvollziehen zu können.
* Die Anzeige von ETW-Ereignissen wird von den Visual Studio-Tools für Service Fabric unterstützt.


## Anzeigen von Service Fabric-Ereignissen in Visual Studio

Service Fabric gibt ETW-Ereignisse aus, um die Abläufe in der Plattform für Anwendungsentwickler sichtbar zu machen. Falls Sie dies noch nicht getan haben, führen Sie die Schritte unter [Erstellen Ihrer ersten Anwendung in Visual Studio](./service-fabric-create-your-first-application-in-visual-studio.md) aus, um eine Anwendung mit der Anzeige von Diagnoseereignissen einzurichten, in der die Ablaufverfolgungsmeldungen angezeigt werden.

1. Wenn das Fenster "Diagnoseereignisse" nicht automatisch angezeigt wird, wechseln Sie in Visual Studio zur Registerkarte "Server-Explorer", klicken Sie mit der rechten Maustaste auf den Service Fabric-Cluster, und wählen Sie "Diagnoseereignisse anzeigen" im Kontextmenü aus.

  ![Visual Studio-Anzeige von Diagnoseereignissen öffnen](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

2. Jedes Ereignis verfügt über Standard-Metadateninformationen, anhand derer Sie den Knoten, die Anwendung und den Dienst ablesen können, aus denen das Ereignis stammt. Sie können die Liste der Ereignisse mit dem Feld „Ereignisse filtern“ oben in den Fenstern filtern (z. B. nach Knoten- oder Dienstname). Sie können außerdem die Anzeige der Details für ein Ereignis mithilfe der Schaltfläche oben im Fenster anhalten und später fortsetzen, ohne dass dabei Ereignisse verloren gehen.

  ![Visual Studio-Anzeige von Diagnoseereignissen](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## Hinzufügen eigener benutzerdefinierter Ablaufverfolgungen zum Anwendungscode
Die Service Fabric-Visual Studio-Projektvorlagen enthalten Beispielcode. Der Code zeigt, wie benutzerdefinierte ETW-Ablaufverfolgungen zum Anwendungscode hinzugefügt werden. Diese werden in der ETW-Anzeige in Visual Studio zusammen mit den Systemablaufverfolgungen von Service Fabric angezeigt. Der Vorteil dieser Methode liegt darin, dass Metadaten automatisch zu Ablaufverfolgungen hinzugefügt werden und der Visual Studio-Diagnose-Viewer bereits für die Anzeige der Ablaufverfolgungen konfiguriert ist.

Suchen Sie bei Projekten, die mit **Dienstvorlagen** (zustandslos oder zustandsbehaftet) erstellt wurden, einfach nach der `RunAsync`-Implementierung:

1. Der Aufruf an `ServiceEventSource.Current.ServiceMessage` in der `RunAsync`-Methode zeigt ein Beispiel für eine benutzerdefinierte ETW-Ablaufverfolgung aus dem Anwendungscode.
2. Die Datei **ServiceEventSource.cs** enthält eine Überladung für die `ServiceEventSource.ServiceMessage`-Methode, die aus Leistungsgründen für häufige Ereignisse verwendet werden sollte.

Für Projekte, die mit **Actor-Vorlagen** (zustandsbehaftet oder zustandslos) erstellt wurden:

1. Öffnen Sie die Datei **ProjectName.cs**, wobei *ProjectName* der Name Ihres Visual Studio-Projekts ist.  
2. Suchen Sie den Code `ActorEventSource.Current.ActorMessage(this, "Doing Work");` in der Methode *DoWorkAsync*. Dies ist ein Beispiel für eine benutzerdefinierte ETW-Ablaufverfolgung, die aus dem Anwendungscode geschrieben wird.  
3. Die Datei **ActorEventSource.cs** enthält eine Überladung für die `ActorEventSource.ActorMessage`-Methode, die aus Leistungsgründen für häufige Ereignisse verwendet werden sollte.

Nachdem Sie die benutzerdefinierte ETW-Ablaufverfolgung zum Dienstcode hinzugefügt haben, können Sie die Anwendung erneut erstellen, bereitstellen und ausführen, um die Ereignisse im Diagnose-Viewer anzuzeigen. Beim Debuggen der Anwendung mit F5 wird der Diagnose-Viewer automatisch geöffnet.

## Nächste Schritte
Der Ablaufverfolgungscode, den Sie zu Ihrer Anwendung für die lokale Diagnose hinzugefügt haben, funktioniert auch mit den Tools für die Anzeige dieser Ereignisse, wenn Sie denselben Code in einem Azure-Cluster ausführen. In den folgenden Artikeln werden die verschiedenen Optionen für die Tools sowie ihre Einrichtung erläutert: * [Erfassen von Protokollen von einem Service Fabric-Cluster in Azure mit WAD (Microsoft Azure Diagnostics) und Operational Insights](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) * [Verwenden von ElasticSearch als Service Fabric-Anwendungsüberwachungsspeicher](service-fabric-diagnostic-how-to-use-elasticsearch.md)

<!---HONumber=Nov15_HO4-->