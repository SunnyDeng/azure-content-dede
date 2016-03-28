<properties
   pageTitle="Der Clusterressourcen-Manager von Service Fabric – Bewegungskosten | Microsoft Azure"
   description="Übersicht über die Bewegungskosten für Service Fabric-Dienste"
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

# Dienstbewegungskosten zum Beeinflussen der Ressourcen-Manager-Optionen
Ein weiterer wichtiger Faktor bei den Überlegungen zu Veränderungen an einem Cluster, und der Bewertung einer bestimmten Lösung sind die Kosten, die mit der Umsetzung dieser Lösung verbunden sind.

Dienstinstanzen oder Replikate zu verschieben kostet zumindest CPU-Zeit und Netzwerkbandbreite. Für zustandsbehaftete Dienste wird darüber hinaus Platz auf dem Laufwerk benötigt, um den Zustand vor dem Herunterfahren alter Replikate zu kopieren. Ihnen ist es wahrscheinlich wichtig, die Kosten einer Lösung zu minimieren, die der Ressourcen-Manager bereithält. Gleichzeitig wollen Sie diejenigen Lösungen nutzen, die die Zuordnung von Ressourcen im Cluster erheblich verbessern.

Der Service Fabric-Ressourcen-Manager stellt zwei Methoden bereit, um Kosten zu berechnen und einzudämmen: 1. Wenn der Ressourcen-Manager ein neues Layout für den Cluster plant, berechnet er genau die zu verschiebenden Daten. Wenn Sie zwei Lösungen mit etwa dem gleichen Endergebnis zur Auswahl haben, wählen Sie diejenige mit den geringsten Kosten (Gesamtzahl der Datenverschiebungen). Das funktioniert recht gut, bis dasselbe Problem wie bei den Standard- oder statischen Lasten auftritt: In einem komplexen System sind Verschiebevorgänge nicht alle gleich; einige sind mit sehr viel höheren Kosten verbunden als andere.

## Ändern der Bewegungskosten eines Replikats und zu berücksichtigende Faktoren
Genau wie bei der Berichterstattung von Lasten (ein weiteres Feature des Clusterressourcen-Managers) kann der Dienst selbst ermitteln, wie teuer es jeweils ist, den Dienst zu einem beliebigen Zeitpunkt zu verschieben.

Code

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost hat vier Stufen: Zero, Low, Medium, und High. Diese stehen in einem Verhältnis zueinander, mit Ausnahme von Zero. Zero bedeutet, dass das Verschieben eines Replikats keine Kosten generiert und die Bewertung der Lösung nicht negativ beeinflussen sollte. Ihre Verschiebekosten als „High“ einzustufen, garantiert nicht, dass das Replikat nicht verschoben wird, sondern nur, dass es nicht ohne triftigen Grund passiert.

![MoveCost als Faktor bei der Auswahl der zu verschiebenden Replikate][Image1]

MoveCost hilft dabei, Lösungen zu finden, die die geringsten Unterbrechungen und den besten Ausgleich versprechen. Das Verständnis von Kosten hinter einem Dienst kann von Vielem abhängen, üblicherweise aber von:

1.	der Menge von Zuständen oder Daten, die ein Dienst verschieben soll
2.	den Trennungskosten von Clients (sodass die Verschiebekosten eines Primären Replikats höher wären als die eines Sekundären)
3.	den Kosten der Unterbrechung einer sich in der Ausführung befindenden Operation (einige Operationen auf Datenspeicherebene sind kostenaufwendiger und werden ab einem bestimmten Punkt nicht mehr unnötigerweise freiwillig abgebrochen) Für die Dauer der Operation stufen Sie die Kosten hoch, um die Wahrscheinlichkeit, dass der Dienst das Replikat oder die Instanz verschiebt, zu minimieren. Wenn die Operation abgeschlossen ist, können Sie sie auf normal zurückstufen.

## Nächste Schritte
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).
- Informationen darüber, wie der Clusterressourcen-Manager die Auslastung im Cluster verwaltet und verteilt, finden Sie im Artikel zum [Lastenausgleich](service-fabric-cluster-resource-manager-balancing.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0316_2016-->