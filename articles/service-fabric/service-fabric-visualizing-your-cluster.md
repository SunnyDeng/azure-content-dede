<properties
   pageTitle="Visualisieren des Clusters mit Service Fabric Explorer"
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
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# Visualisieren des Clusters mit Service Fabric Explorer

Service Fabric Explorer ist ein grafisches Tool zum Untersuchen und Verwalten von Cloudanwendungen und Knoten in einem Microsoft Azure Service Fabric-Cluster. Mit Service Fabric Explorer können Sie sowohl Verbindungen mit lokalen Entwicklungsclustern als auch mit Azure-Clustern herstellen. Informationen zu den Service Fabric-PowerShell-Cmdlets finden Sie unter **Nächste Schritte**.

> [AZURE.NOTE]Die Erstellung von Service Fabric-Clustern in Azure ist noch nicht verfügbar.

## Einführung in Service Fabric Explorer

Stellen Sie sicher, dass Ihre lokale Entwicklungsumgebung gemäß der Anleitung unter [Einrichten Ihrer Service Fabric-Entwicklungsumgebung](service-fabric-get-started.md) eingerichtet wurde.

Führen Sie Service Fabric Explorer über Ihren lokalen Installationspfad (%Programme%\\Microsoft SDKs\\Service Fabric\\Tools\\ServiceFabricExplorer\\ServiceFabricExplorer.exe) aus. Mit dem Tool wird, falls vorhanden, automatisch eine Verbindung mit einem lokalen Entwicklungscluster hergestellt. Damit werden beispielsweise folgende Informationen zum Cluster angezeigt:

- Anwendungen, die auf dem Cluster ausgeführt werden
- Informationen zu den Knoten des Clusters
- Integritätsereignisse von den Anwendungen und Knoten
- Auslastung der Anwendungen im Cluster
- Überwachung des Anwendungsupgradestatus

![Visuelle Darstellung der Service Fabric-Cluster und der bereitgestellten Anwendungen][servicefabricexplorer]

Eine der wichtigen Visualisierungen ist die Clusterzuweisung, die auf dem Dashboard für den Cluster sichtbar ist (z. B. Klicken auf **Onebox/Lokaler Cluster**). Die Clusterzuweisung zeigt die Upgrade- und Fehlerdomänen sowie Informationen dazu an, welche Knoten welchen Domänen zugewiesen sind. Machen Sie sich unter [Technischer Überblick über Service Fabric](service-fabric-technical-overview.md) mit den wichtigen Service Fabric-Begriffen vertraut.

![Die Clusterzuweisung zeigt, zu welchen Upgrade- und Fehlerdomänen Knoten gehören.][clustermap]


## Anzeigen von Anwendungen und Diensten

Mit Service Fabric Explorer können Sie die Anwendungen untersuchen, die in Ihrem Cluster ausgeführt werden. Erweitern Sie die **Anwendungsansicht**, um ausführliche Informationen zu den Anwendungen, Diensten, Partitionen und Replikaten anzuzeigen.

Im Diagramm unten ist zu sehen, dass die Anwendung mit dem Namen **„fabric:/Stateful1Application“** über einen zustandslosen Dienst **„fabric:/Stateful1Application/MyFrontEnd“** und einen zustandsbehafteten Dienst **„fabric:/Stateful1Application/Stateful1“** verfügt. Der zustandslose Dienst verfügt über eine Partition mit einem Replikat, das auf **Node.4** ausgeführt wird. Der zustandsbehaftete Dienst verfügt über zwei Partitionen, jeweils mit drei Replikaten, die auf unterschiedlichen Knoten ausgeführt werden.

![Ansicht der Anwendungen, die auf dem Service Fabric-Cluster ausgeführt werden][applicationview]

Das Klicken auf eine Anwendung, einen Dienst, eine Partition oder ein Replikat liefert ausführliche Informationen zur Entität. Im Diagramm unten ist das Dashboard für die Integrität von Dienstreplikaten für ein primäres Replikat des zustandsbehafteten Diensts zu sehen. Es enthält die Rolle, den Knoten, auf dem es ausgeführt wird, die Lauschadresse, den Speicherort der Dateien auf dem Datenträger und die Integritätsereignisse.

![Ausführliche Informationen zu einem Service Fabric-Replikat][replicadetails]


## Herstellen einer Verbindung mit einem Service Fabric-Remotecluster

Klicken Sie zum Anzeigen eines Service Fabric-Remoteclusters auf **Verbinden**, um das Dialogfeld **Mit Service Fabric-Cluster verbinden** zu öffnen. Geben Sie den **ServiceFabric-Endpunkt** für das Cluster ein, und klicken Sie auf **Verbinden**. Der Service Fabric-Endpunkt ist normalerweise der öffentliche Name Ihres Clusterdiensts, der über Port 19000 lauscht.

![Einrichten einer Verbindung mit Ihrem Service Fabric-Remotecluster][connecttocluster]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

- [Testability – Übersicht](service-fabric-testability-overview.md)
- [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric-Anwendungsbereitstellung per PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[applicationview]: ./media/service-fabric-visualizing-your-cluster/applicationview.png
[clustermap]: ./media/service-fabric-visualizing-your-cluster/clustermap.png
[connecttocluster]: ./media/service-fabric-visualizing-your-cluster/connecttocluster.png
[replicadetails]: ./media/service-fabric-visualizing-your-cluster/replicadetails.png
[servicefabricexplorer]: ./media/service-fabric-visualizing-your-cluster/servicefabricexplorer.png

<!---HONumber=August15_HO6-->