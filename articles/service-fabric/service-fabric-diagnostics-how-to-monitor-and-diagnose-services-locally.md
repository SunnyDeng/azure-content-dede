<properties
   pageTitle="Microsoft Azure Service Fabric – Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten"
   description="In diesem Artikel wird beschrieben, wie Sie mit Microsoft Azure Service Fabric erstellte Dienste auf einem lokalen Entwicklungscomputer überwachen und diagnostizieren."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/22/2015"
   ms.author="kunalds"/>


# Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer
Überwachung, Erkennung, Diagnose und Problembehandlung ermöglichen das Ausführen von Diensten mit minimalen Unterbrechungen für Benutzer. Diese Vorgänge sind in einer tatsächlichen bereitgestellten Produktionsumgebung wichtig, die Wirksamkeit hängt jedoch davon ab, ob bei der Entwicklung von Diensten ein ähnliches Modell verwendet wird. Nur so ist sichergestellt, dass die Vorgänge tatsächlich funktionieren. Service Fabric erleichtert Dienstentwicklern das Implementieren von Diagnosen, die in einer lokalen Umgebung auf einem einzelnen Computer und in der tatsächlichen Konfiguration in einem Produktionscluster nahtlos verwendet werden können.

## Ablaufverfolgung und Protokollierung
[Event Tracing for Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) \(ETW\) ist die empfohlene Technologie für das Nachverfolgen von Meldungen in Service Fabric. Hierfür gibt es folgende Gründe:

* ETW ist schnell. Es wurde als Technologie für die Ablaufverfolgung erstellt, die minimale Auswirkungen auf die Codeausführungszeiten hat.
* Die ETW-Ablaufverfolgung funktioniert nahtlos in lokalen Entwicklungsumgebungen und in realen Clusterkonfigurationen. Das heißt, Sie müssen den Ablaufverfolgungscode nicht neu schreiben, wenn Sie den Code in einem echten Cluster bereitstellen.
* Der Service Fabric-Systemcode verwendet ETW auch für die interne Ablaufverfolgung. Dadurch können Sie Ihre Anwendungsablaufverfolgung überlappend mit der Service Fabric-Ablaufverfolgung anzeigen, um die Sequenzen und Beziehungen zwischen dem Anwendungscode und den Ereignissen im zugrunde liegenden System besser nachvollziehen zu können.
* Die Anzeige von ETW-Ereignissen wird von den Visual Studio-Tools für Service Fabric unterstützt.


## Anzeigen von Service Fabric-Ereignissen in Visual Studio

Service Fabric gibt ETW-Ereignisse aus, um die Abläufe in der Plattform für Anwendungsentwickler sichtbar zu machen. Gehen Sie zum Anzeigen dieser Ereignisse folgendermaßen vor:

1. Die folgenden Komponenten müssen installiert sein.
   * Visual Studio 2015
   * Service Fabric-SDK

2. Starten Sie Visual Studio als Administrator.

3. Erstellen oder öffnen Sie ein vorhandenes Projekt für einen zustandsbehafteten oder zustandslosen Actor oder Dienst.

  ![Erstellen einer Service Fabric-Anwendung](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject.png)

  ![Erstellen eines Service Fabric-Diensts](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject-2.png)

4. Drücken Sie F5, um die Anwendung zu debuggen. Die Service Fabric-Ereignisse werden nun im Fenster "Diagnoseereignisse" angezeigt. Jedes Ereignis verfügt über Standard-Metadateninformationen, anhand derer Sie den Knoten, die Anwendung und den Dienst ablesen können, aus denen das Ereignis stammt. Sie können die Liste der Ereignisse mit dem Feld „Ereignisse filtern“ oben in den Fenstern filtern \(z. B. nach Knoten- oder Dienstname\).

  ![Visual Studio-Anzeige von Diagnoseereignissen](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

5. Wenn das Fenster "Diagnoseereignisse" nicht automatisch angezeigt wird, wechseln Sie in Visual Studio zur Registerkarte "Server-Explorer", klicken Sie mit der rechten Maustaste auf den Service Fabric-Cluster, und wählen Sie "Diagnoseereignisse anzeigen" im Kontextmenü aus.

  ![Visual Studio-Anzeige von Diagnoseereignissen öffnen](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

## Hinzufügen eigener benutzerdefinierter Ablaufverfolgungen zum Anwendungscode
Die Service Fabric-Visual Studio-Projektvorlagen enthalten Beispielcode. Der Code zeigt, wie benutzerdefinierte ETW-Ablaufverfolgungen zum Anwendungscode hinzugefügt werden. Diese werden in der ETW-Anzeige in Visual Studio zusammen mit den Systemablaufverfolgungen von Service Fabric angezeigt. Der Vorteil dieser Methode liegt darin, dass Metadaten automatisch zu Ablaufverfolgungen hinzugefügt werden und der Visual Studio-Diagnose-Viewer bereits für die Anzeige der Ablaufverfolgungen konfiguriert ist.

Für Projekte, die mit **Dienstvorlagen** \(zustandsbehaftet oder zustandslos\) erstellt wurden:

1. Öffnen Sie die Datei **Service.cs**. Der Aufruf an `ServiceEventSource.Current.Message` in der *RunAsync*-Methode zeigt ein Beispiel für eine benutzerdefinierte ETW-Ablaufverfolgung aus dem Anwendungscode.
2. In der Datei **ServiceEventSource.cs** zeigen die Überladungen der `ServiceEventSource.Message`-Methode eine Option zum Schreiben benutzerdefinierter ETW-Ablaufverfolgungen.

Für Projekte, die mit **Actor-Vorlagen** \(zustandsbehaftet oder zustandslos\) erstellt wurden:

1. Öffnen Sie die Datei **ProjectName.cs**, wobei *ProjectName* der Name Ihres Visual Studio-Projekts ist.  
2. Suchen Sie den Code `ActorEventSource.Current.ActorMessage(this, "Doing Work");` in der Methode *DoWorkAsync*. Dies ist ein Beispiel für eine benutzerdefinierte ETW-Ablaufverfolgung im Anwendungscode.  
3. In der Datei **ActorEventSource.cs** zeigen die Überladungen der `ActorEventSource.ActorMessage`-Methode eine Option zum Schreiben benutzerdefinierter ETW-Ablaufverfolgungen.

Nachdem Sie die benutzerdefinierte ETW-Ablaufverfolgung zum Dienstcode hinzugefügt haben, können Sie die Anwendung erneut erstellen, bereitstellen und ausführen, um die Ereignisse im Diagnose-Viewer anzuzeigen. Beim Debuggen der Anwendung mit F5 wird der Diagnose-Viewer automatisch geöffnet.

##In Kürze verfügbar
Der Ablaufverfolgungscode, den Sie zu Ihrer Anwendung für die lokale Diagnose hinzugefügt haben, funktioniert auch mit den Tools für die Anzeige dieser Ereignisse, wenn Sie denselben Code in einem Azure-Cluster ausführen. Hierzu werden in Kürze weitere Informationen verfügbar gemacht.

## Nächste Schritte

* [Einführung in Service Fabric-Integrität](service-fabric-health-introduction.md)
* [Application Insights-Einrichtung](service-fabric-diagnostics-application-insights-setup.md)
* [Diagnose und Leistungsüberwachung für Actors in Azure Service Fabric](service-fabric-reliable-actors-diagnostics.md)
* [Diagnose zustandsbehafteter zuverlässiger Dienste](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=July15_HO5-->