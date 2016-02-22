<properties
   pageTitle="Service Fabric-Programmiermodelle | Microsoft Azure"
   description="Service Fabric bietet zwei Frameworks zum Erstellen von Diensten: das Actors-Framework und das Services-Framework. Sie bieten unterschiedliche Kompromisse im Hinblick auf Einfachheit und Steuerung."
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
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# Auswählen eines Frameworks für den Dienst

Azure Service Fabric bietet zwei High-Level-Frameworks zum Erstellen von Diensten: die Reliable Services-API und die Reliable Actors-API. Beide Programmiermodelle setzen auf dem gleichen Service Fabric-Kern auf, sie unterscheiden sich jedoch durch unterschiedliche Kompromisse zwischen Einfachheit und Flexibilität im Hinblick auf Parallelität, Partitionierung und Kommunikation. Um das richtige Framework für einen bestimmten Dienst in Ihrer Anwendung zu wählen, sollten Sie beide Modelle kennen.

## Vergleich der Reliable Actors- und der Reliable Services-API

|**Argumente für die Reliable Actors-API**|**Argumente für die Reliable Services-API**|
|-----------------------|--------------------------|
|Der Problembereich enthält zahlreiche (1.000+) kleine, unabhängige Zustands- und Logikeinheiten.|Sie müssen Logik über mehrere Komponenten hinweg gewährleisten.|
|Sie möchten mit Single-Threaded-Objekten arbeiten, die keine wesentliche externe Interaktion erfordern.|Sie möchten Reliable Collections (wie .NET Reliable Dictionary und Reliable Queue) zum Speichern und Verwalten des Zustands verwenden.|
|Die Plattform soll die Kommunikation für Sie verwalten.|Sie möchten die Kommunikation verwalten und das Partitionierungsschema für Ihren Dienst steuern können.|

Bedenken Sie, dass es durchaus sinnvoll ist, verschiedenen Frameworks für verschiedene Dienste in Ihrer App zu verwenden. Sie können beispielsweise über einen zustandsbehafteten Dienst verfügen, der die von mehreren Akteuren generierten Daten aggregiert.

## Nächste Schritte

- [Erfahren Sie mehr über die Reliable Actors-API.](service-fabric-reliable-actors-introduction.md)
- [Erfahren Sie mehr über die Reliable Services-API.](service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0211_2016-->