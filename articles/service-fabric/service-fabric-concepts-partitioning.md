<properties
   pageTitle="Partitionieren von Service Fabric-Diensten"
   description="Beschreibt, wie Sie die Service Fabric-Dienste partitionieren."
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="aprameyr"/>

# Partitionieren von Service Fabric-Diensten
Service Fabric erleichtert das Entwickeln skalierbare zustandsbehafteter Dienste, indem es die Partitionierung des Dienstzustands ermöglicht und jede Partition auf einer Teilmenge des Gesamtzustands ausgeführt wird. Jede Partition wird als Einheit behandelt, die mit [hoher Verfügbarkeit](service-fabric-availability-services.md) bereitgestellt wird. Die Replikate der Partitionen werden auf den Knoten im Cluster verteilt und ausgeglichen.

> [AZURE.NOTE]Zustandslose Dienste können ebenfalls partitioniert werden, dieses Szenario ist jedoch selten und für die meisten Service Fabric-Dienste nicht erforderlich.

Es sind drei verschiedene Partitionierungsschemas verfügbar.

## Singleton-Partitionierungsschema
Dieses Schema wird verwendet, um anzugeben, dass der Dienst keine Partitionierung erfordert.

## Namensbasiertes Partitionierungsschema
Dieses Schema wird verwendet, um einen festen Namenssatz für jede Partition des Diensts anzugeben. Einzelne Partitionen können anhand ihres Namens gesucht werden.

## Bereichsbasiertes Partitionierungsschema
Dieses Schema wird verwendet, um einen Integer-Bereich (identifiziert durch einen niedrigen und hohen Schlüssel) und eine Anzahl von Partitionen (n) anzugeben. Es werden n Partitionen erstellt, die jeweils für einen nicht überlappenden untergeordneten Bereich verantwortlich sind. Beispiel: Ein bereichsbasiertes Partitionierungsschema (für einen Dienst mit drei Replikaten) mit einem niedrigen Schlüssel 0, einem hohen Schlüssel 99 und einer Anzahl von 4 erstellt 4 Partitionen, wie unten dargestellt.

![Bereichspartitionierung](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Üblicherweise wird ein Hash für einen eindeutigen Schlüssel in einem Dataset erstellt. Ein Schlüssel kann beispielsweise eine Fahrgestellnummer, Mitarbeiter-ID oder eine eindeutige Zeichenfolge sein. Mit dem eindeutigen Schlüssel wird dann ein langer Hashcode erstellt (Modulo-Vorgang zum Berechnen des Schlüsselbereichs), den Sie als Ihren Schlüssel verwenden. Sie können den obere und untere Grenzwert des zulässigen Schlüsselbereichs angeben.

Darüber hinaus sollten Sie eine ausreichend hohe Anzahl an Partitionen planen, um den ungünstigsten Fall der Ressourcenauslastung (z. B. Speicherbeschränkungen oder Netzwerkbandbreite) zur berücksichtigen, und gleichzeitig darauf achten, dass die Partitionen nicht zu spärlich sind.

### Auswählen eines Hashalgorithmus
Beim Hashing spielt die Wahl des Hashalgorithmus eine wichtige Rolle. Ausschlaggebend ist, ob ähnliche nah beieinanderliegende Schlüssel gruppiert werden sollen (lokalitätsbasiertes Hashing), oder ob die Aktivität über alle Partitionen weitläufig verteilt werden soll (verteiltes Hashing)

Hilfreiche Informationen zur Wahl des Hashalgorithmus finden Sie auf der [Wikipedia-Seite über Hashfunktionen](http://en.wikipedia.org/wiki/Hash_function).

> [AZURE.NOTE]Es ist nicht möglich, die Anzahl der Partitionen oder den Typ des Partitionierungsschemas für einen Dienst zu ändern, nachdem der Dienst erstellt wurde.

## Nächste Schritte

Informationen zu den Service Fabric-Konzepten finden Sie hier:

- [Verfügbarkeit der Service Fabric-Dienste](service-fabric-availability-services.md)

- [Skalierbarkeit der Service Fabric-Dienste](service-fabric-concepts-scalability.md)
 

<!---HONumber=Oct15_HO3-->