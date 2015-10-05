<properties
   pageTitle="Erweiterte Verwendung des Reliable Services-Programmiermodells"
   description="Erfahren Sie mehr über die erweiterte Verwendung des Reliable Service-Programmiermodells von Service Fabric, das mehr Flexibilität für Ihre Dienste bietet."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="jesseb"/>

# Erweiterte Verwendung des Reliable Services-Programmiermodells
Service Fabric vereinfacht das Schreiben und Verwalten zuverlässiger zustandsloser und zustandsbehafteter Dienste (Reliable Services). In diesem Handbuch wird die erweiterte Verwendung des Reliable Services-Programmiermodells erläutert, mit dem Sie mehr Kontrolle und Flexibilität für Ihre Dienste erlangen. Machen Sie sich vor dem Lesen dieses Handbuchs mit dem [Reliable Services-Programmiermodell](service-fabric-reliable-services-introduction.md) vertraut.

## Basisklassen für zustandslose Dienste
Die Basisklasse StatelessService bietet "CreateCommunicationListener()" und "RunAsync()". Dies ist für die meisten zustandslosen Dienste ausreichend. Die Klasse StatelessServiceBase unterliegt StatelessService und stellt zusätzliche Lebenszyklusereignisse für Diensten bereit. Verwenden Sie eine Ableitung von „StatelessServiceBase“, falls zusätzliche Kontrolle oder Flexibilität erforderlich ist. Weitere Informationen finden Sie in der Referenzdokumentation für Entwickler zu [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) und [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx).

- `void OnInitialize(StatelessServiceInitializiationParameters)` "OnInitialize" ist die erste Methode, die von Service Fabric aufgerufen wird. Es werden Dienstinitialisierungsinformationen wie der Dienstname, die Partitions-ID, die Instanz-ID sowie Informationen zum Codepaket bereitgestellt. Hier sollte keine komplexe Verarbeitung erfolgen. Längere Initialisierungen sollten in "OnOpenAsync" erfolgen.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` "OnOpenAsync" wird zur Verwendung der zustandslosen Dienstinstanz aufgerufen. Erweiterte Serviceinitialisierungsaufgaben können zu diesem Zeitpunkt gestartet werden.

- `Task OnCloseAsync(CancellationToken)` "OnCloseAsync" wird aufgerufen, wenn die zustandslose Dienstinstanz ordnungsgemäß beendet wird. Dies kann der Fall sein, wenn Code für den Dienst aktualisiert, die Dienstinstanz aufgrund des Lastenausgleichs verschoben oder ein vorübergehender Fehler erkannt wird. "OnCloseAsync" kann verwendet werden, um Ressourcen sicher zu schließen, die Hintergrundverarbeitung anzuhalten, das Speichern des externen Status zu beenden oder bestehende Verbindungen zu deaktivieren.

- `void OnAbort()` "OnAbort" wird aufgerufen, wenn das Herunterfahren der zustandslosen Dienstinstanz erzwungen wird. Diese Methode wird im Allgemeinen verwendet, wenn auf dem Knoten ein dauerhafter Fehler erkannt wird oder Service Fabric den Lebenszyklus der Dienstinstanz aufgrund von internen Fehlern nicht zuverlässig verwalten kann.

## Basisklassen für zustandsbehaftete Dienste
Die Basisklasse StatefulService sollte für die meisten zustandsbehafteten Dienste ausreichen. Wie bei zustandslosen Diensten unterliegt die Klasse StatefulServiceBase der Klasse StatefulService und stellt zusätzliche Lebenszyklusereignisse für Dienste bereit. Darüber hinaus können Sie damit einen zuverlässigen benutzerdefinierten Zustandsanbieter bereitstellen und optional Kommunikationslistener auf sekundären Replikaten unterstützen. Verwenden Sie eine Ableitung von „StatefulServiceBase“, falls zusätzliche Kontrolle oder Flexibilität erforderlich ist. Weitere Informationen finden Sie in der Referenzdokumentation für Entwickler zu [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) und [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx).

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` "OnChangeRoleAsync" wird immer dann aufgerufen, wenn der zustandsbehaftete Dienst die Rolle wechselt und z. B. zu einem primären oder sekundären Replikat wird. Primäre Replikate erhalten Schreibstatus (mit Erlaubnis zum Erstellen und Schreiben in den zuverlässigen Auflistungen) und sekundäre Replikate Lesestatus (können nur aus vorhandenen zuverlässigen Auflistungen lesen). Sie können die Hintergrundaufgaben infolge von Rollenänderungen starten oder aktualisieren, z. B. wenn schreibgeschützte Überprüfungen durchgeführt oder Berichte generiert werden oder auf einem sekundären Replikat Data Mining durchgeführt wird.

- `IStateProviderReplica CreateStateProviderReplica()` Bei einem zustandsbehafteten Dienst wird davon ausgegangen, dass dieser über einen zuverlässigen Zustandsanbieter verfügt. StatefulService verwendet die Klasse ReliableStateManager, welche zuverlässige Auflistungen (z. B. Wörterbücher und Warteschlangen) bereitstellt. Sie möchten einen eigenen Anbieter angeben, wenn Sie den Zustand selbst verwalten möchten, oder die Funktionalität eines der integrierten Zustandsanbieter erweitern.

- `bool EnableCommunicationListenerOnSecondary { get; }` Standardmäßig werden Kommunikationslistener nur auf primären Replikaten erstellt. Mit StatefulService und StatefulServiceBase können Sie diese Eigenschaft außer Kraft setzen, damit Kommunikationslistener auf sekundären Replikaten erstellt werden können. Sie können auf Ihren sekundären Replikaten die Verarbeitung schreibgeschützter Anforderungen zulassen, um den Durchsatz bei leseintensiven Arbeitsauslastungen zu verbessern.

    > [AZURE.NOTE]Sie sind dafür verantwortlich, sicherzustellen, dass Ihre sekundären Replikate nicht versuchen, Daten in zuverlässigen Auflistungen zu erstellen oder zu schreiben. Versuche, auf ein sekundäres Replikat zu schreiben, verursachen eine Ausnahme, die in ungelöstem Zustand dazu führt, dass das Replikat geschlossen und erneut geöffnet wird.

StatefulServiceBase bietet außerdem die gleichen vier Lebenszyklusereignisse wie StatelessServiceBase, mit derselben Semantik und identischen Anwendungsfälle:

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## Nächste Schritte
Erweiterte Themen im Zusammenhang mit Service Fabric finden Sie in den folgenden Artikeln.

- [Konfigurieren zustandsbehafteter Reliable Services](service-fabric-reliable-services-configuration.md)

- [Einführung in Service Fabric-Integrität](service-fabric-health-introduction.md)

- [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [Platzierungseinschränkungen – Übersicht](service-fabric-placement-constraint.md)

<!---HONumber=Sept15_HO4-->