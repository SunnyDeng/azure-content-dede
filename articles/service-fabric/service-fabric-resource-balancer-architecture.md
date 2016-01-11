<properties
   pageTitle="Resource Balancer-Architektur | Microsoft Azure"
   description="Eine Übersicht über die Architektur von Service Fabric Resource Balancer"
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
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Übersicht über die Resource Balancer-Architektur

![Resource Balancer-Architektur][Image1]

Resource Balancer in Service Fabric besteht aus einem einzigen, zentralisierten Ressourcenausgleichsdienst und einer auf jedem Knoten vorhandenen Komponente. Diese werden mit dem Failover-Manager von Service Fabric bzw. dem lokalen Service Fabric-Knoten konzeptionell sortiert.

Der lokale Agent ist dafür verantwortlich, Auslastungsberichte von den auf dem lokalen Knoten ausgeführten Diensten zu sammeln, zu puffern und an den Resource Balancer-Dienst zu senden. Zudem meldet er Fehler und sonstige Ereignisse an den Failover-Manager und Resource Balancer (1 und 2 oben). Der Resource Balancer-Dienst und der Failover-Manager arbeiten zusammen, um auf Load-Ereignisse oder andere Ereignisse im System zu reagieren. Dazu gehören Replikat- oder Knotenausfälle, Auslastungsberichte sowie erstellte und gelöschte Dienste und Anwendungen.

Wenn beispielsweise bei einem Replikat ein Fehler auftritt, fordert der Failover-Manager von Resource Balancer basierend auf den Auslastungsdaten der verschiedenen Knoten eine Speicherortempfehlung für den Ersatz an. Auf ähnliche Weise fordert der Failover-Manager beim Empfang einer neuen Dienstanforderung von Resource Balancer Empfehlungen für den Speicherort des Diensts an. In all diesen Fällen reagiert Resource Balancer mit Änderungen an verschiedenen Dienstkonfigurationen (3). Diese werden dann vom Failover-Manager in Kraft gesetzt werden. Im vorherigen Beispiel erstellt der Failover-Manager ein neues Replikat auf dem Knoten und erzielt damit den optimalen Gesamtlastausgleich (4).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Funktionen des Ressourcenausgleichs:

- [Beschreiben des Clusters](service-fabric-resource-balancer-cluster-description.md)
- [Beschreiben von Diensten](service-fabric-resource-balancer-service-description.md)
- [Melden dynamischer Auslastungen](service-fabric-resource-balancer-dynamic-load-reporting.md)
- [Proaktives Packen von Metriken](service-fabric-resource-balancer-proactive-metric-packing.md)
- [Knotenpufferprozentsatz](service-fabric-resource-balancer-node-buffer-percentage.md)

[Image1]: media/service-fabric-resource-balancer-architecture/Service-Fabric-Resource-Balancer-Architecture.png

<!---HONumber=AcomDC_1223_2015-->