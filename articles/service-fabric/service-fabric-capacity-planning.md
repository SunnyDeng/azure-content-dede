<properties
   pageTitle="Kapazitätsplanung für Service Fabric-Apps | Microsoft Azure"
   description="Informationen zum Bestimmen der Anzahl von Computeknoten, die für eine Service Fabric-Anwendung erforderlich sind."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/12/2015"
   ms.author="subramar"/>


# Kapazitätsplanung für Service Fabric-Anwendungen


In diesem Dokument erfahren Sie, wie Sie die Anzahl der Ressourcen (CPU, Arbeitsspeicher, Datenträgerspeicher) bestimmen, die Sie zum Ausführen von Azure Service Fabric-Anwendungen benötigen. Es ist üblich, dass sich die erforderlichen Ressourcen mit der Zeit ändern. In der Regel brauchen Sie für das Entwickeln und Testen Ihres Diensts ein bestimmtes Maß an Ressourcen, das Sie erhöhen müssen, sobald Sie zur Produktionsumgebung wechseln und die Beliebtheit Ihrer Anwendung zunimmt. Beim Entwurf einer Anwendung ist es am besten, sich die langfristigen Anforderungen zu vergegenwärtigen und jetzt Entscheidungen zu treffen, die Ihrem Dienst ein einfaches Skalieren zum Erfüllen einer hohen Kundennachfrage ermöglichen.

 Wenn Sie einen Service Fabric-Cluster erstellen, entscheiden Sie, aus welchen Arten von virtuellen Computern (VMs) der Cluster bestehen soll. Jede VM verfügt über eine begrenzte Menge an Ressourcen in Form von CPUs (Kernen und -Geschwindigkeit), Netzwerkbandbreite, Arbeitsspeicher (RAM) und Datenträgerspeicher. Wenn der Dienst mit der Zeit wächst, können Sie ein Upgrade auf VMs mit mehr Ressourcen vornehmen und/oder Ihrem Cluster weitere VMs hinzufügen. Für den letzteren Schritt müssen Sie allerdings Ihren Dienst anfänglich so planen, dass er neue VMs nutzen kann, die dem Cluster dynamisch hinzugefügt werden.

Einige Dienste verwalten wenige oder keine Daten auf den virtuellen Computern. Beim Planen der Kapazität für diese Dienste sollte daher der Schwerpunkt auf der Leistung liegen. Dies bedeutet, dass Sie die Leistung der Algorithmen des Codes und die Leistung der VM-CPUs (Kerne und Geschwindigkeit) sorgfältig prüfen müssen, die für die Ausführung Ihrer Algorithmen erforderlich sind. Darüber hinaus müssen Sie die Netzwerkbandbreite prüfen, einschließlich der Häufigkeit von Netzwerkübertragungen sowie der übertragenen Datenmenge. Wenn Ihr Dienst bei steigender Nutzung weiter eine gute Leistung bieten soll, können Sie dem Cluster weitere VMs hinzufügen und unter alle VMs für einen Lastenausgleich der Netzwerkanforderungen sorgen.

Bei Diensten, die sehr viele Daten in den VMs verwalten, sollte der Fokus der Kapazitätsplanung auf der Größe liegen. Dies bedeutet, dass Sie die Kapazität des Arbeits- und Datenträgerspeichers des virtuellen Computers sorgfältig prüfen müssen. Das Windows-Verwaltungssystem für virtuellen Arbeitsspeicher sorgt dafür, dass der Anwendungscode Speicherplatz auf dem Datenträger als RAM interpretiert. Dadurch können Anwendungen mehr Arbeitsspeicher nutzen, als auf dem virtuellen Computer physisch verfügbar ist. Durch den zusätzlichen RAM wird die Leistung gesteigert, da der virtuelle Computer mehr Datenträgerspeicher im RAM belassen kann. Entscheiden Sie sich bei der Wahl eines virtuellen Computers für eine VM, deren Speicherplatz auf dem Datenträger alle in der VM gewünschten Daten aufnehmen kann. Wählen Sie zudem eine RAM-Größe, die Ihnen den Zugriff auf diese Daten im gewünschten Tempo erlaubt. Wenn die Daten Ihres Diensts mit der Zeit anwachsen, können Sie dem Cluster weitere VMs hinzufügen und die Daten auf alle VMs verteilen.

## Festlegen der benötigten Anzahl von Knoten

Das Partitionieren Ihres Diensts ermöglicht das horizontale Hochskalieren der Daten Ihres Diensts. (Weitere Details zur Partitionierung finden Sie unter [Partitionieren von Service Fabric](service-fabric-concepts-partitioning.md).) Jede Partition muss in eine einzige VM passen, doch mehrere (kleine) Partitionen können in einer einzelnen VM platziert werden. Daher gibt Ihnen eine große Anzahl kleiner Partitionen mehr Flexibilität als eine kleine Anzahl größerer Partitionen. Der Nachteil besteht darin, dass viele Partitionen den Verarbeitungsaufwand für Service Fabric erhöhen und Transaktionsvorgänge nicht partitionsübergreifend erfolgen können. Außerdem fällt potenziell mehr Netzwerkdatenverkehr an, wenn Ihr Dienst häufig auf Datenelemente zugreifen muss, die sich in verschiedenen Partitionen befinden. Wenn Sie Ihren Dienst entwerfen, müssen Sie diese Vor- und Nachteile sorgfältig prüfen, um zu einer effektiven Partitionierungsstrategie zu gelangen.

