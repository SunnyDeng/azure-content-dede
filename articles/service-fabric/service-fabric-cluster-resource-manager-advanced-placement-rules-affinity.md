<properties
   pageTitle="Clusterressourcen-Manager von Service Fabric – Affinität | Microsoft Azure"
   description="Übersicht über zusätzlichen Platzierungsrichtlinien und -regeln für Service Fabric-Dienste"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Konfigurieren und Verwenden der Dienstaffinität in Service Fabric

Affinität ist einer der Aspekte, der zumindest auf den ersten Blick, für eine Microservice-Umgebung eigentlich nicht sinnvoll ist. Das liegt daran, dass Affinität in einer Microservice-Umgebung wirklich nicht sinnvoll ist. Affinität ist ein Steuerungsinstrument, das hauptsächlich bereitgestellt wird, um den Übergang von größeren zuvor monolithischen Anwendungen in die Welt der Cloud und Microservices zu vereinfachen.

Angenommen, Sie überführen eine größere App bzw. eine, die nicht mit Blick auf Microservices entwickelt wurde, in Service Fabric. Dies ist tatsächlich ziemlich üblich, und wir hatten einige Kunden (intern und extern) in dieser Situation. Sie beginnen mit dem Überführen der gesamten App in die Umgebung, wandeln sie in ein Paket um, und führen sie aus. Anschließend unterteilen Sie sie in verschiedene kleinere Dienste, die alle miteinander kommunizieren.

Doch dann ertönt ein „Huch“. Das „Huch“ gehört meist zu einer dieser Kategorien:

1. Es stellt sich heraus, dass Komponente X in der monolithischen App über eine nicht dokumentierte Abhängigkeit von Komponente Y verfügte, die wir gerade in einen Dienst umgewandelt und im Cluster verschoben haben. Die ist jetzt unterbrochen.
2.	Diese Elemente kommunizieren über (lokale Named Pipes | freigegebenen Speicher | Dateien auf dem Datenträger), doch ich muss unbedingt in der Lage sein, sie unabhängig zu aktualisieren, um die Sache ein wenig zu beschleunigen.
3.	Alles ist in Ordnung, doch es stellt sich heraus, dass diese beiden Komponenten sehr kommunikativ bzw. leistungsabhängig sein. Denn nachdem ich sie in getrennte Dienste verschoben hatte, ging meine Leistung den Bach herunter.

In allen diesen Fällen wollen wir nicht, dass unser Aufwand für das Refactoring umsonst war, und auch nicht zum monolithischen Ansatz zurück. Doch wir wollen versuchen, die Dinge wieder auf die Reihe zu bekommen, sodass sie sich lokal „anfühlen“, bis wir eine Lösung gefunden haben.

Vorgehensweise Wir könnten also versuchen, mit Affinität zu arbeiten.

## Funktionsweise der Affinität
Um für Affinität zu sorgen, definieren Sie eine Affinitätsbeziehung zwischen zwei Diensten. Sie können sich das so vorstellen, dass ein Dienst auf einen anderen „zeigt“ und angibt, dass dieser Dienst nur dort ausgeführt werden kann, wo auch der andere ausgeführt wird. Mitunter wird dies als Beziehung über- und untergeordneter Dienste bezeichnet (wobei der untergeordnete auf den übergeordneten Dienst zeigt). Dadurch stellen Sie sicher, dass die Replikate oder Instanzen eines Diensts auf denselben Knoten platziert werden wie die Replikate oder Instanzen des Dienst, zu dem eine Affinität besteht.

``` csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

## Verschiedene Affinitätsoptionen
Affinität wird mithilfe eines von mehreren Korrelationschemen dargestellt und weist zwei verschiedene Modi auf. Der gängigste Affinitätsmodus ist, was wir als „NonAlignedAffinity“ bezeichnen. Bei „NonAlignedAffinity“ werden die Replikate oder Instanzen der anderen Dienste auf denselben Knoten platziert. Der andere Modus heißt „AlignedAffinity“. „AlignedAffinity“ wird für zustandsbehaftete Dienste verwendet. Das Konfigurieren eines zustandsbehafteten Diensts mit „AlignedAffinity“ mit einem anderen zustandsbehafteten Dienst stellt sicher, dass die primären Replikate dieser Dienste auf denselben Knoten platziert werden und dass jedes Paar sekundärer Replikate auch auf denselben Knoten platziert wird. Es ist auch möglich, (jedoch nicht so üblich) „NonAlignedAffinity“ für zustandsbehaftete Dienste zu konfigurieren. In diesem Fall erhalten Sie nahezu dasselbe Verhalten wie bei zustandslosen Diensten. Die verschiedenen Replikate der beiden zustandsbehafteten Dienste werden auf denselben Knoten angeordnet, doch es wird nicht versucht sicherzustellen, dass sich die primären und sekundären Replikate auf denselben Knoten befinden.

![Affinitätsmodi und ihre Auswirkungen][Image1]

### Anstreben des gewünschten Zustands
Es gibt einige Unterschiede zwischen Affinität und monolithischen Architekturen. Fast alle lassen sich zu der Tatsache zusammenfassen, dass eine Affinitätsbeziehung für bestes Bemühen steht, da es sich grundlegend um verschiedene Dienste handelt, die beispielsweise unabhängig voneinander ausfallen können. Andere Aspekte können dazu führen, dass andere Replikate des Diensts getrennt werden, z. B. Kapazitätseinschränkungen.


### Ketten im Vergleich zu Sternen
Derzeit können wir Ketten von Affinitätsbeziehungen nicht modellieren. Dies bedeutet, dass ein Dienst, der ein untergeordneter Dienst einer Affinitätsbeziehung ist, kein übergeordneter Dienst in einer anderen Affinitätsbeziehung sein kann. Wenn Sie diese Art von Beziehung modellieren möchten, müssen Sie sie als Stern und nicht als Kette modellieren, indem Sie den untersten untergeordneten Dienst als übergeordneten Dienst des übergeordneten Dienst des „mittleren“ untergeordneten Diensts darstellen.

![Ketten im Vergleich zu Sternen im Kontext von Affinitätsbeziehungen][Image2]

Eine weitere Eigenschaft derzeitiger Affinitätsbeziehungen ist, dass sie gerichtet sind. Dies ist ein zarter Hinweis, doch dies bedeutet, dass die Affinitätsregel nur erzwingt, dass sich der untergeordnete Dienst dort befindet, wo der übergeordnete Dienst ist. Sollte für den übergeordneten Dienst plötzlich ein Failover auf einen anderen Knoten erfolgen (oder eine andere begrenzte Aktion, die nur die Verschiebung des übergeordneten Diensts erzwingt), erkennt der Ressourcen-Manager nicht unbedingt, dass etwas falsch ist, bis er bemerkt, dass sich der untergeordnete nicht beim übergeordneten Dienst befindet. Die Beziehung wird nicht unmittelbar erzwungen.

### Unterstützung der Partitionierung
Der letzte wichtige Affinitätsaspekt ist, dass Affinitätsbeziehungen nicht unterstützt werden, wenn das übergeordnete Element partitioniert ist. Dies wird ggf. künftig unterstützt werden, ist derzeit aber nicht zulässig.

## Nächste Schritte
- Weitere Informationen zu den anderen Optionen, die für die Konfiguration von Diensten zur Verfügung stehen, finden Sie im Thema zu den anderen verfügbaren Clusterressourcen-Manager-Konfigurationen, [Konfigurieren von Diensten](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png

<!---HONumber=AcomDC_0316_2016-->