<properties
   pageTitle="Konfigurieren von Diensten mit dem Clusterressourcen-Manager von Service Fabric | Microsoft Azure"
   description="Beschreibt einen Service Fabric-Dienst durch Angabe der Metriken, Platzierungseinschränkungen und weiterer Platzierungsrichtlinien"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>


# Konfigurieren von Einstellungen des Clusterressourcen-Managers für Service Fabric-Dienste
Der Clusterressourcen-Manager von Service Fabric ermöglicht eine sehr detaillierte Steuerung der Regeln, die jeden einzelnen benannten Dienst lenken. Jede Dienstinstanz kann angeben, wie sie im Cluster zugewiesen werden soll, und den Satz von Metriken definieren, den sie melden will, inklusive der Wichtigkeit dieser Metriken für den Dienst. Das Konfigurieren von Diensten besteht im Allgemeinen aus drei verschiedenen Aufgaben:

1. Konfigurieren von Platzierungseinschränkungen
2. Konfigurieren von Metriken
3. Konfigurieren von erweiterten Platzierungsregeln (seltener)

Die einzelnen Punkte im Detail:

## Platzierungseinschränkungen
Platzierungseinschränkungen steuern, an welchen Knoten im Cluster ein Dienst tatsächlich ausgeführt werden kann. In der Regel ist die Ausführung einer bestimmten benannten Dienstinstanz oder aller Dienste eines Typs auf einen bestimmten Typ von Knoten beschränkt. Platzierungseinschränkungen hingegen sind erweiterbar – Sie können eine beliebige Anzahl von Eigenschaften auf Grundlage des Knotentyps definieren, und dann mit Einschränkungen für sie festlegen, wann der Dienst erstellt wird. Platzierungseinschränkungen sind über die Lebensdauer des Diensts dynamisch aktualisierbar, sodass Sie auf Veränderungen im Cluster reagieren können.

## Metriken
Metriken stellen die Liste der Ressourcen dar, auf die der Dienst verteilt wird, und enthalten Informationen darüber, wie viel von einer Ressource jedes Replikat oder jede Instanz des Diensts standardmäßig konsumiert. Metriken enthalten auch eine Gewichtung dessen, wie wichtig sie für den Dienst sind, falls Kompromisse erforderlich sind.

## Weitere Platzierungsregeln
Es gibt weitere Platzierungsregeln, die in erster Linie in geografisch verstreuten Clustern hilfreich sind, oder in anderen, weniger üblichen Szenarien Anwendung finden. Sie werden mithilfe von Korrelationen oder Richtlinien konfiguriert. Obwohl sie nur in einer geringen Anzahl von Szenarien Verwendung finden, werden sie aus Gründen der Vollständigkeit beschrieben.

## Nächste Schritte
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
- Affinität ist ein Modus, den Sie für Ihre Dienste konfigurieren können. Er ist nicht üblich, aber bei Bedarf erhalten Sie [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) weitere Informationen.
- Es gibt viele verschiedene Platzierungsregeln, die für Ihren Dienst konfiguriert werden können, um zusätzliche Szenarien zu verarbeiten. Informationen zu diesen verschiedenen Platzierungsrichtlinien finden Sie [hier](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md).
- Starten Sie mit einer [Einführung in den Clusterressourcen-Manager von Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md).
- Der Clusterressourcen-Manager bietet viele Optionen für die Beschreibung des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).

<!---HONumber=AcomDC_0316_2016-->