<properties
   pageTitle="Auswählen eines Frameworks"
   description="Service Fabric bietet zwei High-Level-Frameworks zum Erstellen von Diensten: das Actors-Framework und das Services-Framework. Wenn Sie beide Frameworks kennen, verfügen Sie über die notwendigen Grundlagen, um sich für die richtige Architektur für Ihre Anwendung entscheiden zu können."
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
   ms.date="07/09/2015"
   ms.author="seanmck"/>

# Auswählen eines Frameworks für den Dienst

Service Fabric bietet zwei High-Level-Frameworks zum Erstellen von Diensten: Reliable Actors-APIs und Reliable Services-APIs. Beide Programmiermodelle setzen auf dem gleichen Service Fabric-Kern auf, sie unterscheiden sich jedoch durch unterschiedliche Kompromisse zwischen Einfachheit und Flexibilität im Hinblick auf Parallelität, Partitionierung und Kommunikation. Um das richtige Framework für einen bestimmten Dienst in Ihrer Anwendung zu wählen, sollten Sie beide Modelle kennen.

## Vergleich der Reliable Actors- und Reliable Services-APIs

|**Reliable Actors-APIs**|**Reliable Services-APIs**|
|-----------------------|--------------------------|
|Der Problembereich enthält viele kleine unabhängige Zustands- und Logikeinheiten.|Sie müssen Logik über mehrere Komponenten hinweg gewährleisten.|
|Sie möchten mit Singlethread-Objekten arbeiten und dennoch skalieren und Konsistenz gewährleisten können.|Sie möchten zuverlässige Auflistungen (wie .NET Wörterbuch und Warteschlange) zum Speichern und Verwalten des Zustands verwenden.|
|Das Framework soll die Parallelität und Granularität des Zustands verwalten.|Die Granularität und Parallelität des Zustands soll gesteuert werden können.|
|Die Plattform soll die Kommunikation für Sie verwalten.|Sie möchten die Kommunikation verwalten und das Partitionierungsschema für Ihren Dienst steuern können.|

Bedenken Sie, dass es durchaus sinnvoll ist, verschiedenen Frameworks für verschiedene Dienste in Ihrer App zu verwenden. Sie können beispielsweise über einen zustandsbehafteten Dienst verfügen, der die von mehreren Akteuren generierten Daten aggregiert.

## Nächste Schritte

- [Erfahren Sie mehr über Reliable Actors-APIs](service-fabric-reliable-actors-introduction.md)
- [Erfahren Sie mehr über Reliable Services-APIs](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=July15_HO4-->