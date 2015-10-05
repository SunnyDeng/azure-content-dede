<properties
   pageTitle="Knotenpufferprozentsatz"
   description="Sie erhalten einen Überblick über die Rolle des Knotenpufferprozentsatzes im Resource Balancer."
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

# Knotenpufferprozentsatz – Übersicht

Derzeit können Kunden den Grenzwert für die Knotenkapazität als Einschränkung angeben, die vom Resource Balancer gemäß der Einschränkungspriorität berücksichtigt wird. Wenn die Kapazitätseinschränkung eine hohe Priorität hat (Knotenkapazität darf nicht verletzt werden) und wenn die Clusterknoten eine hohe Auslastung aufweisen, kann es passieren, dass das Failover nicht sofort erfolgt oder dass die Knotenkapazität verletzt wird.

Es können Probleme auftreten, wenn Knoten mit sekundären Replikaten kurz vor dem Erreichen der Kapazitätsgrenze stehen und ein Knoten mit dem primären Replikat ausfällt. Falls die primäre Auslastung größer als die sekundäre Auslastung ist, kann das sekundäre Replikat nicht sofort heraufgestuft werden, ohne dass eine Überlastung des Knotens auftritt oder eine Replikatkopie erforderlich ist.

Wenn proaktive Logik für das Packen ausgeführt wird, bewegt sich eine höhere Anzahl von Clusterknoten nahe am Grenzwert der Knotenkapazität. Der Knotenpufferprozentsatz ist ein Feature, mit dem eine erhöhte Failoverzeit oder eine Knotenüberlastung während des Failovers verhindert werden. Hierzu erhalten Kunden die Möglichkeit, den Prozentsatz des Knotens anzugeben, der freigehalten werden soll. Replikate der neuen Dienste sollten dem Knotenpufferspeicher nicht hinzugefügt werden. Der Resource Balancer sollte aber dazu in der Lage sein, die gesamte Knotenkapazität (Pufferspeicherabrechnung) für Failover und das Hinzufügen von fehlenden Replikaten zu nutzen.

Sie können dieses Feature auch verwenden, wenn das Feature für das proaktive Packen von Metriken deaktiviert ist.

Im Codebeispiel wird veranschaulicht, dass Knotenpufferprozentsätze für Metriken pro Metrik über das FabricSettings-Element im Clustermanifest konfiguriert werden.

``` xml
<FabricSettings>
  <Section Name=" NodeBufferPercentage">
    <Parameter Name="MetricName" Value="0.1"/>
  </Section>
</FabricSettings>

```

Der Wert 0,1 für die Metrik mit dem Namen „MetricName“ bedeutet, dass 10 % aller Knotenkapazitäten für die Metrik „MetricName“ freigehalten werden sollten.

Wenn der Wert in diesem Abschnitt nicht angegeben ist, wird als Standardwert 0 verwendet.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=Sept15_HO4-->