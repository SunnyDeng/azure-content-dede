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
   ms.date="12/06/2015"
   ms.author="seanmck"/>

# Ihre Service Fabric-Anwendung und nächste Schritte
Ihre Azure Service Fabric-Anwendung wurde erstellt. Dieser Artikel beschreibt die Zusammensetzung des Projekts und einige mögliche nächste Schritte.

## Ihre Anwendung
Jede neue Anwendung umfasst ein Anwendungsprojekt. Es gibt möglicherweise je nach gewähltem Diensttyp ein oder zwei zusätzliche Projekte.

### Das Anwendungsprojekt
Das Anwendungsprojekt besteht aus:

- einem Satz von Verweisen auf die Dienste, aus denen die Anwendung besteht

- zwei Veröffentlichungsprofilen (Lokal und Cloud), die Sie zum Verwalten der Voreinstellungen für die Arbeit mit unterschiedlichen Umgebungen verwenden können – z. B. als Clusterendpunkt und ob standardmäßig Upgradebereitstellungen ausgeführt werden sollen

- zwei Anwendungsparameterdateien (Lokal und Cloud), mit denen Sie umgebungsspezifische Anwendungskonfigurationen verwalten können, wie z. B. die Anzahl der Partitionen, die für einen Dienst erstellt werden sollen

- ein Bereitstellungsskript, mit denen Sie Ihre Anwendung über die Befehlszeile oder als Teil einer automatisierten fortlaufenden Integrationspipeline bereitstellen können

- das Anwendungsmanifest, das die Anwendung beschreibt

### Reliable Services
Wenn Sie einen neuen Reliable Service hinzufügen, fügt Visual Studio Ihrer Lösung ein Dienstprojekt hinzu. Das Dienstprojekt enthält eine Klasse, die sich je nach ausgewähltem Typ entweder von `StatelessService` oder `StatefulService` ableitet.

### Reliable Actors
Wenn Sie einen neuen Reliable Actor hinzufügen, fügt Visual Studio zwei Projekte zur Projektmappe hinzu: ein Akteurprojekt und ein Schnittstellenprojekt.

Das Akteurprojekt definiert den Akteurtyp und (bei statusbehafteten Akteuren) dessen Zustand. Das Projekt enthält eine Schnittstelle, die andere Dienste zum Aufrufen des Akteurs nutzen können.

Beachten Sie, dass Akteurprojekte kein Standardverhalten für den Start enthalten, da Akteure von anderen Diensten aktiviert werden müssen. Ziehen Sie ein Reliable Service- oder ASP.NET-Projekt in Betracht, um Akteure zu erstellen und mit diesen zu interagieren.

### ASP.NET 5
Die ASP.NET 5-Vorlagen zur Verwendung in Service Fabric-Anwendungen sind fast identisch mit jenen für unabhängig erstellte ASP.NET 5-Projekte. Die einzigen Unterschiede sind:

- Das Projekt enthält einen **PackageRoot**-Ordner zum Speichern der ServiceManifest-Datei sowie Data- und Config-Pakete.

- Das Projekt verweist auf ein zusätzliches NuGet-Paket (Microsoft.ServiceFabric.AspNet.Hosting), das als Brücke zwischen der .NET Ausführungsumgebung (DNX) und Service Fabric fungiert.

## Nächste Schritte
### Hinzufügen eines Web-Front-Ends zur Anwendung
Service Fabric bietet Integration in ASP.NET 5 zum Erstellen von webbasierten Einstiegspunkten für Ihre Anwendung. Informationen über das Erstellen einer REST-Schnittstelle basierend auf der ASP.NET-WebAPI finden Sie unter [Hinzufügen eines Web-Front-Ends zur Anwendung][add-web-frontend].

### Erstellen eines Azure-Clusters
Das Service Fabric-SDK stellt einen lokalen Cluster zu Entwicklungs- und Testzwecken bereit. Informationen zum Erstellen eines Clusters in Azure finden Sie unter [Einrichten eines Service Fabric-Clusters im Azure-Portal][create-cluster-in-portal]

### Kostenloses Testen der Bereitstellung von Party Clustern in Azure

Wenn Sie das Bereitstellen und Verwalten von Anwendungen in Azure testen möchten, ohne eigene Cluster einrichten zu müssen, können Sie den kostenlosen [Party Cluster-Dienst](http://aka.ms/tryservicefabric) verwenden.

### Veröffentlichen der Anwendung in Azure
Sie können Ihre Anwendung direkt aus Visual Studio in einem Azure-Cluster veröffentlichen. Informationen dazu finden Sie unter [Veröffentlichen Ihrer Anwendung in Azure][publish-app-to-azure].

### Visualisieren des Clusters mit Service Fabric-Explorer
Service Fabric-Explorer bietet eine einfache Möglichkeit zum Visualisieren Ihres Clusters, einschließlich der bereitgestellten Anwendungen und des physischen Layouts. Informationen dazu finden Sie unter [Visualisieren des Clusters mit Service Fabric-Explorer][visualize-with-sfx].

### Versionierung und Upgrade Ihrer Dienste
Service Fabric ermöglicht eine unabhängige Versionierung und Upgrades von unabhängigen Diensten in einer Anwendung. Weitere Informationen finden Sie unter [Versionierung und Upgrade Ihrer Dienste][app-upgrade-tutorial].

### Konfigurieren der Continuous Integration in Visual Studio Team Services
Informationen zur Einrichtung einer Continuous Integration für die Service Fabric-Anwendung finden Sie unter [Konfigurieren der Continuous Integration in Visual Studio Team Services][ci-with-vso].



<!-- Links -->
[add-web-frontend]: ./service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: ./service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: ./service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: ./service-fabric-visualizing-your-cluster.md
[ci-with-vso]: ./service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: ./service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: ./service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_0114_2016-->