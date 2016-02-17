<properties
   pageTitle="Erweiterte Verwendung des Reliable Services-Programmiermodells | Microsoft Azure"
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
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Erweiterte Verwendung des Reliable Services-Programmiermodells
Azure Service Fabric vereinfacht das Schreiben und Verwalten zuverlässiger zustandsloser und zustandsbehafteter Dienste (Reliable Services). In diesem Handbuch wird die erweiterte Verwendung des Reliable Services-Programmiermodells erläutert, mit dem Sie mehr Kontrolle und Flexibilität für Ihre Dienste erlangen. Machen Sie sich vor dem Lesen dieses Handbuchs mit dem [Reliable Services-Programmiermodell](service-fabric-reliable-services-introduction.md) vertraut.

## Basisklassen für zustandslose Dienste
Die Basisklasse „StatelessService“ bietet „RunAsync()“ und „CreateServiceInstanceListeners()“. Dies ist für die meisten zustandslosen Dienste ausreichend. Die Klasse StatelessServiceBase unterliegt StatelessService und stellt zusätzliche Lebenszyklusereignisse für Diensten bereit. Verwenden Sie eine Ableitung von „StatelessServiceBase“, falls zusätzliche Kontrolle oder Flexibilität erforderlich ist. Weitere Informationen finden Sie in der Referenzdokumentation für Entwickler zu [StatelessService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservice.aspx) und [StatelessServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statelessservicebase.aspx).

- `void OnInitialize(StatelessServiceInitializiationParameters)` "OnInitialize" ist die erste Methode, die von Service Fabric aufgerufen wird. Dienstinitialisierungsinformationen wie der Dienstname, die Partitions-ID, die Instanz-ID sowie Informationen zum Codepaket werden bereitgestellt. Hier sollte keine komplexe Verarbeitung erfolgen. Längere Initialisierungen sollten in "OnOpenAsync" erfolgen.

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` "OnOpenAsync" wird zur Verwendung der zustandslosen Dienstinstanz aufgerufen. Erweiterte Serviceinitialisierungsaufgaben können zu diesem Zeitpunkt gestartet werden.

- `Task OnCloseAsync(CancellationToken)` „OnCloseAsync“ wird aufgerufen, wenn die zustandslose Dienstinstanz ordnungsgemäß beendet wird. Dies kann der Fall sein, wenn Code für den Dienst aktualisiert, die Dienstinstanz aufgrund des Lastenausgleichs verschoben oder ein vorübergehender Fehler erkannt wird. "OnCloseAsync" kann verwendet werden, um Ressourcen sicher zu schließen, die Hintergrundverarbeitung anzuhalten, das Speichern des externen Status zu beenden oder bestehende Verbindungen zu deaktivieren.

- `void OnAbort()` „OnAbort“ wird aufgerufen, wenn das Herunterfahren der zustandslosen Dienstinstanz erzwungen wird. Diese Methode wird im Allgemeinen verwendet, wenn auf dem Knoten ein dauerhafter Fehler erkannt wird oder Service Fabric den Lebenszyklus der Dienstinstanz aufgrund von internen Fehlern nicht zuverlässig verwalten kann.

## Basisklassen für zustandsbehaftete Dienste
Die Basisklasse StatefulService sollte für die meisten zustandsbehafteten Dienste ausreichen. Wie bei zustandslosen Diensten unterliegt die Klasse StatefulServiceBase der Klasse StatefulService und stellt zusätzliche Lebenszyklusereignisse für Dienste bereit. Darüber hinaus können Sie damit einen zuverlässigen benutzerdefinierten Zustandsanbieter bereitstellen und optional Kommunikationslistener auf sekundären Replikaten unterstützen. Verwenden Sie eine Ableitung von „StatefulServiceBase“, falls zusätzliche Kontrolle oder Flexibilität erforderlich ist. Weitere Informationen finden Sie in der Referenzdokumentation für Entwickler zu [StatefulService](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservice.aspx) und [StatefulServiceBase](https://msdn.microsoft.com/library/microsoft.servicefabric.services.runtime.statefulservicebase.aspx).

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` „OnChangeRoleAsync“ wird immer dann aufgerufen, wenn der zustandsbehaftete Dienst die Rolle wechselt und z. B. zu einem primären oder sekundären Replikat wird. Primäre Replikate erhalten Schreibstatus (mit Erlaubnis zum Erstellen und Schreiben in den zuverlässigen Auflistungen). Sekundäre Replikate erhalten Lesestatus (können nur aus vorhandenen zuverlässigen Auflistungen lesen). Sie können die Hintergrundaufgaben infolge von Rollenänderungen starten oder aktualisieren, z. B. wenn schreibgeschützte Überprüfungen durchgeführt oder Berichte generiert werden oder auf einem sekundären Replikat Data Mining durchgeführt wird.

- `IStateProviderReplica CreateStateProviderReplica()` Bei einem zustandsbehafteten Dienst wird davon ausgegangen, dass dieser über einen zuverlässigen Zustandsanbieter verfügt. StatefulService verwendet die Klasse ReliableStateManager, welche zuverlässige Auflistungen (z. B. Wörterbücher und Warteschlangen) bereitstellt. Überschreiben Sie diese Methode, um die ReliableStateManager-Klasse zu konfigurieren, indem Sie eine ReliableStateManagerConfiguration an ihren Konstruktor übergeben. Dann können Sie benutzerdefinierte Zustandsserialisierungsprogramme bereitstellen und angeben, was geschieht, wenn Daten verloren gehen. Darüber hinaus können Sie Replikator-/Zustandsanbieter konfigurieren.

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

<!---HONumber=AcomDC_0204_2016-->