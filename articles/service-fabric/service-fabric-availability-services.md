<properties
   pageTitle="Verfügbarkeit der Service Fabric-Dienste"
   description="Beschreibt Fehlererkennung, Failover und Wiederherstellung für Dienste"
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
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# Verfügbarkeit der Service Fabric-Dienste
Service Fabric-Dienste können zustandsbehaftet oder zustandslos sein. Dieser Artikel bietet eine Übersicht darüber, wie Service Fabric die Verfügbarkeit eines Diensts bei einem Ausfall aufrechterhält.

## Verfügbarkeit zustandsloser Service Fabric-Dienste
Eine zustandsloser Dienst ist ein Anwendungsdienst, der keinen [lokale persistenten Zustand](service-fabric-concepts-state.md) aufweist.

Um einen zustandslosen Dienst zu erstellen, muss die Anzahl der Instanzen des zustandslosen Diensts definiert werden, die im Cluster ausgeführt werden sollen. Hierbei handelt es sich um die Anzahl der Kopien der Anwendungslogik, die im Cluster instanziiert werden. Das Erhöhen der Anzahl der Instanzen ist die empfohlene Vorgehensweise für das Skalieren zustandsloser Dienste.

Wird in einer Instanz des zustandslosen Diensts ein Fehler erkannt, wird auf einem anderen geeigneten Knoten im Cluster eine neue Instanz erstellt.

## Verfügbarkeit zustandsbehafteter Service Fabric-Dienste
Eine zustandsbehafteter Dienst ist ein Dienst, dem ein Zustand zugeordnet ist. In Service Fabric ist ein zustandsbehafteter Dienst als eine Replikatgruppe modelliert. Jedes Replikat ist eine Instanz des Dienstcodes, die über eine Kopie des Status verfügt. Lese- und Schreibvorgänge erfolgen im selben Replikat (primäres Replikat). Änderungen des Zustands aufgrund von Schreibvorgängen werden in anderen Replikaten (aktive sekundäre Replikate) *repliziert*. Diese Kombination aus primären und aktiven sekundären Replikaten ist der Replikatgruppe des Diensts.

Es kann jeweils nur ein primäres Replikat für die Verarbeitung von Lese- und Schreibanforderungen vorhanden sein. Es können jedoch mehrere aktive sekundäre Replikate vorhanden sein. Die Anzahl der aktiven sekundären Replikate ist konfigurierbar. Eine höhere Anzahl von Replikaten bedeutet, dass eine höhere Anzahl von gleichzeitigen Software- und Hardwarefehlern toleriert werden kann.

In Fall eines Fehlers (wenn das primäre Replikat ausfällt) ernennt Dienst Fabric eines der aktiven sekundären Replikate zum neuen primären Replikat. Dieses aktive sekundäre Replikat verfügt bereits über die aktualisierte Version des Zustands (durch *Replikation*) und kann die Verarbeitung von Lese- und Schreibvorgängen fortsetzen.

Dieses Konzept, bei dem ein Replikat ein primäres oder ein aktives sekundäres Replikat ist, wird Replikatrolle genannt.

### Replikatrollen
Die Rolle eines Replikats wird zum Verwalten des Lebenszyklus des Zustands verwendet, der von diesem Replikat verwaltet wird. Ein Replikat mit der Rolle eines primären Replikats verarbeitet Leseanforderungen. Es verarbeitet auch Schreibanforderungen, indem es seinen Zustand aktualisiert und die Änderungen in den aktiven sekundären Replikaten in der Replikatgruppe repliziert. Ein aktives sekundäres Replikat empfängt die vom primären Replikat replizierten Zustandsänderungen und aktualisiert seinen Zustand entsprechend.

>[AZURE.NOTE]In komplexen Programmiermodellen, wie z. B. im [Reliable Actors-Framework](service-fabric-reliable-actors-introduction.md), ist das Konzept der Replikatrolle für Entwickler abstrahiert.

## Nächste Schritte

Informationen zu den Service Fabric-Konzepten finden Sie hier:

- [Skalierbarkeit der Service Fabric-Dienste](service-fabric-concepts-scalability.md)

- [Partitionieren von Service Fabric-Diensten](service-fabric-concepts-partitioning.md)

- [Definieren und Verwalten von Zuständen](service-fabric-concepts-state.md)
 

<!---HONumber=August15_HO6-->