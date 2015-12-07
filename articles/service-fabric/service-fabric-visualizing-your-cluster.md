<properties
   pageTitle="Visualisieren des Clusters mit Service Fabric-Explorer | Microsoft Azure"
   description="Service Fabric Explorer ist ein nützliches GUI-Tool zum Untersuchen und Verwalten von Cloudanwendungen und Knoten in einem Microsoft Azure Service Fabric-Cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/30/2015"
   ms.author="jesseb"/>

# Visualisieren des Clusters mit Service Fabric Explorer

Service Fabric-Explorer ist ein webbasiertes Tool zum Untersuchen und Verwalten von Anwendungen und Knoten in einem Service Fabric-Cluster. Service Fabric-Explorer wird direkt innerhalb des Clusters gehostet, daher ist er immer verfügbar, unabhängig davon, wo der Cluster ausgeführt wird.

## Verbinden mit Service Fabric-Explorer

Wenn Sie die Anweisungen zum [Vorbereiten Ihrer Entwicklungsumgebung](service-fabric-get-started.md) befolgt haben, können Sie Service Fabric-Explorer auf dem lokalen Cluster starten, indem Sie zu http://localhost:19080/Explorer navigieren.

>[AZURE.NOTE]Wenn Sie Internet Explorer (IE) mit Service Fabric-Explorer zum Verwalten eines Remoteclusters verwenden, müssen Sie einige IE-Einstellungen konfigurieren. Wechseln Sie zu **Extras -> Einstellungen der Kompatibilitätsansicht**, und deaktivieren Sie **Intranetsites in Kompatibilitätsansicht anzeigen**, um sicherzustellen, dass alle Informationen ordnungsgemäß geladen werden.

## Grundlegendes zum Layout von Service Fabric-Explorer

Sie können mithilfe der Strukturansicht auf der linken Seite in Service Fabric-Explorer navigieren. Auf der Stammebene der Struktur bietet das Clusterdashboard eine Clusterübersicht, einschließlich einer Zusammenfassung der Anwendungs- und Knotenintegrität.

![Service Fabric-Explorer-Clusterdashboard][sfx-cluster-dashboard]

### Die Clusterzuweisung

Knoten in einem Service Fabric-Cluster befinden sich über einem zweidimensionalen Raster an Fehlerdomänen und Upgradedomänen, um sicherzustellen, dass Ihre Anwendungen auch bei Hardwareausfällen und Anwendungsupgrades zur Verfügung stehen. Mithilfe der Clusterzuweisung können Sie anzeigen, wie der aktuelle Cluster angeordnet ist.

![Service Fabric-Explorer-Clusterzuweisung][sfx-cluster-map]

### Anzeigen von Anwendungen und Diensten

Der Cluster enthält zwei Unterstrukturen: eine für Anwendungen und eine für Knoten.

Die Anwendungsansicht ermöglicht die Navigation durch die logische Hierarchie von Service Fabric: Anwendungen, Dienste, Partitionen und Replikate.

Im Beispiel unten besteht die **MyApp**-Anwendung aus zwei Diensten: **MyStatefulService** und **WebSvcService**. Da **MyStatefulService** statusbehaftet ist, enthält er eine Partition mit einem primären und zwei sekundären Replikaten. Im Gegensatz dazu ist „WebSvcService“ statusfrei und enthält eine einzelne Instanz.

![Service Fabric-Explorer-Anwendungsansicht][sfx-application-tree]

Auf jeder Ebene der Struktur werden im Hauptbereich relevante Informationen zum Element angezeigt. Zum Beispiel werden der Integritätsstatus und die Version für einen bestimmten Dienst angezeigt.

![Service Fabric-Explorer-Bereich für essentielle Informationen][sfx-service-essentials]

### Anzeigen der Knoten des Clusters

Die Knotenansicht zeigt das physische Layout des Clusters. Sie können für einen angegebenen Knoten überprüfen, welche Anwendungen über auf diesem Knoten bereitgestellten Code verfügen, und insbesondere, welche Replikate derzeit dort ausgeführt werden.

## Aktionen mit Service Fabric-Explorer

Service Fabric-Explorer bietet eine schnelle Möglichkeit zum Aufrufen von Aktionen für die Knoten, Anwendungen und Dienste in Ihrem Cluster.

Um beispielsweise eine Anwendungsinstanz zu löschen, wählen Sie einfach die Anwendung in der Struktur auf der linken Seite und dann „Aktionen > Anwendung löschen“ aus.

![Löschen einer Anwendung in Service Fabric-Explorer][sfx-delete-application]

Da viele Aktionen destruktiv sind, werden Sie aufgefordert, den Löschvorgang zu bestätigen, bevor die Aktion abgeschlossen wird.

>[AZURE.NOTE]Jede Aktion, die mit Service Fabric-Explorer ausgeführt werden kann, kann auch mithilfe von PowerShell oder einer REST-API unter Verwendung von Automatisierung ausgeführt werden.



## Herstellen einer Verbindung mit einem Service Fabric-Remotecluster

Da Service Fabric-Explorer webbasiert ist und innerhalb des Clusters ausgeführt wird, kann von jedem Browser darauf zugegriffen werden, solange Sie die Endpunkte des Clusters kennen und über ausreichende Berechtigungen für den Zugriff verfügen.

### Ermitteln des Service Fabric-Explorer-Endpunkts für einen Remotecluster

Um Service Fabric-Explorer für einen bestimmten Cluster zu erreichen, verweisen Sie mit dem Browser auf:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

Die vollständige URL steht auch im Cluster Essentials-Bereich des Azure-Portal zur Verfügung.

### Verbinden mit einem sicheren Cluster

Sie können den Zugriff auf den Service Fabric-Cluster steuern, indem Sie die Präsentation eines Zertifikats durch Clients zum Herstellen einer Verbindung erfordern.

Wenn Sie versuchen, eine Verbindung mit Service Fabric-Explorer auf einem sicheren Cluster herzustellen, fordert der Browser ein Zertifikat an, um Zugriff zu gewähren.

## Nächste Schritte

- [Testability – Übersicht](service-fabric-testability-overview.md)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric-Anwendungsbereitstellung per PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png

<!---HONumber=AcomDC_1125_2015-->