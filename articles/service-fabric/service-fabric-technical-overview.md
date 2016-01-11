<properties
   pageTitle="Service Fabric – Technische Übersicht | Microsoft Azure"
   description="Technischer Überblick über Service Fabric. Es werden wichtige Begriffe und die Architektur erläutert."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2015"
   ms.author="mfussell"/>

# Technischer Überblick über Service Fabric

Azure Service Fabric ist eine Plattform mit verteilten Systemen, die Ihnen das einfache Erstellen von skalierbaren, zuverlässigen Anwendungen für die Cloud mit geringer Latenz und benutzerfreundlicher Verwaltung ermöglicht. Dies bedeutet, dass Sie sich auf Ihre geschäftlichen Anforderungen konzentrieren und es Service Fabric überlassen können, sich um die Verfügbarkeit und Skalierung der Anwendung zu kümmern.

## Wichtige Begriffe

**Cluster**: Per Netzwerk verbundene virtuelle oder physische Computer, auf denen Anwendungsinstanzen bereitgestellt werden. Cluster können auf Tausende von Computern skaliert werden.

**Knoten**: Eine adressierbare Einheit in einem Cluster. Knoten weisen Merkmale wie Platzierungseigenschaften und eindeutige IDs auf. Knoten können einem Cluster beitreten und mit einer Betriebssysteminstanz korrelieren, für die „Fabric.exe“ ausgeführt wird.

**Anwendung/Anwendungstyp**: Eine Sammlung von Diensten (bzw. „Mikrodiensten“). Stellen Sie sich einen Anwendungstyp als Container für einen oder mehrere Diensttypen vor. Informationen dazu, auf welche Weise ein Cluster (der selbst aus mehreren Knoten besteht) mehrere Anwendungstypen enthalten kann, finden Sie im Artikel [Anwendungsmodell](service-fabric-application-model.md).

**Dienst/Diensttyp**: Code und Konfiguration, mit dem bzw. der eine eigenständige Funktion ausgeführt wird (kann unabhängig gestartet und ausgeführt werden), z. B. ein Warteschlangen- oder Datenbankdienst. Ein Anwendungstyp kann aus einem oder mehreren Diensttypen bestehen. Es gibt zwei Arten von Diensttypen:

- Zustandsloser Dienst: Ein Dienst, bei dem der Zustand im externen Speicher vorgehalten wird, z. B. in Azure-Datenbanken oder im Azure-Tabellenspeicher. Wenn ein Knoten ausfällt, auf dem eine Instanz dieses Diensts aktiv ist, wird auf einem anderen Knoten automatisch eine andere Instanz gestartet.

- Zustandsbehafteter Dienst: Ein Dienst, der über einen Zustand verfügt und die Zuverlässigkeit per Replikation zwischen Replikaten auf anderen Knoten im Cluster erreicht. Zustandsbehaftete Dienste verfügen über ein primäres Replikat und mehrere sekundäre Replikate. Wenn ein Knoten, auf dem ein Replikat dieses Diensts aktiv ist, ausfällt, wird ein neues Replikat auf einem anderen Knoten gestartet. Wenn der Knoten, der ausfällt, das primäre Replikat ist, wird ein sekundäres Replikat automatisch zum neuen primären Replikat höher gestuft.

**Anwendungsinstanz**: In einem Cluster können Sie viele Anwendungsinstanzen mit Anwendungstyp und jeweils einem bestimmten Namen erstellen. Jede Anwendungsinstanz kann über andere Anwendungsinstanzen desselben oder eines anderen Typs unabhängig verwaltet und versioniert werden. Außerdem wird darüber die Ressourcen- und Sicherheitsisolation definiert.

**Dienstinstanz**: Code, der für einen Diensttyp instanziiert wurde. Jede Dienstinstanz verfügt über einen eindeutigen Namen, der mit `fabric:/` beginnt, und ist einer Anwendungsinstanz mit einem bestimmten Namen zugeordnet.

**Anwendungspaket**: Die Sammlung der Dienstcodepakete und der Konfigurationsdateien, die für eine bestimmte Anwendung kombiniert werden. Dies sind die physischen Dateien, die bereitgestellt werden, und sie sind einfach in einem Layout im Format mit Dateien und Ordnern enthalten. Ein Anwendungspaket für eine E-Mail-Anwendung kann ein Warteschlangen-Dienstpaket, ein Front-End-Dienstpaket und ein Datenbank-Dienstpaket enthalten.

**Programmiermodelle**: In Service Fabric sind zwei Programmiermodelle zum Erstellen von Anwendungen verfügbar:

- Reliable Services: Eine API zum Erstellen von zustandslosen und zustandsbehafteten Diensten basierend auf den .NET-Klassen `StatelessService` und `StatefulService`. Der Zustand wird in zuverlässigen .NET-Sammlungen (Wörterbuch und Warteschlange) gespeichert. Es besteht zudem die Möglichkeit, verschiedene Kommunikationsstapel zu verknüpfen, z. B. Web-API und Windows Communication Foundation. Dieses Programmiermodell ist für Anwendungen geeignet, bei denen Sie Berechnungen über mehrere Zustandseinheiten durchführen müssen.

- Reliable Actors: Eine API zum Erstellen von zustandslosen und zustandsbehafteten Objekten mit dem Programmiermodell auf der Basis von virtuellen Akteuren, das für Anwendungen mit mehreren unabhängigen Zustands- und Compute-Einheiten geeignet ist.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
Weitere Informationen zu Service Fabric finden Sie unter:

- [Anwendungsmodell](service-fabric-application-model.md)
- [Anwendungsszenarien](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_1223_2015-->