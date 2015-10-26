<properties
   pageTitle="Behandeln von Problemen beim Service Fabric-Clustersetup | Microsoft Azure"
   description="Dieser Artikel enthält eine Reihe von Vorschlägen für die Problembehandlung Ihres lokalen Entwicklungsclusters."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/13/2015"
   ms.author="seanmck"/>

# Problembehandlung beim Einrichten des Clusters für die lokale Entwicklung

Wenn beim Interagieren mit Ihrem lokalen Entwicklungscluster ein Problem auftritt, sind bei der Lösung unter Umständen die folgenden Vorschläge hilfreich.

## Fehler bei der Clustereinrichtung

### Bereinigen von Service Fabric-Protokollen nicht möglich

#### Problem

Während der Ausführung des DevClusterSetup-Skripts wird ein Fehler der folgenden Art angezeigt:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### Lösung

Schließen Sie das aktuelle PowerShell-Fenster, und starten Sie ein neues PowerShell-Fenster als Administrator. Nun sollte es möglich sein, das Skript auszuführen.

## Clusterverbindungsfehler

### Ausnahme bei der Typinitialisierung

#### Problem

Beim Herstellen einer Verbindung mit dem Cluster in PowerShell oder im Service Fabric Explorer wird für System.Fabric.Common.AppTrace eine TypeInitializationException angezeigt.

#### Lösung

Die Pfadvariable wurde während der Installation nicht ordnungsgemäß festgelegt. Melden Sie sich von Windows ab und dann wieder an. Dadurch wird der Pfad vollständig aktualisiert.

### Fehler „Objekt ist geschlossen“ für Clusterverbindung

#### Problem

Für einen Aufruf von Connect-ServiceFabricCluster tritt ein Fehler der folgenden Art auf:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### Lösung

Schließen Sie das aktuelle PowerShell-Fenster, und starten Sie ein neues PowerShell-Fenster als Administrator. Nun sollte es möglich sein, die Verbindung herzustellen.

### FabricConnectionDeniedException

#### Problem

Beim Debuggen von Visual Studio erhalten Sie eine FabricConnectionDeniedException.

#### Lösung

Dieser Fehler tritt gewöhnlich beim Versuch auf, einen Diensthostprozess manuell zu starten, anstatt der Service Fabric-Laufzeit das Starten zu überlassen.

Stellen Sie sicher, dass in Ihrer Projektmappe keine Dienstprojekte als Startprojekte festgelegt sind. Nur Service Fabric-Anwendungsprojekte sollten als Startprojekte festgelegt werden.


## Nächste Schritte

- [Grundlagen und Problembehandlung für Cluster mit Systemintegritätsberichten](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualisieren Ihres Clusters mit Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!---HONumber=Oct15_HO3-->