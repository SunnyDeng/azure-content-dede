<properties
   pageTitle="Diagnose und Problembehandlung von Service Fabric-Diensten"
   description="Enthält grundlegende Informationen und Lernprogramme zur Diagnose, Überwachung und Problembehandlung von Service Fabric-Diensten."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2015"
   ms.author="ryanwi"/>

# Diagnostizieren und Überwachen von Service Fabric-Diensten
Überwachung, Erkennung, Diagnose und Problembehandlung ermöglichen das Ausführen von Diensten mit minimalen Unterbrechungen für Benutzer. Weitere Informationen finden Sie unter:

- [Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Einrichten von Application Insights für Service-Fabric-Anwendungen](service-fabric-diagnostics-application-insights-setup.md)
- [Beheben von Problemen mit Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md)
- [Diagnose und Leistungsüberwachung für Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
- [Diagnose und Leistungsüberwachung für Reliable Services](service-fabric-reliable-services-diagnostics.md)

## Beheben von Clusterproblemen
Die folgende Informationen helfen Ihnen beim Beheben von Problemen mit dem lokalen Entwicklungscluster:

- [Problembehandlung beim Einrichten des lokalen Entwicklungsclusters](service-fabric-troubleshoot-local-cluster-setup.md)

## Integritätsmodell
Service Fabric führt ein Integritätsmodell ein, das eine umfassende, flexible und erweiterbare Berichterstellungs- und Evaluierungsfunktion für Service Fabric-Entitäten bietet. Service Fabric-Komponenten erstellen standardmäßig Integritätsberichte für alle Entitäten. Die Integritätsdaten können mit logikspezifischen Informationen von Benutzerdiensten erweitert werden, die sich entweder auf den Benutzerdienst selbst oder auf andere Entitäten im Cluster beziehen. Weitere Informationen finden Sie unter:

- [Einführung in Service Fabric-Integritätsüberwachung](service-fabric-health-introduction.md)
- [Gewusst wie: Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)
- [Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Gewusst wie: Hinzufügen von Service Fabric-Integritätsberichten](service-fabric-report-health.md)

<!---HONumber=Oct15_HO1-->