Nehmen wir an, dass Ihre Anwendung über einen einzelnen statusbehafteten Dienst verfügt, dessen Speichergröße in einem Jahr voraussichtlich auf DB\_Size GB anwachsen wird. Sie sind bereit, auch über dieses Jahr hinaus dem Wachstum entsprechend weitere Anwendungen (und Partitionen) hinzuzufügen. Um die gesamte Datenbankgröße (DB\_Size) in allen Replikaten zu ermitteln, müssen wir auch den Replikationsfaktor (RF) berücksichtigen, der die Anzahl der Replikate für Ihren Dienst bestimmt. (Die gesamte Datenbankgröße aller Replikate entspricht dem Produkt aus „DB\_Size“ und Replikationsfaktor.) „Node\_Size“ (Knotengröße) stellt den Datenträgerspeicherplatz/RAM pro Knoten dar, den Sie für Ihren Dienst verwenden möchten. Für eine optimale Leistung sollte „DB\_Size“ in den Arbeitsspeicher des gesamten Clusters passen, weshalb Sie eine Knotengröße wählen, die ungefähr der RAM-Kapazität der gewählten VM entspricht. Durch Zuordnen einer Knotengröße „Node\_Size“, die die RAM-Kapazität übersteigt, sind Sie auf die Betriebssystemauslagerung angewiesen. Folglich ist die Leistung möglicherweise nicht optimal, kann jedoch für Ihren Dienst ausreichend sein.

Die Anzahl der Knoten, die für eine maximale Leistung erforderlich ist, kann wie folgt berechnet werden:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## Berücksichtigen von Wachstum

Möglicherweise möchten Sie die Anzahl von Knoten basierend auf der Datenbankgröße „DB\_Size“ berechnen, bis zu der Ihr Dienst erwartungsgemäß zusätzlich zur anfänglichen „DB\_Size“ wachsen wird. Steigern Sie dann die Anzahl der Knoten mit dem Wachstum Ihres Diensts so, dass Sie sie nicht überdimensionieren. Die Anzahl der Partitionen sollte jedoch auf der Anzahl von Knoten basieren, die bei Ausführung des Diensts bei maximalem Wachstum erforderlich ist.

Es empfiehlt sich zudem, einige zusätzliche VMs (mit Zusatzkapazität) jederzeit vorzuhalten, damit Sie unerwartete Spitzen oder Fehler in der Infrastruktur (z. B. Ausfälle einiger VMs) ausgleichen können. Die Grundlage für die Berechnung dieser Zusatzkapazität sollten Ihre erwarteten Spitzen sein. Als Ausgangspunkt empfiehlt sich die Reservierung einiger zusätzlicher VMs (etwa 5-10 %).

Im obigen Beispiel wird von einem einzelnen statusbehafteten Dienst ausgegangen. Wenn Sie mehrere statusbehaftete Dienste verwenden, müssen Sie die mit den anderen Diensten verknüpfte Datenbankgröße „DB\_Size“ in die Gleichung einbeziehen, oder Sie müssen die Anzahl der Knoten für jeden statusbehafteten Dienst einzeln berechnen. Ihr Dienst weist möglicherweise Replikate oder Partitionen auf, die nicht ausgeglichen sind. Einige Partitionen enthalten möglicherweise mehr Daten als andere. Weitere Informationen hierzu finden Sie im [Artikel mit bewährten Methoden zur Partitionierung](service-fabric-concepts-partitioning.md). Die oben aufgeführte Gleichung ist jedoch unabhängig von der Anzahl der Partitionen oder Replikate, da Service Fabric dafür sorgt, dass die Replikate optimal auf die Knoten verteilt werden.


## Verwenden einer Kalkulationstabelle zur Berechnung der Kosten

Jetzt werden wir die zuvor genannte Formel mit echten Zahlen füllen. Im [Beispielarbeitsblatt](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) wird gezeigt, wie die Kapazität für eine Anwendung mit drei Arten von Datenobjekten geplant wird. Für jedes Objekt wählen wir einen Schätzwert für seine Größe und die erwartete Anzahl. Wir wählen außerdem aus, wie viele Replikate von jedem Objekttyp wir wünschen. Das Arbeitsblatt berechnet die Gesamtmenge des Arbeitsspeichers, der im Cluster gespeichert werden soll.

Dann geben wir eine VM-Größe und die monatlichen Kosten ein. Basierend auf der VM-Größe weist die Tabelle die Mindestanzahl von Partitionen aus, auf die Sie Ihre Daten verteilen müssen, damit diese physisch auf die Knoten passen. Sie benötigen ggf. mehr Partitionen, um die besonderen Rechnen- und Netzwerkverkehrsanforderungen Ihrer Anwendung zu erfüllen. Das Arbeitsblatt zeigt, dass sich die Anzahl der Partitionen zum Verwalten der Benutzerprofilobjekte von eins auf sechs erhöht hat.

Basierend auf all diesen Informationen zeigt das Arbeitsblatt nun, dass physisch für alle Daten mit den gewünschten Partitionen und Replikaten ein Cluster mit 26 Knoten erforderlich ist. Allerdings ist dieser Cluster dicht gepackt, weshalb einige zusätzliche Knoten für etwaige Knotenausfälle und Upgrades hinzugefügt werden sollten. Das Arbeitsblatt zeigt auch, dass sich mit mehr als 57 Knoten kein zusätzlicher Nutzen ergibt, da es leere Knoten gäbe. Doch für etwaige Knotenausfälle und Upgrades können Sie sich für mehr als 57 Knoten entscheiden. Sie können die Kalkulationstabelle an die speziellen Bedürfnisse Ihrer Anwendung anpassen.

![Kalkulationstabelle zur Berechnung der Kosten][Image1]



## Nächste Schritte

Unter [Partitionieren von Service Fabric-Diensten][10] erfahren Sie mehr über die Partitionierung von Diensten.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md

<!---HONumber=AcomDC_0121_2016-->