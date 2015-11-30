<properties
   pageTitle="Häufig auftretende Probleme und Problembehandlung | Microsoft Azure"
   description="Die häufigsten Probleme beim Bereitstellen von Diensten auf Microsoft Azure Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>


# Häufig auftretende Probleme und Problembehandlung
Beim Ausführen von Diensten auf dem Entwicklercomputer sind [Visual Studio-Debugtools](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) hilfreich. Für Remotecluster sind [Integritätsberichte](service-fabric-view-entities-aggregated-health.md) immer ein guter Ausgangspunkt. Am einfachsten greifen Sie auf diese Berichte über PowerShell oder [SFX](service-fabric-visualizing-your-cluster.md) zu. In diesem Artikel wird davon ausgegangen, dass Sie einen Remotecluster debuggen und über grundlegende Kenntnisse zur Verwendung der Tools verfügen.

##Anwendungsabstürze
Der Bericht „Partition is below target replica or instance count“ ist ein guter Hinweis darauf, dass der Dienst abstürzt. Um herauszufinden, an welcher Stelle Ihr Dienst abstürzt, bedarf es einiger weiterer Untersuchungen. Bei einer ordnungsgemäßen Skalierung stellt ein Satz von gut durchdachten Ablaufverfolgungen die beste Möglichkeit dar. Es wird empfohlen, [WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) zum Sammeln und Anzeigen der Verfolgungsdaten zu testen.

![SFX-Partitionsintegrität](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###Bei der Initialisierung des Diensts oder Akteurs
Alle Ausnahmen, die vor der Initialisierung des Diensttyps auftreten, bewirken, dass der Prozess abstürzt. Für diese Art von Abstürzen wird im Anwendungsereignisprotokoll der Fehler des Diensts angezeigt. Dies sind die am häufigsten auftretenden Ausnahmen, bevor der Dienst initialisiert wird.

| Fehler | Beschreibung |
| --- | --- |
| System.IO.FileNotFoundException | Diese Ausnahmen treten oft aufgrund fehlender Assemblyabhängigkeiten auf. Überprüfen Sie die CopyLocal-Eigenschaft in Visual Studio oder im GAC für den Knoten.
| System.Runtime.InteropServices.COMException bei System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)|Hiermit wird angezeigt, dass der Name des registrierten Diensttyps nicht mit dem Dienstmanifest übereinstimmt. |

[WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) kann konfiguriert werden, um das Ereignisprotokoll der Anwendung für alle Ihre Knoten automatisch hochzuladen.

###RunAsync() oder OnActivateAsync()
Wenn der Absturz während der Initialisierung oder Ausführung Ihres registrierten Diensttyps oder Akteurs auftritt, wird die Ausnahme von Service Fabric abgefangen. Sie können dies über die EventSource-Anbieter anzeigen, die in den nächsten Schritten aufgeführt sind.

## Nächste Schritte

Weitere Informationen zu vorhandenen Diagnosefunktionen von Service Fabric:

* [Akteurdiagnose](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services-Diagnose](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=Nov15_HO4-->