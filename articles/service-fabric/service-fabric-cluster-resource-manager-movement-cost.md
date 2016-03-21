<properties
   pageTitle="Der Clusterressourcen-Manager von Service Fabric – Bewegungskosten"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Bewegungskosten
Ein weiterer wichtiger Faktor bei den Überlegungen zu Veränderungen an einem Cluster, und der Bewertung einer bestimmten Lösung sind die Kosten, die mit der Umsetzung dieser Lösung verbunden sind.

Dienstinstanzen oder Replikate zu verschieben kostet zumindest CPU-Zeit und Netzwerkbandbreite. Für zustandsbehaftete Dienste wird darüber hinaus Platz auf dem Laufwerk benötigt, um den Zustand vor dem Herunterfahren alter Replikate zu kopieren. Ihnen ist es wahrscheinlich wichtig, die Kosten einer Lösung zu minimieren, die der Ressourcen-Manager bereithält. Gleichzeitig wollen Sie diejenigen Lösungen nutzen, die die Zuordnung von Ressourcen im Cluster erheblich verbessern.

Der Service Fabric-Ressourcen-Manager stellt zwei Methoden bereit, um Kosten zu berechnen und einzudämmen: 1. Wenn der Ressourcen-Manager ein neues Layout für den Cluster plant, berechnet er genau die zu verschiebenden Daten. Wenn Sie zwei Lösungen mit etwa dem gleichen Endergebnis zur Auswahl haben, wählen Sie diejenige mit den geringsten Kosten (Gesamtzahl der Datenverschiebungen). Das funktioniert recht gut, bis dasselbe Problem wie bei den Standard- oder statischen Lasten auftritt: In einem komplexen System sind Verschiebevorgänge nicht alle gleich; einige sind mit sehr viel höheren Kosten verbunden als andere. Wie bei den Lasten verfügt der Dienst über einen Mechanismus, der automatisch berichtet, wie kostenaufwendig die Verschiebung von Daten zu einem bestimmten Zeitpunkt ist.

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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Erfahren Sie mehr über Metriken](service-fabric-cluster-resource-manager-metrics.md)
- [Learn about how the Cluster Resource Manager Balances Load in the Cluster](service-fabric-cluster-resource-manager-balancing.md) (Informationen zum Lastenausgleich im Cluster durch den Clusterressourcen-Manager)

[Image1]: ./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png

<!---HONumber=AcomDC_0309_2016-->