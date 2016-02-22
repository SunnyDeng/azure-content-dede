<properties
   pageTitle="Platzierungseinschränkungen in der Service Fabric-Cluster-Orchestrierung | Microsoft Azure "
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
   ms.date="02/03/2016"
   ms.author="masnider"/>

# Übersicht über Platzierungseinschränkungen

Azure Service Fabric ermöglicht Entwicklern, die Platzierung von Dienstreplikaten auf Knoten, die bestimmte Bedingungen erfüllen, zu beschränken. Diese Bedingungen werden durch einen booleschen Ausdruck ausgedrückt, der mit entsprechenden Dienstkontext-spezifischen Werten ausgewertet wird.


## Funktionen
Platzierungseinschränkungen ermöglichen Folgendes:

- Beschränken Sie verschiedene Diensttypen auf verschiedene Knotentypen, indem Sie NodeProperties auf den Knoten definieren.

- Legen Sie Ziele für die Einschränkungen fest, sodass sie entweder sowohl für primäre als auch für sekundäre Replikate eines Diensts oder aber nur für die primären Replikate gelten.


## Wichtige Begriffe
NodeProperty – Eine benutzer- oder systemdefinierte Zuordnung einer Zeichenfolge zu einem Wert, der über jeden Knoten, d. h. NodeName, hinweg variieren kann.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Anwendungsszenarien](service-fabric-application-scenarios.md).

<!---HONumber=AcomDC_0211_2016-->