<properties
   pageTitle="Visualisieren des Clusters mit Service Fabric-Explorer | Microsoft Azure"
   description="Service Fabric Explorer ist ein webbasiertes Tool zum Untersuchen und Verwalten von Cloudanwendungen und Knoten in einem Microsoft Azure Service Fabric-Cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/13/2016"
   ms.author="seanmck"/>

# Visualisieren Ihres Clusters mit Service Fabric Explorer

Service Fabric-Explorer ist ein webbasiertes Tool zum Untersuchen und Verwalten von Anwendungen und Knoten in einem Azure Service Fabric-Cluster. Service Fabric-Explorer wird direkt innerhalb des Clusters gehostet und ist daher immer verfügbar – unabhängig davon, wo der Cluster ausgeführt wird.

## Verbinden mit Service Fabric-Explorer

Wenn Sie die Anweisungen zum [Vorbereiten Ihrer Entwicklungsumgebung](service-fabric-get-started.md) befolgt haben, können Sie Service Fabric-Explorer auf dem lokalen Cluster starten, indem Sie zu http://localhost:19080/Explorer navigieren.

>[AZURE.NOTE]Wenn Sie Internet Explorer mit Service Fabric-Explorer zum Verwalten eines Remoteclusters verwenden, müssen Sie einige Internet Explorer-Einstellungen konfigurieren. Wechseln Sie zu **Extras** > **Einstellungen der Kompatibilitätsansicht**, und deaktivieren Sie **Intranetsites in Kompatibilitätsansicht anzeigen**, um sicherzustellen, dass alle Informationen richtig geladen werden.

## Grundlegendes zum Layout von Service Fabric-Explorer

Sie können in Service Fabric-Explorer navigieren, indem Sie die Strukturansicht auf der linken Seite verwenden. Auf der Stammebene der Struktur bietet das Clusterdashboard eine Clusterübersicht, einschließlich einer Zusammenfassung der Anwendungs- und Knotenintegrität.

![Service Fabric-Explorer-Clusterdashboard][sfx-cluster-dashboard]

### Anzeigen des Cluster-Layouts

Knoten in einem Service Fabric-Cluster werden in einem zweidimensionalen Raster aus Fehlerdomänen und Upgradedomänen angeordnet. Durch diese Anordnung wird dafür gesorgt, dass Ihre Anwendungen auch bei Hardwarefehlern und Anwendungsupgrades verfügbar bleiben. Mit der Clusterzuweisung können Sie anzeigen, wie der aktuelle Cluster angeordnet ist.

![Service Fabric-Explorer-Clusterzuweisung][sfx-cluster-map]

### Anzeigen von Anwendungen und Diensten

Der Cluster enthält zwei Unterstrukturen: eine für Anwendungen und eine für Knoten.

Sie können die Anwendungsansicht für die Navigation durch die logische Hierarchie von Service Fabric verwenden: Anwendungen, Dienste, Partitionen und Replikate.

Im Beispiel unten besteht die Anwendung **MyApp** aus zwei Diensten: **MyStatefulService** und **WebService**. Da **MyStatefulService** zustandsbehaftet ist, enthält er eine Partition mit einem primären und zwei sekundären Replikaten. Im Gegensatz dazu ist „WebSvcService“ zustandslos und enthält eine einzelne Instanz.

![Service Fabric-Explorer-Anwendungsansicht][sfx-application-tree]

Auf jeder Ebene der Struktur werden im Hauptbereich relevante Informationen zum Element angezeigt. Beispielsweise werden der Integritätsstatus und die Version für einen bestimmten Dienst angezeigt.

![Service Fabric-Explorer-Bereich für essentielle Informationen][sfx-service-essentials]

### Anzeigen der Knoten des Clusters

Die Knotenansicht zeigt das physische Layout des Clusters. Für einen Knoten können Sie überprüfen, für welche Anwendungen Code auf dem Knoten bereitgestellt wurde. Genauer gesagt können Sie anzeigen, welche Replikate derzeit darauf ausgeführt werden.

