<properties
   pageTitle="Platzierungseinschränkungen für die Service Fabric-Cluster-Orchestrierung "
   description="Eine grundlegende Übersicht über die Platzierungseinschränkungen in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/17/2015"
   ms.author="abhic"/>

# Platzierungseinschränkungen – Übersicht

Service Fabric ermöglicht Entwicklern, die Platzierung von Dienstreplikaten auf Knoten, die bestimmte Bedingungen erfüllen, zu beschränken. Diese Bedingungen werden durch einen booleschen Ausdruck ausgedrückt, der mit entsprechenden Dienstkontext-spezifischen Werten ausgewertet wird.


## Funktionen
Platzierungseinschränkungen ermöglichen Folgendes:

- Beschränken verschiedener Diensttypen auf verschiedene Knotentypen durch Definieren von NodeProperties auf den Knoten.

- Anwenden bestimmter Einschränkungen auf primäre Replikate, jedoch nicht auf sekundäre Replikate


## Wichtige Begriffe
NodeProperty – Eine benutzer- oder systemdefinierte Zuordnung einer Zeichenfolge zu einem Wert, der über jeden Knoten, d. h. NodeName, hinweg variieren kann.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Anwendungsszenarien](../service-fabric-application-scenarios).
 

<!---HONumber=August15_HO6-->