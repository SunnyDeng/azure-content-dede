<properties
   pageTitle="Nächste Schritte bei der Service Fabric-Projekterstellung | Microsoft Azure"
   description="Dieser Artikel enthält Links zu einer Reihe wichtiger Entwicklungsaufgaben für Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/16/2015"
   ms.author="seanmck"/>

# Ihre Service Fabric-Anwendung und nächste Schritte
Ihre Service Fabric-Anwendung wurde nun erstellt. Dieser Artikel beschreibt die Zusammensetzung des Projekts und einige mögliche nächste Schritte.

## Ihre Anwendung
Jede neue Anwendung umfasst ein Anwendungsprojekt. Es gibt möglicherweise je nach gewähltem Diensttyp ein oder zwei zusätzliche Projekte.

### Das Anwendungsprojekt
Das Projekt für die Anwendung besteht aus: - Einer Reihe von Verweisen auf die Dienste, die die Anwendung bilden - Zwei Veröffentlichungsprofilen (lokal und Cloud), mit denen Sie die Voreinstellungen für unterschiedliche Umgebungen verwalten können, wie z. B. beim standardmäßigen Ausführen von Upgradebereitstellungen für Clusterendpunkte - Zwei Anwendungsparameterdateien (lokal und Cloud), mit denen Sie umgebungsspezifische Anwendungskonfigurationen verwalten können, wie z. B. die Anzahl der Partitionen, die für einen Dienst erstellt werden - Ein Bereitstellungsskript, mit dem Sie Ihre Anwendung über die Befehlszeile oder als Teil einer automatisierten fortlaufenden Integrationspipeline bereitstellen können - Das Anwendungsmanifest, das die Anwendung beschreibt

### Reliable Services
Wenn Sie einen neuen Reliable Service hinzufügen, fügt Visual Studio der Projektmappe ein Dienstprojekt hinzu. Das Dienstprojekt enthält eine Klasse, die sich je nach ausgewähltem Typ entweder von `StatelessService` oder `StatefulService` ableitet.

### Reliable Actors
Wenn Sie einen neuen Reliable Actor hinzufügen, fügt Visual Studio zwei Projekte zur Projektmappe hinzu: ein Akteurprojekt und ein Schnittstellenprojekt.

Das Akteurprojekt definiert den Akteurtyp und (bei statusbehafteten Akteuren) dessen Zustand. Das Projekt enthält eine Schnittstelle, die andere Dienste zum Aufrufen des Akteurs nutzen können.

Beachten Sie, dass Akteurprojekte kein Standardverhalten für den Start enthalten, da Akteure von anderen Diensten aktiviert werden müssen. Ziehen Sie ein Reliable Service- oder ASP.NET-Projekt in Betracht, um Akteure zu erstellen und mit diesen zu interagieren.

### ASP.NET 5
Die ASP.NET 5-Vorlagen zur Verwendung in Service Fabric-Anwendungen sind fast identisch mit jenen für unabhängig erstellte ASP.NET 5-Projekte. Dies sind die einzigen Unterschiede: - Das Projekt enthält einen Ordner **PackageRoot** zum Speichern des ServiceManifests zusammen mit Daten und Konfigurationspaketen. - Das Projekt enthält einen Verweis auf ein zusätzliches NuGet-Paket (Microsoft.ServiceFabric.AspNet.Hosting), das als Brücke zwischen DNX und Service Fabric fungiert.

## Nächste Schritte
### Hinzufügen eines Web-Front-Ends zur Anwendung
Service Fabric bietet Integration in ASP.NET 5 zum Erstellen von webbasierten Einstiegspunkten für Ihre Anwendung. Informationen über das Erstellen einer REST-Schnittstelle basierend auf der ASP.NET-WebAPI finden Sie unter [Hinzufügen eines Web-Front-Ends zur Anwendung][add-web-frontend].

### Erstellen eines Azure-Clusters
Das Service Fabric-SDK stellt einen lokalen Cluster zu Entwicklungs- und Testzwecken bereit. Informationen zum Erstellen eines Clusters in Azure finden Sie unter [Einrichten eines Service Fabric-Clusters im Azure-Portal][create-cluster-in-portal]

### Veröffentlichen der Anwendung in Azure
Sie können Ihre Anwendung direkt aus Visual Studio in einem Azure-Cluster veröffentlichen. Informationen dazu finden Sie unter [Veröffentlichen Ihrer Anwendung in Azure][publish-app-to-azure].

### Visualisieren des Clusters mit Service Fabric-Explorer
Service Fabric-Explorer bietet eine einfache Möglichkeit zum Visualisieren Ihres Clusters, einschließlich der bereitgestellten Anwendungen und des physischen Layouts. Informationen dazu finden Sie unter [Visualisieren des Clusters mit Service Fabric-Explorer][visualize-with-sfx]

### Versionierung und Upgrade Ihrer Dienste
Service Fabric ermöglicht eine unabhängige Versionierung und Upgrades von unabhängigen Diensten in einer Anwendung. Weitere Informationen finden Sie unter [Versionierung und Upgrade Ihrer Dienste][app-upgrade-tutorial].

### Konfigurieren der fortlaufenden Integration in Visual Studio Online
Informationen zur Einrichtung eines kontinuierlichen Integrationsverfahren für die Service Fabric-Anwendung finden Sie unter [Konfigurieren der fortlaufenden Integration in Visual Studio Online][ci-with-vso].


<!-- Links -->
[add-web-frontend]: ./service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: ./service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: ./service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: ./service-fabric-visualizing-your-cluster.md
[ci-with-vso]: ./service-fabric-configure-continuous-integration-with-vso.md
[reliable-services-webapi]: ./service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: ./service-fabric-application-upgrade-tutorial.md

<!---HONumber=Nov15_HO4-->