## Aktionen

Service Fabric-Explorer bietet eine schnelle Möglichkeit zum Aufrufen von Aktionen für die Knoten, Anwendungen und Dienste in Ihrem Cluster.

Um beispielsweise eine Anwendungsinstanz zu löschen, wählen Sie einfach die Anwendung in der Struktur auf der linken Seite und dann **Aktionen** > **Anwendung löschen** aus.

![Löschen einer Anwendung in Service Fabric-Explorer][sfx-delete-application]

In der folgenden Tabelle sind die für jede Entität verfügbaren Aktionen aufgeführt:

| **Entität** | **Aktion** | **Beschreibung** |
| ------ | ------ | ----------- |
| Anwendungstyp | Bereitstellung des Typs aufheben | Entfernt das Anwendungspaket aus dem Imagespeicher des Clusters. Dabei müssen zunächst alle Anwendungen dieses Typs entfernt werden. |
| Anwendung | Anwendung löschen | Löscht die Anwendung, einschließlich aller Dienste und Zustände (sofern vorhanden). |
| Dienst | Dienst löschen | Löscht den Dienst und seinen Zustand (sofern vorhanden). |
| Knoten | Aktivieren | Aktiviert den Knoten. |
|| Deaktivieren (anhalten) | Hält den Knoten im aktuellen Zustand an. Die Dienste werden weiterhin ausgeführt, Service Fabric verschiebt jedoch nur dann Elemente proaktiv in oder aus dem Dienst, wenn dies zur Vermeidung von Ausfällen oder Dateninkonsistenzen erforderlich ist. Diese Aktion wird normalerweise zum Aktivieren von Debuggingdiensten auf einem bestimmten Knoten verwendet, um sicherzustellen, dass sie während der Überprüfung nicht verschoben werden. |
|| Deaktivieren (neu starten) | Ermöglicht das sichere Verschieben aller In-Memory-Dienste von einem Knoten und schließt dauerhafte Dienste. Wird in der Regel verwendet, wenn die Hostprozesse oder Hostcomputer neu gestartet werden müssen. |
|| Deaktivieren (Daten entfernen) | Ermöglicht das sichere Schließen aller auf dem Knoten ausgeführten Dienste, nachdem ausreichend Ersatzreplikate erstellt wurden. Wird in der Regel verwendet, wenn ein Knoten (oder zumindest sein Speicher) dauerhaft außer Betrieb genommen wird. |
|| Knotenzustand entfernen | Entfernt die Replikatinformationen eines Knotens aus dem Cluster. Wird in der Regel verwendet, wenn ein Knoten, auf dem bereits ein Fehler aufgetreten ist, als nicht mehr wiederherstellbar gilt. |

Da viele Aktionen destruktiv sind, werden Sie aufgefordert, den Vorgang zu bestätigen, bevor die Aktion abgeschlossen wird.

>[AZURE.TIP]Jede Aktion, die mit Service Fabric-Explorer ausgeführt werden kann, kann auch mithilfe von PowerShell oder einer REST-API ausgeführt werden, um die Automation zu ermöglichen.



## Herstellen einer Verbindung mit einem Service Fabric-Remotecluster

Da Service Fabric-Explorer webbasiert ist und innerhalb des Clusters ausgeführt wird, kann von jedem Browser darauf zugegriffen werden, solange Sie die Endpunkte des Clusters kennen und über ausreichende Berechtigungen für den Zugriff verfügen.

### Ermitteln des Service Fabric-Explorer-Endpunkts für einen Remotecluster

Um Service Fabric-Explorer für einen bestimmten Cluster zu erreichen, verweisen Sie mit dem Browser auf:

http://&lt;your-cluster-endpoint&gt;:19080/Explorer

Die vollständige URL steht auch im Cluster Essentials-Bereich des Azure-Portal zur Verfügung.

### Herstellen einer Verbindung mit einem sicheren Cluster

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

<!---HONumber=AcomDC_0121_2016-->